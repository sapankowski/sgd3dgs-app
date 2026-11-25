# SGD3DGS cross-repository ten-week work plan

## 1. How to use this plan

This is a side-project plan, not a full-time sprint plan.

- A project week begins on **Wednesday** and ends at the **Tuesday evening
  meeting**.
- A normal contribution is approximately **one focused day or two to three
  evenings, about 5–8 hours per person per week**.
- Tasks describe weekly outcomes. Nobody is expected to work every day.
- Each person has one core task. Stretch tasks are optional and begin only after
  the core task is reviewed.
- If a task will exceed the budget, reduce its scope. Do not silently add hours.
- At most three epics are active: editor/product, segmentation/data, and
  integration/research quality.

The plan coordinates two repositories:

- **App** (`sgd3dgs-app`): editor, canonical scene contract, validation,
  integration, and product release.
- **Research** (`sgd3dgs-research`): segmentation/completion methods, GPU
  environments, adapters, experiments, and evaluation artifacts.

The app owns this plan and the schema. Research produces versioned artifacts
against that schema. Implementation modules are not copied between repositories.

The plan assumes a six-person team that understands the idea of Gaussian
Splatting but has not previously built or modified a 3DGS system. Learning and
reproduction are therefore scheduled work rather than preparation people must do
privately.

## 2. Roles

Replace `Person 1` through `Person 6` with names at the first Tuesday meeting.
Roles describe primary responsibility, not exclusive ownership.

| Person | Primary role | Home repository | Secondary/review role |
|---|---|---|---|
| Person 1 | Editor and interaction | App | User workflow and documentation |
| Person 2 | Rendering and transform geometry | App | Editor performance review |
| Person 3 | Segmentation methods | Research | Method reproducibility |
| Person 4 | Scene data and reconstruction interfaces | Research; App for schema changes | Schema and adapter review |
| Person 5 | Completion and artifact research | Research | Segmentation cleanup early in the project |
| Person 6 | Integration, evaluation, and lightweight coordination | App coordination; Research evaluations | CI, release, and evidence quality |

Working pairs:

- **Product pair:** Persons 1 and 2.
- **Scene-understanding pair:** Persons 3 and 4.
- **Research-quality pair:** Persons 5 and 6.

Every significant PR is reviewed by someone outside the author's pair. Person 6
maintains the board and meeting notes, but coordination is capped at roughly one
hour per week so it does not consume their technical contribution.

### Issue routing

- Editor behavior, schema, validation, persistence, and packaging use App
  issues and PRs.
- Method setup, adapters, runs, metrics, data cards, and generated artifacts use
  Research issues and PRs.
- A cross-repository outcome has one lead issue and one linked integration
  issue. The project board tracks the lead issue only.
- Research output is accepted by the app only after passing the app validator.

## 3. Weekly operating rhythm

### Between meetings

Contributors choose their own working time. Useful asynchronous updates are:

- claim the task or issue when work starts;
- post evidence or a blocker when it changes the expected outcome;
- open a draft PR early if another task depends on its interface;
- request review at least several hours before the Tuesday meeting when
  practical.

There is no daily stand-up. A contributor who does not work on the project on a
particular day owes no status update.

### Tuesday evening meeting: 60–75 minutes

1. **5 minutes — scope check:** what shared milestone was expected?
2. **30 minutes — pair demonstrations:** up to ten minutes per pair, showing
   evidence rather than narrating activity.
3. **10 minutes — integration result:** does the week's end-to-end path work?
4. **10 minutes — decisions and risks:** accept/reject experiments and record
   blockers.
5. **10–20 minutes — next week:** assign one core outcome per person, reviewers,
   and dependencies.

The meeting ends with the board containing no ambiguous “work on X” tasks. Each
task has an output and a clear definition of done.

## 4. Definition of done

A weekly task is complete when:

- its promised output exists and fits the agreed scope;
- another person can reproduce or inspect it;
- relevant tests or exact commands are present;
- large artifacts are on shared storage with checksums;
- documentation and license/provenance notes are updated;
- the reviewer approves it;
- it can be demonstrated or summarized at the Tuesday meeting.

An installation attempt may be complete even if the method did not run, provided
the time box was respected and logs identify the blocker and next decision.

## 5. Preparation before Week 1

