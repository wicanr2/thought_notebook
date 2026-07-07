# Feeling the Unexpected: ResTacVLA for Contact-Rich Manipulation via Residual Tactile Representation

## 原文資訊
- 論文：Feeling the Unexpected: ResTacVLA for Contact-Rich Manipulation via Residual Tactile Representation
- 作者：Pengwei Zhang, Bin Xie, Ce Hao, Xinpan Meng, Xinyu Guo, Fang Deng, Long Cheng, Tiancai Wang
- arXiv ID：2607.03387v1
- 分類：cs.RO
- 發表 / 更新：2026-07-03 / 2026-07-03
- 連結：[abs](https://arxiv.org/abs/2607.03387) / [pdf](https://arxiv.org/pdf/2607.03387)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-07-07

## 為什麼選這篇

這篇同樣屬於 VLA for robotics，但切入點不是語言推理，而是「接觸」：當機器人要做插入、鎖螺絲、擦拭等 contact-rich manipulation，視覺常常看不到真正的物理狀態。這是 Physical AI 很實際的瓶頸，因為真實世界的操作不是只靠辨識物體與生成軌跡就能完成。

我選它的原因是，它把觸覺整合的問題描述成 modality collapse：視覺訊號高頻、連續、維度大；觸覺訊號稀疏、事件驅動。若只是把觸覺當另一個 token 或 feature 丟進模型，模型可能自然偏向「聲音比較大」的視覺，反而忽略真正關鍵的接觸訊息。

它提出的 residual tactile representation 很值得記下：觸覺不必與視覺平權競爭，而可以被定義為「視覺預期與實際物理感覺的差」。這種 framing 對多模態機器人很有啟發，因為每個 modality 的價值未必在原始訊號本身，而在它相對於其他 modality 能提供多少額外資訊。

## 一句話理解

ResTacVLA 想讓 VLA 在接觸密集任務中不要被視覺主導，而是把觸覺轉成「意外 / 殘差」訊號，只在物理不確定時強化觸覺影響。

## Summary / Abstract 說了什麼

摘要指出，觸覺對 contact-rich manipulation 很重要，但把觸覺整合進 VLA 時容易出現 modality collapse：高頻寬視覺特徵壓過稀疏觸覺線索。作者受到 predictive coding 啟發，主張系統不應等量處理所有輸入，而應削弱可預期訊號，把注意力放在「出乎預期」的感覺上。

ResTacVLA 因此把觸覺改寫成 residual tactile representation，也就是視覺先驗與實際物理感覺之間的差異。摘要說，這些 residuals 會透過 vector quantized bottleneck 離散化成 latent contact primitives，捕捉視覺錯過的關鍵接觸事件；另外再用 visual prior 的 uncertainty 做 gating，在視覺不可靠時才加強觸覺整合。

摘要宣稱這能避免視覺支配，並在 contact-rich manipulation 中優於 baseline。這裡我只根據摘要與 Introduction 記錄設計邏輯，不把實驗數字視為已充分驗證。

## Introduction 的問題設定

Introduction 先指出，VLA 透過大規模 embodied pretraining 變成機器人操作的重要方向，但現有 VLA 多半是 vision-centric。對許多一般操作任務，視覺可能已足夠；但在精密插入、螺紋組裝、表面擦拭等任務中，真正決定成敗的是接觸、摩擦、卡住、偏移等物理動態，而這些常被遮擋或低於視覺解析度。

作者接著說，直接把觸覺投影到共享特徵空間是一個直覺方法，但會遇到 modality collapse。原因不是觸覺不重要，而是訊號型態不對稱：視覺是連續、高頻寬、密集；觸覺則較稀疏、事件驅動。如果模型在預訓練或融合時沒有特別設計，觸覺可能被視覺「淹沒」。

論文借用 predictive coding 的直覺：生物系統會預測感覺結果，並削弱可預測輸入，把注意力放在 prediction error，也就是意外訊號。作者用「人不能搔自己癢」作比喻：因為大腦預測到自發動作的感覺後果，所以會抑制預期中的感覺，優先處理外部或意外刺激。

在這個設定下，ResTacVLA 不把觸覺當作另一個原始模態，而是透過 Cross-Modal Predictor 估計視覺應該預期到的觸覺 latent，再取其與實際觸覺之間的殘差。這個殘差被向量量化為 Latent Contact Primitives；同時，Surprise-Aware Gate 會根據視覺先驗的不確定性調節觸覺注入，讓觸覺在接觸關鍵期被放大，在自由空間移動時被抑制。

Introduction 使用到的不確定性符號是 `σ_t`，可理解為時間 `t` 的視覺先驗不確定度；直覺上，`σ_t` 越高，代表視覺越不確定，觸覺殘差越應該被系統重視。

## 研究的第一性問題

- **基本問題**：當視覺無法充分描述接觸狀態時，VLA 如何利用觸覺而不被視覺訊號壓過？
- **約束**：觸覺訊號稀疏且事件驅動；視覺訊號高頻寬且容易主導 representation；contact-rich 任務需要低延遲反應；多模態融合不能只靠把 feature 接在一起。
- **既有方法卡點**：naive tactile fusion 讓觸覺與視覺在同一空間競爭，卻沒有處理兩者資訊密度與時間型態的不對稱。
- **作者試圖移動的邊界**：把觸覺的角色從「另一個輸入」改為「視覺預期失準時的殘差信號」，以資訊增益而非模態平權來做融合。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 提出 ResTacVLA，以 residual tactile representation 整合觸覺。
- 用 Cross-Modal Predictor 將視覺預期與實際觸覺的差異編碼成高資訊量表示。
- 用 VQ bottleneck 形成 Latent Contact Primitives，捕捉視覺漏掉的接觸事件。
- 用 Surprise-Aware Gate 根據視覺不確定性調節觸覺注入。
- 宣稱在五個真實 contact-rich 任務中優於標準 VLA 與 naive tactile fusion baseline。

### 我的保守判讀

- 這篇最有價值的概念是「殘差模態」：某個模態不一定要提供完整世界描述，只要提供其他模態預測不到的差異。
- 若方法成立，它可能比單純加大 tactile encoder 更有系統性，因為它先改變了問題定義。
- 但 Introduction 尚未交代 Cross-Modal Predictor 如何訓練、觸覺硬體與任務分佈多廣、VQ contact primitives 是否能跨物體與跨任務泛化。
- Predictive coding 是有啟發性的類比，但不能因此自動推論模型具備類似生物感知機制；這仍然是工程設計，不是神經機制證明。

## 可放進資料庫的筆記

1. **殘差模態思維**：模態融合不一定是把所有原始訊號平等拼接，而是問「哪個模態能解釋其他模態解釋不了的誤差」。
2. **Modality collapse 的物理版**：在機器人中，視覺可能因資料量與特徵密度太強而壓過較稀疏但關鍵的觸覺。
3. **接觸是 Physical AI 的真實邊界**：許多任務失敗不在語意理解，而在細微力學狀態不可見。
4. **Surprise 作為 gating signal**：不確定性 `σ_t` 可以被用來決定何時讓觸覺進入控制，而不是每一步都等量融合。
5. **Latent Contact Primitives**：接觸事件可被離散成可重用的物理詞彙，這可能是觸覺版的 tokenization。
6. **視覺先驗不是敵人**：ResTacVLA 不是放棄視覺，而是用視覺產生預期，再把觸覺用於修正預期。
7. **多模態模型要處理訊號節奏差異**：連續模態與事件模態的融合，需要時間尺度設計，不只是 feature 對齊。

## 後續想追的問題

1. Cross-Modal Predictor 的訓練資料是否需要同步視覺—觸覺紀錄？資料收集成本多高？
2. Latent Contact Primitives 是否能從一類接觸任務遷移到另一類，例如插入到擦拭？
3. Surprise-Aware Gate 的不確定性 `σ_t` 如何估計？是模型輸出 variance、ensemble，還是 heuristic？
4. 若觸覺感測器漂移或損壞，殘差表示會不會把硬體噪音誤認成「意外」？
5. 這種方法能否和長期記憶式 VLA 結合，讓機器人記住某個物件的接觸特性？
