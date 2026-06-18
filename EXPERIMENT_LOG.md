# TableVerse x Gemma 4 Experiment Log

Append one numbered entry per experiment. Each entry should include setting, exact command, outputs, results, and notes.

## Benchmark: TABVERSE

TABVERSE is a controlled multimodal table benchmark designed to isolate representation effects. The same table content is aligned across multiple structural formats and rendered images, so format can change while table semantics stay fixed.

Source: https://arxiv.org/abs/2606.09578

Why it matters here:

This makes it a good fit for interpretability analysis because hidden-state differences can be compared across representations of the same underlying table rather than across different table contents.

Tasks:

- Question Answering (QA): Answer natural-language questions using the table. This is the task used in Experiments 000 and 001.
  - Size: 700 question-table pairs in the QA test split used here.
  - Metric: Exact-match accuracy in the benchmark; our runner also keeps predictions and activations for interpretability.
- Structural Understanding Capability (SUC): Test table-structure understanding such as boundaries, rows, columns, headers, and cell access.
  - Size: 629 unique-table structural understanding examples.
  - Metric: Exact-match accuracy.
- Structure Reconstruction (SR): Reconstruct or generate table structure from another representation.
  - Size: Not run in this phase.
  - Metric: Structural similarity metrics.

Formats:

- text: HTML, Markdown, LaTeX
- image: rendered HTML table image, rendered Markdown table image, rendered LaTeX table image

Metadata:

- question category
- question difficulty
- source dataset
- score

Scope in this project:

- Experiments 000 and 001 use only the QA split.
- Experiments 000 and 001 use only text formats, not rendered images.
- For each QA example, Gemma sees the same question and same table content while only the table representation changes.
- Hidden-state vectors are saved so that behavioral format sensitivity can be linked to internal representation geometry.

## Experiment 000: Smoke QA Text Formats

Status: completed

Date: 2026-06-18

Purpose:

Verify the end-to-end TableVerse QA pipeline with Gemma 4 12B on a tiny text-format subset, including prediction output and selected hidden-state activation saving.

Setting:

| Field | Value |
|---|---|
| Cluster | `HAIC interactive GPU session` |
| Node | `haic-hgx-4` |
| GPU | `NVIDIA H100 80GB HBM3` |
| Project root | `/hai/scratch/fangruih/tableverse_gemma/structure_data` |
| Conda env | `/hai/scratch/fangruih/tableverse_gemma/conda_envs/tableverse-gemma` |
| PyTorch | `2.6.0+cu124` |
| Dataset | `MBZUAI/TABVERSE` |
| Dataset config | `qa` |
| Split | `test` |
| Model | `google/gemma-4-12B-it` |
| Prompt format | `tokenizer chat template` |
| Examples | `5` |
| Formats | `html_code, markdown_code, latex_code` |
| Total generations | `15` |
| Decoding | `deterministic, do_sample=false, max_new_tokens=32` |
| Activation policy | `selected layers 0, 6, 13, 20, -1; final-token vector and mean-pooled vector` |

Setup notes:

- Persistent storage uses /hai/scratch/fangruih/tableverse_gemma, not home.
- /scratch/users/$USER was not writable.
- /tmp was rejected because the conda environment needs to persist.
- Initial torch 2.12.1+cu130 failed because the node driver supported CUDA 12.4. Fixed by installing CUDA 12.4 PyTorch wheels.
- Bare prompting produced degenerate Gemma output, so the runner uses tokenizer.apply_chat_template.

Command:

```bash
python experiments/tableverse_gemma/scripts/run_qa.py \
  --config experiments/tableverse_gemma/configs/smoke.yaml
```

Outputs:

```text
experiments/tableverse_gemma/outputs/runs/smoke_text_5.jsonl
experiments/tableverse_gemma/outputs/activations/smoke_text_5/
```

Metrics:

| Metric | Value |
|---|---:|
| Total rows | 15 |
| Runtime errors | 0 |
| HTML accuracy | 1.000 |
| Markdown accuracy | 1.000 |
| LaTeX accuracy | 1.000 |

Result:

Smoke test passed. The run produced 15 prediction rows, 0 runtime errors, and activation files for every example-format pair.

Notes:

- Initial exact scorer reported 0 accuracy because TableVerse labels are list-valued.
- List-aware rescoring gives 15/15.
- For final reporting, use or adapt the official TableVerse evaluator.

## Experiment 001: Full QA Text Benchmark

Status: completed

Date: 2026-06-18

Purpose:

Run Gemma 4 12B on the full TableVerse QA text benchmark across HTML, Markdown, and LaTeX, while saving compact selected-layer hidden-state vectors for cross-format representation analysis.

Setting:

| Field | Value |
|---|---|
| Config | `experiments/tableverse_gemma/configs/qa_full_text.yaml` |
| Run ID | `qa_full_text_700` |
| Dataset | `MBZUAI/TABVERSE` |
| Dataset config | `qa` |
| Split | `test` |
| Examples | `700` |
| Formats | `html_code, markdown_code, latex_code` |
| Total generations | `2100` |
| Model | `google/gemma-4-12B-it` |
| Prompt format | `tokenizer chat template` |
| Decoding | `deterministic, do_sample=false, max_new_tokens=32` |
| Activation policy | `selected layers 0, 6, 13, 20, -1; final-token vector and mean-pooled vector` |

