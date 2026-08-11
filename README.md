# TinyWrite — public deploy target

This repository contains **no application source**. It exists only to publish TinyWrite to GitHub
Pages.

TinyWrite is a local-first, client-only PWA: everything runs in your browser, and its AI model runs
on your device. The app's source lives in a private repository; because the app is client-only, the
*built* bundle is public as soon as the site is live, so only the build is mirrored here.

**Live site:** https://manivannan-mp.github.io/tinywrite-site/

## How a deploy happens

1. The private repo builds the site on every push to its `main`, with the on-device model weights
   included.
2. It uploads the result as the `site-latest` release asset here, then sends a
   `repository_dispatch` of type `deploy-site`.
3. `.github/workflows/pages.yml` downloads that asset and publishes it to Pages.

The site is deployed through the Actions artifact route rather than a `gh-pages` branch: the model
is a single ~497 MB file, and git rejects anything over 100 MB. The published site must stay under
the ~1 GB Pages limit — the current build is ~587 MB.

## Do not edit this repo by hand

Both files here — this README and `.github/workflows/pages.yml` — are **synced from the private
repo** (`deploy/tinywrite-site/`) on every publish, and local edits are overwritten. Change them
there.

## Setup (one time)

- **Settings → Pages → Source: GitHub Actions.**
- In the *private* repo, add a **repository** secret `SITE_DEPLOY_TOKEN`: a fine-grained PAT scoped
  to **this repo only**, with `Contents: Read and write` and `Workflows: Read and write` (the
  latter is what allows the workflow file above to be synced). The blast radius stays a repository
  that is already public.

To deploy without a source change, run this repo's **Deploy (GitHub Pages)** workflow manually — it
re-publishes whatever `site-latest` currently holds.
