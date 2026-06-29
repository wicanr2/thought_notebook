# Rigs of Rods：把軟體車體物理當成 Physical AI 的「身體與接觸」章節

## 原始資訊

- 專案：[`RigsOfRods/rigs-of-rods`](https://github.com/RigsOfRods/rigs-of-rods)
- 專案網站 / 下載：[`https://www.rigsofrods.org/download`](https://www.rigsofrods.org/download)
- 文件：[`https://docs.rigsofrods.org/`](https://docs.rigsofrods.org/)
- Developer portal：[`https://developer.rigsofrods.org/`](https://developer.rigsofrods.org/)
- 本次檢查日期：2026-06-29
- 本次檢查方式：讀取 GitHub metadata、shallow clone、`README.md`、`BUILDING.md`、`DEPENDENCIES.md`、`doc/doxygen/CodebaseOverview.md`、`doc/angelscript/ScriptingFeaturesOverview.md`、`doc/angelscript/FreeForces.md`、`doc/angelscript/FreeBeams.md`、`doc/angelscript/ActorSimAttributes.md`、核心 C++ 檔案與範例 AngelScript。
- 最新檢查到的 commit：`d4625a821fca520c7cc3692c8013cd0352a916f2`
- GitHub metadata：public repo，預設分支 `master`，主要語言 C++，GPLv3 or later，約 1.2k stars、204 forks；repo topic 包含 `physics-simulation`、`sandbox-game`、`soft-bodies`、`trucks`、`trailers`、`trains`、`boats`、`airplanes`、`cars`。
- 最新 release：`2026.01`（2026-01-04）。

## 一句話結論

`Rigs of Rods` 不是現代 ROS / Gazebo / Isaac Sim 型的機器人訓練平台；它是一個開源的 **soft-body physics sandbox**，核心價值在於用「節點、樑、彈簧、阻尼、接觸、破壞、變形」來模擬車輛、機械、船、飛機與地形互動。

如果把它放進 Physical AI 筆記，它最適合成為一個章節：

> **Physical AI 不只是 policy 或 VLA，還需要一個能描述身體、接觸、力、變形與控制介面的世界。Rigs of Rods 可作為理解 soft-body vehicle / machine simulation 的案例庫。**

它的「共用」價值不在於直接拿來訓練通用 robot foundation model，而在於拆出幾個可移植的概念：可變形 body representation、力學步進、地形接觸、可腳本化控制、waypoint AI、外力 / ad-hoc beam、車輛格式與 mod 生態。

## 它是什麼，不是什麼

### 它是什麼

根據 README，Rigs of Rods 是一個開源物理沙盒遊戲，可即時模擬車輛的運動與變形。它支援：

- 汽車、卡車、火車、船、飛機、直升機、重機械。
- 玩家在地圖中移動、進出車輛、同時 spawn 多個 vehicle / actor。
- 鍵盤、gamepad、方向盤與踏板等輸入。
- 社群 mod、車輛零件替換、AngelScript 腳本。
- 多人連線、server browser。

程式架構上，它是 monolithic C++ 程式，有自己的 main input / rendering loop；OGRE 主要負責 3D rendering，不是完整 game engine。很多「遊戲引擎」層的系統由 RoR 自己實作，例如 message queue、mod cache、terrain、actor、simulation、scripting。

### 它不是什麼

它不是：

- ROS 2 middleware。
- Gazebo / Isaac Sim 那種以 robot sensor / robotics stack integration 為中心的 simulator。
- Gymnasium / RLBench / ManiSkill 那種以 RL / imitation learning benchmark 為中心的訓練環境。
- 現成的 VLA / embodied AI benchmark。
- 工業級安全驗證或 sim-to-real pipeline。

所以不能把它誤讀為「可直接拿來做 Physical AI 訓練平台」。它更像是 Physical AI 課程裡的一章：**從遊戲物理看可變形機械身體如何被建模、控制與暴露給腳本層。**

## repo 結構觀察

本次 shallow clone 後，重要目錄大致如下：

- `source/main/physics/`：核心物理、actor、collision、air、flex、wheels、forces 等。
- `source/main/gameplay/`：VehicleAI、Autopilot、角色、camera、gameplay logic。
- `source/main/scripting/`：AngelScript engine 與 C++ binding。
- `source/main/resources/`：rig definition、terrain、object、skin、dashboard、tuneup 等 file format parser。
- `source/main/terrain/`：terrain geometry、terrain object、procedural road、terrain editor。
- `resources/scripts/`：內建腳本與 demo，例如 vehicle input simulation、autopilot、truck physics、freeforce halfbeam、autotest scenario。
- `resources/gadgets/`：script editor、rig editor、road editor、engine tool、shock tool、ogre inspector 等工具。
- `doc/angelscript/`：腳本 API 與功能說明，共 108 個文件。

依檔案數觀察，repo 不只是 C++ source，也包含大量資源檔、材質、mesh、image、shader、script、翻譯與工具。這符合它作為 long-lived simulation sandbox / game 的特性。

## 核心技術觀察：它把「身體」拆成 node / beam

Codebase overview 明確指出，simulation subsystem 使用 mass-spring-damper physics：

- `RoR::node_t`：物理節點，可理解為具備位置、速度、質量、受力狀態的點。
- `RoR::beam_t`：節點之間的連桿 / 彈簧阻尼元件，承擔形變、應力、破壞等邏輯。
- `Actor::CalcNodes()`：處理 node 的 collision、integrate velocity / position、重力、空氣阻力、水阻與浮力等。
- `Actor::CalcBeams()`：處理 beam 長度偏差、彈簧力、阻尼力、shock、rope、support beam、deformation、breakage。

這對 Physical AI 很有啟發：很多 embodied AI 論文會把「身體」簡化成 action vector 或 robot morphology token，但真實物體不是剛體方塊。車輛、懸吊、輪胎、纜繩、載具結構、軟性連接、拖曳、碰撞與變形，都會改變 action 的結果。

Rigs of Rods 的視角是：

```text
車輛 / 機械不是一個 rigid body
而是一張 node-beam graph
每個 timestep 更新：接觸 → 外力 → 彈簧阻尼 → 破壞/變形 → 積分 → 顯示
```

這可以轉成 Physical AI 的一個思維模型：

> policy 不只是對「物體中心點」輸出速度；policy 實際上是在控制一個有結構、有延遲、有彈性、有破壞閾值的身體。

## 物理 loop 對 Physical AI 的可共用概念

`Actor::CalcForcesEulerCompute()` 的呼叫順序很像一份物理系統的課綱。它依序做：

1. `CalcNodes()`：節點碰撞與積分。
2. `UpdateBoundingBoxes()`：更新 actor 包圍盒。
3. `CalcEventBoxes()`：處理地形 event box 互動。
4. `CalcReplay()`：replay 系統。
5. `CalcAircraftForces()`：airbrake、turboprop、screwprop、wing forces。
6. `CalcFuseDrag()`：機身阻力。
7. `CalcBuoyance()`：浮力。
8. `CalcDifferentials()`：差速器。
9. `CalcWheels()`：車輪、牽引控制、ABS 類邏輯。
10. `CalcShocks()`：懸吊 / shock。
11. `CalcHydros()`：hydro beams，可控制方向、speed、ailerons、rudder、elevator。
12. `CalcCommands()`：命令 / control input。
13. `CalcTies()`：tie / secure load。
14. `CalcTruckEngine()`：引擎。
15. `CalcMouse()`：玩家滑鼠抓取 node 的力。
16. `CalcBeams()`：beam force / deformation / breakage。
17. `CalcCabCollisions()`：cab collision。
18. `updateSlideNodeForces()`：slide node force。
19. `CalcForceFeedback()`：回饋力。

這份順序的重要性在於：Physical AI 系統常常會把 simulator 看成黑盒，但如果要做可靠的控制與學習，必須理解力學步進的順序。控制輸入何時生效、碰撞何時更新、外力何時清空、感測值對應哪個 timestep，都會影響 policy 的穩定性。

## 地形、接觸與 event：Physical AI 的世界不是背景圖

Codebase overview 對 terrain 的描述值得記下：

- terrain 由 heightmap 產生。
- terrain object 透過 ODEF 格式定義 visual mesh、collision mesh 或 event box。
- TOBJ 可定義靜態物件與 procedural road。
- collision 包含 node vs. static surface，並使用 ground model 定義 surface properties。
- event box 可以根據 avatar、truck、truck wheels、airplane、boat 等 filter 觸發。

這對 Physical AI 的共用價值是：

1. **環境不是美術背景，而是可互動的 constraint field。**
2. **不同接觸材質會改變行動結果。**
3. **world 裡應該有事件區域，而不只是幾何。**
4. **任務可以從「到達座標」升級成「觸發某種世界狀態」。**

如果未來做 AMR / 工地機器人 / 越野機器人模擬，這些概念可以轉到 Gazebo / Isaac / Mujoco：高度圖、collision object、surface model、event trigger、wheel-only filter、contact state logging。

## 腳本層：它提供了控制與實驗接口

Rigs of Rods 使用 AngelScript。Developer portal 說明，腳本可以：

- 讀寫 console variables。
- 瀏覽、搜尋、加入、移除 installed mods。
- 透過 `game.pushMessage()` 把 request 放進主 message queue。
- 接收 game events。
- 讀取與模擬 controller inputs。
- live-adjust 某些 actor simulation attributes。
- spawn / inspect actors。
- link actors。
- teleport / move character。
- 建立 FreeForces / FreeBeams。
- 透過 OGRE binding 操作 scene graph、mesh、texture。

這很適合拿來對照 Physical AI 的常見系統分層：

```text
高階任務 / script
→ message queue / API
→ actor control input 或 force actuator
→ physics loop
→ world state / event callback
→ script 或 agent 再決策
```

這與現代 robotics stack 的形式相似：

```text
LLM / task planner
→ skill library
→ ROS action/service/topic
→ controller / simulator
→ sensor/state feedback
→ planner 更新
```

差別在於 RoR 不是 ROS，但它展示了「腳本層如何安全地改變 simulation state」這個設計問題。

## Waypoint AI：不是智能模型，但可當作控制基線

`VehicleAI` 是 simple waypoint AI。它可以：

- 啟用 / 停用 AI。
- 新增 waypoint。
- 新增 waypoint dictionary。
- 在 waypoint 綁定 event。
- 在 waypoint 設定速度與 power。
- 根據 vehicle rotation 與 waypoints 計算 offset translation。

這不是現代學習式 policy，但它是一個好 baseline：

- 對 navigation 來說，waypoint following 是最小可用控制策略。
- 對 imitation / RL 來說，rule-based waypoint AI 可以產生初始 demonstration 或 curriculum。
- 對 LLM planner 來說，waypoint 是介於語言任務與低階控制之間的中介 representation。

Physical AI 筆記可以把它放在「classical baseline」章節：

> 先讓機器能用 waypoint / PID / hand-coded controller 完成任務，再把 learned policy 加上去。不要一開始就把所有控制問題都交給 VLA。

## Autopilot：航空器控制的傳統接口

`AutoPilot.h` 顯示 RoR 內部有航空器 autopilot 概念：

- heading mode：none、fixed、wing-leveler、nav。
- altitude mode：none、fixed、vertical speed。
- IAS / GPWS / ILS 相關狀態。
- 控制輸出：ailerons、elevator、rudder、throttle。
- 可設定 inertial reference nodes。

這對 Physical AI 的啟發是：控制系統通常不是「輸出一個 action」而已，而是有模式、狀態、保護條件與多層控制器。語言模型若要接航空 / 車輛 / 工業設備，不應直接跳到 motor command，而應該呼叫既有控制模式或 skill。

```text
User / task planner: 維持航向、降低高度、接近 runway
Autopilot / controller: heading mode、altitude mode、IAS、ILS deviation
Physics: airfoil、engine、body drag、node/beam
```

這個分層比「LLM 直接輸出舵面角度」更接近工程現實。

## FreeForces / FreeBeams：可腳本化外力與臨時物理結構

`FreeForces.md` 說明 FreeForces 是 global、persistent forces，作用於某個 actor 的某個 node。它可以是：

- constant force。
- towards coordinates。
- towards node。

而 `FreeBeams.md` 把 FreeForces 擴充成接近 truck file 裡 beam 行為的 `HALFBEAM_GENERIC` / `HALFBEAM_ROPE`。若要完全模擬 beam，需要兩個相反方向的 halfbeam freeforces。

這對 Physical AI 很有用，因為它提供一個可共用的實驗概念：

- 在不改 vehicle rig 的情況下，加入外力、牽引、繩索、臨時連接。
- 測試 agent 對干擾力、拖曳、綁定、載重變化的反應。
- 把世界裡的 constraint 當成 runtime 可插拔元件。

換到 ROS / Gazebo / Isaac，對應概念可能是 apply wrench、constraint joint、temporary link、contact force plugin、domain randomization force disturbance。

## ActorSimAttributes：live tinkering 是實體 AI 實驗的必要能力

`ActorSimAttributes.md` 說，AngelScript 可對 actor physics attributes 做 live read / write，例如：

```angelscript
void main() { thisActor.setSimAttribute(ACTORSIMATTR_TC_WHEELSLIP_CONSTANT, 0.2); }
```

文件也指出防作弊限制：race 會被中止，多人模式會 log warning 並不生效。

這代表一個很重要的 simulator 設計原則：

> 實驗需要 live parameter tuning，但產品 / 比賽 / 多人環境需要限制狀態竄改。

Physical AI 如果要做可重現 benchmark，也會遇到同樣問題：

- 哪些 physics parameter 可以 runtime randomize？
- 哪些改動算 domain randomization，哪些算 cheating？
- logging 是否足以重現實驗？
- multiplayer / distributed evaluation 是否禁止 client-side physics mutation？

## 可共用的章節定位

我會把這個 repo 放在 Physical AI 筆記的這一章：

```text
Physical AI Stack
├── World：terrain、heightmap、collision object、event box
├── Body：node-beam soft body、wheel、shock、hydro、wing、propeller
├── Contact：ground model、node collision、cab collision、water / buoyancy
├── Control：input events、VehicleAI、Autopilot、hydros、commands
├── Runtime Experiment：AngelScript、message queue、FreeForces、ActorSimAttributes
├── Visualization / Debug：OGRE、flexbody、debug view、force feedback
└── Benchmark Caveat：不是 ROS / Gazebo，缺少現代 sensor / RL / VLA integration
```

它最值得共用的不是整個 engine，而是以下思維模板：

### 1. 身體是 graph，不只是 bounding box

用 node-beam graph 表示可變形 body，能自然描述：

- 懸吊。
- 車架扭曲。
- 繩索。
- support beam。
- 變形與破壞。
- 軟性結構與力傳遞。

### 2. 控制不是單層 action，而是 mode + actuator + physics

一個 aircraft / vehicle control stack 至少有：

- 高階模式：heading、altitude、waypoint、nav。
- 中階控制：steering、throttle、brake、rudder、elevator、ailerons。
- 低階物理：engine、wheel friction、airfoil、beam force、collision。

### 3. 世界狀態需要可觀測、可修改、可記錄

AngelScript API 展示一個實驗平台應該具備：

- 查詢 actor、resource、terrain、input。
- 注入 message。
- 接收 event。
- 修改 parameter。
- 加入外力。
- 讀寫 local storage。
- debug script error。

### 4. 外力與 constraint 是測試 robustness 的好工具

FreeForces / FreeBeams 可以轉成 Physical AI 的 robustness 測試：

- 給機器人施加橫向力，看 navigation 是否穩定。
- 加入拖曳或繩索，看 planner 是否理解連接限制。
- 改變 wheel slip / friction，看 controller 是否適應。
- 在水、坡地、崎嶇地形做 stress test。

## 對現代 Physical AI 的限制

### 1. robotics integration 不完整

RoR 不是 ROS 2 simulator，因此缺少：

- ROS topic / service / action。
- TF tree。
- sensor plugin 生態。
- Nav2 / MoveIt integration。
- 標準 robot description pipeline，例如 URDF / SDF。

### 2. sensor / perception 不是主軸

它有 camera / rendering / dashboard / HUD / OGRE binding，但不是以 RGB-D、LiDAR、IMU、semantic segmentation、synthetic data export 為中心設計。

對 VLA / perception-heavy Physical AI 來說，RoR 比較適合作為 dynamics / contact 案例，不適合作為 perception benchmark。

### 3. learning loop 不是主設計目標

repo 內看到腳本與 autotest scenario，但不是 Gymnasium-style `reset/step/observation/reward/done` 環境。若要拿來做 RL，需要自行包裝：

- episode reset。
- state extraction。
- action injection。
- reward definition。
- deterministic seed / logging。
- batch simulation。
- headless mode。

### 4. 授權與工程成本

主專案是 GPLv3 or later。如果只是學習與研究筆記沒有問題；但若要把程式碼整合到商業 simulator 或閉源產品，必須嚴格評估授權影響。

## 如果要實際改造成 Physical AI 實驗章節

### Phase 1：把它當成閱讀案例

目標不是跑訓練，而是建立物理概念：

- 讀 `doc/doxygen/CodebaseOverview.md`。
- 讀 `ActorForcesEuler.cpp` 的 force loop。
- 讀 `SimData.h` 的 node / beam / actor data structure。
- 讀 `FreeForces.md`、`FreeBeams.md`、`ActorSimAttributes.md`。
- 讀 `VehicleAI.h/.cpp` 與相關 AngelScript binding。

產出：一張 Physical AI body/contact/control stack 圖。

### Phase 2：用腳本做控制實驗

可從內建範例開始：

- `resources/scripts/example_truck_setEventSimulatedValue.as`：對 actor 模擬 accelerator / steering / brake input。
- `resources/scripts/example_autopilot.as`：航空器 autopilot 互動。
- `resources/scripts/example_truck_physics.as`：讀取 / 展示 truck physics。
- `resources/scripts/example_freeforce_halfbeam.as`：FreeForces / FreeBeams 實驗。
- `resources/scripts/example_autotest_scenario.as`：自動測試情境概念。

產出：一組小任務，例如「卡車沿 waypoint 前進，途中被外力推偏，測試是否能回到路線」。

### Phase 3：包一個外部 agent interface

如果真的要接 Physical AI agent，可以考慮：

```text
外部 Python agent
→ command channel / script / local file / network bridge
→ AngelScript message queue
→ actor input / force / waypoint
→ physics simulation
→ state dump / event callback / log
→ Python reward / planner
```

這會比直接改 C++ 低風險，但工程量仍不小。

### Phase 4：只移植概念，不移植 engine

更務實的做法是把 RoR 的概念轉進現代 robotics simulator：

- node-beam graph → deformable / articulated body representation。
- FreeForces → apply wrench / disturbance plugin。
- FreeBeams → temporary constraint / joint。
- waypoint AI → classical navigation baseline。
- ActorSimAttributes → domain randomization parameter API。
- event boxes → task success / trigger zones。
- force feedback → contact / wrench observation。

## 跟 Gazebo / ROS 2 的關係

如果 Gazebo 是 Physical AI 的「標準機器人沙盒」，RoR 更像一個「車輛軟體物理專題教室」。

- Gazebo / ROS 2：適合學 AMR、sensor、navigation、manipulation、robot middleware。
- RoR：適合理解車輛 body deformation、soft-body mechanics、地形接觸、腳本化外力、mod-based physical object design。

兩者不是替代關係，而是互補：

```text
Gazebo / ROS 2：機器人系統工程
Rigs of Rods：軟體車體與接觸物理
```

Physical AI 如果只學 ROS，容易把 body dynamics 想得太乾淨；如果只看 RoR，又會缺少現代 robotics middleware、sensor stack 與 learning benchmark。把它們放在不同章節比較合理。

## 對台灣產業與實體 AI 的延伸

RoR 的案例對台灣 IPC、AMR、車載、工業自動化有幾個啟示：

1. **機器人的差異化不只在模型，也在身體與場域。** 物流車、越野車、工地設備、農機、船舶、特種載具，真正難處常在接觸、振動、載重、地形與可靠性。
2. **Physical AI 需要 domain-specific simulator。** 通用 VLA 不會自動理解輪胎打滑、懸吊、拖車、斜坡、水面、軟性連接。
3. **傳統控制與 learned policy 要共存。** Waypoint AI、autopilot、engine controller、stability aids 是可重用的底層 skill，不該被語言模型取代。
4. **debug / tooling 是護城河。** RoR 長期演化出 script editor、rig editor、shock tool、engine tool、OGRE inspector，這提醒我們 simulator 的價值常在工具鏈，而不只是物理公式。

## 可放進 Physical AI 章節的標題建議

- `Chapter: Soft-body Vehicle Simulation as Embodied Physics`
- `Chapter: Node-Beam Bodies, Terrain Contact, and Scriptable Forces`
- `Chapter: Rigs of Rods — 車輛軟體物理、接觸與可腳本化控制`

我會採用第三個，因為它最清楚說明不是泛泛的遊戲介紹，而是把它放在 Physical AI stack 裡的角色。

## 最終判斷

`RigsOfRods/rigs-of-rods` 對 Physical AI 的價值是「可變形載具與接觸物理的開源案例」，不是「現成機器人 AI 訓練平台」。

可共用的核心是：

- **Body representation**：node / beam / soft-body graph。
- **Dynamics loop**：forces、collision、drag、buoyancy、wheels、shocks、hydros、beams。
- **Contact world**：terrain、ground model、event box、collision object。
- **Control interface**：input events、VehicleAI、Autopilot、message queue。
- **Experiment knobs**：AngelScript、FreeForces、FreeBeams、ActorSimAttributes。
- **Tooling culture**：mod、gadget、editor、debug view。

限制也很明確：缺少 ROS / sensor / RL / VLA 的現代接口，GPLv3 授權也使商業整合需要小心。

因此最好的使用方式是：把它當成 Physical AI 研究筆記裡「身體與接觸」的章節，從中抽出設計模式，再轉譯到 Gazebo、Isaac、MuJoCo 或自建 simulator。
