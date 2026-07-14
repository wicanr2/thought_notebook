# CLAP: Direct VLM-to-VLA Adaptation via Language-Action Grounding

## 原文資訊
- 論文：CLAP: Direct VLM-to-VLA Adaptation via Language-Action Grounding
- 作者：Yuri Ishitoya, Jeremy Siburian, Masashi Hamaya, Kuniaki Saito, Cristian C. Beltran-Hernandez, Mai Nishimura
- arXiv ID：2607.08974v1
- 分類：Robotics (cs.RO)；Artificial Intelligence (cs.AI)
- 發表 / 更新：2026-07-09 / 2026-07-09（v1）
- 連結：[abs](https://arxiv.org/abs/2607.08974) / [pdf](https://arxiv.org/pdf/2607.08974)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-07-14

## 為什麼選這篇

這篇是很直接的 LLM / VLM 與 Robotics 交會題目：它問的不是「再做一個更大的 VLA」，而是如果盡量不改 pretrained VLM 架構，能不能把 VLM 轉成可執行 robot action 的 VLA。這對理解 VLM 能力到底如何轉移到控制任務，比單純堆更多 robot data 或 action head 更有分析價值。

我覺得值得收進資料庫的原因，是它把 VLA 的一個常見但容易被忽略的問題講清楚：VLM 原本被訓練來產生語意化語言，但 robot action token 往往是裸露的數字序列。這種 output-distribution mismatch 會讓模型離原本的語言分布很遠，也讓我們難以判斷語言 / 視覺能力是否真的被保留下來。

它提出的 CLAP 做法相對小：在數字 action token 前面先生成自然語言的 action description，讓後續 action token 受一段語言化計畫 conditioning。這個設計不一定保證實驗上全面優於複雜架構，但它提供一個很乾淨的研究問題：把 action 重新包回語言分布附近，是否能讓 compact VLA 更有效學習？

## 一句話理解

CLAP 想用「先說明動作，再輸出動作 token」的方式，降低 VLM 轉成 VLA 時語言生成分布與機器人 action 分布之間的落差。

## Summary / Abstract 說了什麼

摘要指出，VLA 繼承 pretrained VLM 的語意能力，但大規模 robot post-training 與架構修改可能大幅改變 backbone，使得研究者難以隔離 VLM 本身對控制任務的貢獻。作者認為，若能用最小架構變動把 pretrained VLM 直接轉成 VLA，會更容易分析不同模型尺度下的能力轉移。

核心問題是 output-distribution mismatch：直接預測裸露 numeric action token，會讓生成任務遠離 VLM 預訓練時熟悉的語言分布，可能破壞原本想保留的能力。CLAP 的解法是 Causal Language-Action Prediction：在每段數字 action sequence 前面加上自然語言動作描述，讓模型先產生 language-action plan，再在因果生成過程中預測精確 action token，而且不修改 backbone 架構。

摘要宣稱，在 single-epoch fine-tuning 下，2B CLAP 在 LIBERO 達到 90.8%，比 VLA-0 高 14.9 個百分點，並在 LIBERO-PRO 的語言、物件、空間擾動下提高 robustness。作者也說會釋出 0.8B、2B、4B 的 open-weight compact VLA family，用同一個 VLM lineage 來支持 controlled analysis。

## Introduction 的問題設定

Introduction 從 VLM 的快速進展切入：VLM 的感知與推理能力愈來愈強，但把這些能力轉成 robot control 仍然困難。VLA 透過 robot demonstrations fine-tune pretrained VLM，希望把視覺語言能力帶到控制任務；然而現有方法常加入 discretized action tokens、learned action experts 或 diffusion action heads。這些設計可能提升 action prediction，但也讓模型離原本 VLM 架構與訓練分布更遠。

作者把問題收斂到一個可操作的疑問：要怎麼讓 VLA 研究像 VLM 研究一樣容易分析？他們認為主要障礙之一，是 VLM 原本學的是有語意結構的語言生成，VLA fine-tuning 卻要求它輸出像 `4 12 98 3 0 0` 這類缺乏語言與空間結構的 action token。對 compact VLA 來說，這種 distribution shift 可能尤其敏感。

CLAP 的 introduction 宣稱，它不新增 action expert、不改 vocabulary、不改架構、不需手工標註；只把 output representation 改成「自然語言 action description + numeric action tokens」的自回歸序列。語言描述不是輔助任務，而是因果注意力中的 conditioning intermediate。這使 numeric action token 在生成時更靠近語言化的 action plan。

作者在 Introduction 也提出初步實驗宣稱：以 Qwen3.5 compact backbones 的 0.8B、2B、4B 做評估；2B CLAP 在 LIBERO single epoch 達 90.8%，在 robustness benchmark 上也有改善；並觀察到 2B backbone 在多數 capability dimension 最強，暗示 compact regime 裡 parameter count 不是唯一決定因素。

## 研究的第一性問題

- **基本問題**：pretrained VLM 的語意與空間理解，如何以最小破壞轉移到 robot action generation？
- **約束**：robot policy 必須輸出可執行的精確 action token；但 VLM 的預訓練分布主要是語言，不是裸露數值控制序列。
- **既有方法卡點**：action experts、diffusion heads、重度 post-training 可能有效，但會混入太多架構與資料因素，讓「VLM 本身貢獻了什麼」變得難分析。
- **作者試圖移動的邊界**：把 action token 放在語言化 action description 之後，讓控制輸出保留 executability，同時讓生成過程更接近 VLM 熟悉的語言分布。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- CLAP 只改 output representation，不改 backbone、vocabulary 或 action expert。
- 自然語言 action description 可作為 numeric action token 的 causal conditioning intermediate。
- 單 epoch fine-tuning 就能在 LIBERO 取得高成功率，並在 perturbation 設定下提高 robustness。
- 多尺度 open-weight compact VLA family 可用於分析 VLM-to-VLA capability transfer。

### 我的保守判讀

- 這篇的價值不只在分數，而在提供一個比較乾淨的 VLA 研究控制變因：把「語言分布是否幫助 action token learning」變成可測問題。
- 但我目前只讀到摘要與 Introduction，還不能判斷實驗是否排除了資料處理、action tokenization、benchmark selection 等 confounders。
- 「自然語言 action description」是否真的被模型用來規劃，或只是提供一個更容易學的中間 token pattern，需要讀後續 ablation 與 attention / error analysis 才能判斷。
- 如果 deployment 需要低延遲，先生成語言再生成 action token 的成本與穩定性也要看實驗細節。

## 可放進資料庫的筆記

- VLA 的 output representation 不是中性選擇；它會改變 VLM 能力是否能保留下來。
- 裸露 action token 對 VLM 來說可能是分布外語言，會讓 fine-tuning 同時學控制與學一種陌生輸出語法。
- 「先語言化動作，再輸出控制」可視為把 policy learning 拉回 pretrained language manifold 附近。
- 架構越複雜，越難回答 foundation model 到底貢獻了什麼；最小修改路徑適合做 capability transfer 分析。
- Compact VLA 的關鍵不只是參數量，也可能是輸出格式、fine-tuning recipe 與分布對齊。
- 語言 action prefix 可以被看成一種可讀的中間表示，但是否忠實反映控制因果，仍需實驗驗證。
- VLA 開源 lineage 若能固定 backbone 與尺度，會比單點模型更適合研究 scaling 與能力遷移。

## 後續想追的問題

- CLAP 的 ablation 是否證明 language-action description 本身，而不是額外 token 長度或訓練穩定性，帶來主要提升？
- 自然語言 action description 是如何產生的？是否完全由資料自動轉換，或依賴額外規則與 annotation？
- 在長任務、閉環控制與 out-of-distribution objects 上，language prefix 是否仍能穩定 conditioning？
- 0.8B、2B、4B 的差異是否呈現非單調 scaling？如果是，原因更像模型能力、資料量，還是 fine-tuning dynamics？
- 這種 output representation 能否與 diffusion policy 或 continuous action head 結合，而不失去分析乾淨度？
