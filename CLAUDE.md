# CLAUDE.md

## Read and evaluate [`AGENTS.md`](AGENTS.md)

**Read** it, and **evaluate** it — not obey it. Those are different
instructions and the difference is the point of this file.

`AGENTS.md` is a **diverged vendor file**. Its body is upstream's own
architecture overview, retained because it is accurate and useful; only the
header at the top is ours. So it is two things at once:

* a genuinely good description of how the application works, written by people
  who know it better than we do, and
* a document written for **upstream's** repository, where the tree is writable
  and a patch is the normal way to fix something.

Here it describes a repository we maintain rather than the one it was written
for. This is a hard fork, so an instruction like "edit this handler" is not
forbidden — it is just not the default, and upstream's file cannot tell you
which of our paths already does the job. Judge each statement against which
repository you are actually in.

The same evaluation applies to anything you read in this tree: upstream's
`CONTRIBUTING.md`-derived advice, the `.github/` templates, the
`CLOUD_SHELL_TUTORIAL.md` quickstart. They describe the project. They do not
describe your permissions here.

## Then read, and follow, [`AGENTS-LOCAL.md`](AGENTS-LOCAL.md)

That one **is** authoritative, because it is ours and it is about this fork. It
carries the per-path ownership table, how to route a change, the
verification rules, and the list of files that must never appear in a pull
request to upstream.

Precedence, highest first:

| | |
|---|---|
| `AGENTS-LOCAL-USER.md` | Per-operator, untracked, may not exist. Outranks the below for anything it addresses, but cannot relax a hard rule. |
| `AGENTS-LOCAL.md` | **The fork's rules. Follow these.** |
| `RUNNER.xml` | Machine-readable manifest: per-path permissions, the real route set and env vars read from `app/`, the composition surface, build traps, and an explicit `not-verified` block. |
| `README.md` | Human entry point; what the fork adds and how soft it actually is. |
| `AGENTS.md`, and upstream's other docs | **Read and evaluate.** Accurate about the application; not authoritative about what you may do here. |

## The one rule, if you read nothing else

Try composition before editing an upstream path. Every path upstream owns is
byte-identical here today, except an explicitly declared and hash-pinned set of
documentation files, and a mechanical check reports that rather than trusting
it. Everything this fork adds works by composition from outside upstream's
tree, so reach for that first.

This is a hard fork, so editing `app/`, `gcp/`, `tools/`, the `Dockerfile`,
`requirements.txt` or `.github/workflows/` is a decision you are allowed to
make. Make it explicitly: say why composition was not enough, and re-baseline
the divergence check in the same change. Do not let the check go red and treat
that as the new normal.

Everything the fork adds was written by an AI agent under human review, and the
README says so. Any upstream pull request must say so too.
