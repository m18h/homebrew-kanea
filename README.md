# homebrew-kanea

The Homebrew tap for [Kanea](https://github.com/m18h/kanea) — container
orchestration in one binary.

## Install

```bash
brew tap m18h/kanea
brew trust m18h/kanea   # brew ≥ 6 refuses formulae from untrusted third-party taps
brew install kanea
```

Homebrew ships the **CLI**, not the node (Kanea PRD §5.2.12): on macOS you
get the authoring half — `kanea plan` validates job specs with file-and-line
diagnostics, no daemon needed — while the platform itself runs on Linux. On
a Linux machine the formula installs the same full binary, but a *node*
belongs to the [install script](https://github.com/m18h/kanea#install):
root-owned at `/usr/local/bin`, where `kanea upgrade` owns the swap. A
brew-owned binary upgrades with `brew upgrade kanea`, then `sudo kanea
upgrade --no-fetch` for the restart-and-migrate half.

## How the formula is updated

`Formula/kanea.rb` is generated output — **do not edit it by hand**.
`scripts/update-formula.sh` is its only author: the `update` workflow polls
[m18h/kanea](https://github.com/m18h/kanea)'s latest release hourly,
downloads that release's `checksums.txt`, and regenerates the formula — the
version and hashes are never typed, and the asset names are read out of the
checksum file. The arrangement is deliberately credential-free: kanea's
release workflow writes only to its own repository, and this one needs
nothing beyond its own `GITHUB_TOKEN`.

CI style-checks the formula, regenerates it from the release it names and
diffs (a hand edit fails by name), and install-tests it on Linux and macOS.
