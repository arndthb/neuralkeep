# Local Qwen inference on Apple Silicon

This guide explains how to run a **local Qwen model using MLX** and connect it to a **Karakeep container**. The inference server runs **natively on macOS**, while Karakeep runs in Docker.

> [!NOTE]
> This is an experimental integration. The reference setup has demonstrated API reachability from Docker, but **automatic tagging has not yet been verified end-to-end**. Commands below describe a reproducible starting point, not a tested one-click installer.

## Why MLX instead of putting everything in Docker?

[MLX](https://github.com/ml-explore/mlx) is optimised for Apple Silicon. Running the model natively on macOS gives it direct access to the host's Apple Silicon acceleration and unified memory. The application containers can remain isolated in Docker.

| Component | Where it runs | Purpose |
|---|---|---|
| Karakeep, Chromium, Meilisearch | Docker Compose | Bookmark capture and search |
| MLX-LM | macOS Python virtual environment | Model inference |
| Qwen | Downloaded model weights on the Mac | Text generation |
| Tailscale | Host network | Private remote access |

## 1. Prerequisites

- An Apple Silicon Mac with sufficient available unified memory
- Python and `venv` (Python 3.12 is a practical starting point)
- Docker Desktop running Karakeep
- Internet access for the initial model download
- Enough free disk space for model weights and cache

Model sizes depend on the selected quantisation. For example, the reference environment uses a **4-bit Qwen3 30B-A3B model** occupying roughly **16 GB of model files**. Leave additional memory for macOS and other applications.

## 2. Install MLX-LM in a virtual environment

In a macOS terminal:

```bash
mkdir -p ~/AI/neuralkeep-mlx
cd ~/AI/neuralkeep-mlx

# Use an installed Python 3.12 interpreter.
python3.12 -m venv .venv
source .venv/bin/activate

python -m pip install --upgrade pip
python -m pip install mlx-lm
python -m pip show mlx-lm
```

If `python3.12` is unavailable, install a compatible Python version first. Keep MLX-LM in a virtual environment rather than modifying the system Python.

## 3. Choose a Qwen model

The reference setup uses the model identifier:

```text
mlx-community/Qwen3-30B-A3B-4bit
```

Model identifiers are exact strings. Check the model's current availability and requirements on [Hugging Face](https://huggingface.co/mlx-community).

A smaller quantised model may be preferable on Macs with less unified memory. The first run typically downloads model files.

## 4. Start an OpenAI-compatible inference endpoint

MLX-LM provides an HTTP server entry point. In the activated virtual environment, check the flags supported by your installed version:

```bash
python -m mlx_lm.server --help
```

For versions supporting these flags, a typical local launch is:

```bash
python -m mlx_lm.server \
  --model mlx-community/Qwen3-30B-A3B-4bit \
  --host 0.0.0.0 \
  --port 8091
```

**Security:** `0.0.0.0` listens on all host interfaces. It is shown because a Docker container may need to reach the host endpoint. Restrict access with your host firewall and trusted networks; do **not** forward port 8091 to the internet. If your Docker networking supports a narrower bind, prefer it. The example API key below is not an authentication mechanism for an unauthenticated local server.

Some installations use a different OpenAI-compatible wrapper around MLX rather than the built-in `mlx_lm.server`. In that case, use that wrapper's documented start command and keep the Karakeep API URL consistent.

## 5. Verify the server independently

On the Mac:

```bash
curl -fsS http://127.0.0.1:8091/v1/models
```

**Listing models is not enough.** Send a real completion request:

```bash
curl -sS http://127.0.0.1:8091/v1/chat/completions \
  -H 'Content-Type: application/json' \
  -d '{
    "model": "mlx-community/Qwen3-30B-A3B-4bit",
    "messages": [{"role": "user", "content": "Reply with exactly: NeuralKeep ready"}],
    "max_tokens": 80
  }'
```

Check the response for an actual assistant message. Some wrappers have different model identifiers or API behaviour; use the model ID returned by your own server.

## 6. Connect Karakeep from Docker

Place these settings in your **private, untracked** Karakeep `.env` file:

```dotenv
OPENAI_API_KEY=local
OPENAI_BASE_URL=http://host.docker.internal:8091/v1
INFERENCE_TEXT_MODEL=mlx-community/Qwen3-30B-A3B-4bit
```

On Docker Desktop for macOS, `host.docker.internal` resolves to the host. The port `8091` is the **macOS inference endpoint**, not the Karakeep web port.

> [!IMPORTANT]
> Adding variables to `.env` does not guarantee that the Compose file passes them to the application. Inspect the **effective** configuration and confirm the variables are available to the Karakeep web service.

```bash
docker compose config
```

**Do not paste the full output into public issues**: Compose output can include secrets. Redact values before sharing.

After any long-running import has finished, restart or recreate the relevant Karakeep service **according to your deployment's Compose setup** so the new environment takes effect.

## 7. Check connectivity from inside the container

Use your actual service name; the reference deployment calls it `web`:

```bash
docker compose exec web sh
```

Inside the container, use whichever HTTP client is installed to request:

```text
http://host.docker.internal:8091/v1/models
```

If no HTTP client is present, use a temporary diagnostic container on an appropriate Docker network instead of installing debugging tools into a production image.

## 8. Verify Karakeep tagging

1. Save a **non-sensitive** test bookmark.
2. Trigger the supported automatic tagging flow in your Karakeep version.
3. Check that tags are actually created.
4. Review the web service logs for model/API errors.
5. Only consider bulk tagging after a successful end-to-end test.

The exact configuration names and tagging behaviour may change across Karakeep releases; consult [upstream documentation](https://docs.karakeep.app/) for your installed version.

## Troubleshooting

| Symptom | What to check |
|---|---|
| Connection refused | Server running? Correct port and bind address? |
| `/v1/models` works, tagging fails | Test `/v1/chat/completions`; check model ID and response compatibility |
| Host can connect, container cannot | Docker Desktop host routing, firewall, `host.docker.internal` |
| Out-of-memory / slow responses | Smaller quantised model, free unified memory, reduce concurrent inference |
| Variables ignored | Effective `docker compose config`, container environment, controlled restart |
| Authentication redirects fail on phone | Karakeep `NEXTAUTH_URL` and the actual access URL |

## Reference deployment status

- **Observed:** an MLX-compatible endpoint reachable on host port 8091; `/v1/models` accessible from Docker.
- **Not yet confirmed:** successful completion generation through the same endpoint and automatic tagging inside Karakeep.

## Further reading

- [MLX-LM source and usage](https://github.com/ml-explore/mlx-lm)
- [MLX framework](https://github.com/ml-explore/mlx)
- [Karakeep documentation](https://docs.karakeep.app/)
- [Qwen model catalogue for MLX](https://huggingface.co/mlx-community)
