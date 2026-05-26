# 触发系统

触发系统让脚本可以自动播放，而不是只能靠 `/icinematics play` 手动执行。它运行在服务端，负责监听 Forge 事件、轮询玩家状态、评估条件、记录触发状态，并向客户端发送播放包。

根目录速查版见 [`TRIGGER_TYPES.md`](../../TRIGGER_TYPES.md)。

## 核心类

| 类 | 路径 | 作用 |
| --- | --- | --- |
| `TriggerRegistry` | `trigger/server/TriggerRegistry.java` | 保存触发器类型注册表 |
| `TriggerType` | `trigger/server/TriggerType.java` | 描述触发器 ID、策略、轮询间隔和评估器 |
| `TriggerEngine` | `trigger/server/TriggerEngine.java` | 触发系统调度核心 |
| `Evaluators` | `trigger/server/evaluator/Evaluators.java` | 条件评估函数 |
| `TriggerRegistration` | `trigger/server/TriggerRegistration.java` | 单个脚本触发器注册项 |
| `TriggerStateStore` | `trigger/server/store/TriggerStateStore.java` | 玩家触发状态持久化 |
| `ScriptManager` | `script/ScriptManager.java` | 从脚本加载触发器并注册 |

## 注册流程

`ImmersiveCinematics.registerTriggerTypes()` 在 common setup 中注册所有触发器类型。每种类型包含：

- 类型 ID，如 `location`。
- 监听策略：`EVENT_DRIVEN` 或 `POLLING`。
- 轮询间隔，事件驱动类型通常为 0。
- 条件评估器，如 `Evaluators::evaluateLocation`。
- 关联的 Forge 事件类型集合。

服务器启动后：

1. `ScriptManager.copyGlobalToWorld()` 将全局脚本复制到世界目录。
2. `ScriptManager.loadAll()` 从世界目录加载脚本。
3. `TriggerStateStore.initialize()` 初始化状态存储。
4. `TriggerEngine.initialize()` 初始化触发引擎。
5. `ScriptManager.registerAllTriggers()` 将 `meta.triggers` 转为 `TriggerRegistration`。

## 事件驱动触发器

事件驱动触发器由 Forge 事件触发，适合登录、进度、击杀、交互、合成、使用物品、维度切换等瞬时行为。

| 类型 | 事件来源 |
| --- | --- |
| `login` | `PlayerLoggedInEvent` |
| `advancement` | `AdvancementEarnEvent` |
| `entity_kill` | `LivingDeathEvent` |
| `interact` | `RightClickBlock`、`LeftClickBlock`、`EntityInteract` |
| `dimension_change` / `dimension` | `PlayerChangedDimensionEvent` |
| `item_craft` | `ItemCraftedEvent` |
| `item_use` | `LivingEntityUseItemEvent.Finish` |
| `custom` | 外部调用跟踪器 |
| `command` | 当前预留 |

`entity_kill`、`interact`、`item_craft`、`item_use` 会先把最后一次行为记录到对应 tracker，再进入评估器。

## 轮询触发器

轮询触发器在服务器 tick 里定期检查。

| 类型 | 默认间隔 | 说明 |
| --- | --- | --- |
| `location` | 20 tick | 位置、区域或维度 |
| `biome` | 40 tick | 生物群系 |
| `inventory` | 20 tick | 物品栏 |
| `structure` | 20 tick | 结构 |
| `gamestage` | 20 tick | GameStages 阶段 |

轮询间隔来自 `Config`，可以在配置文件或配置界面中调整。

## 条件评估

评估器只关心当前玩家和 `conditions`。

常见匹配规则：

- `*` 匹配任意。
- `namespace:*` 匹配命名空间。
- 完整资源 ID 精确匹配。
- 无冒号字符串按子串匹配。

`advancement` 是例外：当前会按完整 `ResourceLocation` 查进度对象。

## 延迟与重复

每个触发器支持：

- `repeatable: false`：默认单次触发，触发后写入状态。
- `repeatable: true`：允许重复触发。
- `delay`：条件满足后延迟若干秒再执行动作。

`TriggerEngine` 会跳过这些情况：

- 玩家已经在播放同一脚本。
- 不可重复触发器已经被记录为触发过。

## 播放动作

当前脚本触发器注册时会生成 `StartPlaybackAction(scriptId)`。触发成功后，服务端通过网络包通知客户端播放对应脚本。

客户端接收流程：

1. `S2CPlayScriptPacket` 到达客户端。
2. `ClientScriptReceiver.handlePlayScript()` 解析脚本 JSON。
3. 调用 `CameraManager.INSTANCE.playScript()`。
4. 客户端开始播放并回传开始/完成状态。

## 状态保存

`TriggerStateStore` 按玩家保存触发状态，用于支持不可重复触发器。玩家登录时加载，退出、世界保存或服务器停止时保存。

这意味着：

- 单次触发剧情不会因为玩家重进服务器而反复播放。
- 如果要重置玩家剧情进度，需要调用状态存储相关重置逻辑或清理对应存档状态。

## 示例：进入村庄播放

```json
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
```

## 示例：进入区域播放

```json
{
  "id": "enter_area",
  "type": "location",
  "repeatable": true,
  "conditions": {
    "dimension": "minecraft:overworld",
    "corner1": { "x": 0, "y": 60, "z": 0 },
    "corner2": { "x": 50, "y": 90, "z": 50 }
  }
}
```

## 示例：获得物品播放

```json
{
  "id": "got_key",
  "type": "inventory",
  "repeatable": false,
  "conditions": {
    "items": ["minecraft:tripwire_hook"],
    "mode": "and"
  }
}
```
