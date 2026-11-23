# CyCognito fork of Katana

Public fork of [projectdiscovery/katana](https://github.com/projectdiscovery/katana) (MIT, see `LICENSE.md`)
used by `cartel_katana_worker`. The worker image builds Katana from a tag of this fork.

## What differs from upstream

| Commit | Change |
|---|---|
| `fix(headless): de-duplicate emitted requests by method and URL` | Rows were de-duplicated by URL only. A CORS preflight (`OPTIONS /api/x`) registered the URL, so the real `POST /api/x` that follows was dropped, hiding cross-host JSON APIs. Non-GET requests now use `METHOD URL` as the key; GET keys are unchanged. |

Keep this list short. Anything that is general should also be sent upstream.

## Tag naming

Tags are `v<upstream version>-cartel.<n>`, for example `v1.8.0-cartel.1`.
The worker build reads the upstream version from the tag name and fails when upstream
has published a newer release (see `build_katana.sh` in `cartel_katana_worker`).

## Moving to a new upstream release

```bash
git remote add upstream https://github.com/projectdiscovery/katana.git   # once
git fetch upstream --tags
git checkout -b cartel/v1.9.0 v1.9.0
git cherry-pick <commits of the previous cartel/v* branch that are not upstream>
# run: go test ./pkg/engine/headless/...
git tag v1.9.0-cartel.1 && git push origin cartel/v1.9.0 v1.9.0-cartel.1
```

Then bump `KATANA_FORK_REF` in the worker `Dockerfile`.
