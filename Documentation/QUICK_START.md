# Quick start

[Documentation home](../README.md)

Use the supplied project first to explore the complete creator-to-gameplay flow. The [integration guide](INTEGRATION.md) explains how to connect that flow to your own game.

## Open FORMA

1. Install Unreal Engine **5.8** through the Epic Games Launcher.
2. Extract the project to a writable folder. Keep `FORMA.uproject`, `Config` and `Content` together.
3. Open `FORMA.uproject` with UE 5.8. The required engine plugins are enabled in the project descriptor. If Unreal shows a Beta or Experimental feature notice, the dependency details are in [requirements](TROUBLESHOOTING.md#requirements).
4. Allow the first shader and asset preparation to finish. A fresh installation can take longer than later starts.
5. Open `/Game/FORMA/Maps/Lvl_FORMA_Creator` if the creator level is not already open.
6. Use the editor's **Play** button. The runtime creator UI appears alongside the character preview.

The shipped project has no custom C++ build step. A request to rebuild a missing FORMA development tool plugin usually indicates that a different project copy is being opened; check the `.uproject` file and the [plugin list](TROUBLESHOOTING.md#requirements).

## Create a character

| Category | Controls |
| --- | --- |
| Base | Choose the Female or Male branch. |
| Body | Adjust the body shape and proportions. |
| Head & Face | Adjust head, nose and mouth shape. |
| Eyes | Adjust eye shape and eye color. |
| Skin | Choose available skin options and a skin tint; peach-fuzz options also appear here. |
| Hair | Choose hair, eyebrows, eyelashes and facial hair, and adjust hair color. |
| Clothing | Choose Top, Bottom and Shoes, with Original or custom clothing colors. |

Selections and sliders request an asynchronous Mutable mesh update. Give the update time to complete before judging the result or entering gameplay. Controls depend on the selected body branch; for example, the Male branch hides the Female-specific Hips and Chest controls.

Use the **15° rotation buttons** to inspect the character from different angles. Switching categories changes the camera framing for the relevant region.

Optional clothing and groom groups can use `None`. Appearance parameter names and color behavior are described in [Parameters and appearance](PARAMETERS.md).

## Enter the gameplay demo

Click **PLAY** inside the creator UI after the appearance update has completed. This calls `BP_FORMA_GameInstance.StartGameplay`, stores the current instance and cosmetic color values, and opens `/Game/FORMA/Maps/Lvl_FORMA_Gameplay`.

The demo uses `BP_FORMA_GameMode`, `BP_FORMA_PlayerCharacter` and `BP_FORMA_PlayerController`. Its keyboard and mouse controls are:

| Input | Action |
| --- | --- |
| W, A, S, D | Move. |
| Mouse | Look around. |
| Space | Jump. |

The keyboard mapping is `/Game/FORMA/Gameplay/Input/IMC_FORMA_Keyboard`. The player controller also adds the mouse-look mapping from the supplied Epic dependencies. Change these mappings to match your own input scheme.

Stopping Play ends the session. The next fresh session starts from the project's defaults; a persistent character save is an integration task, described in [Game integration](INTEGRATION.md#add-persistent-character-saves).

## Explore the example looks

The following Customizable Object Instances are under `/Game/FORMA/Creator/Examples`:

- `CI_FORMA_Example_Casual`
- `CI_FORMA_Example_Urban`
- `CI_FORMA_Example_Active`

Open an instance in its editor to inspect its parameter values. To offer it as an in-game preset, copy its parameters to the current runtime instance and request an update; see [Apply an example preset](INTEGRATION.md#apply-an-example-preset). The creator UI does not include a preset-loading menu.

## Before adding your own game features

Read the [architecture](ARCHITECTURE.md) to identify the instance owner, preview actor and playable character. Then change one part at a time: first the destination level, then your gameplay behavior, then additional appearance content.

