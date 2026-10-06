# System architecture

[Documentation home](../README.md)

Mutable owns character assembly and parameter-driven appearance. Blueprints own the creator UI, camera, level transition, input and gameplay presentation. The supplied flow has one locally controlled character and one appearance instance carried between its creator and gameplay levels.

## Main assets and responsibilities

Paths in this table are relative to `/Game/FORMA`.

| Asset | Responsibility |
| --- | --- |
| `Character/CO_FORMA_Character` | Root Mutable object, shared shape parameters, body branch and appearance parameters. |
| `Character/CO_FORMA_Female` and `Character/CO_FORMA_Male` | Female and male assembly branches. |
| `Character/Clothing/Female` and `Character/Clothing/Male` | Fitted garment meshes and child Customizable Objects. |
| `Character/Grooms/Female` and `Character/Grooms/Male` | Child Customizable Objects for groom options. |
| `Creator/Blueprints/BP_FORMA_CreatorCharacter` | Owns the preview instance, Body and Face components, appearance updates and color application. |
| `Creator/UI/WBP_FORMA_Creator` | Builds option and float rows from Mutable parameter metadata; selects categories and camera views. |
| `Creator/UI/WBP_FORMA_OptionRow` | Changes an enum option on the current instance. |
| `Creator/UI/WBP_FORMA_FloatRow` | Changes a float parameter on the current instance. |
| `Creator/UI/WBP_FORMA_ColorRow` | Offers color choices used by the creator's appearance controls. |
| `Creator/Blueprints/BP_FORMA_StudioCamera` | Named camera presets and smooth transitions between them. |
| `Gameplay/Blueprints/BP_FORMA_GameInstance` | Holds the instance and cosmetic color state during the creator-to-gameplay transition. |
| `Gameplay/Blueprints/BP_FORMA_VisualCharacter` | Child of the creator actor Blueprint; presents the stored appearance during gameplay without the creator UI. |
| `Gameplay/Blueprints/BP_FORMA_PlayerCharacter` | Playable Character with movement, camera and a `VisualOverride` child actor. |
| `Gameplay/Blueprints/BP_FORMA_PlayerController` | Adds keyboard and mouse-look mappings and switches to game input mode. |
| `Gameplay/Blueprints/BP_FORMA_GameMode` | Selects the supplied player character and player controller. |
| `Gameplay/Animation/ABP_FORMA_Retarget` | Animation Blueprint used on the generated Body during gameplay. |
| `Gameplay/Animation/RTG_FORMA_Mannequin` | IK retargeter used by the gameplay animation setup. |

## Instance lifetime

The runtime path is:

```text
Creator actor creates a Mutable instance
    -> UI changes its parameters
    -> Mutable updates Body, Face and generated appearance
    -> PLAY calls GameInstance.StartGameplay
    -> GameInstance retains that instance and cosmetic colors
    -> gameplay visual actor uses the retained instance
    -> player character connects the visual actor to its animation setup
```

`StartGameplay` records `CharacterInstance`, hair and eye colors with their set flags, skin tint with its set flag, and `IsGameplay`. It opens the level named by `GameplayMap` with the supplied FORMA game-mode override.

`GameInstance` survives ordinary level travel, so this retains the appearance for that running session. It is not a disk save or a multiplayer state container.

## Generated mesh components

The creator actor connects two `CustomizableSkeletalComponent` instances to the same Mutable instance. Their component names are **`Body`** and **`Face`**. Those names correspond to the components produced by the character's Mutable graphs.

The Face component follows the Body pose through the supplied leader-pose setup. Grooms are created through Mutable Groom Extensions. `OnAppearanceUpdated` reapplies cosmetic material colors and ground clearance after a generated appearance update.

In gameplay, `BP_FORMA_PlayerCharacter.InitializeAppearance` attaches the visual actor's generated Body to the player's underlying skeletal mesh and assigns `ABP_FORMA_Retarget`. Movement collision remains the responsibility of the player Character and its capsule. Changing appearance height does not automatically redesign the gameplay capsule.

## UI initialization and updates

`WBP_FORMA_Creator.InitializeCreator` waits for a compiled object with available parameters. It binds `HandleCreatorAppearanceUpdated` so it can initialize when the first generated update becomes available.

The widget caches the parameter object and sorted parameter names. An appearance callback rebuilds this cache when the object changes or its parameter count changes. Ordinary appearance updates with the same object and parameter count do not trigger that initialization rebuild.

Category selection uses `IsParameterVisible`, `SectionOrder` and parameter UI metadata. Generic rows currently support **Int/enum options** and **Float controls**. Color rows use the creator's dedicated color functions. A new metadata section or parameter type may need corresponding widget logic; see [Parameters and appearance](PARAMETERS.md).

## Gameplay colors and performance

Clothing colors are Mutable parameters. Hair, eye and skin colors are applied to generated materials by Blueprint and reapplied after appearance updates.

The cosmetic color setters and gameplay initialization also start an `ApplyColors` timer every **0.3 seconds**. `ApplyColors` visits the actor's mesh components. This is relevant when adapting the demo for many characters: profile that recurring work, and consider using the completed-update path plus explicit color-change events instead of repeatedly applying unchanged values.

The supplied float and option rows request an asynchronous mesh update on each value change. In a custom UI, apply several related parameter changes before one update, and consider delaying expensive updates until a drag completes. Keep that policy in your own appearance controller so it can be tuned to the target game.

Each independently customizable NPC needs its **own** Mutable instance. The shipped gameplay visual actor follows the single-player GameInstance handoff; using it unchanged for several NPCs would reuse that player's stored instance. See [Multiple characters](INTEGRATION.md#multiple-characters-and-multiplayer).

