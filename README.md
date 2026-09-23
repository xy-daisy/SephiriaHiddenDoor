# Sephiria Hidden Door Highlight（隐藏门高亮）v1.0.0 — 安装说明

把**需要攻击才能打开的隐藏墙**用像素风轮廓标出来，不用再对着墙乱砍试运气。

- **装上就生效，没有快捷键**（刻意不做开关：需要你记得去开的高亮，等于没有）。
- 轮廓是**贴着墙砖描的虚线轮廓**，不规则形状的墙也能贴合；走廊型金色、传送门型青色。
- 单机 / 联机都可用。只读，不改地形、不改战斗、不联网发任何东西。

---

## 一、它标什么

游戏里有两类"打碎后才出现隐藏区域"的墙，机制完全不同，本 mod 都覆盖：

| 类型 | 游戏里的类 | 外观 | 怎么打开 | 高亮色 |
|---|---|---|---|---|
| **走廊型** | `HiddenRoomTriggerCollider` | **完全看不见**，藏在普通墙里 | 近战打 **3 下**（只吃直接攻击，单次伤害不足 20 会被抬到 20，50 血） | 金色 `FFD23F` |
| **传送门型** | `BreakableProp_HiddenPortal`（入口侧） | 有贴图的实体墙 | 每次固定 1 点伤害，打碎后出现可交互的**传送门** | 青色 `4FD6FF` |

- 打碎后墙上的标记**自动消失**（对象被销毁 / `IsBroken` 翻转，下次扫描就没了）。
- **装饰性可破坏物不会被误标**：游戏用同一个类生成石像之类的可破坏景物，本 mod 只认被游戏 `Connect()` 配对过的真入口（判据是 `isConnected`），所以雕像、罐子不会有框。
- 第五章的剧情裂缝（`C5CrackProp`）也支持，但**默认关闭**——它是任务道具，不是隐藏区入口。

---

## 二、前置条件：需要 BepInEx 6

本 mod 是 BepInEx 插件，**必须先装好 BepInEx 6（Unity Mono / x64）**。

判断方法：打开游戏根目录（Steam 里右键 Sephiria → 管理 → 浏览本地文件），同时看到下面这些就说明装好了，直接跳到第三节：

```text
Sephiria.exe
winhttp.dll
doorstop_config.ini
BepInEx/
```

> 装过 **Sephiria Together**（联机 mod）的 `with-BepInEx` 整合包的话，BepInEx 已经自带，不用再装。

没装的话：从 BepInEx 官方仓库取 **v6 的 Unity Mono x64 构建**（本游戏用的是 `BepInEx 6.0.0-be.697`，Unity 6000.3.x），把压缩包内容**直接解压到游戏根目录**（`winhttp.dll` 要和 `Sephiria.exe` 在同一层），**先启动一次游戏**让 BepInEx 生成自己的目录和缓存，退出后再装本 mod。

> ⚠️ **BepInEx 5 用不了本 mod**。判断方法：看 `BepInEx/core/` 里有没有 `BepInEx.Core.dll`——有就是 v6，只有 `BepInEx.dll` 就是 v5。放错版本的表现是**静默不加载**（日志里一个字都没有）。

---

## 三、安装

1. 在 Steam 中右键 **Sephiria** → **管理** → **浏览本地文件**，打开游戏根目录。
2. 把本压缩包里的 **`BepInEx` 文件夹整个拖进游戏根目录**，和已有的 `BepInEx` 合并。
3. 确认最终路径长这样（`Sephiria` 是游戏根目录）：

```text
Sephiria/
└─ BepInEx/
   ├─ plugins/
   │  └─ SephiriaHiddenDoor.dll          ← 本体
   └─ config/
      └─ com.sephiria.hiddendoor.cfg     ← 配置（可选，首次启动会自动生成）
```

4. 启动游戏。**第一次进城镇时会弹一条「隐藏高亮生效」**——看到它就说明 mod 在跑了；之后本次游戏不再弹。
5. 进关卡找到带裂纹的墙，应该能看到贴着墙爬行的虚线轮廓。

