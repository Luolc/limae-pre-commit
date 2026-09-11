# limae-pre-commit

A [pre-commit](https://pre-commit.com/) hook for
[limae](https://github.com/Luolc/limae), a Markdown linter that starts from
Chinese technical-writing typography rules.

Distributed as a standalone repository to enable installing limae via prebuilt
wheels from PyPI: this hook needs no Rust toolchain and compiles nothing.

## Usage

```yaml
repos:
  - repo: https://github.com/Luolc/limae-pre-commit
    rev: v0.13.2  # the limae version to use; see the tags of this repository
    hooks:
      - id: limae
```

`rev` is a tag of *this* repository, and each tag pins exactly one limae
version — `v0.13.2` here installs `limae==0.13.2`. The tags are written by
limae's own release workflow, so they track upstream releases one for one.

To have limae rewrite what it can instead of only reporting it, add
`args: ["--fix"]`. That is the one thing `args:` is for here — see below.

## Rules go in the config file, never in `args:`

limae's `--disable` / `--enable` flags **replace** the file configuration
rather than adding to it: when either is present on the command line,
`limae.toml` and `pyproject.toml` are not read at all, and nothing warns you.
So a hook written like this silently throws away whatever the repository
itself configured:

```yaml
# Wrong: this turns off every setting in the repository's limae config.
hooks:
  - id: limae
    args: [--disable, zh-typography-3]
```

Put rule selection in the configuration file instead, where the repository's
own tooling and this hook read the same thing:

```toml
# limae.toml, at the repository root
disable = ["zh-typography-3"]
```

limae looks for its configuration starting from the directory of the file
being checked and walking upwards, **stopping at the first directory that
contains `.git`**. In each directory it tries `limae.toml` first, then the
`[tool.limae]` table of `pyproject.toml`. The two are the same keys; only
`pyproject.toml` puts them under a table header. A Node project therefore uses
`limae.toml` — `package.json` is not a configuration source.

The full set of keys is documented in
[limae's README](https://github.com/Luolc/limae#readme).

## No Windows wheels

limae ships prebuilt binaries for Linux x86_64 / aarch64 and macOS x86_64 /
arm64. There is no Windows build, so on Windows `pip install limae` finds no
wheel and this hook fails to install.

On Windows, use the hook in the upstream repository instead, which builds from
source and needs a Rust toolchain:

```yaml
repos:
  - repo: https://github.com/Luolc/limae
    rev: v0.13.2
    hooks:
      - id: limae
```

Both hooks are maintained; they are alternatives, not a migration. The
trade-off is in
[limae's README](https://github.com/Luolc/limae#readme).

## This repository is generated

Everything here except the `limae==` pin in `pyproject.toml` is hand-written,
and that pin is rewritten by limae's release workflow on every version tag. Do
not edit it by hand; the next release overwrites it.

Issues and pull requests belong upstream, at
[Luolc/limae](https://github.com/Luolc/limae).

## License

Apache License, Version 2.0 — the same as limae. See [`LICENSE`](LICENSE).
