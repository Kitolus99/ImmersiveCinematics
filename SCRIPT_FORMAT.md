# ImmersiveCinematics 脚本格式参考

本文面向脚本作者，说明 `.json` 过场脚本的结构、字段和常见写法。更完整的中文说明见 [docs/zh/script-format.md](docs/zh/script-format.md)。

## 根结构

```json
{
  "meta": {},
  "timeline": {}
}
```

## `meta`

`meta` 描述脚本身份、运行时行为和触发器。

| 字段 | 类型 | 必填 | 默认值 | 说明 |
| --- | --- | --- | --- | --- |
| `id` | string | 是 | 无 | 脚本唯一 ID，只允许 `[a-zA-Z0-9_]`，最长 32 字符 |
| `name` | string | 是 | 无 | 显示名称，最长 50 字符 |
| `author` | string | 是 | 无 | 作者名，最长 30 字符 |
| `version` | int | 是 | 无 | 当前固定为 `3` |
| `description` | string | 否 | `""` | 脚本描述 |
| `dimension` | string | 否 | `null` | 预留维度限制字段 |
| `triggers` | array | 否 | `[]` | 自动触发条件 |

### 运行时行为

| 字段 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| `block_keyboard` | boolean | `true` | 播放期间屏蔽键盘输入 |
| `block_mouse` | boolean | `true` | 播放期间屏蔽鼠标输入 |
| `block_mob_ai` | boolean | `false` | 预留/兼容字段 |
| `hide_hud` | boolean | `true` | 隐藏 HUD |
| `hide_arm` | boolean | `true` | 隐藏第一人称手臂 |
| `suppress_bob` | boolean | `true` | 抑制视角晃动 |
| `hide_chat` | boolean/null | `null` | `null` 表示跟随 `hide_hud` |
| `hide_scoreboard` | boolean/null | `null` | 同上 |
| `hide_action_bar` | boolean/null | `null` | 同上 |
| `hide_title` | boolean/null | `null` | 同上 |
| `hide_subtitles` | boolean/null | `null` | 同上 |
| `hide_hotbar` | boolean/null | `null` | 同上 |
| `hide_crosshair` | boolean/null | `null` | 同上 |
| `render_player_model` | boolean | `true` | 是否渲染玩家模型 |
| `pause_when_game_paused` | boolean | `true` | 游戏暂停时是否冻结脚本时间 |
| `interruptible` | boolean | `true` | 是否允许被其它脚本打断 |
| `skippable` | boolean | `true` | 是否允许玩家长按跳过键提前结束 |
| `hold_at_end` | boolean | `false` | 播放结束后是否停留在最后一帧 |

### 触发器

```json
"triggers": [
  {
    "id": "on_login",
    "type": "login",
    "conditions": {},
    "repeatable": false,
    "delay": 1.5
  }
]
```

| 字段 | 类型 | 必填 | 默认值 | 说明 |
| --- | --- | --- | --- | --- |
| `id` | string | 否 | 当前解析器不使用 | 建议保留，方便编辑器和人工阅读 |
| `type` | string | 是 | 无 | 触发器类型 |
| `conditions` | object | 否 | `{}` | 类型对应的条件 |
| `repeatable` | boolean | 否 | `false` | 是否可重复触发 |
| `delay` | number | 否 | `0` | 条件满足后延迟执行秒数 |

完整触发器字段见 [TRIGGER_TYPES.md](TRIGGER_TYPES.md)。

## `timeline`

```json
"timeline": {
  "total_duration": 30.0,
  "tracks": []
}
```

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `total_duration` | float | 是 | 总时长，正数为有限时长，负数为无限时长，不能为 0 |
| `tracks` | array | 是 | 轨道数组 |

## 轨道类型

轨道写法为：

```json
{
  "type": "CAMERA",
  "clips": []
}
```

解析器大小写不敏感，`"camera"` 和 `"CAMERA"` 都可以。

| 类型 | 说明 |
| --- | --- |
| `CAMERA` | 相机位置、朝向和光学参数 |
| `LETTERBOX` | 电影黑边 |
| `AUDIO` | 音频片段数据 |
| `EVENT` | 服务端命令事件数据 |
| `MOD_EVENT` | 第三方模组事件数据 |

## Camera clip

| 字段 | 类型 | 必填 | 默认值 | 说明 |
| --- | --- | --- | --- | --- |
| `start_time` | float | 是 | 无 | 全局时间轴起点，单位秒 |
| `duration` | float | 是 | 无 | 持续时间，正数有限，负数无限，不能为 0 |
| `transition` | string | 否 | `cut` | `cut` 或 `morph` |
| `transition_duration` | float | 否 | `0.5` | `morph` 过渡时长 |
| `interpolation` | string | 否 | `linear` | `linear`、`smooth` 等枚举 |
| `position_mode` | string | 否 | `relative` | `relative` 或 `absolute` |
| `loop` | boolean | 否 | `false` | 是否循环 |
| `loop_count` | int | 否 | `-1` | `-1` 表示无限循环 |
| `curve` | object | 否 | `null` | 贝塞尔路径配置 |
| `keyframes` | array | 是 | 无 | 至少 1 个关键帧，时间必须递增 |

