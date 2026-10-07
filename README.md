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

To recover or immediately deploy a known source revision, run **Build and deploy organization
site** from the Actions tab on this repository's `main` branch. Set `source_sha` to the full
40-character hexadecimal commit SHA from `RazorConsole/RazorConsole`; leaving it empty builds that
repository's current `main`. The workflow validates a provided value before checkout, and its
summary records both the requested ref and the commit that was actually built.

Source-repository automation can request the same exact revision through the workflow dispatch API:

```json
{
  "ref": "main",
  "inputs": {
    "source_sha": "0123456789abcdef0123456789abcdef01234567"
  }
}
```

The caller must replace the example with the source event's full commit SHA and authenticate as a
GitHub App or fine-grained token that can dispatch Actions workflows in this repository. A
repository-scoped `GITHUB_TOKEN` from `RazorConsole/RazorConsole` cannot trigger a workflow in this
repository. Scheduled and push runs continue to build source `main`, and the manual fallback
requires no cross-repository secret.
