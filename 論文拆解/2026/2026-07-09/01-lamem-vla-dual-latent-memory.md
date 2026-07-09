# Dual Latent Memory in Vision-Language-Action Models for Robotic Manipulation

## 原文資訊
- 論文：Dual Latent Memory in Vision-Language-Action Models for Robotic Manipulation
- 作者：Hongyu Qu; Jianzhe Gao; Xiaobin Hu; Shaohuan Yang; Xinlei Yu; Rui Yan; Wenguan Wang; Xiangbo Shu; Shuicheng Yan
- arXiv ID：2607.07608v1
- 分類：cs.RO, cs.CV
- 發表 / 更新：2026-07-08 / 2026-07-08
- 連結：[abs](https://arxiv.org/abs/2607.07608) / [pdf](https://arxiv.org/pdf/2607.07608)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-07-09

## 為什麼選這篇

這篇直接落在「LLM/VLM/VLA + Robotics」交會處：它不是單純做機器人控制，也不是單純做多模態理解，而是問 VLA 模型在長程操作任務裡，怎麼把歷史經驗變成模型推理時真正可用的內部記憶。

我選它的主要原因，是它把近期 VLA 常見的短視窗問題講得很清楚。許多 VLA 模型看起來能從目前影像與語言指令生成 action chunk，但在長步驟任務中，模型需要知道「已經做過什麼」、「物體狀態如何變化」、「現在是任務哪個階段」。這些資訊不一定存在於當前畫面，也不一定能靠固定長度的影片上下文解決。

另一個值得記錄的點，是作者把 memory-augmented VLA 的問題從「加一個外部 memory bank」推進到「記憶是否能進入 VLA 原生 latent embedding space」。這對之後思考 robot foundation model 很有用：記憶若只是 policy 旁邊的輔助資料，與記憶若參與模型內部的多模態推理，兩者系統假設不同。

## 一句話理解

LaMem-VLA 想解決的是：讓機器人歷史經驗不只是外掛檢索資料，而是被重建成可插入 VLA latent 空間的短期與長期記憶 token，參與長程操作的動作推理。

## Summary / Abstract 說了什麼

摘要指出，主流 VLA 模型多半依賴近似 Markovian 的假設，也就是主要根據當前 observation 預測動作；這在長程、時間相依的 manipulation task 裡會遇到困難。既有 memory-augmented VLA 常見兩種做法：一是擴大 observation window，二是從 memory bank 取回歷史資訊，作為 policy 端的輔助 context。作者認為這些方法仍把記憶放在 VLA 原生推理空間之外。

LaMem-VLA 的做法，是把歷史經驗重建為 latent memory tokens，並直接和當前 observation、instruction、action query 放在同一個連續 embedding sequence 裡。摘要列出四個元件：curator 將歷史經驗整理為短期與長期 memory vault；seeker 用目前多模態 cognition 查詢相關記憶；condenser 將檢索結果壓縮成短期與長期 latent memory token；weaver 則把這些 token 編入 VLA 的推理序列。

摘要與 Introduction 都提到作者在 SimplerEnv 與 LIBERO 做了評估，並宣稱 LaMem-VLA 優於既有方法。不過本次沒有閱讀實驗章節，因此這裡只把它視為論文自稱，不把數字延伸成已驗證結論。

## Introduction 的問題設定

Introduction 先從 VLA 模型的吸引力說起：它們結合 pretrained vision-language model 與 robot policy learning，把影像 observation 與語言 instruction 映射成可執行的 action chunks。問題在於，若模型主要根據當前 observation 反應，就會形成作者稱為 temporal short-horizon bias 的限制。

作者將既有補救方法分成兩類。第一類是把歷史 frames 串進輸入或把輸入擴展成 video sequence；這能看到近期狀態變化，但計算與記憶體成本會隨 context length 增加，而且固定視窗仍然會丟掉視窗外的任務證據。第二類是從外部 memory bank 檢索過去軌跡或歷史 tokens，再讓下游 policy 使用；這證明歷史經驗有價值，但作者批評它把 memory 存在模型原生 token space 之外，讓 memory 無法在 VLA 的 perception、instruction interpretation、action query resolution 過程中自然交錯。

接著，Introduction 提出核心問題：歷史經驗能否被表示成 context-native robotic memory，在 VLA 已經用來感知、推理與行動的連續空間中被儲存、檢索與消費？作者的答案是 latent embedding space。短期 memory 偏向視覺證據，例如目前 episode 的物體位置與細微狀態變化；長期 memory 偏向語意、任務進度與 action continuity。兩者功能不同，但最後都被重建為 latent memory tokens。

## 研究的第一性問題

- 基本問題：長程機器人操作需要跨時間的狀態、任務進度與動作連續性；VLA 若只看當下畫面，會缺少這些資訊。
- 約束：context 長度、計算成本、歷史資訊冗餘、記憶與模型內部推理空間不相容，都會限制可用性。
- 既有方法卡點：擴大視窗會增加成本且仍有上限；外部 memory bank 雖能檢索歷史，但常被當成 policy 旁邊的輔助 context，而不是 VLA 推理序列的一部分。
- 作者試圖移動的邊界：把 robotic memory 從「外部資料」改寫成「模型原生 latent token」，讓歷史經驗可以在 action query 被解析前參與推理。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 提出 LaMem-VLA，將歷史經驗重建為可直接插入 VLA embedding sequence 的 latent memory tokens。
- 將記憶分為短期視覺證據與長期語意 / action continuity 兩種 vault。
- 以 curator、seeker、condenser、weaver 四個模組構成 latent memory pipeline。
- 宣稱在 LIBERO 與 SimplerEnv 上優於基線與既有 memory VLA 方法。

### 我的保守判讀

- 這篇的概念價值在於「memory placement」：記憶到底放在模型外、policy 旁，還是進入模型原生推理空間。這會影響後續 VLA 架構設計。
- 真正的限制需要看實驗與 ablation 才能判斷，特別是 latent memory 是否只是增加參數 / context 的另一種形式，還是真的改善長程任務的 credit assignment 與 phase tracking。
- Introduction 對短期 / 長期記憶的功能區分有啟發性，但要看實作細節才能知道這個區分是否穩定、可解釋，或只是工程上方便的分桶。
- 若部署到真實機器人，memory retrieval 的錯誤、過時歷史、跨任務污染與安全約束可能是比 benchmark 成績更重要的問題；本次閱讀範圍不足以判斷作者如何處理。

## 可放進資料庫的筆記

- **VLA 的短視窗偏誤**：能反應當前畫面，不等於理解任務歷程；長程操作需要知道已完成步驟與狀態轉移。
- **記憶位置是架構問題**：外部 memory bank、policy-side context、native latent tokens 是三種不同系統假設。
- **短期記憶 vs 長期記憶**：短期偏視覺與當前 episode 狀態；長期偏語意、任務進度與 action continuity。
- **Context-native memory**：若記憶要參與多模態推理，最好在模型本來的 token / embedding 空間中被消費，而不是只在最後接給 policy。
- **Memory condenser 的必要性**：歷史資料通常冗餘，長程任務需要先壓縮成固定長度或可控長度的表示。
- **Action query 前置整合**：記憶若在 action query resolution 前進入序列，與在動作生成後才補資訊，語意上不同。
- **Physical AI 的資料問題不只在資料量**：資料如何被記住、取回、壓縮、放入推理流程，同樣是模型能力的一部分。

## 後續想追的問題

- LaMem-VLA 的短期 / 長期 memory vault 實際如何建立？是否需要額外 supervision？
- ablation 是否能證明 native latent memory 比外部 retrieved context 更有效，而不只是更大 context 或更多模組帶來的提升？
- memory retrieval 錯誤時，模型是否會出現比無記憶模型更嚴重的動作偏差？
- 在真實機器人連續部署中，記憶何時寫入、何時遺忘、如何避免跨任務污染？
- 這種 latent memory 設計能否與 safety monitor 或 initiation authorization 類機制結合？
