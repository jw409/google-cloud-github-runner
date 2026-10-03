# Contributing

Thanks for looking. Two things to know before you spend any effort.

## 1. Consider upstream first 🍴

This repository is a hard fork. We maintain this line of the code, and a change
to any part of it can land here, including `app/`, `gcp/` and `tools/`.

That said, upstream is
**[Cyclenerd/google-cloud-github-runner](https://github.com/Cyclenerd/google-cloud-github-runner)**
and it is active. If your change is a fix or an improvement to the runner
manager that would apply there unchanged, sending it upstream reaches everyone
running the tool rather than only the people on this fork. That is worth more
than getting it merged here faster.

Upstream, if you want it:

* **Issues** → <https://github.com/Cyclenerd/google-cloud-github-runner/issues>
* **Pull requests** → <https://github.com/Cyclenerd/google-cloud-github-runner/pulls>
* **Coding style** → upstream's
  [CONTRIBUTING.md](https://github.com/Cyclenerd/google-cloud-github-runner/blob/master/CONTRIBUTING.md)
  is the authority for code style under `app/`, `gcp/` and `tools/`. We have
  not rewritten those conventions and do not intend to.

An issue opened upstream also has a decent chance of already being someone's
problem there — including things we reported ourselves.

**We will not refuse a change for being upstream's.** We may say that upstream
is the better home for it and ask whether you want to send it there too. If
you would rather it just landed here, that is a fine answer.

## 2. What lands here 📥

Anything in this repository. We do keep a working preference for **adding** a
path over editing one upstream owns, because it keeps pulling upstream changes
cheap, but it is a preference rather than a rule, and a change that needs to
edit `app/` can.

`README.md` lists what this fork adds and measures how far it has diverged.
That count is kept in one place rather than restated here.

**Issues are enabled on this fork**, for the fork's own paths:
<https://github.com/Meshly-Open-Source/google-cloud-github-runner/issues>. Prefix the title
with the path it concerns (`docs:`, and so on) so it is obvious at a glance
that it is not an upstream bug filed in the wrong place.

An issue about the runner manager itself is welcome here, and we will usually
point at upstream as well, because upstream is where a fix reaches everyone.
What we will not do is leave it open here looking tracked while nobody who can
fix it is reading it — so expect either work or a straight answer.

### How we evaluate a contribution

Stated plainly, because an unstated priority order is just a slower rejection.
In this order:

1. **Does it benefit [meshly.ai](https://meshly.ai)?** We maintain this fork to
   run our own CI, on our own time, and that is the budget it comes out of.
2. **Does it benefit the wider community?** A change that is good for everyone
   is better than one that is only good for us, and we would rather find the
   general version of your idea than the narrow one.

Being honest about the order does not mean the second criterion is decoration.
What this fork adds is built to be given away — vendor-neutral, named for what
it does rather than for us, no dependency on our deployment — precisely so that
each piece can be offered upstream as "add this directory". If your change makes
something here *more* generally useful, that helps on both counts.

If the answer to (1) is no but (2) is a clear yes, the right home is upstream
or your own fork, and we will tell you that rather than leave a PR open.

### What we need from a change

* **A test that fails without it.** Anything security-relevant gets extra
  scrutiny here, because the failure mode of a fail-closed control is not
  misbehaviour — it denies everyone, which looks exactly like a quiet day and
  is therefore invisible. Each added package carries its own proof command;
  see its directory.
* **No new runtime dependencies**, unless the PR argues for the one it adds.
  Upstream's `requirements.txt` is unmodified, and additions here are
  stdlib-only so that one of ours can never silently widen an upstream pin.
* **Match the surrounding style.** `flake8 --max-line-length=127`, spaces,
  no trailing whitespace — upstream's rules, which this fork follows.
* **Please do not add tooling.** No new linters, formatters, type checkers,
  taint analysers or language-specific scanners, and no CI jobs to run them.
  We run a fair amount of that machinery on our own code and deliberately keep
  it out of this repository: it is upstream's project, it has its own
  conventions, and a fork that imposes its employer's toolchain on a volunteer
  codebase is a nuisance to everyone downstream of it. A PR whose diff is
  mostly configuration for a tool nobody asked for will be declined.

### Security

Do not open a public issue for a vulnerability. See [SECURITY.md](SECURITY.md).
If it affects upstream's code rather than the directories listed above, report
it upstream.

## Licence

Upstream is [Apache-2.0](LICENSE) and so is everything added here.
Contributions are accepted under the same terms.

Thanks again. ❤️
