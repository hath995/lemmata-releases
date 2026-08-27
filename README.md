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

One thing to know when unpacking manually: the archive is self-contained but
**not a single file**. `lem` must stay alongside the `dafny/` directory and the
runtime files shipped with it — add the directory to `PATH`, or symlink to `lem`
inside it, rather than moving the binary out on its own.

On Linux, also see the ICU note below.

## Known issue: Linux archives need ICU

**Applies to:** the Linux `.tar.gz` on a minimal system — containers, Ubuntu
Server, CI images, slim base images.
**Does not apply to:** the apt package (it declares the dependency and apt
installs it for you), or Windows and macOS. Most desktop and developer Linux
installs already have ICU pulled in by something else and are unaffected.

**Symptom.** `lem` installs and `lem version` prints normally, then any real
command stops with:

```
Process terminated. Couldn't find a valid ICU package installed on the system.
```

That split is what makes this confusing: the binary looks fine right up until
you use it. `lem version` returns a constant, while `lem doctor` and the rest
touch culture-aware string handling, which is where the bundled .NET runtime
demands ICU and aborts rather than degrading.

**Fix — install ICU for your distribution:**

| Distribution | Package |
|---|---|
| Debian / Ubuntu | `sudo apt install libicu-dev` (or the versioned `libicu74` / `libicu72` / `libicu70`) |
| Fedora / RHEL / Rocky | `sudo dnf install libicu` |
| Arch | `sudo pacman -S icu` — usually already present |

**Alpine and other musl distributions are not supported by this archive at all**,
ICU or no ICU. The Linux build is glibc-linked, so the binary won't start there
(`./lem: not found`, which is the dynamic loader failing, not a missing file).

**Why the archive can't fix it for you.** A tarball carries no package metadata,
so unlike the `.deb` it has no way to declare a dependency — nothing checks
before you run it. The versioned name differs per distribution too (`libicu74`
on noble, `libicu70` on jammy, `libicu72` on bookworm), which is exactly the
kind of thing a package manager exists to resolve.

**Status.** Being addressed for a future release, either by building with
globalization-invariant mode so ICU isn't needed at all, or by failing with a
clear message that names the package instead of a raw runtime abort. Until then,
**installing from apt avoids this entirely** and is the recommended route on
Debian and Ubuntu.

## Licensing

`lem` is proprietary — all rights reserved (see the `LICENSE` inside the archive).
The bundled Dafny and Z3 are MIT-licensed; their license texts ship under `./dafny`
in the archive (see `THIRD-PARTY-NOTICES.txt`).

> The shipped binaries are currently **unsigned**. On Windows, SmartScreen may warn on
> first run. On macOS, Homebrew strips the download quarantine on install, so `lem` runs
> without a Gatekeeper prompt.
