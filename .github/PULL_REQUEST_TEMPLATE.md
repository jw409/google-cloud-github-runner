<!-- Adapted from Josee9988/project-template (MIT) — see .github/ATTRIBUTION.md -->

## Would this be better upstream?

This is a hard fork, so a change to any part of it can land here, including
`app/`, `gcp/` and `tools/`. Nothing gets closed for being in the wrong place.

Worth a thought first: if the change is a fix to the runner manager that would
apply to
[upstream](https://github.com/Cyclenerd/google-cloud-github-runner) unchanged,
sending it there reaches everyone running the tool. You are welcome to do both.

[How far this fork diverges](../README.md#how-far-this-diverges) if you want
the current measurement.

---

## What does this change, and why?

<!-- The outcome first: what is different for a user or an operator afterwards,
     and what problem that solves. Not a list of files. -->

*

## How did you verify it?

<!-- Commands you actually ran and what they printed. A claim that something
     passes is not the same as having watched it pass.

     If you touched a package here, run ITS proof command — each one is named in
     its directory. For the IP allowlist:
         python -m pytest -c ipfilter/pytest.ini ipfilter/tests

     Style is upstream's: flake8 --max-line-length=127 --extend-ignore=W292,W503 -->

*

## Checklist

<!-- Tick only what you actually did. An unticked box is fine and more useful
     than a ticked one that is aspirational. -->

- [ ] This change is confined to the paths this fork owns
- [ ] I read [CONTRIBUTING.md](../CONTRIBUTING.md)
- [ ] Tests cover the change, and I saw them fail before they passed
- [ ] No new runtime dependency (or the description argues for the one it adds)
- [ ] No new linter, formatter, scanner or CI job
- [ ] If any of this was AI-generated, the description says so

## Anything else

<!-- Risk, rollback, follow-ups, things you could not verify. "I could not test
     X" is a useful sentence, not an admission. -->

*
