## Finetuning Llama 3.1 (8B) with Unsloth on MEDIQA for Medical Question-Answering. 

### Intent

This project fine-tunes Llama 3.1 (8B) for medical question answering using MEDIQA dataset and evaluates whether supervised finetuning improves model accuracy versus the base model. The notebook runs an A/B comparison: Before Finetuning vs After Finetuning on the same test subset.

### Dataset used

- **Source**: Lavita AI medical-qa-datasets → subset `medical_meadow_mediqa` (≈2.21k rows)
- **Link**: [Hugging Face dataset viewer](https://huggingface.co/datasets/lavita/medical-qa-datasets/viewer/medical_meadow_mediqa?views%5B%5D=medical_meadow_mediqa)
- **Split strategy** (random, seed=42):
  - Train: 80% → 1,766 rows (downsampled to 200 for Colab constraints)
  - Validation: 10% → 221 rows (downsampled to 50)
  - Test: 10% → 221 rows (downsampled to 50)

### Before Finetuning

- **Model**: `unsloth/Meta-Llama-3.1-8B` loaded via Unsloth in 4-bit quantization.
- **Prompting**: Alpaca-style prompt template.
- **Generation settings**: `max_new_tokens=200`, `do_sample=False`, `temperature=0.0`.
- **Evaluation set**: 50 examples from the test subset.
- **Output artifact**: `before-finetuning.xlsx` (50 rows with prompt, ground-truth output, and model prediction).

### After Finetuning

- **Finetuning framework**: Unsloth + LoRA (PEFT) + TRL SFT.
- **Evaluation set**: same 50 test examples.
- **Output artifact**: `after-finetuning.xlsx` (50 rows with prompt, ground-truth output, and finetuned model prediction).

### Evaluation

Used ChatGPT to label each prediction as correct (1) or incorrect (0) against the ground-truth output, using these criteria:

- Core meaning preserved
- No contradictions with ground truth
- Completeness relative to the question (concise is fine if essentials are covered)
- Tone consistency (avoid inappropriate disclaimers if the reference is professional)
- Readable and not cut off

Metrics computed from ChatGPT-labeled files `before-finetuning-eval.xlsx` and `after-finetuning-eval.xlsx`:

- Before finetuning: Accuracy 0.18, Precision 1.00, Recall 0.18, F1 ≈ 0.305
- After finetuning: Accuracy 0.32, Precision 1.00, Recall 0.32, F1 ≈ 0.484

Overall, accuracy improved by 14 percentage points (relative ≈+78%).

### Tools used

- **Unsloth**: Fast loading/inference and PEFT helpers (4-bit quantization) for Llama 3.1 8B
- **TRL**: `SFTTrainer` for supervised finetuning
- **PEFT (LoRA)** via Unsloth
- **Hugging Face Datasets** for loading/splitting
- **Pandas** for saving results to Excel
- **scikit-learn** for metric computation

### Links

- Unsloth reference notebook (Llama 3.1 (8B) Alpaca):
  https://colab.research.google.com/github/unslothai/notebooks/blob/main/nb/Llama3.1_(8B)-Alpaca.ipynb
- Dataset (Lavita AI → medical_meadow_mediqa):
  https://huggingface.co/datasets/lavita/medical-qa-datasets/viewer/medical_meadow_mediqa?views%5B%5D=medical_meadow_mediqa
- Colab notebook to run :
  <https://colab.research.google.com/drive/1ZawPYhN9ym9MTb6tva-Tzhq5l7vOxOqI?usp=sharing>
