# [Sharing] 2020 Summer Conf. PART 1


![ ](https://res.cloudinary.com/eevee-blog/image/upload/c_scale,f_auto,q_auto,w_800/v1619884192/blog-posts/2020-08/19-cover1.webp)

<!--more-->

### AIS3 新型態資安~~食物~~實務課程 (7/27 - 8/2) @NTUST

必須通過 [AIS3 Pre-exam](../ais3-2020-preexam-writeups/) 才能參加的資安課程，今年印象中最後錄取 186 名，換句話說就算來自非資安領域的同學，能來參加代表也有一定的實力。課程一共七天，採五天上課＋兩天分組專題報告，而今年的分組類別有 **關鍵基礎設施**、**國家安全**、**數位鑑識**、**網頁安全**、**軟體安全開發**，分組的題目會和專題有關，而且要在課餘時間生出來，也因為事前打聽到常常會弄到半夜，所以我在地人還特地申請台科大的宿舍 = w =

然後... 可以從官方的安排感受到 `關鍵基礎設施` & `國家安全` 投注特別多的心力 XD

<br>

當時在 pre-exam 的聊天室聽到 **關鍵基礎設施** 課程會教內網滲透覺得很有趣，所以我填志願放在第一順位，結果課程主要是以工控系統（ICS）為主的資安議題，[伊朗核電廠病毒 Stuxnet](https://ithelp.ithome.com.tw/articles/10186904) 和 [2018 台積電產線中毒事件](https://www.ithome.com.tw/news/125098) 常常被拿出來鞭(X) 警惕(O)，焦點多圍繞在`Modbus 工業領域通訊協定`、`OPC UA 自動化技術的傳輸協定`、`SCADA 系統`，導致在思考專題題目時相當頭疼，畢竟每個真要做起來規模都不小（或是只用模擬的方式），這裡也感謝組員用力 CARRY，臨時生出針對 Operator 和 PLC 之間的明文傳送，用 `訊息識別碼(MAC)` 上簽章防 `中間人攻擊（Man-in-the-middle attack）`，也讓我幾乎在旁邊當廢人 QwQ

其他比較有趣的課像是**數位鑑識**由 @CK 主講的「Chimera APT 攻擊調查：案例及調查手法分享」，印象中為了讓我們聽懂前面補很多 Domain knowledge，結果最後重點反而講得很匆忙稍嫌可惜，不過內容有趣到 @stavhaygn 一度想跳槽到數位鑑識組XD 。Lab 方面教 `moloch` 分析 `.pcap` 封包，看來以後除了 Wireshark 又多了一套工具...~~如果我還記得怎麼用的話~~。 

**軟體開發安全**由 @Ysc 主講的「開發安全與資安測試」，重點放在**自動化**、**自動化** 還有... **自動化!!**（正色），真的是講師開頭結尾都在強調的東西XDDD 這門重點放在DevOps的`CI/CD`（即自動化整合及部署），並加入資安各種自動化檢查測試，形成 **DevSecOps** 讓整個流程能夠更完善，這也讓我發現身邊有很多常用工具有這方面的應用，包括 GitHub、GitLab、Docker、Jenkins、Docker，真的算是開了眼界，~~應該是我自己太少接觸哭了~~。 另外 鄭欣明教授 帶來的實作 4G 偽基地站提取手機`IMSI`也蠻有趣，只是我們自己用手機找不到`IMSI`沒辦法驗證（只能挖到`IMEI`）有點好笑，最後做完我還幫官方提供的電腦裝ASCII水族箱 嘻嘻 

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-ais3-1.webp)

所以我說那個內網滲透呢？嗯、他被放在**網頁安全**由楊鈞皓 How 主講的「紅人演練」，是個很精實的 AD 滲透實作，包含一些提權及橫向滲透，跟我之前的**網路安全**專題有 87% 像，只是滲透等級又更高了許多，甚至到有點跟不上的程度 XD  最後**國家安全**唔嗯... 只能說很尷尬，大方向都是都是對網路言論或是資訊進行分析，感覺和這方面有關的台下都... Zzzz 都在念舊上午的紅人演練，看來大家都比較喜歡偏實務演練的課 = w =

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-ais3-2.webp)

