# From Foundation to Application: Improving VLA Models in Practice

## 原文資訊
- 論文：From Foundation to Application: Improving VLA Models in Practice
- 作者：Wei Wu, Fangjing Wang, Fan Lu, He Sun, Shi Liu, Yunnan Wang, Yibin Yan, Yong Wang, Shuailei Ma, Xinyang Wang, Yibin Liu, Shuai Yang, Tianxiang Zhou, Kejia Zhang, Lei Zhou, Cheng Su, Nan Xue, Bin Tan, Han Zhang, Youchao Zhang, Fei Liao, Xing Zhu, Yujun Shen, Kecheng Zheng
- arXiv ID：2607.06403v1
- 分類：cs.RO
- 發表 / 更新：2026-07-07 / 2026-07-07
- 連結：[abs](https://arxiv.org/abs/2607.06403v1) / [pdf](https://arxiv.org/pdf/2607.06403v1)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-07-08

## 為什麼選這篇

這篇是明確的 VLA / robot foundation model 題目，且不是只做單一 manipulation skill，而是把焦點放在「foundation model 到實際應用」之間的落差。對 Physical AI 筆記庫來說，這是重要主題：VLA 如果要從 benchmark 走到部署，通常瓶頸不只在模型架構，還包含資料、embodiment coverage、action space 與 temporal reasoning。

作者提出 LingBot-VLA 2.0，摘要與 Introduction 都把改進聚焦在三件事：跨任務與跨 embodiment 的 generalization、更大的 action space、以及 predictive dynamics modeling。這三個方向剛好對應真實機器人應用的幾個基本限制：資料不夠多樣、控制介面不夠完整、模型缺少對未來狀態的預測。

我把它選為今天第二篇，是因為它和第一篇 semantic handoff 互補：第一篇看長任務 skill 交接的診斷，這篇看 VLA foundation model 如何擴大資料與控制覆蓋。兩者都在回答「VLA 如何離開實驗室設定」。

## 一句話理解

這篇把 VLA 實用化視為資料規模、跨 embodiment 動作空間與未來動態預測三者要一起補強的系統問題。

## Summary / Abstract 說了什麼

摘要說，雖然 VLA foundation models 近期進展很快，但實驗室條件與真實應用之間仍有落差。作者提出 LingBot-VLA 2.0，從三個功能面改進既有 LingBot-VLA。

第一是 generalization across tasks and embodiments：作者聲稱重整資料處理流程，整理約 60,000 小時的 pretraining data，其中包含 50,000 小時、跨 20 種 robot configurations 的 robot trajectories，以及 10,000 小時 egocentric human videos。

第二是 expanded action space：除了 dual-arm hardware，系統也支援 head、waist、mobile base、dexterous hands 等自由度，讓機器人能處理更接近實際場景的複雜任務。

第三是 predictive dynamics modeling：作者把 future prediction 當作 proxy task，利用 video representation model 的 semantic priors 與 depth estimation model 的 geometric cues 來改善 temporal reasoning。摘要稱 GM-100 benchmark 的 generalist setting 評估支持這些改動的效益，並提到 LingBot-VLA 2.0 展現跨 embodiment 的長程 mobile manipulation 能力。

## Introduction 的問題設定

Introduction 先把 VLA 放在 generalist robot policies 的脈絡中：預訓練 vision-language models 提供多模態 alignment 與語意表徵，讓 VLA models 更能理解複雜場景並跨任務泛化；同時，擴大機器人資料的數量與多樣性，也被視為提升 VLA 系統能力的重要方向。

接著，作者指出實驗室 benchmark 與真實部署之間仍有落差。真實機器人會面對更廣的 embodiment diversity、更豐富的 action spaces、更動態的環境。也就是說，泛化不只是任務轉移，還包括能不能處理異質機器人配置與不同資料來源。

Introduction 把現有方向整理成幾類：有些工作擴大 robot pretraining data，有些做 embodiment-aware architecture，有些用 latent action model 改善動態環境中的 decision making。作者認為，實用化 VLA 不能只靠單一方向，而需要同時處理 data、embodiment、predictive capability。

LingBot-VLA 2.0 的定位就是這個綜合路線：重新設計資料處理、建立大規模跨 embodiment corpus；擴大控制介面到 head、waist、mobile base、dexterous hands；並加入 future prediction 作為代理目標，讓模型不只是反應當前觀察，也能學習動作後果與場景演化。

## 研究的第一性問題

- **基本問題**：VLA foundation model 要從實驗室 benchmark 走向實際機器人部署，缺的究竟是更大的模型、更好的資料，還是更完整的控制介面？
- **約束**：真實機器人平台差異大，action space 不只雙臂；環境會變動，模型需要預測未來，而不是只根據當前影像反應。
- **既有方法卡點**：許多 VLA setting 仍偏向標準 dual-arm manipulation 或有限 embodiment，容易把「benchmark 可行」誤認為「部署可行」。
- **作者試圖移動的邊界**：把 VLA 擴展到更多資料來源、更多 robot configurations、更多自由度，並用 predictive dynamics proxy task 補強 temporal reasoning。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- LingBot-VLA 2.0 透過重整資料處理流程，整理約 60,000 小時 pretraining data，涵蓋 20 種 robot configurations 與 egocentric human videos。
- 系統支援比標準雙臂更大的 action space，包括 head、waist、mobile base、dexterous hands。
- 透過 future prediction proxy task，結合 video representation 的語意 priors 與 depth estimation 的幾何 cues，改善 temporal reasoning。
- 在 GM-100 benchmark 的 generalist setting 與兩個 long-horizon mobile manipulation tasks 中，作者宣稱這些改動有實用能力提升。

### 我的保守判讀

- 這篇比較像 VLA 實用化路線圖與系統升級報告，而不是只提出單一演算法技巧。它的價值在於把 data scaling、embodiment coverage、action-space design、temporal prediction 放在同一個部署問題裡。
- 摘要中的 60,000 小時資料與 20 種 robot configurations 是重要訊號，但我目前只讀到 Introduction，尚未檢查資料組成、清理標準、模型訓練細節與評估設計，因此不能判斷這些資料是否足以支撐跨 embodiment 泛化結論。
- predictive dynamics modeling 的概念合理，但摘要與 Introduction 還不足以判斷它對實際控制的貢獻有多大，或只是提供額外 representation learning signal。

## 可放進資料庫的筆記

- **VLA 實用化三角**：資料多樣性、embodiment 覆蓋、action space 完整度，三者缺一都會限制部署。
- **Action space 是產品邊界**：VLA 支援哪些自由度，決定它能不能處理 head gaze、waist motion、mobile base、dexterous hand 等真實任務需求。
- **Human egocentric video 作為補充資料**：10,000 小時 egocentric human videos 可能提供場景與互動 priors，但是否能轉成 robot control 能力需要實驗支持。
- **Predictive dynamics proxy task**：把未來預測當作訓練目標，直觀上是在逼模型學「動作後果」而不只是當下視覺 grounding。
- **跨 embodiment 泛化不是一句話**：需要同時處理資料對齊、控制維度差異、頻率、末端執行器、身體自由度與任務分布。
- **Benchmark 到部署的落差**：GM-100 或其他 generalist benchmark 若不能覆蓋 mobile manipulation、whole-body coordination，仍可能高估實用性。
- **系統型 VLA 論文的閱讀方法**：先拆 data pipeline、action interface、training objectives、evaluation tasks，再看單一模型分數。

## 後續想追的問題

- 60,000 小時資料的品質與分布如何？哪些 robot configurations 佔比最高？
- human egocentric videos 在訓練中如何與 robot trajectories 對齊？只是表徵學習，還是影響 action prediction？
- expanded action space 如何處理不同 embodiment 的維度不一致？是否使用 mask、tokenization、MoE 或其他 routing？
- predictive dynamics modeling 的 ablation 效果是否穩定，尤其是在 long-horizon mobile manipulation 上？
- GM-100 generalist setting 的任務是否足以代表真實部署，或仍偏向可控 benchmark？
