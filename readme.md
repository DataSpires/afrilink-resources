# AfriLink Resources

Notebooks and reference artifacts for the [AfriLink SDK](https://pypi.org/project/afrilink-sdk/).

AfriLink gives one-line access to a dedicated **NVIDIA A100 80 GB** hosted by [OpenToken](https://opentoken.global) for **training and finetuning** across text, vision and multimodal models. Runs from Google Colab, Kaggle, Jupyter, VS Code, or any Python environment.

| Resource | What it covers |
|----------|----------------|
| [AfriLink SDK End-to-End Demo — [A100 Backend]](afriLinkDemo%20-%20%5BA100%20Backend%5D.ipynb) **(current)** | Full v0.8.x workflow on the OpenToken A100: stateless API-key auth → browse the model/dataset registry → prepare a small Alpaca-style DataFrame → submit a QLoRA `client.finetune()` job on the A100 → download adapter weights → load with `PeftModel` and test → **new**: `client.find_existing_image()` + `client.build_and_train()` for custom containers with hash-based cache → merge, convert to GGUF and deploy locally with Ollama. |
| [AfriLink SDK End-to-End Demo](afriLinkDemo.ipynb) *(legacy — CINECA backend)* | Same workshop content against the legacy CINECA Leonardo SLURM backend with interactive DataSpires email/password auth (`afrilink-sdk` 0.7.x). Kept for users running pre-v0.8 SDKs; the `[A100 Backend]` variant above is the recommended path for all new runs. |

## Getting started

```bash
pip install 'afrilink-sdk[build]'
```

The `[build]` extras are needed for the custom-container path (`cryptography` + `requests`). Without them, only the curated `client.train()` and `client.finetune()` paths work.

You'll need a [DataSpires](https://dataspires.com) account and an AfriLink API key:

1. Sign up at [dataspires.com](https://dataspires.com)
2. Mint a key at **Profile → AfriLink SDK keys → Create new key**
3. Add it to your notebook as a secret named `AFRILINK_API_KEY`
   - **Colab**: 🔑 sidebar → Add secret → name `AFRILINK_API_KEY`, paste, enable for notebook
   - **Kaggle**: Add-ons → Secrets → name `AFRILINK_API_KEY`, paste, attach to notebook
   - **Local Jupyter / VS Code / scripts**: `os.environ["AFRILINK_API_KEY"] = "afk_live_…"` before `client.authenticate()`

`client.authenticate()` then reads the key, exchanges it at `api.dataspires.com` for a short-lived session, and probes the A100 — all in ~1–2 seconds. No email/password prompt, no SSH certificate refresh.

Jobs run on the OpenToken A100 ([opentoken.global](https://opentoken.global)) and are billed at **$2.00 / GPU-hour wall-clock** (1-minute floor) against your DataSpires balance. Build-time on Cloud Build is absorbed by the platform — you only pay GPU-time. Invoices appear on the [Billing dashboard](https://dataspires.com/dashboard/billing) in real time.

## Capabilities at a glance (v0.8.x)

| API | Use case |
|-----|----------|
| `client.train()` | Run any training script in a curated container (`afrilink-yolo` for Ultralytics) on the A100 |
| `client.finetune()` | LoRA / QLoRA LLM fine-tuning with one line (`low` / `medium` / `high` training modes) in the curated `afrilink-finetune` container |
| `client.find_existing_image()` | Check the A100 + Artifact Registry for a matching cached custom image before triggering a ~5 min Cloud Build (hash-based, exact-match) |
| `client.build_image()` | Define `base_image` + `pip_packages` + `apt_packages` + `model_source` (HuggingFace / URL / git / GCS / S3), build on Cloud Build, push to a private Artifact Registry |
| `client.build_and_train()` | One-shot: cache-check → build (or skip) → run on the A100 → ephemeral cleanup |
| `client.download_model()` | Pull the `output/` directory back from the A100 for `PeftModel.from_pretrained()` |
