# GitHub Actions billing：官方計費文件的結構拆解

## 原文資訊

- 原文標題：GitHub Actions billing
- 原文來源：GitHub Docs
- 原文網址：https://docs.github.com/en/billing/concepts/product-billing/github-actions#included-storage-and-minutes
- 文件類型：官方產品計費文件 / 概念說明
- 擷取日期：2026-06-25

## 為什麼分析這篇

這篇不是新聞，而是一篇典型的「平台計費規則文件」。它有一個很值得拆的地方：表面上是在解釋 GitHub Actions 的免費額度、分鐘數、儲存空間與超額費用；但從文章結構來看，它真正要處理的是另一件事：**降低使用者對不可預期收費的焦慮，同時把責任邊界講清楚。**

計費文件的難點不是把價格列出來，而是讓讀者知道：

1. 哪些情境免費？
2. 哪些情境會消耗額度？
3. 誰會被收費？
4. 為什麼刪掉檔案後帳單仍然可能存在？
5. 如果超過額度，系統會怎麼處理？

所以這篇文件的寫法，本質上是在建立一套「成本心智模型」。

## 一句話拆解

這篇文件用「先定義免費邊界 → 再解釋計量方法 → 再列出額度與價格 → 最後提供預算控制」的順序，把 GitHub Actions 從一個開發工具，重新框定成一個可被治理的計量型資源。

## 文章架構地圖

### 1. 標題與導言：先告訴你這不是教學，而是計費規則

> Learn how usage of GitHub Actions is measured against your free allowance and how to pay for additional use.

這句導言很短，但功能很清楚：它不是要教你怎麼寫 workflow，而是要回答「用量如何被扣抵免費額度，以及超出後如何付費」。

這裡刻意用了兩個核心詞：

- **measured against your free allowance**：重點是「如何計量」與「如何扣抵」。
- **pay for additional use**：暗示超額不是錯誤，而是產品設計的一部分。

它先讓讀者知道：後面不是功能文件，而是資源會計文件。

### 2. 先講免費情境：降低防衛心

文件一開始先說：

- self-hosted runners 免費
- public repositories 使用標準 GitHub-hosted runners 免費

這個順序很有意思。它不是先列價格，而是先列「不用怕被收費」的情境。

可能原因：

1. GitHub Actions 有大量開源專案使用者，公共 repo 免費是核心信任基礎。
2. 如果一開始就列收費表，讀者會先產生防衛心。
3. 先講免費邊界，可以讓讀者把注意力放在「哪些情境例外」。

這是一種官方文件常見策略：**先安撫，再界定責任。**

### 3. 再講 private repositories：把真正的成本場景圈出來

接著文件轉到 private repositories：每個帳號依方案取得免費分鐘、artifact storage、cache storage；超出後會計費。

這裡的核心不是「private repo 要錢」這麼簡單，而是把 GitHub Actions 的成本拆成三種可治理資源：

- Minutes：計算執行時間
- Artifact storage：保存 workflow 產物
- Cache storage：加速建置的快取

這種分類對讀者很重要，因為不同資源的行為模式不同：

- minutes 是「跑一次就消耗」
- artifacts 是「留下來就持續佔空間」
- cache 是「為了節省時間而付出的儲存成本」

文件在這裡其實在教讀者一個基本模型：**CI/CD 的成本不是單一價格，而是時間成本與狀態保存成本的組合。**

### 4. 特別標示 repository owner：處理責任歸屬問題

文件明確寫：minutes usage is charged to the repository owner, not the person who triggered the workflow runs.

這句非常關鍵。

在組織裡，觸發成本的人與付費的人常常不是同一個人。任何有 write access 的人都可以跑 Actions，但帳單會到 repo owner 身上。這裡處理的是「代理問題」（principal-agent problem）：

- 執行者：可能只是合併 PR、重跑測試、調整 workflow
- 付費者：repository owner / organization
- 風險：執行者對成本沒有感覺，付費者承擔後果

