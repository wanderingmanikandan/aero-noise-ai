# AeroNoise AI
## A guided airfoil-noise research demonstrator for beginner AI & Data Science students

Prepared for the Aerospace Engineering Research Pitch Day at **Noorul Islam Centre for Higher Education, Kanyakumari**. Educational prototype; no institutional endorsement is implied.

**Research question:** Does five-neighbour regression predict the dataset's scaled sound pressure level with lower mean absolute error than a training-mean baseline on a specified held-out test set?

## Start without installation

Extract the ZIP and open **`index.html`** in a modern desktop browser. The complete data, styles, charts, model, and interface are embedded in that one file. No Python, server, external script, account, API key, or paid AI service is needed for the app's calculations. JavaScript must be enabled. Internet is needed only to initially load a hosted copy or follow external source links.

The five screens are **Understand → Explore data → Train & test → Try a case → Pitch & export**. Start with Experiment A. Read **`AeroNoise_AI_Student_Guide.pdf`** for the complete student procedure, rehearsal, faculty questions, and deployment instructions.

## Publish on GitHub Pages

1. Create a public repository, for example `aero-noise-ai`.
2. Upload the **extracted package contents**, not the ZIP itself. `index.html` must be at the repository top level, not one directory deeper.
3. Commit the uploaded files to `main`.
4. Open **Settings → Pages**. Under Source, select **Deploy from a branch**.
5. Choose **main** and **/(root)**, then **Save**.
6. After GitHub reports a successful deployment, open the site address displayed there. Share that address, not the GitHub source-code page.

This package has no hard-coded username, repository path, or external asset paths. Any repository name can be used. GitHub Pages supports public repositories on GitHub Free; repository permissions and organizational restrictions may apply. Do not include private documents or students' personal information in a public repository. Deployment is a separate action: delivery of these files does not create a live website.

Official publishing instructions: https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site

## What the app does

- Explains the aerospace problem, inputs, target, baseline, training/test split, and error metrics in plain English.
- Includes all 1,503 NASA/UCI observations, without a runtime data download.
- Runs deterministic five-neighbour regression against a training-target-mean baseline.
- Keeps constructed condition groups entirely in either training or testing.
- Separates making a held-out prediction from revealing its measured value.
- Shows the actual five training neighbours used for a prediction.
- Produces predicted-versus-measured plots, MAE bars, RMSE, R², and a group-balanced MAE.
- Provides a tailored speaking script, four-person team roles, FAQs, and a short self-check.
- Exports full test predictions (CSV), settings and exact row IDs (JSON), a printable HTML report, and pitch text.

To create a PDF experiment report, download the printable report, open it in a browser, and use **Print → Save as PDF**. Its appendix includes all held-out predictions. Predictions in the printed table are rounded; the JSON and CSV retain numerical precision.

## Data and target

Source: Brooks, T., Pope, D., & Marcolini, M. (1989). *Airfoil Self-Noise*, UCI Machine Learning Repository. DOI: https://doi.org/10.24432/C5VW2C

