# ImmersiveCinematics 中文 Wiki

这是 `Kitolus99/ImmersiveCinematics` 的仓库内中文 Wiki。内容基于本地源码、根目录文档和 DeepWiki 对仓库的结构化分析整理，目标是让脚本作者、整合包作者和后续维护者能快速理解这个项目。

## 项目定位

ImmersiveCinematics 是一个 Minecraft Forge 1.20.1 过场动画模组。它不是单纯的相机工具，而是一个轻量叙事引擎：

- 用 JSON 脚本定义镜头、黑边、音频、事件和模组事件。
- 用服务端触发器决定脚本何时播放。
- 用客户端播放引擎按渲染帧驱动相机和 Overlay。
- 用游戏内编辑器降低脚本创作门槛。

## 推荐阅读顺序

1. [配置与命令](configuration.md)：先确认如何启动、构建、放置脚本和手动播放。
2. [脚本格式](script-format.md)：理解 `meta`、`timeline`、`tracks` 和 clip。
3. [触发系统](triggers.md)：理解自动播放的条件与服务端流程。
4. [播放引擎](playback-engine.md)：理解客户端如何真正控制相机和 Overlay。
5. [游戏内编辑器](editor.md)：理解编辑器数据模型和 JSON 生成方式。
6. [架构说明](architecture.md)：从维护者角度看模块边界和调用链。

## 核心目录

| 路径 | 作用 |
| --- | --- |
| `src/main/java/com/immersivecinematics/immersive_cinematics/script/` | 脚本数据结构、解析器、播放器和轨道播放器 |
| `src/main/java/com/immersivecinematics/immersive_cinematics/camera/` | 相机状态、相机路径和相机管理器 |
| `src/main/java/com/immersivecinematics/immersive_cinematics/trigger/` | 触发器、服务端状态、网络包和评估器 |
| `src/main/java/com/immersivecinematics/immersive_cinematics/editor/` | 游戏内编辑器 |
| `src/main/java/com/immersivecinematics/immersive_cinematics/overlay/` | 电影黑边和 Overlay 调度 |
| `cinematics/` | 示例脚本和触发器测试脚本 |
| `docs/zh/` | 中文 Wiki |

## 常用入口

- `README_CN.md`：中文快速介绍。
- `SCRIPT_FORMAT.md`：脚本字段速查。
- `TRIGGER_TYPES.md`：触发器字段速查。
- `MOD_DESIGN.md`：原始设计文档，目前包含较多历史内容。
- `build.gradle`：ForgeGradle 构建配置。

## 版本信息

| 项目 | 版本 |
| --- | --- |
| Mod | 0.3.1 |
| Minecraft | 1.20.1 |
| Forge | 47.x |
| 脚本格式 | v3 |

## 当前状态提示

- `runClient` 和 `build` 已在本地验证通过。
- 当前构建产物为 `build/libs/immersive_cinematics-0.3.1.jar`。
- `audio`、`event`、`mod_event` 已有数据结构；具体播放/分发能力请以源码实现为准，不要只看字段存在就假设功能完整。
