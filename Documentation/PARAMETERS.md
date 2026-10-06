# Parameters and appearance

[Documentation home](../README.md)

The compiled `CO_FORMA_Character` exposes parameter names, types, options and UI metadata. The creator reads these to build its option and float rows. Use the parameter data from your current compiled object as the authority when extending the project.

## Body branch and option groups

`Body` is the enum group that chooses **Female** or **Male**. Additional groups are prefixed by the branch they belong to:

| Option group suffix | Examples | Creator category |
| --- | --- | --- |
| Top, Bottom, Shoes | `Female Top`, `Male Shoes` | Clothing |
| Hair, Eyebrows, Eyelashes | `Female Hair`, `Male Eyebrows` | Hair |
| Beard, Mustache | `Female Beard`, `Male Mustache` | Hair |
| Peachfuzz | `Female Peachfuzz`, `Male Peachfuzz` | Skin |

Optional groups allow `None`. The UI hides option groups for the inactive body branch. Selecting `Body` refreshes the available controls.

Use exact parameter and option names from Mutable. A friendly label shown in the UI can differ from the internal name used by Blueprint setters or a save format.

## Shape controls

The shape parameter families are:

| Family | Parameter names |
| --- | --- |
| Body | `Build`, `Muscle`, `Hips`, `Chest`, `Height`, `Teen`, `Inseam` |
| Head | `HeadSize`, `HeadLength`, `HeadWidth`, `Forehead`, `BackOfHead`, `JawWidth`, `JawHeight`, `Chin`, `ChinWidth`, `Cheekbones`, `CheekHeight`, `EarAngle`, `EarSize` |
| Eyes | `EyeSize`, `EyeSpacing`, `OuterEyeCorners`, `InnerEyeCorners`, `UpperLid`, `LowerLid`, `BrowHeight` |
| Nose and mouth | `NoseWidth`, `NoseLength`, `NoseBridge`, `NoseTip`, `MouthWidth`, `UpperLip`, `LowerLip` |

The paired controls use a neutral value of `0` and typically range from `-1` to `1`; `Muscle` and `Teen` use `0` to `1`. Read `MinimumValue` and `MaximumValue` from the parameter's UI metadata when creating custom controls. The supplied UI hides Hips and Chest for the Male branch.

Shape values are converted to mesh morph factors in the Mutable graphs. Body and head need coordinated deformation to preserve their seam. The included garment morphs also need to track the body shapes that affect them.

## Clothing colors

These are Mutable parameters shared across the relevant garment options:

| Parameter | Purpose |
| --- | --- |
| `Top Color`, `Bottom Color`, `Shoes Color` | Custom color value. |
| `Top Color Mode`, `Bottom Color Mode`, `Shoes Color Mode` | `Original` or `Custom`. |

The actor function `SetClothingColor(Category, Color, Original)` accepts `Top`, `Bottom` or `Shoes` as the category. It sets the color and its mode, then requests an update.

`Original` uses the garment's original material-color path. `Custom` uses the selected color in the garment's connected color path. Other texture and material features remain governed by that garment's graph; a custom garment must wire its own material parameters into the same scheme.

These internal color parameters are handled by dedicated color rows, rather than appearing as generic float or option rows.

## Hair, eye and skin colors

Hair, eye and skin color state is stored on the creator actor and applied to generated mesh materials by Blueprint. During the gameplay transition, the GameInstance retains the values and set flags.

The supplied material names used by `ApplyColors` include:

| Appearance | Material parameters |
| --- | --- |
| Hair | `hairDye`, `hairMelanin`, `hairRedness` |
| Eyes | `Iris Primary Color Hue`, `Iris Global Saturation`, `Iris Primary Color Value` |
| Skin | `Basecolor Global Multiply`, `Basecolor Global Multiply Post-Bake` |

Different materials may use different parameter names. When adding a custom material, expose compatible parameters or adapt `ApplyColors`. Cosmetic values are reapplied after appearance updates because generated components and materials can change.

## UI metadata and category rules

| Metadata field | Usage |
| --- | --- |
| `ObjectFriendlyName` | Label for the control. |
| `UISectionName` | Section used for ordering and category filtering. |
| `UIOrder` | Order within the section. |
| `MinimumValue`, `MaximumValue` | Bounds for a float control. |
| `ExtraInformation.MinLabel`, `ExtraInformation.MaxLabel` | Labels at the ends of a float control. |

`WBP_FORMA_Creator.SectionOrder` currently lists `Body`, `Head`, `NoseMouth`, `Eyes`, `Skin`, `Grooms` and `Clothing`. `GetParameterSortKey` combines the section position with `UIOrder`; use small, distinct order values within a section so the intended grouping remains clear.

`IsParameterVisible` contains the actual category rules. Clothing and groom groups are also recognized by name, and Female/Male prefixes control branch visibility. Adding metadata alone does not create a new category or a widget for an unsupported parameter type.

After editing parameters or metadata, save the child objects and compile the root before refreshing the creator. If the same running widget retains old metadata after a graph edit, restart the Play session; the cache detects object/count changes and is not a general metadata-change watcher.

