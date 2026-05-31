# Privacy Audit

This audit covers the public GSPO tutorial folder only. The original private
training script was read as a reference and was not modified.

## Sensitive Categories Searched

- Real personal names and local usernames discovered during source inspection.
- Private project directory names.
- Private machine paths.
- Private dataset names.
- Server aliases and hostnames.
- Email addresses.
- Hugging Face access-token patterns.
- WandB API-key patterns.
- Shell environment files and logs.
- Large model weights, checkpoints, caches, and temporary files.

## Values Replaced

| Source category | Public replacement |
|---|---|
| Private local model path | `Jackrong/Qwopus3.6-27B-v2` |
| Private dataset path | `examples/sample_dataset_format.jsonl` or `/workspace/example-user/datasets/gspo_train.jsonl` |
| Private output path | `/workspace/example-user/outputs/qwopus3.6-27b-gspo` |
| Private Hugging Face repo targets | `your-hf-username/...` placeholders |
| Private WandB project naming | `qwopus3.6-27b-gspo-public-example` |
| Private data rows | Synthetic JSONL examples |
| Private training labels | Public GSPO terminology |

## Scan Commands Used

The final validation used commands equivalent to the following, with private
strings supplied from the source-inspection notes and redacted here:

```bash
grep -RInE '<private-name>|<private-username>|<private-project-root>|<private-dataset-name>|<private-server-alias>|<home-path-pattern>|<email-regex>' public_gspo_qwopus3.6_27b_example
grep -RInE '<huggingface-token-regex>|<wandb-key-regex>' public_gspo_qwopus3.6_27b_example
find public_gspo_qwopus3.6_27b_example -type f -size +1M -print
find public_gspo_qwopus3.6_27b_example -type f \\( -name '*.safetensors' -o -name '*.bin' -o -name '*.gguf' -o -name '*.pt' -o -name '*.log' \\) -print
```

## Result

No real secrets, private filesystem paths, private dataset rows, server aliases,
email addresses, access tokens, WandB keys, model weights, checkpoints, logs, or
temporary files are intentionally present in the public folder.

The public files contain generic placeholders such as `/workspace/example-user`
and `your-hf-username`. These are examples, not private values.

## Original File Preservation

The private reference file was not overwritten, deleted, moved, or edited. The
public tutorial was created in a separate release folder.

## Remaining Manual Review Checklist

- Confirm the target repository branch is correct before pushing.
- Re-run the privacy scan inside the GitHub working tree after copying files.
- Confirm only `README.md` and the public tutorial folder are staged.
- Confirm no weights, checkpoints, logs, caches, or generated model files are
  staged.
- Confirm all heavy training, export, and Hugging Face upload flags remain
  disabled by default.