所以文件用 TIP 把這件事凸顯出來，不只是補充，而是在提醒管理者：**權限設計就是成本設計。**

### 5. 插入 Copilot code review：把新產品接到既有計費模型

文件中間有一段 Copilot code review and GitHub Actions minutes。

這段看似突兀，但其實很合理。GitHub 需要回答一個新疑問：Copilot code review 是 AI 服務，為什麼會消耗 Actions minutes？

它的寫法是：

- 先說 Copilot code review 會消耗 Actions minutes，也會消耗 AI credits
- 再分 private / public repo
- 最後說預設跑在 Ubuntu runner，也可以改用 larger runners 或 self-hosted runners

這段的功能是把「AI 功能」重新放回「runner 執行資源」的成本模型裡。

換句話說，GitHub 想避免使用者誤以為 Copilot code review 只是一個抽象 AI API。它背後仍然要跑計算資源，而計算資源會對應 Actions 計費。

### 6. Storage billing：把最容易誤解的部分拉出來單獨教

文件花很多篇幅解釋 storage billing，而且使用 hourly accrual model。

這代表 GitHub 判斷「儲存費用」比「分鐘費用」更容易讓人誤會。

分鐘費用比較直覺：跑了 10 分鐘就是 10 分鐘。

儲存費用不直覺：

- 我現在刪掉了，為什麼帳單還在？
- 為什麼看到的 current storage 和 bill 上的 accrued storage 不一樣？
- 為什麼是 GB-Hours，不是月底看一次 GB？

所以文件先給概念，再給例子：10 GB 放 10 天，刪掉後 current storage 歸零，但已累積 2,400 GB-Hours。

這裡的寫法是在修正讀者的錯誤直覺：**帳單不是月底拍照，而是整個月錄影。**

這句可以放進資料庫：

> 對連續使用型資源來說，計費不是「結算時的狀態」，而是「期間內的積分」。

### 7. Storage measurement units：先避免單位爭議

文件明確說 GitHub Actions storage 的 GB 是 binary gigabytes：

- 1 GB = 2^30 bytes
- 也就是 GiB
- 1 GB = 1,024 MB

這段看起來很細，但在計費文件裡很必要。因為只要牽涉帳單，單位定義就會變成爭議來源。

它的功能不是教讀者電腦單位，而是提前封住模糊空間：**價格文件需要把自然語言變成可計算語言。**

### 8. Examples：用具體情境校正直覺

文件接著給多個例子：

- Linux runner 跑 10 分鐘
- workflow 失敗 5 分鐘後重跑成功，總共算 15 分鐘
- log files 和 job summary 不算 artifact storage
- cache storage 以每小時 peak usage 衡量

這些例子的作用是處理邊界案例。

官方文件知道使用者真正會問的不是「規則是什麼」，而是：

- 失敗的 workflow 算不算？
- 重跑會不會重新計算？
- log 算不算 artifact？
- cache 超過一點點怎麼算？

所以 examples 不是附錄，而是把抽象規則落到「可預期的場景」。

### 9. Free use table：把方案差異做成查表，而不是散落段落

到了 Included storage and minutes，文件用表格列出：

- GitHub Free
- GitHub Pro
- GitHub Free for organizations
- GitHub Team
- GitHub Enterprise Cloud

欄位包含：

- Artifact storage
- Minutes per month
- Cache storage per repository
- Custom image storage

這裡的表格功能不是說服，而是查詢。前面已經建立模型，這裡才進入數字。

這個順序很重要：如果先給表格，讀者可能只記得方案數字；但先解釋計量邏輯，再看表格，讀者才知道每個欄位的成本含義。

### 10. 使用超額與付款方式：把產品狀態分成 blocked 與 billed

文件說：

- 沒有有效付款方式：額度用完就 blocked
- larger runners：一定要設定付款方式
- 有付款方式：超額使用會透過帳號付款方式付費

