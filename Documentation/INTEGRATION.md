# Game integration

[Documentation home](../README.md)

The supplied Blueprints provide a local creator-to-gameplay example. Use their instance handoff and completed-update callbacks as the starting points for your own game.

## Use your own gameplay level

1. Open `/Game/FORMA/Gameplay/Blueprints/BP_FORMA_GameInstance`.
2. In Class Defaults, set **`GameplayMap`** to your destination's package path, for example `/Game/MyGame/Maps/Lvl_Main`. Use the package path without a `.Lvl_Main` object suffix.
3. Keep `BP_FORMA_GameInstance` as the Game Instance Class in **Project Settings > Maps & Modes**, or move its state and handoff logic into your own GameInstance Blueprint.
4. Add your destination map to **Project Settings > Packaging > List of maps to include in a packaged build**, replacing or retaining the demo map as appropriate.
5. Review `StartGameplay` before changing the game mode. Its Open Level node currently supplies `?game=/Game/FORMA/Gameplay/Blueprints/BP_FORMA_GameMode.BP_FORMA_GameMode_C`. That explicit option selects FORMA's game mode even if your destination level has a different default. Replace it with your game mode or remove it to use the normal map/project selection.

The shipped default destination is `/Game/FORMA/Maps/Lvl_FORMA_Gameplay`. Both demo levels are ordinary levels stored under `/Game/FORMA/Maps`.

## Extend the supplied player

For a new game, a child Blueprint of `BP_FORMA_PlayerCharacter` gives you a clear place to add interaction, inventory, combat or other gameplay features. Keep the `VisualOverride` child actor and the call to `InitializeAppearance` while building on the supplied animation setup.

If you create a derived player class, assign it as your GameMode's Default Pawn Class. Use `BP_FORMA_PlayerController` or carry its input setup into your own controller. It adds `IMC_FORMA_Keyboard` and the supplied mouse-look mapping for a local player, then selects game input mode.

Body height and footwear alter visual appearance and ground clearance. Review capsule dimensions, camera position, movement and collision separately for your game's desired proportions.

## Connect an existing player character

For a player that already has its own locomotion system:

1. Add a Child Actor Component that uses `BP_FORMA_VisualCharacter`, or implement an equivalent appearance component arrangement in your own actor.
2. Supply the current Mutable instance. In the demo, the visual actor reads `BP_FORMA_GameInstance.CharacterInstance` when `IsGameplay` is true.
3. Keep the generated component names `Body` and `Face` consistent with the Mutable object, and attach their presentation to your player.
4. Adapt the animation assignment in `InitializeAppearance`. The supplied setup attaches the generated Body to the source skeletal mesh and uses `ABP_FORMA_Retarget` with `RTG_FORMA_Mannequin`. A different source skeleton or animation system needs a corresponding retargeting setup.
5. Preserve the completed appearance-update binding so colors and ground clearance can be reapplied after Mutable regenerates components.

Moving files between projects should use Unreal's **Migrate** workflow, starting from the FORMA assets you need. Keep the returned dependency set and review the destination's rendering and plugin settings. Changing folder names should use editor rename/move operations followed by redirector cleanup.

## Set appearance from your own UI

Operate on the character's current `CustomizableObjectInstance`:

```text
Set Enum Parameter Selected Option or Set Float Parameter Selected Option
    -> set any other related parameter values
    -> Update Skeletal Mesh Async
    -> completed appearance update reapplies presentation state
```

Changing a parameter value alone does not regenerate the character. Existing row widgets show the set-and-update pattern. For clothing colors, call the creator actor's `SetClothingColor` rather than setting only a material tint; it updates both the color value and the Original/Custom mode.

Calls to the supplied `SetHairColor`, `SetEyeColor` and `SetSkinColor` functions maintain the Blueprint cosmetic state. If you bypass these functions, preserve their set flags and reapply values after mesh updates.

## Apply an example preset

The three `CI_FORMA_Example_*` assets are Customizable Object Instances. To add a preset button:

1. Reference the example instance asset in your own Blueprint.
2. Get the current runtime instance used by the preview actor.
3. Call Mutable's **Copy Parameters From Instance** on the runtime instance, passing the example as the source.
4. Request an asynchronous skeletal mesh update.
5. Refresh the creator controls to show the newly selected values.

Copy into the runtime instance rather than editing the saved example asset. Blueprint-only hair, eye and skin color state must be handled alongside Mutable parameters if your preset system includes those values.

## Add persistent character saves

**Lite does not include a SaveGame asset, SaveGameToSlot flow or LoadGameFromSlot flow.** The GameInstance stores state only while the game is running.

For persistent saves, add a SaveGame Blueprint containing a schema version, the chosen enum option names, float and color parameter values, and the Blueprint cosmetic colors plus their set flags. Store values by parameter name; array positions and display labels are unsuitable as durable identifiers.

At save time, read those values from the current instance and cosmetic state, then use Unreal's SaveGame workflow. At load time:

1. Create a new instance of `CO_FORMA_Character` after its parameter data is available.
2. Restore the `Body` branch and the saved parameter values by name.
3. For names or options that no longer exist, retain defaults or apply your schema migration policy.
4. Restore hair, eye and skin values and their set flags.
5. Request one asynchronous appearance update and let its completion path apply presentation state.

Generated skeletal meshes, dynamic material instances and UObject references are runtime results. Save the appearance description needed to regenerate them, rather than relying on those transient objects as persistent data. Texture, projector or other new parameter types require an explicit extension of your save schema.

## Multiple characters and multiplayer

The included handoff is designed for one local player's appearance. For independent NPCs, create one instance per character and change the visual actor's instance-acquisition logic so it does not reuse the player's `GameInstance.CharacterInstance`.

Appearance replication is an integration task. Define and replicate a compact appearance description, validate allowed parameter values and options, and reconstruct a local instance on each client. The supplied GameInstance reference does not replicate a generated appearance between clients.

Budget Mutable update work, groom rendering, material changes and animation per active character. Update appearance when it changes, reuse completed results where appropriate, and measure representative scenes on your target hardware.

