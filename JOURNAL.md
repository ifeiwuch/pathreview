## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/154

**Issue title:** Health check DB probe passes a raw SQL string, which fails under SQLAlchemy 2.x

**Tier:** [x] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
This issue is about formatting in the `health.py` file. The database probe in this file (checking if the databse is accepting connections) is using a string, instead of wrapping the request in sqlalchemy.text(). This causes the probe to report that the database is disconnected when it isn't. A successful fix would accomplish a correct probe, where the request is wrapped in sqlalchamey.text() and the database isn't marked as disconnected every time.

**Branch name:** fix/154-health-check-sql-string

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger

**Is this right for me?:** This issue is right for me because I understand the underlying problem, and how it could be fixed. I also feel that I'll be able to solve it in a reasonable amount of time and it's something that is not completely new to me.

## Week 8 — Reproduction & solution planning

**Reproduction commit link:** https://github.com/ifeiwuch/pathreview/commit/f81f0c2ffc4956960318cc96be910bf9379a30f0

**Reproduction summary:**
I started the app locally with Postgres running (`make run`) and hit `curl -i http://localhost:8000/health`. Even with the database fully up, the endpoint returned `503` with `postgres: "unhealthy"`, and the server logs showed `postgres_health_check_failed` with `Not an executable object: 'SELECT 1'` — confirming that `db.execute("SELECT 1")` in `health.py` raises under SQLAlchemy 2.x because the query isn't wrapped in `sqlalchemy.text()`, not because Postgres is actually down.

**PLAN.md link:** https://github.com/ifeiwuch/pathreview/blob/fix/154-health-check-sql-string/PLAN.md

**Walkthrough video (recommended):** [N/A]

**Blockers or open questions:**
Need to confirm whether CI has a Postgres service available so the regression test can hit a real DB, or whether it needs to mock `db.execute` instead — haven't checked `.github/workflows/ci.yml` for this yet. Also want to double check there are no other raw-string `db.execute(...)` calls elsewhere in the codebase before closing this out.


## Week 9 — Solution building & PR submission

### Check-in 1 (mid-week)

**Current progress:**
No code changes yet — this check-in covers closing out the two open blockers from Week 8 before starting implementation. Confirmed `.github/workflows/ci.yml`'s `test-integration` job spins up a real `postgres:16-alpine` service, so the regression test in PLAN.md step 4 can hit a real DB instead of mocking `db.execute`. Also grepped the codebase for other raw-string `.execute("...")` call sites and confirmed `api/routes/health.py` is the only one — no hidden repeats of this bug elsewhere.

**Next steps:**
Work through PLAN.md steps 1, 3, 4, and 5: apply the one-line fix (`text("SELECT 1")`) in `api/routes/health.py`, re-run `make run` + `curl /health` to confirm a `200`/`"healthy"` response, add a regression test under `tests/integration/` against the real Postgres service, and confirm the endpoint still correctly returns `503`/`"unhealthy"` when pointed at a bad `DATABASE_URL`.

**Blockers:**
None currently — both open questions from Week 8 are resolved.

---

### Check-in 2 (end of week)

**PR link:** [link to your submitted pull request]

**Branch:** [the branch name you worked on, e.g. `fix/123-short-description`]

**What you built:**
[1–3 sentences summarizing what your fix does and how it works]

**Tests added or updated:**
[Which test files did you touch? What do they cover?]

**Self-review confirmation:** [ ] make check passes  [ ] make test-unit passes

**Draft PR feedback received from:** [name or Slack handle, or "none"]