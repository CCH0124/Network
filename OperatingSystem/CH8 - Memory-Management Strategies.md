## Background
- 明顯的，*記憶體訪問*和*記憶體管理*是現代電腦系統機操作中非常重要的部分。每條指令必須先從記憶體中取出才能執行，且大多數指令都涉及從記憶體中檢索數據或將數據儲存在記憶體中，或兩者都有
- multi-task OS 的出現增加了記憶體管理的複雜性，因為隨著 process 在 CPU 進出的交換，因此它們的代碼和數據必須在記憶體內進出交換，這些都是快速，並且不會干擾任何其他 process
- Shared memory、virtual memory，將記憶體分類為只讀與讀寫的概念以及像是寫時復制分叉之類的概念使問題進一步複雜化

### Basic Hardware(基本硬體)
- CPU 只能訪問其暫存器和主記憶體
  - 例如，它不能直接訪問硬碟驅動，因此儲存在硬碟驅動上的所有數據必須先轉移到主記憶體芯片中，然後 CPU 才能使用它。
  - 設備驅動程序透過中斷和"記憶體"訪問與其硬體進行通訊，例如：發送簡短指令以將數據從硬碟驅動傳輸到主記憶體中的指定位置。磁碟控制器監視匯流排以獲取此類指令，傳輸數據然後透過另一個中斷通知 CPU 數據在那，但 CPU 從不直接訪問磁碟。
- 對暫存器的記憶體訪問非常快，通常是一個時鐘滴答，並且一個時鐘滴答 CPU 可能執行多個機器指令
- 對主記憶體的記憶體訪問相對較慢，可能需要花費一些時鐘滴答才能完成。如果不是大多數現代 CPU 內置的中間快取記憶體緩存，這將需要 CPU 難以忍受的等待。快取記憶體的基本思想是一次將記憶體塊從主記憶體傳輸到快取，然後一次從快取訪問單個記憶體位置
- 必須限制用戶 process，以便它們只能訪問屬於該特定 process 的記憶體位置
- 對於每個 process，通常使用一個基底暫存器（base register）和一個界限暫存器（limit register）來實現，下兩張圖所示

