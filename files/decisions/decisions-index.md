# Decisions Index

One line per **active** decision. Superseded / reversed / deprecated entries fall off this index but stay on disk in this folder for the audit trail.

Format per line: `- [NNN — title](file.md) — one-line rationale (≤120 chars)`

Keep this file under 100 lines. When it gets long, that usually means several decisions can be retired (mark them `deprecated` and let them drop off).

---

- [001 — Use Postgres over Mongo](001-example-postgres-over-mongo.md) — schema fit, transactions, ops simplicity (example file — replace with your own)
