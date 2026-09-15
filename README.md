# robot-arm —— Seeed reBot DevArm B601-RS 功能开发

在 Seeed reBot DevArm **B601-RS** 六轴机械臂上做功能开发：多人协作、单仓库、每人本地用 Claude Code 作为开发助手。

> 生效日期：2026-09-15。仓库：https://github.com/relativequntum/robot-arm （private，单 `main` 分支）。

## 硬件简介

| 项目 | 参数 |
|---|---|
| 型号 | Seeed reBot DevArm B601-RS（RobStride 电机版） |
| 自由度 | 6 DOF + 1 夹爪 |
| 负载 | 2.5 kg |
| 最大臂展 | 754 mm |
| 重复定位精度 | < 0.2 mm |
| 重量 | 约 6.7 kg |
| 通信总线 | CAN，1 Mbps（Linux socketcan，默认通道 `can0`） |
| 供电 | DC 48V |

以上仅摘录官方文档明确写出的参数。更多参数、软件栈、CAN 初始化命令、完整链接与官方文档未覆盖的存疑项，见 [`docs/硬件/官方文档索引.md`](docs/硬件/官方文档索引.md)。

## 官方资源入口

| 资源 | 链接 |
|---|---|
| 官方仓库 reBot-DevArm | https://github.com/Seeed-Projects/reBot-DevArm |
| wiki 快速上手（B601-RS） | https://wiki.seeedstudio.com/rebot_b601_rs_getting_started/ |
| URDF / meshes（RS） | https://github.com/Seeed-Projects/reBot-DevArm/tree/main/Rebot_Arm_description/RS |
| 底层 SDK MotorBridge | https://motorbridge.seeedstudio.com |
| Python 控制库 reBotArm_control_py | https://github.com/Seeed-Projects/reBotArm_control_py |
| ROS 2 工作区（可选路线） | https://github.com/Seeed-Projects/reBotArmController_ROS2 |

## 协作方式

本项目用 **issue 驱动 + 看板 + CLAUDE.md** 三件套协作：

- **issue 三类型**：跨模块口径对齐开 `[会签]`，契约变更与纪律解释开 `[裁决]`（总管拍板），总管下发执行事项开 `[任务]`；一律用 `.github/ISSUE_TEMPLATE/` 的模板开，并打模块标签 + 类型标签 + 指派到人。
- **issue 是唯一存档地**：讨论与结论都留在 issue 线程里，群聊里聊出的共识必须由提出方搬回 issue；写在文档里的会签请求不算发出。
- **看板自动生成、禁止手改**：[`docs/看板.md`](docs/看板.md) 由 GitHub Actions 机器人在 issue / 评论变动后重建并提交回 main，每次开工 `git pull` 后先看「按人待办」里自己登录名下的条目。读看板走 git，不需要任何额外客户端；gh CLI 仍需全员各自安装并授权一次。
- **`CLAUDE.md` 是行为宪法**：仓库根目录的 [`CLAUDE.md`](CLAUDE.md) 被每位成员的 Claude Code 会话自动加载，规则以它为准。
- **新成员接入**：建一个空文件夹、打开 Claude Code、粘贴属于自己的那段启动文本——见 [`docs/协作者交接文本.md`](docs/协作者交接文本.md)。
- **结论回填台账**：会签 / 裁决关闭后把一句话结论回填 [`docs/开放问题结论台账.md`](docs/开放问题结论台账.md)（任务类不回填）。

仓库为 private，单 `main` 分支，不建长期分支。

> 后续按需可再引入协作模式模板第三节的两个可选机制：showcase 成果展示页（每模块一条演示命令跑共享样例，全队互见进展）与数据 / 耗材需求清单（清单进 git、数据不进 git）；规则来源见 [`gh-issue协同开发模式.md`](gh-issue协同开发模式.md)。

## 本地环境要求

- **Python 3.10+**（官方 motorbridge SDK 要求 Python ≥ 3.10）；ROS 2（Humble / Jazzy）为可选路线。
- **硬件控制必须在 Linux 上运行**：Ubuntu / Jetson，经 socketcan（`can0`，1 Mbps）与 CAN-USB 转换板连接机械臂。Windows 只用于开发与写文档，跑实机要到 Linux 机器上。
- 首次接入执行 `git config --global pull.rebase true`；安装 GitHub CLI 并授权一次——**安装与授权的完整步骤只在 `CLAUDE.md`「gh CLI —— 全员标准配置」一节写全**（要点：`gh auth login` 是交互式的，由本人在系统终端执行，或在 Claude Code 输入框以 `!` 前缀运行）。
- 数据目录：`share-data/` 进 git（小体量样例、配置、标定结果）；`local-data/` 不进 git（数据集、录像、模型权重、日志、大 STEP / mesh）。

## 目录

| 路径 | 说明 |
|---|---|
| `CLAUDE.md` | 开发规范与协作红线（最高优先级） |
| `docs/看板.md` | 开放问题看板（机器人自动生成，禁止手改） |
| `docs/开放问题结论台账.md` | 会签/裁决结论台账 |
| `docs/协作者交接文本.md` | 每人一段的接入启动文本 |
| `docs/硬件/官方文档索引.md` | 官方硬件与软件文档索引 |
| `.github/` | issue 模板与看板 workflow |
| `gh-issue协同开发模式.md` | 协作模式的规则来源（跨项目方法论模板，原文保留；示例标签来自他项目，不适用本项目；凡与 `CLAUDE.md` 冲突处以 `CLAUDE.md` 为准） |
| `share-data/` | 进 git 的共享数据（小体量样例、配置、标定结果） |
| `local-data/` | 不进 git 的本地数据（数据集、录像、模型权重、日志、大 STEP / mesh），首次用到时自建 |
| `.gitignore` / `.gitattributes` / `.env.example` | 忽略规则、换行与文本属性、密钥键名模板 |

> 模块划分与 `src/` 目录结构由首条 `[裁决]` issue 确定后回填。
