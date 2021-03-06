# OWASP
Open Web Application Security Project 維護定期更新且最緊迫的 Web 應用程序安全問題列表。
## What is OWASP ?
Open Web Application Security Project（OWASP）是一家致力於 Web 應用程序安全的國際非營利組織。OWASP 的核心原則之一是，所有資源都可以在其網站上免費獲取並輕鬆訪問，使得任何人都可以提高自己的 Web 應用程序安全性。他們提供的材料包括文件、工具、視頻和論壇，也許他們最著名的項目是 OWASP Top 10。

## What is the OWASP Top 10 ?
OWASP Top 10 是定期更新的報告，概述了 Web 應用程序安全性的安全問題，重點關注 10 個最關鍵的風險。該報告由來自世界各地的安全專家團隊匯總。OWASP 將前 10 名稱為"awareness document"，並建議所有公司將報告納入其流程，以最大限度的減少和/或降低安全風險。
以下是 OWASP 2017 年十大報告中報告的安全風險：
##### 1. Injection
當不受信任的數據透過表單輸入或向 Web 應用程序提交的某些其他數據發送到代碼 Interpreter 時，就會發生注入攻擊。例如，攻擊者可以將 SQL 數據庫代碼輸入到需要明文用戶名的表單中，如果該表單輸入未得到適當保護，則會導致執行該 SQL 代碼。這稱為 `SQL Injection` 攻擊。

可以透過**驗證**或**清理用戶提交的數據**來防止注入攻擊（驗證意味著拒絕可疑數據，而清理是指清理數據中可疑的部分）此外，數據庫管理員可以設置控制以最小化注入攻擊可能暴露的訊息量。
##### 2. Broken Authentication
身份驗證（登錄）系統中的漏洞可以讓攻擊者訪問用戶帳戶，甚至可以使用管理員帳戶破壞整個系統。例如，攻擊者可以獲取包含數據洩露期間獲得的數千個已知用戶名/密碼組合的列表，並使用腳本在登錄系統上嘗試所有這些組合，以查看是否有任何工作。

緩解身份驗證漏洞的一些策略要求使用 `2-factor authentication`（2FA）以及使用速率限制限製或延遲重複登錄嘗試。
##### 3. Sensitive Data Exposure
如果 Web 應用程序不保護敏感數據（如財務信息和密碼），則攻擊者可以訪問該數據並將其用於惡意目的。竊取敏感信息的一種流行方法是使用 `man-in-the-middle attack`。

透過加密所有敏感數據以及禁用任何敏感信息的緩存，可以最大限度地減少數據暴露風險。此外，Web 應用程序開發人員應注意確保它們不會不必要地存儲任何敏感數據。

>緩存是臨時儲存數據以供重用的做法。例如，Web 瀏覽器通常會緩存網頁，以便在用戶在固定時間範圍內重新訪問這些頁面時，瀏覽器不必從 Web 獲取頁面。

##### 4. XML External Entities (XEE)
這是對解析 XML 輸入的 Web 應用程序的攻擊。此輸入可以引用外部實體，嘗試利用解析器中的漏洞。在此上下文中的"外部實體"指的是儲存單元，例如：hard drive。可以將 XML 解析器用於向未經授權的外部實體發送數據，該外部實體可以將敏感數據直接傳遞給攻擊者。

防止 XEE 攻擊的最佳方法是讓 Web 應用程序接受較不復雜的數據類型（如 JSON），或者至少修補 XML 解析器並禁用 XML 應用程序中外部實體的使用。

>XML 或 Extensible Markup Language 是一種標記語言，只在使人類可讀和機器可讀。由於其複雜性和安全漏洞，它現在正逐步停止在許多 Web 應用程序中使用。

>JavaScript Object Notation（JSON）是一種簡單的，人類可讀的符號，通常用於通過 Internet 傳輸數據。雖然它最初是為JavaScript 創建的，但 JSON 與語言無關，可以被許多不同的編程語言解釋。
##### 5. Broken Access Control
訪問控制（Access Control）是指控制對訊息或功能的訪問的系統。**破壞的訪問控制允許攻擊者繞過授權並執行任務**，就好像他們是特權用戶（如管理員）一樣。例如，Web 應用程序可以允許用戶僅透過更改 URL 的一部分來更改他們登錄的帳戶，而無需任何其他驗證。

