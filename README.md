# P6G

Radio propagation research: outdoor 5G/6G coverage simulation with Sionna RT,
using real building geometry from CityGML (LOD2/LOD3 cadastral data) and
IFC/BIM models. The goal is better 5G/6G network accessibility for network
planning, and to support relevant teams from an urban built-environment
perspective.

[Sionna RT](https://nvlabs.github.io/sionna/rt/) is NVIDIA's open-source
ray tracer for radio propagation, built on
[Mitsuba](https://www.mitsuba-renderer.org/)/[Dr.Jit](https://drjit.readthedocs.io/).
It simulates how a signal reflects, diffracts, and attenuates as it travels
through a 3D scene, giving physically grounded coverage predictions instead
of idealized path-loss formulas. This project feeds it real building
geometry instead of synthetic scenes: Hamburg's official CityGML city model,
and IFC/BIM models of individual structures. Results reflect actual sites,
not generic ones.

This repository exists so the team can develop and run this pipeline
collaboratively instead of everyone keeping separate local copies.

## Pipeline

Step 1 (geometry pipeline person): get building geometry into a Mitsuba scene
Sionna can load.
- 1a: CityGML -> Blender -> Mitsuba XML (`step1_blender_to_mitsuba/1a_citygml/`)
- 1b: IFC -> FBX -> Blender -> Mitsuba XML (`step1_blender_to_mitsuba/1b_ifc/`)

Step 2 (simulation person; geometry pipeline person provides the scene data
and has overview): load a scene from `scenes/`, place transmitters/receivers,
run the parameter sweeps (positions, counts, materials), compute paths and
radio maps. (`step2_sionna_simulation/`)

Step 3 (shared): turn step 2 results into figures for presenting the work.
(`step3_visualization/`)

`scenes/` is the handoff point between step 1 and step 2.

## Getting a scene from Blender to the simulation person

Run the step 1 notebook (it documents both a manual and an AI-assisted way
of doing this), which writes `scenes/<name>/<name>.xml` plus a `meshes/`
folder next to it. Commit and push that folder. Step 2 notebooks then just
point `SCENE_XML_PATH` at it.

Name the export folder for what's actually different about it
(`citygml_40_buildings`, `ifc_stationhall`) instead of a bare `test12`.
Once there are a dozen of these, nobody remembers which is which.

## Repo size

Step 2 sweeps generate a lot of raw output fast: radio maps, path pickles,
CIR/CFR arrays. Commit the code and a few representative results, not every
sweep output.

For now, results are small enough (individual outputs on the order of
5-10 MB) to just commit to git/GitHub directly. No external storage is set
up yet. This won't scale indefinitely: once sweep outputs grow past that, or
once the team has access to shared storage (e.g. HCU Nextcloud, not yet set
up for everyone), move bulk results there instead and stop committing them,
since every version of a binary file you commit stays in the repo history
forever.

`.ply` and `.fbx` files are tracked with Git LFS (`.gitattributes`). Run
`git lfs install` once if you haven't used LFS before.

## Setup

Each notebook lists what it needs at the top, but the underlying toolchain
isn't pinned anywhere else, and every piece of it is version-sensitive
(add-ons break across Blender majors, Sionna RT has had breaking API changes
across majors). If something in step 1 fails in a way the notebook's
troubleshooting table doesn't cover, mismatched versions against this list
are the first thing to check.

| Tool | Version in use | Notes |
|---|---|---|
| Blender | 4.1 (known working) | The [mitsuba-blender](https://github.com/mitsuba-renderer/mitsuba-blender) add-on's own docs call out 3.6 and 4.2 (both LTS) as their most-tested versions, not 4.1. If you hit an add-on install/compat issue on 4.1, try 4.2 LTS before debugging further. |
| CityGML importer/exporter add-on | [orttak/blender-citygml-importer-exporter](https://github.com/orttak/blender-citygml-importer-exporter) | Needs Blender 4.0+. Writes surface-type info as custom properties (`surface_type`, `SurfaceTyp`, `Typ`, `FeatureType`, ...); `1a`'s material-mapping step reads these. Using a *different* CityGML add-on means checking what property names it actually writes before trusting `1a` §1.3. |
| Mitsuba-Blender export add-on | [mitsuba-renderer/mitsuba-blender](https://github.com/mitsuba-renderer/mitsuba-blender) | Requires Blender 2.93+. Needed for the Sionna-facing XML export in both `1a` and `1b`. |
| `sionna-rt` | Track the [official Sionna RT tutorial](https://nvlabs.github.io/sionna/rt/tutorials/Introduction.html) (currently targets 2.0.1) | `pip install sionna-rt` also pulls in compatible `mitsuba`/`drjit`. Don't pin those separately unless you have a reason to. Run `pip freeze \| grep -E "sionna|mitsuba|drjit"` in a known-working environment and put the exact pins in `requirements.txt` once you have one confirmed working end-to-end. |
| Python (step 2/3, and the "verify" section of step 1 notebooks) | not yet pinned | Record whatever version your working environment uses here once confirmed. |

The IFC → FBX leg of step 1b has no fixed tool requirement. It's been done
via Rhino, but any tool works as long as the exported FBX preserves object
hierarchy and layer/parent naming, since `1b`'s material assignment matches
on parent-object names.

Step 2/3 currently run against Google Drive-mounted scenes from a Colab
notebook in practice, not the local git/`scenes/` flow described above.
Once step 2 code lands in this repo, its setup instructions should cover
both: a `SCENE_XML_PATH` pointed at a local checkout of `scenes/`, and a
Drive-mounted path for Colab, so either environment works.
