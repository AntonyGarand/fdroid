# fdroid.garand.dev

A self-hosted [F-Droid](https://f-droid.org) repository for garand.dev apps. Add it to any F-Droid client:

```
https://fdroid.garand.dev/repo?fingerprint=628645046A543FDA62129032FE021D780B76ACF62548C6F25E0584134A59B04F
```

## How it works

`.github/workflows/publish.yml` runs every 6 hours, on push, and on demand (`gh workflow run publish.yml -R AntonyGarand/fdroid`):

1. Downloads the APKs from the last 5 GitHub releases of each repo in `sources.txt`. Each app's own release key has already signed them.
2. Runs `fdroid update` (fdroidserver), which reads `metadata/<package id>.yml` and builds and signs the index with the repo key.
3. Deploys `site/` (landing page, CNAME) + `repo/` to GitHub Pages.

Nothing built is committed: `repo/` is regenerated on every run.

## Adding an app

1. Make the app's repo attach a signed APK to each GitHub release.
2. Add `owner/repo` to `sources.txt` and a `metadata/<package id>.yml` (see the existing one). Declare anti-features honestly.

## Secrets

- `FDROID_KEYSTORE_B64`: base64 of the repo signing keystore (PKCS12, alias `repokey`). Local copy: `~/.secrets/fdroid-repo-keystore.p12`.
- `FDROID_KEYSTORE_PASSWORD`: its password. Local copy: `~/.secrets/FDROID_REPO_KEYSTORE_PASSWORD`.

Users pin the repo key's fingerprint when they add the repo. **Losing the key means everyone has to re-add the repo.**

## Local run

```sh
pip install fdroidserver==2.4.5
cp ~/.secrets/fdroid-repo-keystore.p12 fdroid-keystore.p12
FDROID_KEYSTORE_PASSWORD=$(cat ~/.secrets/FDROID_REPO_KEYSTORE_PASSWORD) fdroid update
```

On Windows, add `apksigner: <sdk>/build-tools/<ver>/apksigner.bat` to `config.yml` locally. Don't commit it.