UCI describes NASA wind-tunnel observations of different-sized NACA 0012 airfoils. There are 1,503 observations, five numerical inputs, and no missing values. Inputs are frequency (Hz), angle of attack (degrees), chord length (m), free-stream velocity (m/s), and suction-side displacement thickness (m). The target is **scaled sound pressure level (dB)**. [Source](https://archive.ics.uci.edu/dataset/291/airfoil%2Bself%2Bnoise)

The supplied raw .dat file matches the published SHA-1: `76e5be1548fd8222e5074cf0faae75edff8cf93f`. See `DATASET_LICENSE.md` for full attribution and integrity information. Numeric source values are unchanged.

## Exact method

The engine is in the `aeronoise-core` script inside `index.html`; the interface is in `aeronoise-ui`. The core is dependency-free JavaScript, not a remote AI model.

**Constructed group:** identical angle, chord, speed, and displacement thickness; frequency excluded. There are 106 groups, numbered in order of first appearance. These are not original experimental run IDs. Grouping reduces one obvious source of similar-row leakage but is not a complete physical independence guarantee.

**A — New conditions:** shuffle all 106 groups with seed 2026 using a specified Fisher–Yates algorithm. Hold out the first `ceil(106 × 0.20) = 22` groups; the other 84 groups train the model. This is a rounded fraction of groups, not exactly 20% of observations.

Shuffle generator: `state = (1664525 * state + 1013904223) mod 2^32`; for each descending Fisher–Yates index `i`, use `floor((state / 2^32) * (i + 1))`. JavaScript uses `Math.imul` to implement the 32-bit multiplication exactly.

**B — Unseen high speed:** hold out all observations at 71.3 m/s. Train at 31.7, 39.6, and 55.5 m/s. This is a fixed high-speed extrapolation challenge. Its test population differs from A, so the two experiments are not a controlled comparison of interpolation versus extrapolation difficulty.

**Scaling:** z-score the raw five inputs using training means and population standard deviations (`ddof=0`). No log transform. A zero standard deviation is replaced with 1. No scaling statistics are fitted on test rows. Standardised Euclidean distance is used to select exactly five nearest training rows; distance ties are resolved by original row ID. Average the five target values uniformly. This numerical regression average is not an acoustic-energy sum of sound sources.

**Baseline:** the target mean of the training set, reused for every test row.

**Metrics:** MAE, RMSE, and R² calculated over all test observations. Group-balanced MAE averages each condition's MAE equally. R² is not a percentage accuracy and may be negative. Relative MAE reduction is `100 × (baseline MAE − model MAE) / baseline MAE`; it is not a reduction in actual aircraft noise.

Settings are fixed at k=5, seed=2026 for A, and held-out speed=71.3 m/s for B. No hyperparameter tuning, repeated split selection, or test-label model selection is performed by the app. Changing settings after studying test results would require a new evaluation design before presenting a final untouched test claim.

## Verified results for version 1.0.0

These are calculated results, not sample scores.

| Quantity | Experiment A | Experiment B |
|---|---:|---:|
| Training rows | 1,188 | 1,038 |
| Test rows | 315 | 465 |
| Training groups | 84 | 73 |
| Test groups | 22 | 33 |
| Group overlap | 0 | 0 |
| kNN MAE (dB) | 3.221811 | 2.383905 |
| Baseline MAE (dB) | 5.201178 | 5.950653 |
| kNN RMSE (dB) | 4.450259 | 3.290633 |
| Baseline RMSE (dB) | 6.334686 | 7.159149 |
| kNN R² | 0.505710 | 0.779176 |
| Baseline R² | −0.001524 | −0.045227 |
| Group-balanced kNN MAE (dB) | 3.148982 | 2.391340 |
| Relative MAE reduction (%) | 38.056132 | 59.938774 |

**Interpretation:** the model beats its own baseline in each fixed experiment. B's lower MAE does not prove that prediction outside the training range is generally easier or reliable; the test populations differ. The app flags out-of-range input coordinates, but does not estimate confidence intervals or certify predictions inside the ranges.

## Verification and limits of testing

`verification/verify.py` independently recalculates the JavaScript results using NumPy. It checks exact dataset integrity, embedded-data equivalence, every prediction, both models' metrics, group-balanced MAE, all split IDs, and training-only scaling. `run_core.cjs` also tests reproducibility and verifies that changing every test target does not change predictions or fitted input scaling. All **780 predictions across the two experiments** matched the independent NumPy predictions exactly in this build.

`verification/browser_test.py` exercised all five screens in **Chromium 144.0.7559.96**, with networking disabled. It checked prediction/reveal sequencing, downloads and their contents, proper A/B separation, HTML escaping, quiz/reset behavior, and mobile widths of 360 and 390 px. No JavaScript errors or network requests occurred. The exported report rendered to PDF.

**Testing limitation:** this environment blocks `file://` and localhost navigation by administrator policy. The actual standalone HTML was rendered directly in an offline browser context using Playwright `set_content`. Double-click file navigation and a live GitHub Pages deployment were not tested here. The app uses no fetch, imports, relative resources, or other server-dependent functionality. Check the saved file and published link on the students' intended device before the event. Other browser engines were not separately certified.

Developer-only reproducibility commands (not needed to operate the app):

```bash
python verification/verify.py        # needs NumPy and Node.js
python verification/browser_test.py  # needs Playwright and Chromium
```

Machine-readable check outputs and full expected experiment JSONs are included in `verification/`.

## Responsible presentation

This is an educational, reproducible benchmark and student research pitch, not a claim of a new AI algorithm, certified noise prediction, a noise-reducing aircraft design, or a safety product. The students did not collect the NASA data. The app and documentation were prepared with AI assistance; students should acknowledge that assistance and explain the reasoning they can reproduce.

One fixed holdout is not repeated cross-validation. No causal claim, real-world error guarantee, independent flight validation, or generalisation to another airfoil is established. The target is not total aircraft/drone/airport noise. Prepared cases are genuine input combinations; the app does not propose physical designs from arbitrary sliders.

## Privacy and licences

All calculations run in the browser. The app has no analytics or telemetry. The optional team name stays in memory and is written only into downloaded reports. Refreshing or closing the page clears session results. Hosting providers can process ordinary website traffic; clicking an external reference opens that site.

Code and original documentation: MIT (`LICENSE`). Dataset, including the embedded copy: **CC BY 4.0**, separately (`DATASET_LICENSE.md`). Keep dataset attribution when sharing or adapting. No endorsement is implied.

## Primary method references

- Nearest-neighbour regression: https://scikit-learn.org/stable/modules/neighbors.html
- Avoiding preprocessing leakage: https://scikit-learn.org/stable/common_pitfalls.html
- Held-out and grouped evaluation: https://scikit-learn.org/stable/modules/cross_validation.html
- Dataset and source provenance: https://archive.ics.uci.edu/dataset/291/airfoil%2Bself%2Bnoise
