# AfriLink Resources

Notebooks and reference artifacts for the [AfriLink SDK](https://pypi.org/project/afrilink-sdk/).

AfriLink gives one-line access to A100 GPUs for **training and finetuning** across text, vision and multimodal models, runnable from Google Colab, Kaggle, Jupyter, VS Code, or any Python environment.

| Resource | What it covers |
|----------|----------------|
| [AfriLink SDK End-to-End Demo](afriLinkDemo.ipynb) | Full finetune workflow: install → authenticate with DataSpires → browse the model/dataset registry → prepare a small Alpaca-style DataFrame → submit a QLoRA `client.finetune()` job to A100s → download adapter weights → load with `PeftModel` and test → merge, convert to GGUF and deploy locally with Ollama. |

## Getting started

```bash
pip install afrilink-sdk
```

All notebooks assume a [DataSpires](https://dataspires.com) account — `client.authenticate()` prompts for credentials, then auto-handles the HPC SSH certificate flow. Jobs run on the CINECA Leonardo Booster partition (4× A100 per node) and are billed at $2.00 / GPU-hour against your DataSpires balance.

## Capabilities at a glance

| API | Use case |
|-----|----------|
| `client.train()` | Run any training script (YOLOv8, custom PyTorch, etc.) on HPC A100s via the container registry |
| `client.finetune()` | LoRA / QLoRA LLM fine-tuning with one line (`low` / `medium` / `high` training modes) |
| `client.download_model()` | Pull trained adapter weights back to the notebook for `PeftModel.from_pretrained()` |
| `client.recover_session()` | Re-auth after the 12-hour SSH cert expires, check tracked jobs, download finished models |
