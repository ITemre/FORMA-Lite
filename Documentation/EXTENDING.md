# Add your own content

[Documentation home](../README.md)

Add new appearance content through saved child Customizable Objects. Begin with a duplicate of a shipped option from the same body branch and slot, then replace the assets and option identity. Keep your original option available while you verify the new one.

## Add clothing

1. Choose a template under `/Game/FORMA/Character/Clothing/Female` or `/Game/FORMA/Character/Clothing/Male` that belongs to the desired Top, Bottom or Shoes group.
2. Duplicate its garment Customizable Object and give it a unique `CO_` name and a unique option name. Retain the template's parent-object/group assignment for the same branch and slot.
3. Assign a garment skeletal mesh fitted to the corresponding FORMA body. Match its skeleton and preserve the weight, morph and LOD data needed by your garment.
4. Replace the mesh and each affected section material in the graph. Refresh mesh nodes when section or LOD layouts change. Replacing only the mesh can leave a section using the template's material.
5. Connect the garment's shape morphs to the existing shared shape factors. The shipped `_RBF` meshes contain baked garment morphs; these do not automatically transfer to an unrelated replacement mesh.
6. Preserve pose adjustment and the merge into the **Body** component. Review the template's shared shape inputs and its reshape settings before changing them.
7. Replace the clip mask with one that matches the new garment's coverage on the body's UVs. Retain the correct `Body` tag and mask convention.
8. Connect the garment's color path to the relevant shared clothing color and Original/Custom mode if the garment should support the creator's palette.
9. Save the new child object, then compile `/Game/FORMA/Character/CO_FORMA_Character`.
10. Check the option at neutral and strong body-shape values, from the front, sides and back, during movement and at its intended LODs.

Mutable discovers child options through saved asset references. An unsaved child can be missing from a group even when its graph looks complete.

### Garment fit and masks

The runtime package contains fitted garment meshes and their body-shape morphs. It does not contain a tool that fits arbitrary imported clothing to every body shape. New garments need appropriate fitting, skinning and shape preparation in your authoring workflow.

The supplied body-mask setup uses body UVs in tile **1001**. Its original mask convention is converted by a Texture Invert node before the clip operation. Match the final mask convention of your chosen template; an incorrect UV tile, inversion or body tag can remove the wrong body surfaces.

Clipping avoids rendering covered body surfaces. It does not resolve every possible garment overlap or validate every extreme shape combination. Check coverage and deformation together.

## Add a groom

1. Duplicate a groom child Customizable Object from the appropriate Female or Male directory.
2. Give the new option a unique name and keep the correct parent group, such as Hair, Eyebrows or Beard.
3. Assign a groom asset and a binding compatible with the intended FORMA head. A groom binding is specific to its target mesh; do not assume a binding for another MetaHuman head is interchangeable.
4. Replace the corresponding groom, binding and material references in the duplicated graph.
5. Save the child, compile the root, and inspect it on a running character instance. Review fit, animation, material color and LOD transitions.

Female and Male options have separate child objects and bindings. Add each required branch explicitly. The Blueprint hair-color path also needs material parameters compatible with `ApplyColors`.

Groom authoring, rebinding and arbitrary hairstyle conversion are separate Unreal/DCC authoring tasks. The Lite runtime does not generate a missing binding or fit a hairstyle to an unrelated head automatically.

## Add a shape control

1. Add a uniquely named float parameter to the root Customizable Object, using existing controls as examples for factor splitting and shared shape outputs.
2. Add the required morphs to the body and head branches. Where the shape affects clothing, prepare matching garment deformation as well.
3. Fill in the parameter's friendly name, section, order and float range metadata.
4. Use an existing category section, or update `SectionOrder` and `IsParameterVisible` if adding a new section/category.
5. Save and compile the changed objects, then restart the creator session to refresh its metadata.

The generic row builder handles Int/enum and Float types. A new Bool, Texture, Projector or other type requires a matching widget and setter flow. A float that exists in the root but is not connected to an active branch will not change that branch's appearance.

## Adjust the creator camera

`BP_FORMA_StudioCamera.ShowPreset(PresetName)` looks up named presets from these parallel arrays:

- `PresetNames`
- `PresetLocations`
- `PresetRotations`
- `PresetFOVs`

Keep the arrays aligned when adding or removing a preset. Existing names include Body, Head, Face, Eyes, Hair, Torso, Legs and Feet. Update `WBP_FORMA_Creator.RefreshCamera` when a new category or view needs to request a different preset.

The creator UI occupies the left side of the view. Evaluate framing with the runtime UI visible so that the edited body region stays clear of the controls.

## Keep the extension maintainable

Use unique parameter and option names, save child objects before root compilation, and move assets through the editor so references follow. Keep authoring experiments and development plugins outside the delivered runtime project.

When adding a new dependency, document it and check whether it is needed at runtime or only during authoring. Profile a representative gameplay scene after significant changes to mesh complexity, grooms or appearance-update frequency.

