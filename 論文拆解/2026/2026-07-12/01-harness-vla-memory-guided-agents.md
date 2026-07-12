# Harness VLA: Steering Frozen VLAs into Reliable Manipulation Primitives via Memory-Guided Agents

## 原文資訊
- 論文：Harness VLA: Steering Frozen VLAs into Reliable Manipulation Primitives via Memory-Guided Agents
- 作者：Yixian Zhang, Huanming Zhang, Feng Gao, Xiao Li, Zhihao Liu, Chunyang Zhu, Jiaxing Qiu, Yuchen Yan, Jiyuan Liu, Wenhao Tang, Zhengru Fang, Yi Nie, Changxu Wei, Yu Wang, Wenbo Ding, Chao Yu
- arXiv ID：2607.08448v1
- 分類：cs.RO
- 發表 / 更新：2026-07-09 / 2026-07-09
- 連結：[abs](https://arxiv.org/abs/2607.08448v1) / [pdf](https://arxiv.org/pdf/2607.08448v1)
- 本次閱讀範圍：Summary/Abstract + Introduction（arXiv HTML）
- 擷取日期：2026-07-12

## 為什麼選這篇

這篇很貼近「LLM + Robotics / Embodied AI」交會問題：VLA 模型擅長把視覺觀察與語言條件轉成局部接觸動作，但在長任務、場景重配置、目標重新綁定與語意變化下容易出界；LLM coding agent 則擅長語意拆解與組合，但純分析式 primitive 又不擅長細緻接觸控制。作者把兩者的邊界問題放在同一個系統設計裡討論，而不是把所有能力都塞進單一端到端模型。

我選它的原因是：近期很多 VLA 論文都在追求更大的模型或更直接的 action prediction；這篇摘要與 Introduction 強調「frozen VLA + memory-guided agent + 固定 analytic primitives」的 harness 思路，像是在問機器人系統能不能先用工程化外骨骼，把既有 VLA 放進較可靠的操作區間。這對實際部署很重要，因為真實機器人常遇到的不是完全沒能力，而是能力在 distribution shift 下不穩。

它也延續資料庫中幾條 Physical AI 線索：skill composition、VLA reliability、長任務與部署 perturbation。相較於單純 benchmark 表現，這篇更值得記錄的是系統分工觀：哪一些留給語意 agent，哪一些交給 analytic primitives，哪一些才讓 VLA 在接觸密集區域處理。

## 一句話理解

這篇想把 frozen VLA 當成可重試的局部接觸 primitive，再用具記憶的 agent 與固定 analytic primitives 包住它，讓語言條件操作在場景變動與長任務中更穩。

## Summary / Abstract 說了什麼

摘要指出，語言條件操作同時需要精細的 contact-rich control，以及對語言、場景與長期目標的穩健推理。端到端 VLA 在局部視覺—動作控制上有優勢，但它們主要從特定任務軌跡訓練，部署時遇到語意重定向、目標重新綁定、空間布局改變或局部接觸不穩，就可能失效。

作者認為 LLM coding agents 可以補上語意與組合推理，但如果只靠 analytic primitives，很難處理不規則抓取、受限放置、可動物件互動等物理細節。Harness VLA 因此把 frozen VLA 暴露成可重試的 contact-rich primitive，並結合一個小而固定的 analytic primitive library，負責 grounding、staging、transport、navigation、release 等較可解析的部分。

摘要中的關鍵不是「再訓練一個更大 VLA」，而是讓 harness 從任務級經驗記憶中學到這些固定 primitives 的 operating range。保守地說，這是一個把模型能力邊界記下來、並用記憶與重試策略管理邊界的機器人 agent 框架。

## Introduction 的問題設定

Introduction 從一個長期目標開始：機器人能否在物件、布局與 embodiment 變動下，可靠執行自由形式自然語言指令。作者把現有路線分成兩端：一端是端到端 VLA，從機器人軌跡中學 contact-rich visuomotor control；另一端是 LLM coding agent，透過語言模型推理組合顯式 perception/control API。

作者的問題診斷是，兩端都把太多責任放在錯的位置。Monolithic VLA 必須在同一個 policy 裡吸收語言 grounding、長期任務組合與低階控制；coding agent 則被迫用手工或生成的 API 去處理物理上很細緻的互動。Introduction 明確提出一個分工：analytic primitives 用來穿越部署 perturbations，VLA 只在其訓練分布比較有資訊的局部 contact-rich region 被呼叫。

這種鋪陳把 VLA 的優勢定義得比較窄：不是全任務智慧，而是局部、影像條件下的接觸能力；VLA 的弱點則是離開訓練軌跡分布後，對同一個物件或任務的泛化會變不穩。作者因此主張需要一個 harness，而不是把 VLA 直接當完整 agent。

## 研究的第一性問題

- **基本問題**：如何讓自然語言機器人操作同時具備語意組合能力與局部接觸控制能力，而不是在兩者之間二選一。
- **約束**：真實部署會有物件、空間布局、目標綁定、接觸狀態與任務順序變動；VLA 的訓練資料不可能覆蓋所有 perturbation。
- **既有方法卡點**：端到端 VLA 容易把語意、規劃與低階控制混在一起；LLM coding agent 的 analytic primitive 對細緻物理互動不足。
- **作者試圖移動的邊界**：把 VLA 從「完整策略」改看成「局部可重試 primitive」，用 agent 記憶與固定 primitive library 管理何時呼叫、如何重試、何時改走解析式步驟。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 提出 Harness VLA，一個 memory-augmented agentic framework，用 frozen VLA 處理 contact-rich primitive。
- 結合固定 analytic primitive library，處理 grounding、staging、transport、navigation、release 等較可結構化的動作段落。
- 透過任務級經驗記憶學習固定 primitives 的 operating range，而不是不斷擴充技能庫。
- 主張這種 harness 可改善 semantic retargeting、goal re-binding、spatial-layout shifts 與 local contact instability 下的可靠性。

### 我的保守判讀

- 這篇的價值可能在「系統分工」大於單點模型架構：它承認 VLA 不是完整機器人大腦，而是應被約束在較合適的局部控制區域。
- 真正困難處可能會落在 operating range 如何表示、記憶如何避免過擬合、失敗後如何判斷是 VLA 重試、primitive 切換，還是任務重規劃。這些需要讀方法與實驗才能判斷。
- 固定 primitive library 的好處是可控與可除錯；限制是可遷移範圍可能取決於 library 設計品質。摘要與 Introduction 尚不足以判斷它在不同 embodiment 或長尾任務上的泛化強度。
- 若未來 VLA 本身變得更強，harness 仍可能有價值，因為可靠部署通常需要能力邊界管理，而不只是更高平均成功率。

## 可放進資料庫的筆記

1. **VLA as primitive, not agent**：把 VLA 視為局部接觸 primitive，可降低它承擔語意規劃與長期組合的壓力。
2. **Harness 思維**：可靠機器人系統可能不是單一模型勝出，而是用外部結構把模型放在它最擅長的操作區間。
3. **Operating range memory**：部署經驗的重點不只記成功軌跡，也要記每個 primitive 在什麼條件下可用或易失敗。
4. **語意與物理的錯位**：LLM 擅長語意拆解，但不代表能自然產生可執行的接觸控制；VLA 擅長接觸，但不代表能處理完整任務語意。
5. **固定技能庫的策略價值**：不擴技能庫反而可能提升可分析性，因為系統學的是邊界與調度，而不是無限制技能膨脹。
6. **部署 perturbation 是主問題**：semantic retargeting、goal re-binding、layout shift 比單一 benchmark 任務更接近真實機器人痛點。
7. **重試不是補丁，而是架構元素**：如果 VLA 被設計成 retryable primitive，失敗恢復就成為系統能力的一部分。

## 後續想追的問題

1. Harness 的 memory 具體儲存什麼：語言條件、視覺狀態、primitive 結果，還是失敗類型？
2. 系統如何判斷 VLA 的 operating range：靠手工規則、LLM 反思、統計成功率，還是 learned classifier？
3. 固定 analytic primitive library 是否真的足以支援長尾任務？遇到 library 外的新行為時如何退場？
4. 評估是否包含跨物件、跨布局、跨 embodiment 的真正 out-of-distribution 測試？
5. 相比直接 fine-tune VLA 或加入更多機器人資料，harness 在資料效率與可靠性上的 trade-off 是什麼？
