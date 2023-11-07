# [Codeforces] April Fools Day Contest 2019


聽說是 Codeforces 每年都會辦的愚人節活動，今次算是體驗到何謂無厘頭解謎XD

<!--more-->

除了 pA 還算正常，其他都在玩猜猜樂（根本通靈大賽），賽後透過一些線索才把 pB, pC, pD 搞定，如果有任何錯誤也歡迎來信指教 ><


## [Problem A - Thanos Sort](https://codeforces.com/problemset/problem/1145/A)

一種有趣的排序法，透過多次對半刪減直到剩下的序列是 OK 的才停止。  
這題看計分板相當和平，我自己也是用遞迴慢慢跑就 AC 了w

應該算是出題者給大家牛刀小試用的吧~

## [Problem B - Kanban Numbers](https://codeforces.com/problemset/problem/1145/B)

題目短到可以不用點開連結XD  
> 輸入 $a (1≤a≤99)$ ，輸出 "Yes" or "No" 。

計分板上各種猜，提交 100 餘次的不在話下，可惜我看到 pD 比較多人 AC 就沒花時間猜這題了。

而謎底就藏在題目... "kanban"，拿去估狗翻譯會得到"看板"的解釋，但實際上應該要拆成kan、ban。kan 本身無意，而 ban 有禁止的意味，答案就是「禁止kan」。嗯？？？

嗯、就這樣。 其實在數字的英文拼音中並沒有出現 **k** 或是 **a** 的字母。  
因此，你只要檢查輸入的數字在拼寫中是否出現 **n** 就行了XD

真的是不能用正常的思路解謎呢...


## [Problem C - Mystery Circuit](https://codeforces.com/problemset/problem/1145/C)

題目給了很神秘的電路...然後要你輸入 $a (0≤a≤15)$ 、輸出整數，沒了。

老實說這題摸半天還是沒有頭緒，倒是友人在 [Quirk](https://algassert.com/quirk) 把電路拼起來丟 0101 測一測就發現答案了，莫名其妙XD

附上code，若非該領域的玩家應該都想不到吧...

```cpp
#include <iostream>
using namespace std;
int main(){
	int n; cin >> n;
	int arr[] = {15, 14, 12, 13, 8, 9, 10, 11, 0, 1, 2, 3, 4, 5, 6, 7};
	cout << arr[n] << endl;
	return 0;
}
```

## [Problem D - Pigeon d'Or](https://codeforces.com/contest/1145/problem/D)

讓我花最久的題目，老實說真的差一步就解出來了QQ

原本第一眼看題目還真的看不太懂，所以拿去餵狗翻譯... 嗯、還是看不懂呢，甚至還在懷疑是哪一國的語言，但大部分明明就是看得懂的英文啊QQ

後來從測資開始猜：  
範測是給了五個數字、得到一個數字，而且輸入數字範圍還在 32 內，用往年經驗有可能是邏輯運算，而這種題目最常用的就是 **XOR** ，所以就想盡辦法用範測和 XOR 湊答案，也因此吃了好幾次 WA 、深信下一次交出去的肯定會 AC（並不會好嗎.jpg）

但到了後來卡關又把題目讀了幾次... 想到會不會是出題者故意寫錯字!?  
從第一段的 "ftying rats" 感覺就應該是 "flying rats"，所以就想了幾個辦法測錯字。

最後是用 word 內建的拼字檢查，還真的不少錯字wwwww

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1694931764/blog-posts/2019-04/04-pD-wrong-spelling.webp)


英文超爛的我就開始慢慢對著把拼錯的字母抓出來... 然後競賽就結束了（幹  
賽後解出來看到神奇的...單詞？

`twoplusxorofthirdandminelements`

簡單分析一下... two plus XOR of third and min elements

> (輸入的第三個數字) ^ (序列中最小值) + 2。

意外地前面有猜到 XOR，只是這解法真的想不到XDDD  
如果有早點發現題目內的錯字 Bug，說不定就有機會即時解出來了qq

<br><br>

差不多就這樣、思考的過程真的蠻有趣的XD  
畢竟沒給其他線索真的只能無厘頭隨便猜、看到丟破百次才 AC 都不會感到驚訝，反而覺得「居然猜到AC實在太神LA」

