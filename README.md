
Code and results for a study comparing four strategies for integrating social
determinants of health (SDOH) data into diabetes risk prediction models, with
formal missing-data sensitivity analysis (complete-case vs. multiple imputation)
and uncertainty quantification (DeLong tests, Rubin's-rules pooled confidence
intervals, calibration diagnostics).

**Manuscript:** [add citation/DOI once published]
**Manuscript status:** in preparation for submission to *JAMIA*

## Key finding

No SDOH integration strategy (raw variables, HP2030 domain groupings, SHAP-based
data-driven selection, or a composite deprivation index) significantly
outperformed another on discrimination, calibration, or reclassification. The
strategy with the best point-estimate AUC reversed depending on whether missing
income data was handled by complete-case deletion or multiple imputation —
demonstrating that single-approach comparisons of SDOH integration strategies
risk reporting sampling noise as a substantive finding.

## Repository structure

```
├── src/
│   ├── run_pipeline.py       # End-to-end pipeline (merge → outcome → strategies → evaluation)
│   └── stats_helpers.py      # Hand-implemented survey-weighted GLM, DeLong, Hosmer-Lemeshow, NRI
├── notebook/
│   └── code.ipynb            # Full annotated analysis, including ⚠️ cells for optional
│                              # cross-validation against statsmodels/samplics/shap
├── results/                  # Numeric outputs (JSON/CSV) from the published analysis
├── requirements.txt
└── LICENSE
```

## Data

This project uses **public-use NHANES data**, not redistributed here. Download
the following files from the CDC/NCHS pre-pandemic 2017–March 2020 combined
release and place them in a local `data/` folder (create it; not tracked in
this repo):

| File | NHANES component | URL |
|---|---|---|
| `P_DEMO.xpt` | Demographics | `https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2017/DataFiles/P_DEMO.xpt` |
| `P_DIQ.xpt` | Diabetes questionnaire | `https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2017/DataFiles/P_DIQ.xpt` |
| `P_GLU.xpt` | Fasting plasma glucose | `https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2017/DataFiles/P_GLU.xpt` |
| `P_GHB.xpt` | Glycohemoglobin | `https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2017/DataFiles/P_GHB.xpt` |
| `P_FSQ.xpt` | Food security | `https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2017/DataFiles/P_FSQ.xpt` |
| `P_HIQ.xpt` | Health insurance | `https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2017/DataFiles/P_HIQ.xpt` |
| `P_INQ.xpt` | Income (supplementary) | `https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2017/DataFiles/P_INQ.xpt` |
| `P_OCQ.xpt` | Occupation | `https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2017/DataFiles/P_OCQ.xpt` |

**Note:** the NHANES Housing Characteristics module (HOQ) was not released as
part of the 2017–March 2020 pre-pandemic combined file (confirmed via the
NHANES variable search tool — HOQ jumps from the 2017–2018-only `HOQ_J` file
to the 2021–2023 `HOQ_L` file, with no combined-cycle version in between).
Housing is therefore excluded from the SDOH variable set in this analysis —
see manuscript Limitations.

## Setup

```bash
git clone https://github.com/singhlamohit87/sdoh-integration-diabetes-nhanes.git
cd sdoh-integration-diabetes-nhanes
pip install -r requirements.txt
```

For the notebook's optional cross-validation cells (marked ⚠️), you will
additionally need:
```bash
pip install statsmodels samplics shap xgboost
```
These were not available in the environment where this pipeline was originally
built and validated; the published results were subsequently reproduced with
these packages and matched the original approximations closely (see manuscript
Methods for details — e.g., `IterativeImputer`-based multiple imputation
matched proper `statsmodels` MICE within 0.0002 AUC across all four strategies).

## Reproducing the analysis

```bash
cd src
python run_pipeline.py
```

Or step through `notebook/code.ipynb` for the fully annotated
version, including the missing-data sensitivity analysis (Section 8) and
survey-variance cross-validation (Section 6a).

## Statistical methods implemented from scratch

`statsmodels`, `samplics`/`survey`, and `shap` were not installable in the
original development environment (sandboxed, no network access for `pip
install`). `src/stats_helpers.py` therefore includes hand-built implementations
of:
- Survey-weighted logistic regression with Taylor-linearized (sandwich)
  standard errors, clustering on PSU nested within strata
- DeLong's test for correlated ROC curves
- Hosmer-Lemeshow goodness-of-fit test (weight-normalized)
- Net reclassification improvement (NRI)
- Stratified PSU-cluster bootstrap

These were subsequently cross-validated against `statsmodels`' cluster-robust
GLM (17 of 18 Strategy 1 coefficient SEs agreed within ~15%) and against
`statsmodels`' MICE implementation for multiple imputation (matched within
0.0002 AUC). See manuscript Methods for full validation details.

## Citation

If you use this code, please cite the manuscript [citation to be added upon
publication] and this repository [Zenodo DOI to be added].

## License

MIT License — see `LICENSE`.



