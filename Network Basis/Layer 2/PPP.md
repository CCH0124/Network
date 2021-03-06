# PPP 廣域網路協定
## 廣域網路
廣域網路（Wide Area Network，WAN ）和一般所謂的區域網路不一樣。區域網路通常是指範圍較小的網路區段，可能是同一棟大樓、同一層樓或是一個區域較小的地理位置。 而廣域網路是範圍比較廣的網路區段，一般跨國型的企業都會使用廣域網路來連結位於不同國家的各個分部，好讓資訊能夠相互流通。 
## PPP 協定
PPP（Point to Point Protocol）協定是普遍被使用的**廣域網路協定**，如同名稱所示，PPP 協定是指**點對點的網路協定，_通常用於兩個網路節點的直接連接_**，例如兩台電腦透過電話線的網路連接，經常使用在寬頻網路連線上。目前許多ADSL 網路服務供應商（ISP ）提供使用者以 PPP 協定的方式撥接到本身的機房，然後再連上網際網路。以往，則大多使用 SLIP（Serial Line Internet Protocol ）協定，該協定採用的是TCP/IP 協定的點對點串列連線的標準。SLIP 協定是很久以前的協定，主要用於串列埠與數據機之間的連線，現在慢慢地已經被 PPP 協定取代。



### PPP 協定在廣域網路連線上的定位
PPP 協定算是廣域網路在網路協定第二層封裝協定的種類之一，當網路封包要傳送到廣域網路之前，在網路第二層協定時，就一定會先透過特定的方式進行封裝，而為了保證能使用正確的封裝方式，在設定 Cisco 設備來支援廣域網路時就必須選對正確的封裝協定，而封裝協定的選擇與所使用的廣域網路技術和廣域網路設備有關。 因此，網路管理人員一定要瞭解廣域網路所使用的封裝協定，才能夠正確的選擇並且設定第二層封裝協定。

一般廣域網路的網路協定第二層封裝協定有以下幾個種類：
- HDLC
- PPP
- SLIP
- X.25
- LAPB
- 幀中繼（Frame Relay ）
- ATM

所以PPP 協定正是其中一種。

### PPP RFC 中定義資訊

PPP 協定包含了各種認證方式、加密與解密方式、壓縮資料方式以及 PPP 協定如何與其他網路協定的合作與交互關係。

PPP 協定中的CHAP 協定，也就是握手（Handshake ）協定，用於建立撥號連接。 

PPPoE 協定在乙太網路中用於傳輸PPP 協定中的資料，經常使用於ADSL 上。

PPPoA 協定，主要應用在以ATM 網路卡來傳輸PPP 協定的資料。由於PPPoA 使用於ATM 網路，所以也稱為PPPoATM。

PPP 協定可以在兩個網路系統之間支援多點連接以便增加網路頻寬，在這樣的網路環境之下，PPP 協定支援兩個頻寬的合併，同時也支援流量平衡的功能。

> PPP 協定分成 LCP 和 NCP 兩個部分，各負責不同的工作內容。

