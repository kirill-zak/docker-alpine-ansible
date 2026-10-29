# AGENTS.md

## About the project
docker-alpine-ansible is an Alpine Linux (3.21–3.23) image with Ansible
pre-installed, designed for testing playbooks and roles. All versions live in a
single repository and are published as per-release tags to Docker Hub and GHCR.

## Repository structure
- `alpine<branch>/` — separate build context: `Dockerfile`, `.dockerignore`.
- `.github/workflows/`:
  - `ci.yml` — checks on PR/push to master; calls reusable `test-image.yml`.
  - `build.yml` — manual build check across all platforms (`workflow_dispatch`);
    calls reusable `build-image.yml` with `push: false`.
  - `release.yml` — publishing on tags: `validate-tag` → `config` → `test` →
    matrix `release` (reusable `build-image.yml`, `secrets: inherit`).
  - `config.yml` — reusable workflow, single source of constants: outputs
    `versions` (JSON array of release tags), `platforms` (JSON array),
    `latest_version` (string); callers drive their matrices from
    `needs.config.outputs.*`.
  - `test-image.yml`, `build-image.yml` — reusable workflows with shared steps.
- `Makefile` + `scripts/test.mk` — `make test-build` smoke test mirroring
  `test-image.yml`; the version is set via the `version_branch` variable (default:
  the latest supported release).
- `.context/optimize-ci.md` — CI/CD architecture plan and rationale (read before
  changing the workflows).
- `.gitignore` — files kept out of git (`.context`, `.omo`).

## Versioning
- Images are published per Alpine release tag (e.g. `3.21.0` … `3.21.9`,
  `3.23.4`), not per branch.
- A release tag implies a branch: `3.21.5` → branch `3.21` → build context
  `alpine3.21`. The build must pass `--build-arg ALPINE_VERSION=<full release>`
  to select the base image.
- `version_branch` (workflow input and Makefile variable) holds a full release;
  the branch (context) is derived by stripping the last dot segment.

## Build and test
- Local build: `cd alpine3.23 && docker build -t docker-alpine-ansible . --build-arg ALPINE_VERSION=3.23.4`
- Smoke test, as in CI (`test-image.yml`):
  1. `podman build --build-arg ALPINE_VERSION=3.23.4 -t docker-alpine-ansible:3.23.4 ./alpine3.23/.`
  2. `podman run --name test-container --detach --privileged \
     --volume /sys/fs/cgroup:/sys/fs/cgroup:ro docker-alpine-ansible:3.23.4`
  3. `podman exec --tty test-container env TERM=xterm ansible --version`
- Or via the Makefile: `make test-build` (override the version with
  `make test-build version_branch=3.22.4`).

## CI/CD conventions
- Release matrix: `3.21.x`, `3.22.x`, `3.23.x` (full list in the
  `versions` output of `config.yml`).
- The `latest` tag maps to the newest release (`3.23.4`), set via
  `latest_version` in `config.yml`.
- Versions, platforms, and the `latest` mapping live in `config.yml` — edit it to
  change the build/test matrix; no other workflow needs editing for a version change.
- `3.16.x`–`3.20.x` are archived — no longer built or published (see README,
  "Archive tags").
- Platforms: `linux/386`, `linux/amd64`, `linux/arm/v6`, `linux/arm/v7`,
  `linux/arm64/v8`, `linux/ppc64le`, `linux/s390x`.
- Release tag: `x.x` or `x.x.x`, optional `v` prefix
  (regex `^v?[0-9]{1,2}\.[0-9]{1,2}(\.[0-9]{1,2})?$`), no suffixes — enforced by
  the `validate-tag` job.
- `GITHUB_TOKEN` permissions are minimal: workflow-level `contents: read`.
  Registry-capable callers grant `packages: write` on their calling job
  (`release` in `release.yml`, `test-build` in `build.yml`), and `build-image.yml`
  requests it on the `build` job. Note: a called workflow's token is the
  *intersection* of the caller job's and the called job's permissions.
- Container tool in CI and `scripts/test.mk` is `podman` (pre-installed on the
  GitHub runner), mirroring docker-ubuntu-ansible.
- Images are published to Docker Hub (`kirillzak/docker-alpine-ansible`) and GHCR
  (`ghcr.io/<owner>/<repo>`).
- Once the work on a branch is complete and the PR passes all checks, merge it
  into `master` with the **delete branch** option enabled, so the remote work
  branch is removed from the repository after the merge.

## Commit message conventions
Pull requests and commit messages are written in English.

Use Conventional Commits:

- New feature: `feat: <description>`
- Bug fix: `fix: <description>`

Guidelines:

- The subject (first line) should be as short as possible; aim for ≤72 characters
  (GitHub truncates longer subjects with an ellipsis).
- Hard limit: the entire commit message (subject + optional body) must not exceed
  140 characters.
- If more context is needed, add a body separated by a blank line; keep the total
  message within the 140-character limit.
- Other Conventional Commits types may be used when `feat`/`fix` do not fit:
  `docs:`, `refactor:`, `chore:`, `ci:`, `test:`.

## How to add a new Alpine version
1. Create `alpine<branch>/` with `Dockerfile` and `.dockerignore`.
2. Add the release tag(s) to the `versions` output in `.github/workflows/config.yml`
   (and update `latest_version` there if a new release becomes `latest`); add the
   tags to README.
3. If the new release is the latest, update the `version_branch` default in
   `scripts/test.mk`.
4. Run `workflow_dispatch` on `build.yml` and run the local smoke test
   (`make test-build`).