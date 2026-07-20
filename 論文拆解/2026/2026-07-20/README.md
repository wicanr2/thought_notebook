# 2026-07-20 論文拆解

本日自動選文共 2 篇。兩篇都屬於 Physical AI / VLA 系統堆疊：一篇看部署後持續學習與技能保留，一篇看 real-time VLA streaming inference。均只讀 arXiv Summary/Abstract + Introduction，未讀全文其他章節。

## 今日選文

1. [Towards Human-like Physical Intelligence: LifelongVision-Language-Action Learning for Robotic Manipulation](./01-lifelongvla-lifelong-vla-learning.md)
   - arXiv：2607.14852v1
   - 選擇理由：把 continual learning 的 plasticity-stability trade-off 放進 VLA manipulation 與部署後技能累積問題，並處理 replay 成本。
   - 閱讀範圍：Summary/Abstract + Introduction。

2. [Reflex: Real-Time VLA Control through Streaming Inference](./02-reflex-streaming-vla-control.md)
   - arXiv：2607.14695v1
   - 選擇理由：聚焦 VLA 在真實控制迴路中的 latency、KV cache 正確性、非同步 pipeline 與數值穩定，是 Physical AI 落地的系統層問題。
   - 閱讀範圍：Summary/Abstract + Introduction。

## Commit 狀態

- 本日筆記與索引更新已納入本次 git commit / push 流程；最終 commit SHA 以 repository git log 為準。
