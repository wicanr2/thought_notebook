# 2026-07-14 論文拆解

今日選文 2 篇；本次只讀 arXiv Summary/Abstract 與 Introduction，未讀全文其他章節。兩篇都屬於近期 arXiv 新論文，且 arXiv ID 在 repo 中未發現重複。第一篇直接落在 VLM/VLA 與 robot action grounding；第二篇聚焦生成式影片先驗如何經過剛體幾何檢查後進入真實機器人操作。

## 今日選文

1. [CLAP: Direct VLM-to-VLA Adaptation via Language-Action Grounding](./01-clap-vlm-to-vla-language-action-grounding.md)
   - arXiv ID：2607.08974v1
   - 分類：Robotics (cs.RO)；Artificial Intelligence (cs.AI)
   - 選擇理由：它用最小架構變動討論 VLM-to-VLA adaptation，核心問題是 language distribution 與 numeric action token 的落差，對 compact VLA 與能力轉移分析有直接價值。
   - 閱讀範圍：Summary/Abstract + Introduction

2. [GenVid2Robot: From Video Generation to Robot Manipulation via Rigid-Geometric Consistency](./02-genvid2robot-rigid-geometric-consistency.md)
   - arXiv ID：2607.09191v1
   - 分類：Robotics (cs.RO)；Machine Learning (cs.LG)
   - 選擇理由：它把 generated video 視為不確定的 motion hypothesis，而不是 robot demonstration；用 sparse rigid `SE(3)` consistency 作為 Physical AI 進入實體執行前的幾何閘門。
   - 閱讀範圍：Summary/Abstract + Introduction

## commit 狀態

- 本日筆記建立於排程任務執行期間。
- 實際 commit SHA 以 git log 為準。
