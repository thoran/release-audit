# release-audit

Report which repositories beneath a root have a release left half done.

A release passes through stages — the work is committed, the version is tagged,
the branch and the tag are pushed, and where the repository is a gem, the version
is published. Each stage can be reached and the next left undone, and nothing
says so. `release-audit` walks a root for repositories and says, per repository,
which stage each one stopped at.

## Installation

### 0. Have a recent version of Ruby installed

### 1a. Via Homebrew

```shell
$ brew tap thoran/tap
$ brew install thoran/tap/release-audit
```

### 1b. Manually

```shell
git clone https://github.com/thoran/release-audit
cp ./release-audit/bin/release-audit to your preferred executable path
chmod +x /path/to/release-audit
```

## Usage

```shell
$ release-audit --root ~/code
```

With no root named, the current directory is used. A root may also be configured,
so that the common case takes no arguments at all:

```shell
$ cat ~/.config/release-audit/config.json
{"root": "~/code/scm/github.com/thoran"}
```

Only repositories with something outstanding are listed. `--all` lists every one.

## What it reports

| | |
| --- | --- |
| `uncommitted` | the working tree has changes which no commit holds |
| `untagged` | a version the repository claims, with no tag naming it |
| `unpushed` | commits on the release branch which the remote does not have |
| `no upstream` | the branch tracks nothing, so pushed cannot be answered |

A version the repository claims is one named in a commit subject, in the form
`1.2.3: summary`.

## What it does not report yet

Two of the five stages need the network and are not asked here: whether the tag
the repository holds is the tag the remote holds, and whether the version is
published. Both are described in section 4 of `missing-tools-note.md`, and
`tap-audit`'s `remote_findings` already does the first for a tap.

## Exit status

1 where anything was found, 0 where nothing was, so it can gate a release.

## Contributing

1. Fork it: `https://github.com/thoran/release-audit/fork`
2. Create your feature branch: `git checkout -b my-new-feature`
3. Commit your changes: `git commit -am 'Add some feature'`
4. Push to the branch: `git push origin my-new-feature`
5. Create a new pull request

## License

MIT
