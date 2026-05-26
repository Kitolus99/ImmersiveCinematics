# 脚本格式

脚本是 ImmersiveCinematics 的核心资产。每个 `.json` 文件描述一个可播放的过场动画，包含脚本元信息、运行时行为、自动触发器和时间轴轨道。

根目录速查版见 [`SCRIPT_FORMAT.md`](../../SCRIPT_FORMAT.md)。

## 根对象

```json
{
  "meta": {},
  "timeline": {}
}
```

`ScriptParser` 会严格要求根元素是 JSON 对象，并要求同时存在 `meta` 和 `timeline`。

## `meta`

`meta` 负责脚本身份和运行策略。

```json
"meta": {
  "id": "intro",
  "name": "入场镜头",
  "author": "MapAuthor",
  "version": 3,
  "description": "玩家进入区域后播放",
  "block_keyboard": true,
  "block_mouse": true,
  "hide_hud": true,
  "hide_arm": true,
  "suppress_bob": true,
  "pause_when_game_paused": true,
  "interruptible": true,
  "skippable": true,
  "hold_at_end": false,
  "triggers": []
}
```

关键限制：

- `id` 只允许 `[a-zA-Z0-9_]`，最长 32 字符。
- `name` 最长 50 字符。
- `author` 最长 30 字符。
- `version` 当前必须为 `3`。

## 运行时行为

运行时行为由 `ScriptMeta.RuntimeBehavior` 表示。

| 字段 | 默认值 | 影响 |
| --- | --- | --- |
| `block_keyboard` | `true` | 播放期间阻止大部分键盘输入 |
| `block_mouse` | `true` | 播放期间阻止鼠标视角控制 |
| `block_mob_ai` | `false` | 兼容字段 |
| `hide_hud` | `true` | 隐藏全部 HUD |
| `hide_arm` | `true` | 隐藏第一人称手臂 |
| `suppress_bob` | `true` | 抑制行走、受伤、反胃等镜头晃动 |
| `render_player_model` | `true` | 控制玩家模型渲染相关行为 |
| `pause_when_game_paused` | `true` | 游戏暂停时冻结脚本时间 |
| `interruptible` | `true` | 是否允许新脚本打断当前脚本 |
| `skippable` | `true` | 是否允许玩家长按跳过 |
| `hold_at_end` | `false` | 结束后是否停在最后一帧 |

HUD 子项如 `hide_chat`、`hide_scoreboard`、`hide_action_bar`、`hide_title`、`hide_subtitles`、`hide_hotbar`、`hide_crosshair` 可以写 `true`、`false` 或省略。省略时由主 `hide_hud` 决定。

## `timeline`

```json
"timeline": {
  "total_duration": 30.0,
  "tracks": []
}
```

`total_duration` 的规则：

- 正数：有限时长。
- 负数：无限时长。
- `0`：非法。

## 轨道

每条轨道由 `type` 和 `clips` 组成。

```json
{
  "type": "CAMERA",
  "clips": []
}
```

支持类型：

- `CAMERA`
- `LETTERBOX`
- `AUDIO`
- `EVENT`
- `MOD_EVENT`

解析器会调用 `TrackType.valueOf(value.toUpperCase())`，所以大小写不敏感。

## 相机轨道

相机轨道由 `CameraClip` 组成。它负责相机移动和光学参数。

```json
{
  "start_time": 0.0,
  "duration": 6.0,
  "transition": "cut",
  "interpolation": "linear",
  "position_mode": "relative",
  "loop": false,
  "keyframes": []
}
```

字段说明：

- `start_time` 是全局时间轴秒数。
- `duration` 是 clip 持续秒数，不能为 0。
- `transition` 支持 `cut` 和 `morph`。
- `transition_duration` 默认 `0.5`。
- `interpolation` 支持 `InterpolationType` 枚举中已存在的值。
- `position_mode` 为 `relative` 或 `absolute`。
- `keyframes` 至少 1 个，并且 `time` 必须单调递增。

## 关键帧

```json
{
  "time": 0.0,
  "position": { "dx": 6, "dy": 2, "dz": 4 },
  "yaw": 120,
  "pitch": 8,
  "roll": 0,
  "fov": 70,
  "zoom": 1.0,
  "dof": 0
}
```

相对模式使用 `dx`、`dy`、`dz`。绝对模式使用 `x`、`y`、`z`。

镜头参数：

- `yaw`：偏航角。
- `pitch`：俯仰角。
- `roll`：翻滚角。
- `fov`：视场角。
- `zoom`：缩放倍率。
- `dof`：景深字段，当前主要作为预留数据。

## 贝塞尔路径

如果 `camera` clip 写了 `curve`，相机位置可以沿贝塞尔路径计算。

```json
"curve": {
  "type": "bezier",
  "control_points": [
    { "x": 10, "y": 1.5, "z": 3 },
    { "x": 0, "y": 2.0, "z": -2 }
  ]
}
```

注意：

- 当前要求 `control_points` 恰好 2 个。
- 曲线控制位置路径，不替代关键帧里的朝向、FOV 等属性。

## 黑边轨道

```json
{
  "type": "LETTERBOX",
  "clips": [
    {
      "start_time": 0.0,
      "duration": 10.0,
      "enabled": true,
      "aspect_ratio": 2.35,
      "fade_in": 0.5,
      "fade_out": 0.5
    }
  ]
}
```

`LetterboxTrackPlayer` 会把当前 clip 状态写入 `OverlayManager` 的 `LetterboxLayer`。

## 音频、事件和模组事件

这些轨道已经有数据结构和解析逻辑：

- `AudioClip`
- `EventClip`
- `ModEventClip`

但具体播放/分发能力需要以当前源码实现为准。写脚本时可以先把它们当作兼容未来能力的数据层，不要默认所有运行时行为都已完整实现。

## 触发器

触发器写在 `meta.triggers`：

```json
"triggers": [
  {
    "id": "enter_village",
    "type": "structure",
    "repeatable": false,
    "delay": 1.0,
    "conditions": {
      "structure": "village",
      "radius": 32
    }
  }
]
```

服务端当前会从 `type`、`conditions`、`repeatable`、`delay` 构造触发注册项。`id` 建议保留给人工阅读和编辑器，但当前服务端注册 ID 并不直接使用该字段。

完整触发器参考见 [触发系统](triggers.md)。
