# Automating the Design of Embodied AgentArchitectures

## 原文資訊
- 論文：Automating the Design of Embodied AgentArchitectures
- 作者：Jian Zhou; Sihao Lin; Jin Li; Shuai Fu; Gengze Zhou; Qi Wu
- arXiv ID：2606.30111v1
- 分類：cs.RO, cs.AI, cs.LG
- 發表 / 更新：2026-06-29 / 2026-06-29
- 連結：[abs](https://arxiv.org/abs/2606.30111v1) / [pdf](https://arxiv.org/pdf/2606.30111v1)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-06-30

## 為什麼選這篇

這篇切中「LLM agents + embodied AI」的交會：不是只把 LLM 放進機器人系統，而是問 embodied agent 的整體架構能不能被自動搜尋。它把 perception、mapping、memory、planning、control、model calls 等模組看成可編輯的架構圖，這比單一 policy 或單一 prompt 更接近實體 AI 系統工程。

它也延續了近年 text-domain Agent Architecture Search（AAS）的問題意識，但把場景移到具感知與模擬 rollouts 的 embodied tasks。這很重要，因為文字代理的 workflow 搜尋通常便宜、狀態較少；embodied agent 則有 simulator noise、episode-level evaluation、長 execution traces、觀測與動作回饋等限制。

我選它作為第二篇，是因為它和第一篇 SA-VLA 的層級互補：SA-VLA 關心 action tokenizer 的低層介面，這篇則關心 embodied agent 的高層模組拓撲。兩者都不是單純「模型變大」敘事，而是在問 Physical AI 系統要如何被組織。

## 一句話理解

這篇想知道：能否把 embodied agent 的感知、記憶、規劃、行動模組做成可搜尋的圖結構，讓 coding agent 自動改架構並用模擬 rollout 評估。

## Summary / Abstract 說了什麼

摘要指出，embodied agents 通常是由 perception、memory、planning、action modules 手工組成。這種模組化暴露了很大的設計空間，但目前仍依賴研究者直覺決定資訊存在哪裡、觀測如何處理、模型呼叫如何連接。

作者把 text-domain Agent Architecture Search 的想法轉到 embodied agents，並提出兩個東西：AgentCanvas 與 KDLoop。AgentCanvas 是 typed-graph runtime，可把 embodied executors 裝成可編輯的 node-and-wire programs，並支援 simulator-aware execution 與 episode-level logs。KDLoop 則是 coding-agent search procedure，在 proposal、critique、experiment、distillation 之間循環，並在停滯時觸發 reflection。

摘要宣稱，作者在四種 embodied executors 上評估三種 AAS variants，涵蓋 vision-language navigation、embodied question answering、language-conditioned manipulation。結果顯示 architecture-level search 可以產生可部署且方向正確的 success-rate gains，但也暴露 text-domain AAS 中比較不明顯的限制：rollout noise 會遮蔽 optimization signals，search 會陷入 local edit basins，即使有詳細 logs，episode-level credit assignment 也只會部分浮現。這些我目前只視為摘要與 Introduction 的宣稱，尚未讀實驗細節。

## Introduction 的問題設定

Introduction 從 embodied agents 的系統組成開始：現代 embodied agent 越來越常把 foundation models 與 perception、mapping、memory、planning、control modules 組合起來。這種 agentic paradigm 的優點是架構外顯；不同於 end-to-end policy 把結構吸收到 weights 裡，模組式系統讓資訊流與可編輯節點變得可見。

但外顯結構也帶來設計問題。每個 agent 都要固定 sensor abstractions、map representations、memory state、prompt structure、planner topology、model placement、action interfaces。這些選擇通常為單一 benchmark 手工設計；當 foundation models 與 embodied tools 增加，可能架構空間會比人工迭代更快膨脹。

作者接著引入 AAS：在文字代理裡，AAS 用 optimizer 自動提出與評估不同 LLM-agent workflows。不過 Introduction 強調，embodied setting 不是直接移植。文字 AAS 常面對便宜、stateless 的 calls，以及較成熟的 primitive vocabulary；embodied agents 則需要與 stateful simulators 互動，評估有噪音，execution traces 更長，並包含 observations、actions、tool calls 與 intermediate state。

因此，論文的問題不是「能不能找到一個更好的 prompt」，而是「能不能建立一個 substrate，讓 embodied architectures 可以被安全編輯、rollout、記錄與評估」。這也是 AgentCanvas 與 KDLoop 的定位。

## 研究的第一性問題

- 基本問題：當 embodied agent 是由多個可替換模組組成時，架構本身能否成為搜尋與優化對象？
- 約束：搜尋不能只產生文字流程圖，必須能在 simulator 中執行、留下 episode-level evidence，並避免 leakage 或不可部署的候選方案。
- 既有方法卡點：text-domain AAS 的 cheap/stateless assumptions 不適合 embodied rollouts；人工設計 embodied architectures 又難以覆蓋快速擴張的模組組合空間。
- 作者試圖移動的邊界：把 embodied executor 表示為 typed graph，讓 coding agent 可以在可驗證的 node-and-wire 程式上進行架構搜尋。

可用一個簡化模型理解：文字代理搜尋常像是在離散 workflow 集合 `W` 上找 `argmax score(w)`；embodied AAS 則更接近在可執行圖 `G` 上搜尋 `argmax E_rollout[R(G)]`。這裡的困難是 `R(G)` 來自帶噪音的 simulator episodes，不是一次便宜 API call 的穩定分數。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 提出 AgentCanvas：一個 typed-graph runtime，把 embodied executors 表示成可編輯 node-and-wire programs。
- 提出 KDLoop：coding-agent search procedure，在 proposal、critique、experiment、distillation 與 reflection 間循環。
- 將 AAS 系統性移植到 perceptual embodied agents，並在 navigation、EQA、language-conditioned manipulation 等任務上評估。
- 指出 embodied AAS 的限制：rollout noise、local edit basins、episode-level credit assignment 不完整，以及 leakage-bearing candidate 的排除。

### 我的保守判讀

- 這篇的主要價值可能在研究 framing 與 infrastructure：讓 embodied agent architecture 從「研究者手工搭積木」變成「可搜尋、可記錄、可審計的圖」。
- 目前只讀摘要與 Introduction，不能確認三種 AAS variants 的比較是否公平，也不能確認 success-rate gains 的幅度、統計穩定性與成本。
- 「自動設計架構」聽起來很大，但實際可能受限於預先定義的 typed nodes、可編輯範圍、simulator fidelity 與 evaluation budget。
- leakage-bearing candidate 被拒絕這點很值得注意：embodied AAS 若能修改 execution graph，可能意外使用不該使用的資訊。這使 governance / validation 成為方法的一部分，而不只是實驗附註。

## 可放進資料庫的筆記

- Embodied agent 的架構外顯性是優點，也是設計空間爆炸的來源。
- Agent Architecture Search 從文字代理轉到 embodied agent 時，最大差異是 stateful simulator、rollout noise 與長 trace。
- 可搜尋的 embodied architecture 需要 substrate：typed graph、可執行節點、pre-rollout validation、episode logs。
- Physical AI 的「agent」不只是 LLM planner，而是 perception-memory-planning-control 的資訊流設計。
- 架構搜尋的分數若來自 simulator rollout，`score` 本身會有噪音與外洩風險，不能只看最高分。
- Local edit basin 是 embodied AAS 的實務問題：coding agent 可能只在容易改的局部架構附近打轉。
- 自動化 architecture design 需要 governance：哪些訊號可用、哪些候選方案算 leak-bearing、如何審計改動。

## 後續想追的問題

- AgentCanvas 的 typed graph primitives 有多通用？是否只適用於作者選的 executors？
- KDLoop 的 reflection 與 distillation 具體如何從 failed rollouts 中提取可用 credit？
- 三種 AAS variants 的成本、迭代次數與成功率提升幅度如何？
- leakage-bearing candidate 是什麼類型的外洩？系統如何偵測與排除？
- 若換到真實機器人而非 simulator，evaluation noise、成本與安全限制會不會讓 AAS 幾乎不可行？
