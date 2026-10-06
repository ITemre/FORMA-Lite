# FORMA Lite roadmap

This roadmap records deferred work. It is not a release promise or a delivery schedule.

## After V1: automatic clothing import

**Status: deferred; excluded from FORMA Lite V1.**

V1 includes its prepared clothing options and manual integration of compatible, already prepared garment meshes. It includes no automatic clothing-fitting tool, no clothing-import button and no supported `.mhpkg`-to-FORMA import workflow. The supplied clothing options remain part of V1.

The first future target is compatible MetaHuman Outfit packages. Importing arbitrary FBX meshes is a separate capability that has not been validated.

### Bounded feasibility check — 2026-10-06

A fresh third-party `oa_survivoroutfit.mhpkg` was tested in an isolated UE 5.8 copy on the FORMA female body. Native package import and neutral MetaHuman fitting succeeded. Mutable vertex reshape produced visible defects at strong body-shape values.

One corrective route used native MetaHuman endpoint fitting, persisted the resulting garment morphs, disabled Mutable vertex reshape and retained pose adjustment. It was checked at neutral, maximum Chest, maximum Build, and their combination, from front and back. This route still showed visible intersections and exposed body surfaces on the coat, so it did not pass visual acceptance. No further fitting-parameter trials were performed.

The native fitted test mesh contained one LOD. The package manifest also reported missing wardrobe body-mask assignment and insufficient coat LODs. These are additional authoring constraints; the test does not prove a universal incompatibility of MetaHuman outfits or Mutable.

The test used existing development editor bridges. They, the test assets, and the third-party package are excluded from the delivered project. The test is not animation, cook, runtime-performance or all-garment certification.

### Acceptance criteria before shipping an importer

- Import a representative supported third-party package without changing existing garments, presets or source assets; report incompatible input clearly.
- Produce durable garment shape data for every supported control and relevant combinations, and verify it after a fresh editor load.
- Pass visual checks at neutral and extremes, front/side/back, during representative animations, and at every intended LOD, on each supported body branch.
- Validate body-mask coverage, sleeve openings, layered surfaces, materials, skin weights and LOD transitions. Avoid garment overlap rather than hiding it with an unrelated mask.
- Keep fitting and mesh preparation in the editor; gameplay must use prepared assets through Mutable. Measure import cost and runtime update cost separately.
- Provide a buyer-facing workflow compatible with the content-project distribution, with explicit editor dependencies, predictable errors and rollback. Development plugins must not become undisclosed product dependencies.

Only an importer that passes these checks should be added to the supported feature list.

Tracking issue: [Automatic clothing import after V1](https://github.com/ITemre/FORMA-Lite/issues/1).
