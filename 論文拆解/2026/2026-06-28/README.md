# 2026-06-28 論文拆解

今日選文 2 篇；本次只讀 arXiv Summary/Abstract 與 Introduction，未讀全文其他章節。兩篇都屬於 VLA / Robotics 交會方向，品質足夠且 arXiv ID 未在 repo 中重複。

## 今日選文

1. [LA4VLA: Learning to Act without Seeing via Language-Action Pretraining](./01-la4vla-language-action-pretraining.md)
   - arXiv ID：2606.27295v1
   - 主題：Vision-Language-Action、language-action pretraining、robot manipulation、embodied AI
   - 發表 / 更新：2026-06-25 / 2026-06-25
   - 選文理由：這篇把 VLA 中語言監督被視覺訊號稀釋的問題說清楚，並用既有 demonstrations 重組出 language-action supervision；對 VLA 資料設計與 Physical AI 訓練流程都有可重用價值。
   - 閱讀範圍：Summary/Abstract + Introduction

2. [RouterVLA: Turning Smoke Tests into Supervision for Heterogeneous VLA Selection](./02-routervla-smoke-tests-supervision.md)
   - arXiv ID：2606.27355v1
   - 主題：Vision-Language-Action、robot policy routing、commissioning、evaluation protocol
   - 發表 / 更新：2026-06-25 / 2026-06-25
   - 選文理由：這篇把部署前 smoke tests 視為 routing supervision，並強調 outcome-disjoint evaluation；對多 VLA policy 系統、robot specialist lifecycle 與 embodied agent 評估方法有啟發。
   - 閱讀範圍：Summary/Abstract + Introduction

## Commit 狀態

- 建立日期：2026-06-28
- 狀態：已納入本次 cron 提交