### 关键帧

| 字段 | 类型 | 必填 | 默认值 | 说明 |
| --- | --- | --- | --- | --- |
| `time` | float | 是 | 无 | clip 内时间偏移，不能为负 |
| `position` | object | 是 | 无 | 坐标 |
| `yaw` | float | 是 | 无 | 偏航角 |
| `pitch` | float | 是 | 无 | 俯仰角 |
| `roll` | float | 是 | 无 | 翻滚角 |
| `fov` | float | 是 | 无 | 视场角 |
| `zoom` | float | 否 | `1.0` | 缩放倍率 |
| `dof` | float | 否 | `0.0` | 景深强度，当前预留 |

相对坐标：

```json
"position_mode": "relative",
"position": { "dx": 5, "dy": 2, "dz": 3 }
```

绝对坐标：

```json
"position_mode": "absolute",
"position": { "x": 100, "y": 64, "z": 200 }
```

### 贝塞尔曲线

```json
"curve": {
  "type": "bezier",
  "control_points": [
    { "x": 10, "y": 1.5, "z": 3 },
    { "x": 0, "y": 2.0, "z": -2 }
  ]
}
```

`control_points` 必须恰好包含 2 个点。

## Letterbox clip

| 字段 | 类型 | 必填 | 默认值 | 说明 |
| --- | --- | --- | --- | --- |
| `start_time` | float | 是 | 无 | 起始时间 |
| `duration` | float | 是 | 无 | 持续时间 |
| `enabled` | boolean | 否 | `true` | 是否启用 |
| `aspect_ratio` | float | 否 | `2.35` | 目标宽高比 |
| `fade_in` | float | 否 | `0.5` | 淡入时间 |
| `fade_out` | float | 否 | `0.5` | 淡出时间 |

## Audio clip

| 字段 | 类型 | 必填 | 默认值 | 说明 |
| --- | --- | --- | --- | --- |
| `start_time` | float | 是 | 无 | 起始时间 |
| `duration` | float | 是 | 无 | 持续时间 |
| `sound` | string | 是 | 无 | 声音 ID，如 `minecraft:music.game` |
| `volume` | float | 否 | `1.0` | 音量 |
| `pitch` | float | 否 | `1.0` | 音调 |
| `loop` | boolean | 否 | `false` | 是否循环 |
| `fade_in` | float | 否 | `0.0` | 淡入 |
| `fade_out` | float | 否 | `0.0` | 淡出 |

## Event clip

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `start_time` | float | 是 | 起始时间 |
| `duration` | float | 是 | 事件片段持续时间，通常为 `0` |
| `event_type` | string | 是 | 通常为 `command` |
| `command` | string | 是 | 要执行的命令，如 `/weather clear` |

## ModEvent clip

| 字段 | 类型 | 必填 | 默认值 | 说明 |
| --- | --- | --- | --- | --- |
| `start_time` | float | 是 | 无 | 起始时间 |
| `duration` | float | 是 | 无 | 持续时间 |
| `event_type` | string | 是 | 无 | 第三方事件 ID |
| `data` | object | 否 | `{}` | 任意自定义数据 |

## 最小示例

```json
{
  "meta": {
    "id": "intro",
    "name": "入场镜头",
    "author": "MapAuthor",
    "version": 3,
    "description": "玩家登录后播放的短镜头",
    "block_keyboard": true,
    "block_mouse": true,
    "hide_hud": true,
    "hide_arm": true,
    "suppress_bob": true,
    "pause_when_game_paused": true,
    "interruptible": true,
    "skippable": true,
    "hold_at_end": false,
    "triggers": [
      {
        "id": "on_login",
        "type": "login",
        "repeatable": true,
        "delay": 1.0,
        "conditions": {}
      }
    ]
  },
  "timeline": {
    "total_duration": 6.0,
    "tracks": [
      {
        "type": "CAMERA",
        "clips": [
          {
            "start_time": 0.0,
            "duration": 6.0,
            "transition": "cut",
            "interpolation": "linear",
            "position_mode": "relative",
            "keyframes": [
              {
                "time": 0.0,
                "position": { "dx": 6, "dy": 2, "dz": 4 },
                "yaw": 120,
                "pitch": 8,
                "roll": 0,
                "fov": 70,
                "zoom": 1.0,
                "dof": 0
              },
              {
                "time": 6.0,
                "position": { "dx": 2, "dy": 2, "dz": 2 },
                "yaw": 95,
                "pitch": 10,
                "roll": 0,
                "fov": 65,
                "zoom": 1.2,
                "dof": 0
              }
            ]
          }
        ]
      },
      {
        "type": "LETTERBOX",
        "clips": [
          {
            "start_time": 0.0,
            "duration": 6.0,
            "enabled": true,
            "aspect_ratio": 2.35,
            "fade_in": 0.5,
            "fade_out": 0.5
          }
        ]
      }
    ]
  }
}
```
