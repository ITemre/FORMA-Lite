# FORMA Lite

FORMA Lite is a Blueprint character creator and third-person starter project for Unreal Engine 5.8. It uses Epic's Mutable system to assemble a MetaHuman-based character from body, clothing, skin and groom options, then carries that character into the gameplay demo.

**Release status:** FORMA Lite is being prepared for release on Fab. This repository contains its English documentation. The Unreal project and its assets are supplied separately with the product.

**Engine feature status:** In the UE 5.8 plugin descriptors, **Mutable is marked Beta** and **Mutable Groom Extensions (`HairStrandsMutable`) is marked Experimental**. FORMA Lite requires both.

## Start here

| Guide | What you will learn |
| --- | --- |
| [Quick start](Documentation/QUICK_START.md) | Open the project, create a character and enter the gameplay demo. |
| [System architecture](Documentation/ARCHITECTURE.md) | Find the main Blueprints and understand instance ownership, mesh updates and animation. |
| [Game integration](Documentation/INTEGRATION.md) | Use your own gameplay level, connect an existing player character and add persistent saves. |
| [Parameters and appearance](Documentation/PARAMETERS.md) | Work with shape controls, option groups, colors and UI metadata. |
| [Add your own content](Documentation/EXTENDING.md) | Extend clothing, grooms, shape controls and camera presets. |
| [Requirements and troubleshooting](Documentation/TROUBLESHOOTING.md) | Check dependencies, diagnose common problems and understand the scope of Lite. |

## Included system

- A creator studio with Base, Body, Head & Face, Eyes, Skin, Hair and Clothing categories.
- Female and male character branches in one root Customizable Object.
- Clothing and groom options implemented as separate child Customizable Objects.
- Clothing meshes with baked body-shape morphs, body masks and pose adjustment.
- A Blueprint handoff from the creator to a third-person gameplay character.
- Three example Customizable Object Instances for exploring appearance combinations.
- Editable Blueprint UI, camera presets, input, game flow and animation assets.

The project contains no custom C++ modules and requires no FORMA-specific code plugin. The included runtime depends on Unreal Engine plugins listed in the [requirements](Documentation/TROUBLESHOOTING.md#requirements).

## Important scope

The supplied flow retains the chosen appearance **within the current game session**, including the transition from the creator to gameplay. **Persistent SaveGame storage and multiplayer appearance replication are not included.** The [integration guide](Documentation/INTEGRATION.md) explains where to add them.

The release targets Unreal Engine 5.8 on Windows desktop. Other engine versions and platforms are outside the current supported scope. New clothing and grooms need compatible meshes, materials and bindings; the creator does not automatically fit arbitrary imported content.

**Automatic clothing import is excluded from V1.** The release includes prepared clothing options and supports manual integration of compatible, already prepared garments. There is no clothing-import button or supported automatic `.mhpkg`-to-FORMA fitting workflow. See the [roadmap](Documentation/ROADMAP.md#after-v1-automatic-clothing-import) for the acceptance criteria for a future importer.

## Project layout

All project assets are under `/Game/FORMA`:

```text
FORMA/
  FORMA.uproject
  Config/
  Content/
    FORMA/
      Character/       Mutable objects, body meshes, clothing, grooms and skins
      Creator/         Studio actor, camera, widgets and example instances
      Gameplay/        Player, game flow, input and animation
      Maps/            Creator and gameplay demo levels
      Dependencies/    Referenced Epic assets used by the project
  README.md
  Documentation/
```

The same documentation is included beside `FORMA.uproject`, with relative links that work in the downloaded project.

## Support

Report FORMA-specific problems through [GitHub Issues](https://github.com/ITemre/FORMA-Lite/issues). Include your Unreal Engine version, the level and appearance options involved, the reproduction steps, and the relevant error text. A screenshot or short recording helps with visual problems.