這裡不是單純收費說明，而是在定義產品行為：

- 不能收錢時，系統用阻擋保護平台與使用者
- 能收錢時，系統允許超額，但需要預算治理

這是 SaaS 計費的典型邏輯：**免費額度不是無限承諾，而是進入付費治理的門檻。**

### 11. 價格表與計算範例：把焦慮轉成可驗算

文件最後列 baseline minute costs、storage pricing，並給計算範例。

這裡的重點不是讓每個人手算帳單，而是讓使用者相信規則是可重現的：

- Linux / Windows / macOS 不同價格
- artifact storage 用 GB-Hours 轉 GB-Months
- cache storage 只計超過 10 GB 的部分

它把「我會不會被亂收錢」轉成「我可以用公式驗算」。

官方文件在這裡追求的不是文學性，而是可稽核性。

### 12. Budget 與 alerts：最後給控制槓桿

最後一段是 managing your budget：設定 budgets、90% 與 100% 通知。

這個位置放在最後很合理。因為只有讀者先理解成本如何產生，預算工具才有意義。

它的敘事順序是：

1. 你如何產生成本
2. 成本怎麼計算
3. 超額會怎樣
4. 你如何控制它

這是一個從「理解」到「治理」的結構。

## 第一性原理拆解

### 這篇文件真正解決的問題

表面問題：GitHub Actions 如何計費？

底層問題：在多人協作、事件觸發、自動化運算的環境中，平台如何讓成本可歸屬、可預期、可治理？

### 基本約束

1. **計算資源是稀缺的**  
   runner minutes、macOS runner、larger runners、storage 都是真實成本。

2. **觸發者與付費者可能分離**  
   有 write access 的人可以觸發 workflow，但 repository owner 承擔帳單。

3. **自動化會放大成本**  
   一個錯誤 workflow 可以被反覆觸發；cache、artifact、custom image 也可能默默累積。

4. **使用者對成本的直覺通常是錯的**  
   特別是儲存費用：使用者容易以為刪掉就等於本月成本消失。

5. **平台必須兼顧成長與風險控制**  
   太早談收費會降低採用；太晚談收費會造成帳單驚嚇。

### 激勵結構

- GitHub 想鼓勵 public repositories 和開源使用，所以 public standard runners 免費。
- GitHub 想讓 private / enterprise 使用者把 Actions 當成可擴張的基礎設施，所以提供免費額度再收超額費。
- GitHub 想避免濫用或無意識爆量，所以需要 payment method、budget、alerts、quota blocked。
- 組織管理者需要知道成本歸屬，所以文件強調 repository owner 被收費。

### 權力關係

這篇文件其實在定義三方關係：

1. **平台 GitHub**：提供資源、制定計量方式、執行收費與限制。
2. **repository owner / organization**：擁有資源、承擔帳單、需要設定預算與權限。
3. **workflow 觸發者**：實際消耗資源，但不一定承擔成本。

因此，GitHub Actions billing 不是單純價格表，而是平台治理文件。

## 寫法與敘事策略

### 策略一：先講免費，再講收費

這降低讀者的防衛心，也延續 GitHub 對開源社群的品牌承諾。

### 策略二：先講模型，再講數字

文件沒有一開始就丟價格表，而是先解釋 minutes、storage、cache、hourly accrual。這讓數字有上下文。

### 策略三：把容易吵架的地方寫成例子

例如：刪 artifact 後帳單仍然存在、失敗 workflow 也算 minutes、cache 用 peak usage 計算。這些都是使用者最容易產生爭議的地方。

### 策略四：用 NOTE / TIP 管理注意力

TIP 用來提醒「誰會被收費」。NOTE 用來處理例外、限制與容易漏看的規則。

這種文件不是線性散文，而是「主規則 + 風險提示 + 邊界案例」的組合。

### 策略五：把責任轉成可操作工具

