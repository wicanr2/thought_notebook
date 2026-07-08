# 2026-07-08 論文拆解

今日依防重入規則檢查後，原本尚無非 README 的論文筆記；本次新增 2 篇。兩篇都屬於 LLM / VLM / VLA 與 Robotics / Embodied AI 的交會題目，且各自有獨立價值：一篇聚焦長任務 skill composition 的失敗診斷，一篇聚焦 VLA foundation model 從實驗室到實務部署的系統升級。

## 今日選文

1. [Diagnosing Semantic Handoff Failures in Agent-Orchestrated Vision-Language-Action Skill Composition](./01-semantic-handoff-vla-skill-composition.md)
   - arXiv：2607.06256v1
   - 選擇理由：把長任務機器人中的 VLA skill 交接問題具體化為 semantic handoff failure，能補上「單技能成功率」與「端到端任務失敗」之間的解釋缺口。
   - 閱讀邊界：Summary/Abstract + Introduction；未讀 methods / experiments / results 全文。

2. [From Foundation to Application: Improving VLA Models in Practice](./02-lingbot-vla-2-practical-vla.md)
   - arXiv：2607.06403v1
   - 選擇理由：LingBot-VLA 2.0 把 VLA 實用化拆成資料規模、跨 embodiment action space、predictive dynamics modeling 等系統問題，和 Physical AI / robot foundation model 主線高度相關。
   - 閱讀邊界：Summary/Abstract + Introduction；未讀 methods / experiments / results 全文。

## 今日 commit 狀態

- 狀態：由本次排程完成 commit / push；實際 commit SHA 以 `git log -1` 與遠端 `main` 為準。
