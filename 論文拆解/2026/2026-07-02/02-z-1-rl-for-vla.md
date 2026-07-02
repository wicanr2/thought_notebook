# Z-1: Efficient Reinforcement Learning for Vision-Language-Action Models

## 原文資訊
- 論文：Z-1: Efficient Reinforcement Learning for Vision-Language-Action Models
- 作者：Lang Cao, Renhong Chen, Luyi Li, Peng Wang, Mofan Peng, Yitong Li
- arXiv ID：2606.31846v1
- 分類：Robotics (cs.RO); Artificial Intelligence (cs.AI)
- 發表 / 更新：Submitted on 30 Jun 2026
- 連結：[abs](https://arxiv.org/abs/2606.31846) / [pdf](https://arxiv.org/pdf/2606.31846)
- 本次閱讀範圍：Summary/Abstract + Introduction；未讀 methods / experiments / results 章節
- 擷取日期：2026-07-02

## 為什麼選這篇

這篇延續近期 VLA for robotics 的核心問題：如果 VLA policy 主要靠 behavior cloning 或 supervised fine-tuning 從固定 demonstrations 學習，它是否能從自己的失敗中進步？Z-1 把 LLM 領域常見的 RL post-training / GRPO 語彙帶進 flow-based VLA policy，屬於 LLM 訓練方法與 robot control 的明確交會。

我選它作為今日第二篇，是因為它處理的不是單純資料收集，而是「VLA policy 如何在互動中被 post-train」。這和第一篇 Human-as-Humanoid 的資料轉換問題互補：一篇問 labels 從哪裡來，另一篇問 policy 如何用 rollout 和 reward 超越 imitation initialization。

需要保守的是，摘要宣稱 Z-1 在 24 個 RoboCasa tasks 達到 80.6% average success rate，並比 SFT initialization 提升 13.2 percentage points；但我沒有讀實驗章節，所以目前只把它視為作者宣稱，不把 benchmark 結論擴大解讀。

## 一句話理解

這篇想用針對 flow-based VLA 設計的 GRPO post-training，讓機器人 policy 從線上 rollouts 與任務 reward 中改善，而不是停留在固定 demonstration 的模仿學習。

## Summary / Abstract 說了什麼

摘要把 VLA 模型定位為連接語言指令、視覺觀測與連續控制的框架。問題在於，現有 policy 多半受限於 behavior cloning 或 SFT，這些方法依賴固定 demonstrations，缺少從 policy 自身失敗中改進的機制。

Z-1 是一個針對 flow-based VLA models 的 RL post-training framework。它以 $\pi_{0.5}$ 為基礎，先只用公開 RoboCasa demonstrations 做 SFT，再在 24 個標準 RoboCasa tasks 上使用 task-wise Group Relative Policy Optimization（GRPO）。GRPO 可粗略理解為：把同一任務或同一前綴下的多條 rollout 放在一組中比較，用相對表現更新 policy，而不是只看單條 trajectory 的絕對回饋。

為了提升線上最佳化的效率與穩定性，摘要列出幾個設計：shared-prefix rollout construction、tree-structured trajectory branching、completion-aware reward calibration，以及 selective joint training of VLM and Action Expert。作者宣稱，在 24 個 RoboCasa tasks 上，Z-1 平均成功率達 80.6%，比 SFT initialization 高 13.2 percentage points，並優於已發表的 sota models。

## Introduction 的問題設定

Introduction 先說明現代 robotic manipulation 需要同時連接高階語言指令、視覺觀測與低階連續控制，VLA models 因此成為自然框架。RT-2、OpenVLA、$\pi_0$ / $\pi_{0.5}$ 等系統展示了 foundation model priors 在 manipulation 上的潛力。

但作者接著指出，許多 VLA policies 仍主要依賴 human expert demonstrations 做 BC 或 SFT。這種初始化穩定，但受限於 demonstration coverage。長程與組合式任務中，小錯誤會累積，把 robot 推到 training data 未充分覆蓋的狀態；而 BC / SFT 本身沒有直接機制讓 policy 從自己的失敗中調整。

因此，Introduction 把 interaction-based post-training 設為下一步。RL post-training 可以透過 online rollouts 與 task-level rewards 改善 imitation policy，但在 VLA 場景有三個困難：flow-based VLA rollout 生成昂貴、稀疏 reward 與長 horizon 讓 optimization variance 變大、凍結 vision-language backbone 可能限制視覺 grounding / spatial reasoning / language-conditioned perception 的修正能力。

Z-1 的定位，就是為 flow-based VLA 設計一個 modular GRPO pipeline。它用 shared-prefix 與 tree-structured branching 減少重複 simulation，把比較集中在任務關鍵互動階段；用 success-aware reward decay 做 completion-aware reward calibration；必要時也更新 vision-language module，而不只調 action expert。

## 研究的第一性問題

- 基本問題：VLA policy 能不能在固定 demonstration 之外，透過自己的 rollouts 與任務回饋有效改進？
- 約束：robot rollout 成本高；任務 reward 常稀疏；長程 manipulation 容易累積錯誤；flow-based policy 的 action generation 與一般語言模型 token generation 不完全相同。
- 既有方法卡點：BC / SFT 穩定但受 demonstration coverage 限制；直接 RL 容易成本高、variance 大；只更新 action head 可能無法修正 perception-language grounding 錯誤。
- 作者試圖移動的邊界：把 LLM post-training 中的 group-relative optimization 思路，改造成適合 VLA rollout、branching 與 completion reward 的 robot policy post-training pipeline。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 提出 Z-1，一個用於 flow-based VLA models 的 modular GRPO post-training framework。
- 設計 shared-prefix GRPO 與 tree-structured prefix branching，減少 rollout 重複計算並保留可訓練 trajectory segments。
- 使用 success-aware reward decay 與 VLM / Action Expert joint training，處理 completion-aware reward calibration 與 perception-action co-adaptation。
- 在公開 RoboCasa demonstration setting 下，宣稱於 24 個 RoboCasa tasks 達到 80.6% average success rate，較 SFT initialization 提升 13.2 percentage points。

### 我的保守判讀

- 這篇的概念價值在於把「post-training」從純文字 LLM 推到 physical control：不只是對答案排序，而是在 rollout 成本、任務階段與物理狀態分布下做相對最佳化。
- shared-prefix / tree branching 的直覺很實用：robot trial 的前半段常有大量重複，若能共用前綴，就能把計算花在更關鍵的分岔點。
- Selective joint training of VLM and Action Expert 也反映一個重要判斷：robot failure 有時不是 action smoothing 問題，而是視覺 grounding 或語言條件理解問題。
- 限制是目前只根據摘要與 Introduction，無法判斷結果是否依賴 RoboCasa simulator、reward shaping、任務選擇或特定 base model。

## 可放進資料庫的筆記

- VLA 的 post-training 問題可看成「語言模型 RLHF / GRPO」與「robot rollout efficiency」的交會，而不是直接套用 LLM 方法。
- Demonstration coverage 是 BC / SFT 的天花板；長程任務中 distribution shift 會從小錯誤累積成不可恢復狀態。
- GRPO 在 robot setting 的關鍵不只是 group comparison，而是如何構造有意義且成本可控的 rollout groups。
- Shared-prefix rollout 是 Physical AI 的 test-time / train-time compute 節省策略：把重複前段共用，把分支放在決策敏感區。
- Sparse reward 需要 completion-aware calibration，否則長 horizon 中很多「接近完成」與「完全無效」可能都被同樣視為失敗。
- Action-only adaptation 未必足夠；若錯誤源自視覺 grounding 或語言條件理解，VLM backbone 也可能需要被選擇性更新。
- 「公開 demonstrations + online RL」若成立，會比依賴更多 private robot demos 更有可複製性，但仍要看 simulator-to-real 與 benchmark generality。

## 後續想追的問題

- Z-1 的 rollout 成本相對於一般 online RL 降低多少？shared-prefix 與 tree branching 各自貢獻多大？
- Reward calibration 如何定義 completion progress？是否需要任務專屬工程？
- Selective joint training VLM 時，如何避免破壞 pretrained visual-language priors？
- RoboCasa 結果能否轉移到真實機器人，或主要證明 simulator benchmark 上的 post-training 效率？
- GRPO 對 flow-based VLA 的設計，與對 autoregressive action-token VLA 的設計會有哪些差異？