### PPP 協定的連線種類
廣域網路有以下三種類型
- Leased Line （專線）
  - Point-to-point 或 Dedicated 的連線。
  - 固定的電路，也就是專線
  - 優點可以保證頻寬，而且頻寬是獨享的，不會與其他網路共享
  - 缺點是花費太高，因為是專線，所以成本相對高昂許多
  ![](https://i.imgur.com/Br08gr1.png)
- Circuit Switched
  - 傳輸過程中整個網路連線都必須存活，有點類似撥接
  ![](https://i.imgur.com/ajmViZn.png)
- Packet Switched

PPP 協定屬於 Leased Line 或 Circuit Switched 類型。

### 廣域網路封裝協定與連線種類的關係
|廣域網路種類|OSI 網路第二層的封裝協定|
|---|---|
|Leased Line|HDLC 、PPP 以及SLIP|
|Packet Switched|X.25 、Frame Relay 以及 HDLC|
|Circuit Switched|HDLC 、PPP 以及SLIP|

### PPP 協定的階層式架構
PPP 協定中的階層式架構主要分成兩層，
- 上層為 NCP（Network Control Protocol ）
  - 負責認證等
  - 與 OSI 網路架構的第三層協同運作
  - 因為 NCP ，PPP 協定才能與各種不同的網路協定整合
- 下層為 LCP（Link Control Protocol ）
  - 負責建立資料連結的連線
  - 用於協調點對點的網路連線
  - 用於檢查網路封包訊框等等設定

## LCP 的設定與特性
會被使用的認證協定有 PAP 協定和 CHAP 協定。

|功能|主要工作內容|運作的協定|
|---|---|---|
|認證（Authentication）|要求密碼、執行握手（Handshake）過程|PAP CHAP|
|壓縮（Compression）|在來源端壓縮資料、在目的端解壓縮資料|Stacker Predictor|
|錯誤偵測| 監視連線中被丟棄的資料、避免訊框迴路|Quality Magic Number|
|多連結（Multilink）|在多連結的環境使用流量平衡（Load Balancing）|Multilink PPP（MLP）|

壓縮主要是用來增加 PPP 連線的網路流量，透過壓縮的方式來減少傳遞資料所需承載的網路封包大小。Cisco 路由器一般會使用 `Stacker` 和`Predictor` 這兩種壓縮協定。

PPP 協定的連線也具備錯誤偵測的功能，通常會使用 `Quality Magic Number` 來確保比較穩定而且不會有網路迴圈的資料傳輸。

PPP 協定的連線也可以在路由器的介面（Interfaces ）上使用流量平衡（Load Balancing ）的功能。

>Load balancing ，就是在具有相同路徑選擇時將網路流量平均分配到這些網路路徑上，以便達到網路流量平均分攤的效果，減少每一條網路路徑的負擔。

### 多通道的 PPP 協定
- Multilink PPP 協定
  - Multilink PPP 又稱為 MLPPP，或是 MP、MPPP、MLP 等等。MPPP 允許在多個不同的 PPP 連線中傳遞網路封包。舉例來說，MPPP 可以同時在兩條或是更多的網路專線、多條撥接網路上傳遞 PPP 封包。但是，==每一條 PPP 連線上所有的網路封包必須依照先後順序傳遞==，因此所有的 PPP 網路封包必須加上序號，以便於之後組裝時了解網路封包的先後順序。
- Multiclass PPP 協定
  - 在多個不同的 PPP 連線中無法同時建立並使用多個 PPP 連線，Multilink PPP 也不行。Multilink PPP 是指可以讓單一 PPP 連線同時在多個不同的連線媒介中完成。Multiclass PPP 是其中一種 Multilink PPP 的延伸，但是每一種 Traffic 的類型都用不同的序號來定序網路封包。

>Cisco 的網路設備必須把 Cisco IOS 升級到 11.1，才能支援 Multilink PPP。

## PPP 協定連線的建立過程
PPP 協定連線的建立過程分成三個階段。
1. **網路連線建立**
建立 PPP 協定的連線，在這個階段中，每一個使用 PPP 協定的網路設備（Cisco 路由器）都會發送 `LCP` 協定的網路封包，以便設定和測試網路連線。此時，可以透過 `LCP` 協定的設定值來設定 PPP 協定的連線，其設定包含最多能接收的封包單位數目、壓縮設定以及認證協定的設定等等，如果此時沒有設定這些設定值，就會自動採用預設值。  
2. **認證確認**
一旦網路連線建立，而且認證協定被決定之後，就代表連線的雙方可進入認證確認的階段。而認證確認的階段可有可無，不一定需要這個階段也可以完成 PPP 協定的連線。PPP 協定支援兩種認證協定：`PAP` 協定和 `CHAP` 協定。
3. **網路層協定處理**
最後一個階段是`網路層協定`的處理階段，這個階段是必須的。在這個階段中，PPP 協定會發送 `NCP` 協定封包來選擇，並且設定一個以上的網路層協定（IP 協定等等）。決定網路層的協定後，雙方就可以開始發送網路封包。  
## PPP 協定的認證協定
### PAP 認證協定
PAP 認證協定是一種雙向握手（Two way handshake ）協定，透過雙向握手的流程讓遠端的電腦或網路設備進行辨識（Identity ）認證的工作。一旦建立網路連線而且認證協定被決定之後，就代表連線的雙方可進入認證確認的階段。因此，==認證的發生時間是在 PPP 協定連線建立之後就進行==。
進行時，遠端的電腦或是網路設備會不斷地發送帳號和密碼到 Cisco 路由器，這樣的發送過程會一直持續，直到這個認證被接受或是這個 PPP 連線被中斷為止。
### PAP 認證協定的隱憂
雖然 PPP 協定使用著 PAP 認證協定，但事實上 PAP 認證協定本身並不是一個很嚴謹的認證協定，因為，PAP 認證協定會把密碼用純文字的方式（Clear Text）來傳送，這代表==傳送過程之中密碼不會被加密==，因此如果有人攔截到這樣的網路封包，就可以直接看到密碼。除了 Token Ring 網路架構之外，這樣的運作模式在大部分的網路環境中是相當不安全的。不僅如此，PAP 協定針對 `Playback` 和 `Trial-and-error` 的攻擊方式也沒有任何的保護措施。

### CHAP 認證協定
CHAP 認證協定採用的運作流程是 3-way handshake ，如果使用 CHAP 認證協定，當 PPP 連線建立階段完成之後，Cisco 路由器設備（本地端）就會發送所謂的 `Challenge 封包`給遠端的網路設備，接著遠端的網路設備會透過`One way hash` 的方式，針對傳送過來的 Challenge 值和密碼計算出一個特定的值，然後將這個特定的值傳回給原本發送的 Cisco 路由器設備。這裡所使用的 `One way hash` 方式一般都採用 `MD5`（Message Digest 5 ）演算法。接下來，本地端的 Cisco 路由器設備根據已知的計算方式再次計算出 hash 值，然後比對自己所計算的 hash 值和收到的 hash 值是否相同。如果兩個計算出來的 hash 值相同，代表這個認證已經通過，否則就會立刻中斷目前這個 PPP 連線。

![](https://i.imgur.com/AqGDMpY.png)

### CHAP 認證協定為何比較安全
CHAP 認證協定比 PAP 認證協定安全許多，因為雙方都會計算出特定的 hash 值加以比對，而且並不會把密碼直接用明文的方式來傳送。不僅如此，CHAP 認證協定對於 `Playback` 的攻擊方式也有相對應的保護措施。 剛剛提到的 `Challenge` 值很難被預測，因為 `Challenge` 值是由亂數產生，所以這也同時代表著所計算出來的 hash 值也是由亂數產生的。此外，Challenge 封包會不斷發送，這種作法也可以避免受到攻擊，因為每次發送的Challenge 值都不一樣。

## 實作

[PPP](https://itachi0124.blogspot.com/2017/11/ppp.html)
