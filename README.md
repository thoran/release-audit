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

### 1b. From a checkout, symlinked

```shell
$ git clone https://github.com/thoran/release-audit
$ ln -s "$PWD/release-audit/bin/release-audit" ~/bin/release-audit
```

The command on your path is then the checkout itself: a pull updates it, and there
is no second copy to drift from the repository.

### 1c. From a checkout, copied

```shell
$ git clone https://github.com/thoran/release-audit
$ cp ./release-audit/bin/release-audit /path/to/your/executables
$ chmod +x /path/to/your/executables/release-audit
```

The command and the repository are then two things, to be kept the same by hand.
Worth it where the command should outlive the checkout, which a symlink does not.

## Usage

```shell
$ release-audit --root ~/code
```

With no root named, the current directory is used. A root may also be configured,
so that the common case takes no arguments at all:

```shell
$ cat ~/.config/release-audit/config.json
{"root": "~/code/scm/github.com/thoran", "owner": "thoran"}
```

`owner` is who "ours" means: a repository counts as yours where its root commit's
author matches, which is how a fork sitting beneath the root is left out. It
defaults to `git config user.name`, so it usually needs no saying at all.

A directory naming a repository audits that one instead of those beneath the root.
Relative and absolute both, `~` expanded, `.` being the one you are in, and more
than one may be named:

```shell
$ cd ~/code/scm/github.com/thoran/coinmarketcap && release-audit .
$ release-audit ~/code/scm/github.com/thoran/imap
$ cd ~/code/scm/github.com/thoran && release-audit coinmarketcap imap
```

`--root` names the directory the repositories are *in*, which is why `--root .`
from inside a repository finds none.

Only repositories with something outstanding are listed. `--all` lists every one,
and naming one implies it, since that is already saying which is wanted.

Rows are ordered by what can be acted upon: a release which stopped between being
made and being delivered first, then a tree with work in it, then what is only
historical, and within each by name.

### Options

| | |
| --- | --- |
| `-c, --config FILE` | Where the configuration is (default: `~/.config/release-audit/config.json`) |
| `-r, --root DIR` | Where the repositories are (default: the configured one, else `.`) |
| `-B, --branch NAME` | The branch each repository releases from (default: `master`) |
| `-a, --all` | List every repository, not only those with something outstanding |
| `-n, --name` | Head the repository column, which is otherwise left unheaded |
| `-o, --origin` | Ask origin whether its tags name what ours do (one call each) |
| `-O, --owner NAME` | Whose repositories are ours (default: `git config user.name`) |
| `-h, --help` | Show this help |

## What it reports

| | |
| --- | --- |
| `uncommitted` | the working tree has changes which no commit holds |
| `untagged` | a version the repository claims, with no tag naming it |
| `tags unsigned` | version tags carrying no signature, counted against the whole |
| `lightweight` | a version tag which is a bare ref, so it carries no message either |
| `unpushed` | commits on the release branch which the remote does not have |
| `no upstream` | the branch tracks nothing, so pushed cannot be answered |

A version the repository claims is one named in a commit subject, in the form
`1.2.3: summary`.

With `--origin`, two more, which are the only checks here touching the network:

| | |
| --- | --- |
| `has not got N of our tags` | a tag cut here which origin does not hold |
| `names X and this clone's names Y` | a tag both hold, naming different commits |

A push corrects neither on its own: it moves commits and leaves tags where they
are, and `--follow-tags` carries only what is reachable from what is pushed.

## What it does not report yet

Whether a version is published. That is the one remaining stage, and `ROADMAP.md`
asks whether it belongs here.

## Exit status

1 where anything was found, 0 where nothing was, so it can gate a release, and 2
where the fault is in how it was called: a path which names no repository, or a
switch which does not exist. A gate reading 1 has found work; it has not been
misused, which is why the two are not the same number.

## Contributing

1. Fork it: `https://github.com/thoran/release-audit/fork`
2. Create your feature branch: `git checkout -b my-new-feature`
3. Commit your changes: `git commit -am 'Add some feature'`
4. Push to the branch: `git push origin my-new-feature`
5. Create a new pull request

## License

MIT
