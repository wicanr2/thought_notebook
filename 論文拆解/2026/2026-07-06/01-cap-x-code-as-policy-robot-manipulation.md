# CaP-X: A Framework for Benchmarking and Improving Coding Agents for Robot Manipulation

## 原文資訊
- 論文：CaP-X: A Framework for Benchmarking and Improving Coding Agents for Robot Manipulation
- 作者：Letian Fu, Justin Yu, Karim El-Refai, Ethan Kou, Haoru Xue, Huang Huang, Wenli Xiao, Guanzhi Wang, Dantong Niu, Fei-Fei Li, Guanya Shi, Jiajun Wu, Shankar Sastry, Yuke Zhu, Ken Goldberg, Linxi “Jim” Fan
- arXiv ID：2603.22435v2
- 分類：cs.RO, cs.AI
- 發表 / 更新：2026-03-23 / 2026-07-02
- 連結：[abs](https://arxiv.org/abs/2603.22435v2) / [pdf](https://arxiv.org/pdf/2603.22435v2)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-07-06

## 為什麼選這篇

這篇切在 LLM/VLM 與機器人控制的交會點：不是把語言模型當純文字規劃器，也不是把 VLA 當端到端黑盒，而是研究「coding agent 產生可執行 robot-control program」這條路線到底有多少實際控制能力。

我選它的主因是問題設定相當關鍵：過去 Code-as-Policy 常依賴人類設計的高階 primitive，例如一個函式就包好一大段操作。這會讓 benchmark 看起來像在測 LLM，其實可能是在測人類工程師提供的 scaffold。CaP-X 試圖把 primitive 抽象層級、互動輪次、視覺 grounding 分開測，這對評估「LLM agent 能否真的接近機器人控制層」很有價值。

另一個值得收入資料庫的原因是它把 evaluation、agent harness、以及後續 RL 介面放在同一框架裡。即使不讀實驗細節，從摘要與 introduction 已可看出它關心的是 Physical AI 的系統邊界：哪些能力來自模型，哪些來自手工抽象，哪些來自 test-time compute 與互動式修錯。

## 一句話理解

CaP-X 想把「LLM/VLM 寫程式控制機器人」從 demo 推向可系統評估的研究問題：少掉人類 scaffold 後，coding agent 還剩多少操控能力？

## Summary / Abstract 說了什麼

摘要把 CaP-X 定義成一個 open-access framework，核心包含 CaP-Gym、CaP-Bench、CaP-Agent0 與 CaP-RL。CaP-Gym 提供互動式環境，讓 agent 透過合成與執行程式組合 perception/control primitives 來控制機器人；CaP-Bench 則用不同抽象層級、互動形式與感知 grounding 評估語言/視覺語言模型。

論文自稱的主要觀察是：12 個模型在有人工設計抽象時表現較好，但當這些先驗被拿掉時會明顯退化，顯示模型仍依賴 designer scaffolding。同時，摘要也指出 multi-turn interaction、structured execution feedback、visual differencing、automatic skill synthesis、ensembled reasoning 等 test-time 機制能提高 robustness。

摘要最後把 CaP-Agent0 描述成 training-free framework，並說它在若干模擬與真實 embodiment 的 manipulation tasks 上達到接近人類可靠度；CaP-RL 則探索用 verifiable rewards 對 coding agent 做 reinforcement learning，且透過共同的 code-as-action-space 支援 sim-to-real transfer。這些結果我只記為「摘要中的主張」，尚未檢查實驗設計與數據。

## Introduction 的問題設定

Introduction 先對比兩條機器人控制路線。第一條是傳統顯式程式、幾何規劃、TAMP 與控制回饋，優點是結構清楚、可解釋、可處理幾何與 edge cases，缺點是高度依賴人類工程師，且常變成任務特化。第二條是 VLA，能從大規模 visuomotor data 學到接觸豐富的操作能力，但也受限於資料分布、可解釋性與跨 embodiment / 長程任務泛化。

作者接著提出一個橋接思路：現代 coding agents 已能在軟體工程 benchmark 中合成、定義函式、debug，因此也許可以讓 coding agent 取代一部分人類工程師，產生 robot-control code。這與早期只能呼叫固定函式的 language-conditioned planner 不同，因為新的 agent 有能力生成中低階邏輯。

核心缺口在於：既有 Code-as-Policy demo 常給了很強的高階 primitive，所以不清楚成功來自模型本身，還是來自人類設計的介面。Introduction 因此把研究問題拆成三個評估軸：primitive 的抽象層級、single-turn vs multi-turn 的互動能力、以及不同視覺 grounding 對程式生成的影響。

作者宣稱的貢獻包括：CaP-Gym 作為互動式 robot coding environment；CaP-Bench 作為跨 task / primitive / modality 的 benchmark；CaP-Agent0 作為 multi-turn、visual differencing、ensemble、skill synthesis 的 training-free harness；CaP-RL 作為用環境 reward 訓練 coding agent 的路徑。

## 研究的第一性問題

- **基本問題**：如果 robot action space 用「可執行程式」表示，LLM/VLM agent 能否在少量 scaffold 下完成 manipulation？
- **約束**：機器人控制需要幾何精度、感知 grounding、失敗恢復、長程分解與安全邊界；單純文字推理不能直接保證閉環控制可靠。
- **既有方法卡點**：傳統 robotics 可控但工程成本高；VLA 可學但資料與泛化受限；Code-as-Policy demo 又可能被高階 primitive 過度美化。
- **作者試圖移動的邊界**：把 benchmark 設計成能拆出「模型能力」與「人類 scaffold」的貢獻，並探索 test-time compute 是否能補足低階控制介面的困難。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 提供 CaP-Gym / CaP-Bench，系統測量 coding agents 在 robot manipulation 中的表現。
- 顯示模型在高階人工抽象下較強，但移除 scaffold 後退化。
- 指出 multi-turn interaction、execution feedback、visual differencing、skill synthesis、ensemble 等機制可提升穩健性。
- 提出 CaP-Agent0 與 CaP-RL，分別代表 training-free agent harness 與後續 RL post-training 路徑。

### 我的保守判讀

- 這篇的價值不只在結果，而在把「agent 寫程式控制機器人」拆成可診斷的系統問題；這比單一 demo 更接近可累積的 Physical AI 研究基礎。
- 需要等讀全文後確認 benchmark 任務難度、primitive 設計、公平性、真實機器人覆蓋範圍，以及所謂 near human-level reliability 是否集中在特定條件。
- 若 CaP-X 的低階 primitive 仍與真實部署有距離，則它比較像 agentic robotics 的中間層 benchmark，而不是完整控制堆疊的答案。

## 可放進資料庫的筆記

- 評估 LLM-for-robotics 時，要分清楚「模型能力」與「介面 scaffold」；高階 primitive 可能把問題偷偷解完。
- Code-as-Policy 的關鍵不是自然語言規劃，而是把程式變成 action space，讓 agent 可組合、debug、重用控制邏輯。
- Physical AI benchmark 應拆開 abstraction level、interaction budget、perceptual grounding，否則很難知道瓶頸在哪。
- Test-time compute 在機器人情境中可表現為多輪互動、執行回饋、視覺差異檢查、技能庫合成與 ensemble。
- VLA 與 CaP 不是互斥路線：前者偏資料驅動動作生成，後者偏結構化程式控制；未來可能在不同控制層分工。
- 「能寫出可執行控制程式」是 LLM agent 走向 embodied system 的重要中介能力，但仍需感知、幾何與安全機制支撐。
- 任何宣稱 agentic robot control 的系統，都應問：失敗時它能否觀察、定位錯誤、修改程式並重試？

## 後續想追的問題

- CaP-Bench 的低階 primitive 到底有多低階？是否仍內含大量 robotics know-how？
- CaP-Agent0 的提升主要來自哪個元件：多輪、visual differencing、skill library，還是 ensemble？
- 真實機器人實驗的任務多樣性與安全邊界如何？
- CaP-RL 對 coding agent 的訓練成本、reward hacking 風險與 sim-to-real 穩定性如何？
- 這條路線與端到端 VLA 在長程任務中應如何分層整合？
