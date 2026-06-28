# Sakana Fugu 初步調查：當「模型」變成模型群的指揮官

## 原文資訊

- 原文標題：[Fugu是什麼？日本Sakana AI最新AI系統一次看懂　8個QA解析模型編排如何挑戰OpenAI、Anthropic](https://www.storm.mg/article/11144808)
- 來源：風傳媒
- 作者：溫芳瑜
- 發布時間：2026-06-26 11:04
- 筆記建立：2026-06-28
- 類型：使用者指定初步調查；非每日自動選文。
- 主要並讀來源：
  - [Sakana AI 官方發布｜Sakana Fugu: One Model to Command Them All](https://sakana.ai/fugu-release/)
  - [Sakana Fugu 產品頁](https://sakana.ai/fugu/)
  - [Sakana AI Console｜Models](https://console.sakana.ai/models)
  - [Sakana AI Console｜Pricing](https://console.sakana.ai/pricing)
  - [GitHub｜SakanaAI/fugu](https://github.com/SakanaAI/fugu)
  - [arXiv｜Sakana Fugu Technical Report, 2606.21228](https://arxiv.org/abs/2606.21228)

## 為什麼選這篇

這篇風傳媒文章表面上是在做「Fugu 是什麼」的 QA 懶人包；但真正值得記錄的不是產品資訊，而是它捕捉到一個重要轉向：AI 競爭不只是在比誰訓練出最大、最強的單一模型，也開始比誰能把多個模型組成可管理、可替換、可擴張的系統。

Sakana Fugu 的敘事重點不是「日本又做出一個 ChatGPT」，而是「把模型編排本身產品化」。如果這條路成立，企業採用 AI 的核心問題會從「選哪個模型」變成「如何治理一個模型群」。這會牽動成本、延遲、資安、法遵、供應商鎖定、出口管制與 AI 主權等議題。

## 一句話拆解

Fugu 不是單純的下一個 LLM，而是把「挑模型、分工、驗證、整合」這套原本由工程團隊手寫的 agent workflow，包成一個 OpenAI 相容 API 的模型編排產品；它的價值主張是用模型群的集合智慧，替代對單一前沿模型供應商的依賴。

## 先把事實釐清

### 1. Fugu 是什麼

依 Sakana AI 官方說法，Sakana Fugu 是「multi-agent system delivered as one model」：外部看起來像呼叫一個模型，內部則由 Fugu 動態協調多個 agent / LLM 共同完成任務。

它的基本流程可以理解成：

1. 使用者把任務送到單一 endpoint。
2. Fugu 判斷任務是否能直接完成。
3. 若需要更複雜能力，就從 agent pool 中選擇合適模型或工具。
4. 它負責分派、溝通、驗證與整合。
5. 最後仍回傳一個單一答案。

這和一般「模型路由器」不同。路由器通常是「這個請求該送到哪個模型」；Fugu 的主張則是「這個任務應該拆成哪些子工作、讓哪些模型參與、如何互相檢查、最後如何合成」。

### 2. 兩個公開型號

官方目前提供兩個主要模型：

- **Fugu**：偏向效能與延遲平衡。官方模型頁說它會依任務路由到最佳模型，也能讓使用者排除特定 agents，以符合資料、隱私、法遵或組織要求。
- **Fugu Ultra**：偏向高品質與困難任務。官方模型頁說它會協調更深的 expert agent pool，並視問題在一到三個 agents 間路由；成本較高，適合高風險、多步驟、品質優先的工作。

### 3. OpenAI 相容 API 與工具支援

官方 Console 文件顯示 Fugu 支援 OpenAI-compatible 的：

- Chat Completions API
- Responses API
- Models API

官方也建議 generation request 優先使用 Responses API，並支援 Responses API 的 built-in `web_search` tool。這代表 Fugu 的產品策略不是另起爐灶，而是刻意貼近既有 OpenAI 生態，降低開發者遷移成本。

### 4. GitHub repo 的性質

`SakanaAI/fugu` 不是開源完整模型權重或編排系統，而比較像一個使用者接入與安裝入口。README 說明：

```bash
curl -fsSL https://sakana.ai/fugu/install | bash
codex-fugu
```

也就是說，公開 repo 的功能偏向把 Fugu 接到 Codex / 開發者環境；真正的模型與編排後端仍是 Sakana 的雲端服務。這點很重要，因為它提醒我們：Fugu 的「開放」主要是 API 相容與工具整合，不等於模型或 orchestration policy 已完全開源。

查詢 GitHub metadata 時，`SakanaAI/fugu` 在 2026-06-28 顯示約 596 stars、73 forks，預設分支為 `main`，最新 main commit 為 `fd9884eab4e9275e3dd9b472302d80638bc10f15`（message: `Merge pull request #25 from floatingsun/gpu-mode`）。這可視為早期開發者關注度的低基數訊號，而不是成熟生態證明。

### 5. 技術報告與研究脈絡

arXiv 技術報告《Sakana Fugu Technical Report》（2606.21228）摘要把 Fugu 定義為一系列 orchestrator models：模型本身被訓練來理解 query，並動態設計 agentic scaffolds。

技術報告提到的訓練 / 設計來源包括：

- large-scale fine-tuning
- evolutionary algorithms
- reinforcement learning approaches
- production-oriented infrastructure and design principles

官方產品頁也把 Fugu 連到兩篇 ICLR 2026 研究：

- **TRINITY: An Evolved LLM Coordinator**：用 evolved coordinator 在多個 LLM 之間分配 Thinker、Worker、Verifier 等角色。
- **Learning to Orchestrate Agents in Natural Language with the Conductor**：用強化學習訓練 Conductor，以自然語言設計 agent 溝通拓撲與指令。

這讓 Fugu 的主張比「手寫 agent workflow」更進一步：它不是只把某套固定流程包起來，而是宣稱讓 orchestration policy 本身可被訓練、可更新、可隨 agent pool 進化。

## 文章架構地圖

| 區塊 | 表面功能 | 深層意圖 | 為什麼這樣安排 |
|---|---|---|---|
| 標題 | 用「8 個 QA」降低技術門檻 | 把陌生產品包成可快速理解的消費型科技知識 | 讀者未必懂 model orchestration，QA 能降低進入成本 |
| 快速重點 | 先給結論：Fugu 是管理 ChatGPT 們的 AI | 避免讀者把它誤認為單一聊天模型 | 先建立「專案經理」比喻，後面所有段落都圍繞這個框架 |
| Q1-Q3 | 解釋 Fugu 與 LLM / router / 第三方模型的差異 | 把產品定位從「模型」推到「模型協調層」 | 這是文章最重要的概念校準 |
| Q4、Q7 | 討論供應鏈與出口管制風險 | 把技術產品放進地緣政治與企業韌性敘事 | 讓 Fugu 不只是工具，而是 AI 主權問題的答案之一 |
| Q5-Q6 | benchmark 與價格 | 補足企業採用的理性判準：能力、成本、場景 | 避免只停留在概念宣傳 |
| Q8 | 日本 AI 產業焦慮 | 把 Sakana 放進「日本如何避開美國巨頭正面戰」的產業敘事 | 讓單一產品變成國家技術路線選擇 |
| 結尾 | 「AI 管理 AI」 | 抽象出下一波 AI 競爭方向 | 從產品介紹升格成趨勢判斷 |

## 第一性原理拆解

### 基本問題：單一模型越強，為什麼還需要編排？

直覺上，若模型愈來愈強，似乎只要選最強模型就好。但複雜任務通常不是單一能力問題，而是多能力組合問題：

- 讀懂需求
- 拆解任務
- 找資料
- 寫程式
- 跑測試
- 檢查邏輯
- 評估風險
- 整合結果
- 控制成本與延遲

單一模型可以做全部，但不一定每一步都是最優。不同模型在 coding、數學、長上下文、視覺、資安、科學推理、語言風格上的強弱不同。若能在任務中動態組合，就可能用「群體互補」超過任一單體。

Fugu 的第一性問題可以寫成：

> 當模型能力變得分化，AI 系統的瓶頸就不只是模型能力，而是「如何選擇、協調與驗證模型能力」。

### 稀缺資源：不是只有算力，還有可信協調

Fugu 把幾種稀缺資源重新排列：

1. **前沿模型存取權**：最強模型可能受價格、配額、地區、出口管制限制。
2. **任務級可靠性**：單步回答漂亮不等於長任務能完成。
3. **延遲預算**：多 agent 會增加時間成本，不能無限制堆疊。
4. **成本可預測性**：編排會產生額外 orchestration tokens，企業需要知道怎麼計價。
5. **治理能力**：企業不一定能讓所有資料流向所有供應商。

Sakana 的產品頁特別強調可排除特定 agents，這表示它知道企業真正買的不是「多找幾個模型聊天」，而是「在多模型世界中保留控制權」。

### 供應商鎖定：從 cloud lock-in 變成 model lock-in

Fugu 官方發布文非常強調 single-vendor dependency 與 export controls。這背後的思維是：

- 若應用層深度依賴單一模型 API，模型供應商改價、降額、封鎖地區、調整政策，都會直接變成應用風險。
- 若企業內部 prompt、tool schema、evaluation、workflow 全都圍繞某個模型特性設計，遷移成本會越來越高。
- 因此，編排層可能成為「模型抽象層」，類似雲端時代的 Kubernetes / service mesh / cloud abstraction。

但這裡有一個反諷：Fugu 可能降低對 OpenAI / Anthropic / Google 的依賴，卻也可能製造對 Sakana 編排層的新依賴。真正的問題不是「有沒有 lock-in」，而是「lock-in 發生在哪一層、是否比較可治理」。

## Benchmark 該怎麼看

官方模型頁列出多項 benchmark，例如：

- SWE Bench Pro：Fugu Ultra 73.7、Fugu 59.0
- Terminal Bench 2.1：Fugu Ultra 82.1、Fugu 80.2
- LiveCodeBench：Fugu Ultra 93.2、Fugu 92.9
- LiveCodeBench Pro：Fugu Ultra 90.8、Fugu 87.8
- GPQA Diamond：Fugu Ultra 95.5、Fugu 95.5
- Humanity’s Last Exam：Fugu Ultra 50.0、Fugu 47.2
- Long Context Reasoning：Fugu Ultra 73.3、Fugu 74.7
- MRCRv2：Fugu Ultra 93.6、Fugu 86.6

這些數字可作為初步參考，但不應過度解讀為「全面贏過所有 frontier models」。原因有三：

1. 風傳媒文章也提醒，官方比較主要來自 Sakana 自身公布與整理，尚需第三方長期驗證。
2. 技術報告說，Fugu 以外 baseline scores 來自各模型供應商公布值；不同模型、不同 scaffold、不同測試條件可能不完全可比。
3. Fugu 的優勢可能最明顯在「多步驟 workflow」，但一般 benchmark 未必完整反映企業實際成本、延遲、失敗率與可追溯性。

因此比較好的判讀方式是：

> Fugu 的 benchmark 不是證明「它一定是最強模型」，而是證明「learned orchestration 已經足以進入 frontier-level 能力討論」。

## 成本與定價觀察

官方 Pricing 頁顯示：

- Fugu pay-as-you-go：
  - 1 agent：依 underlying model 的標準費率計算。
  - multiple agents：官方說不會 stack model fees，而是依參與的最高 tier model 收單一 rate。
- Fugu Ultra `fugu-ultra-20260615` 固定價：
  - 標準 context：input $5 / 1M tokens、output $30 / 1M tokens、cached input $0.50 / 1M tokens。
  - context 超過 272K：input $10、output $45、cached input $1。
- 訂閱制：Standard $20/月、Pro $100/月、Max $200/月。

最值得注意的是 usage fields。官方說 Fugu Ultra 會把使用者可見 token 與 orchestration token 分開回報，但 orchestration tokens 仍是真實用量，會計入最後價格。

這表示使用 Fugu Ultra 時，不能只用「最終回答長度」估成本；真正成本取決於內部編排做了多少輪、用了多少 agent、生成了多少中間推理 / 指令 / 工具呼叫。企業導入時應該測的是「每完成一件工作」的總成本，而不是單純比較每百萬 token 價格。

## 可能的影響

### 1. Agent framework 的一部分價值會被 API 化

現在很多團隊用 LangGraph、CrewAI、AutoGen、Claude Code、Codex、內部 workflow 來做多 agent。Fugu 的產品化方向是在說：你不一定要自己設計 workflow，直接呼叫一個會協調模型的模型。

如果成功，它會吃掉一部分「手寫 agent orchestration」的市場。但短期內它更可能變成互補：

- Fugu 負責模型層的協調。
- 企業內部 framework 負責權限、資料、工具、流程、審計與人類批准。

### 2. AI 主權敘事會從「自研大模型」轉向「可替換模型池」

日本若要正面追 OpenAI / Anthropic / Google 的訓練規模，非常困難。Sakana 選擇的路線更像：不一定最大，但要能整合最大者。

這對中型國家、企業、研究機構都有啟發：AI 主權未必只有「自己訓練一個最大模型」這條路，也可能是：

- 有自己的 orchestration layer。
- 有可替換 agent pool。
- 有資料與供應商治理能力。
- 能在外部模型變動時維持服務連續性。

### 3. 「模型能力」會被重新定義成「模型 + scaffold + pool」

技術報告有一個重要觀念：能力不只是 model weights 的性質，也是 scaffold 的性質。這對 benchmark 文化很重要。

未來比較 AI 系統時，可能不能只問：

- 這是哪個 base model？

還要問：

- 它用什麼 scaffold？
- 有哪些 tools？
- agent pool 裡有哪些模型？
- 可以排除哪些供應商？
- 中間過程是否可審計？
- 失敗時是否能回退？

### 4. 對 OpenAI / Anthropic / Google 的挑戰不是直接替代，而是抽象化

Fugu 不一定要比每個模型都強，才有商業價值。它只要讓企業覺得「我不想直接綁死單一模型供應商」，就能成為上層抽象。

這和資料庫、雲端、晶片產業很像：抽象層一旦成熟，底層供應商仍然重要，但議價權會被上層治理層分走一部分。

## 風險與不確定性

### 1. 黑箱編排與可追溯性

Fugu 的價值在於幫使用者隱藏複雜度，但企業治理常常需要看見複雜度：哪個 agent 看過資料？哪個模型做了哪一步？中間答案如何被否決？若出錯，責任歸屬在哪？

若這些審計能力不足，Fugu 可能很適合研究 / coding / exploratory work，但在金融、醫療、政府核心流程中會遇到阻力。

### 2. 延遲與成本尾部風險

多 agent 系統的平均表現可能很好，但尾端延遲與成本可能不穩。困難任務若觸發更多內部步驟，使用者可能遇到：

- 回答時間不穩。
- orchestration token 成本超出預期。
- 同樣 prompt 因 routing 差異產生不同成本與品質。

這會要求平台提供更好的 budget control、max agent count、trace、cache 與 fallback 設定。

### 3. 供應鏈韌性不等於完全不受管制

官方強調「without the risk of export controls」，但更精準的理解應該是「降低單一供應商與特定模型中斷的風險」。如果 agent pool 仍大量依賴美國前沿模型，地緣政治風險只是被分散，不是消失。

真正的 AI 主權要看：

- agent pool 的地理與公司來源是否多元。
- 是否有 Sakana 自研或開源模型可補位。
- 特定區域 / 法規下是否能指定不使用某些供應商。
- 在最壞情境下品質降級是否可接受。

### 4. GitHub repo 不是完整開源實作

`SakanaAI/fugu` 的 README 偏向安裝、Codex 接入與使用文件。這對開發者友善，但它不是讓外部研究者完整重現 Fugu 編排能力的 repo。若要評估技術透明度，仍需以 arXiv 技術報告、API 文件與未來是否釋出更多 evaluation / traces 為準。

## 可抽象成的思考模型

### 模型：從「最佳單體」到「可治理集體」

當一個領域進入多供應商、多專長、多限制狀態時，最重要的競爭點常會從「誰的單體最強」轉向「誰最會治理組合」。

可套用到：

- AI 模型：單一 LLM → model pool / orchestrator。
- 雲端：單一 cloud → multi-cloud orchestration。
- 晶片：單一 accelerator → heterogeneous compute scheduling。
- 供應鏈：單一 vendor → qualified supplier pool。
- 組織：明星員工 → 能讓專家協作的管理系統。

判斷一個 orchestration layer 是否有價值，可以問五個問題：

1. 它是否真的知道誰擅長什麼？
2. 它是否能在成本與品質間動態取捨？
3. 它是否能在供應商失效時降級但不中斷？
4. 它是否讓治理更容易，而不是更黑箱？
5. 它是否讓使用者保留退出與替換能力？

### 模型：抽象層的雙面性

抽象層的承諾是降低底層複雜度；抽象層的風險是把複雜度與權力集中到自己身上。

Fugu 的價值在於把多模型協作包成單一 API；Fugu 的風險也在於使用者可能不知道內部到底如何決策。這不是缺點本身，而是任何抽象層都會遇到的治理問題。

## 我可以放進資料庫的筆記

- Fugu 的核心不是「又一個 LLM」，而是「orchestrator model」：模型被訓練來呼叫、組合、驗證其他模型。
- Sakana 的路線避開與美國巨頭在單體模型規模上正面硬碰硬，轉向 model orchestration / collective intelligence。
- Fugu 對企業的真正賣點是：單一 API、OpenAI 相容、可替換 agent pool、供應商依賴下降、複雜任務品質提升。
- Fugu 的真正成本不是輸入 / 輸出 token 牌價，而是任務完成過程中的 orchestration token、延遲、內部 agent 數與失敗重試。
- 「AI 主權」不一定等於自研最大模型；也可以是擁有可治理、可替換、可降級的模型編排層。
- 但 Fugu 仍可能形成新的 lock-in：從 model provider lock-in 轉成 orchestration provider lock-in。
- GitHub `SakanaAI/fugu` 目前偏接入與安裝工具，不是完整開源 Fugu 後端；技術透明度主要仍依賴技術報告與官方 API 文件。

## 後續調查清單

1. 實測 Fugu / Fugu Ultra 在相同 coding task、研究 task、文件拆解 task 的品質、延遲與成本。
2. 檢查 API 是否提供足夠 trace：agent 選擇、token 分解、工具呼叫、供應商排除設定。
3. 比較 Fugu 與 Claude Code / Codex / OpenAI Agents SDK / LangGraph 的分工邊界。
4. 追蹤第三方 benchmark 與使用者實測，尤其是長任務、程式維護、資安分析、研究複現。
5. 觀察 Sakana 是否逐步加入自研模型 / 開源模型，降低對美國 frontier providers 的依賴。

## 仍不確定的地方

- 官方 benchmark 的可重現性、測試 scaffold 與 baseline 條件仍需第三方驗證。
- agent pool 內實際包含哪些模型、在不同地區 / plan / compliance 設定下是否不同，公開資訊仍有限。
- 「不受出口管制風險影響」更像產品定位與韌性主張，不應理解成完全不受任何法規或供應鏈限制。
- GitHub repo 的 star / fork 只能代表早期開發者興趣，不代表生產採用成熟度。
- 多 agent 對長任務是否穩定超越單一前沿模型，需要看真實任務中的成本、延遲、失敗率與維護負擔。
