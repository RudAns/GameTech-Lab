# GameTech-Lab

研究游戏开发中可能用到的各种技术栈，并在 Unreal Engine 5.8 中做成可交互的复现 Demo。每个技术对应一个独立的工程仓库，本仓库是它们的总览。

## 工程一览

| 主题 | 工程仓库 | 状态 |
|---|---|---|
| [声音遮挡、阻挡与衍射](#gtl-audio-occlusion声音遮挡阻挡与衍射) | [GTL-Audio-Occlusion](https://github.com/RudAns/GTL-Audio-Occlusion) | 已完成 |
| [3D 场景实时渲染成像素画](#gtl-render-pixelart3d3d-场景实时渲染成像素画) | [GTL-Render-PixelArt3D](https://github.com/RudAns/GTL-Render-PixelArt3D) | 已完成 |
| [《海狸浮生记》的网格水](#gtl-physics-gridwater海狸浮生记的网格水) | [GTL-Physics-GridWater](https://github.com/RudAns/GTL-Physics-GridWater) | 已完成 |
| [《盗贼之海》式的共享海面](#gtl-net-sharedocean盗贼之海式的共享海面) | [GTL-Net-SharedOcean](https://github.com/RudAns/GTL-Net-SharedOcean) | 已完成 |
| [《反恐精英2》式的响应式体积烟雾](#gtl-render-volumetricsmoke反恐精英2式的响应式体积烟雾) | [GTL-Render-VolumetricSmoke](https://github.com/RudAns/GTL-Render-VolumetricSmoke) | 已完成 |
| [《最后生还者 第二部》的物理绳索](#gtl-physics-rope最后生还者-第二部的物理绳索) | [GTL-Physics-Rope](https://github.com/RudAns/GTL-Physics-Rope) | 未完成 |
| [可穿越的体素体积云](#gtl-render-voxelclouds可穿越的体素体积云) | [GTL-Render-VoxelClouds](https://github.com/RudAns/GTL-Render-VoxelClouds) | 未完成 |

---

## GTL-Audio-Occlusion：声音遮挡、阻挡与衍射

**仓库**：[RudAns/GTL-Audio-Occlusion](https://github.com/RudAns/GTL-Audio-Occlusion) · **状态**：已完成

![声音遮挡实验室俯视总览](docs/images/audio-overview.jpg)

**研究什么**：墙后、门外、楼上的声音应当如何衰减、滤波并改变方向。调研比较了 Wwise Spatial Audio、Steam Audio、微软 Project Acoustics、FMOD、CRI ADX 与 UE5 原生方案，以及卡普空 RE ENGINE、《彩虹六号：围攻》、《守望先锋》等作品的公开做法。核心结论是：遮挡问题的关键不在“直线上有没有墙”，而在“声音经哪条路到达”。已上线作品的共识结构是“空间拓扑 → 传播路径 → 少量参数”，射线只用于同一空间内的局部阻挡；衍射损失按路径的超额程差（excess path length）分频段计算，比按拐角角度映射更接近物理，且几乎不增加开销。房间—门户方案可以完整用在手游上。

**Demo 里能做什么**：在一座六个房间的“声屋”里，收音机、机床、座钟、篝火、测试音箱和巡逻者同时发声（需戴耳机，全部走 Steam Audio HRTF 双耳渲染）。

- 左侧面板按真实依赖关系组织：先单选处理方案（不处理、UE 原生、自研管线、GPU 波动烘焙、Steam Audio），再勾选该方案下的子层，从多射线阻挡、房间—门户路径、分频衍射、穿墙透射一路叠加到房间混响耦合与时间平滑。
- 六组 A/B（/C）对照：二值遮挡的跳变、路径是否从正确的门口传来、按角度与按程差两种衍射映射、三种方案级实现的听感与开销。
- 开关门可以听到声音改道；路径、虚拟声源、透射命中点、烘焙探针和声场切片都可以画在场景里，第一人称和俯视视角之间可随时切换。

| 第一人称：路径与透射的分频衰减读数 | GPU 波动烘焙：直达声响度切片与探针 |
|---|---|
| ![第一人称客厅](docs/images/audio-firstperson.jpg) | ![波动烘焙切片](docs/images/audio-wavebake.jpg) |

---

## GTL-Render-PixelArt3D：3D 场景实时渲染成像素画

**仓库**：[RudAns/GTL-Render-PixelArt3D](https://github.com/RudAns/GTL-Render-PixelArt3D) · **状态**：已完成

![像素风墓园 45° 视角总览](docs/images/pixelart-overview.jpg)

**研究什么**：如何让 3D 场景在运动中仍然像手绘像素画一样干净稳定。调研以 Hypersect 工作室在 GDC 2026 上分享的《Never's End》渲染方案为主线：低分辨率渲染加整数倍最近邻放大、相机与物体吸附到像素网格以消除像素爬行（pixel creep）、亚像素补偿、朝向量化与阶跃关键帧，以及最关键的精灵排序（sprite sorting），即让每个物体整体作为一层绘制、而光照仍按真实位置计算，并处理三个物体互相遮挡成环时的切环策略。

**Demo 里能做什么**：操控一名骑士在像素风墓园里走动，场景中六个彩色踏板各对应一项技术，走上去就出现原理说明。

- 左侧“管线开关”逐项控制低分辨率、吸附、排序模式、切环策略、光照位置、描边归属、抖动、toon 色阶、相机跟随方式等十几项技术，另有参数页可以实时调像素尺寸、每米像素数、俯角和光源方向；视角按 45° 一档旋转。
- 运动时的稳定性是重点：相机连续跟随、背景按亚像素平滑滚动，角色和它的影子在放大时单独成层、不随背景偏移，因此角色在屏幕上纹丝不动，背景也不会整格跳；按 J 可以和缓动跟随、锁定像素等做法对比，右上角实时显示角色在屏幕上的抖动。
- 调试视图可显示物体 ID、排序层级、真实深度与推移后深度，以及物体之间的前后关系图；按 M 通过 GPU 回读实测帧间闪烁率；挑战模式会随机关掉一项技术，让玩家猜是哪一项。

| 精灵排序：长矛整体画在墓室墙前 | 三杆成环：互不相交的合并成一层 |
|---|---|
| ![精灵排序](docs/images/pixelart-wallsort.jpg) | ![循环遮挡](docs/images/pixelart-cycle.jpg) |

---

## GTL-Physics-GridWater：《海狸浮生记》的网格水

**仓库**：[RudAns/GTL-Physics-GridWater](https://github.com/RudAns/GTL-Physics-GridWater) · **状态**：已完成

![河谷水利总览](docs/images/gridwater-overview.jpg)

**研究什么**：城建游戏《海狸浮生记》（Timberborn，Mechanistry 开发）如何在大网格上实时模拟可被玩家改造的水系。调研拆解了它的虚拟管道法（virtual pipes）水流、恶水潮污染度的平流与门控扩散、按水深与聚簇大小分档的蒸发、灌溉湿度与土壤污染，以及大坝、防洪闸、自动阀等水利建筑的约束方式；同时指出原作在质量守恒上的缺口，提出半隐式摩擦、质量守恒的污染输运等改进。

**Demo 里能做什么**：一个可交互的河谷水系模拟，季节在温和季与危险季（干旱或恶水潮）之间循环，可以随时改造地形和水利设施，观察水流、污染和灌溉的变化。

- 可以建堤坝、大坝、防洪闸、自动阀、水泵，挖掘或堆土改变地形，水流、污染和灌溉范围会立即响应。相邻的同类建筑自动连成一排，点击即可选中整排，统一调整闸门高度或阀门模式。
- 右侧“原理实验室”中的七项开关可以在原作做法与改进方案之间逐项切换，左下角的水量账本实时显示守恒误差，关掉出流比例缩放后能看到误差持续增长。
- 调试视图可显示水深、流速、污染度、聚簇饱和度、灌溉湿度和土壤污染；季节可以快进，也可以直接跳到下一个危险季。

| 恶水潮：自动阀截断污染，水库保持清洁 | 相连的自动阀整排选中、统一切换模式 |
|---|---|
| ![恶水潮与自动阀](docs/images/gridwater-badtide.jpg) | ![整排选中](docs/images/gridwater-selection.jpg) |

---

## GTL-Net-SharedOcean：《盗贼之海》式的共享海面

**仓库**：[RudAns/GTL-Net-SharedOcean](https://github.com/RudAns/GTL-Net-SharedOcean) · **状态**：已完成

![风暴区的巨浪](docs/images/sharedocean-overview.jpg)

**研究什么**：Rare 的多人航海游戏《盗贼之海》（Sea of Thieves，2018）如何让每个玩家看到同一片起伏的大海、同一条随浪摇晃的船。调研涵盖 Tessendorf FFT 海浪的频谱与级联划分、玩法波段（CPU，服务器与物理都要读取）与画面波段（GPU）的拆分、浮力探针与平面拟合两种浮力模型，以及多人一致性的关键：各端在同步的网络时钟下各自确定性地计算海面，船体由服务器权威模拟，客户端用快照插值并按本地海面重建竖直运动。

**Demo 里能做什么**：一个可联机的航海小游戏，可以单机运行，也可以在同一台机器上并排开主机和客户端对比两端画面。

- 在甲板上走动，走到船尾舵轮旁掌舵，升降帆、转舵，在海岛之间航行、收集漂流宝箱，穿过会移动的风暴区。
- “原理实验台”按“海面与巨浪 / 船体浮力 / 多人一致性”三组分层列出开关：海面分层着色、浮力探针数量与浮力模型、服务器物理频率、海浪时钟模式、船的三种呈现方式、模拟网络延迟等，标明哪些作用于服务器、哪些作用于客户端。
- 网络仪表实时显示往返时间、两端海浪时钟误差与吃水偏差；快照幽灵用线框画出最新快照与插值回放位置之间的差距。

| 浮力探针：每个探针的入水深度与受力 | 客户端：快照幽灵与插值回放位置 |
|---|---|
| ![浮力探针](docs/images/sharedocean-probes.jpg) | ![快照幽灵](docs/images/sharedocean-ghost.jpg) |

---

## GTL-Render-VolumetricSmoke：《反恐精英2》式的响应式体积烟雾

**仓库**：[RudAns/GTL-Render-VolumetricSmoke](https://github.com/RudAns/GTL-Render-VolumetricSmoke) · **状态**：已完成

![走廊里的烟按体积守恒规则延伸](docs/images/volumetricsmoke-overview.jpg)

**研究什么**：Valve 的《反恐精英2》（Counter-Strike 2，2023）的“响应式烟雾”如何按关卡几何扩散、被子弹和高爆手雷临时挖空，并让所有玩家看到同一团烟。调研依据 GameTracking-CS2 导出的类结构、联网字段和反编译的着色器，涵盖每颗烟 32³ 体素的到达距离场、三种扩散规则的取舍、按事件时间在着色器里挖空、低分辨率光线步进与逐体素光照缓存，以及服务器下发体素结果、烟后敌人不发给客户端的竞技公平做法。

**Demo 里能做什么**：一个可联机的第一人称突破关卡，可以单机运行，也可以在同一台机器上并排开主机和客户端。

- 用烟雾弹封住三名哨兵的视线，用高爆手雷清开烟，推进到包点安放炸弹；子弹能在烟里打出可以看穿的通道。
- “原理面板”按“扩散规则 / 交互 / 渲染 / 网络同步 / 公平性”五组分层列出开关：三种扩散规则、体素与热力图显示、步进分辨率、噪声、光照与上采样方式、两种同步方式与注入的关卡差异、烟雾相关性剔除与透视视角，标明哪些作用于服务器、哪些只改本机画面。
- 右上角实时显示各渲染阶段的 GPU 耗时、每颗烟的联网字节数和与服务器不一致的烟雾数量。

| 子弹通道：从枪口打向准星，沿弹道能看穿烟 | 客户端：烟后的哨兵不再发送，透视视角里只剩一名 |
|---|---|
| ![子弹通道](docs/images/volumetricsmoke-tunnel.jpg) | ![相关性剔除](docs/images/volumetricsmoke-cull.jpg) |

---

## GTL-Physics-Rope：《最后生还者 第二部》的物理绳索

**仓库**：[RudAns/GTL-Physics-Rope](https://github.com/RudAns/GTL-Physics-Rope) · **状态**：未完成

**研究什么**：顽皮狗（Naughty Dog）在 GDC 2021 讲座《Rope Simulation in Uncharted 4 and The Last of Us 2》中介绍的两套绳索系统：动态绳用基于位置的动力学（PBD）配合弯曲约束、摩擦约束、多重网格与局部碰撞平面，张紧绳用连续碰撞求出沿凸边滑动的最短路径。调研对照了 PBD/XPBD、长程约束、Cosserat 杆、Cable Joints 以及 UE 和 Source 引擎的现有实现，并给出改进论证与手游分档。

**Demo 里能做什么**：在一座被水淹的废弃庭院里找到三份补给，每一份都要用一种绳索机制拿到：把绳子抛过钢梁再爬上去，荡过积水，以及拖着长度有限的电缆绕过柱子和断墙接到配电箱。

---

## GTL-Render-VoxelClouds：可穿越的体素体积云

**仓库**：[RudAns/GTL-Render-VoxelClouds](https://github.com/RudAns/GTL-Render-VoxelClouds) · **状态**：未完成

**研究什么**：Guerrilla 的 Andrew Schneider 在 SIGGRAPH 2023 讲座《Nubis³: Methods (and madness) to model and render immersive real-time voxel-based clouds》中介绍的《地平线：西之绝境》资料片《燃烧海岸》体积云系统：整片云景存成 8 m 一格的三维体素网格，用噪声提升到约 0.5 m 的细节，光线步进在云外按块压缩的 SDF 跳步，太阳光改查预计算的光照网格，并按 200 m 拆分两档分辨率。调研在 RTX 3060 上复核了这些数字，发现讲座的 BC1 距离编码跨厂商时会越界，并给出保守编码和 BC4 对数映射两种改进。

**Demo 里能做什么**：驾驶一架螺旋桨飞机穿云竞速，按顺序穿过 13 个检查环：钻过隧道云，飞进积云中间的云洞，最后穿进雷暴云的巢穴，在闪电中穿出。原理面板可以把云换成 2.5D 包络（隧道被填实）、关掉 SDF 跳空、切换 SDF 编码和步数上限、换回逐采样的光照步进，或者把同一份数据交给 UE 自带的体积云组件对照。

---

## 许可证

本仓库的文字、图片与模板文件以 [MIT 许可证](LICENSE) 发布。各工程仓库有各自的许可证说明，其中的第三方素材按各自的许可使用。
