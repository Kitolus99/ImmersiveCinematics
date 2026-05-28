# ImmersiveCinematics

Add cutscenes to Minecraft modpacks, adventure maps, and servers. Authors can write JSON scripts or use the in-game timeline editor.

[中文文档](README.md) | [中文 Wiki](docs/zh/index.md) | [Script Format](SCRIPT_FORMAT.md) | [Trigger Reference](TRIGGER_TYPES.md)

> This repository is a fork of `SanZiNEO/ImmersiveCinematics`. The Chinese documentation was rebuilt from the local source tree and DeepWiki analysis.

## What Is This?

ImmersiveCinematics is a Minecraft Forge 1.20.1 mod for scripted cinematic playback.

- The server-side trigger engine decides when a script should play.
- The client-side playback engine drives camera movement, letterbox overlays, and timeline tracks.
- The in-game editor lets authors create and preview scripts without leaving the game.

## Use Cases

- Area intro cutscenes.
- Boss fight presentation shots.
- Fixed-camera puzzle rooms.
- Server tutorials and guided map tours.
- Story beats combined with server commands or other mods.

## Features

- 6-DOF camera: position, yaw, pitch, roll, FOV, zoom.
- Keyframes, relative/absolute positioning, loops, infinite duration.
- Timeline tracks: `camera`, `letterbox`, `audio`, `event`, `mod_event`.
- Letterbox overlays with aspect ratio and fade control.
- Server triggers for login, location, advancement, biome, dimension change, entity kill, interaction, crafting, item use, inventory, structure, gamestage, custom events, and command placeholders.
- Runtime behavior flags for input blocking, HUD hiding, bob suppression, skipping, interruption, and hold-at-end playback.
- In-game editor: press `F6` by default. Hold `C` by default to skip skippable scripts.

## Getting Started

1. Install Minecraft Forge 1.20.1 (47.x).
2. Put the built jar into `.minecraft/mods/`.
3. Put JSON scripts into `immersive_cinematics/scripts/`.
4. Use commands in game:

```mcfunction
/icinematics play example_orbit
/icinematics stop
/icinematics status
```

## Development

```powershell
.\gradlew.bat build --stacktrace --no-daemon
.\gradlew.bat runClient --stacktrace --no-daemon
```

`runClient` starts the Minecraft development client and stays open until you close the game.

## Documentation

The most complete documentation in this fork is currently in Chinese:

- [中文 Wiki 总览](docs/zh/index.md)
- [架构说明](docs/zh/architecture.md)
- [脚本格式](docs/zh/script-format.md)
- [触发系统](docs/zh/triggers.md)
- [播放引擎](docs/zh/playback-engine.md)
- [游戏内编辑器](docs/zh/editor.md)
- [配置与命令](docs/zh/configuration.md)

## Version

| Item | Version |
| --- | --- |
| Mod | 0.3.1 |
| Minecraft | 1.20.1 |
| Forge | 47.x |
| Script Format | v3 |

## License

MIT License