透過確保 Web 應用程序使用 `authorization tokens` 並對其設置嚴格控制，可以保護訪問控制。

>許多服務在用戶登錄時發出 `authorization tokens`。用戶發出的每個特權請求都需要存在授權令牌。這是一種確保用戶是他們所說的人的安全方式，而無需經常輸入他們的登錄憑據。

##### 6. Security Misconfiguration
安全性錯誤配置（Security Misconfiguration）是列表中最常見的漏洞，通常是使用默認配置或顯示過於冗長的錯誤的結果。例如：應用程序可能會向用戶顯示**過度描述性錯誤**，這些錯誤可能會洩露應用程序中的漏洞。這可以透過刪除代碼中任何未使用的功能並確保錯誤消息更簡單描述來減輕。

##### 7. Cross-Site Scripting
當 Web 應用程序允許用戶將自定義代碼添加到 URL 路徑或其他用戶將看到的網站上時，會發生 `Cross-Site Scripting` 漏洞。可以利用此漏洞在受害者的瀏覽器上運行惡意 `JavaScript` 代碼。例如：攻擊者可以向受害者發送電子郵件，該受害者似乎來自受信任的銀行，並帶有該銀行網站的鏈接，此鏈接可能會在 URL 的末尾標記一些惡意 JavaScript 代碼。如果銀行的網站沒有得到適當的 `Cross-Site Scripting` 保護，那麼當他們點擊鏈接時，該惡意代碼將在受害者的網絡瀏覽器中運行。

`Cross-Site Scripting` 的緩解策略包括轉義不受信任的 HTTP 請求以及驗證或清理用戶生成的內容。使用現有的 Web 開發框架（如ReactJS 和 Ruby on Rails）還提供了一些內置的 `Cross-Site Scripting` 保護。
##### 8. Insecure Deserialization
此威脅針對許多經常序列化和反序列化數據的 Web 應用程序。序列化意味著從應用程序代碼中獲取 Object 並將其轉換為可用於其他目的的格式，例如：將數據儲存到 Disk 或 streaming 。反序列化恰恰相反，將序列化數據轉換回應用程序可以使用的 Object。序列化就像在移動之前將家具包裝成盒子一樣，反序列化就像打開包裝盒並在移動後組裝家具一樣。不安全的反序列化攻擊就像讓搬運工在解包之前篡改箱子的內容。

不安全的反序列化漏洞利用是從不受信任的來源反序列化數據的結果，並且可能導致嚴重的後果，如 `DDoS` 攻擊和遠程代碼執行攻擊。雖然可以採取措施來捕獲攻擊者，例如監視反序列化和實施類型檢查，**但是防止不安全的反序列化攻擊的唯一可靠方法是禁止從不受信任的來源反序列化數據**。

##### 9. Using Components With Known Vulnerabilities
許多現代 Web 開發人員在其 Web 應用程序中使用 libraries 和 frameworks 等組件。這些組件是幫助開發人員避免冗餘工作並提供所需功能的軟體。常見的例子包括像 React 這樣的前端框架和用於添加共享圖標或 a/b 測試的較小庫。一些攻擊者會查找這些組件中的漏洞，然後他們可以使用這些漏洞來協調攻擊。一些更受歡迎的組件被用於數十萬個網站，攻擊者在其中一個組件中發現安全漏洞可能會使數十萬個站點容易被攻擊。

組件開發人員通常會提供安全補丁和更新來插入已知漏洞，但 Web 應用程序開發人員並不總是在其應用程序上運行已修補或最新版本的組件。為了最大程度地降低運行具有已知漏洞的組件的風險，開發人員應從項目中刪除未使用的組件，並確保他們從受信任的來源接收組件並確保它們是最新的。

##### 10. Insufficient Logging And Monitoring
許多 Web 應用程序沒有採取足夠的步驟來檢測數據洩露。發生破壞的平均發現時間大約是 200 天。這會讓攻擊者有很多時間在有任何響應之前造成損害。OWASP 建議 Web 開發人員應實施日誌記錄和監視以及事件響應計劃，以確保他們了解對其應用程序的攻擊。