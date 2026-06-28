# lemmata-releases

Public distribution channel for **`lem`**, the verified package manager for Dafny.

The `lem` source lives in a separate (private) repository. This repo hosts only the
**prebuilt release archives** so that package managers — e.g. [Scoop](https://scoop.sh)
— can download them anonymously.

Each `lem-<version>-<rid>.zip` is **self-contained**: it bundles a pinned Dafny + Z3,
so you need neither the .NET runtime nor a separate Dafny install, and your PATH is
left untouched.

## Install (Windows, via Scoop)

```
scoop bucket add lemmata https://github.com/hath995/scoop-lemmata
scoop install lem
lem doctor
```

## Licensing

`lem` is proprietary — all rights reserved (see the `LICENSE` inside the archive).
The bundled Dafny and Z3 are MIT-licensed; their license texts ship under `./dafny`
in the archive (see `THIRD-PARTY-NOTICES.txt`).

> The shipped `lem.exe` is currently **unsigned**, so Windows SmartScreen may warn on
> first run.
