<p align="center">
  <img src="pnut-logo.png" alt="PNUT logo" width="180">
</p>

📄 **Rendered HTML report (GitHub Pages):** https://hanjookim-phd.github.io/PNUT/

# PNUT: Personalized Network Utility Toolkit

PNUT is an **R Markdown** toolkit that derives **personalized (individual-level) network features** from questionnaire item responses to support **Network-Augmented Machine Learning Utility (NAMU)**. PNUT converts each participant’s item-response profile into an individualized **symptom co-activation** representation and extracts network-informed predictors (e.g., **node strength**, **pairwise edge weights**, and **graph-level connectivity summaries** such as binary/weighted density). These features can be used alongside conventional predictors in downstream machine learning pipelines.

---

## What PNUT Produces

Given a dataset with an ID column and item-response columns, PNUT appends:

- **Strength features**: `Strength_<item>`
- **Edge-weight features**: `EW_<item1>_<item2>`
- **Graph-level summaries**:
  - `BinaryDensity`
  - `WeightedDensityMean`
  - `WeightedDensitySum`

Output is written as a CSV (path controlled by `params: output_file`).

---

## Quick Start

### Option A — RStudio (recommended)
1. Open `PNUT.Rmd` in RStudio.
2. Edit the YAML header `params:` section (at minimum: `input_file`, `id_col`, and `item_cols` or `item_regex`).
3. Click **Knit** to generate the HTML report.
4. Find the output CSV at `params: output_file` (default: `output/PNUT_outputs.csv`).

### Option B — R console
```r
rmarkdown::render("PNUT.Rmd")
```

Detailed usage examples are provided in the rendered HTML report.

---

## Repository Structure (recommended)

```
PNUT/
  PNUT.Rmd
  README.md
  LICENSE
  data/        # optional: user data or example data
  output/      # created automatically if missing
```

> Tip: If you commit this repo to GitHub, consider adding a `.gitignore` to avoid committing rendered HTML files, `_files/` folders, and `output/`.

---

## Input Data Requirements

PNUT expects a **CSV** containing:

1. **ID column**
   - Set in YAML as `params: id_col`
   - Must uniquely identify participants/rows for merging outputs.

2. **Item columns**
   Choose **one**:
   - `params: item_cols` (recommended): explicit list of item column names
   - `params: item_regex`: regex pattern to automatically select item columns when `item_cols` is empty

### Example (template)
The template uses `DPQ010`–`DPQ080` to illustrate the **PHQ-8** items (PHQ-9 excluding the suicidality item).

---

## How to Configure PNUT (YAML `params:`)

Most users only need to edit the YAML header.

### Minimal required edits
- `input_file`: path to your CSV
- `id_col`: ID column name
- `item_cols` **or** `item_regex`: item columns to use

### Full parameter reference
- `input_file`: CSV path (relative to the `.Rmd` file is recommended)
- `input_file_has_headers`: whether the first row contains column names (`true/false`)
- `id_col`: participant ID column name
- `item_cols`: list of item column names (recommended; takes priority over `item_regex`)
- `item_regex`: regex to select item columns if `item_cols` is empty (e.g., `^DPQ`)
- `na_action`: missing-data handling (`listwise`, `mean_impute`, `median_impute`)
- `scale_items`: whether to z-standardize item scores before feature derivation (`true/false`)
- `binary_edge_rule`: rule for `BinaryDensity` (`A_gt_0` or `x_gt_0`)
- `output_file`: path to save the output CSV
- `show_data_glimpse`: whether to print a preview of cleaned data (`true/false`)
- `histogram_bins`: number of bins for the BinaryDensity histogram

### Notes on a few key options
- **`item_cols` vs `item_regex`**: If both are provided, PNUT uses `item_cols`.
- **`na_action`**:
  - `listwise`: drop rows with any missing items
  - `mean_impute`: impute missing items with item-wise mean
  - `median_impute`: impute missing items with item-wise median
- **`scale_items`**: If `true`, PNUT standardizes item scores before feature derivation.
- **`binary_edge_rule`** (BinaryDensity definition):
  - `A_gt_0`: “active” if co-activation (`x_j * x_k`) is > 0
  - `x_gt_0`: “active” if both items are > 0
  With nonnegative item scales (e.g., 0–3), these are often equivalent; they may differ if item scores can be negative (e.g., after standardization).

---

## Demo Mode (No Input File Found)

If `params: input_file` is not found, PNUT automatically generates a small **synthetic demo dataset** so the report can knit end-to-end.

- This synthetic dataset is provided for **demo/testing only**.
- It is **not** intended for substantive analysis or interpretation.

---

## Output Details

The output CSV contains:
- All original columns from the input dataset
- Appended network features:
  - `Strength_<item>`
  - `EW_<item1>_<item2>`
  - `BinaryDensity`, `WeightedDensityMean`, `WeightedDensitySum`

Default output path:
- `output/PNUT_outputs.csv`

---

## Dependencies

PNUT currently uses:
- `rmarkdown`, `knitr`
- `dplyr`, `tidyr`, `ggplot2`

If you can knit the Rmd successfully in RStudio, dependencies are already satisfied.

---

## Troubleshooting

### 1) “File not found” for `input_file`
- Confirm the file path in YAML (`params: input_file`).
- Use relative paths like `data/mydata.csv` (recommended).
- On Windows, prefer forward slashes `/` in paths.

### 2) “Provide item_cols or item_regex.”
- Provide `params: item_cols` OR set `params: item_regex`.
- If you want automatic selection, set `item_cols` to empty and use `item_regex`.

### 3) ID or item column not found
- Ensure `params: id_col` exactly matches the column name in your CSV.
- Ensure all columns listed in `params: item_cols` exist in the CSV.

### 4) TOC/HTML widgets look broken
- Try setting `self_contained: false` in the YAML output options (this separates JS/CSS into an `_files/` folder).

---


## Citation

If you use PNUT in academic work, please cite this repository and the NAMU reference below.

Kim, H. (2026). *PNUT: Personalized Network Utility Toolkit*. GitHub repository. [https://github.com/prechter-data-team/PNUT](https://github.com/prechter-data-team/PNUT)

Kim, H., Yocum, A., McInnis, M., & Sperry, S. H. (2025). Predicting suicidal ideation from depression screening data: A network-augmented machine learning approach.*Journal of Affective Disorders*. [https://doi.org/10.31234/osf.io/rqyvx_v2](https://doi.org/10.1016/j.jad.2026.121671)

---

## License

Licensed for use, modification, and redistribution under the terms of the LICENSE. See `LICENSE` for details.

---

## Contributing

Suggestions and improvements are welcome. Please open an issue or submit a pull request.
By contributing, you agree that your contributions will be licensed under the same terms as this repository’s LICENSE.

---

## Contact

For questions or bug reports, please use the GitHub Issues page for this repository.
