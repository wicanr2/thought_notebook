# 2026-06-30 論文拆解

今日新增 2 篇。兩篇都屬於 LLM / embodied AI / robotics 的交會，但層級不同：第一篇看 VLA action tokenizer 的底層控制介面，第二篇看 embodied agent architecture 的高層模組搜尋。皆只根據 arXiv Summary/Abstract 與 Introduction 撰寫，未讀 methods/experiments/results。

## 今日選文

1. [SA-VLA: State-aware tokenizer for improving Vision-Language-Action Models' performance](./01-sa-vla-state-aware-tokenizer.md)
   - arXiv：2606.30113v1
   - 選擇理由：直接處理 VLA policy 中 discrete action token 到 continuous control 的瓶頸，並把 robot state / proprioception 放進 action decoding，對理解 robot foundation model 的 action interface 很有參考價值。
   - 閱讀範圍：Summary/Abstract + Introduction。

2. [Automating the Design of Embodied AgentArchitectures](./02-automating-embodied-agent-architectures.md)
   - arXiv：2606.30111v1
   - 選擇理由：把 Agent Architecture Search 從 text-domain agents 推向 embodied agents，關注 perception、memory、planning、action modules 的可搜尋圖結構，適合追蹤 LLM agents 與 Physical AI 系統工程交會。
   - 閱讀範圍：Summary/Abstract + Introduction。

## Commit 狀態

- 本索引會與單篇筆記、年度索引、根目錄 catalog 一併 commit/push。