![](https://i.imgur.com/FCbc0ID.png "Base and Limit Registers")

![](https://i.imgur.com/plYPKLh.png "Hardware Address Protection with Base and Limit Registers")

- 根據這兩個暫存器檢查用戶 process 進行的每次記憶體訪問，如果嘗試在有效範圍之外進行記憶體訪問，則會產生致命錯誤
- 作業系統顯然可以訪問所有現有的記憶體位置，只有 OS 能夠執行於 kernel model 之中，因為這對於將用戶的代碼和數據交換進出記憶體是必需的
- 明顯的，更改基底暫存器和界限暫存器的內容是一項特權動做，僅允許 OS kernel 使用

### Address Binding(位址連接)
使用者行程通常使用符號名稱（例如 "i"、"count" 和 "averageTemperature"）引用記憶體地址。這些符號名稱必須映射或連接(bind)到實體記憶體地址，這通常分幾個階段進行：
- Compile Time 
  - 如果在編譯時知道程式在實體記憶體中位置，則編譯器可以生成包含實際實體地址的絕對代碼(absolute code)
  - 如果加載地址(起始位置)稍後更改，則必須重新編譯程式。DOS.COM 程序使用編譯時連接(bind)
- Load Time
  - 如果在編譯時不知道要加載程式的位置，則編譯器必須產生*可重定位碼(relocatable code)* ，該代碼引用相對於程式開始的地址
  - 如果該起始地址發生更改，則必須重新加載程序，不能重新編譯
- Execution Time 
  - 如果程式可以在執行過程中在記憶體中四處移動，則連接(bind)必須延遲到執行時間為止。這需要特殊的硬體，並且是大多數現代 OS 所實現的方法

下圖，顯示了連接(bind)過程的各個階段以及每個階段涉及的單元：

![](https://i.imgur.com/Fb2tu3h.png "Multistep Processing of a User Program")

### Logical Versus Physical Address Space(邏輯位址空間和實體位址空間) 
- CPU 產生的地址是*邏輯位址*，而記憶體硬體實際看到的地址是*實體位址*。
- 在編譯時或加載時綁定的地址具有相同的邏輯和實體位址
- 但是，在執行時創建的位址具有不同的邏輯和實體位址
  - 在這種情況下，邏輯位址也稱為*虛擬位址(virtual address)* ，這兩個術語在文章中可以互換使用
  - 程式使用的所有邏輯位址的集合組成*邏輯位址空間*，而所有相應的實體位址的集合組成*實體地址空間*
- 邏輯位址到實體位址的運行時間，映射由*記憶體管理單元(memory-management unit) MMU* 處理
  - MMU 可以採用多種形式。最簡單的方法之一是對先前描述的基底暫存器方案的修改
  - 基底暫存器現在稱為*重定位暫存器(relocation register)* ，其值被添加到硬體級別的每個記憶體請求中

![](https://i.imgur.com/ibvEVcd.png "Dynamic relocation using a relocation register")

基底值為 14000，則使用者要存取位址 0 時，就會被重新定位到 14000；對 346 位址的存取會被對應到 14346。程式可以建立一個指標指向 346 的位置，將它存在記憶體中、處理他、將它害其他位只做比較，所有都以 346 做處理。

>用戶程式永遠不會看到實體位址。用戶程式完全在邏輯位址空間中工作，並且任何記憶體引用或操作均使用純邏輯位址完成。僅當位址發送到實體記憶體芯片時，才會產生實體記憶體位址。

### Dynamic Loading(動態載入)
- 動態載入而不是立即將整個程式載入到記憶體中，而是在調用每個常式時載入它們
- 優點是無需載入未使用的常式，從而減少了總記憶體使用量並縮短了程式啟動時間
- 缺點是增加了複雜性和開銷，即檢查常式是否在每次調用時都已載入，如果尚未載入則將其載入

### Dynamic Linking and Shared Libraries(動態鏈結和共用程式庫)
- *靜態鏈結(static linking)* 庫模組完全包含在可執行模組中，浪費了硬碟空間和主記憶體使用，因為每個包含庫中某個常式的程序都必須將自己的常式副本鏈結到其可執行代碼中
- 對於*動態鏈結(dynamic linking)* ，只有記號(stub)鏈結到可執行模組，其中包含對在運行時鏈結的實際庫模組的引用
  - 該方法節省了硬碟空間，因為不需要將常式完全包含在可執行模組中，而僅將記號(stub)包含在常式中
  - 動態鏈結庫（DLL，在 UNIX 系統上也稱為共享庫(shared library)或共享對象）的另一個好處是易於升級和更新。當程式使用標準庫中的常式並且常式進行了更改時，則必須重新構建（重新鏈接）該程式以合併更改。但如果使用 DLL，則只要記號(stub)沒有變化，就可以僅通過將 DL L的新版本加載到系統上來更新程序。版本訊息同時在程式和 DLL 中維護，以便程式可以在必要時指定 DLL 的特定版本

## Swapping(置換)
- 必須將一個 process 加載到記憶體中才執行
- 如果沒有足夠的記憶體將所有正在運行的 process 同時保留在記憶體中，則某些當前未使用 CPU 的 process 可能會將其記憶體換出到稱為*備份儲存體(backing store)* 的 fast 本地硬碟中

### Standard Swapping(標準置換)
- 如果使用了編譯時或加載時位址綁定，則必須將 process 交換回原來的交換位置。如果使用執行時間綁定，則可以將 process 交換回任何可用位置
- 與其他操作相比，交換(swapping)是一個非常緩慢的過程。例如，如果用戶 process 佔用了 10MB 並且備份儲存體的傳輸速率為每秒 40MB，則僅需要 1/4秒（250毫秒）即可完成數據傳輸。再加上 8 毫秒的等待時間滯後並暫時忽略了 head seek 時間，並且進一步識別到交換涉及將舊數據和新數據移出，此交換所需的總傳輸時間為 512 毫秒，或半秒以上。為了有效進行處理器調度，CPU time slice 應比此丟失的傳輸時間長得多
- 為了減少交換傳輸開銷，希望傳輸盡可能少的訊息，這要求系統知道 process 正在使用多少記憶體，而不是可能使用多少記憶體。程式員可以透過釋放不再使用的動態記憶體來提供幫助
- 重要的是，僅當 process 處於空閒狀態時才將其交換出記憶體，或者更重要的是，只有在沒有掛起的 I/O 操作時才進行交換。（否則，掛起的 I/O 操作可能會寫入錯誤 process 的記憶體空間。）解決方案是僅交換完全空閒的 process，或者僅將 I/O 操作進出 OS 緩衝區，然後再進出 OS 緩衝區 process 的主記憶體作為第二步
- 大多數現代 OS 不再使用交換，因為它太慢並且有更快的替代方案。（例如，Paging）但是，如果系統變得非常繁忙，某些 UNIX 系統仍會調用交換，然後在負載再次降低時中斷交換。Windows 3.1 將使用由用戶某種程度控制的交換的修改版本，交換過程在必要時退出，然後僅在用戶專注於該特定窗口時才將其交換回去
- 交換(Swapping)通常禁用
  - 如果分配的內存量超過閾值，則開始
  - 一旦內存需求降低到閾值以下，則再次禁用
  
![](https://i.imgur.com/3T4qTEd.png "Schematic View of Swapping")

### Swapping on Mobile Systems 
## Contiguous Memory Allocation(連續記憶體配置)
- 一種記憶體管理方法是將每個 process 載入到一個連續的空間中。通常先在低記憶體位置或高記憶體位置為作業系統分配空間，然後根據需求將剩餘的可用記憶體分配給 process。

### Memory Protection (記憶體保護)
- 重定位暫存器(Relocation registers)用於保護用戶 process 彼此之間以及更改 OS 代碼和數據時相互保護
  - 基址暫存器(Base register)包含最小實體位址的值
  - 限制暫存器(Limit register)包含邏輯位址範圍 – 每個邏輯位址必須小於限制暫存器
  - MMU 動態映射邏輯位址
  - 然後可以允許像是內核代碼是暫態(transient)的和內核大小改變之類的動作
- 下圖中所示的系統可以防止使用者程序訪問不應訪問的區域，允許根據需要將程序重定位到不同的記憶體起始地址，並允許專用於 OS 的記憶體空間隨著需求的變化而動態增長或縮小
- MMU 藉著加上重新定位暫存器中的值來動態的重新定位，這個對應位址於是被傳送到記憶體

![](https://i.imgur.com/cQIQSzm.png "Hardware Support for Relocation and Limit Registers")

### Memory Allocation (記憶體配置)
- 分配連續記憶體的一種方法是將所有可用記憶體劃分為大小相等的分區，並將每個 process 分配給它們自己的分區
  - 這限制了同時進行的 process 數量和每個 process 的最大大小，並且不再使用
- 另一種方法是保留未使用的（空閒）記憶體區塊（稱：洞）列表，並在需要將 process 載入到記憶體時查找合適大小的洞
- 有許多不同的策略可以找到 process 的"最佳"記憶體分配，包括最常討論的三種：
1. First fit (最先配分) 
搜索洞列表，直到分配足夠大的第一個洞
2. Best fit (最佳配分)
分配足夠大的最小洞，必須搜索整個列表，除非按大小排序。會產生最小的剩餘洞，而浪費
3. Worst fit (最差配分)
分配最大的洞，還必須搜索整個列表。會產生最大的剩餘洞

模擬顯示，就時間和儲存利用率而言，`First fit` 或 `Best fit` 比 `Worst fit`。就儲存利用率而言，`First fit` 和 `Best fit` 的數量相等，但是 `First fit` 的速度更快。

### Fragmentation (碎片)
- 所有記憶體分配策略都受到外部碎片(external fragmentation)的影響，儘管 `First fit` 和 `Best fit` 的人比 `Worst fit` 的人經歷的問題更多
  - 外部碎表示將可用記憶體分成許多小塊，儘管總和可以，但這些小塊都不足以滿足下一個記憶體需求
- 因碎片而丟失的記憶體量可能會因演算法、使用模式和某些設計決策（例如，要分配的洞的一端以及要保存在空閒列表中的一端）而異
- 例如，首次模擬的統計分析表明，對於 `N` 個分配的記憶體，碎片會丟失另外 `0.5N`
- 使用所有記憶體分配策略時，也會發生**內部碎片(Internal fragmentation)**
  - 這是由於以下事實造成的：將記憶體按固定大小分配，而實際所需的記憶體很少是那個確切大小
  - 對於隨機分配的記憶體請求，平均每個記憶體請求將浪費 `1/2` 個區塊，因為平均而言，最後分配的區塊將只有一半是滿的
  - 一些系統使用可變大小的區塊，以最大程度減少內部碎片造成的損失
- 如果記憶體中的程序是可重定位的（使用執行時地址綁定），則可以透過**聚集(compaction)** 來減少外部碎片問題，即將所有 process 下移到物理記憶體的一端
  - 這僅涉及為每個 process 更新重定位暫存器，因為所有內部工作都是使用邏輯地址完成的
- 在接下來的部分中看到的另一種解決方案是允許 process 使用物理記憶體的非連續塊，並為每個區塊使用單獨的重定位暫存器

>請注意，硬碟驅動器也會產生相同的效果，並且現代硬體為我們提供了越來越大的驅動器和記憶體，而以越來越大的區塊大小為代價，這表示更多的記憶體因內部碎片而丟失

>外部碎片：存在滿足請求的總記憶體空間，但不是連續的

>內部碎片：分配的記憶體可能比請求的記憶體大，這種大小差異是分區內部的記憶體，但未被使用

## 8.4 Segmentation(分段)
### 8.4.1 Basic Method(基本方法)
- 大多數使用者（程序員）並不認為他們的程序存在於一個連續的線性位址空間中
- 相反，他們傾向於在多個**分段**中考慮其記憶體，每個分段專用於特定用途，例如 code、data、stack、heap 等
- 記憶體**分段**透過為位址提供分段編號（映射到分段基底位址）和距離該分段開頭的偏移量來提供此視圖
- 例如，C 編譯器可能會為用戶代、，library 代碼、全域（靜態）變量、stack 和 heap 生成 5 個分段，如下圖所示：
![](https://i.imgur.com/NcHWvCr.png "User’s View of a Program")

![](https://i.imgur.com/NarhWys.png "Logical View of Segmentation")

### Segmentation Hardware(分段硬體)
- 邏輯位址考慮以下元組
```		
<segment-number, offset>,
```
- 分段表(segment table)將分段偏移位址映射到物理位址，並使用類似於前面討論的 page table 和重定位暫存器(relocation base registers)的系統同時檢查無效位址

- Segment table 映射二維物理位址。每個表條具有：
  - base 
    - 包含分段駐留在記憶體中的起始物理位址
  - limit 
    - 指定分段的長度

- 分段表基底位址暫存器（STBR,Segment-table base register）
  - 指向分段表在記憶體中的位置
- 分段表長度暫存器（STLR,Segment-table length register）
  - 指示程式使用的分段數量
- 如果 `s<STLR`，則分段號 `s` 是合法的

> 在目前的分段討論中，每個分段都保存在連續的記憶體中，並且可能具有不同的大小，但是分段也可以與分頁(paging)結合使用。

![](https://i.imgur.com/nkyx9yx.png "Segmentation Hardware")
從上圖可以知道，一個邏輯位址包含兩個元組：分段號碼 `s`；進入該分段的偏移量 `d`。
- 分段號碼
  - 指向分段表的索引
- 偏移量
  - 介於 0 和分段界線值之間
  
![](https://i.imgur.com/9pWmy7q.png "Example of segmentation")
上圖有 0 - 4 的 5 個分段。
如第二段有 400 字長，從 4300 位置開始。因此對此段中的第 53 個字參考，會映射到位置 4300 + 53 上

## Paging(分頁)
- 分頁是一種記憶體管理方案，它允許 process 物理記憶體不連續，透過在相等大小的區塊（稱為 *pages*）中分配記憶體來消除碎片問題
  - 只要有可用的物理記憶體，就為 process 分配實體記憶體
    - 避免外部碎片
    - 避免了大小不同的記憶體區塊的問題
- 是當今使用的主要記憶體管理技術

### Basic Method(基本方法)
- 分頁背後的基本思想是將實體記憶體劃分為多個大小相等的區塊稱為**欄(frame)** ，並將程序邏輯儲存空間劃分為相同大小的區塊稱為**頁(page)**
  - frame 大小為 2 的次方，介於 512bytes 和 16MB 之間
  - page 追蹤所有空閒的 `frame`
- 任何 `page`（來自任何 process）都可以放入任何可用 `frame` 中
- **page table**用於查找當前特定 `page` 儲存在哪個 `frame` 中。例如，在下面示例中，程序邏輯記憶體的第 2 頁當前儲存在實體記憶體的第 3 欄中：

![](https://i.imgur.com/B9qEiN0.png "Paging hardware")

![](https://i.imgur.com/0ugU6al.png "Paging model of logical and physical memory")

- 邏輯位址由兩部分組成：位址所在的頁數(page number)(p)，以及距該頁開頭的偏移量(page offset)(d)
  - 頁數中的位數限制單個 process 可以處理的頁數量。偏移量中的位數確定每頁的最大大小，並且應與系統欄(frame)大小相對應
- 分頁表(page table)將頁數映射到欄(frame)，以產生一個實體位址，該位址也分為兩部分：
  - 欄數
  - 該欄內的偏移量
  - 欄中的位數確定系統可以尋址的欄數，而偏移量中的位數確定每個欄的大小
- 頁數(page number)、欄數(frame numbers)和欄大小(frame sizes)由架構確定，但通常為 2 的次方，從而允許將位址拆分為一定數量的 bit
  - 例如，如果邏輯位址大小為 `2^m`，頁大小為 `2^n`，則邏輯位址的高階 `m-n` 位指定頁數，其餘 `n` 位代表偏移量
- 頁數中的位數和欄數中的位數不必相同。 前者確定邏輯位址空間的位址範圍，後者與實體位址空間有關

![](https://i.imgur.com/3NZ243R.png "Address Translation Scheme")

- 考慮以下示例，其中一個 process 具有 16bytes 的邏輯記憶體，並在 4byte 的 page 中映射到 32bytes 的實體記憶體。  
  - 邏輯位址 3 第 0 頁偏移量 3，對應實體位址 23[=(5\*4)+3]
  
![](https://i.imgur.com/1RNY3p0.png "Paging Example")

- paging 就像有一張重定位暫存器表， logical memory 的每一頁一個。
- paging 沒有外部碎片(external fragmentation)
  - 所有的實體記憶體區塊都被使用，並且兩者之間沒有間隙，也沒有為特定的記憶體區塊找到適合大小的洞(hold)的問題
- 但是，內部存在碎片。記憶體是按 page 大小分區塊分配的，平均而言，最後一頁(page)僅佔一半，而每個 process 平均浪費一半的記憶體
  - 如果 process 將其代碼和數據儲存在單獨的 page 中，則可能更多內部碎片
  - 分配以**欄**為單位
- 較大的 page 大小會浪費更多的記憶體，但是在開銷方面更有效
  - 現代趨勢一直是增加 page 大小，有些系統甚至具有多個 page 大小，以嘗試兼顧兩者
- 頁表條目（欄號）通常是 32bit 數字，允許訪問 `2^32` 個實體頁欄
  - 如果這些欄的大小均為 `4KB`，表示有 16TB 的可尋址實體記憶體
  - 32+12=44bit 實體位址空間
- 當 process 請求記憶體時（例如，從硬碟加載其代碼時），從空閒欄列表中分配空閒欄，並將其插入到該  process 的頁表中
- process 被阻止訪問其他任何人的記憶體，因為它們的所有記憶體請求都**透過其頁表進行映射**，他們無法生成映射到任何其他 process 的記憶體空間的位址
- OS 必須追蹤每個單獨 process 的頁表，在 process 的 page 移入和移出記憶體時進行更新，並在處理 `system call` 特定 process 時應用正確的頁表
  - 所有這些都增加了在 CPU 內外交換 process 時所涉及的開銷。（必須更新當前活動頁面表以反映當前正在運行的 process。）
  
 ![](https://i.imgur.com/NNSNt0F.png "Free Frames")
 
- 計算內部碎片
  - Page size  = 2,048 bytes
  - Process size = 72,766 bytes
  - 35 pages + 1,086 bytes
  - 2,048 - 1,086 = 962 bytes 的內部碎片
  - 最壞情況的碎片 = 1 frame – 1 byte
  - 平均碎片 = 1/2 frame size
  - 但是每個頁表項都需要記憶體來追蹤
  - 頁面大小隨時間增長
- process 視圖和實體記憶體現在大不相同
- 透過實現 process 只能訪問自己的記憶體

### Hardware Support
- 必須針對每個記憶體引用進行頁查找，並且每當一個 process 被換入或換出 CPU 時，其 `page table` 也必須與指令暫存器(instruction registers)一起換入和換出
  - 適當提供硬體支援對於該操作，使其盡可能快並且也使 process 切換盡可能的快
- 一種選擇是為 `page table` 使用一組暫存器
  - `DEC PDP-11` 使用 16bit 位址和 `8KB` 頁大小，因此每個 process 僅 8 page 並全放在快速暫存器中
- 另一種選擇是將 `page table` 儲存在主記憶體中，並使用分頁表基底暫存器（稱為**page-table base register, PTBR**）來記錄 `page table` 在記憶體中的位置
  - process 切換速度很快，因為僅需更改單個暫存器
  - 但是，記憶體訪問的速度卻快了一半，因為現在每次記憶體訪問都需要兩次記憶體訪問 - 一種從記憶體中獲取欄號，然後另一次訪問為所需的記憶體位置
  - 解決此問題的方法是使用一種非常特殊的高速儲存設備，稱為轉譯旁觀緩衝區(translation look-aside buffer, TLB)
    - TLB 的好處在於，它可以在整個表中並行搜索鍵值，並且如果在表中的任何位置找到它，則將返回相應的查找值
  ![](https://i.imgur.com/qPDRBEs.png)
    - 但是，TLB 非常昂貴，因此非常小。（不足以容納整個 page table）因此，它用作緩存設備
      - 首先根據 TLB 檢查位址，如果訊息不存在（TLB miss），則從主記憶體中查找欄並更新 TLB
      - 如果 TLB 已存滿，則替換策略的範圍從最近使用的 **LRU(least-recently used)** 到隨機的選取
      - 某些 TLB 允許將某些條目**硬體繞線固定**，這表示它們無法從 TLB 中刪除。通常，這些將是內核欄
      - 一些 TLB 儲存位址空間標識符 ASID，以追蹤哪個 process 擁有了 TLB 中的特定條目。這允許將來自多個 process 的條目同時儲存在 TLB 中，而無需授予一個 process 訪問其他 process 的記憶體位置的權限。如果沒有此功能，則必須使用每個 process 交換將 TLB 全部清洗。
 - 在 TLB 中找到所需訊息的時間百分比稱為**命中率(hit ratio)**
 
##### Example
假設訪問記憶體需要 100 ns，而搜索 TLB 只需 20 ns。因此，TLB 命中總共要花費 120 ns（找到欄號需要 20 ns，然後再花費 100 ns 才能獲取數據），而 TLB 未命中要花費 220 ns（要搜索 TLB 則要花費 20 ns，找到欄號要花費 100 ns，然後再獲取另一個 100 以獲取數據。）因此，如果 TLB 命中率為80％，則平均記憶體訪問時間為：

```
0.80 * 120 + 0.20 * 220 = 140 nanoseconds
```
降低 40％ 以獲得欄號。 98％ 的命中率將產生 122 ns 的平均訪問時間，速度降低 22％。

第九版忽略了搜索 TLB 所需的20 ns，得出
```
0.80 * 100 + 0.20 * 200 = 120 nanoseconds
```
降低 20％ 以獲得欄號。 99％ 的命中率將產生 101 ns 的平均訪問時間，速度降低 1％。

##### Effective Access Time

![](https://i.imgur.com/80MUmnV.png)

### Protection
- 分頁表(page table)還可以幫助防止 process 訪問它們不應該訪問的記憶體，或者以不應該訪問的方式訪問自己的記憶體
- 可以向分頁表中添加一個或多個 bit，將分頁(page)分類為 `read-write`、`read-only`、`read-write-execute` 或這些種類的某種組合
  - 可以檢查每個記憶體參照，以確保它以適當的模式訪問記憶體
- 可以將 `valid/invalid` bit 添加到分頁表中當前 process 未使用的 "mask off" 條目中，如下圖所示
![](https://i.imgur.com/togHIwt.png "Valid (v) or Invalid (i) Bit In A Page Table")
- 許多 process 沒有使用所有可用的分頁表，特別是在具有很大潛在分頁表的現代系統中
  - 某些系統不是透過為每個 process 創建一個完整的分頁表來浪費記憶體，而是使用**分頁表長度暫存器(page-table length register, PTLR)** 來指定分頁表的長度。

>因為內部碎片(internal fragmentation)，上述 `valid/invalid` bit 無法阻止所有非法記憶體訪問。最後的 page 中的內存區域未被 process 完全持有，並且可能包含最後使用該欄的人留下的數據

### Shared Pages (共用分頁)
- 透過簡單的在多個頁欄(page frames)中複製頁號(page numbers)，分頁系統(Paging systems)可以非常輕鬆的共享記憶體區塊
  - 可用代碼或數據來完成
- 如果代碼是**可重進入**的，表示它不會以任何方式寫入(唯讀)或更改代碼（它是非自行修改的），因此可以安全重新輸入它。更重要的是，也表示代碼可以由多個 process 共享，只要每個 process 都有自己的數據和暫存器拷貝（包括指令暫存器）即可
- 下面示例中，三個不同的使用者同時運行編輯器，但是代碼僅一次加載到記憶體中（在頁欄(page frames)中）
![](https://i.imgur.com/6dTgXBa.png "Sharing of code in a paging environment")

## Structure of the Page Table (分頁表的結構)
### Hierarchical Paging
- 大多數現代計算機系統支持 `2^32` 到 `2^64` 的邏輯位址空間
- 具有 `2^32` 的位址空間和 `4K（2^12）` 的頁面大小，這將在頁面表中保留 `2^20` 項。在每個項 4 個位元組的情況下，這相當於一個 `4MB` 的分頁表，該表太大而無法合理保存在連續記憶體中
- 一種選擇是使用兩層分頁系統，即分頁表本身也由分頁表的方法取得
![](https://i.imgur.com/eBovhMG.png "Two-Level Page-Table Scheme")
- 例如，上述的 20 bits 可以分解為兩個 10 bits 的分頁號碼
  - 第一個標識外部分頁表中的項，該項標識在記憶體中的位置以查找內部頁表的一頁
  - 後 10 bits 在該內部分頁表中找到特定項，該項進而標識實體記憶體中的特定欄(frame)
  - 32 bits 邏輯位址的其餘 12 bits 是 4K 欄內的偏移量
  ![](https://i.imgur.com/9phdiGy.png)
  - `p1` 是指向外層分頁表(outer page table) 的索引值
  - `p2` 是內層分頁表所指的分頁表之偏移量，如下圖
  ![](https://i.imgur.com/DVYguBl.png "Address translation for a two-level 32-bit paging architecture")
  - 因位址轉換由外層分頁表向內做起，也稱**向前對映分頁表(forward-mapped page table)**

- VAX 體系結構將 32 bit 位址分為 4 個大小相等的部分，每頁為 512 bytes ，產生的位址形式為：
![](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_15A_VAX_Address.jpg)
  - 就上圖一個區段而言，單層分頁表的大小扔然是 `2^21 bit x 每項 4 Byte = 8MB`
  
- 在具有 64 bit 邏輯位址空間和 4KB 頁的情況下，有 52 bit 值得使用的分頁號碼，即使對於兩級分頁也是如此
  - 一個可以提高分頁級別，但是如果使用 10 bit 分頁表，它將需要 7 個間接級別，這將極大降低記憶體訪問速度。因此，必須使用其他方法
  ![](https://i.imgur.com/aaQiMPO.png "Three-level Paging Scheme")
  
### Hashed Page Tables
