# RouterVLA: Turning Smoke Tests into Supervision for Heterogeneous VLA Selection

## 原文資訊
- 論文：RouterVLA: Turning Smoke Tests into Supervision for Heterogeneous VLA Selection
- 作者：Xingyu Ren, Chugang Yi, Ge Ma, Youran Sun
- arXiv ID：2606.27355v1
- 分類：Robotics (cs.RO)
- 發表 / 更新：2026-06-25 / 2026-06-25
- 連結：[abs](https://arxiv.org/abs/2606.27355) / [pdf](https://arxiv.org/pdf/2606.27355)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-06-28

## 為什麼選這篇

這篇把 VLA 進步的焦點從「訓練更強單一模型」移到「如何在部署前後使用多個 heterogeneous policies」。這對 robotics 很實際：一個團隊往往不只有一個模型，而是有 foundation checkpoint、fine-tuned variants、task specialists；不同 policy 在不同擾動、相機角度、物件位置或初始狀態下的強弱可能不一樣。

它值得收進資料庫的原因，是它把 robot policy selection 當成一個有 evidence boundary 的問題。作者問：部署前的 smoke tests 是否只是成本，還是可以變成 supervision，用來選擇哪個 VLA expert 應該負責某個 target condition？

這篇與 LLM routing / mixture-of-agents 有概念相似性，但落點在 embodied policy：不是把多個文字模型投票或融合，而是在機器人實體任務中，利用少量 commissioning rollouts 來做 expert routing。這個角度對「LLM + Robotics」交會領域很有啟發，尤其是當未來 robot systems 可能由多個 specialist policies 組成，而不是單一通才模型一次解決所有情境。

## 一句話理解

這篇想解決的是：當我們有多個 VLA policies 時，能否把部署前的 smoke test rollouts 轉成可靠的選擇訊號，而不是只用全域平均分數挑一個模型。

## Summary / Abstract 說了什麼

摘要說，機器人團隊通常會在部署前 smoke test 多個 candidate VLA policies，但常見做法是把這些試跑壓縮成一個 global winner。RouterVLA 則研究是否能重用這些 pre-deployment evaluation rollouts 來監督 policy selection。

作者強調 outcome-disjoint cross-fitting：用一部分 recorded probes 建立每個 frozen expert 的 profile，再用另一個不進入 profile 的 trial 評分選出的 expert。根據摘要，在 34,752 筆 LIBERO-Plus rollout records 上，一個透明的 probe-success rule 把 held-out success 從 0.4686 提升到 0.6149，增加 14.64 個百分點。

摘要也說，在本文研究的 scalar-only profiles 下，learned scorers 與簡單規則在統計上不可區分；這表示 routing value 主要來自 commissioning evidence，而不是更複雜的 scorer capacity。作者也指出，如果重用被評分的 trial，測得的 gain 會被放大 1.87 倍，因此 credible ledger routing 需要 outcome separation。這些結果我本次只讀到摘要與 Introduction，沒有檢查完整實驗細節。

## Introduction 的問題設定

Introduction 從一個很實務的流程開始：robotics team 會在目標 setup 上測試 VLA policy，可能對每個 candidate checkpoint 做少量 smoke tests。一般做法是把 commissioning phase 當成部署前成本，最後選平均表現最好的 policy。作者問的是：這些 commissioning data 能不能變成 supervision？

作者接著指出 policy pool 通常不是冗餘的。某個 expert 可能能忍受 camera shift，卻在物件被移動後失敗；另一個 expert 可能速度較慢，但在困難初始狀態中更可靠。若只看 global average，會掩蓋「哪個 expert 適合哪種 condition」的條件資訊。

Introduction 特別把問題邊界定清楚：對某個 target task 與 specified perturbation，系統對每個 expert 跑 B 個 probes，摘要出 success、rollout length、duration、termination behavior 等 profile，再選一個 expert 去做 scored execution。被評分的結果在選擇時不可用。這不是 zero-probe routing，也不是 unseen condition 上的純泛化，而是針對會重複使用的 station、task configuration 或 operating condition，利用初始 qualification phase 的資訊做 routing。

作者也指出 retrospective evaluation 的陷阱：如果拿同一個 scored rollout 既建立 expert profile 又評分 selection，selector 可能只是吃到 outcome proxy，而不是真正預測另一個 disjoint execution。這就是他們把 outcome separation 視為問題定義的一部分，而不只是報告細節的原因。

## 研究的第一性問題

- 基本問題：當多個 robot policies 的能力互補時，系統應該追求單一最強模型，還是學會在特定 condition 下選對 expert？
- 約束：真實機器人試跑昂貴，commissioning probes 數量有限；部署時不能偷看將被評分的結果。
- 既有方法卡點：global average 把不同 perturbations、task variants、expert strengths 壓成單一分數，丟掉條件性資訊。
- 作者試圖移動的邊界：把 smoke tests 從「部署前成本」改寫為「routing supervision」，並用 outcome-disjoint protocol 避免自我欺騙。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 少量 commissioning probes 已能讓 policy pool 在 target condition 下比 global winner 更好用。
- 透明的 probe-success rule 就能帶來顯著 held-out success 提升。
- 在 scalar-only profiles 下，更複雜的 learned scorers 沒有明顯超過簡單規則。
- 評估 routing 時必須做 outcome separation，否則同 trial reuse 會高估效益。

### 我的保守判讀

- 這篇最重要的概念可能是「deployment scaling」：不是只擴大單一模型，而是擴大系統使用既有 specialists 的能力。
- outcome-disjoint evaluation 是這篇的關鍵方法論提醒。對任何 agent / robot router，如果 profile 與 score 沒切開，報告出來的 routing gain 很可能太樂觀。
- 但本文依賴 LIBERO-Plus ledger 與 scalar profiles；如果真實世界 probes 更 noisy、任務分布更稀疏，簡單 probe-success rule 是否仍穩定，需要讀完整實驗與後續外部驗證。
- 這不是證明 VLA routing 已能泛化到未見情境，而是證明在有 target-condition probes 的情況下，commissioning evidence 可能有可觀價值。

## 可放進資料庫的筆記

1. Robot foundation models 的進步可以分成兩條軸：單一模型 scaling，以及多 expert 系統的 deployment scaling。
2. Smoke tests 不只是驗收流程，也可以是低成本的 routing supervision。
3. 對 heterogeneous policies，global average 可能是錯誤摘要；條件性可靠度比總平均更接近部署需求。
4. Outcome separation 是 routing 評估的底線：建 profile 的 trial 與評分 trial 必須分離。
5. 簡單規則若接近 learned scorer，代表真正的瓶頸可能是 evidence acquisition，而非 scorer capacity。
6. VLA 系統未必需要立刻蒸餾成單一 generalist；先把 specialist lifecycle 管好也可能是有效路徑。
7. 「選對模型」在 embodied systems 中會牽涉成本、風險與安全，不能只看 benchmark aggregate score。

## 後續想追的問題

1. LIBERO-Plus ledger 的 perturbation 類型與真實部署情境有多接近？
2. 三個 probes per expert 的成本在真實機器人部署中是否可接受？不同 B 值下效益如何變化？
3. Scalar profiles 包含哪些特徵？若加入影像、語言任務描述或 trajectory embeddings，learned scorer 是否會超過簡單規則？
4. Router 選錯 expert 的失敗模式是什麼？有沒有安全風險分層？
5. 未來若把 routed executions 回收成資料，如何避免 routing bias 造成資料分布越來越偏？
