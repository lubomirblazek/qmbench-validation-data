# QM Bench validation datasets

These files let anyone check the numbers behind https://qmbench.com/validation/ .
Each `<case>.csv` is an input dataset exactly as used by the QM Bench validation harness.
`expected-results.csv` lists the reference value for each statistic.

Reference values were computed by the harness (validation/validate.py) on 2026-10-24 with Python 3.12.0,
NumPy 2.5.4 and SciPy 1.18.1, or taken from published tables (AIAG SPC and MSA manuals, ISO 2859-1).
The `source` column says which one applies to each row.

## expected-results.csv

| column | meaning |
|---|---|
| case | name of the input file without .csv |
| statistic | what is calculated |
| settings | specification limits and options used |
| expected | reference value |
| tolerance | a result passes when abs(result - expected) <= tolerance * max(1, abs(expected)); 0 means exact match |
| source | where the reference value comes from |

## Cases

| file | method | layout |
|---|---|---|
| capability_xbar_r.csv | Process capability (Cp, Cpk, Pp, Ppk, Cpm) and X-bar R limits, subgroups of 5, sigma within = R-bar / d2 | one row per subgroup |
| capability_imr.csv | Capability with I-MR, sigma within = MR-bar / 1.128 | one value per row |
| gage_rr_aiag.csv | Gage R&R, crossed ANOVA (interaction pooled into repeatability) and average and range method; AIAG MSA 4th ed. example data, 10 parts, 3 operators, 3 trials | one row per part and operator |
| type1_gauge.csv | Type 1 gauge study Cg = 0.2 T / 6 s, Cgk = (0.1 T - abs(bias)) / 3 s | one value per row |
| linearity_bias.csv | Gauge linearity and bias (regression of bias on reference) | one row per reference part, 12 repeats |
| p_chart.csv | p chart, centre line and 3 sigma UCL for sample 1 | defectives and sample size |
| u_chart.csv | u chart, centre line and 3 sigma UCL for sample 1 | defects and units inspected |
| aql_plans.csv | ISO 2859-1 single sampling plan, normal inspection: code letter, n, Ac, Re | one row per lot |
| aql_oc.csv | OC curve point: probability of acceptance = binomial CDF(Ac; n, p) | one row per point |
| regression.csv | Simple linear regression and correlation | x, y |

## Reproduce it yourself

1. Download the zip and open any CSV in Excel, Python, R or Minitab.
2. Compute the statistic with your own tool using the settings column.
3. Compare with the expected column using the tolerance rule above.

Python example for the I-MR capability case:

```python
import numpy as np
x = np.loadtxt('capability_imr.csv', delimiter=',', skiprows=1, usecols=1)
sw = np.abs(np.diff(x)).mean() / 1.128
print('Cpk', min(10.25 - x.mean(), x.mean() - 9.8) / (3 * sw))
```

Within the QM Bench project, `npm test` runs src/lib/engine/validationData.test.ts, which loads every file here,
runs the QM Bench engine and asserts each row of expected-results.csv.

## Licence and citation

Datasets are free to reuse with attribution.

Cite as: QM Bench validation datasets (2026-10-24), https://qmbench.com/validation/

## License and citation

CC BY 4.0, see LICENSE.md. How to cite: see CITATION.cff, or use the DOI shown on Zenodo.
Web version and live tools: https://qmbench.com/validation/
