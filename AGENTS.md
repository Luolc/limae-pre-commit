# Agent rules (this repository)

**Repository language: English.** Everything that lands here — README, YAML
comments, commit messages — is written in English, whatever language the
request came in. This repository is read by pre-commit users in any ecosystem,
so the Chinese default that applies to some of the owner's other repositories
does not apply here.

Cross-repository agent rules live in `~/.agents/AGENTS.md` and still hold; this
file only states what is specific to this repository.

## What this repository is for

One thing: letting `pre-commit` install limae from a prebuilt PyPI wheel
instead of compiling it. pre-commit has 21 languages and none of them is
"download a prebuilt binary"; `language: python` is the way in, because
pre-commit is itself written in Python. That language requires `pip install .`
to work at the root of the hook repository, which means a `pyproject.toml` at
the root — and upstream deliberately carries no Python project. Hence this
repository.

It has no build, no tests and no release process of its own.

## The version pin is generated, not authored here

The `limae==<version>` line in `pyproject.toml`, and the `v<version>` tags of
this repository, are written by the `mirror` job of the release workflow in
[Luolc/limae](https://github.com/Luolc/limae) (`.github/workflows/release.yml`),
after that version's wheels are live on PyPI. A hand edit is overwritten by the next
upstream release and leaves no trace of why it was made.

So: **do not edit that line, and do not tag this repository by hand.** Change
the upstream workflow instead, and let a release write the result.

Everything else here (`.pre-commit-hooks.yaml`, `README.md`, `LICENSE`, this
file) is hand-written and is never touched by the workflow. Changing the hook
definition — its `entry`, its `files` pattern, a new id — is an ordinary
change to this repository.

## Do not add `args:` to the hook definition

`--disable` / `--enable` replace limae's file configuration rather than adding
to it, silently. A rule selection baked into `entry` or `args` here would turn
off every consumer's `limae.toml`. Rule selection belongs in the consumer's
configuration file; the README says so, and this file says why it must stay
that way.
