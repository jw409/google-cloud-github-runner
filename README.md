# google-cloud-github-runner — meshly.ai fork

[![Badge: License](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](LICENSE)
[![Badge: Python](https://img.shields.io/badge/Python-3670A0?logo=python&logoColor=ffdd54)](#readme)
[![Badge: Google Cloud](https://img.shields.io/badge/Google%20Cloud-%234285F4.svg?logo=google-cloud&logoColor=white)](#readme)

A **hard fork** of **[Cyclenerd/google-cloud-github-runner](https://github.com/Cyclenerd/google-cloud-github-runner)** —
ephemeral, just-in-time self-hosted GitHub Actions runners on Google Cloud.

Hard fork means we maintain this line of the code. Upstream is where it came
from and we send work back where it fits, but nothing here is waiting on
upstream's permission and a change to `app/` is allowed.

In practice we still **add** paths rather than edit upstream's, because it
keeps pulling upstream changes cheap. That is a preference we measure
([how far it diverges](#how-far-this-diverges)), not a promise about what the
running artefact is — see
[what byte-identity does NOT mean](#what-byte-identity-does-not-mean).

**Read upstream's documentation, not this file.** Everything about what the
tool is, how to deploy it, how to configure it, its architecture, its
environment variables and its API lives in
**[upstream's README](https://github.com/Cyclenerd/google-cloud-github-runner#readme)**
and is not duplicated here. Duplicated docs rot, and a fork's copy rots
fastest, so this README covers one thing only: **how this fork differs from
upstream.** If you are evaluating the tool, you are in the wrong repository —
go upstream.

## ⚠️ AI-generated code disclosure

**Everything this fork adds was written by an AI coding agent** (Claude), under
human direction and review. That covers every path this fork
adds, and the fork-facing documentation including this README. Treat it accordingly:

* **Upstream's code is not affected.** The fork modifies no path upstream owns,
  so nothing generated here has touched the application. That is verified
  mechanically, not asserted — see
  [the invariant](#the-additions-never-edits-invariant).
* **Agents: read [`AGENTS-LOCAL.md`](AGENTS-LOCAL.md) before editing anything
  here.** It is repo-wide and states which paths are ours, how to route a
  change, and what must never be included in an upstream pull request.
* **Review it before you trust it.** Some of it is access-control code, which
  is exactly the category where plausible-looking wrong code is most expensive:
  a bug does not misbehave, it denies every caller, and that looks
  indistinguishable from a quiet day. Read the tests — they are the claim — and
  do not deploy any of it on our say-so.
* **What was actually verified**, so you can judge the rest: the test suite
  passes, it is `flake8`-clean under upstream's own rules, and twelve
  deliberate mutations of the implementation each turn the suite red (wrong
  address family, ignored chain index, unevaluable treated as allow, uncovered
  route allowed, and so on). That establishes the tests detect those specific
  failures. It does not establish the design is right for your deployment.
* **If we offer any of this upstream**, it will say the same thing in the pull
  request. A maintainer deciding whether to review a patch is entitled to know
  how it was produced, and finding out afterwards is worse for everyone.

No claim is made here that a human hand-wrote it. We would rather say so plainly
than have you infer it from the commit style.

## What's different from upstream

No upstream **code** is changed. Five directories are added, six root files are
added, and four documentation files are diverged — see
[how far it diverges](#how-far-this-diverges) for the honest
accounting.

| | Change | Path |
|---|---|---|
| ➕ | **IP allowlist** — a policy core plus WSGI and ASGI adapters, stdlib-only, framework-free, no address ranges baked in. Not wired into the application. | [`ipfilter/`](ipfilter/) |
| ➕ | **Self-hosting notes** — Artifact Registry layout and Cloud Build caching for running your own build of the manager image. | [`docs/`](docs/) |
| ➕ | **Agent instructions** — an entry point that says to *read and evaluate* upstream's `AGENTS.md` rather than obey it, repo-wide rules, and a machine-readable manifest. | [`CLAUDE.md`](CLAUDE.md), [`AGENTS-LOCAL.md`](AGENTS-LOCAL.md), [`RUNNER.xml`](RUNNER.xml) |
| ✏️ | This file, [`CONTRIBUTING.md`](CONTRIBUTING.md), [`SECURITY.md`](SECURITY.md) and [`AGENTS.md`](AGENTS.md), to say what the fork is and where to send patches. | — |
| ✏️ | Default branch is `main`. Upstream's is `master`. | — |

That is the complete list, and it is mechanically enforced rather than
promised — see [below](#the-additions-never-edits-invariant).

**Not changed as FILES:** `app/`, `gcp/`, `tools/`, `tests/`, `Dockerfile`,
`requirements.txt`, `.github/workflows/`, and every other path upstream owns.
Byte for byte. No patch series, no cherry-picks, no new runtime dependencies.
`ipfilter/` is **not imported by** upstream's tree, which is why `app/` does
not need to move.

### What byte-identity does NOT mean

It would be easy to read the above as "this behaves like upstream". **It does
not, and the distinction is worth being exact about**, because the honest
version is less reassuring than the convenient one.

Byte-identity is a claim about **source files**. It is not a claim about the
**artefact**, and we deploy a materially different artefact:

| | upstream | what we run |
|---|---|---|
| Entrypoint | `gunicorn run:app` | `gunicorn meshly.wsgi:app` — a module of ours |
| Request path | straight into Flask routing | our WSGI wrapper runs **first**, before the app's own in-handler signature check |
| Docs a reader sees | upstream's | ours, for four files |
| Community health files | upstream's | ours, minus `FUNDING.yml` |

So the source layer tracks upstream and the artefact layer does not. We are
fundamentally augmenting this package. Pretending otherwise because no vendor
byte moved would be a true statement arranged to create a false impression.

What the invariant actually buys is therefore narrower than it sounds, and it
is a **maintainability** property rather than a fidelity one:

* an upstream bump is a **fast-forward of a pointer**, not a merge of code we
  did not write;
* `git diff` on that pointer shows exactly what we are accepting, before we
  accept it;
* there is no patch that can apply **with fuzz** — succeed, mean something
  different, and fail nothing;
* our side can be rebuilt on a new upstream without reconciling edits.

What it does **not** buy, stated so nobody relies on it:

* that the running service behaves as upstream's does — see the table;
* that upstream's documentation predicts our behaviour;
* that upstream's tests cover what we deploy. They test `app/`. **Nothing
  tests the composed artefact**, and that gap is ours, not theirs.

## Why we run this at all

Mild version, since it is the honest one: **GitHub's hosted runners are
expensive at our volume.** [Blacksmith](https://blacksmith.sh) was a genuinely
usable drop-in replacement — a one-line `runs-on` change and it worked — but
also too expensive to be the answer. Ephemeral Spot VMs in our own project are
the next step down in cost.

So **much of what this fork adds is for our own CI/CD**, not a product. That is
worth stating plainly for two reasons. It tells you what the additions are
optimised for: cost and predictability, with queueing and delay treated as
acceptable. And it tells you what they are *not* — none of this is a hosted
service, a support commitment, or a claim that it will suit your cost profile.

If GitHub-hosted runners are affordable for you, use them. They are less work
than any of this.

## Public repositories

GitHub's advice is to use self-hosted runners only with private repositories.
Anyone can open a pull request against a public repo, and a workflow triggered
by that pull request runs the contributor's code on your runner.

Ephemeral just-in-time runners are the mitigation GitHub names for this, and
they are what upstream builds. They solve persistence: a fresh VM per job means
nothing survives into the next job. They do not help with the job itself.
During that one job the submitted code has shell access on a VM in your GCP
project, with whatever its service account and network reach allow.

If you want self-hosted runners on a public repository anyway:

- Trigger them only on events a fork cannot cause — `push` to a branch in your
  own repository, tags, `workflow_dispatch`, `schedule`. Not `pull_request`
  from forks.
- Never `pull_request_target` where secrets are in scope. That combination runs
  your workflow definition against their code with your token.
- Under Settings → Actions → General, set the fork pull request policy to
  *Require approval for all outside collaborators*. A workflow from an
  untrusted pull request then waits for a maintainer instead of running. Both
  public repositories in this organisation are set this way.
- Give the runner's service account only what the build needs. No
  project-wide roles, no ambient credentials on the image.
- Keep organisation and repository secrets out of reach of the job.

This repository is public, so its own CI runs on GitHub-hosted runners.
`.github/workflows/ci.yml` is `ubuntu-latest` in all four jobs. The one
workflow that targets a self-hosted label, upstream's `example.yml`, is
`workflow_dispatch` only, which a fork pull request cannot trigger. The
self-hosted path is for private repositories.

## How far this diverges

"Hard fork" says who maintains it, not how much changed. The amount:

| | Count | What |
|---|---|---|
| Upstream **code** paths modified | **0** | `app/` `gcp/` `tools/` `tests/` `Dockerfile` `requirements*.txt` `.github/` — byte-identical, mechanically verified |
| Upstream **doc** paths diverged | **4** | `README.md` `CONTRIBUTING.md` `SECURITY.md` `AGENTS.md` — each pinned by blob hash |
| Directories added | **5** | `ipfilter/` `docs/` `scripts/` `.claude/` `.meshly/` |
| Root files added | **6** | `AGENTS-LOCAL.md` `CHANGELOG.md` `CLAUDE.md` `NOTICE` `RUNNER.xml` `justfile` |

So the code side is currently untouched, and the check that reports this is
mechanical: git object ids compared against a recorded baseline of upstream's
tree, with the four documentation exceptions pinned by blob hash.

Read that as a **measurement, not a guarantee**. A change that needs to edit
`app/` can; the check will say so, which is the point of having it. What it
buys is that the divergence is always a number somebody chose rather than one
that accumulated.

## Why the fork exists

We needed somewhere to put code upstream does not have yet, and we needed to
own the release cadence of the thing our CI depends on.

The reason we still avoid editing upstream's paths is selfish: it keeps a
version bump a fast-forward instead of a merge of code we did not write. What
it specifically buys is protection from a patch that applies **with fuzz** —
which succeeds, and now means something different, and nothing fails and nobody
looks.

It also means you can audit this fork's **source** without reading a diff:
upstream's files are byte-identical, and everything of ours is in a new path.
Auditing its **behaviour** is a different exercise and needs the table
[above](#what-byte-identity-does-not-mean) — the artefact is not upstream's.

### The additions-never-edits preference

> Every path upstream owns is byte-identical here, **except** an explicitly
> baselined set of top-level `.md` files, each pinned by its exact blob hash.
> Everything this fork adds is a new top-level path.

That is true today and the check reports it. It is a preference we hold because
it keeps bumps cheap, not a constraint the fork is required to satisfy — this
is a hard fork, and editing an upstream path is a decision we are allowed to
make. Making it means re-baselining, deliberately, in the same change.

Checked by comparing git object ids against a recorded baseline of upstream's
tree, which is a Merkle comparison — `app` matching means every file beneath
it matches, recursively. The four documentation exceptions are declared
individually and pinned, so editing one *again* fails the check until it is
re-baselined, and a non-`.md` or nested path cannot be declared at all. That
last restriction is the one that actually protects `app/`.

## The added packages

Each addition is self-contained, vendor-neutral, and built to be given away:
named for what it does rather than for us, no dependency on our deployment, and
no import of this application — so the offer upstream is "add exactly this
directory". None of them is wired into the runner manager; enabling one is a
composition step a deployment performs for itself, which is why upstream's
`app/` never has to move.

Each carries its own design notes and its own proof command in its directory.
For the IP allowlist that is [`ipfilter/__init__.py`](ipfilter/__init__.py) and:

```bash
python -m pytest -c ipfilter/pytest.ini ipfilter/tests
```

Pass the directory explicitly — `testpaths` resolves against pytest's inferred
rootdir, which differs between a bare run and one with arguments, and a bare
run picks up the project's own suite instead.

## Contributing

**Upstream first** — <https://github.com/Cyclenerd/google-cloud-github-runner/issues>.
For this fork's own paths, its tracker is open:
<https://github.com/Meshly-Open-Source/google-cloud-github-runner/issues>.
A fix landed upstream reaches everyone running this tool, including us; a fix
landed here strands you on a fork. We will not merge a change that should have
gone upstream.

Changes to the paths this fork adds are welcome here, since they have nowhere
upstream to go yet. See [CONTRIBUTING.md](CONTRIBUTING.md) for how we evaluate
them, including the priority order, stated plainly.

## Licence and credit

Upstream is the work of **[Cyclenerd](https://github.com/Cyclenerd)** and its
contributors, used here under the [Apache-2.0 licence](LICENSE). Additions in
this fork are under the same licence.

If this project is useful to you, support **upstream**, not this fork.

## Sponsor

Maintained and sponsored by **[meshly.ai](https://meshly.ai)**, who run their
CI fleet on it.
