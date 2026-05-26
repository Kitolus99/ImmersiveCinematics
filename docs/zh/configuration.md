# 配置与命令

本文说明如何构建、运行、放置脚本、使用命令和调整配置。

## 开发命令

在仓库根目录运行：

```powershell
.\gradlew.bat build --stacktrace --no-daemon
```

构建成功后，jar 输出在：

```text
build/libs/immersive_cinematics-0.3.1.jar
```

启动开发客户端：

```powershell
.\gradlew.bat runClient --stacktrace --no-daemon
```

`runClient` 会打开 Minecraft 客户端，不会自动退出。验证完成后手动关闭游戏即可。

## 脚本目录

脚本目录名固定为：

```text
immersive_cinematics/scripts
```

服务端启动时，`ScriptManager` 会尝试把全局目录里的脚本复制到世界目录。

常见位置：

- 全局目录：服务器或游戏运行根目录下的 `immersive_cinematics/scripts/`
- 世界目录：当前世界存档下的 `immersive_cinematics/scripts/`
- 命令也支持绝对路径作为兜底

## 游戏内命令

命令入口为 `/icinematics`。

| 命令 | 说明 |
| --- | --- |
| `/icinematics play <file>` | 播放脚本 |
| `/icinematics stop` | 停止当前播放 |
| `/icinematics status` | 查看播放状态和全局脚本目录 |

`play` 命令会按以下方式搜索：

1. 全局目录里的 `<file>`。
2. 全局目录里的 `<file>.json`。
3. 世界目录里的 `<file>`。
4. 世界目录里的 `<file>.json`。
5. 传入路径本身。

示例：

```mcfunction
/icinematics play example_orbit
/icinematics play example_orbit.json
/icinematics stop
/icinematics status
```

## 默认按键

| 按键 | 作用 |
| --- | --- |
| `F6` | 打开游戏内编辑器 |
| 长按 `C` | 跳过可跳过脚本 |
| `Ctrl + P` | 强制退出当前播放 |

按键文本来自本地化文件：

- `src/main/resources/assets/immersive_cinematics/lang/en_us.json`
- `src/main/resources/assets/immersive_cinematics/lang/zh_cn.json`

## 配置项

配置由 `Config.java` 定义，注册为 Forge common config。

| 配置键 | 默认值 | 说明 |
| --- | --- | --- |
| `skipHoldThresholdMs` | `3000` | 长按跳过键需要持续的毫秒数 |
| `showSkipHud` | `true` | 播放时是否显示跳过提示 |
| `skipVoteRatio` | `100` | 多人跳过投票比例 |
| `debugLogging` | `false` | 是否开启调试日志 |
| `triggerPollInterval_location` | `20` | `location` 触发器轮询间隔 |
| `triggerPollInterval_biome` | `40` | `biome` 触发器轮询间隔 |
| `triggerPollInterval_inventory` | `20` | `inventory` 触发器轮询间隔 |
| `triggerPollInterval_structure` | `20` | `structure` 触发器轮询间隔 |
| `triggerPollInterval_gamestage` | `20` | `gamestage` 触发器轮询间隔 |

## 常见问题

### `/icinematics play` 找不到脚本

检查：

- 文件是否在 `immersive_cinematics/scripts/`。
- 命令是否写了正确文件名。
- 如果省略 `.json`，确认同名 `.json` 文件存在。
- 世界目录和全局目录是否混淆。

### 脚本解析失败

常见原因：

- `version` 不是 `3`。
- `timeline.total_duration` 为 `0`。
- `camera` clip 的 `duration` 为 `0`。
- 关键帧 `time` 没有递增。
- `position_mode` 和坐标字段不匹配。
- JSON 语法错误。

### 脚本没有自动触发

检查：

- 脚本是否在服务器启动时加载。
- `meta.triggers` 是否写在 `meta` 内。
- `type` 是否为已注册触发器。
- `conditions` 字段是否与触发器类型匹配。
- 不可重复触发器是否已经触发过。
- 玩家是否已经在播放同一脚本。

### 中文显示乱码

确认 `zh_cn.json` 使用 UTF-8 保存，且 JSON 语法合法。
