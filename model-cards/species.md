# Model card: species (Palmer Penguins)

Predicts `species` from one record of a table, with a probability for each class.

## Model

- Logistic regression (C = 10, chosen on the validation set from 0.1, 1, 10), after filling missing values, scaling numbers and one-hot encoding categories.
- Registered as `penguins-species`, version 1.
- Classes: Adelie, Chinstrap, Gentoo.

## Data

- `dataset/penguins.csv` (release palmerpenguins-0.1.1, SHA-256 `f204db2c753b…`).
- Inputs: `bill_length_mm`, `bill_depth_mm`, `flipper_length_mm`, `body_mass_g`, `island`, `sex`.
- Every record has a target; they were split by class (seed 42): train 206, validation 69, test 69.

## Results on the test set

- 69 records: accuracy 0.986, macro-F1 0.983, expected calibration error 0.013.

| True class | Adelie | Chinstrap | Gentoo |
|---|---|---|---|
| Adelie | 29 | 1 | 0 |
| Chinstrap | 0 | 14 | 0 |
| Gentoo | 0 | 0 | 25 |

## Limits

- Answers are only as good as the records the model learnt from: records unlike them (other places, years or kinds) may be wrong with confidence.
