# pod-unsloth-studio

The `unsloth-studio` candy of the OpenCharly candy library, as a standalone repo
(the candy de-submodule cutover, kind-prefixed naming). It provides the Unsloth
Studio web UI for GPU LLM fine-tuning, backed by a pixi PyTorch/transformers
environment and composed llama.cpp GGUF export tools.

## What it provides

Owns the pixi fine-tuning environment (Python 3.13 + PyTorch/CUDA + transformers
+ PEFT/TRL), composes the `llama-cpp` and `unsloth` candies, and runs the Unsloth
Studio web UI as a supervisord service on port 8888. The official installer
(`https://unsloth.ai/install.sh`) lands the `unsloth` launcher at
`~/.local/bin/unsloth` and bootstraps the studio runtime venv at build time.

| Property | Value |
|---|---|
| Requires | `layer-cuda`, `layer-supervisord` |
| Composes | `layer-llama-cpp`, `layer-unsloth` |
| Ports | `8888` (Studio UI), `8000` (vLLM API) |
| Volume | `workspace` → `/workspace` |
| Service | `unsloth-studio` (`~/.local/bin/unsloth studio -H 0.0.0.0 -p 8888`, `restart: always`) |
| Env | `NVIDIA_PYTHON_PROJECT=~/.pixi`, `LD_LIBRARY_PATH=/usr/lib64:$HOME/llama.cpp` |

Every owned artifact lands at a fixed path — the pixi interpreter at
`~/.pixi/envs/default` and the composed llama.cpp CLI at `~/llama.cpp` — so the
environment and the composition's effect are directly checkable.

## How to use it

```yaml
my-image:
  candy:
    - '@github.com/opencharly/pod-unsloth-studio:<tag>'
```

```bash
charly box build my-image
charly start my-image
# Open http://localhost:8888
```

## Verification

The candy's `check:` plan asserts the `unsloth` launcher, the pixi Python
interpreter, that PyTorch imports, the composed `~/llama.cpp/llama-cli` binary,
the transformers/PEFT/TRL fine-tuning stack, and — at deploy scope — the running
`unsloth-studio` service and a reachable `127.0.0.1:${HOST_PORT:8888}`.

## Layout

- `charly.yml` — the `unsloth-studio:` candy entity (description, `require`,
  `candy`, `distro`, `env`, `port`, `volume`, `service`, `plan`) plus its
  `skill:` entity.
- `pixi.toml`, `pixi.lock` — the fine-tuning Python environment.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-jupyter:unsloth-studio` — the candy properties, the
  candy composition, and the Studio service.
- `/charly-jupyter:llama-cpp` — the composed GGUF export tools.
- `/charly-jupyter:unsloth` — the composed vLLM + unsloth fine-tuning stack.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