結果在無聊之餘在報復性聊天室還出現很多CSS攻擊XDDDD  
最後群組就出現這張圖.... 笑死 ~~CSY明明沒參加還是會被信徒拿出來喊~~

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-ais3-meme.webp)


<br><br>


噢對 Day 1 & 2 晚上都由額外的活動，所以晚餐是由**奧義**贊助...嗯？你問我他們不是賣冰淇淋的嗎，不僅如此，他們還有出過桌遊哩XD

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-ais3-3.webp)
![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-ais3-4.webp)

課程共筆的部分可以參考 https://hackmd.io/@AIS3-official/AIS3-2020/，也可以去朝聖一下報復性聊天室，只是現在回去看已經沒那麼精采，當時真的是有些高頻率閃動是看了眼睛會痛 wwwww

大概就是這樣，最後兩天的專題感覺很微妙。還沒報告的組別忙著報告，報告完的多半也無心繼續聽(?) 聽說是從去年改成專題形式，感覺得出來很多人比較想玩 Final CTF，不過值得一提的是，我... 不小心發現**網路安全**組的釣魚實驗，而且我還是第一個在官方群組反應的，因此對話被節錄放在他們的報告上，某方面也是一種成就達成吧（抹臉

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1690000796/blog-posts/2020-08/19-ais3-expose-phishing-website.webp)

簡言之就是

|                  | Official  | Fake     |
| :---:            | :---:     | :---:    |
| Website domain   | ais3.org  | ais3.tw  |
| Instagram        | @ais3tw   | @ais3org |


他們巧妙的把官方的命名不統一對調，並把問卷 QR code 貼在會場各個角落，有種破壞了我們和官方之間的信任感，~~但也因此有幸認識到游桑和 Tina 兩位大大 www~~

<br><br>

### COSCUP 開源人年會 (8/1 - 8/2) @NTUST

有沒有覺得 COSCUP 的時間有點眼熟 ... 嗯沒錯，剛好就是 AIS3 的最後兩天專題報告！而且還同樣都在台科大（大概走路 5 分鐘的距離）XD
所以當我**關鍵基礎設施**在專題報告 Day 1 上午報完，就開始規劃想聽的議程（下午是**國家安全**專題），然後偷偷溜出去（笑

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-coscup-1.webp)

攤位的部分，因為 @stavhaygn 在前幾天在 **MOPCON** 的粉絲頁底下 tag 我開玩笑地説想拿貼紙，結果我翹課過去馬上就被官方認出來，還破例讓我多拿幾張，笑死www 到現在還是不太敢貼在筆電上>///<

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-coscup-2.webp)

除了到處晃晃拿貼紙，我還在官方攤買了酒杯和線材包，被同行友人笑每次翹課都會有收穫（幹w

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-coscup-3.webp)

議程部分都大概平均 30 min，Day 1 主要都在逛攤、Day 2 由於很想聽**網路安全**的釣魚專題 還有 @stavhaygn 的專題，所以偷溜出去的頻率少了一些，印象中聽的議程有`GCP`、`SDN x Cloud`、`Golang`、`Vault 私鑰管理`，還有個`Open Source 推廣分享`是去朝聖常常在 FB 和小馬拌嘴的蝦蝦姊（笑）。我在開源界的時間不算長，平常也只是偶爾摳來用，所以跑的主題幾乎都是聽過但沒機會摸，算是去增廣見聞，另外、還有認親 XDDDD

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-coscup-4.webp)

噢對了，另外我還拿到了神奇序號，主要是右邊的`.tw`免費註冊域名，只是想 domain name 是個令人頭疼的事QQ 另外左邊的`.GAY`工商真的頗炫wwww ~~還一直被朋友鼓吹去買個，真是謝了~~

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-coscup-5.webp)
