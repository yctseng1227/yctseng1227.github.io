# [Sharing] Jenkins 🛠 Github 當服務生遇到章魚貓


![https://memegenerator.net/instance/27413392/philosoraptor-if-jenkins-is-used-for-ci-does-the-jenkins-team-use-jenkins-to-build-jenkins](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1633253123/blog-posts/2021-06/19-cover.webp)

Nice question.

<!--more-->

## 一些 murmur

這學期修了一門「雲端大數據基礎建設實務」，真的是完全不在畢業學分的規劃內。
課名不重要，亮點是學校和**台GG**合開的新課程（資工/資管限修），所以內容以台GG合作的大廠輪流分享技術為主，包含 Cisco、Aruba、VMware、Red Hat、Splunk、Oracle 和 SAS，還記得第一堂旁聽的人多到爆炸，我們 Lab 也有不少人修。

期中期末的分組報告主題為「**如何以最低建置與營運成本建置一個高穩定，高效率的雲端服務**」，原本應該是只要腦補一個系統報告就好，不過在討論題目的時候 @stavhaygn 一句<font color='red'>「蛤 我比較想要可以實作的」</font>，注定這條路勢必不好走（笑）

{{< admonition type=note >}}
💡 專案有放在 [MacacaHub Github](https://github.com/MacacaHub)，名稱呼應當時「[大排長榮](https://en.wikipedia.org/wiki/2021_Suez_Canal_obstruction)」事件時事取名為「EVERCTF」，不知道之後還有沒有時間繼續開發就是了QQ
{{< /admonition >}}

結果就是，小組拆成書報組和實作組，分別由我和 @stavhaygn 帶頭，題目為「**以基礎架構即程式碼原則實作高穩定之自動化部署 CTF 競賽平台**」。在這個半數成員都在趕碩論的組合，我一開始就不指望實作可以完成，只能走多少算多少XD

現在，由於實作組的夥伴正在碩論超絕趕進度中，而我對專案中 DevOps 的環節蠻有興趣，所以在準備期末報告之餘也摸 Jenkins 這塊。~~什麼，你問我的碩論呢？傷心事就別提了。~~


總之，以下為透過 Github 和 Jenkins Pipeline 進行 CI/CD 的一些筆記。研究時間有限若有用字上的任何 Bug 都歡迎各位來信指正!!!

## Introduction

「CI/CD」中文譯作「持續性整合 / 持續部署or交付」，  
其原文為 (Continuous Integration / Continuous Deployment 或是 Continuous Delivery) ，個人認為這種把程式從**建構**、**測試**、**部署**，開發流程全部<font color='red'>自動化</font>的技術真是資訊圈的重要突破，雖然繁複的事做久了大家會想寫成 script，但對於整套都自動化做起來又是另一回事 XD

Jenkins 是基於 Java 在 CI/CD 這個領域中老牌的服務，網路上的介紹文數不勝數，尤其 iThome 鐵人賽有不少優文，我想這裡就不花時間介紹了，另外這篇主要是針對 EVERCTF 系統設計上 CI/CD 的筆記，因此在其他的工具使用上不會有太多著墨 (不然扯到 Git、Docker 等等的不知道要花幾篇介紹XDDDDD)。

### System overview

簡單說明一下 EVERCTF 在 CI/CD 這塊的運作吧 : 3  
下圖是我們規劃的 Jenkins Pipeline，流程上由出題者主動推到 Github (Push/PR)，然後觸發 Jenkins 進行檢查後將 Docker image 推到 Docker registry，最後透過 K8s 進行測試及部署。

![ ](https://raw.githubusercontent.com/MacacaHub/EVERCTF-Deployment/main/pic/jenkins-pipeline.png)

下面是預期中的資料結構，不過畢竟根據題目的需求還有很多改善空間。

```bash {linenos=false}
. (workspace) 
+- jenkinsfile
+- challenges
    +- pwn
        +- pwn1
            +- config.yaml
            +- Dockerfile
            +- exploit.py
            +- blahblahblah...
        +- pwn2
    +-web
        +- web1
            +- config.yaml
            +- Dockerfile
            +- html
                +- index.html
                +- blahblahblah...
            +- exploit
                +- Dockerfile
                +- exploit.py
        +- web2
```

## Configure

由於我平常只摸過小工具(?)，對於這種需要設定/認證的工具真的是很不上手，花了很多時間看 tutorial。

### Environment

{{< admonition type=note >}}
該篇文章的測試環境分別在 Ubuntu 18.04 和 MacOS 10.15（Jenkins 版本為 jenkinsci/blueocean :1.24.7）
{{< /admonition >}}

Jenkins 的 Docker 安裝如下！

```bash
$ docker run \
  --name jenkins-blueocean \
  -d \
  -u root \
  -p 9000:8080 \
  -v jenkins-data:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkinsci/blueocean
```

簡單說明 parameter 用途
- line 2: 自訂 images name
- line 3: 背景執行 container
- line 4: 以 root 身份登入
- line 5: 將 container 端的 Port 8080 對應到 host 端的 9000
- line 6: 將 host 端的 jenkins-data(volume) 掛載到 container 端的 /var/jenkins_home
- line 7: 讓 Jenkins 可以操作 host 端的其他 container，避免 docker in docker 現象
- line 8: Docker image from DockerHub [jenkinsci/blueocean](https://hub.docker.com/r/jenkinsci/blueocean/)

關於 line 8 所使用的 image，雖然 Jenkins 官方有另一個 [jenkinsci/jenkins](https://hub.docker.com/r/jenkinsci/jenkins) 比較像正版的 image (?)，不過從 [User Handbook](https://www.jenkins.io/doc/book/installing/docker/) 來看官方比較推 jenkinsci/blueocean，該版本加入了 [Blue Ocean](https://www.jenkins.io/doc/book/blueocean/) 的使用介面，簡單講就是官方 UI ~~太醜~~ 太傳統，所以加入了新的 UI（笑）。

若成功執行上述的 `docker run`，可以從 `docker ps -a` 確認到正在運行的 Container。  
從 `127.0.0.1:9000` 會發現需要 Unlock Jenkins，用下方指令直接從 Jenkins Container 內抓 password。

```bash {linenos=false}
$ docker exec -it jenkins-blueocean cat /var/jenkins_home/secrets/initialAdminPassword
```

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1624041590/blog-posts/2021-06/19-jenkins-unlock.webp)

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1624041590/blog-posts/2021-06/19-jenkins-setup.webp)

選左邊的安裝套件，裡面也包含之後會用到的 [Github plugin](https://plugins.jenkins.io/github/)，將剩下的設定點一點就算是完成了！

### Github Webhook

這部分可參考這篇 [透過 GitHub Webhook 讓你 push code 到 Github 就會自動觸發本地 Jenkins Pipeline](https://zoejoyuliao.medium.com/透過-github-webhook-觸發本地-jenkins-pipeline-讓你-push-code-到-github-就會自動跑-ci-cd-7c4bd7a22446)


為了要讓 Github 能夠主動觸發 Jenkins 進行 CI/CD，需要讓 Jenkins 有個對外的 Domain/IP，若沒有實體 IP 可以使用 ngrok 來產生 Domain，要注意 Port 是剛剛 `Docekr run` 設定的 9000，反之如果有實體 IP 就可以跳過 ngrok。

```bash {linenos=false}
$ ./ngrok http 9000
```

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1624042176/blog-posts/2021-06/19-ngrok.webp)

在欲進行的 repo > Settings > Webhooks > Add webhook，找到下圖畫面。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1624042177/blog-posts/2021-06/19-github-webhook.webp)

新增完 Webhook 如果點進去拉到最底下的 "Recent Deliveries" 有成功出現綠勾勾就是設定完成，要注意若把 ngrok 關閉，下次透過 Github 使用 Jenkins 就必須重新設定 Webhook ~~(所以我都直接綁實驗室的固網 IP)~~。

除了 Webhook 以外，再來就是認證的部分。  
也因為我們的專題是建立在 Private Repository 進行，而認證的方法很多種，這部分可參考這篇 [Jenkins - Build a Private GitHub Repo](https://dev.to/jmprobst/jenkins-build-a-private-github-repo-5489) ，非常之詳細。

在公私鑰產生的部分個人是用 ed25519，產生的 KEY 相對短很多，至於下指令 `ssh-keygen` 地方還未確定，個人是在 Jenkins Docker 中進行，但重點只是要產生公私鑰而已，頂多就是會多出 `/.ssh/` 的目錄(?)。

```bash {linenos=false}
$ docker ps -a
$ docker exec -it jenkins-blueocean bash

bash# ssh-keygen -t ed25519 -C <e-mail>
Generating public/private ed25519 key pair.
Enter file in which to save the key (/root/.ssh/id_ed25519): #直接Enter
Enter passphrase (empty for no passphrase): #直接Enter
Enter same passphrase again: #直接Enter
bash# cat /root/.ssh/id_ed25519.pub
ssh-ed25519 blahblahblahblahblahblahblahblahblahblahblahblahblah
bash# cat /root/.ssh/id_ed25519
-----BEGIN OPENSSH PRIVATE KEY-----
blahblahblahblahblahblahblahblahblahblahblahblah
blahblahblahblahblahblahblahblahblahblahblahblah
blahblahblahblahblahblahblahblahblahblahblahblah
blahblahblahblahblahblahblahblahblahblahblahblah
blahblahblahblahblahblahblahblahblahblahblahblah
-----END OPENSSH PRIVATE KEY-----
```

Github 端填 public key（左圖），Jenkins 端填 private key（右圖）。  
![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1624688960/blog-posts/2021-06/19-credentials-setting1.webp)

{{< admonition type=warning >}}
上述情境都是在 Private Repository 且自推(Push)的情境，若今次是團隊合作共用 Private Repository 且採 pull request (PR) 的狀況，那 credential 就要從該 repo 擁有者的帳號 "Settings > Developer settings > Personal access tokens" 設定。
{{< /admonition>}}

### Jenkins Theme (optional)

這部分可參考 [jenkins-material-theme](https://github.com/afonsof/jenkins-material-theme/)，就只是換個順眼的主題。


### Jenkins Language (optional)

透過 Browser 打開 Jenkins 很意外的是中文介面，查了一下發現是跟著 Browser 的語言設定，但可惜是半殘的中文化，俗稱晶晶體(?)，這種中英交雜的情況要對照網路上的教學文非常痛苦，所以稍微查了一下更改語言的方式。

Dashboard > (左側)管理 Jenkins > 管理外掛程式 > (可用的) 搜尋 [locale](https://plugins.jenkins.io/locale/) 進行安裝。

重啟 Jenkins 後，  
Dashboard > (左側)管理 Jenkins > 設定系統 > 直接找到 Locale 的預設語言，填 `ENGLISH` 或 `us` 都可，並且勾選下方的選項「Ignore browser preference and force this language to all users」。

## Jenkins Freestyle Project

{{< admonition success "🏆GOAL" >}}
透過 push 到 Github private repository，觸發 Jenkins 編譯 cpp 並執行。
{{< /admonition>}}

先來個簡單的小專案，從 "New Item > Freestyle project" 進入專案設定。  
在 **Source Code Management(SCM)** 部分選 "Git"，並注意 "Repository URL" 的部分因為是 Private Repository 要貼 SSH 格式。貼完之後系統會立即存取測試（即出現 ERROR），這時只要下方 "Credentials" 選擇我們稍早設定的 credential 即可。

另外要注意的是圖中箭頭所指的 "Branch Specifier"（預設是 */master），由於 2020 年黑人平權的議題 Github 將以 main 取代 master 做為預設 branch，詳細可參考[這篇 iThome新聞](https://www.ithome.com.tw/news/140094)。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1624690601/blog-posts/2021-06/19-job-sample-setting.webp)

在 **Build Triggers** 部分要勾選的是 "GitHub hook trigger for GITScm polling"，表示在接收到 Github Push 後會觸發 Jenkins。附上 [Github plugin](https://plugins.jenkins.io/github/) 的說明 "If Jenkins will receive PUSH GitHub hook from repo defined in Git SCM section it will trigger Git SCM polling logic. So polling logic in fact belongs to Git SCM."

最後，我們在 **Build** 部分選 Execute shell 嘗試讓 Jenkins 幫我們編譯 .cpp 並執行，Command 參考如下。

```bash {linenos=false}
echo "Oh, It works!!!"
ls -a
cd $WORKSPACE/job-sample
g++ -std=c++17 main.cpp -o main.out
./main.out
```

試著在本機端上傳 [job-sample/main.cpp](https://github.com/yctseng1227/jenkins-sample/blob/main/job/main.cpp) 看看 Jenkins 會不會動吧！  
沒意外的話會在左側欄的 Build History 出現紀錄，然後... 失敗 w

查看 Jenkins Build 的方法有兩種，一是使用當前的原生 GUI 左側 Build History，二是使用我們在 Docker Run 使用 jenkinsci/blueocean 所提供 Blue Ocean GUI，看起來是有稍微好看一點 XD

總之為什麼會失敗呢？  
從 error logs 可以發現是 `g++: not found`，也就是專案所需的套件是要在 Jenkins 安裝（由於 jenkinsci/blueocean 是基於 Apache Maven，因此安裝指令為 `apk`）。


```bash {linenos=false}
$ docker ps -a
$ docker exec -it jenkins-blueocean bash

bash-5.1# apk add g++
bash-5.1# g++ -v
```

安裝完 g++ 就回到 Jenkins 透過 "Build Now"（原生GUI）或 "Run"（Blue Ocean GUI）重新執行看看，成功！（下圖為 Blue Ocean GUI）

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1624698423/blog-posts/2021-06/19-job-sample-result.webp)

## Jenkins Pipeline

從上面 Freestyle project 的範例比較適合單一工作的小專案，對於我們的專題可能不敷使用，因此接下來要介紹的是透過 Jenkinsfile 執行的 Jenkins pipeline !!

{{< admonition success "🏆GOAL" >}}
透過 push 到 Github private repository，觸發 Jenkins pipeline 建立 Docker image 後 push image 到 Harbor registry。
{{< /admonition >}}

首先，專題需求要安裝 [Docker Pipeline Plugins](https://plugins.jenkins.io/docker-workflow/)。
再來，同樣從 "New Item > Freestyle project" 進入專案設定，要注意的點大致相同。
- **Build Triggers** 選擇 "GitHub hook trigger for GITScm polling"
- **Pipeline** 的部分:
    - 選擇 `Pipeline script from SCM` > `Git`
    - 貼上 SSH 格式的 Repository URL
    - `Branch` 注意是 */main
    - `Script Path` 在專案根目錄，預設為 Jenkinsfile

再來我們需要可以上傳的 Docker registry，大部分想到的應該都是 [DockerHub](https://hub.docker.com/)，不過考慮到我們的題目都必須在 private registry，所以這邊使用 Harbor registry 比較符合我們的需求，設定上可參考[這篇iThome文章](https://ithelp.ithome.com.tw/articles/10249640)。

{{< admonition type=note >}}
在實驗的過程發現在執行 Harbor 沒辦法只走 HTTP，所以又花了點時間學習怎麼用 Let's Encrypt 自簽憑證走 HTTPS，認真覺得自己在網頁開發方面真的很新手XD
{{< /admonition >}}

確認將 Harbor 成功 run 起來後，下一步就是要設定 Jenkins 的 Credentials，讓 Jenkins 能夠把 Docker image 推到 Harbor。新增 credentials 的部分和設定 webhook 一樣，這次我們透過 "Username with password"，而非 SSH，注意 ID 欄位就是之後放在 Jenkinsfile 做為環境變數，讓觸發 Jenkins 時才能順便 push image 到 Harbor。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/c_scale,f_auto,q_auto,w_600/v1624704318/blog-posts/2021-06/19-credentials-setting2.webp)


<br>

最後就是重頭戲 Jenkinsfile 了！
說實在這部分個人覺得不是很好寫，何況 Jenkins pipeline 還分成兩種語法，分別是 Scripted pipeline 和 Declarative pipeline，沒有統一的寫法根本就是奇美拉QQ 對第一次接觸的我來說光是要找範例code就花費不少時間，關於語法的細節可以參考這篇 [精通 Jenkins Pipeline — part1 (Groovy 以及 Jenkinsfile)](https://medium.com/getamis/%E7%B2%BE%E9%80%9A-jenkins-pipeline-part1-e8ef48d3543e)，整個系列我認為寫得相當完整。

![ ](https://i.redd.it/a24ek2gvmjc41.png)


這裡的 Sample code 為[simple-flask](https://github.com/yctseng1227/jenkins-sample/tree/main/simple-flask)，簡單生出一個網頁，並且透過測試檢查網頁內容是否正確。

測試失敗的案例如下，多了 Meow~ (喵喵喵)

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1624786706/blog-posts/2021-06/19-jenkins-pipeline-test1.png)

測試成功的話最終就能把 Docker image 推去 Harbor 了~

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1624785155/blog-posts/2021-06/19-jenkins-pipeline-test2.webp)

注意 push 的位置為 `< harbor domain >/< harbor projects>`，至於 Docker image 可以直接寫 `< image name >/< your name >`，不會被視為新目錄（會拿出來講是因為我以為會這樣XD）。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1624790826/blog-posts/2021-06/19-harbor.webp)

<br>

最後就附個 Jenkinsfile ，應該是不太需要註解... 吧

```groovy {filename=Jenkinsfile}
pipeline {
    environment {
        // setting in Jenkins credential
        REGISTRY_CREDENTIAL = "harbor-credential" 
    }
/* I'm not sure about the variable scoping rules in the Groovy language,
   so I write in setup stage as coded below.
    parameters {
        string (name: 'DOCKER_REG', defaultValue: "registry.eevee.tw/lab",  description: 'Docker registry')
        string (name: 'IMAGE_NAME', defaultValue: "app/yctseng",  description: 'Docker image name')
    }
*/
    agent any
    stages {
        stage("Setup") {
            steps {
                sh "docker version"
                script {
                    DOCKER_REG = "registry.eevee.tw/lab"
                    IMAGE_NAME = "app/yctseng"
                }
            }   
        }
        stage("Build Docker Image") {
            steps {
                dir("simple-flask") {
                    script {
                        ImageName = "${DOCKER_REG}/${IMAGE_NAME}"
                        DockerImage = docker.build(ImageName)
                    }
                }
            }   
        }
        stage("Test Docker Image") {
            steps {
                sh "docker run --rm ${DOCKER_REG}/${IMAGE_NAME} flask test"
            }   
        }
        stage("Push Docker Image") {
            steps {
                script {
                    docker.withRegistry("https://${DOCKER_REG}", REGISTRY_CREDENTIAL) {
                        DockerImage.push("latest")
                    }
                }
                echo "Remove unused images"
                sh "docker rmi ${DOCKER_REG}/${IMAGE_NAME}"
            }
        }
    }
}
```

<br>

我負責的範圍大概就到這裡，剩下就是由 @stavhaygn 接手透過 Kubernetes(K8s) 做 CI/CD，他最後寫出來的 Jenkins 比我的還要複雜XD 但最後我們的評價一致認為下次 CI/CD 的工具應該不會再用 Jenkins，雖說是老牌資源相對多，但也因為長久以來沒有統一的格式造成許多寫法上的混亂，也難怪有些文章的做法是 Jenkins 搭配 ansible 省得麻煩，現在想想還蠻聰明的 XD

### Syntax Error in Jenkinsfile ?

推薦 VSC Extension [Jenkins Pipeline Linter Connector](https://marketplace.visualstudio.com/items?itemName=janjoerke.jenkins-pipeline-linter-connector)，可以不用 push 到 Github 才從 Jenkins 發現 compiler error，安裝完記得在 VSC settings 設定 Jenkins 的 username / password / url，使用 ctrl/cmd+shift+p 或是預設 shift+alt/option+v 進行檢查。

