# Jetson-PI: Towards Onboard Real-Time Robot Control via Foresight-Aligned Asynchronous Inference

## 原文資訊
- 論文：Jetson-PI: Towards Onboard Real-Time Robot Control via Foresight-Aligned Asynchronous Inference
- 作者：Zebin Yang, Qi Wang, Yunhe Wang, Xiurui Guo, Bo Yu, Shaoshan Liu, Jiafeng Xu, Hao Dong, Meng Li
- arXiv ID：2607.12659v1
- 分類：cs.RO, cs.AI
- 發表 / 更新：2026-07-14 / 2026-07-14
- 連結：[abs](https://arxiv.org/abs/2607.12659v1) / [pdf](https://arxiv.org/pdf/2607.12659v1)
- 本次閱讀範圍：Summary/Abstract + Introduction（未讀方法、實驗與其他章節；PDF 連結於擷取時回傳 404，因此 Introduction 取自 arXiv HTML）
- 擷取日期：2026-07-15

## 為什麼選這篇

這篇切中 VLA 從 demo 走向部署時很現實的問題：模型能不能跑在機器人身上的低功耗邊緣裝置，而不是每次都靠高階 GPU 或遠端伺服器。若 VLA 的控制頻率太低，語言與視覺理解再強，也會在真實環境中變成反應慢、停頓長、對變化不敏感的控制系統。

它和 LLM + Robotics 的交會點不在「讓 LLM 更會推理」，而在「大型多模態模型如何被嵌入實體控制迴路」。這是 Physical AI 很容易被 hype 掩蓋的工程邊界：真實機器人不只要會理解任務，也要在電池、散熱、頻寬、延遲與控制週期內工作。

我選它作為第二篇，是因為它與今天第一篇 VistaVLA 形成互補：VistaVLA 關心 spatial representation，Jetson-PI 關心 onboard real-time inference。兩者都在問同一個底層問題：VLA 要變成可部署的機器人能力，模型表示與系統時序都必須被重新設計。

## 一句話理解

Jetson-PI 想解決的是：如何讓 VLA 在 Jetson Orin 這類低功耗 onboard device 上，以更接近即時控制的方式運行。

## Summary / Abstract 說了什麼

摘要指出，VLA 在 embodied tasks 上表現不錯，但高計算複雜度讓它難以部署在 Jetson Orin 等低功耗 onboard devices 上，造成 inference latency 高、control frequency 低。Asynchronous inference 可以一邊執行目前 action chunk、一邊推理下一個 chunk，但會帶來兩個問題：perception-execution misalignment，以及 reaction time 仍然很長。

作者提出 Jetson-PI，核心是 Foresight-Aligned Asynchronous Correction。摘要中的方法主張包括：訓練 lightweight future correction module，根據已 committed actions 預測 future environment representation，讓 action expert 從未來時間點直接預測動作；另用 confidence-based scheduling optimization 降低反應時間、提高控制頻率。

摘要也宣稱 Jetson-PI 在 Jetson Orin 上相對 naive PyTorch 與 vla.cpp 提升控制頻率，並在 LIBERO benchmark 上優於 VLASH。這些數字本次只來自摘要與 Introduction，未讀實驗設定，因此只當作作者自稱。

## Introduction 的問題設定

Introduction 先指出，VLA 借助 pretrained VLM 的知識與 robot demonstration datasets，可從 raw observations 與自然語言直接預測 action chunks。但高參數量與計算複雜度讓它在真實機器人上遇到低延遲部署問題；高階 GPU 雖能降低延遲，卻帶來功耗與電池續航問題，遠端伺服器也會帶來網路延遲與活動範圍限制。

作者接著把問題具體化到 Jetson Orin：在低功耗 onboard device 上，state-of-the-art VLA 推理延遲可到約 1.4 秒，控制頻率約 0.7 Hz。這會讓機器人在 action chunks 之間停頓，且對環境變化反應慢。

Introduction 對 asynchronous inference 的分析是本文重點。它可以隱藏部分延遲，但當下一段 action chunk 被推理完成時，外部環境已經相對原始 image input 改變，造成 prediction-execution misalignment；同時 robot reaction speed 仍受 VLA inference time 限制。Jetson-PI 因此提出 future correction module 與 scheduling / system-level acceleration，試圖同時修正時序對齊與提高控制頻率。

## 研究的第一性問題

- **基本問題**：大型 VLA 如何在機器人本體的低功耗硬體上，維持足夠快的控制迴路？
- **約束**：機器人受限於電池、散熱、onboard compute、記憶體頻寬與環境變化速度；不能假設永遠有 RTX 4090 或低延遲雲端。
- **既有方法卡點**：同步推理太慢；單純非同步推理會把舊觀察下的 prediction 拿到已變化的未來環境中執行。
- **作者試圖移動的邊界**：讓非同步推理不只是 hiding latency，而是透過 future representation correction 把 action prediction 對齊到執行時刻。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 提出 Jetson-PI，用 foresight-aligned asynchronous inference 支援 onboard VLA deployment。
- 用 future correction module 預測未來 VLM representations，降低 perception-execution misalignment。
- 透過 scheduling optimization 與 llama.cpp-based edge inference framework 提高控制頻率。
- 宣稱在 Jetson Orin、模擬與真實機器人測試中改善控制頻率與成功率。

### 我的保守判讀

- 這篇的價值在於把 VLA 部署問題從「模型壓縮」推到「時間對齊」：控制系統裡的延遲不是單一數字，而會改變觀察、推理與執行的關係。
- future correction module 的可靠性會很關鍵；若環境變化不是由已 committed actions 主導，而是有外部人、物體滑動或非預期接觸，預測未來 representation 可能失準。
- 系統工程貢獻需要讀後文才知道有多少可泛化到不同 VLA 架構、不同機器人與不同 edge hardware。

## 可放進資料庫的筆記

- VLA 的部署瓶頸不只是算力，而是「推理完成時，世界已經變了」。
- Asynchronous inference 若沒有 temporal alignment，可能只是把延遲藏起來，而不是消除控制錯位。
- Onboard deployment 是 Physical AI 的現實邊界：電池、功耗、頻寬與控制頻率會反過來塑造模型架構。
- Action chunking 與 real-time reaction 之間存在張力：chunk 太長可能平滑，但也降低外界變化的反應速度。
- Future representation correction 可視為一種控制時序補償，而不只是模型推理加速技巧。
- Edge VLA 的評估應同時看 success rate、control frequency、latency、power / battery impact，而不是只看 benchmark 成績。
- 真實機器人部署需要把 ML model、scheduler、runtime 與硬體限制視為同一個系統。

## 後續想追的問題

- future correction module 是如何訓練的？它對未見動態、碰撞、滑動或人為干擾有多穩？
- confidence-based scheduling 如何判斷何時重跑 VLM、何時多次呼叫 action expert？
- Jetson-PI 的效益是否依賴特定 VLA 架構，還是可以泛化到其他 action expert / diffusion policy？
- latency 改善是否伴隨能耗、記憶體使用或準確率上的隱性成本？
- 在比 LIBERO 更動態、更長時域的任務中，非同步時序補償是否仍然有效？