These are repository-owner/coordinator tasks, not six parallel technical tasks.
Complete them before the first Wednesday where possible:

- Follow [PUBLISHING.md](PUBLISHING.md) to publish both repositories under the
  configured `sapankowski` GitHub account and verify their cross-links.
- Invite all collaborators to both repositories and grant write access.
- Protect `main`, require one review, and enable squash merging in both.
- Create one coordination board with App/Research fields and this plan's weeks
  as milestones.
- Confirm access to one Ubuntu NVIDIA worker with at least 24 GB VRAM.
- Create a shared data directory and model-cache directory.
- Select one public reference scene and one target object.
- Place images, COLMAP data, and a pretrained PLY in shared storage.
- Run the documented full checks in both repositories once.

If GPU access or the reference scene is missing, Week 1 begins with setup only;
do not pretend the method comparison can proceed at the same time.

---

# Week 1 — Wednesday to Tuesday

## Theme: learn the stack and prove existing pieces

### Shared goal

Load one shared pretrained scene in SuperSplat, obtain one per-Gaussian object
mask from an existing method, import or simulate that mask in the editor, and
move the selected object. The first result may use an object list instead of a
click. The purpose is to expose interfaces and blockers, not create final code.

### Learning focus

Everyone should finish the week able to explain Gaussian order, a 3DGS PLY's
main attributes, how 2D masks are lifted to Gaussians, and why moving an object
creates background and appearance artifacts.

### Individual work

| Person | Core task, sized for 5–8 hours | Evidence due Tuesday |
|---|---|---|
| Person 1 | Build the unmodified editor, load the shared PLY, practice selection/transform/undo/export, and identify the smallest existing API path for selecting known Gaussian indices. Do not redesign the UI. | A short screen recording plus notes naming the relevant editor modules and one proposed integration point. |
| Person 2 | Inspect the editor's ID/depth picking, `GaussianInstances`, layers, and transform palette. Test manual translation and rotation on a selected subset and record frame rate, pivot behavior, and visible SH/lighting artifacts. | A technical note with code references, one transform recording, browser/GPU details, and two concrete risks. |
| Person 3 | Reproduce SAGA on the agreed scene or its smallest compatible fallback. Use click/multi-click selection and export a binary mask. Time-box installation to half the weekly budget before switching to a prepared upstream example. | Exact commit/environment/commands, runtime or failure log, mask path/checksum, selected count, and three rendered views. |
| Person 4 | Prepare the canonical reference-scene directory and version `0.1` manifest. Verify source PLY checksum and Gaussian count. Make source assets read-only and document where each file came from. | A valid `scene.json` on shared storage, data card, source checksum, and `./scripts/uv.sh run sgd3dgs validate --check-files ...` output. |
| Person 5 | Reproduce Gaussian Grouping on the same scene or its documented example, focusing only on object labels—not inpainting. Record nested dependencies and licensing. Time-box setup as for SAGA. | Commands/environment or failure log, output identity/mask format, runtime/VRAM if successful, and a short comparison note. |
| Person 6 | Own the week-1 experiment records, create a tiny mask statistics script/test if needed, and define a comparison sheet for SAGA versus Gaussian Grouping. Help connect whichever real mask is available to the editor without building an API. | Completed experiment records, selected-count/bounding-box/component statistics, and a five-minute Tuesday integration checklist. |

### Pair integration

- Persons 3–5 provide one mask whose length and ordering are understood.
- Person 4 confirms it is bound to the exact source PLY.
- Persons 1–2 load the selected subset or an equivalent converted layer and move
  it.
- Person 6 records what is real, what is simulated, and what failed.

### Core exit criteria

- Editor and core build from documented commands.
- One scene and target object are fixed for the next week.
- At least one method produces a usable mask, or a precisely documented blocker
  explains why not.
- The team can move a masked/manual subset and show leakage, holes, or remnants.
- A provisional baseline is chosen for Week 2.

### Optional stretch work

- Person 1 adds a temporary “select fixture object” development action.
- Person 6 calculates a rough spatial connected-component count.
- No one starts inpainting or object generation.

### Tuesday decision

Choose the first segmentation baseline using quality, reproducibility, runtime,
output compatibility, license implications, and ease of later gsplat migration.

---

