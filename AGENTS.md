# AGENTS.md

## Repo layout

This is a meta/docs repo, not a code repo. The root contains only `README.md` and `.gitmodules`; all code lives in three git submodules, each an independent Go CLI/TUI built with Bubble Tea + Lip Gloss:

- `AttackLogcatCLI` — `adb logcat` reader/filter (TUI + headless)
- `HTDotFile` — dotfile manager (has its own `AGENTS.md` — read it before editing)
- `aabToApk` — `.aab` → universal `.apk` converter via bundletool

The root repo tracks only submodule commit SHAs. The Vietnamese root README is the main deliverable; keep its version numbers and feature claims in sync with each submodule's `VERSION` file.

## Working in submodules

- Go 1.24 for HTDotFile, Go 1.22 for the other two (per `go.mod` `go` directive).
- Entrypoint: `cmd/<tool>/main.go`; logic in `internal/*`.
- Verify with `go test ./...`; build with `go build -ldflags "-X main.version=$(cat VERSION)" ./cmd/<tool>`.
- CI runs on the self-hosted `light` runner: `go test ./...`, `go vet ./...`, and cross-platform builds for every supported chip. Pushing a `v*` tag creates a GitHub release with all binaries attached. No lint config anywhere. HTDotFile convention: always run the app once (`go run ./cmd/htdot`) after code changes, before running tests.

## Git gotchas

- When the user says "commit" (or similar, e.g. "commit và push"), commit immediately using conventional commit style (`type: subject`, e.g. `feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`) and push right away — no confirmation, no reminders. Submodule changes first (pushed), then root gitlink bump + root docs (pushed).
- Code changes must be committed and pushed **inside the submodule first**, then `git add <submodule>` in the root to record the new SHA and push the gitlink. Committing at the root alone captures nothing useful.
- Fresh clone: `git submodule update --init --recursive`. `AttackLogcatCLI` and `HTDotFile` use SSH remote URLs (`git@github.com:Association-of-stupid-people/...`); `aabToApk` uses https — without an SSH key only `aabToApk` will clone.

## Release (run inside each submodule)

- `scripts/build.ps1` cross-compiles 5 targets into `dist/` plus `SHA256SUMS` and `latest.json`.
- `scripts/release-r2.ps1` uploads to Cloudflare R2 (bucket `cli`, per-tool prefix). Requires `scripts/r2.env` copied from `r2.env.example` — it is gitignored and holds credentials; never commit it.
- Full flow and URL config: `docs/DEPLOY-R2.md` in each submodule.
