# AGENTS.md — pod-unsloth-studio

Standalone candy repo for the `unsloth-studio` candy — the Unsloth Studio web UI
for GPU LLM fine-tuning, backed by a pixi PyTorch/transformers environment and
composed llama.cpp GGUF export tools. The candy lives in `charly.yml` at the repo
root plus its pixi environment.

Canonical files:

- `charly.yml` — the `unsloth-studio:` candy entity (description, `require`,
  `candy`, `distro`, `env`, `port`, `volume`, `service`, `plan`) and its `skill:`
  entity.
- `pixi.toml`, `pixi.lock` — the fine-tuning Python environment.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-jupyter:unsloth-studio` — the owning skill: the candy properties, the
  candy composition, and the Studio service. Load before editing, building,
  deploying, or troubleshooting this candy.
- `/charly-jupyter:llama-cpp` — the composed GGUF export tools.
- `/charly-jupyter:unsloth` — the composed vLLM + unsloth fine-tuning stack.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `run:` / `check:`, service declarations).
- `/charly-check:check` — the check/R10 framework (`charly check box`,
  `charly check run <bed>`).
- `/charly-pod:pod` — the `kind: pod` / deploy schema reference (this candy is
  composed into a box; ports, volumes, services).
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The candy's own `check:` steps assert the `unsloth` launcher, the pixi Python
  interpreter, the PyTorch import, the composed `~/llama.cpp/llama-cli`, the
  fine-tuning stack, and — at deploy scope — the running service and reachable
  UI port.

## Modify this repo

- Edit the `unsloth-studio:` candy entity in `charly.yml`; the `skill:` entity in
  the same file is the owning skill's source — a candy change and its skill
  change land together.
- The `unsloth` CLI comes from the official curl installer, **not** the pixi env;
  keep the install step and the `unsloth-cli-present` check in step.
- The Studio installer hard-requires `cmake` + `git` + a C/C++ toolchain on Arch
  (`base-devel`); keep the `distro.arch` block in step with that requirement.
- The `skill:` entity is the source for `/charly-jupyter:unsloth-studio`; never
  edit the generated `SKILL.md` — regenerate it.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
