# GameTech-Lab

研究及复现游戏开发中可能用到的各种技术栈。

本仓库是一个**索引仓库**：它不包含任何 UE5 工程本体，只负责记录"有哪些技术研究子工程、它们在哪、研究到了什么程度"，并提供子工程可直接复用的 Git 配置模板。每个 UE5 工程都是独立的 Git 仓库，各自管理自己的 LFS 资产与历史。

## 子工程索引

| 子工程 | 领域 | 研究内容 | 引擎版本 | 状态 | 仓库 |
|---|---|---|---|---|---|
| GTL-Render-PixelArt3D | Render | 3D 模型渲染成像素画（Never's End 技法）：低分辨率与吸附、精灵排序、真实位置 toon 光照、描边，做成可交互演示小游戏 | 5.8 | 进行中 | [链接](https://github.com/RudAns/GTL-Render-PixelArt3D) |

状态取值：`规划中` / `进行中` / `已完成` / `已归档`。

登记方式见 [docs/project-convention.md](docs/project-convention.md#在索引中登记)。

## 目录结构

```
GameTech-Lab/
├─ README.md                  本文件，含子工程索引表
├─ docs/
│  └─ project-convention.md   子工程目录约定、登记流程、为何不用 submodule
├─ templates/
│  └─ ue5/
│     ├─ .gitignore           UE5 工程通用忽略规则
│     └─ .gitattributes       UE5 工程 Git LFS 规则
└─ Projects/                  本地克隆子工程的位置（内容不进本仓库）
```

## 命名约定

**子工程仓库名**：`GTL-<领域>-<主题>`，主题用 PascalCase，例如：

- `GTL-Render-VirtualShadowMap`
- `GTL-Anim-MotionMatching`
- `GTL-Net-RollbackNetcode`

**领域取值**（按需扩充，扩充时同步更新此表）：

| 领域 | 含义 |
|---|---|
| `Render` | 渲染、材质、光照、后处理 |
| `Anim` | 动画、骨骼、IK、动作匹配 |
| `Physics` | 物理、碰撞、破碎、布料 |
| `AI` | 行为树、State Tree、寻路、感知 |
| `Gameplay` | GAS、战斗、交互、相机 |
| `Net` | 网络同步、预测、回滚 |
| `PCG` | 程序化生成、World Partition |
| `UI` | UMG、Common UI、HUD |
| `Audio` | MetaSounds、空间音频 |
| `Tools` | 编辑器扩展、管线、自动化 |

**本地文件夹名**与仓库名一致，放在 `Projects/` 下，例如 `Projects/GTL-Anim-MotionMatching/`。

**`.uproject` 文件名**去掉 `GTL-` 前缀并去掉连字符，例如 `AnimMotionMatching.uproject`（UE 模块名不能含连字符）。

## 新建子工程（简要）

1. 在 GitHub 创建 `RudAns/GTL-<领域>-<主题>` 仓库。
2. 克隆到 `Projects/` 下，把 `templates/ue5/` 里的 `.gitignore` 和 `.gitattributes` 复制到工程根目录，执行 `git lfs install`。
3. 在 UE 中创建工程，首次提交。
4. 回到本仓库，在上方索引表加一行并提交。

完整步骤见 [docs/project-convention.md](docs/project-convention.md)。
