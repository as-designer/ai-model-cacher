# AI Model Cacher

[![CI Test Action](https://github.com/as-designer/ai-model-cacher/actions/workflows/test-action.yml/badge.svg)](https://github.com/as-designer/ai-model-cacher/actions/workflows/test-action.yml)
[![Marketplace](https://img.shields.io/badge/marketplace-ai--model--cacher-blue.svg)](https://github.com/marketplace/actions/ai-model-cacher)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A lightweight GitHub Action designed for **Local-First, Edge AI, and LLM developers**. This action intelligently downloads, hashes, and caches large model weights (`.gguf`, `.safetensors`, `.onnx`, `.bin`) directly on GitHub's internal cache network, slashing your CI/CD pipeline runtimes and saving external network bandwidth.

## 🚀 Why Use This?

- **Zero-Bust Caching:** Generates deterministic SHA-256 hashes of your model URLs. If your model source changes, the cache automatically busts and updates.
- **Massive Time Savings:** Skips external data transfers (e.g., Hugging Face, automated registries) on cache hits, downloading directly from GitHub’s high-speed internal cache infrastructure.
- **Directory Auto-Creation:** Safely creates target folders (`mkdir -p`) automatically before attempting downloads.

---

## 🛠️ Usage

Add this step to your `.github/workflows/test.yml` pipeline before running your AI integration or inference tests.

```yaml
name: CI Inference Tests
on:
  workflow_dispatch: # To manually run workflow

jobs:
  run-tests:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      # 1. Setup and cache your heavy model weights
      - name: Fetch and Cache LLM Weights
        id: ai-cache
        uses: as-designer/ai-model-cacher@v1
        with:
          model-url: 'https://huggingface.co'
          model-path: './models/phi3.gguf'

      # 2. Verify cache status (Optional)
      - name: Log Cache Status
        run: echo "Was cache restored? \${{ steps.ai-cache.outputs.cache-hit }}"

      # 3. Execute your tests locally using the cached binary
      - name: Run Python Inference Tests
        run: |
          pip install -r requirements.txt
          pytest tests/test_llm.py --model-path ./models/phi3.gguf
```

---

## 🎛️ Configuration Reference

### Inputs

| Input | Description | Required | Default |
| :--- | :--- | :---: | :--- |
| `model-url` | The direct URL to download the model weights file. | **Yes** | — |
| `model-path` | The local relative path where the model file will be saved. | **Yes** | — |
| `cache-key-prefix` | Custom prefix prepended to the generated SHA-256 key. | No | `'ai-model'` |

### Outputs

| Output | Description |
| :--- | :--- |
| `cache-hit` | Returns string `'true'` if restored from GitHub cache, or `'false'` if it had to download fresh. |

---

## ⚠️ Notes & GitHub Cache Limits

- **GitHub Cache Size Limit:** GitHub provides up to **10 GB** of total cache storage per repository. Ensure your target models fit within this limit alongside your standard dependency caches (like `node_modules` or `pip`).
- **Cache Eviction:** GitHub automatically evicts caches that haven't been accessed in 7 days.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).