> **不要把整个 zip 放进 `BepInEx/plugins`**，也不要放进 `Sephiria_Data`。
> 只要 `SephiriaHiddenDoor.dll` 这一个文件在 `BepInEx/plugins/` 下就行。

配置文件是可选的：不放也会在首次启动时自动生成一份带注释的默认值。
**注意：BepInEx 不会覆盖 cfg 里已经存在的值**——升级后想拿新默认值，就删掉旧 cfg（或只留你要改的那几项）再启动一次。

---

## 四、卸载

删掉这一个文件即可，游戏立刻恢复原样：

```text
Sephiria/BepInEx/plugins/SephiriaHiddenDoor.dll
```

配置文件 `Sephiria/BepInEx/config/com.sephiria.hiddendoor.cfg` 留着无害，想彻底清理就一起删。

---

## 五、常用配置

配置文件：`Sephiria/BepInEx/config/com.sephiria.hiddendoor.cfg`
（用记事本/VSCode 打开，改完**重启游戏**生效；**游戏运行时改会被覆盖回内存里的旧值**）

| 分区 | 配置项 | 默认 | 作用 |
|---|---|---|---|
| General | `Enabled` | `true` | 总开关（默认开，装了就用） |
| General | `ScanIntervalSeconds` | `0.5` | 多久扫一次场景找隐藏门（秒）。楼层生成后才会出现门，不用太快 |
| General | `MaxDistance` | `0` | 只标玩家周围这么远的门；`0` = 不限（本层所有门都标，包括你还没去的房间） |
| General | `ShowNotice` | `true` | 首次进城镇弹一条「隐藏高亮生效」 |
| General | `HeartbeatSeconds` | `0` | 每多少秒往日志打一行状态；`0` 关闭 |
| General | `DebugLog` | `false` | 详细日志（含选墙诊断）。排错时才开 |
| Targets | `HighlightCorridorWalls` | `true` | 标走廊型隐藏墙 |
| Targets | `HighlightPortalWalls` | `true` | 标传送门型隐藏墙 |
| Targets | `HighlightCrackProps` | `false` | 标第五章剧情裂缝（任务道具，默认关） |
| Targets | `FitCorridorFrameToWall` | `true` | 走廊型改成**贴墙砖描轮廓**（而不是框住触发器那片区域）。只读。找不到墙砖时回退成矩形 |
| Appearance | `PixelsPerUnit` | `12` | 一个世界单位画多少个像素块。**这就是像素风的来源**：越小越粗犷 |
| Appearance | `BorderThickness` | `2` | 轮廓粗细（像素） |
| Appearance | `DashPeriod` | `4` | 虚线段长度（像素），也是动画帧数 |
| Appearance | `AnimationFps` | `8` | 虚线爬行速度，故意做成 8-bit 那种顿挫感 |
| Appearance | `Padding` | `0.12` | 轮廓外扩多少世界单位 |
| Appearance | `Outline` / `OutlineColor` | `true` / `0A0A12` | 1 像素深色描边，保证亮墙上也能看清 |
| Appearance | `Icon` | `false` | 墙上方的像素「!」图标，默认关（太抢眼） |
| Appearance | `SortingLayerName` | `FX` | 强制轮廓画在这个排序层。**别改空**：层优先级压过 sorting order，画在 `Default` 层会被屋顶/岩壁盖掉一半 |
| Colors | `CorridorColor` / `PortalColor` / `CrackColor` | `FFD23F` / `4FD6FF` / `FF6B6B` | 三类目标的 `RRGGBB` 颜色 |
| Passage | `ProbeAfterOpening` | `false` | 走廊墙被打破后，把门口瓦片/碰撞体/角色碰撞体尺寸打进日志。只读，排「打不开、进不去」时用 |

---

## 六、常见调法

- **框太扎眼** → `Icon = false`（已默认）、`BorderThickness = 1`、`AnimationFps = 4`、`CorridorColor` 调暗一点。
- **框看不清 / 被地形盖住一半** → 确认 `SortingLayerName = FX`；还不行就把 `DebugLog = true` 重启一次，日志里 `tilemap sorting:` 那行会列出本层所有瓦片地图用的层，照着挑一个更高的。
- **轮廓位置不对**（框在空地上 / 圈进黑暗里）→ `DebugLog = true` 重启一次，把日志里 `wall pick: ... chose=...` 那行发我（`chose=` 后面是被描边的格子坐标）。
- **只想看附近的门** → `MaxDistance = 30`（一个房间大约 26 × 18 单位）。
- **完全静默** → `ShowNotice = false`。

