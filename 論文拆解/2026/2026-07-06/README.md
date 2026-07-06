# 2026-07-06 論文拆解

今日自動選文以 LLM + Robotics / Embodied AI 交會與 VLA 資料泛化問題為主。兩篇都只根據 arXiv Summary/Abstract 與 Introduction 撰寫，未讀 methods / experiments / results 全文細節。

## 今日選文

1. [CaP-X: A Framework for Benchmarking and Improving Coding Agents for Robot Manipulation](./01-cap-x-code-as-policy-robot-manipulation.md)
   - arXiv ID：2603.22435v2
   - 選擇理由：它把 Code-as-Policy / coding agent for robot manipulation 從 demo 推向可診斷 benchmark，特別適合追蹤 LLM agent 與 Physical AI 控制介面的交會。
   - 閱讀範圍：Summary/Abstract + Introduction。

2. [The Moving Eye: Enhancing VLA Spatial Generalization via Hybrid Dynamic Data Collection](./02-moving-eye-vla-spatial-generalization.md)
   - arXiv ID：2607.02322v1
   - 選擇理由：它從資料收集與 shortcut learning 角度分析 VLA spatial generalization，能補上近期 VLA 筆記中「模型能力其實受資料分布約束」的線索。
   - 閱讀範圍：Summary/Abstract + Introduction。

## 今日判斷

- 今天新增 2 篇，未超過每日上限。
- 第二篇不是為了湊數；它與第一篇的問題意識不同：CaP-X 偏 agentic code-as-control 的評估框架，The Moving Eye 偏 VLA 資料分布與空間泛化。
- 兩篇 Introduction 均成功取得；筆記中未聲稱讀過全文其他章節。

## Commit 狀態

- 待本次 cron 完成後由 git commit / push 記錄。
