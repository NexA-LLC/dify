# Fork runner allocation

This public fork sends Python Style, Web Style and Web Tests to
`[self-hosted, macOS, ARM64, buildchan]` only for trusted events owned by
`NexA-LLC/dify`: same-repository pull requests, pushes and manual dispatches.
External fork pull requests, pull_request_target and all other event types keep
the original `ubuntu-latest` runner. The selector does not skip any check.

Python keeps setup-uv/setup-python, the full locked API/default/dev dependencies,
Ruff and dotenv checks. uv uses its project virtual environment. Native ARM64
Python 3.12 offline locked dry-run with dev groups succeeds; dependencies/lint
were not installed/run locally, and live CI is still required. Web checks keep
Node 22, pnpm 10, frozen installs, lint/tests and existing changed-file guards.

## Hosted exceptions

- `api-tests.yml` / `test`: The complete API suite includes hoverkraft Compose middleware/integration jobs; Linux Docker execution is retained.
- `build-push.yml` / `build`: Linux/amd64 and Linux/arm64 image builds, QEMU/buildx and publication retain their platform runner choices.
- `build-push.yml` / `create-manifest`: Linux/amd64 and Linux/arm64 image builds, QEMU/buildx and publication retain their platform runner choices.
- `db-migration-test.yml` / `db-migration-test`: Docker Compose services and the complete DB migration gate remain on Linux.
- `deploy-dev.yml` / `deploy`: appleboy/ssh-action@v0.1.8 is a Docker action; keep the existing deploy gate on Linux.
- `deploy-enterprise.yml` / `deploy`: appleboy/ssh-action@v0.1.8 is a Docker action; keep the existing deploy gate on Linux.
- `deploy-rag-dev.yml` / `deploy`: appleboy/ssh-action@v0.1.8 is a Docker action; keep the existing deploy gate on Linux.
- `docker-build.yml` / `build-docker`: QEMU/buildx Docker image checks retain their Linux execution path.
- `stale.yml` / `stale`: Scheduled issue mutations remain on the original hosted infrastructure; no issue automation is dispatched for migration verification.
- `style.yml` / `docker-compose-template`: Docker Compose generator keeps its existing Linux shell environment pending native validation.
- `style.yml` / `superlinter`: Super-linter slim is a Docker action and requires Linux.
- `tool-test-sdks.yaml` / `build`: Preserve Node 16/18/20/22 matrix and frozen-install test commands. The SDK has no tracked pnpm lockfile or explicit pnpm bootstrap, so native SDK qualification needs a separate dependency/reproducibility fix; no versions/checks are dropped.
- `translate-i18n-base-on-english.yml` / `check-and-update`: Translation/PR-writing automation remains on original hosted infrastructure; no generation or PR automation is dispatched for migration verification.
- `vdb-tests.yml` / `test`: Compose vector database services plus Linux-only free-disk-space action retain Linux.

## Operations

Keep this shared runner available only to trusted code. Changing the fork name,
trust policy or supported event list requires re-review of the selector.
Triggers, schedules, test matrix, conditions, release/publication gates and
permissions are unchanged. No deploy, publish, issue mutation, translation or
database operation is manually triggered during this migration.

Verify with actionlint, `git diff --check`, selector event cases and structural
comparison of all workflow gates. Revert this commit to restore runner allocation.

## Relocatable Python on buildman6

The hosted setup-python Darwin ARM64 installer expects a `/Users/runner` prefix,
which the actual `buildman` runner account cannot create. Native jobs preserve
their Python version requests and use pinned setup-uv v7 / uv 0.13.1 with the
official ARM64 checksum. Managed Python, seeded venvs, uv tools/cache/bin and pip
cache all stay in `RUNNER_TEMP`; no account/global Python or permissions change.
Existing dependency and test commands remain.

The composite keeps its original setup-python and uv-version inputs on hosted
Linux/fork jobs. Only trusted Mac ARM64 jobs use this relocatable path. The public
fork runner selector and all lint assertions stay unchanged.
