# 2026-07-15 論文拆解

今日排程選文 2 篇；皆屬 LLM / VLM + Robotics / Embodied AI 交會主題。排程選文只讀 arXiv Summary/Abstract 與 Introduction，未讀全文其他章節。兩篇的 PDF 連結在擷取時回傳 404，因此 Introduction 取自 arXiv HTML；結論強度已依此保守處理。

## 今日選文

1. [VistaVLA: Geometry- and Semantic-Aware 3D Gaussian-Grounded VLA for Robotic Manipulation](./01-vistavla-3d-gaussian-grounded-vla.md)
   - arXiv ID：2607.12356v1
   - 分類：Robotics (cs.RO)
   - 選擇理由：它把 VLA 的 2D observation bottleneck 轉成「語意如何 grounding 到 3D 空間並壓縮成 policy 可用 tokens」的問題，對 robot foundation model 的中間表示很有參考價值。
   - 閱讀範圍：Summary/Abstract + Introduction（Introduction 取自 arXiv HTML）

2. [Jetson-PI: Towards Onboard Real-Time Robot Control via Foresight-Aligned Asynchronous Inference](./02-jetson-pi-onboard-real-time-vla.md)
   - arXiv ID：2607.12659v1
   - 分類：Robotics (cs.RO)；Artificial Intelligence (cs.AI)
   - 選擇理由：它把 VLA 部署問題放回 onboard real-time control：低功耗硬體、推理延遲、非同步控制與 perception-execution misalignment，補上 Physical AI 從 demo 到部署的系統邊界。
   - 閱讀範圍：Summary/Abstract + Introduction（Introduction 取自 arXiv HTML）

## commit 狀態

- 本日筆記建立於排程任務執行期間。
- 實際 commit SHA 以 git log 為準。
