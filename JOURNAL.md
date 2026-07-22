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

**Reproduction commit link:** [link to commit documenting the reproduced issue]

**Reproduction summary:**
I started the app locally with Postgres running (`make run`) and hit `curl -i http://localhost:8000/health`. Even with the database fully up, the endpoint returned `503` with `postgres: "unhealthy"`, and the server logs showed `postgres_health_check_failed` with `Not an executable object: 'SELECT 1'` — confirming that `db.execute("SELECT 1")` in `health.py` raises under SQLAlchemy 2.x because the query isn't wrapped in `sqlalchemy.text()`, not because Postgres is actually down.

**PLAN.md link:** [link to PLAN.md in your fork]

**Walkthrough video (recommended):** [link to your Loom video, ≤2 min — recommended, not graded]

**Blockers or open questions:**
[Anything you're still uncertain about going into Week 9, or leave blank]