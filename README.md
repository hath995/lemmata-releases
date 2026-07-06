# lemmata-releases

Public distribution channel for **`lem`**, the verified package manager for Dafny.

The `lem` source lives in a separate (private) repository. This repo hosts only the
**prebuilt release archives** so that package managers — e.g. [Homebrew](https://brew.sh)
and [Scoop](https://scoop.sh) — can download them anonymously.

Each release archive is **self-contained**: it bundles a pinned Dafny + Z3, so you need
neither the .NET runtime nor a separate Dafny install, and your PATH is left untouched.
Windows builds ship as `lem-<version>-win-x64.zip`; macOS builds as
`lem-<version>-osx-arm64.tar.gz` / `-osx-x64.tar.gz` (native per-arch — no Rosetta).

## Install

### macOS (Homebrew)

```
brew tap hath995/lemmata
brew install lem
lem doctor
```

Upgrade later with `brew upgrade lem`.

### Windows (Scoop)

```
scoop bucket add lemmata https://github.com/hath995/scoop-lemmata
scoop install lem
lem doctor
```

Upgrade later with `scoop update lem`.

## Licensing

`lem` is proprietary — all rights reserved (see the `LICENSE` inside the archive).
The bundled Dafny and Z3 are MIT-licensed; their license texts ship under `./dafny`
in the archive (see `THIRD-PARTY-NOTICES.txt`).

> The shipped binaries are currently **unsigned**. On Windows, SmartScreen may warn on
> first run. On macOS, Homebrew strips the download quarantine on install, so `lem` runs
> without a Gatekeeper prompt.
