# Invasion Breast Cancer LVI Prediction

**Research web calculator and reproducibility repository** for predicting class 1
(presumed breast cancer lymphovascular invasion, LVI) with a fitted six-input
L1-regularized logistic regression model.

**Online calculator (when GitHub Pages is deployed):**
https://weiweiusd.github.io/Invasion-Breast-Cancer-LVI-Prediction/

**Repository:**
https://github.com/weiweiusd/Invasion-Breast-Cancer-LVI-Prediction

> **Research software, not a clinically validated medical device.**
> Class-1 outcome coding, upstream model development, data independence,
> external validation and calibration require verification by the study team.
> This repository is not intended to guide patient diagnosis or treatment.

## What the calculator includes

- Six inputs: **US-ALNs**, **Size**, **Age**, **Peri3mm Model**, **HabitatAll Model**, **DL Model**.
- US-ALNs is 0/1; Size and Age initially display 0.
- The final three inputs are probabilities between **0.000 and 1.000**, entered
  to at most three decimal places and initially displayed as **0.000**.
- **Calculate** shows a predicted class-1 probability, classification,
  and an individual red/blue feature-contribution force plot.
- **Reset** clears the result and restores the default inputs.
- A **training-set maximum-Youden cutoff** of `0.3210826514929313` is used
  for classification (not 0.50).
- Browser-only calculations: no prediction backend, patient upload, or external
  JavaScript/CDN dependencies.

The force plot is a **SHAP-style visualization of exact mean-centered linear
logit contributions**, not a probability-scale SHAP calculation.

## Research summary

| Property | Value |
|---|---|
| Model | scikit-learn `LogisticRegression`, L1, liblinear |
| Predictors | 6 |
| Training data | 903 cases |
| Supplied test file | 151 cases (independence not independently established) |
| Decision threshold | 0.3210826514929313 (training ROC Youden index) |
| Training ROC AUC | 0.860660 |
| Supplied test ROC AUC | 0.819780 |
| Training sensitivity / specificity at threshold | 0.820339 / 0.754934 |
| Supplied test sensitivity / specificity | 0.700000 / 0.780220 |
| Original fitted model artifact | `logistic_final.joblib` (not distributed publicly) |
| Attribution background | Training-set predictor means (n=903) |

These are **computed descriptive metrics**, not proof of external clinical
validation. See [VALIDATION.md](docs/VALIDATION.md) and
[MODEL_CARD.md](docs/MODEL_CARD.md).

## Repository layout

```text
index.html                         Standalone, self-contained website (GitHub Pages entry)
website-source.html                Editable website markup before bundling
assets/
  app.js                           Input validation, prediction presentation, force plot
  model-core.js                    Logistic probability and additive contributions
  model-data.js                    Exported coefficients, cutoff, training means
  styles.css                       Site styling
model/
  model-parameters.json            Machine-readable audit export
scripts/
  build_website.py                 Deterministically regenerate index.html
  check_release.py                 Compare deployed HTML and original source/parameters
  derive_training_cutoff.py        Recompute threshold using a privately held train.csv
  reproduce_results.py            Reproduce aggregate train/test metrics
  compare_original_joblib.py      Optional comparison with a TRUSTED serialized model
tests/
  verify.js                        Reference vectors, additivity, cutoff tests
docs/
  MODEL_CARD.md                    Model description and limitations
  METHODS.md                       Prediction, Youden cutoff, attribution methods
  DATA_DICTIONARY.md              Exact feature and column mapping
  VALIDATION.md                    Reproduced performance results and caveats
  DATA_AVAILABILITY.md            Public/private data handling
  PROVENANCE.md                   Original file SHA-256 checksums
  PUBLICATION_CHECKLIST.md        Checks before academic citation/submission
  DEPLOYMENT.md                   GitHub Pages instructions
.github/workflows/verify.yml      Automated verification on push / pull request
CHANGELOG.md                       Version notes
CITATION_TEMPLATE.md              Citation template (authors/DOI must be completed)
.nojekyll                           Static Pages configuration
.gitignore                          Local confidential-data exclusions
```

## Deploy to GitHub Pages

**For the existing repository**, upload the *contents* of the extracted
release ZIP to the **repository root**. Do not upload the ZIP itself and do
not create an extra top-level `Invasion_Breast_Cancer_...` folder in GitHub.

In GitHub, use **Add file → Upload files**; drag in all extracted files and
folders, then commit to `main`. Under **Settings → Pages**, choose
**Deploy from a branch**, `main`, and `/(root)`.

**The public `index.html` is completely standalone**: it contains the web
styles, original coefficients and calculator JavaScript in one file. Even if
GitHub's uploader overlooks the `assets/` folder, the deployed calculator can
still work. The rest of the repository is required to preserve auditability,
source code and repeatable builds.

See [DEPLOYMENT.md](docs/DEPLOYMENT.md) for full instructions and troubleshooting.

## Build and verify

The deployed site does not require Python or Node.js. For release
maintenance, use Python 3.10+ and optionally Node.js 20+:

```bash
python scripts/build_website.py
python scripts/check_release.py
node tests/verify.js
```

The site is rebuilt from `website-source.html` and `assets/`.
**Edit those source files, not only `index.html`.** Keep the generated
`index.html` in sync by rerunning the build before committing.

With secure access to the original (unpublished) datasets:

```bash
python scripts/derive_training_cutoff.py /secure/path/train.csv
python scripts/reproduce_results.py /secure/path/train.csv /secure/path/test2.csv
```

For trusted originals only, use `scripts/compare_original_joblib.py`.
Be aware that Python joblib files can execute arbitrary code when loaded;
see the script's `--trusted-file` requirement.

## Reproducibility and academic reporting

The original source data and serialized fitted model were supplied for the
website implementation, but are **not committed in this public repository**.
`train.csv` and `test2.csv` both contain an `ID` column. `PROVENANCE.md`
documents their digests without distributing patient rows.

Important remaining matters for publication include verifying the definition of
LVI positivity, Size units, US-ALNs coding, whether upstream predictions were
out-of-fold when building the final model, data separation, ethics and data
permissions, independent validation, and calibration. The three-decimal UI
also introduces slight input rounding; see `METHODS.md`.

A public repository improves transparency, but **does not remove the need for
scientific or ethical review**. See
[PUBLICATION_CHECKLIST.md](docs/PUBLICATION_CHECKLIST.md).

## Rights and citation

No copyright license is granted by the presence of the public repository;
the owner should choose and add an appropriate license after confirming they
have the rights to publish the code and model parameters. A scholarly citation
must use verified study authors and an actual repository release/DOI.
See [CITATION_TEMPLATE.md](CITATION_TEMPLATE.md).

**Release:** v1.0.0 — research implementation. No claim of external validation.
