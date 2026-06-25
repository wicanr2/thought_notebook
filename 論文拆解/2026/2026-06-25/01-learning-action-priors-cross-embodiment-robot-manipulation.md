# Learning Action Priors for Cross-embodiment Robot Manipulation

## 原文資訊

- 論文：Learning Action Priors for Cross-embodiment Robot Manipulation
- 作者：Dong Jing, Tianqi Zhang, Jiaqi Liu, Jinman Zhao, Zelong Sun, Li Erran Li, Zhiwu Lu, Mingyu Ding
- arXiv ID：2606.26095v1
- 分類：cs.RO, cs.AI, cs.CV
- 發表 / 更新：2026-06-24 / 2026-06-24
- 連結：[abs](https://arxiv.org/abs/2606.26095v1) / [pdf](https://arxiv.org/pdf/2606.26095v1)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-06-25

## 為什麼選這篇

這篇很適合放進 Physical AI 的思維筆記，因為它切到 Vision-Language-Action（VLA）模型的一個底層不平衡：現在的 VLA 通常繼承了很強的視覺與語言先驗，但「動作模組」常常像新接上去的器官，沒有同等程度的物理運動先驗。

換句話說，模型可能很會「看」與「理解指令」，但不一定一開始就有好的「如何連續移動」的結構感。這對跨機器人 embodiment 特別重要，因為不同機器人的 action space、運動分布、任務形式都不同。

我選它不是因為已經確認方法有效，而是因為它的問題設定很有價值：如果 Physical AI 要從單一機器人 demo 走向 generalist robot policy，關鍵不只是更多語言與視覺資料，也可能需要把「動作」本身視為一種可預訓練的結構。

## 一句話理解

這篇論文想解決的問題是：VLA 模型不能只靠視覺語言先驗，還需要先讓 action module 學會跨 embodiment 的時間動作結構，再進行視覺、語言與控制的對齊。

## Summary / Abstract 說了什麼

摘要指出，多數 VLA 模型會在 VLM backbone 上接一個 action module，然後把整個 policy 一起最佳化。這樣做能繼承 VLM 的視覺與語言能力，但 action module 幾乎要從零開始學物理運動。

作者認為這會造成早期訓練的雙重負擔：模型一方面要發現 temporal action dynamics，另一方面又要學 cross-modal alignment。到了 cross-embodiment 場景，這個問題更嚴重，因為不同機器人的任務、action space、運動分布都不一樣。

論文提出兩階段訓練：

1. **Stage 1：action prior learning**  
   用不含視覺與語言的 action trajectories，先訓練一個輕量 flow-matching encoder-decoder action module，讓它學到 temporal motion structure。

2. **Stage 2：VLA training with prior transfer**  
   把 Stage 1 學到的 action prior 轉移到 VLA 訓練中，包括 decoder reuse、early-stage latent distillation，以及用 encoder 壓縮 state-action history 成 temporal context token。

摘要宣稱，這個方法在 13 個跨 embodiment 任務、模擬與真實平台上，帶來更快收斂、更高成功率，並在 data-scarce real-world tasks 上有更強表現。

## Introduction 的問題設定

Introduction 的鋪陳很清楚：

第一步，它先承認 VLA 已經成為 robotic manipulation 的重要範式。這些模型建立在 foundation VLM 或 video generation model 上，能把高階語言指令、視覺觀察與低階 physical execution 接起來。作者也承認這已經在 tabletop manipulators、quadruped robots、humanoid systems 上展現潛力。

第二步，它指出這些成功主要來自視覺與語言先驗，而不是動作先驗。這是整篇的核心轉折：VLA 的「V」和「L」有大規模 foundation model 支撐，但「A」常常缺乏等價的預訓練結構。

第三步，它把問題定義成 training pipeline 的 imbalance。標準做法是把 action module 接到 foundation VLM 上，再用 imitation learning 共同最佳化。但 action module 常從 random weights 或不相關 modality 的參數初始化，因此早期梯度可能不穩，甚至干擾 foundation backbone。

第四步，它把問題推到 cross-embodiment。單一機器人已經難，跨機器人更難，因為不同 robot 的 action spaces、motion distributions、tasks 都不一致。這讓「從零學動作」變成更大的瓶頸。

第五步，作者提出類比：先讓 action module 像 blindfolded apprentice 一樣練基本動作，不看視覺、不聽語言，只學 action trajectories 裡的 intrinsic temporal structure。這個比喻很重要，因為它把方法從技術細節翻譯成可理解的訓練哲學：先學會動，再學會看著世界動。

## 研究的第一性問題

### 基本問題

Physical AI 的底層問題不是「模型是否理解語言」，而是「模型能不能把語言與視覺理解轉成穩定、連續、符合物理約束的行動」。

對 robot manipulation 來說，action 不是離散 token 的漂亮輸出，而是會進入真實世界的連續控制訊號。這些訊號有時間結構、平滑性需求、動力學限制，也會受到 robot embodiment 影響。

### 約束

- 不同機器人的 action space 不同。
- 真實世界 demo 昂貴且稀缺。
- VLM 的語義先驗不等於物理動作先驗。
- 早期 joint training 可能同時承擔太多任務：學運動、學對齊、學策略。
- action head 若一開始太弱，可能提供不穩定梯度，拖累整體 VLA 訓練。

### 既有方法卡點

既有 VLA pipeline 偏向把 action module 當成附加輸出頭，而不是一個需要獨立先驗的物理模組。這讓模型的語義理解能力與動作生成能力不對稱。

這篇論文的問題意識可以寫成：

> 如果視覺與語言都需要 foundation prior，為什麼 action 不需要 motion prior？

### 作者試圖移動的邊界

作者不是只改 VLA architecture，而是把訓練順序重排：先學 action prior，再做 cross-modal VLA alignment。這個重排的意義是把「動作結構」從 joint training 裡拆出來，變成一個可先學、可轉移、可壓縮歷史的模組。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 指出 absence of action priors 是 VLA training 的 structural bottleneck。
- 提出 two-stage framework，先學 action-centric robot motion，再做 cross-modal policy alignment。
- 用 flow-matching encoder-decoder action module 學 compact motion-aware embeddings。
- 把 action prior 用於 decoder reuse、early-stage latent distillation、history compression。
- 在 13 個 cross-embodiment tasks、simulation 與 real-world Franka platform 上宣稱更快收斂、更高成功率、更好的 long-tail stability。

### 我的保守判讀

這篇的真正價值也許不只在具體方法，而是在提出一個 Physical AI 的設計原則：不要把 action module 當成 VLM 後面接上的薄輸出層，而要把 action 看成一個有自己統計結構與時間幾何的 domain。

但因為本次只讀 summary 與 introduction，我還不能判斷：

- 實驗設定是否公平；
- 13 個任務的多樣性是否足以支持 cross-embodiment claim；
- action-only data 的來源、規模與品質是否容易取得；
- flow-matching action prior 是否真的比其他 sequence/action representation 更關鍵；
- 方法在更複雜或更長時序真實任務中是否穩定。

## 可放進資料庫的筆記

- VLA 的不平衡：V 和 L 常有 foundation prior，但 A 可能沒有同等的 action prior。
- Physical AI 的核心不是理解世界，而是把理解轉成受物理約束的連續行動。
- 跨 embodiment 的困難來自 action space、motion distribution、robot morphology 與任務分布同時改變。
- 如果一個系統早期訓練要同時學太多東西，可以思考是否應該先把其中一個結構拆出來預訓練。
- action prior 可以被理解為「動作空間裡的語法」：它不保證任務成功，但讓輸出比較像合理運動。
- 對 Physical AI 來說，資料稀缺不只發生在語義標註，也發生在真實世界長尾動作。
- 好的 robot policy 也許需要三種先驗：視覺先驗、語言先驗、動作先驗。
- 訓練順序本身是一種架構設計：先學什麼，會改變後面對齊的難度。

## 後續想追的問題

1. Stage 1 的 action-only data 是否需要來自同一批 robot，還是可以大規模混合不同 embodiment？
2. action prior 學到的是通用 motion structure，還是只是資料集特定的動作分布？
3. decoder reuse 和 latent distillation 哪一個貢獻最大？
4. flow matching 是否是必要選擇，還是任何能建模 action trajectory 的 sequence model 都可行？
5. 如果 robot morphology 差異更大，action prior 會轉移還是會誤導？

## 我的理解總結

這篇論文把 VLA 的問題從「如何讓模型看懂並聽懂」推進到「如何讓模型在行動之前就有動作結構」。它提醒我們，Physical AI 的 foundation model 不能只建立在 vision-language representation 上；如果最後輸出要進入真實世界，那 action 本身也應該有可學習、可轉移、可治理的先驗。
