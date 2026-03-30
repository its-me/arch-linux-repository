# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository automates daily builds of the Google Fonts TrueType collection as an Arch Linux package (`ttf-google-fonts-git-daily`). There is no application code — the repo consists of a GitHub Actions workflow and a PKGBUILD template.

## Repository Structure

- `.github/workflows/create-package.yaml` — The sole CI/CD workflow; handles build, versioning, and release
- `PKGBUILD.template` — Template for the AUR-compatible package that end users install to get the fonts

## Workflow Logic

The workflow runs on push to `main`, daily at 02:00 UTC, and on manual dispatch:

1. **Hash comparison** — Fetches the latest commit SHA (9 chars) from `google/fonts` and compares it to the hash embedded in the latest GitHub Release tag. If they match, the workflow cancels itself via `gh run cancel`.
2. **Build** — Clones the upstream AUR package `ttf-google-fonts-git` and runs `makepkg --nodeps --noconfirm` as an unprivileged `builder` user inside an `archlinux:latest` container.
3. **Release** — Extracts the version from the built `.pkg.tar.zst` using `pacman -Qip`, then creates a GitHub Release with the package as an artifact.

## Release Tag Format

Tags follow the pattern `r{REVISION}.{HASH}` (e.g., `r12060.fc87bc9c8`), where:
- `REVISION` is the Google Fonts git revision count
- `HASH` is the first 9 characters of the Google Fonts commit SHA

The `PKGBUILD.template` must be updated manually after a build to reflect the new `pkgver` and `sha256sums` when distributing via AUR.

## Key Details

- Compression: `zstd --ultra -20 -T0` (maximum compression, multi-threaded)
- The build runs as user `builder` (not root); `makepkg` requires a non-root user
- `PACKAGER` is set from `github.event.pusher.email` — this will be empty on scheduled/dispatch runs
- `actions: write` permission is required to cancel the workflow run when no changes are detected