# Week 2 — Wednesday to Tuesday

## Theme: freeze the first contracts and build a thin bridge

### Shared goal

Convert one real method output into canonical `object_id.npy`, validate it against
the scene manifest, load it in the editor, select the object by name, and move it
without duplicating the complete source scene.

### Starting point after Week 1

- The reference is `lerf-mask-ramen`; the target is the yellow bowl, excluding
  the food.
- The exact 1,266,122-Gaussian PLY, classifier, configuration and checksums are
  in the private
  [SGD3DGS artifact repository](https://huggingface.co/datasets/SGD3DGS/sgd3dgs-artifacts).
- Gaussian Grouping produced a documented real ramen result and exporter, but
  its arrays, logs and renders still need publication.
- SAGA produced a verified truck fallback. A matched SAGA ramen result and the
  final supported-baseline decision remain deferred.
- Persons 1–2 demonstrated the existing editor selection, transform and undo
  machinery. Week 2 should bridge labels into it, not redesign those controls.

Use Gaussian Grouping as the **provisional integration input** this week. This
is a practical bridge decision, not final baseline approval.

### Individual work

| Person | Core task, sized for 5–8 hours | Evidence due Tuesday |
|---|---|---|
| Person 1 | Add the smallest object list/panel that reads fixture metadata and selects one object. Reuse the documented `select.mask` path and existing transform/undo commands. Start with a fixture; use the real bowl package if it arrives in time. Do not add click picking yet. | PR and short recording: choose object → highlight → translate/rotate → undo. |
| Person 2 | Prototype an in-memory `object_id → Gaussian indices/ranges` structure and shared-data layer or selection-state path. Benchmark the fixture and, when labels arrive, the 1,266,122-Gaussian ramen scene. | Code, selection-time measurement and a recommendation for layer versus selection-state representation. |
| Person 3 | Reproduce the provisional Gaussian Grouping path from pinned ramen input to raw mask in a clean environment. Reuse Person 5's exporter; remove personal paths and do not create another exporter. | Exact command/config, clean-run log, raw-mask checksum and selected count. |
| Person 4 | Implement the raw-mask-to-`uint16` converter with source checksum, Gaussian-count and provenance checks. Add wrong-length and wrong-source tests, run it on the real output, and update the manifest. | Converter PR, tests, canonical `object_id.npy` and successful `--check-files` validation. |
| Person 5 | Publish the Gaussian Grouping native ID, confidence and mask arrays, receipt, logs and renders to Hugging Face. Then report distant spatial islands as conservative cleanup candidates without deleting data by default. | Artifact paths/checksums and a three-view before/candidate cleanup report. |
| Person 6 | Add manifest/converter integration fixtures and tests, verify published checksums, coordinate one clean-environment reproduction, and validate the complete real package when Persons 4–5 deliver it. Do not build another mask viewer. | CI result, reproduction notes, package-validation result and a short integration demo checklist. |

Persons 1–2 work from fixtures immediately and must not wait for the real
package. Persons 3–5 produce the artifact path; Person 6 validates it
independently. Large files and recordings go to Hugging Face, never GitHub.

### Core exit criteria

- A canonical label array exists and is tied to a source checksum.
- The real ramen package passes validation with file checks enabled.
- The editor selects an object from metadata without copying all Gaussian data.
- Translation, rotation, highlight, and undo use existing command machinery.
- The method and converter are separately reproducible.

### Optional stretch work

- Display the producing method and selected Gaussian count in the object panel.
- Visualize ambiguous/unknown labels with distinct colours.

### Tuesday decision

Approve scene schema 0.1 for the MVP. Any change after approval requires a small
ADR and migration discussion. If real artifacts arrive too late, demonstrate
the fixture bridge and converter tests rather than claiming real integration;
carry only the package run into Week 3. Click selection remains Week 3 work,
and generative repair remains scheduled for later weeks.

---

# Week 3 — Wednesday to Tuesday

## Theme: click-to-object selection MVP

### Shared goal

A click on an unambiguous visible part of the labelled object selects the whole
object locally in the browser. Selection should feel immediate and should not
call the Python service.

### Individual work

| Person | Core task | Evidence due Tuesday |
|---|---|---|
| Person 1 | Implement click mode, selection feedback, deselection, and a clear distinction between camera drag and click. Preserve existing editor controls. | Interaction PR and short usability recording. |
| Person 2 | Connect picked Gaussian index to `object_id`, then to all object ranges. Start with a single pixel and record failure cases; add a small weighted neighbourhood only if time permits. | Correct click selection on an agreed set of 20 easy clicks plus latency measurements. |
| Person 3 | Define positive/negative correction semantics and create ten difficult click cases at boundaries/occlusions for later testing. Do not build the correction UI yet. | Annotated click-case document and expected object IDs. |
| Person 4 | Make editor packaging preserve the manifest, object labels, coordinate system, and source identity. Document any import reordering performed by SuperSplat. | Round-trip report and explicit index-map strategy if reordering occurs. |
| Person 5 | Evaluate the cleanup spike against the difficult click/object cases. Recommend default-off, default-on, or rejection. | Adopt/reject report with visual evidence. |
| Person 6 | Add a repeatable interaction benchmark harness or manual protocol and collect p50/p95 click-to-highlight time and frame rate. | Metrics file and reproducible measurement instructions. |

### Core exit criteria

- At least 18/20 unambiguous clicks select the intended object.
- Click-to-highlight is below 100 ms on the reference machine.
- No network/model call occurs during click selection.
- Index reordering is either prevented or explicitly mapped.

### Optional stretch work

- Add 5×5 opacity/depth-weighted voting.
- Allow cycling through two ambiguous object candidates.

### Tuesday decision

Freeze the MVP picking algorithm. Boundary correction remains a Week-5 concern
unless easy clicks still fail.

---

# Week 4 — Wednesday to Tuesday

## Theme: supported segmentation pipeline

### Shared goal

Turn the chosen baseline from a Week-1 experiment into a documented pipeline
that produces canonical labels for a prepared scene. It may still run in an
isolated legacy container/environment.

### Individual work

| Person | Core task | Evidence due Tuesday |
|---|---|---|
| Person 1 | Add import/progress/error UI for a completed label artifact; avoid building a general job dashboard. | UI PR demonstrating success and one invalid-mask error. |
| Person 2 | Add label/selection visualization modes needed to inspect boundaries and unknown content without changing source colour permanently. | Three-view visual inspection recording and implementation tests where practical. |
| Person 3 | Package the chosen segmentation baseline as one documented entry command with pinned commits and external weights. | Clean-run evidence on the shared GPU and promoted legacy environment recipe. |
| Person 4 | Implement the supported adapter that converts method-specific output, records provenance, and updates a derived manifest rather than editing the source manifest in place. | Adapter PR, test fixture, and one real converted run. |
| Person 5 | Compare one inexpensive refinement method—spatial islands, confidence thresholding, or neighbourhood voting—against the raw baseline. Choose only one. | Quantitative/visual comparison and adopt/reject decision. |
| Person 6 | Create a smoke-test checklist for the complete offline pipeline and check license/weight documentation. | Reproduction report from prepared scene to editor-loadable labels. |

### Core exit criteria

- A new contributor can follow one document to produce labels.
- The process never overwrites the source PLY.
- Provenance includes method commit, configuration, source checksum, and runtime.
- The editor rejects mismatched counts/checksums clearly.

### Optional stretch work

- Expose a local command through the API only if the CLI path is already stable.
- Produce a small downloadable label-only fixture for CI.

### Tuesday decision

Decide whether to begin a clean gsplat implementation in Week 5/6 or retain the
legacy method as an offline adapter through the MVP.

---

# Week 5 — Wednesday to Tuesday

## Theme: correction, ambiguity, and multiple objects

### Shared goal

Support several objects in one scene and give users a bounded way to correct a
bad selection without retraining everything.

### Individual work

| Person | Core task | Evidence due Tuesday |
|---|---|---|
| Person 1 | Add object-list search/visibility plus a minimal correction interaction, such as add/remove Gaussian selection followed by “save as corrected object.” | Recording with one intentional correction and undo. |
| Person 2 | Harden neighbourhood click voting, ambiguity handling, and pivot computation against floaters. Keep thresholds visible/configurable. | Results on easy and difficult click suites with chosen defaults. |
| Person 3 | Produce labels for at least five objects and test multi-click behavior on two difficult objects. Record where additional clicks help or hurt. | Multi-object experiment and masks/provenance. |
| Person 4 | Version corrected labels as derived artifacts, preserving their parent method/run and a compact correction log. | Schema-compatible artifact record and tests. |
| Person 5 | Test one automatic repair operation: remote-island reassignment or merge of strongly overlapping split IDs. Never modify without a preview. | Before/after report and conservative acceptance rule. |
| Person 6 | Build a small segmentation scorecard: rendered-mask IoU where labels exist, object purity/coverage proxy, selected count, and correction effort. | Scorecard for five objects and a prioritized failure list. |

### Core exit criteria

- Five objects can be listed, selected, moved, hidden, reset, and distinguished.
- Corrections produce a new artifact and can be reverted.
- Automatic cleanup is previewable and provenance-preserving.
- The team knows the two most important segmentation failure modes to address.

### Optional stretch work

- Add keyboard navigation between objects.
- Add confidence visualization if the method provides meaningful confidence.

### Tuesday decision

Choose one segmentation-quality improvement for the release. Defer the rest to
research backlog instead of expanding the MVP indefinitely.

---

# Week 6 — Wednesday to Tuesday

## Theme: transform correctness and persistence

### Shared goal

Make translation and yaw rotation technically correct, pleasant to use, and
preserved across save/reload. This is the gate before completion work.

### Individual work

| Person | Core task | Evidence due Tuesday |
|---|---|---|
| Person 1 | Finalize transform UX: mode indicator, local/world choice if needed, undo/redo, numeric reset, and save/reload behavior. Keep controls minimal. | End-to-end editing recording and persistence test. |
| Person 2 | Verify means, Gaussian orientation/covariance, depth sorting, and view-dependent SH behavior under rotation. Implement or document the correct SH/local-view handling. | Technical test scene, before/after renders, and transform correctness note. |
| Person 3 | Run segmentation regression after transforms and quantify object remnants and background leakage on the five-object set. | Ghost/leakage report linked to label improvements, not transform code. |
| Person 4 | Compute robust pivot, OBB, floor-contact estimate, and scene-up handling. Protect against outliers and unknown scale. | Geometry utility tests and three-object pivot demonstration. |
| Person 5 | Define artifact-region masks produced by a move: vacated background, newly exposed object surface, and old baked shadow region. | Artifact taxonomy implemented as inspectable metadata or analysis script. |
| Person 6 | Measure interaction FPS, memory, click latency, save/load consistency, and deterministic undo. Add a release-blocking regression checklist. | Performance table and pass/fail checklist. |

### Core exit criteria

- Translation and yaw rotation preserve object shape.
- Rotation uses a plausible pivot and scene-up convention.
- Saving and loading reproduce the same object transforms.
- Undo restores exact prior state.
- Reference scene meets 30 FPS and 100 ms click targets on agreed hardware.

### Optional stretch work

- Floor snapping based on the computed contact point.
- Uniform scale only if every core criterion already passes.

### Tuesday gate

If transform correctness or persistence fails, Week 7 becomes a stabilization
week. Completion research does not begin on an unreliable editor.

---

# Week 7 — Wednesday to Tuesday

## Theme: controlled evidence and simple background repair

### Shared goal

Create a small controlled evaluation capture and implement the simplest credible
background-disocclusion baseline, preferring real observations over generation.

### Individual work

| Person | Core task | Evidence due Tuesday |
|---|---|---|
| Person 1 | Add a clear editor state for original versus repaired background and a toggle/overlay for the vacated region. | UI recording comparing original and baseline repair. |
| Person 2 | Produce depth/visibility renders and determine which background samples are genuinely observed in other views. | Reprojection visualization and technical assumptions. |
| Person 3 | Generate reliable removal masks for the controlled target object across the selected training views. | Mask set with quality notes and provenance. |
| Person 4 | Coordinate a minimal controlled capture: object at pose A, background empty, and object at pose B, with fixed scene markers and documented camera/registration procedure. | Dataset card, checksums, consent/privacy check, and usable aligned subset. |
| Person 5 | Implement a background baseline: reuse/reproject observed pixels or Gaussians first, then optionally a simple 2D inpainter only for unobserved pixels. | Baseline output, command/config, runtime, and failure examples. |
| Person 6 | Define masked PSNR/SSIM/LPIPS or simpler initial metrics against the empty-scene reference and prepare a consistent view set. | Baseline score report plus visual grid. |

### Core exit criteria

- A controlled example has an approximate empty-background reference.
- The project distinguishes observed reprojection from generated fill.
- Baseline repair can be toggled without altering the original scene.
- Evaluation focuses on the disoccluded region rather than the whole image.

### Optional stretch work

- Fine-tune background Gaussians from the baseline filled views.
- Capture a separate complete turntable/object scan if convenient.

### Tuesday decision

Select exactly one Week-8 generative experiment based on the dominant failure:
background inpainting or incomplete-object completion, not both.

---

# Week 8 — Wednesday to Tuesday

## Theme: one bounded generative experiment

### Shared goal

Evaluate one generative method against the Week-7 simple baseline. Integration is
not required unless it demonstrably improves the controlled case.

### Individual work

| Person | Core task | Evidence due Tuesday |
|---|---|---|
| Person 1 | Add only the comparison controls needed to inspect observed, baseline, and generated variants. Avoid a general model UI. | Side-by-side/toggle recording and clear generated-content indicator. |
| Person 2 | Align and render generated Gaussians/geometry without damaging observed content. Verify coordinate, scale, covariance, and sorting behavior. | Alignment report and multi-view render grid. |
| Person 3 | Prepare precise masks/conditioning views and ensure the generator is not asked to replace reliable observed surfaces. | Conditioning artifact record and mask quality check. |
| Person 4 | Package input/output provenance: views, poses, prompts, seed, model/checkpoint license, runtime, and alignment transform. | Complete experiment manifest and dataset linkage. |
| Person 5 | Lead one method only: preferably Inpaint360GS for background holes or TRELLIS for an object-completion hypothesis. Time-box setup and retain the Week-7 baseline as fallback. | Reproducible run or bounded failure report, multi-view outputs, and recommendation. |
| Person 6 | Compare against no repair and simple repair using regional metrics, consistency views, and a small blind team preference check. | Adopt/reject scorecard and risks. |

### Core exit criteria

- Generated content is visibly marked and removable.
- Observed source content remains intact.
- The experiment is reproducible or has a documented technical blocker.
- The team makes an evidence-based adopt/reject/defer decision.

### Optional stretch work

- Test one additional random seed to measure instability.
- Record generation latency and cache behavior.

### Tuesday decision

If the generative method does not clearly beat the simple baseline, do not force
it into the release. Publish the negative result and keep the simpler system.

---

# Week 9 — Wednesday to Tuesday

## Theme: hardening, packaging, and a second scene

### Shared goal

Make the supported path reproducible by another teammate and validate it on a
second scene without adding major features.

### Individual work

| Person | Core task | Evidence due Tuesday |
|---|---|---|
| Person 1 | Polish onboarding and the essential interaction path; fix the highest-impact usability issues only. | Fresh-user walkthrough with observed friction and fixes. |
| Person 2 | Profile renderer memory/FPS on both scenes and remove one demonstrated bottleneck or document a scene-size limit. | Before/after profile or supported-limit statement. |
| Person 3 | Run the supported segmentation pipeline on the second scene and document fallback/correction steps. | Second-scene label set and quality report. |
| Person 4 | Package both scenes with manifests and data cards; verify coordinates, checksums, and round-trip behavior. | `--check-files` validation and import/export report for both. |
| Person 5 | Harden the adopted repair path or, if none was adopted, document artifact handling and graceful warnings. | Stable repair command or user-facing limitations document. |
| Person 6 | Run clean-clone CI/setup reproduction, triage release blockers, and draft release notes. | Reproduction checklist, blocker board, and release candidate report. |

### Core exit criteria

- A clean clone builds editor and core from documented commands.
- Two scenes complete the supported segmentation-to-movement path.
- Known limits and hardware assumptions are explicit.
- No major feature is scheduled for Week 10.

### Optional stretch work

- Add a tiny downloadable demo fixture if licensing and size permit.
- Improve error messages encountered during fresh setup.

### Tuesday decision

Freeze release scope. Only bugs, documentation, tests, packaging, and demo work
enter Week 10.

---

# Week 10 — Wednesday to Tuesday

## Theme: release, demonstration, and honest evaluation

### Shared goal

Publish coordinated `v0.1.0` application and research-record releases that
another small team can install, understand, reproduce, and extend.

### Individual work

| Person | Core task | Evidence due Tuesday |
|---|---|---|
| Person 1 | Produce the final interaction demo and verify the README path from clone to moving an object. | Edited demo video/screenshots and approved user workflow. |
| Person 2 | Verify final transform/export correctness and record supported performance limits. | Signed-off transform/performance checklist. |
| Person 3 | Write the segmentation-method report: selected baseline, comparison evidence, correction requirements, and remaining failures. | Final research report with commands and citations. |
| Person 4 | Finalize scene/data documentation, provenance, and reproducible example instructions without publishing restricted/private assets. | Data cards and validated release manifests. |
| Person 5 | Write the completion/artifact report, including negative results and why methods were adopted, rejected, or deferred. | Final repair report and reproducibility links. |
| Person 6 | Coordinate final CI, license inventory, changelog, tag/release notes, issue triage, and the retrospective. | Green release checklist and draft/published `v0.1.0`. |

### Release criteria

- All supported paths are documented and tested.
- Licenses and model-weight terms are recorded.
- The editor performs local click selection and movement on labelled objects.
- Segmentation artifacts and disocclusion limitations are visible, not hidden.
- The release includes quantitative and qualitative evidence.
- No private or oversized assets are present in Git history.
- The backlog clearly separates supported improvements from research ideas.

### Tuesday release meeting

1. Run the demonstration from a clean clone or release artifact.
2. Review limitations and license inventory.
3. Approve or delay the tag based on the checklist, not the calendar.
4. Hold a short retrospective: keep, stop, start.
5. Decide whether the team wants another bounded cycle and at what sustainable
   time commitment.

---

## 6. Scope reduction rules

If the team falls behind, reduce scope in this order:

1. Drop optional generative integration; keep the experiment report.
2. Support one segmentation method rather than two.
3. Support translation and yaw only; omit scaling and arbitrary rotation.
4. Support one reference scene plus one smaller validation scene.
5. Use a documented CLI pipeline rather than building a job UI.

Do not remove checksum/order validation, provenance, undo, or generated-content
marking. Those are correctness and trust requirements, not polish.

## 7. Risk register and owners

| Risk | Early warning | Primary owner | Mitigation |
|---|---|---|---|
| Legacy CUDA method will not build | Half weekly budget spent without smoke test | Person 3 | Preserve logs, use prepared output, isolate environment, consider clean gsplat implementation |
| Labels map to wrong Gaussian order | Correct count but nonsensical spatial mask | Person 4 | Bind checksum/count, preserve or emit index map, add round-trip test |
| Click selects floaters/background | Boundary clicks produce inconsistent IDs | Person 2 | Depth/opacity-weighted neighbourhood vote and ambiguity handling |
| Object leaves ghosts or moves background | Visible remnants/leakage after transform | Person 3 | Correction tools, conservative cleanup, purity/coverage evaluation |
| Rotation looks wrong | Highlight/colour remains tied to old orientation | Person 2 | Correct covariance and SH/local-view handling; test scene |
| Completion hallucinates or overwrites truth | Observed surfaces change unexpectedly | Person 5 | Lock observed content, generated flag, baseline comparison, reject if needed |
| Side-project workload expands | Tasks roll over or contributors add hidden hours | Person 6 | One weekly core outcome, 5–8 hour budget, reduce scope at Tuesday meeting |
| Licensing blocks distribution | Nested dependency has restrictive terms | Person 6 | Maintain both `THIRD_PARTY.md` files, isolate research tools, prefer permissive components |

## 8. Coordinator checklist for every Tuesday

- Was the shared milestone demonstrated?
- Did each person's task stay within the intended side-project budget?
- Which outputs are merged, which are experiment-only, and which are discarded?
- Do any data/checksum/schema changes affect another pair?
- Were large files, private data, or model terms handled correctly?
- Is next week's shared goal one sentence and realistically achievable?
- Does every person have one core task, reviewer, output, and stopping condition?
- Are stretch tasks clearly optional?

The coordinator should protect the team's time. A smaller reproducible result is
better than six unfinished ambitious branches.
