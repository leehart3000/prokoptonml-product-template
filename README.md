# ProkoptonML product template

A starting point for a product built on [ProkoptonML](https://github.com/leehart3000/prokoptonml):
an app's config, data, checks, CI, deploy settings and release, with the
platform pinned exactly from PyPI (`prokopton-ml`). As it comes, it is a
working product: the Palmer Penguins app, which predicts a penguin's species
from its measurements, island and sex, and serves the answer at
`POST /classify`.

To start a product, use this template ("Use this template" on GitHub), then
follow [docs/new-product.md](docs/new-product.md).

| Path | What it is |
|---|---|
| `prokopton.toml` | The app: its data, models, pipeline and serving steps (config schema 4) |
| `dataset/` | The Palmer Penguins data (CC0), with its source, checksums and citations |
| `tests/known-answers/` | Requests whose answers must not change (`prokopton-ml check`) |
| `model-cards/` | Model cards, written by the pipeline's card stage |
| `pixi.toml` | The environment, the platform's exact version, and the tasks |
| `Dockerfile` | The API image: the platform from PyPI, serving the bundle |
| `infra/deploy/app.env` | Deploy settings for Cloud Run (nothing secret) |
| `.github/workflows/` | CI, the pipeline in CI, secret scanning, deploy, release, lockfile updates |
| `scripts/check_config.py` | Checks the config, and that the three platform pins agree |
| `ruff.toml` | Formatting and lint settings for the product's own Python |

## Run it

With [Pixi](https://pixi.sh):

```bash
pixi run pipeline        # every stage: split, train, evaluate, card, bundle
pixi run known-answers   # the known answers, against the bundle
pixi run serve           # the API at http://localhost:8080 (POST /classify)
pixi run check           # format, lint, and check the config and pins
```

MLflow, the models and the bundle are written to `data/`, which is
git-ignored.

## Licence

AGPL-3.0-only, like the platform. The Palmer Penguins data in `dataset/` is
CC0 (see `dataset/README.md`).
