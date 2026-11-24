# SGD3DGS App

**Interactive object-level editing for 3D Gaussian Splatting scenes.**

This repository contains the supported application: a browser editor for
selecting an object by clicking its visible Gaussians, moving or rotating that
object, undoing the edit, and saving the result. It also owns the canonical
scene/label contract used to exchange artifacts with the research pipeline.

The experimental GPU work lives in the separate
[`sgd3dgs-research`](https://github.com/sapankowski/sgd3dgs-research) repository. In a
local two-repository workspace it is expected at `../sgd3dgs-research`.

> Status: initial application scaffold. A pinned SuperSplat editor builds and
> passes its upstream test suite. The Python core validates versioned scene
> manifests. Object-ID import and click-to-object behavior are the first product
> changes planned for weeks 2–4.

## Responsibilities

This repository owns:

- the SuperSplat-based WebGPU editor and interaction design;
- local click picking, whole-object selection, transforms, and undo/redo;
- scene import/export and non-destructive edit persistence;
- the versioned scene manifest and per-Gaussian label contract;
- lightweight validation CLI and local application API;
- product tests, release packaging, and user documentation;
- the cross-repository ten-week coordination plan.

It does **not** contain segmentation training, legacy CUDA environments, model
weights, completion experiments, or large datasets. Those belong in the
research repository or shared artifact storage.

## Two-repository project

```text
SGD3DGS/                       # Local workspace; not a Git repository
├── sgd3dgs-app/               # This repository
│   ├── apps/editor/           # Pinned PlayCanvas SuperSplat snapshot
│   ├── services/core/         # Scene models, validation CLI, local API
│   ├── schemas/               # Authoritative versioned interchange contracts
│   ├── examples/              # Metadata-only fixtures
│   ├── containers/core/       # Optional local API image
│   ├── scripts/               # Setup and repository checks
│   └── docs/                  # Product architecture and team plan
└── sgd3dgs-research/          # Segmentation/completion research repository
    ├── adapters/              # Method-output conversion specifications
    ├── experiments/           # Small reproducible records
    ├── src/                   # Method-neutral research utilities
    ├── containers/            # Isolated legacy environment recipes
    └── data/                  # Ignored local/shared artifact layout
```

The application repository is the authority for schema `0.1`. The research
repository may produce artifacts against it but may not silently redefine it.
A contract change starts here, includes migration notes and tests, and is then
adopted by the research repository.

## Quick start

### Prerequisites

- Git 2.40+
- Node.js 20.19+ and npm
- Python 3.12
- [uv](https://docs.astral.sh/uv/)
- Chrome or Edge with WebGPU support

```bash
./scripts/check_system.sh
```

### Editor

```bash
make editor-install
make editor-test
make editor-dev
```
or
```bash
npm --prefix apps/editor run develop
```

Open `http://localhost:3000`. The initial editor is the pinned SuperSplat
baseline; project-specific object selection will be added in reviewed PRs.

### Python core

```bash
make core-sync
make core-check
make validate-sample
```

The uv wrapper deliberately uses `venv/` rather than `.venv/` to avoid a macOS
hidden-file issue that can disable editable installs in Desktop/Documents
folders.

### Full verification

```bash
make verify
./scripts/verify_repository.sh
```

## Application/research boundary

The research repository hands the app a derived scene package containing:

- an immutable source PLY/SOG checksum and Gaussian count;
- `object_id.npy` as `uint16[N]`;
- optional `confidence.npy` as `float32[N]`;
- object metadata and coordinate-system information;
- method, commit, configuration, and artifact provenance.

The app rejects mismatched counts, invalid checksums, unsafe paths, and unknown
schema versions. Interactive picking and dragging remain browser-local; they do
not wait for a model server or research environment.

See [the data contract](docs/DATA_FORMAT.md) and
[architecture](docs/ARCHITECTURE.md).

## Team workflow

The project is designed for six contributors working approximately one focused
day or two to three evenings per week. A project week runs Wednesday through
the Tuesday evening meeting. There is no daily work expectation.

The complete role assignment, one bounded task per person per week, integration
gates, and release criteria are in the
[ten-week work plan](docs/WORK_PLAN.md).

Use the app issue tracker for UI, schema, product integration, and release work.
Use the research issue tracker for method reproduction, GPU environments,
experiments, metrics, and completion work. Cross-repository work has one lead
issue and links the dependent issue instead of duplicating status.

## Documentation

- [Getting started](docs/GETTING_STARTED.md)
- [Publishing both repositories](docs/PUBLISHING.md)
- [Ten-week team work plan](docs/WORK_PLAN.md)
- [Application architecture](docs/ARCHITECTURE.md)
- [Scene and label contract](docs/DATA_FORMAT.md)
- [Contributing](docs/CONTRIBUTING.md)
- [Glossary](docs/GLOSSARY.md)

## Data and security

Do not commit trained scenes, captures, label arrays, generated Gaussians,
weights, tokens, or private URLs. The editor loads local assets; real artifacts
remain on approved shared storage. See [SECURITY.md](SECURITY.md).

## Upstream and licensing

`apps/editor` is a source snapshot of
[PlayCanvas SuperSplat](https://github.com/playcanvas/supersplat), pinned and
attributed in [apps/editor/UPSTREAM.md](apps/editor/UPSTREAM.md). SuperSplat and
new SGD3DGS App code are MIT-licensed. Research baselines retain their own terms
and are inventoried in the research repository rather than bundled here.

## First milestone

> On one reference scene, import a real per-Gaussian object label artifact,
> click the object in the browser, move and rotate it, undo the edit, reload the
> saved state, and document visible artifacts.

Completion and generative repair remain research outputs until this path is
correct and reproducible.
