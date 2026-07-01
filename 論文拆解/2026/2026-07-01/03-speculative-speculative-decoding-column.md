# 專欄｜Speculative Speculative Decoding：LLM 推論加速正在從「模型更小」轉向「時間軸重排」

## 原文資訊

- 論文標題：Speculative Speculative Decoding
- 作者：Tanishq Kumar、Tri Dao、Avner May
- arXiv：[`2603.03251v3`](https://arxiv.org/abs/2603.03251v3)
- PDF：[`https://arxiv.org/pdf/2603.03251v3`](https://arxiv.org/pdf/2603.03251v3)
- 分類：cs.LG
- 初版：2026-03-03
- 最新版：2026-05-04
- 備註：ICLR 2026；作者提供 code：[`tanishqkumar/ssd`](https://github.com/tanishqkumar/ssd)
- 本次閱讀範圍：arXiv metadata、Abstract、Introduction，並額外快速掃描 Related Work 的題名層級脈絡；未讀完整方法、實驗、附錄證明，因此以下對方法細節與效能結果只採「論文自稱」與「保守判讀」。
- 收錄性質：使用者指定專欄，今日自動論文選文已滿 2 篇；本篇作為額外專欄，不計入每日自動選文上限。

## 為什麼選這篇

Speculative decoding 近兩年像是 LLM inference 裡的「第二條摩爾定律」：模型參數不一定變小，GPU 不一定更便宜，但我們嘗試把生成過程中被浪費的等待時間拿回來。

這篇 `Speculative Speculative Decoding` 值得整理，不只是因為它提出 Saguaro / SSD，而是它很清楚地揭露了一個趨勢：

> LLM 推論加速的研究焦點，正在從「減少一次 forward 的成本」轉向「重排 decoding 的時間軸」。

傳統 autoregressive decoding 的瓶頸是：token 必須一個接一個生成。Speculative decoding 先用小 draft model 猜一段，再讓大 target model 一次驗證；這已經把「逐 token 等待」改成「批次驗證」。但 SSD 再往前問一步：既然 speculative decoding 自己仍然有「先猜、再驗、再猜」的順序依賴，那能不能連這個順序依賴也消掉？

這就是這篇論文的核心。

## 一句話理解

> 普通 speculative decoding 是「小模型先猜，大模型再驗」；SSD 是「大模型正在驗的同時，小模型預先猜測大模型可能驗出什麼結果，並提前準備下一輪答案」。

換句話說，它不是只加速 token，而是加速「等待關係」。

## 先把 speculative decoding 用第一性原理拆開

Autoregressive LLM 生成一段文字時，本質上在做：

$$
P(x_{1:T}) = \prod_{t=1}^{T} P(x_t \mid x_{<t})
$$

白話：第 $t$ 個 token 必須依賴前面所有 token，所以不能像影像模型那樣自然一次算完。

### 傳統解法：接受 sequential bottleneck

最樸素的生成方式是：

```text
target model 產生 token 1
target model 產生 token 2
target model 產生 token 3
...
```

問題是大模型每一步都很貴，而且現代 GPU 有大量平行運算能力，卻被 sequential dependency 卡住。

### Speculative decoding 的基本賭法

Speculative decoding 引入一個小而快的 draft model：

```text
draft model 先猜 k 個 token
target model 一次驗證這 k 個 token
接受前面連續正確的 token
若中途不接受，再由 target model 補一個 token
```

這個方法的關鍵不是「小模型取代大模型」，而是：

> 小模型只提出候選，大模型仍維持最終分布正確性。

所以 speculative decoding 是一種很有吸引力的工程折衷：它可以保持 target model 的輸出分布，同時減少 target model 被逐 token 呼叫的次數。

## 這篇論文指出的下一個瓶頸

普通 speculative decoding 已經比 autoregressive 快，但它仍有一個順序依賴：

```text
draft 猜 → target 驗 → draft 再根據驗證結果猜下一輪 → target 再驗
```

也就是說，在 target model 正在驗證時，draft model 常常不能直接準備下一輪，因為它還不知道 target 會接受幾個 token、以及 bonus token 會是什麼。

這篇論文的問題設定是：

> 能不能讓 draft model 在 target model 驗證還沒完成時，就先猜「target 可能驗出哪些結果」，並為這些可能結果都預先準備下一輪 speculation？

這就是 **Speculative Speculative Decoding**：對 speculative decoding 本身再做 speculative。

## SSD / Saguaro 的核心想法

Introduction 裡的描述可以整理成三層：

### 1. 把 draft 和 target 放到不同硬體，讓它們同時工作

普通 speculative decoding 常像一條流水線，但仍有明顯等待。

SSD 的想法是：

```text
target model 正在驗證第 n 輪
同時，draft model 在另一張硬體上預測第 n 輪可能驗證結果
並替每一個可能結果準備第 n+1 輪 speculation
```

如果 target 最後真的落在 draft 預先準備的結果集合裡，就可以立即送出下一輪 speculation，省掉 draft 等待時間。

### 2. 它要猜的不只是「接受幾個 token」

驗證結果不是單一 yes/no。它至少包含：

- target 接受了幾個 draft token；
- 如果中途拒絕，target 補出的 bonus token 是什麼；
- 在 sampling 場景下，結果還會受溫度與機率分布影響。

所以 SSD 的困難不是「多跑幾條分支」這麼簡單，而是要在有限 draft compute 下，猜中最可能發生的 verification outcome。

### 3. Saguaro 是針對這個框架的最佳化算法

論文自稱 Saguaro 面對三個問題：

1. **如何預測 verification outcome**：用 draft logits 去猜 bonus token，作者聲稱最高可到 90% accuracy。
2. **如何平衡 cache hit 與 acceptance rate**：預先準備很多分支可能提高命中率，但也可能降低 speculation 品質，需要 sampling 策略平衡。
3. **cache miss 時怎麼 fallback**：如果 target 的驗證結果不在預先準備集合裡，不能讓系統整個停住；fallback 策略會隨 batch size 改變。

作者在摘要與導論中聲稱：Saguaro 平均比最強 speculative decoding baseline 快 30%，相對 autoregressive decoding 最高可達 5x，且改善 throughput-latency Pareto frontier。

## 為什麼這像是一個 LLM 研究趨勢，而不是單篇技巧？

我會把 speculative decoding 趨勢拆成三條線。

### 趨勢一：推論成本變成 LLM 產品的第一級問題

訓練成本很大，但產品每天燒的是 inference。聊天、agent、程式生成、長上下文、multimodal assistant，都會把成本壓到 decoding 上。

對使用者來說，延遲是體驗；對平台來說，延遲是 GPU 利用率與毛利率。

所以 inference 研究不再只是系統工程附屬品，而是模型商業化的核心。

### 趨勢二：加速方向從「模型壓縮」擴展到「分布保持的重排」

早期常見加速包括 quantization、pruning、distillation、KV cache 最佳化。這些通常是在降低單次計算成本。

Speculative decoding 的不同點是：

> 它不一定改變 target model，而是改變 target model 被呼叫的節奏。

這非常重要，因為很多高價值應用不願意犧牲輸出品質或分布一致性。若 speculative decoding 保持 lossless，它就比較容易被服務端採用。

### 趨勢三：研究正在從「猜 token」升級成「猜計算路徑」

這篇 SSD 最有意思的地方，是它把 speculation 的對象從 token 擴大到整個推論流程：

```text
第一代：猜下一個 token
第二代：猜一段 token，讓 target 批次驗證
第三代：猜 target 的驗證結果，提前準備下一輪計算
```

這其實是把 LLM decoding 看成一個可調度的 pipeline，而不是單純的機率抽樣。

如果這條線繼續走下去，未來可能看到更多研究把 decoding 拆成：

- 哪些計算可以提前？
- 哪些分支值得預算？
- 哪些 cache 可以跨請求 reuse？
- 哪些 token / span / block 應該被 parallel verified？
- 在 batch serving、agent 長任務、VLM / VLA 中，speculation 應該在哪一層發生？

## 跟 2026 年 arXiv 脈絡的關係

我快速查了一輪 arXiv metadata，2026 年 6 月底前後仍有大量題名直接含 speculative decoding 或相關變體的論文，例如：

- `BlockPilot: Instance-Adaptive Policy Learning for Diffusion-based Speculative Decoding`
- `Reasoning-aware Speculative Decoding for Efficient Vision-Language-Action Models in Autonomous Driving`
- `When Is a Draft Accepted? A Theory of Acceptance in Speculative Decoding`
- `CSD: Content-aware Speculative Decoding for Efficient Image Generation`
- `JetSpec: Breaking the Scaling Ceiling of Speculative Decoding with Parallel Tree Drafting`
- `RLM-Cascade: Response-Level Speculative Decoding for Cost-Efficient LLM API Serving`
- `EfficientRollout: System-Aware Self-Speculative Decoding for RL Rollouts`

這個列表本身有兩個訊號：

1. speculative decoding 已經從純 LLM text generation 擴散到 diffusion、image generation、VLA、自動駕駛、RL rollout、API serving。
2. 研究焦點越來越像「服務系統 + 模型機率 + 硬體排程」的交會，不是單純模型架構。

所以把 speculative decoding 當成 LLM 研究趨勢是合理的；更精準地說，它是 **inference-time scaling / inference systems** 的核心題目之一。

## 第一性原理：這個方向為什麼會一直出現？

### 1. 生成式模型的價值在輸出 token，但成本也按 token 燒

LLM 服務的基本商業壓力是：

```text
收入 ∝ 有價值的輸出 / 使用者留存
成本 ∝ token 數 × model size × latency × GPU 時間
```

只要 token generation 還是主要互動形式，decoding 加速就會持續有價值。

### 2. 大模型越強，小模型越適合當「猜測器」

如果小模型完全亂猜，speculative decoding 沒用。但現代小模型已經足以模仿大模型在很多局部上下文中的高機率 token。

這形成一個有趣結構：

```text
大模型提供品質與校驗
小模型提供便宜猜測
系統算法負責把猜測變成可用速度
```

這也是 speculative decoding 比單純 distillation 更有吸引力的地方：它不要求小模型完全取代大模型。

### 3. 硬體真正稀缺的不是 FLOPs，而是同步等待

GPU 很會平行算，但 autoregressive decoding 很會讓 GPU 等。SSD 的哲學是：

> 如果不能消除 sequential dependency，就預先計算它可能走向的分支。

這和 CPU branch prediction、資料庫 query planning、網路 prefetching 都有一點相似：你不是真的知道未來，但你可以用機率替未來買保險。

## 這篇論文可能真正推動的思考模型

我會把它抽象成：

> **不要只問「這一步怎麼算更快」，要問「下一步在等什麼，我能不能在等待期間賭它的結果」。**

這個模型可以套到很多 AI system 問題：

- Agent 工具調用：等待工具回傳時，能不能先準備幾個可能分支？
- RAG：等待檢索結果時，能不能先生成查詢改寫或答案骨架？
- VLA / robot：等待 perception grounding 時，能不能先展開幾個 action plan？
- 多模型 routing：等待大模型判斷時，小模型能不能先跑候選路徑？

這篇論文的名字有點玩笑感，但它指向的是一個嚴肅趨勢：AI 系統會越來越像投機式處理器，而不是單線程文字生成器。

## 我會保守看待的地方

1. **硬體假設很重要**：SSD 需要 draft 和 target 在不同硬體上並行，部署成本與資源編排不是免費的。
2. **batch size / temperature 會影響收益**：Introduction 已經提到 cache miss 在大 batch 與高 temperature 下會更常見，所以效益不是所有服務場景都一樣。
3. **baseline 很關鍵**：30% faster 是對「最強 speculative decoding baselines」的論文自稱；實際線上系統還要看 batching、KV cache、network overhead、scheduler、model mix。
4. **lossless 不代表簡單採用**：即使輸出分布保持，系統複雜度、fallback、debug、資源隔離都會提高。

## 可放進資料庫的筆記

> Speculative decoding 的研究趨勢，不只是讓小模型替大模型猜 token；它正在變成 LLM inference 的時間軸重排技術。`Speculative Speculative Decoding` 的關鍵洞察是：普通 speculative decoding 仍有「draft 等 verification 結果」的順序依賴，因此可以在 target 驗證時，讓 draft 預測可能的 verification outcomes 並提前準備下一輪 speculation。這代表 LLM 推論優化正在從模型壓縮、單次 forward 加速，走向更像 branch prediction / pipeline scheduling 的系統化推論設計。

## 後續想追的問題

1. SSD 在真實 API serving 裡，對高併發、多租戶、不同 prompt 長度的效果如何？
2. speculative decoding 會不會和 reasoning model 的長 chain-of-thought 產生新的互補：越長推理越容易用 speculation amortize？
3. VLA / robotics 裡的 speculative decoding 是否會從 token 層延伸到 action chunk / plan branch？
4. 如果未來模型原生支援 multi-token prediction，傳統 draft model 還需要存在嗎？還是 speculative decoding 會變成模型架構的一部分？
5. GPU cluster scheduler 是否會開始把「speculation worker」當成一種常駐角色，就像 web backend 裡的 cache / prefetch worker？
