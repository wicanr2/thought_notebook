# 2026-07-24 論文拆解

今日新增 2 篇。兩篇都屬於 Physical AI / LLM + Robotics / Embodied AI 交會：一篇聚焦 VLA humanoid 從 lab benchmark 走向超市補貨 deployment 的系統整合問題；另一篇聚焦用 VLM seed point 連接語言意圖與多 embodiment grasp generation。閱讀邊界均為 arXiv Summary/Abstract + Introduction，未讀 methods / experiments / results。

## 今日選文

1. [Closing the Lab-to-Store Gap: A Data-Efficient Post-Training and Experience-Driven Learning VLA Framework for Retail Humanoids](./01-deed-vla-retail-humanoids.md)
   - arXiv：2607.20345v1
   - 選擇理由：它把 VLA humanoid 的真實部署問題拆成資料設計、控制頻率、視覺提示、經驗回流與 OOD 監控，比單純模型架構論文更接近 Physical AI 系統堆疊。
   - 閱讀範圍：Summary/Abstract + Introduction（arXiv HTML）。

2. [SeededGrasp: Language-Guided Grasping in Complex Scenes with Multiple Embodiments](./02-seededgrasp-language-guided-grasping.md)
   - arXiv：2607.20207v1
   - 選擇理由：它用 VLM 預測 seed point，再交給 lightweight grasp-generation model，提供一個把語言任務意圖與 3D grasp geometry 解耦的清楚介面。
   - 閱讀範圍：Summary/Abstract + Introduction（arXiv HTML）。

## 今日取捨

今日近期 arXiv cs.RO 也有 humanoid control、embodied tracking、navigation、world-model benchmark 等論文；但為了維持「最多 2 篇、品質優先、不硬湊」規則，本日只選兩篇最能累積 VLA deployment 與 language-conditioned manipulation 觀念的論文。已避開 repo 中既有 arXiv ID。

## Commit 狀態

- Commit 狀態：已納入本次 arXiv 論文拆解提交。
