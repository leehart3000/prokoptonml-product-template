# Starting a new product

From this template to a product of your own, served on Google Cloud Run.
Each step says what to change; the checks (`pixi run check`, CI and the
known answers) say whether it worked.

## 1. Make the repository

1. On GitHub, "Use this template" to create the product's repository.
2. Protect `main` and the release tags as the platform does: changes by pull
   request only (squash), CI and the secret scan required, and `v*` tags
   immutable.
3. Turn on CodeQL's default setup, Dependabot alerts and secret scanning with
   push protection.

## 2. Describe the app

1. In `prokopton.toml`, set `name`, `title` and `[data] release`, and replace
   the Penguins model, stages and serving steps with your own. Every kind's
   settings are checked when the config is read: `pixi run check-config`
   says what is wrong and where.
2. Put the data where the config says (`dataset/` for a small table kept in
   the repository, or `data/`, which is git-ignored, for data the pipeline
   downloads), with its source, licence and citation.
3. Rename the workspace in `pixi.toml`, the API title in the `Dockerfile`,
   and the release name in the `release` task.
4. Replace the known answers in `tests/known-answers/`: each check is a
   request (`<name>.json`) and the start of the answer it must give
   (`<name>.expected`).

`pixi run pipeline` then `pixi run known-answers` must pass, locally and in
CI.

## 3. Set up Google Cloud

One project per product, with a budget and a Cloud Run spending cap, and the
least access each part needs:

1. An Artifact Registry repository for images, and a bucket for bundles (one
   dated folder per bundle, never overwritten).
2. The API key in Secret Manager, and a runtime service account that can read
   only that secret.
3. A deployer service account for GitHub Actions: write to the image
   repository, read the bundle bucket, act as the runtime account, and deploy
   the one Cloud Run service.
4. A Workload Identity pool and provider that trust only this repository
   (by its ID) and only `main` and `v*` tags.

Fill in `infra/deploy/app.env` (every `REPLACE-…` value).

## 4. Deploy

1. Build the bundle (`pixi run pipeline`) and upload `data/models/<name>/bundle`
   to a new dated folder in the bucket; set `BUNDLE` to it.
2. Run the Deploy workflow by hand from `main`, with "Check the live service"
   off for the very first deploy; make the service public (it still needs
   the API key), then run it again with the check on.
3. Release with `pixi run release v1.0.0`: it checks that `main` is ready,
   tags it, and follows the Release workflow to the end.

## 5. Move to a new platform version

The platform is pinned in three places, which `pixi run check` keeps in step:
`pixi.toml`, the `PROKOPTON_ML_VERSION` `ARG` in the `Dockerfile`, and the
deploy workflow (`.github/workflows/deploy.yml`), which calls the platform's
`deploy-cloud-run.yml` by its release's commit, with the tag as a comment:

```bash
V=0.2.1
SHA=$(gh api repos/leehart3000/prokoptonml/commits/v$V --jq .sha)
# then set ==$V in pixi.toml, ARG PROKOPTON_ML_VERSION=$V in the Dockerfile,
# and deploy-cloud-run.yml@$SHA # v$V in .github/workflows/deploy.yml
pixi install && pixi run check && pixi run pipeline && pixi run known-answers
```

Right after a platform release, if Pixi says there is no such version, its
cache of the PyPI index is stale: `pixi clean cache --pypi --yes`.
