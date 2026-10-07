# RazorConsole.github.io

Deployment repository for the RazorConsole organization site at
[https://razorconsole.github.io/](https://razorconsole.github.io/).

The website source remains authoritative in
[`RazorConsole/RazorConsole`](https://github.com/RazorConsole/RazorConsole/tree/main/website).
This repository only owns the organization Pages identity and deployment workflow; generated site
files are never copied into the repository.

## Deployment

[`pages.yml`](.github/workflows/pages.yml) checks out the source repository's `main` branch, records
the exact source commit in the workflow summary, builds the existing website with `/` as both the
Vite base and router basename, verifies the root canonical URLs, sitemap, Google verification file,
and browser navigation, then uploads the generated `website/build/client` directory unchanged apart
from the Pages `404.html` and `.nojekyll` files.

Pull requests run the complete build and verification but never deploy. Production deployment runs
only from this repository's `main` branch after a push, a manual `workflow_dispatch`, or the daily
scheduled source synchronization. Runs are serialized so an older build cannot overtake a newer one.

Before the first deployment, an organization owner must open **Settings → Pages → Build and
deployment** and set **Source** to **GitHub Actions**. The existing project site should not be changed
until the root URL and representative routes return HTTP 200.

To recover or immediately pick up a source change, run **Build and deploy organization site** from
the Actions tab on the `main` branch. GitHub's repository-scoped `GITHUB_TOKEN` cannot trigger a
workflow in another repository, so immediate source-driven deployments would require an explicitly
managed GitHub App or fine-grained token. The scheduled and manual paths require no cross-repository
secret.