---

## 七、排错

**完全没反应 / 没有任何框**
- 确认 `SephiriaHiddenDoor.dll` 在 `BepInEx/plugins/`，不在子文件夹里。
- 看 `Sephiria/BepInEx/LogOutput.log`，启动成功会有这样一行：
  ```text
  [Sephiria Hidden Door Highlight] v1.0.0 loaded. corridor=on, portal=on, crack=off, ppu=12, enabled=on
  ```
- 进楼层后日志会有 `hidden doors in scene: corridor=N, portal=N`，`N` 是本层扫到的墙数。全是 0 说明这层本来就没有隐藏墙（正常），也可能目标判定没命中——把那行发我。
- 日志里一个字都没有 → 大概率装的是 BepInEx 5，见第二节。

**进不去打开的隐藏门**
先怀疑**房间里的怪没清完**（游戏机制：未清场时通道不通行），不是 mod 也不是 bug——本 mod 从不碰地形和战斗状态。
确实怀疑是地形问题时，把 `Passage / ProbeAfterOpening` 改成 `true` 重启，打碎一面墙后把日志里 `passage opened:` / `blockers in the doorway:` 几段发我。

**游戏内报错 / 崩溃**
本 mod 的日志走 BepInEx（`Sephiria/BepInEx/LogOutput.log`），
但 Unity 自己的异常在 Player.log：
`C:\Users\<你的用户名>\AppData\LocalLow\TEAMHORAY\Sephiria\Player.log`

---

## 八、English (quick version)

**What it does:** outlines the breakable walls that unlock hidden areas, in a hard-edged pixel-art
style (dashed contour that traces the actual wall tiles, so irregular walls are traced instead of
boxed). Two kinds are covered: the invisible **corridor** wall (`HiddenRoomTriggerCollider`, three
melee hits, gold) and the **portal** wall (`BreakableProp_HiddenPortal` entrance side, becomes a
teleport portal, cyan). Decorative breakables of the same class are skipped — only walls the game
actually connected to a hidden room are marked. It is on as soon as it is installed; there is
deliberately no hotkey. Read-only: it never writes tiles and never touches combat state.

**Requirements:** BepInEx 6 (Unity Mono, x64) must already be installed in the game folder.
BepInEx 5 will not load it (check for `BepInEx/core/BepInEx.Core.dll`, which means v6).

**Install:** drag the `BepInEx` folder from this archive into the game root directory so the file
ends up here:

```text
Sephiria/BepInEx/plugins/SephiriaHiddenDoor.dll
```

Do not put the whole ZIP inside `BepInEx/plugins`, and do not put anything into `Sephiria_Data`.
Restart the game; the first time you are in town you get a one-line notice confirming it is running.

**Uninstall:** delete `SephiriaHiddenDoor.dll`.

**Config:** `Sephiria/BepInEx/config/com.sephiria.hiddendoor.cfg` (auto-generated on first launch if
absent). Edit, then restart the game — and edit only while the game is closed, because BepInEx
writes the in-memory values back on exit. BepInEx does not overwrite values that already exist in
the file, so delete the cfg after an upgrade to pick up new defaults.

Key knobs: `PixelsPerUnit` (chunkiness of the pixel look), `BorderThickness`, `DashPeriod` /
`AnimationFps` (dash pattern and its crawl speed), `CorridorColor` / `PortalColor`,
`MaxDistance` (only mark doors near you), `ShowNotice` (the one-time town notice).
Keep `SortingLayerName = FX`: sorting layer beats sorting order, so a frame drawn on an earlier
layer gets covered by roof and cliff tiles.

**If the outline is on the wrong wall:** set `DebugLog = true`, restart, and send the
`wall pick: ... chose=...` line from `BepInEx/LogOutput.log`.
