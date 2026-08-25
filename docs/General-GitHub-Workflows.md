## github-release.yml
Create a new GitHub Release (Aggregate logs from "Maven Dependency Updates" action)

```yml
name: Create Release
on:
  workflow_dispatch:
  push:
    # Publish `v1.2.3` tags as releases.
    tags:
      - v*

jobs:
  release:
    uses: <<REPO>>/.github/workflows/github-release.yml@main
    
```

## openwiki.yml
Regenerate the [OpenWiki](https://www.npmjs.com/package/openwiki) documentation in `openwiki/` and open a pull request with the result.

The workflow does nothing when the documented branch received no relevant commits within `lookback-days`
(changes that only touch `openwiki/**` do not count), so it is cheap to schedule it weekly.

Requires an `OPENROUTER_API_KEY` secret. The calling workflow must grant `contents: write` and
`pull-requests: write`; a called workflow can only downgrade the token of its caller, never elevate it.

```yml
name: OpenWiki Update

on:
  workflow_dispatch:
  schedule:
    # Weekly, Monday 06:00 UTC
    - cron: "0 6 * * 1"

permissions:
  contents: write
  pull-requests: write

jobs:
  update:
    uses: <<REPO>>/.github/workflows/openwiki.yml@main
    secrets:
      OPENROUTER_API_KEY: ${{ secrets.OPENROUTER_API_KEY }}
```

| Input | Default | Description |
| --- | --- | --- |
| `branch` | `main` | The branch that is checked for recent commits and that OpenWiki documents. |
| `lookback-days` | `7` | Only update when the branch received relevant commits within this many days. Should match the schedule. |
| `node-version` | `22` | The Node.js version used to run OpenWiki. |
| `model-id` | `z-ai/glm-5.2` | The OpenRouter model id that OpenWiki uses to generate the documentation. |
