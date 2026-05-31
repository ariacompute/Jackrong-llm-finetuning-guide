# Source Parity Audit

Private reference reviewed: `Qwopus3.5-9B-coder-GRPO.py` in the original
training project directory. The exact private filesystem path is intentionally
withheld from this public file.

Public implementation: `qwopus3_6_27b_gspo_training.py`

The public file preserves the training logic that matters for a beginner-facing
GSPO tutorial while replacing private paths, model names, dataset names, output
locations, and publishing targets with safe placeholders.

| Original feature | Public implementation | Preserved? | Changes made | Reason |
|---|---|---:|---|---|
| Model loading | Uses Unsloth `FastModel.from_pretrained` and `FastModel.get_peft_model` | Yes | Model changed to `Jackrong/Qwopus3.6-27B-v2`; dry-run keeps model loading disabled | Preserve working loader pattern while avoiding accidental 27B load |
| Tokenizer and chat template | Keeps chat-template-aware prompt formatting | Yes | Default template is `auto`; optional Unsloth template remains configurable | The target model ships a Qwen3-VL processor template |
| LoRA configuration | Keeps rank, alpha, dropout, bias, vision/language/attention/MLP toggles, gradient checkpointing | Yes | Public rank lowered from the private experimental setting | Conservative tutorial default |
| Dataset parsing | Supports Responses-style rows and public `messages`/`prompt`/`question` rows | Yes | Private dataset path and name removed | Preserve behavior without exposing private data |
| Prompt construction | Appends the answer-format instruction to the last user message | Yes | Wording generalized | Keep reward-compatible prompt behavior |
| Reward functions | Preserves formatting, anomaly, and correctness reward concepts | Yes | Comments rewritten for teaching; private dataset assumptions removed | Clarity and privacy |
| Answer extraction | Preserves answer tag, boxed answer, `Answer:`, angle answer, numeric, fraction, MCQA, and math normalization logic | Yes | Comments translated and generalized | Maintain reward fidelity |
| GSPO-specific behavior | Uses sequence-level importance sampling, `dr_grpo` loss, truncation masking, and multi-generation sampling | Yes | Public docs call this GSPO-style while keeping upstream `GRPOConfig`/`GRPOTrainer` API names | Avoid false API renaming |
| Trainer configuration | Keeps TRL config fields used by the reference workflow | Yes | Public defaults are smaller; `use_vllm=False` retained | Safe tutorial defaults |
| Checkpoint resume | Keeps `RESUME_FROM_CHECKPOINT` support | Yes | Environment-variable based | Beginner-friendly CLI workflow |
| Logging | Keeps console-oriented dry checks and optional report target | Partial | Completion debug logging simplified | Avoid noisy or private completion logs in public tutorial |
| Output paths | Exposes output and adapter directories | Yes | Replaced with `/workspace/example-user/...` placeholders | Remove private filesystem structure |
| Adapter save | Keeps LoRA adapter save with tokenizer | Yes | Guarded by `EXPORT_LORA_ADAPTER` | Safe default after real training |
| Merged 16-bit export | Adds local merged export section | Added | Uses Unsloth merged save when available, PEFT fallback otherwise | Required public export workflow |
| Hugging Face upload | Adds optional upload functions | Added | Disabled by default; uses `HF_TOKEN` environment variable | Required workflow without automatic upload |
| GGUF Q8_0 export | Adds local llama.cpp command workflow | Added | Disabled by default; prints commands unless explicitly enabled | Required public export workflow without accidental heavy work |
| `mmproj` handling | Adds inspection and documented export path | Added | Does not fabricate projector files; documents experimental llama.cpp `--mmproj` path | Target model is multimodal, but no prebuilt projector file was listed |
| Privacy sanitization | Removes private paths, usernames, dataset names, host aliases, and tokens | Yes | Uses placeholders and sample synthetic JSONL | Public release safety |

## GSPO Limitation Statement

The upstream TRL classes are still named `GRPOConfig` and `GRPOTrainer`.
Renaming those class references would break executable code. The public script
therefore describes the workflow as GSPO-style and documents the exact settings
that implement the behavior:

```python
importance_sampling_level = "sequence"
loss_type = "dr_grpo"
mask_truncated_completions = True
num_generations = 2
```

## Validation Performed

- Read the private reference script structure and reward functions.
- Checked installed package versions in the validation environment.
- Inspected TRL source fields for `importance_sampling_level`, `loss_type`,
  `mask_truncated_completions`, and `use_vllm`.
- Inspected `Jackrong/Qwopus3.6-27B-v2` model metadata and found Qwen3-VL
  processor metadata plus a vision config.
- Inspected llama.cpp converter help and quantizer help for GGUF and `mmproj`
  support.
- Ran syntax and lightweight dry-run checks after file creation.

## Unsupported or Environment-Specific Items

- The validation environment's direct TRL trainer import attempted to load a
  missing optional `vllm_ascend` module. This affects real trainer construction,
  not syntax, dataset parsing, or reward tests.
- The model repository did not list a prebuilt `mmproj` file during validation.
  The public script documents the verified experimental converter option rather
  than creating a placeholder projector.