Command:

```bash
python experiments/tableverse_gemma/scripts/run_qa.py \
  --config experiments/tableverse_gemma/configs/qa_full_text.yaml
```

Post-run summary command:

```bash
python experiments/tableverse_gemma/scripts/behavior_summary.py \
  experiments/tableverse_gemma/outputs/runs/qa_full_text_700.jsonl \
  --out experiments/tableverse_gemma/outputs/metrics/qa_full_text_700_behavior.md
```

Outputs:

```text
experiments/tableverse_gemma/outputs/runs/qa_full_text_700.jsonl
experiments/tableverse_gemma/outputs/activations/qa_full_text_700/
experiments/tableverse_gemma/outputs/metrics/qa_full_text_700_behavior.md
```

Metrics:

| Metric | Value |
|---|---:|
| Total rows | 2100 |
| Examples | 700 |
| Runtime | 20:49 |
| Runtime errors | 3 |
| HTML accuracy | 0.431 |
| HTML correct | 302 / 700 |
| Markdown accuracy | 0.430 |
| Markdown correct | 301 / 700 |
| LaTeX accuracy | 0.433 |
| LaTeX correct | 303 / 700 |
| Failure-flip examples | 77 |
| Activation files | 2097 |
| Activation directory size | 563M |
| Avg HTML tokens | 1810.6 |
| Avg Markdown tokens | 933.9 |
| Avg LaTeX tokens | 861.6 |

Result:

Full text QA benchmark completed in 20:49. The run produced 2100 JSONL rows for 700 examples x 3 formats. Accuracy is very similar across HTML, Markdown, and LaTeX, but 77 examples show at least one cross-format correct/incorrect disagreement. Activation files total 2097 because three rows had runtime errors and therefore did not save activations.

Notes:

- PIL emitted a DecompressionBombWarning for a very large TableVerse image field, but this run used text fields only.
- HTML had 1 error, Markdown had 2 errors, and LaTeX had 0 errors.
- The 2097 activation files match 2100 rows minus 3 runtime-error rows.
- Similar aggregate accuracy across formats means the next analysis should focus on the 77 failure-flip examples and layer-wise representation geometry.
- Full token-level activations should be collected only for later diagnostic subsets.

## Experiment 002: Interpretability Analysis For Full QA Text Benchmark

Status: planned

Date: 2026-06-18

Purpose:

Analyze Experiment 001 hidden-state vectors to quantify cross-format representation shifts and test whether failure-flip examples have different layer-wise geometry from non-flip examples.

Setting:

| Field | Value |
|---|---|
| Input run | `experiments/tableverse_gemma/outputs/runs/qa_full_text_700.jsonl` |
| Input activations | `experiments/tableverse_gemma/outputs/activations/qa_full_text_700/` |
| Source experiment | `001` |
| Rows | `2100` |
| Activation files | `2097` |
| Format pairs | `html-markdown, html-latex, markdown-latex` |
| Saved vectors | `selected-layer final-token and mean-pooled vectors` |
| Primary behavioral subset | `77 failure-flip examples` |

Command:

```bash
python experiments/tableverse_gemma/scripts/analyze_geometry.py \
  --run-jsonl experiments/tableverse_gemma/outputs/runs/qa_full_text_700.jsonl \
  --activation-dir experiments/tableverse_gemma/outputs/activations/qa_full_text_700 \
  --out-dir experiments/tableverse_gemma/outputs/metrics/qa_full_text_700_geometry
```

Outputs:

```text
experiments/tableverse_gemma/outputs/metrics/qa_full_text_700_geometry/interpretability_analysis.md
experiments/tableverse_gemma/outputs/metrics/qa_full_text_700_geometry/behavior_rows.csv
experiments/tableverse_gemma/outputs/metrics/qa_full_text_700_geometry/geometry_rows.csv
experiments/tableverse_gemma/outputs/metrics/qa_full_text_700_geometry/geometry_summary_by_layer.csv
experiments/tableverse_gemma/outputs/metrics/qa_full_text_700_geometry/example_distance_summary.csv
experiments/tableverse_gemma/outputs/metrics/qa_full_text_700_geometry/missing_activations.csv
```

Metrics:

| Metric | Value |
|---|---:|
| Mean cosine by layer | pending |
| Failure-flip mean cosine | pending |
| Non-flip mean cosine | pending |
| Flip vs non-flip geometry gap | pending |
| Highest-distance failure-flip examples | pending |

Result:

Pending.

Notes:

- This analysis uses compact saved vectors, not full token-level hidden states.
- If flip and non-flip examples have similar aggregate cosine, inspect top-distance failure-flip cases qualitatively.
- Next deeper analysis can collect full token activations for a small failure-flip subset only.