文章最後不是停在「你會被收費」，而是指向 pricing calculator、usage view、budgets、alerts。這能把讀者從焦慮帶回控制感。

## 可抽象成的思考模型

### 模型一：計費文件的四層結構

一篇好的計費文件通常要回答四層問題：

1. **邊界**：什麼免費？什麼收費？
2. **歸屬**：誰使用？誰付費？
3. **計量**：用什麼單位算？何時計入？如何累積？
4. **治理**：如何預估、限制、告警、停用？

如果只寫價格，而沒有寫這四層，讀者就會覺得不安全。

### 模型二：成本不是價格，而是行為的影子

GitHub Actions 的成本來自行為：跑 workflow、重跑失敗工作、保存 artifacts、累積 cache、保留 custom images。

所以理解計費，不只是理解價格，而是理解哪些行為會留下資源足跡。

> 成本不是價格表上的數字，而是系統行為在資源層留下的影子。

### 模型三：自動化系統的風險在於「無感消耗」

人手動花錢通常會痛；自動化消耗資源通常不痛。CI/CD、雲端運算、AI agent、排程任務都有同樣問題。

因此，任何自動化平台都需要三種保護：

- quota：預設上限
- budget：可調治理
- alert：接近風險時提醒

### 模型四：刪除只能停止未來，不會改寫過去

storage billing 的重點可以抽象成一句話：

> 刪除是停止未來成本，不是取消過去成本。

這不只適用雲端儲存，也適用很多累積型系統：用電、租賃、按量計費 API、廣告投放、GPU instance。

### 模型五：平台文件也是制度設計

官方文件不只是傳達資訊，而是在設定使用者與平台之間的預期契約。

它要同時完成：

- 降低採用阻力
- 降低帳單爭議
- 轉移使用者注意力到可治理工具
- 把平台成本合理化
- 讓組織管理者知道該管權限與預算

## 可以放進資料庫的筆記

- 計費文件的核心不是「列價格」，而是建立成本心智模型。
- 對連續使用型資源來說，計費不是結算時的狀態，而是期間內的積分。
- 自動化系統最大的成本風險，是使用者沒有感覺到自己正在消耗資源。
- 權限設計就是成本設計：誰能觸發 workflow，誰就能影響帳單；但付費者可能是 repository owner。
- 好的官方文件會先安撫、再界定責任、接著提供公式，最後提供控制工具。
- 刪除只能停止未來成本，不會改寫過去成本。
- 價格表回答「多少錢」；計量模型回答「為什麼會是這個錢」。

## 可以用來拆其他文章的問題

1. 這篇文章先講免費、好處、風險，還是限制？為什麼？
2. 它把責任歸屬給誰？使用者、擁有者、平台，還是管理者？
3. 它先給數字，還是先給計量模型？
4. 它用哪些例子處理讀者可能誤會的地方？
5. 它有沒有把焦慮轉成可操作工具？
6. 它想讓讀者形成什麼新的成本直覺？

## 仍不確定的地方

- 文件沒有說明 GitHub 內部如何決定各 runner SKU 的價格，只呈現對外價格。
- 文件沒有深入說明為什麼 public repository standard runners 免費；這需要從 GitHub 對開源生態、平台黏著度與品牌策略推論。
- 文件沒有描述實際組織治理場景，例如如何限制特定 repo 或 team 的 Actions 消耗；它只導向 budgets 與 alerts 文件。

## 我的理解總結

這篇文件之所以這樣寫，是因為 GitHub 不是只想告訴你「Actions 多少錢」，而是要讓你接受一個平台邏輯：Actions 是自動化基礎設施，因此必須被計量、歸屬與治理。

它的文章順序其實很精準：先保護讀者信任，再建立計量模型，接著揭露免費額度與價格，最後給預算控制工具。這是一種很典型的 SaaS 基礎設施文件寫法：**不是先賣價格，而是先教你怎麼不要被價格嚇到。**
