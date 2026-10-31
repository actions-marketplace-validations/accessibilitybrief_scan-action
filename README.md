# AccessibilityBrief scan

A GitHub Action that scans a site with [AccessibilityBrief](https://accessibilitybrief.com) and fails the job when serious accessibility issues appear or the score drops.

```yaml
name: Accessibility
on:
  push:
    branches: [main]

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: accessibilitybrief/scan-action@v1
        with:
          url: https://staging.example.com
          api-key: ${{ secrets.ACCESSIBILITYBRIEF_API_KEY }}
          fail-on: serious
```

The scan runs on AccessibilityBrief's servers against WCAG 2.2 A and AA, at desktop and phone size. The log shows the score and the issues found; the job summary shows a table. The full report, with the exact fix for each issue, is in your AccessibilityBrief account.

## Setup

1. You need an AccessibilityBrief **Pro** or **Agency** plan, and the site must be added to your workspace.
2. Create an API key under **Settings → API and integrations** with the `read` and `scan` scopes.
3. Add it to your repository as a secret named `ACCESSIBILITYBRIEF_API_KEY` (**Settings → Secrets and variables → Actions**).

Each run uses one manual scan of your plan. Scan a deployed site (production, staging or a preview URL), not `localhost`.

## Inputs

| Input | Required | Default | |
|---|---|---|---|
| `url` | yes | | Address or site id of a site in your workspace |
| `api-key` | yes | | API key with the `read` and `scan` scopes |
| `fail-on` | no | | Fail if an issue is at least this severe: `critical`, `serious`, `moderate` or `minor` |
| `min-score` | no | | Fail if the automated score is lower (0 to 100) |
| `locale` | no | `en` | Language of issue titles: `en` or `de` |
| `timeout` | no | `20` | Minutes to wait for the scan |
| `cli-version` | no | `1` | Version of the [accessibilitybrief](https://www.npmjs.com/package/accessibilitybrief) command line tool |

Without `fail-on` or `min-score` the step reports the result and never fails on it. Errors such as a wrong key or an unknown site always fail the step.

## Without this action

The action runs the command line tool, so any CI system can do the same:

```bash
ACCESSIBILITYBRIEF_API_KEY=… npx accessibilitybrief scan https://example.com --fail-on serious
```

[Documentation](https://accessibilitybrief.com/docs/cli-and-mcp)

Automated testing detects only a portion of WCAG issues; manual testing is still required.
