# 2026-07-12 論文拆解

今日新增 2 篇。兩篇都屬於 Physical AI / VLA for robotics，且各自代表不同問題意識：一篇偏系統可靠性與 agent harness，一篇偏輕量化 VLA 架構與部署成本。閱讀範圍皆限於 arXiv Summary/Abstract + Introduction，未讀全文其他章節。

## 今日選文

1. [Harness VLA: Steering Frozen VLAs into Reliable Manipulation Primitives via Memory-Guided Agents](./01-harness-vla-memory-guided-agents.md)
   - arXiv：2607.08448v1
   - 分類：cs.RO
   - 選擇理由：把 frozen VLA、LLM/coding-agent 式語意組合、固定 analytic primitives 與 memory-guided retry 放在同一個可靠操作框架中，對「VLA 如何從 demo 走向部署」很有參考價值。
   - 閱讀邊界：Summary/Abstract + Introduction（arXiv HTML）。

2. [FabriVLA: A Lightweight Vision-Language-Action Model for Precise Multi-Task Manipulation](./02-fabrivla-lightweight-vla.md)
   - arXiv：2607.08575v1
   - 分類：cs.RO
   - 選擇理由：聚焦輕量 VLA，在大型模型成本與機器人即時控制之間尋找平衡；可補上資料庫中「cost-aware / deployable VLA」的觀察線。
   - 閱讀邊界：Summary/Abstract + Introduction（arXiv HTML）。

## Commit 狀態

- 本批筆記完成後會以繁中 commit message 提交並 push 到 `origin/main`。
