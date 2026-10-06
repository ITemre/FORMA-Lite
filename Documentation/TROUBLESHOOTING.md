# Requirements and troubleshooting

[Documentation home](../README.md)

## Requirements

The current release targets **Unreal Engine 5.8 on Windows desktop** with the supplied desktop rendering configuration. It uses DirectX 12 / Shader Model 6, Substrate, software Lumen, GPU skinning with 16-bit bone indices and unlimited bone influences, and groom rendering support. Preserve the relevant settings when integrating into another project.

The `.uproject` explicitly enables these engine plugins:

| Plugin identifier | Role |
| --- | --- |
| `Mutable` | Character assembly, parameters and generated meshes. |
| `HairStrands` | Groom rendering. |
| `HairStrandsMutable` | Mutable Groom Extensions. |
| `RigLogic` | Referenced MetaHuman rig functionality. |
| `ControlRig` | Referenced rig assets and animation dependencies. |
| `IKRig` | IK rig and retargeting assets. |
| `ACLPlugin` | Referenced animation compression assets. |
| `EnhancedInput` | Gameplay input mappings. |

Unreal may enable these plugins' own dependencies automatically. A C++ toolchain, FORMA development tool plugins, MCP or an AI client are not required to use the supplied project.

### Beta and Experimental dependencies

The installed UE 5.8 descriptors mark **Mutable as Beta** and **Mutable Groom Extensions (`HairStrandsMutable`) as Experimental**. FORMA Lite depends on both. Review their behavior when upgrading Unreal or choosing a production platform; the project does not change their engine support status.

## Common problems

| Symptom | What to check |
| --- | --- |
| The first start takes longer than later starts | Allow shader, asset and Mutable preparation to complete. A fresh cache can require additional work. |
| The character or controls have not appeared yet | Allow the first appearance update to complete. The creator initializes when compiled parameter data becomes available. If it stays empty, inspect the Output Log and the root Customizable Object. |
| The editor opens a different scene | Open `/Game/FORMA/Maps/Lvl_FORMA_Creator`, then start Play. |
| PLAY opens the wrong level or fails to travel | Check `BP_FORMA_GameInstance.GameplayMap`, the package path and the packaging map list. Review the explicit game-mode option in `StartGameplay`. |
| Your destination level's game mode is ignored | `StartGameplay` supplies a FORMA game-mode override in the Open Level options. Replace or remove it for your own game. |
| A parameter changes but the mesh stays the same | Request an asynchronous skeletal mesh update after setting values. Check that the parameter affects the selected branch. |
| A new clothing or groom option is missing | Save its child object, verify the parent group and unique option name, then compile the root. |
| Parameter labels or ordering remain old after a graph edit | Save/compile the edited object and restart Play. The widget's cache is not a general watcher for metadata-only changes. |
| The wrong garment texture or material appears | Check each Mesh Section material after changing the mesh; the graph can retain the template's material. |
| Body surfaces disappear in the wrong places | Check body UV tile, mask coverage, inversion and the `Body` tag used by the clip operation. |
| New clothing clips or deforms badly | Check fitted mesh geometry, skin weights, corresponding body-shape morphs, mask coverage and LODs. Arbitrary clothing is not automatically fitted. |
| A groom is missing or does not follow the head | Check the enabled groom plugins and the target mesh, binding and child object's references. Inspect it on a running character instance. |
| Custom hair, eye or skin materials ignore color choices | Expose the parameter names expected by `ApplyColors`, or adapt the Blueprint material-color flow. |
| Several NPCs all show the player's appearance | Give each actor its own instance and adapt the visual actor's GameInstance handoff logic. |
| The chosen character disappears after closing the game | The supplied system retains state within the session. Add the persistent save workflow described in [Game integration](INTEGRATION.md#add-persistent-character-saves). |
| Startup asks for a FORMA authoring/toolset plugin | Check that you opened the delivered project copy. Those development plugins are disabled and are not required by the shipped runtime. |

## Scope of Lite

- The included flow is a local character creator and third-person gameplay starter.
- Persistent appearance saves and multiplayer replication are integration tasks.
- The creator's generic parameter rows support Int/enum and Float types; other types need their own controls.
- New clothing, hairstyles and body/head replacements need compatible authoring data. Fitting tools, original authoring MetaHumans and morph-baking tools are not part of the runtime package.
- The gameplay example's collision capsule, camera and animation setup should be adapted to the target game's needs.
- Makeup controls and an advanced cosmetic authoring system are not included.
- Other Unreal versions, consoles, mobile targets, macOS and Linux are outside the current supported scope.
- There is no fixed frame-rate or crowd-size guarantee. Measure generated mesh updates, groom rendering, animation and material work on the intended hardware and in representative scenes.

## Engine references

For the underlying engine systems, start with Epic's [Mutable Skeletal Mesh Generation](https://dev.epicgames.com/documentation/en-us/unreal-engine/mutable-skeletal-mesh-generation-in-unreal-engine), [Mutable tutorials](https://dev.epicgames.com/community/learning/tutorials/yjw9/unreal-engine-mutable-tutorials) and [Hair Rendering and Simulation](https://dev.epicgames.com/documentation/en-us/unreal-engine/hair-rendering-and-simulation-in-unreal-engine). Epic's [Mutable plugin reference](https://dev.epicgames.com/documentation/unreal-engine/API/PluginIndex/Mutable?lang=en-US) also identifies the plugin's Beta status.

## Report a problem

Open a [FORMA Lite issue](https://github.com/ITemre/FORMA-Lite/issues) with the engine version, the affected level, the selected body and appearance options, and steps that reproduce the problem. Include relevant Output Log errors and a screenshot or recording when the issue is visual. Remove unrelated private data before sharing logs.

