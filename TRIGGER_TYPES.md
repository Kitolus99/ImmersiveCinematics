# ImmersiveCinematics 触发器类型参考

触发器写在脚本的 `meta.triggers` 数组中，用于让服务端在满足条件时自动播放脚本。更完整的系统说明见 [docs/zh/triggers.md](docs/zh/triggers.md)。

## 通用字段

```json
{
  "id": "enter_village",
  "type": "structure",
  "repeatable": true,
  "delay": 1.0,
  "conditions": {
    "structure": "village",
    "radius": 32
  }
}
```

| 字段 | 类型 | 必填 | 默认值 | 说明 |
| --- | --- | --- | --- | --- |
| `id` | string | 否 | 无 | 建议保留，便于人工识别；当前服务端注册 ID 由类型和脚本 ID 派生 |
| `type` | string | 是 | 无 | 触发类型 |
| `repeatable` | boolean | 否 | `false` | 是否可重复触发 |
| `delay` | number | 否 | `0` | 条件满足后延迟执行秒数 |
| `conditions` | object | 否 | `{}` | 类型特有条件 |

## ID 匹配规则

多数资源 ID 字段支持：

- `minecraft:zombie`：精确匹配。
- `minecraft:*`：命名空间通配。
- `zombie`：无冒号时做子串匹配。
- `*`：任意匹配。

注意：`advancement` 当前使用 `ResourceLocation.parse` 后查进度对象，不适合写子串或 `*`。

## 类型总览

| 类型 | 策略 | 说明 |
| --- | --- | --- |
| `login` | 事件驱动 | 玩家登录时触发 |
| `location` | 轮询 | 玩家进入指定维度、点半径或方体区域 |
| `advancement` | 事件驱动 | 玩家获得指定进度 |
| `biome` | 轮询 | 玩家位于指定生物群系 |
| `entity_kill` | 事件驱动 | 玩家击杀指定实体 |
| `interact` | 事件驱动 | 玩家与方块或实体交互 |
| `dimension_change` | 事件驱动 | 玩家切换到指定维度 |
| `dimension` | 事件驱动 | `dimension_change` 的别名 |
| `inventory` | 轮询 | 物品栏包含指定物品或数量变化 |
| `item_craft` | 事件驱动 | 玩家合成指定物品 |
| `item_use` | 事件驱动 | 玩家完成使用指定物品 |
| `custom` | 事件驱动 | 由外部调用 `CustomEventTracker.fire` 触发 |
| `command` | 事件驱动 | 当前预留，评估器固定返回 `false` |
| `structure` | 轮询 | 玩家位于指定结构 |
| `gamestage` | 轮询 | 玩家拥有指定 GameStages 阶段 |

## `login`

玩家登录时触发。

```json
{
  "type": "login",
  "conditions": {}
}
```

## `location`

玩家进入指定位置、区域或维度时触发。

```json
{
  "type": "location",
  "conditions": {
    "dimension": "minecraft:overworld",
    "position": { "x": 100, "y": 64, "z": 200 },
    "radius": 10
  }
}
```

方体区域：

```json
{
  "type": "location",
  "conditions": {
    "dimension": "minecraft:overworld",
    "corner1": { "x": 0, "y": 60, "z": 0 },
    "corner2": { "x": 50, "y": 80, "z": 50 }
  }
}
```

只写 `dimension` 时，表示进入该维度即触发。

## `advancement`

玩家获得指定进度时触发。

```json
{
  "type": "advancement",
  "conditions": {
    "advancement": "minecraft:story/enter_the_nether"
  }
}
```

## `biome`

玩家位于指定生物群系时触发。

```json
{
  "type": "biome",
  "conditions": {
    "biome": "minecraft:desert"
  }
}
```

## `entity_kill`

玩家击杀指定实体时触发。

单实体：

```json
{
  "type": "entity_kill",
  "conditions": {
    "entity": "minecraft:zombie"
  }
}
```

任意一个：

```json
{
  "type": "entity_kill",
  "conditions": {
    "entity": ["minecraft:zombie", "minecraft:skeleton"],
    "mode": "or"
  }
}
```

全部击杀过：

```json
{
  "type": "entity_kill",
  "conditions": {
    "entity": ["minecraft:zombie", "minecraft:skeleton", "minecraft:spider"],
    "mode": "and"
  }
}
```

## `interact`

玩家与方块或实体交互时触发。

```json
{
  "type": "interact",
  "conditions": {
    "target": "minecraft:jukebox"
  }
}
```

`target: "*"` 表示任意交互。

## `dimension_change` / `dimension`

玩家切换到指定维度时触发。

```json
{
  "type": "dimension_change",
  "conditions": {
    "dimension": "minecraft:the_nether"
  }
}
```

## `inventory`

检测玩家物品栏。

全部拥有：

```json
{
  "type": "inventory",
  "conditions": {
    "items": ["minecraft:diamond", "minecraft:emerald"],
    "mode": "and"
  }
}
```

拥有任意一个：

```json
{
  "type": "inventory",
  "conditions": {
    "items": ["minecraft:diamond", "minecraft:emerald"],
    "mode": "or"
  }
}
```

数量增加：

```json
{
  "type": "inventory",
  "conditions": {
    "items": ["minecraft:sponge"],
    "change": "increase"
  }
}
```

数量减少：

```json
{
  "type": "inventory",
  "conditions": {
    "items": ["minecraft:diamond_helmet"],
    "change": "decrease"
  }
}
```

注意：数量变化检测当前按完整物品 ID 建快照，`change` 场景建议写精确 ID。

## `item_craft`

玩家合成指定物品时触发。

```json
{
  "type": "item_craft",
  "conditions": {
    "item": "minecraft:leather_chestplate"
  }
}
```

## `item_use`

玩家完成使用指定物品时触发，例如吃完食物、喝完药水、完成拉弓等。

```json
{
  "type": "item_use",
  "conditions": {
    "item": "minecraft:golden_apple"
  }
}
```

## `custom`

外部代码调用 `Evaluators.CustomEventTracker.fire(player, eventId)` 后触发。

```json
{
  "type": "custom",
  "conditions": {
    "event_id": "story_beat_1"
  }
}
```

## `command`

当前为预留类型，评估器固定返回 `false`，不会自动触发。

```json
{
  "type": "command",
  "conditions": {}
}
```

## `structure`

玩家位于指定结构时触发。

```json
{
  "type": "structure",
  "conditions": {
    "structure": "village",
    "radius": 32
  }
}
```

`radius` 为 0 或省略时，只检查玩家当前方块所在结构；大于 0 时会在周围采样检测。

## `gamestage`

需要安装 GameStages。未安装时始终不触发。

```json
{
  "type": "gamestage",
  "conditions": {
    "stage": "entered_dungeon"
  }
}
```

## 完整示例

```json
{
  "meta": {
    "id": "village_intro",
    "name": "村庄入场",
    "author": "MapAuthor",
    "version": 3,
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
  }
}
```
