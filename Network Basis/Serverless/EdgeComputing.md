# Edge Computing
邊緣計算通過使計算更接近數據源來優化互聯網設備和 Web 應用程序。這最**大限度地減少了客戶端和服務器之間長距離通信的需求，從而減少了 `latency` 和 `bandwidth` 使用**。
## What is edge computing ?
邊緣計算是一種網路理念，致力於使計算盡可能接近數據來源，以減少 `latency` 和 `bandwidth` 使用。簡單來說，邊緣計算意味著在**雲中運行較少的進程並將這些進程移動到本地位置**，例如：用戶的計算機，IoT 設備或邊緣服務器。將計算帶到網路邊緣可以最大限度地減少客戶端和服務器之間必須進行的長距離通訊量。

## What is the network edge ?
對於互聯網設備，網路邊緣是設備或包含設備的本地網路與互聯網通訊的地方。邊緣有點模糊，例如：用戶的計算機或物聯網攝像機內的處理器可被視為路絡邊緣，但用戶的路由器，ISP　或本地邊緣服務器也被視為邊緣。重要的是，**網路的邊緣在地理位置上靠近設備**，不同於來源服務器和雲服務器，它們與它們通訊的設備相距甚遠。

## What’s an example of edge computing ?
考慮使用數十台高清物聯網攝像機保護的建築物。這些是 "dumb" 相機，只需輸出原始視頻信號，並將該信號連續傳輸到雲服務器。在雲服務器上，所有攝像機的視頻輸出都通過運動檢測應用程序，以確保只有具有活動的剪輯保存到服務器的數據庫中。這意味著建築物的互聯網基礎設施將面臨持續而巨大的壓力，因為大量視頻片段正在傳輸大量 `bandwidth`。此外，雲服務器上的負載非常繁重，必須同時處理來自所有攝像機的視頻鏡頭。

現在假設運動傳感器計算被移動到網路邊緣。如果每台攝像機使用自己的內部計算機運行運動檢測應用程序，然後根據需要將鏡頭發送到雲服務器，該怎麼辦？這將導致 `bandwidth` 使用的顯著減少，因為大部分攝像機鏡頭將永遠不必前往雲服務器。此外，雲服務器現在只負責存儲重要的鏡頭，這意味著服務器可以與更多數量的攝像機通訊而不會過載，這就是邊緣計算的樣子。

## What are the benefits of edge computing ?
如上例所示，邊緣計算有助於最大限度地減少 `bandwidth` 使用和服務器資源。`bandwidth` 和雲資源是有限的，並且需要花錢。隨著每個家庭和辦公室配備智能相機、打印機、恆溫器甚至烤麵包機，Statista 預測到 2025 年全球將安裝超過 750 億台物聯網設備。為了支持所有這些設備，必須將大量計算移動到邊緣。

將流程轉移到邊緣的另一個重要好處是減少 `latency`。每次設備需要與某個遠程服務器通訊時，都會產生延遲。例如，同一辦公室中通過 IM 平台聊天的兩個同事可能會遇到相當大的延遲，因為每條消息都必須路由到建築物外，與全球各地的服務器通信，並在收件人出現之前被帶回屏幕。如果該過程處於邊緣，並且公司的內部路由器負責轉移局內聊天，則不會存在明顯的延遲。

同樣，當各種 Web 應用程序的用戶遇到必須與外部服務器通訊的行程時，他們將遇到延遲。這些延遲的持續時間將根據其可用 `bandwidth` 和服務器的位置而變化，但是透過將更多行程引入網絡邊緣可以完全避免這些延遲。

此外，邊緣計算可以提供以前不可用的新功能。例如，公司可以使用邊緣計算來處理和分析邊緣處的數據，這使得實現這一點成為可能。

邊緣計算的主要好處是：
- Decreased latency
- Decrease in bandwidth use and associated cost
- Decrease in server resources and associated cost
- Added functionality

## What are the drawbacks of edge computing ?
邊緣計算的一個缺點是它可以增加攻擊向量。隨著更多"智能"設備的加入，例如：邊緣服務器和具有強大內置計算機的物聯網設備，惡意行為者有機會破壞這些設備。

**邊緣計算的另一個缺點是它需要更多的本地硬體**。例如，雖然物聯網相機需要內置計算機將其原始視頻數據發送到 Web 服務器，但它需要更複雜的計算機和更強大的處理能力，以便運行自己的運動檢測算法。但硬件成本下降使得構建更智能設備的成本更低。

完全減少額外硬件需求的一種方法是利用邊緣服務器（Cloudflare）。