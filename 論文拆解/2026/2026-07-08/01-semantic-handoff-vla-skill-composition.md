# Diagnosing Semantic Handoff Failures in Agent-Orchestrated Vision-Language-Action Skill Composition

## 原文資訊
- 論文：Diagnosing Semantic Handoff Failures in Agent-Orchestrated Vision-Language-Action Skill Composition
- 作者：Ke Rui, Yushen Zuo, Jiawei Wang, Haoran Jia, Jinming Ma, Weitao Zhou, Minglei Li
- arXiv ID：2607.06256v1
- 分類：cs.RO
- 發表 / 更新：2026-07-07 / 2026-07-07
- 連結：[abs](https://arxiv.org/abs/2607.06256v1) / [pdf](https://arxiv.org/pdf/2607.06256v1)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-07-08

## 為什麼選這篇

這篇很貼近「LLM / VLM / VLA 如何真正進入機器人長任務」這條主線。它不是只問單一 VLA skill 能不能完成抓取、放置、開門，而是把問題放到 agent orchestrated skill composition：上層 agent 分解任務、調度 language-conditioned VLA skills，下層 policy 實際控制機器人。

值得收進資料庫的原因，是它把長任務失敗拆成一個更精準的概念：**semantic handoff failure**。一個 skill 可以看起來完成自己的 postcondition，但留下的狀態未必是下一個 skill 可以開始的狀態。這正好補上許多「單技能 benchmark 看起來不錯，但端到端任務仍然卡住」之間的落差。

我也喜歡它的保守定位。作者在 Introduction 裡明確說目標不是宣稱 BEHAVIOR-1K 的 SOTA，而是用 execution harness 把失敗變成可診斷的 trace。這種「先把失敗分類清楚」的工作，對 Physical AI 比單純追逐成功率更有資料庫價值。

## 一句話理解

長任務機器人不是只需要很多會做事的 VLA skills，還需要知道每個 skill 完成後，狀態是否真的交接給下一個 skill。

## Summary / Abstract 說了什麼

摘要指出，家庭長任務需要機器人組合許多 language-conditioned skills，但兩個 skill 之間的邊界通常不明確。前一個 skill 可能滿足自己的完成條件，卻讓機器人、物體或視角停在下一個 skill 難以啟動的狀態。

作者把這個問題稱為 semantic handoff problem，並在 BEHAVIOR-1K 中建立一個 agent-orchestrated VLA execution harness。這個 harness 會呼叫以 π0.5 為基礎的 skill checkpoints，為每個 skill 指定 typed arguments 與 step budget，並用多視角 VLM verifier 判斷是否要前進、重試或重新規劃。

摘要中最重要的訊號是：一些 navigation、grasping、placement、door-opening skills 在乾淨的 skill-boundary snapshots 下可以達到 77–100% 成功率，但在由前一個 skill 產生的 chained terminal states 中，組合任務仍會停滯。作者因此把失敗歸因到 next-skill readiness、target grounding、control execution 等類別。

## Introduction 的問題設定

Introduction 從一個家庭任務例子開始：做微波爆米花需要導航到廚房、開微波爐、放入袋子、關門、啟動設備。這類任務沒有單一 language-conditioned action 能處理，必須組合許多視覺 grounding 的動作，並且在每一步維持對語意狀態的掌握。

作者接著提出一個自然架構：讓 agent 調度 VLA skills。VLA policies 被視為 general-purpose、language-conditioned 的 visuomotor tools；agent layer 則負責 task decomposition、state tracking、verification、recovery 與 evidence collection。這個邊界比手寫 symbolic controller 更有泛化潛力，但問題是 learned skills 的介面不夠明確：它們可能完成局部行為，卻沒有產生下一步可用的狀態。

Introduction 的核心主張是，長任務執行不能只檢查當前 skill 是否完成，還要檢查結果狀態是否 ready for the next skill。例如 move_to(can) 之後不能只因為 can 在畫面裡就前進到 pick_up_from(can)；它還應該足夠靠近、位置適合夾爪接手。

作者因此把論文定位為 diagnostic framing：同一批 skill checkpoints 同時在乾淨 snapshot 與 composed chained states 下測，藉此分離 isolated skill competence 與 compositional robustness。Introduction 宣稱的三個貢獻是：形式化 semantic skill composition、建立 BEHAVIOR-1K execution harness、提供 trace-backed diagnostics。

## 研究的第一性問題

- **基本問題**：當機器人長任務由多個 language-conditioned VLA skills 組成時，什麼才算「前一步真的完成」？
- **約束**：VLA skill 不是 symbolic API；它的輸入輸出邊界模糊，沒有穩定的 stop signal，也不保證 terminal state 會落在下一個 skill 的訓練分布內。
- **既有方法卡點**：單技能 benchmark 通常從乾淨、策展過的 skill-boundary state 開始，容易高估長任務中的可組合性。
- **作者試圖移動的邊界**：把「端到端任務失敗」拆成可觀察的交接失敗、目標 grounding 失敗、控制執行失敗，讓資料收集與 verifier 設計有更明確的改進方向。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 提出 semantic handoff failure：skill 滿足自身 postcondition，但未留下下一個 skill 可用狀態。
- 建立一個 BEHAVIOR-1K semantic execution harness，整合 π0.5 VLA skill checkpoints、typed skill contracts、step budget、多視角 VLM verification、retry / replan trace。
- 顯示 snapshot-state skill competence 不等於 chained-state compositional robustness，並能把失敗 trace 歸因到 next-skill readiness、target grounding、control execution。

### 我的保守判讀

- 這篇的價值可能不在「提出更強 policy」，而在把 VLA 系統工程中的介面問題講清楚：skill library 需要的是可交接的狀態，而不只是局部成功率。
- 它也暗示 robotics agent 的 verifier 不應只問「目前這步完成了嗎」，而要問「完成後是否滿足下一步的前置條件」。這會把 verifier 從靜態判斷推向 next-skill-indexed readiness predicate。
- 限制上，我目前只讀到摘要與 Introduction，尚未檢查實驗規模、verifier 人工校驗方式、BEHAVIOR-1K 任務覆蓋與失敗分類是否穩健。因此不能把摘要中的診斷結果當成一般化結論。

## 可放進資料庫的筆記

- **Semantic handoff**：在 skill composition 中，完成目前 skill 不等於創造下一個 skill 的可啟動狀態。
- **Postcondition vs. readiness**：`ϕ_t` 描述目前 skill 的完成條件；更完整的邊界應加入 `ρ(s_t, s_{t+1})`，表示對下一個 skill 的 readiness。直觀上，就是「看起來做完」還不夠，要「下一步接得上」。
- **Snapshot competence trap**：從乾淨 snapshot 測到的單技能成功率，可能低估 chained terminal states 帶來的分布轉移。
- **Agent / VLA 分層**：agent layer 負責計畫、驗證、重試、重規劃；VLA layer 負責具身控制。失敗可能出在兩層介面，不一定是 policy 本身弱。
- **Verifier 的問題升級**：從「是否完成」升級到「是否完成且可交接」。這對 VLM verifier 的 prompt、視角與證據格式都有影響。
- **長任務資料收集方向**：如果訓練資料只保存乾淨 skill segments，模型可能缺少 messy chained-state distribution。
- **失敗 trace 比成功率更可用**：近零任務成功也能有研究價值，只要 trace 能把瓶頸分類成可修正問題。

## 後續想追的問題

- 作者的 VLM verifier 如何避免把「看起來接近」誤判成「真的可操作」？
- semantic handoff failure 在不同 skill 類型中比例如何？navigation 到 manipulation 的交接是否特別脆弱？
- 如果把 next-skill readiness predicate 明確建模，是否能成為 VLA training data 的標註欄位？
- π0.5 skill checkpoints 的訓練資料清理方式，是否已經把某些 messy transitions 排除掉，反而造成交接盲點？
- 這個 harness 能否接到真實機器人，而不是只在 BEHAVIOR-1K / OmniGibson 中做診斷？
