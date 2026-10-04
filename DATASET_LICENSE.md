# Airfoil Self-Noise: attribution and licence

**Citation:** Brooks, T., Pope, D., & Marcolini, M. (1989). *Airfoil Self-Noise* [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5VW2C

**Creators named by UCI:** Thomas Brooks; D. Pope; Michael Marcolini.

**Dataset page:** https://archive.ics.uci.edu/dataset/291/airfoil%2Bself%2Bnoise

**Original numeric file:** https://archive.ics.uci.edu/ml/machine-learning-databases/00291/airfoil_self_noise.dat

**Licence identified by UCI:** Creative Commons Attribution 4.0 International (CC BY 4.0).
- Human-readable deed: https://creativecommons.org/licenses/by/4.0/
- Legal code: https://creativecommons.org/licenses/by/4.0/legalcode

The data are included at `data/airfoil_self_noise.dat` and embedded numerically in `index.html` so that calculations do not depend on an online download. The values are unchanged. The app adds one-based row IDs, constructed condition-group IDs, training/test assignments, and model outputs separately. Line endings in the embedded HTML may be normalized by browsers; the bundled .dat file retains the verified bytes.

The original creators and UCI are credited in the app and its exported experiment report. Carry this attribution and licence information forward when sharing or modifying the app or data. No endorsement by NASA, UCI, the dataset creators, or the institution named for the event is implied.

## Verified original file

- Rows: 1,503; columns: six (five inputs plus one target).
- Size: 59,984 bytes.
- SHA-1: `76e5be1548fd8222e5074cf0faae75edff8cf93f`
- SHA-256: `74c75fd71783f1e6b71f8a622b993dc592897a97cd689c5090a07147a1b097b3`

The SHA-1 matches the data hash published in the official *Dive into Deep Learning* chapter: https://d2l.ai/chapter_optimization/minibatch-sgd.html
This reference is used only for file-integrity corroboration, not for the app's preprocessing or regression method.

## Field order

Frequency (Hz); angle of attack (degrees); chord length (m); free-stream velocity (m/s); suction-side displacement thickness (m); scaled sound pressure level (dB).

The output is not total aircraft noise. Displacement thickness is not physical wing thickness. UCI's metadata table labels angle as binary, but the supplied numeric data have multiple angle values; this app correctly treats it as a numerical input. The five input names and units follow UCI's explicit variable descriptions.
