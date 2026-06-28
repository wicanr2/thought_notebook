# Amazon 倉儲機器人與 QNX：從「會動的 AI」看實體 AI 的安全軟體層

## 原始線索

- 影片：[Fox Business｜Amazon’s $1 BILLION bet on robots is changing lives](https://www.youtube.com/watch?v=4xVeuLfCdj8)
- 影片日期：2026-06-27
- 影片主題：Fox Business 在 Amazon Westborough, MA facility 展示 Amazon Proteus robot，並把 Amazon 的機器人投資包裝成「改變工作與物流」的商業新聞。
- 本筆記定位：使用者指定延伸查詢，收集與影片相關的 Amazon robotics / QNX / Physical AI 新聞與資料，並判斷兩者關聯強度。

## 一句話拆解

Amazon 的新聞在講「AI 進入倉儲現場」；QNX 的新聞在講「AI 真正進入現場後，需要 deterministic、safety-certified 的軟體底座」；目前可找到 **Amazon Industrial Robotics 招募直接列出 QNX 作為 RTOS 經驗範例**，但尚未找到公開資料證明 Proteus 或 Amazon 倉儲機器人實際採用 QNX。

## 主要來源索引

### Amazon robotics 線

- [About Amazon｜Amazon unveils new Proteus robot in €10 billion European investment in its fulfillment centers](https://www.aboutamazon.com/news/operations/amazon-proteus-robot-europe-investment-employee-support)（2026-06-04）
  - next-generation Proteus 可用自然語言指令接任務。
  - Amazon 計畫投資超過 €10 billion 擴建與現代化歐洲 fulfillment network。
  - 原版 Proteus 在美國 25 個 fulfillment centers 部署，可搬運接近 400 公斤的重型 carts。

- [About Amazon｜Amazon announces new robots, faster delivery, and 25,000 jobs in Europe](https://www.aboutamazon.com/news/operations/amazon-europe-robotics-delivery-investment)（2026-06-04）
  - Amazon 宣布超過 €10 billion 的歐洲 fulfillment center robotics 投資。
  - 歐洲 fulfillment center workforce 預計增加 25,000 人。
  - next-generation Proteus 可以用 conversational prompts 接收工作。

- [About Amazon｜Amazon launches a new AI foundation model to power its robotic fleet and deploys its 1 millionth robot](https://www.aboutamazon.com/news/operations/amazon-million-robots-ai-foundation-model)（2025-07-01）
  - Amazon 已部署第 100 萬台機器人。
  - DeepFleet 是用來協調機器人 fleet movement 的 generative AI foundation model。
  - Amazon 表示 DeepFleet 可提升 robot fleet travel time / efficiency 約 10%。
  - Proteus 被描述為 Amazon 第一個 fully autonomous mobile robot，可在開放區域安全繞過員工並搬運重型 carts。

- [About Amazon｜Introducing the $1 billion Amazon Industrial Innovation Fund](https://www.aboutamazon.com/news/innovation-at-amazon/introducing-the-1-billion-amazon-industrial-innovation-fund)（2022-04-21）
  - Amazon 建立 $1 billion venture investment program，投資 customer fulfillment、logistics、supply chain 的創新。
  - 投資範圍包含 robotics、AI、machine learning、autonomy、safety、logistics 等。
  - 第一批投資包含 Agility Robotics、BionicHIVE、Mantis Robotics、Vimaan、Modjoul。

- [About Amazon｜Amazon announces 2 new ways it's using robots to assist employees and deliver for customers](https://www.aboutamazon.com/news/operations/amazon-introduces-new-robotics-solutions)（2023-10-18）
  - 當時 Amazon 已有超過 750,000 robots 與員工協作。
  - Sequoia 可讓 fulfillment center 收到的 inventory 被辨識與儲存的速度提升最多 75%。
  - Sequoia 可讓訂單通過 fulfillment center 的處理時間減少最多 25%。
  - Amazon 開始測試 Agility Robotics 的 Digit，用於 tote recycling 等重複搬運工作。

- [Amazon.jobs｜Software Development Engineer II, Amazon Industrial Robotics](https://www.amazon.jobs/en/jobs/3168533/software-development-engineer-ii-amazon-industrial-robotics)
  - 這是目前找到的 **Amazon 與 QNX 最直接的公開連結**。
  - 職缺要求包含：embedded C applications that use an RTOS，例如 FreeRTOS、uC-OS、Zephyr、VxWorks、**QNX**，以及 embedded Linux environments。
  - 注意：這只能證明 Amazon Industrial Robotics 招募時把 QNX 視為相關 RTOS 經驗；不能推出 Proteus 實際使用 QNX。

- [Amazon.jobs｜Software Development Engineer II - Amazon Robotics, Proteus AMR, Motion](https://www.amazon.jobs/en/jobs/10393271/software-development-engineer-ii-amazon-robotics-proteus-amr-motion)
  - Proteus AMR motion 團隊工作內容包括 task planning、motion planning、control、navigation software。
  - 職缺文字提到 real-time robotics systems、embedded systems 或 real-time software development。
  - 這能證明 Proteus 軟體問題包含 real-time robotics / control，但沒有直接提到 QNX。

### QNX / BlackBerry / Physical AI 線

- [QNX｜QNX and NVIDIA Deepen Collaboration to Advance Safety-Critical Edge AI Across Robotics, Medical, and Industrial Systems](https://qnx.software/en/press-release/2026/qnx-and-nvidia-deepen-collaboration-to-advance-safety-critical-edge-ai-across-robotics-medical-and-industrial-systems)（2026-04-20）
  - QNX OS for Safety 8.0 整合 NVIDIA IGX Thor 與 Halos Safety Stack。
  - QNX 稱此整合用於 robotics、medical technologies、industrial applications 等 regulated AI-enabled systems。
  - 文章明確說明：在 NVIDIA accelerated compute 做 AI perception / planning / decision-making 的同時，QNX 提供 deterministic、microkernel-based RTOS 承載 real-time controls 與 safety concepts。

- [QNX｜New QNX Research Finds Software is the Biggest Bottleneck to Robotics Innovation as Physical AI Accelerates](https://qnx.software/en/press-release/2026/new-qnx-research-finds-software-is-the-biggest-bottleneck-to-robotics-innovation-as-physical-ai-accelerates)（2026-05-27）
  - 調查 1,000 名 robotics developers。
  - 89% 表示 Physical AI 對未來計畫重要。
  - 95% 表示 deterministic、real-time behaviour 對其系統重要。
  - 91% 仍使用 general-purpose operating systems 跑 real-time 或 safety-critical workloads。
  - 這篇是理解 QNX 市場敘事的核心：機器人從 demo 走向人類共處現場時，軟體瓶頸從「能不能跑模型」變成「能不能可靠、即時、可驗證地動」。

- [QNX｜Robotics industry page](https://qnx.software/en/industries/robotics)
  - QNX 把自己定位為 robotics industry 的 embedded software foundation。
  - 應用場景包含 industrial automation、logistics、autonomous mobile robots。
  - QNX 強調 real-time performance、safety certifications、security、predictability、determinism。

- [QNX｜QNX OS for Safety](https://qnx.software/en/software/products-and-solutions/qnx-os-and-os-for-safety)
  - QNX OS for Safety 8.0 是 certified RTOS for functional safety and cybersecurity。
  - QNX 強調 safe-by-design microkernel architecture、deterministic behavior、system integrity。

- BlackBerry Q1 FY2027 earnings release（PDF）：[Q1 FY27 Earnings Release FINAL](https://irp.cdn-website.com/586e2b1b/files/uploaded/Q1+FY27+Earnings+Release+FINAL.pdf)（2026-06-25）
  - BlackBerry revenue 年增 26% 至約 $153 million。
  - QNX revenue 年增 26% 至 $72.3 million。
  - QNX segment adjusted gross margin 年增 5 個百分點至 86%。
  - QNX segment adjusted EBITDA 年增 52% 至 $19.3 million。
  - BlackBerry 將 physical AI、software-defined vehicles、general embedded market 視為 QNX 的多年度成長機會。

- Reuters / CTV 轉載：[BlackBerry lifts annual revenue forecast as QNX unit powers growth](https://www.ctvnews.ca/business/article/blackberry-lifts-annual-revenue-forecast-as-qnx-unit-powers-growth/)（2026-06-25）
  - BlackBerry 提高年度營收預測，理由是 QNX division 動能延續。
  - 文章稱 QNX provides secure real-time operating systems for mission-critical embedded systems，最主要在 automotive sector。
  - QNX backlog 接近 $1 billion in future royalties。

- Reuters / CTV 轉載：[Amazon unveils new AI warehouse robot in US$12 billion Europe push](https://www.ctvnews.ca/business/article/amazon-unveils-new-ai-warehouse-robot-in-us12-billion-europe-push/)（2026-06-04）
  - Amazon 發表 upgraded AI-powered mobile robot，可回應 conversational prompts。
  - 這是 €10 billion / US$11.6 billion 歐洲 fulfillment network 投資的一部分。
  - 文章把 next-generation Proteus、STARK、Vulcan 放在同一條 warehouse robotics investment 線上。

## 證據強度地圖：Amazon 和 QNX 到底有沒有連上？

| 層級 | 判斷 | 目前證據 |
|---|---|---|
| 直接產品採用 | 未找到 | 尚未找到公開資料說 Proteus、DeepFleet、Sequoia、Vulcan 或 Amazon 倉儲機器人「使用 QNX」。 |
| 直接招募線索 | 有 | Amazon Industrial Robotics 職缺列出 RTOS 經驗範例：FreeRTOS、uC-OS、Zephyr、VxWorks、QNX。 |
| 技術問題重疊 | 強 | Proteus / Amazon Robotics 明確涉及 embedded software、real-time robotics systems、motion planning、control、safety、human-robot coexistence。 |
| 產業敘事重疊 | 很強 | QNX 2026 敘事主軸正是 Physical AI、robotics、AMR、deterministic RTOS、安全認證、AI perception 到 physical action 的可靠轉換。 |
| 投資敘事重疊 | 強 | Amazon 是場景與需求端；QNX 是安全軟體層與嵌入式平台端。兩者都受益於「AI 從螢幕走向現場」這條主線。 |

## 第一性原理拆解

### 1. 為什麼 Amazon 倉儲機器人不是單純的「AI 模型問題」？

在聊天機器人裡，錯一個 token 通常只是體驗問題；在倉儲機器人裡，錯一個動作可能是碰撞、停線、傷害、貨損或產能下降。

所以 Proteus 這類 AMR 的基本問題不是「能不能理解指令」而已，而是：

1. 能不能把高層指令拆成可執行任務。
2. 能不能在多人、多車、多貨架、多變動路徑的現場做 motion planning。
3. 能不能用 sensors / cameras / edge compute 感知環境。
4. 能不能在毫秒級時間內做 predictable response。
5. 能不能在異常時 fail safe，而不是只回傳錯誤訊息。

換句話說，倉儲機器人的護城河不是「模型會不會講話」，而是 **模型輸出的意圖如何被轉成安全、即時、可驗證的物理行動**。

### 2. QNX 的位置：不是替代 AI，而是約束 AI

QNX 的市場敘事很精準：AI 可以是 probabilistic，但機器人的底層動作不能完全 probabilistic。尤其當機器人開始跟人共用空間時，系統需要 deterministic behavior。

可抽象成一個三層架構：

```text
高層 AI / 任務理解：我要做什麼？
中層 planning / coordination：我要走哪裡、先做什麼、避開誰？
底層 RTOS / control / safety：我每一個動作能否準時、可靠、可驗證地執行？
```

Amazon 的 DeepFleet 與 next-gen Proteus 強調上中層：fleet coordination、conversational prompts、route / timing / priority。QNX 強調底層：determinism、real-time control、safety certification、microkernel isolation。

這兩條新聞放在一起看，真正的題目是：**Physical AI 的價值鏈正在分層**。

### 3. Amazon 為什麼是需求端的最佳案例？

Amazon 有幾個一般 robotics startup 很難同時擁有的條件：

- 巨量場景：全球 fulfillment / sortation / delivery network。
- 巨量資料：DeepFleet 直接從 fulfillment centers 與 sortation centers 的 robot movement data 學習。
- 明確 ROI：縮短 travel time、降低 cost-to-serve、提升配送速度。
- 自有需求：不用等外部客戶教育市場，自己就是最大客戶。
- 人機共處壓力：倉儲不是封閉 demo 場，必須跟員工共用空間。

這也是為什麼 Amazon robotics 新聞值得和 QNX 一起讀：Amazon 呈現需求側的規模化壓力；QNX 呈現供給側的可靠性與安全認證壓力。

## 新聞敘事拆解

### Fox Business 影片的寫法

影片標題把重點放在 “$1 BILLION bet” 與 “changing lives”。這種商業新聞寫法通常會把複雜技術壓縮成三個訊號：

1. 資本投入夠大，所以不是玩具。
2. 現場機器人會動，所以可視化效果強。
3. 員工工作被改變，所以有社會與勞動敘事。

但這種寫法容易漏掉真正核心：倉儲機器人的困難不是「讓機器動起來」，而是讓它在高密度、低容錯、人機混行的場域裡穩定運作。

### QNX 2026 新聞的寫法

QNX / BlackBerry 的敘事則是把自己從「車用嵌入式軟體」重新擺進 Physical AI：

1. AI 越來越會做決策。
2. 這些決策會進入車、機器人、醫療、工業設備。
3. 一旦 AI 牽涉物理行動，就需要 real-time determinism 與 safety certification。
4. QNX 因為 microkernel、RTOS、安全認證與 automotive 經驗，可以變成 physical AI stack 的底座。

這是典型的「舊能力重新命名到新週期」：QNX 不是突然變成 AI 公司，而是把原本 mission-critical embedded software 的能力，重新掛到 AI / robotics 的成長敘事上。

## 可抽象成的思考模型：AI 進入現實世界後，價值從模型轉向控制層

### 模型

```text
純軟體 AI：輸出錯誤 → 使用者修正 / 重試
實體 AI：輸出錯誤 → 可能造成碰撞 / 傷害 / 停線 / 法規責任
```

因此實體 AI 的價值鏈會從「誰的模型最聰明」逐步轉向：

- 誰有最多真實場景資料？
- 誰能把 AI decision 接到 real-time control？
- 誰能證明安全？
- 誰能規模化部署並維護？
- 誰能通過車用、醫療、工業等安全認證？

Amazon 的答案偏向場景、資料、部署、物流 ROI。QNX 的答案偏向 deterministic software foundation、安全認證、嵌入式可靠性。

## 我可以放進資料庫的筆記

- **不要把 Amazon robotics 與 QNX 直接畫等號。** 目前只找到 Amazon Industrial Robotics 職缺列 QNX 作為 RTOS 經驗範例，尚未找到 Proteus 採用 QNX 的公開證據。
- **Amazon 是 Physical AI 的 demand-side case study。** 它把 conversational AI、fleet foundation model、AMR、robotic arms、touch sensing、物流 ROI 放進同一個倉儲系統。
- **QNX 是 Physical AI 的 safety-layer case study。** 它的 2026 敘事不是「做 AI 模型」，而是「讓 AI 驅動的物理系統能 deterministic、real-time、safety-certified 地執行」。
- **真正值得追的不是單一機器人，而是 stack 分層。** 高層模型、中層 planning、底層 RTOS / safety / control 會逐漸形成不同供應鏈與估值邏輯。
- **投資敘事風險：** QNX 能否吃到 robotics 成長，不只取決於 Physical AI 是否成長，也取決於 robotics OEM 是否願意從 Linux / ROS / 自研 RTOS / 其他商用 RTOS 遷移到 safety-certified commercial stack。

## 後續追蹤問題

1. Amazon Proteus / Sequoia / Vulcan / STARK 是否有公開供應商、專利、工程演講或招聘內容揭露底層 RTOS / middleware？
2. Amazon Robotics 的 real-time control stack 是完全自研、Linux + real-time patches、ROS2、商用 RTOS，還是混合架構？
3. QNX + NVIDIA IGX Thor 的 robotics design wins 是否會公布具名客戶？
4. QNX 的 General Embedded Market（GEM）營收是否能從 automotive 外溢到 robotics、medical、industrial automation？
5. 如果 robotics 開發者 91% 仍在用 general-purpose OS 跑 real-time / safety-critical workloads，真正阻礙遷移到 QNX 的是成本、開發者生態、ROS 相容性、認證流程，還是既有架構 inertia？

## 仍不確定的地方

- 沒有公開證據顯示 Proteus 實際使用 QNX。
- Amazon.jobs 的 QNX 關鍵字是技能要求，不等於產品採用。
- QNX 官方對 robotics / Physical AI 的說法是自我定位與市場敘事，需要後續用具名客戶、營收分拆、design win、開發者採用率驗證。
- Reuters / CTV 的 BlackBerry QNX 財務報導可支持「QNX 正在成為成長敘事」，但不能直接支持「Amazon 是 QNX 客戶」。
