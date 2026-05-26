# ImmersiveCinematics

给 Minecraft 整合包、冒险地图和服务器剧情添加可编排的过场动画。你可以手写 JSON 脚本，也可以在游戏内用时间轴编辑器创建镜头。

[English Documentation](README.md) | [中文 Wiki](docs/zh/index.md) | [脚本格式](SCRIPT_FORMAT.md) | [触发器参考](TRIGGER_TYPES.md)

> 本仓库是 `SanZiNEO/ImmersiveCinematics` 的 fork，当前中文资料基于仓库源码与 DeepWiki 分析整理。

## 这是什么

ImmersiveCinematics 是一个 Minecraft Forge 1.20.1 模组。它把“何时播放”和“如何播放”分开：

- 服务端触发系统监听登录、位置、进度、生物群系、维度切换、击杀、交互、合成、使用物品、物品栏、结构、游戏阶段、自定义事件等条件。
- 客户端播放引擎解析 JSON 脚本，并驱动相机、黑边、音频、事件轨道等时间轴内容。
- 游戏内编辑器提供可视化时间轴、预览区、属性面板和触发器面板，适合不想手写完整 JSON 的作者。

## 适用场景

- 玩家进入新区域时自动播放入场镜头。
- Boss 战前播放剧情展示或场景巡视。
- 解谜房间使用固定机位或轨道镜头。
- 服务器新手引导、地图导览、建筑展示。
- 和命令、其它模组事件结合，做更完整的叙事流程。

## 功能概览

- 6 自由度相机：位置、yaw、pitch、roll、FOV、zoom。
- 关键帧动画、相对/绝对坐标、循环、无限时长。
- 多轨道时间轴：`camera`、`letterbox`、`audio`、`event`、`mod_event`。
- 电影黑边，支持宽高比、淡入、淡出。
- 服务端触发器，支持事件驱动和轮询驱动两类流程。
- 运行时行为控制：屏蔽输入、隐藏 HUD/手臂、抑制视角晃动、可跳过、可打断、末帧保持等。
- 游戏内编辑器：默认按 `F6` 打开，默认长按 `C` 跳过可跳过脚本。

## 快速开始

1. 安装 Minecraft Forge 1.20.1（47.x）。
2. 将构建出的 jar 放入 `.minecraft/mods/`。
3. 将 `.json` 过场脚本放到 `immersive_cinematics/scripts/`。
4. 进入游戏后使用命令播放：

```mcfunction
/icinematics play example_orbit
/icinematics stop
/icinematics status
```

脚本搜索顺序见 [配置与命令](docs/zh/configuration.md)：全局目录、世界目录、绝对路径。

## 开发验证

```powershell
.\gradlew.bat build --stacktrace --no-daemon
.\gradlew.bat runClient --stacktrace --no-daemon
```

`runClient` 会启动 Minecraft 开发客户端，不会自动退出；看到客户端正常进入后，手动关闭即可。

当前已验证构建产物：

```text
build/libs/immersive_cinematics-0.3.1.jar
```

## 阅读入口

- [中文 Wiki 总览](docs/zh/index.md)
- [架构说明](docs/zh/architecture.md)
- [脚本格式](docs/zh/script-format.md)
- [触发系统](docs/zh/triggers.md)
- [播放引擎](docs/zh/playback-engine.md)
- [游戏内编辑器](docs/zh/editor.md)
- [配置与命令](docs/zh/configuration.md)

## 版本

| 项目 | 版本 |
| --- | --- |
| Mod | 0.3.1 |
| Minecraft | 1.20.1 |
| Forge | 47.x |
| 脚本格式 | v3 |

## 许可证

MIT License
