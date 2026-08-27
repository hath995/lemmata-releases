# lemmata-releases

Public distribution channel for **`lem`**, the verified package manager for Dafny.

The `lem` source lives in a separate (private) repository. This repo hosts only the
**prebuilt release archives** so that package managers — e.g. [Homebrew](https://brew.sh)
and [Scoop](https://scoop.sh) — can download them anonymously.

Each release archive is **self-contained**: it bundles a pinned Dafny + Z3, so you need
neither the .NET runtime nor a separate Dafny install, and your PATH is left untouched.
Linux builds ship as `lem-<version>-linux-x64.tar.gz`; Windows as
`lem-<version>-win-x64.zip`; macOS as `lem-<version>-osx-arm64.tar.gz` /
`-osx-x64.tar.gz` (native per-arch — no Rosetta). Every archive has a matching
`.sha256`.

## Install

### Linux (Debian / Ubuntu — apt)

```
curl -fsSL https://apt.lemmata.sh/lemmata-release.pub \
  | sudo gpg --dearmor -o /usr/share/keyrings/lemmata-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/lemmata-archive-keyring.gpg] https://apt.lemmata.sh stable main" \
  | sudo tee /etc/apt/sources.list.d/lemmata.list
sudo apt update && sudo apt install lem
lem doctor
```

Upgrade later with `sudo apt upgrade`.

The repository index is GPG-signed, and `signed-by=` pins it to that one key, so
apt will only ever install packages signed by it. amd64 only; a single `stable`
codename serves jammy (22.04), noble (24.04), resolute (26.04) and newer
Debian/Ubuntu alike, because the bundled Dafny is built against glibc 2.31 and is
forward-compatible.

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

### Direct download

Grab an archive from a [release](https://github.com/hath995/lemmata-releases/releases),
verify it against the matching `.sha256`, unpack it, and put the resulting
directory on your `PATH`.

Two things to know when unpacking manually:

- The archive is self-contained but **not a single file**. `lem` must stay
  alongside the `dafny/` directory and the runtime files shipped with it — add the
  directory to `PATH`, or symlink to `lem` inside it, rather than moving the
  binary out on its own.
- On a **minimal** Linux image (containers, Ubuntu Server) the bundled runtime
  also needs ICU: `sudo apt install libicu-dev`. Without it `lem version` works
  but `lem doctor` exits. The `.deb` above pulls this in for you.

## Licensing

`lem` is proprietary — all rights reserved (see the `LICENSE` inside the archive).
The bundled Dafny and Z3 are MIT-licensed; their license texts ship under `./dafny`
in the archive (see `THIRD-PARTY-NOTICES.txt`).

> The shipped binaries are currently **unsigned**. On Windows, SmartScreen may warn on
> first run. On macOS, Homebrew strips the download quarantine on install, so `lem` runs
> without a Gatekeeper prompt.
