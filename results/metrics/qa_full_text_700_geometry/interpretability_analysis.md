# Interpretability Analysis: qa_full_text_700

## Behavioral Summary

- rows: 2100
- examples: 700
- failure-flip examples: 77
- missing activation rows: 3

| format | n | correct | accuracy | errors |
|---|---:|---:|---:|---:|
| html | 700 | 302 | 0.431 | 1 |
| latex | 700 | 303 | 0.433 | 0 |
| markdown | 700 | 301 | 0.430 | 2 |

## Layer-Wise Geometry

Values below are cosine similarities between matched examples across table formats. Lower values indicate larger representation shifts.

| pair | layer | representation | n | mean cosine | flip mean | non-flip mean | flip - nonflip |
|---|---:|---|---:|---:|---:|---:|---:|
| html-latex | -1 | final_token | 699 | 0.9269 | 0.8869 | 0.9317 | -0.0449 |
| html-latex | -1 | mean_pool | 699 | 0.9153 | 0.9131 | 0.9156 | -0.0025 |
| html-latex | 0 | final_token | 699 | 1.0000 | 1.0000 | 1.0000 | 0.0000 |
| html-latex | 0 | mean_pool | 699 | 0.6531 | 0.6449 | 0.6541 | -0.0092 |
| html-latex | 13 | final_token | 699 | 0.9898 | 0.9881 | 0.9900 | -0.0018 |
| html-latex | 13 | mean_pool | 699 | 0.9844 | 0.9840 | 0.9844 | -0.0004 |
| html-latex | 20 | final_token | 699 | 0.9972 | 0.9968 | 0.9972 | -0.0004 |
| html-latex | 20 | mean_pool | 699 | 0.9993 | 0.9993 | 0.9993 | -0.0000 |
| html-latex | 6 | final_token | 699 | 0.9276 | 0.9179 | 0.9288 | -0.0109 |
| html-latex | 6 | mean_pool | 699 | 0.8964 | 0.8960 | 0.8965 | -0.0004 |
| html-markdown | -1 | final_token | 697 | 0.9204 | 0.8764 | 0.9257 | -0.0493 |
| html-markdown | -1 | mean_pool | 697 | 0.9292 | 0.9287 | 0.9293 | -0.0006 |
| html-markdown | 0 | final_token | 697 | 1.0000 | 1.0000 | 1.0000 | 0.0000 |
| html-markdown | 0 | mean_pool | 697 | 0.6756 | 0.6711 | 0.6762 | -0.0051 |
| html-markdown | 13 | final_token | 697 | 0.9879 | 0.9861 | 0.9881 | -0.0020 |
| html-markdown | 13 | mean_pool | 697 | 0.9826 | 0.9822 | 0.9826 | -0.0004 |
| html-markdown | 20 | final_token | 697 | 0.9967 | 0.9963 | 0.9967 | -0.0005 |
| html-markdown | 20 | mean_pool | 697 | 0.9994 | 0.9994 | 0.9994 | -0.0000 |
| html-markdown | 6 | final_token | 697 | 0.9194 | 0.9093 | 0.9206 | -0.0114 |
| html-markdown | 6 | mean_pool | 697 | 0.8463 | 0.8434 | 0.8466 | -0.0033 |
| markdown-latex | -1 | final_token | 698 | 0.9402 | 0.9334 | 0.9411 | -0.0077 |
| markdown-latex | -1 | mean_pool | 698 | 0.9796 | 0.9794 | 0.9796 | -0.0002 |
| markdown-latex | 0 | final_token | 698 | 1.0000 | 1.0000 | 1.0000 | 0.0000 |
| markdown-latex | 0 | mean_pool | 698 | 0.8090 | 0.7987 | 0.8103 | -0.0116 |
| markdown-latex | 13 | final_token | 698 | 0.9923 | 0.9948 | 0.9920 | 0.0028 |
| markdown-latex | 13 | mean_pool | 698 | 0.9955 | 0.9952 | 0.9956 | -0.0003 |
| markdown-latex | 20 | final_token | 698 | 0.9977 | 0.9983 | 0.9977 | 0.0006 |
| markdown-latex | 20 | mean_pool | 698 | 0.9997 | 0.9997 | 0.9997 | 0.0000 |
| markdown-latex | 6 | final_token | 698 | 0.9325 | 0.9470 | 0.9307 | 0.0163 |
| markdown-latex | 6 | mean_pool | 698 | 0.9256 | 0.9198 | 0.9263 | -0.0064 |

## Highest Late-Layer Distances Among Failure Flips

| example | pair | late mean distance | category | difficulty |
|---:|---|---:|---|---|
| 151 | html-latex | 0.2267 | Aggregation / Counting / Arithmetic | Easy |
| 321 | html-latex | 0.2250 | Multi-hop Binary Verification | Easy |
| 52 | html-latex | 0.2243 | Conditional Lookup | Easy |
| 693 | html-latex | 0.2230 | Multi-hop Binary Verification | Hard |
| 483 | html-markdown | 0.2134 | Multi-Item Lookup | Hard |
| 151 | html-markdown | 0.2129 | Aggregation / Counting / Arithmetic | Easy |
| 321 | html-markdown | 0.2127 | Multi-hop Binary Verification | Easy |
| 495 | markdown-latex | 0.2098 | Multi-Item Lookup | Hard |
| 52 | html-markdown | 0.2076 | Conditional Lookup | Easy |
| 693 | markdown-latex | 0.2075 | Multi-hop Binary Verification | Hard |
| 483 | markdown-latex | 0.1997 | Multi-Item Lookup | Hard |
| 495 | html-markdown | 0.1924 | Multi-Item Lookup | Hard |
| 359 | html-latex | 0.1776 | Simple Lookup | Hard |
| 359 | html-markdown | 0.1696 | Simple Lookup | Hard |
| 222 | html-latex | 0.1529 | Comparison & Extremum | Easy |
| 222 | html-markdown | 0.1441 | Comparison & Extremum | Easy |
| 8 | html-markdown | 0.0972 | Simple Lookup | Easy |
| 8 | html-latex | 0.0932 | Simple Lookup | Easy |
| 157 | html-latex | 0.0830 | Aggregation / Counting / Arithmetic | Easy |
| 157 | html-markdown | 0.0805 | Aggregation / Counting / Arithmetic | Easy |

## Outputs

- `behavior_rows.csv`
- `geometry_rows.csv`
- `geometry_summary_by_layer.csv`
- `example_distance_summary.csv`
- `missing_activations.csv`
