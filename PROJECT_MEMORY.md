# Project Memory — ServiceMq fork (MPCoreDeveloper)

Internal operational notes for the MPCoreDeveloper fork of [tylerjensen/ServiceMq](https://github.com/tylerjensen/ServiceMq).
Last updated: 2026-09-13.

## Repos & remotes
- Workspace: `d:\repos\MPCoreDeveloper\ServiceMq` — main work branch `feature/sharpcoredb-provider`
- `fork`  = https://github.com/MPCoreDeveloper/ServiceMq.git (we can push here)
- `origin` = https://github.com/tylerjensen/ServiceMq.git (Tyler — **READ-only for us**, `push: false`)

## NuGet ownership — IMPORTANT (corrected 2026-09-13)
- `ServiceMq.SharpCoreDb` on nuget.org is **OWNED by `tylerjensen`** (the nuget.org "Owners" field).
- `Authors: MPCoreDeveloper` in the catalog is **only the csproj `<Authors>` tag**, NOT ownership. Do not confuse the two again.
- Only version published so far: **7.1.0** (SharpCoreDB 1.9.3, **no icon**). `7.1.1 / 7.2.0 / 7.3.0` are NOT on nuget.org yet.
- 7.1.0 was pushed by **Tyler** (owner = first pusher) on 2026-08-22 ~14:43Z, built from merge commit `d62abbb`, alongside `ServiceMq` (14:42:11Z) and `ServiceMq.Sqlite` (14:42:37Z).
- To publish future versions (e.g. 7.3.0 with the logo) either:
  1. **Tyler publishes** after merging PR #2 (how 7.1.0 was done), or
  2. **Tyler adds MPCoreDeveloper as nuget.org co-owner** (package page → Manage owners) — then our `NUGET_API_KEY` CI workflow can publish.
- Otherwise nuget.org rejects the push with 401. **Do NOT run the publish workflow until ownership is granted.**
- Since our account has only READ on the git repo, we also **cannot merge PR #2** — only Tyler can.

## Open work / releases
- **PR #2** — https://github.com/tylerjensen/ServiceMq/pull/2 (`MPCoreDeveloper:feature/sharpcoredb-provider` → `master`), OPEN + CLEAN. Contains:
  - `0001e37` SharpCoreDB **2.0.0.2** engine (package 7.1.1)
  - `78b4d05` perf: default stores to fastest correct engine mode
  - `979b84e` SharpCoreDB → **2.0.0.3**, SonarCloud high-severity fixes, package 7.2.0
  - `620fab4` async API (`IAsyncMessageStore` + async `MessageQueue` methods), package 7.3.0
  - `4622a6b` docs/comments synced to pin 2.0.0.3
  - `b4960f2` **logo**: `docs/images/servicemq-logo.png` (Tyler's official 1.6.4 icon) wired as `<PackageIcon>` in `ServiceMq.SharpCoreDb.csproj`; verified packed in nupkg
  - `613e1ab` / `db7eb1d` CI workflows + `sonar-project.properties`
- The logo only shows on nuget.org **after a new version is published** (existing 7.1.0 cannot be retrofitted).

## Branches
- `fork/master` = upstream `cb0acee` + our CI commit `6096a34` (workflow files + sonar-project.properties, needed for `workflow_dispatch`).
- `fork/feature/sharpcoredb-provider` = `db7eb1d` (6-7 commits ahead of upstream, 0 behind).
- `fork/dev` = `origin/dev` = `3546956`.
- Historical gotcha: GitHub showed the fork default branch "behind" Tyler because fork `master` was never updated; fixed by fast-forwarding to `cb0acee`. Our feature branch is always the branch carrying new work (ahead, not behind).

## CI / GitHub Actions (on fork `master` and feature branch)
- `.github/workflows/publish-nuget.yml` — uses `NUGET_API_KEY`; triggers `workflow_dispatch` + `v*` tags; `dotnet pack` `ServiceMq.SharpCoreDb` → `dotnet nuget push --skip-duplicate`. Gated by a `check-nuget-key` job.
- `.github/workflows/sonarcloud.yml` — uses `SONAR_TOKEN`; triggers `pull_request` (base master), `push` master, `workflow_dispatch`. Scanner: org `mpcoredeveloper`, projectKey `MPCoreDeveloper_ServiceMq` (from `.sonarqube/out/sonar-project.properties`).
- Workflow gotcha: **GitHub Actions does not allow `secrets` in job-level `if`** ("Unrecognized named-value: 'secrets'", run fails with 0 jobs / no logs). Fixed with a check-job → `needs.<id>.outputs` gate (commit `db7eb1d`).

## Current incidents / service status
- **SonarCloud is DOWN / under maintenance** as of 2026-09-13 ~07:55Z: `https://sonarcloud.io/api/server/version` returned **503 ServiceUnavailable**; scanner begin fails with "Pre-processing failed. Exit code: 1". **Retry Sonar runs later — do not change workflow config for this.**
- Sonar run failures observed 2026-09-13 on fork (`db7eb1d` dispatched + `6096a34` master push) are purely the outage.
