# Week 1: Rendering and Transform Geometry Technical Note

Scope: inspect the editor's ID/depth picking, `GaussianInstances`, layers, and the transform palette; test manual translation and rotation on a selected subset; record frame rate, pivot behavior, and SH/lighting artifacts.

## 1. Test Environment

| Item | Value |
| --- | --- |
| Browser | Chrome 157.0.8103.80 |
| OS | Windows 11 Pro, build 26200 (Chrome reports it as Windows 10.0.26200) |
| Discrete GPU | NVIDIA GeForce RTX 5080 (0x10de:0x2c02), driver 32.0.16.1088 |
| Other GPUs | Intel integrated GPU, NPU (not used) |
| WebGPU | Hardware accelerated, Dawn D3D12 backend, running on the RTX 5080 |
| WebGL | ANGLE D3D11, `GL_RENDERER` = RTX 5080 |
| Editor | SuperSplat v3.8.1, SplatTransform 3.11.2, PlayCanvas 2.24.1 (8597cab) |
| Test scene | `Apartment.ply`, 146.8 MB, 2,748,013 vertices |

The header of `Apartment.ply` only contains `f_dc_0..2` and **no `f_rest_*`**, so it is degree-0 SH. SH view-dependent artifacts cannot be tested with this scene.

## 2. Code References

**Picking and layers**

- `apps/editor/src/picker.ts:240`: the ID channel reads a 4-byte pixel and reconstructs a 32-bit ID; `picker.ts:389`: alpha < 1e-6 is treated as no hit. The ID is written in `apps/editor/src/shaders/projected-splat-shader.ts:229` (`gaussianId`) and `:276` (`gaussianId + 1u`).
- `apps/editor/src/controllers.ts:347-353`: double-clicking the canvas calls `pickFocalPoint`. A middle-click (orbit mode) also calls it.
- `apps/editor/src/camera.ts:1182`: `pickFocalPoint` performs depth picking; `:1189` fires `camera.focalPointPicked`.
- `apps/editor/src/camera.ts:700-701`: the gizmo layer is added to the render pass.

**Transform palette and pivot**

- `apps/editor/src/gaussian-instances.ts:180-194`: the low 16 bits of a palette word store the transform index, and the high 16 bits store the colour index. `projected-splat-projector-shader.ts:297` reads the transform index; `:484` reads the colour index.
- `apps/editor/src/splat.ts:434` `getPivot`: the pivot defaults to the object's own origin (in localFrame units).
- `apps/editor/src/splats-transform-handler.ts:47-49`: `selection.changed` calls `placePivot()`, which resets the pivot to the object origin (`:70-72`).
- `apps/editor/src/splats-transform-handler.ts:59-66`: when move/rotate/scale is active, `camera.focalPointPicked` moves the pivot to the picked point.
- `apps/editor/src/splats-transform-handler.ts:86` (`start()`): assigns palette entries to the selected splats; `:138` (`update()`): multiplies the pivot delta into the palette matrix; `:161` (`end()`): records the undo operation.
- `apps/editor/src/tools/transform-tool.ts:51`: the gizmo is attached to the pivot entity and is shown only when move/rotate/scale is active and there is a selection.
- `apps/editor/src/ui/transform.ts:57-60`: the Transform panel shows the pivot's world coordinates (`updateUI`); `:83` writes panel input back to the pivot.

**Rendering and SH**

- `apps/editor/src/shaders/projected-splat-projector-shader.ts:297`: `model = uniforms.model * paletteMatrix(...)`; `:315`: the covariance is transformed as `linear * rotationMatrix(rotation) * S`, so rotation affects the projected covariance.
- `apps/editor/src/shaders/projected-splat-projector-shader.ts:505-508`: the SH direction is first computed as the world direction from the camera to the object, then transformed into local coordinates (`localDirection`) through the transpose of the model matrix before evaluation. SH coefficients are stored in local coordinates, so the SH appearance rotates with the object, like lighting baked into it. Whether this is the intended behavior needs to be verified with a degree-3 SH scene.
- `apps/editor/src/splat-serialize.ts:187`: colours are stored in the order dc followed by f_rest.

## 3. Frame Rate

| State | FPS (Frame Rendering Stats) | Notes |
| --- | --- | --- |
| Idle | 0 | The editor renders on demand and does not redraw when idle (see the comment in `transform-tool.ts`) |
| Rotating the plant | 400–500 | User confirmed this is acceptable on the RTX 5080 |

Data comes from Chrome's Frame Rendering Stats. For stricter per-frame timing, supplement with the Performance panel.

## 4. Unfinished Tests

- **SH/lighting artifacts**: `Apartment.ply` is degree-0 SH, so view-dependent artifacts cannot be observed. A degree-3 SH scene with `f_rest_0..44` is needed.

## 5. Concrete Risks

**Risk: None**