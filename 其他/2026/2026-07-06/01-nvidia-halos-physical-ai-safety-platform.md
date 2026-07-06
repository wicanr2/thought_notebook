# 專欄｜NVIDIA Halos for Robotics：Physical AI 的安全平台不是一個套件，而是一整條責任鏈

## 原文資訊

- 原文：NVIDIA Newsroom — [NVIDIA Announces Halos for Robotics, the Industry’s First Full-Stack Safety System for Physical AI](https://nvidianews.nvidia.com/news/nvidia-announces-halos-for-robotics-the-industrys-first-full-stack-safety-system-for-physical-ai)
- 發布日期：2026-06-22
- 補充來源：NVIDIA Developer Blog — [Inside NVIDIA Halos for Robotics: A Full-Stack Functional Safety System for Physical AI](https://developer.nvidia.com/blog/inside-nvidia-halos-for-robotics-a-full-stack-functional-safety-system-for-physical-ai/)
- 補充來源：NVIDIA — [Halos: Robotics Safety](https://www.nvidia.com/en-us/ai-trust-center/halos/robotics/)
- 補充來源：GitHub — [NVIDIA/halos-outside-in-safety](https://github.com/NVIDIA/halos-outside-in-safety)
- 擷取日期：2026-07-06

## 為什麼寫這篇

Halos 值得單獨做專欄，不是因為 NVIDIA 又推出一個 robotics SDK，而是它把 Physical AI 的問題重新定義成「可驗證的安全責任鏈」。

過去討論機器人 AI，常會集中在：

- 模型能不能看懂環境；
- VLA / policy 能不能控制動作；
- Isaac Sim 能不能做 sim-to-real；
- AMR / humanoid 能不能進工廠、倉庫、物流場域。

但真正進入現場時，問題會變成另一個層次：**誰證明它在異常狀態下仍然安全？誰界定 AI 輸出何時不可靠？誰把 sensor、compute、OS、middleware、decision logic、certification 串成可被第三方檢查的系統？**

Halos 的訊號在這裡：Physical AI 若要從 demo 走向 deployment，安全不再只是 robot 上的一個 emergency stop，也不是模型外面包一層 guardrail，而是平台級、安全標準級、供應鏈級的整合能力。

## 一句話拆解

NVIDIA Halos for Robotics 是把 NVIDIA 在自駕車安全堆疊累積的硬體、OS、sensor bridge、functional safety blueprint 與檢驗流程，移植到 humanoid、AMR、工業機器人的 Physical AI 安全平台。

## 文章架構地圖

### 1. 先用「industry’s first full-stack safety system」定義戰場

新聞稿一開始不是從模型能力或 robot performance 開場，而是先宣稱 Halos 是 full-stack safety system。這個寫法的目的，是把比較軸從「誰的 robot 比較聰明」移到「誰能讓 robot 在人類環境中被允許運作」。

這是一個很重要的敘事轉向：

- demo 階段比的是 capability；
- pilot 階段比的是 integration；
- production 階段比的是 certification、liability、operational safety。

Halos 要搶的是第三層。

### 2. 接著把 AV 安全資產轉成 robotics 正當性

NVIDIA Developer Blog 強調 Halos 延伸自 autonomous vehicle safety，並列出 18,000+ engineering years、21B+ safety transistors、7M+ safety-assessed code、22,000+ safety monitors、30+ certificates / reports 等數字。

這些數字不只是宣傳規模，而是在說：robotics 不必從零建立安全平台，可以借用 AV 領域已經花大量成本建立的 process、tooling、assessment pattern。

這裡真正的商業邏輯是：**NVIDIA 想把「安全工程沉沒成本」變成 Physical AI 平台的進入門檻。**

### 3. 再把 stack 分成硬體、OS、外部感知、檢驗實驗室

Halos 不是單點產品，而是一組層級：

- **NVIDIA IGX Thor**：industrial-grade AI compute，加上 functional safety hardware，例如 Safety Island、diagnostic coverage、IST、FFI / DFI 支援。
- **Holoscan Sensor Bridge**：把 camera / sensor / actuator 串到 IGX，提供低延遲、MACsec、watermarking、safety protocol 等 sensor edge 能力。
- **Halos Core / Halos OS**：Linux 或 Linux + QNX configuration，處理 safety OS、hardware error dispatch、Edge Safety Link、FSI RTOS、Safety MCU firmware。
- **Outside-In Safety Blueprint**：用外部固定 camera、AI perception、event integration、decision maker 影響 robot 行為。
- **AI Systems Inspection Lab**：ANAB-accredited inspection body，讓 partner 先通過 NVIDIA inspection，再拿去給 TÜV、UL、SGS、exida、CertX 等做 final certification。

這種分層讓 Halos 不是單純賣 GPU，而是把 compute、OS、sensor path、safety app、certification pathway 變成一個「被允許部署」的供應鏈。

### 4. 用 Agility / Digit 當第一個具體落地案例

新聞稿選 Agility Digit 當案例很合理。Humanoid 的場景天然充滿風險：它要在人旁邊走動、搬運、轉身、避障，且常在倉庫、工廠、物流這類混合空間中運作。

Agility 使用 IGX Thor 與 Halos Core 來支援 proprietary safe human detection system，並參與 Halos AI Systems Inspection Lab，這讓 Halos 的定位更清楚：

- 不是取代 robot company 的安全系統；
- 而是讓 robot company 的安全系統可以站在一個標準化、可檢驗的平台上。

### 5. 最後用 ecosystem 把平台變成產業標準候選者

NVIDIA 列出 QNX、FreeRTOS、Acontis、Advantech、NexCobot、Infineon、NXP、ST、TI、FORT Robotics、KION、TÜV Rheinland 等 partner。這一段的重點不是每家公司多厲害，而是暗示 Halos 正在形成一個 safety ecosystem：

- RTOS / hypervisor / embedded OS；
- sensor / MCU / silicon；
- industrial application；
- certification body；
- ODM / edge system。

如果這條線成立，Halos 可能會變成 Physical AI 量產部署時的「安全共同語言」。

## 第一性原理拆解：Halos 解決的不是「機器人會不會動」，而是「誰能相信它可以動」

### 問題 1：Physical AI 的風險不是純軟體風險

一般 AI agent 出錯，最多是下錯指令、寫錯程式、調錯 API。Physical AI 出錯，可能撞人、壓貨、卡住產線、造成工安事故。

所以 Physical AI 的核心約束是：

```text
智能能力 × 實體作用力 × 人類共處空間 = 需要可驗證安全鏈
```

模型能力越強，風險不一定越低；因為它會讓 robot 更常進入非結構化場景。能力擴張會把安全問題從「固定區域內的機械防護」推向「動態環境中的情境判斷」。

### 問題 2：傳統 inside-out safety 在某些場景會傷害效率

很多 robot 主要靠 onboard sensors 做 inside-out safety：LiDAR、camera、bumper、proximity sensing。這在開放空間有效，但在 trailer loading 這種狹窄場景會出問題：forklift 可能把 trailer wall、cargo 或遮擋當成障礙，導致速度很慢或頻繁停車。

Halos Outside-In Safety Blueprint 的想法是：把安全感知從 robot 身上擴展到場域基礎設施。固定 camera 看到整個 loading dock，再由 AI perception / event integrator / decision maker 判斷何時可以暫時 mute onboard safety、何時恢復 full safety。

這不是「用 AI 取代安全」，而是用外部視角補上 onboard sensors 看不到、或容易誤判的情境。

### 問題 3：AI safety 必須處理「輸入何時不可相信」

Developer Blog 裡的 Safety AI Monitor（SAIM）很關鍵。它不是一般 object detection，而是監控 perception pipeline 是否可靠：

- camera blockage；
- connectivity drop；
- lighting change；
- out-of-distribution input；
- image anomaly。

這代表 Halos 的安全邏輯不是假設 AI 永遠準，而是設計一個問題：**當 AI perception 的輸入條件離開訓練或驗證範圍時，系統要如何退回 safe state？**

這一點比單純宣稱「AI 讓安全更好」更重要。

### 問題 4：certification 是 Physical AI 的真正落地瓶頸

Halos AI Systems Inspection Lab 的定位，是先檢查 partner 對 Halos stack、AI safety、cybersecurity requirements 的整合，再讓 partner 拿 inspection certificate 去找第三方 certification agency。

這裡的商業價值不是「NVIDIA 自己發證書就結束」，而是降低 partner 從 platform integration 到 system certification 的不確定性。

如果 robot company 每次都要從零向 TÜV / UL / SGS 解釋自己的 AI stack，那量產部署會很慢。Halos 想提供的是一個可重複的 certification pathway。

## Halos 平台的能力地圖

| 層級 | Halos 對應元素 | 要解的問題 | 需要會看的指標 |
|---|---|---|---|
| AI compute | IGX Thor | 同時跑 perception、planning、safety monitor | latency、throughput、thermal、redundancy、diagnostic coverage |
| Sensor path | Holoscan Sensor Bridge | camera / sensor / actuator 的低延遲與可信傳輸 | timestamp、watermark、MACsec、drop rate、jitter |
| Safety OS | Halos Core / Halos OS | hardware error、safety function、runtime isolation | FSI、SMCU、FFI、DFI、SEP、Edge Safety Link |
| RTOS / partition | Linux + QNX / NV Hypervisor | AI workload 與 safety-critical function 隔離 | VM boundary、real-time behavior、fault containment |
| AI safety app | Outside-In Safety Blueprint | 外部場域 camera 生成 safety decision | OOD detection、event fusion、staleness、FSM state |
| Simulation / validation | Isaac Sim / SIL / HIL | 在可控環境中驗證 safety logic | scenario coverage、failure injection、replayability |
| Certification path | AI Systems Inspection Lab | 把系統帶往第三方 certification | IEC 61508、ISO 13849、ISO/IEC TR 5469、inspection report |
| Ecosystem | QNX、FreeRTOS、Acontis、ODM、sensor vendor、certification body | 讓 platform 可被產業採用 | partner maturity、供應鏈可得性、長期維護責任 |

## 要駕馭 Halos，需要理解哪些技能樹？

下面不是「一個人全部精通」的清單，而是如果一家公司想真正掌握 Halos 類平台，團隊需要覆蓋的能力地圖。

### 1. Functional Safety / 工業安全標準

要懂：

- IEC 61508：通用 functional safety；
- ISO 13849：機械安全與控制系統安全；
- ISO 26262：自駕車安全資產如何被移植；
- ISO/IEC TR 5469、ISO/IEC TS 22440：AI 與 functional safety 的新興交界；
- SIL / ASIL / PL 等級不是 marketing badge，而是 hazard analysis、diagnostic coverage、systematic capability、validation evidence 的結果。

為什麼重要：Halos 的核心語言不是 deep learning，而是 safety case。若看不懂標準，就很難判斷它到底是平台能力、certification readiness，還是宣傳包裝。

### 2. Embedded / Real-Time / RTOS

要懂：

- QNX、FreeRTOS、Linux RT behavior；
- hypervisor partitioning；
- safety island / safety MCU；
- real-time scheduling、watchdog、fail-safe / fail-operational；
- EtherCAT、FSOE、industrial I/O。

為什麼重要：Physical AI 的安全決策不能只在 Python pipeline 裡跑。真正的 stop / slow / mute / unmute 要落到 deterministic runtime、safety channel 與 actuator interface。

### 3. Robotics stack

要懂：

- ROS 2 node、topic、service、action；
- TF / localization / mapping / Nav2；
- MoveIt 2 / motion planning；
- AMR、forklift、humanoid 的運動限制；
- onboard safety 與 facility-level safety 如何分工。

為什麼重要：Halos 不會替你理解 robot 本體。它提供 safety architecture，但 robot 的 kinematics、navigation、payload、braking distance、sensor occlusion 仍然要由 robotics team 建模。

### 4. Vision AI / Multi-camera perception

要懂：

- camera calibration、multi-camera tracking；
- object detection / tracking / re-identification；
- region of interest、tripwire、trajectory event；
- latency、staleness、confidence threshold；
- OOD / anomaly detection。

為什麼重要：Outside-In Safety 的第一步是把 camera stream 變成可信事件。若 perception 事件不可靠，後面的 Safety Decision Maker 只是把錯誤快速制度化。

### 5. Safety agent / Decision logic

要懂：

- finite state machine；
- event fusion；
- stale event discard；
- fallback state；
- rule-based safety logic 與 AI perception 的邊界；
- black-box logging 與 traceability。

為什麼重要：Halos 的「agent」不是一般 LLM agent，而是 safety agent。它不能只是會推理，還要可解釋、可測試、可重放、可被稽核。

### 6. Simulation / Digital Twin / SIL / HIL

要懂：

- Isaac Sim / Omniverse；
- scenario design；
- synthetic camera stream；
- software-in-the-loop（SIL）；
- hardware-in-the-loop（HIL）；
- failure injection：camera blocked、lighting changed、worker enters ROI、network delay。

為什麼重要：安全不是跑一次 demo 就算數，而是要系統性地覆蓋危險場景與異常條件。Halos 的價值很大一部分在於把 validation workflow 接到 safety architecture。

### 7. Cybersecurity / Secure sensor data path

要懂：

- device authentication；
- MACsec；
- secure boot / attestation；
- sensor spoofing / replay attack；
- industrial network segmentation；
- safety 與 security 的耦合。

為什麼重要：如果外部 camera 可以被 spoof，outside-in safety 反而會變成攻擊面。Physical AI 的 safety case 不能忽略 cybersecurity。

### 8. Platform / Ecosystem integration

要懂：

- ODM / IPC / edge AI box integration；
- sensor vendor、MCU vendor、RTOS vendor 的責任邊界；
- deployment lifecycle；
- certification body 如何看 evidence package；
- field maintenance 與版本控管。

為什麼重要：Halos 是平台，不是單一 library。真正導入時，會牽涉 robot vendor、工廠現場、系統整合商、certification agency、IT / OT security team。

## 對台灣供應鏈與自動化公司的觀察

Halos 對台灣比較有意思的地方，不一定是「誰直接做 humanoid」，而是以下幾層：

1. **工業電腦 / edge AI box**：Advantech、NexCobot 被列為 embedded systems partner，代表 safety-designed IGX-based systems 會是 Physical AI 部署的硬體入口。
2. **工廠自動化系統整合**：如果 outside-in safety 成為工廠 robot deployment 的常見模式，SI 需要懂 camera placement、event logic、ROS / PLC / WMS / WCS 串接。
3. **感測器與 MCU 生態**：Infineon、NXP、ST、TI 這類 partner 顯示 safety chain 會延伸到 sensor edge 與 safety MCU，台灣模組廠若要切入，需要理解 certification 與安全通訊，不只是供板子。
4. **AMR / 倉儲物流場景**：trailer loading、dock safety、forklift / AMR 共場，是比 humanoid 更早商業化的應用。台灣公司若要找切入點，應先看工廠內 AMR / forklift / conveyor / WMS 的安全互操作。

## 可抽象成的思考模型：Physical AI 平台三問

看任何 Physical AI 平台時，可以問三個問題：

1. **它處理的是 capability，還是 deployability？**  
   只展示 robot 能做什麼，是 capability；能說清楚如何被驗證、維護、認證、責任分配，才是 deployability。

2. **它的安全邏輯停在哪一層？**  
   如果只停在 model guardrail，離實體安全很遠；如果一路接到 sensor path、RTOS、safety MCU、actuator command、certification evidence，才有 production 意義。

3. **它是否把異常狀態納入設計，而不是把正常 demo 當成系統？**  
   真正的安全平台要回答 lighting 變差、camera 被遮、network delay、sensor timestamp stale、AI confidence 不穩、worker 突然進入 ROI 時怎麼辦。

## 我可以放進資料庫的筆記

- Halos 的核心不是「NVIDIA 推 robotics SDK」，而是把 Physical AI 的落地問題定義成 full-stack safety architecture：compute、sensor、OS、decision agent、inspection lab、certification body。
- Outside-In Safety 是重要概念：用場域固定 camera 與 AI perception 補足 onboard sensors，讓 robot 在特定條件下更有效率地運作，但必須同時設計 OOD / fallback / stale event handling。
- Safety agent 與 LLM agent 不同：前者要可驗證、可重放、可稽核、可退回 safe state；不能只用「會推理」來衡量。
- Halos 對 NVIDIA 的平台價值在於：把 AV safety 的沉沒成本與 certification pathway 移植到 robotics，形成 Physical AI 量產部署的共同底座。
- 要駕馭 Halos，團隊技能要跨 functional safety、embedded RTOS、robotics、vision AI、simulation validation、cybersecurity、industrial integration；這不是單一 AI 工程師能獨立完成的堆疊。

## 仍不確定的地方

- Halos Core 目前是 early access；很多實際部署細節、NDA 文件與完整 certification evidence 不公開，外部只能看架構與方向，不能驗證全部成熟度。
- Outside-In Safety Blueprint 的 GitHub README 明確說它是 prototyping / evaluation / integration development，不是未加 certified safety layer 就可直接用於 production safety-related systems。
- Agility / Digit 的實際整合深度、量產時間表、現場 performance 與 certification 結果仍需後續追蹤。
- Halos 是否會成為跨 vendor 標準，還是主要留在 NVIDIA IGX / Isaac / Omniverse 生態內，需要看 certification body、ODM、robot OEM、工廠 SI 的採用速度。

## 後續追蹤清單

- `NVIDIA/halos-outside-in-safety` repo 是否從 early access 走向更完整的 release / docs / examples。
- Halos AI Systems Inspection Lab 是否公布更多實際 certificate / inspection report 案例。
- Agility Digit 是否公布 Halos-enabled safety system 的正式客戶部署。
- QNX、Acontis、FreeRTOS、Advantech、NexCobot 等 partner 是否推出可購買、可維護、可 certification 的產品線。
- IEC 61508、ISO 13849、ISO/IEC TS 22440 在 AI-enabled robotics 上的採納情況。
