# 2026-07-02 論文拆解

本日依「最多 2 篇、品質優先」規則新增 2 篇自動選文。兩篇都屬 Vision-Language-Action / Robotics / Embodied AI 交會主題：一篇聚焦 humanoid VLA 的人類影片轉 robot action supervision，另一篇聚焦 flow-based VLA 的 RL post-training。本次閱讀範圍皆為 Summary/Abstract + Introduction，未讀 methods / experiments / results 章節。

## 今日選文

1. [Human-as-Humanoid: Enabling Zero-Shot Humanoid Learning from Ego-Exo Human Videos with Human-Aligned Embodiments](./01-human-as-humanoid-ego-exo-vla.md)
   - arXiv：2606.32009v1
   - 選擇理由：處理高自由度 humanoid VLA 的資料瓶頸，把同步 ego-exo 人類影片轉成 controller-aligned robot action supervision；對「人類影片如何成為可執行 robot data」這個 Physical AI 基礎問題有參考價值。
   - 閱讀範圍：Summary/Abstract + Introduction。

2. [Z-1: Efficient Reinforcement Learning for Vision-Language-Action Models](./02-z-1-rl-for-vla.md)
   - arXiv：2606.31846v1
   - 選擇理由：把 GRPO / RL post-training 思路改造到 flow-based VLA policy，處理 VLA 從固定 demonstrations 走向 online rollout improvement 的問題；與第一篇的資料轉換問題互補。
   - 閱讀範圍：Summary/Abstract + Introduction。

## Commit 狀態

- 本次筆記已完成寫入；commit / push 狀態以本次執行結束後的 git 記錄為準。
