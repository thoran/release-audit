# release-audit Roadmap

Date: 20260915

Jobs are in TODO. What is here is undecided: questions to be answered rather
than work to be done, which is why they are prose and the jobs are a list.

## Open questions

### Whether there is a machine-readable output, and what consumes it

Nothing asks for one yet. `mercurial` has the precedent at `--format json` since
its 0.10.0, so the shape is settled if the need appears.

Markdown is the wrong candidate. It was tried by hand on 20260915: `dsv`'s
`outstanding` cell runs to 140 characters, and a table with cells that long reads
worse than the aligned columns it replaced. A renderer wanting markdown should
consume the data, not be a mode of the tool.

The parser written for that experiment read the column widths off the header and
guessed the kind of each finding by matching English prose. `findings()` holds
the same data as `[text, standing]` pairs. That a post-processor had to infer
what the tool already knows is the argument for the question, whatever the answer.

### Whether the checked-out branch is the right scope

Every check reads the branch `HEAD` is on, `--branch` naming it. A repository can
carry any number of other branches and report nothing.

Surveyed on 20260913 this was true of nine branches across five repositories, and
one of them inverts the tool's answer: `poloniex.v2.rb` reports clean while `HEAD`
sits on `0.1.0` and `master` — the branch a clone gets — is a year stale at 0.0.0.
`viddy2` is on a local-only `t2` which was never pushed.

Widening the scope is not obviously right. Most of those branches are rewrite
checkpoints and parked work, which are not releases and would be noise. What is
unsettled is whether the default branch being stale is this tool's business, given
that it is the branch anyone cloning receives.

### Whether publication belongs here

The fifth stage is not asked. The argument against was that a tool which never
speaks to a remote stays fast and reliable, and is wanted most when it is.

`--origin` has since made that line less clean: this tool does speak to a remote,
behind a switch, and the same reasoning would put a rubygems lookup behind the
same switch. Either the argument covers both or it covers neither.

### Where the list of repositories comes from

A root is walked for `.git` directories, `ours?` filtering by the root commit's
author. That is a proxy twice over: a grafted history reads as ours, and a
repository begun before the habit of a first commit of one's own reads as someone
else's.

Whether the list should instead be stated — a configuration naming the
repositories, or a file each one carries — is open. The walk suits a directory of
one's own work and suits nothing else.
