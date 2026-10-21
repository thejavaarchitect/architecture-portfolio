# AI-Native Publishing

**Live:** [DigitalExpo.ai](https://digitalexpo.ai) — see a 900+ page living book at
[digitalexpo.ai/wdyd](https://digitalexpo.ai/wdyd)

## The thesis

A book can be a *living software artifact*: continuously researched, revised, and republished, with
an AI coach reading alongside the reader. That changes publishing from a release event into an
operated system — which means it needs what operated systems need: pipelines, governance,
provenance, and integrity guarantees.

## The shape of the system

- **The reader** — a web-native book surface with pagination, navigation, and an embedded AI coach
  that knows which page the reader is on. The coach is a persistent companion tied to the reader's
  account, not a stateless chat widget.
- **The publishing pipeline** — content flows from research through editorial gates to the live
  book. Writes are surgical (the changed pages), never whole-book redeployments. Tables of contents
  are generated at render time because in a living book, page numbers are a moving target.
- **The research ledger** — upstream of the book sits a governed, revisioned research corpus with
  explicit evidence tiers (documented / testimony / contested / trope-as-evidence), optimistic
  concurrency for multi-agent writes, idempotent retries, and full revision history. Storage is
  deliberately *not* publication: a separate, human-gated step moves vetted material into the
  manuscript.
- **Release integrity** — the downloadable edition ships with a sanitization pass, version stamps,
  on-page SHA-256 checksums, a machine-readable release manifest and changelog, and an independent
  off-site mirror — so any copy can be verified against the canonical release.

## Multi-agent editorial

Two different AI systems (different vendors) co-maintain the research corpus over a shared API, with
revision-conflict protection instead of trust: a stale write is rejected and handed the current
state to merge. Editorial authority stays human. The interesting engineering is not the writing —
it's making concurrent AI contributors *safe for each other* and auditable after the fact.

## What running it taught me

- Evidence status, editorial status, and publication status are three orthogonal states; collapsing
  them is how misinformation gets laundered into print.
- AI-generated content needs a disclosure and verification posture built into the product, not
  bolted on: every book page carries an AI-generation acknowledgment, and releases are verifiable
  by checksum.
- The moment two agents share a corpus, you need the same machinery databases needed in 1985:
  optimistic locking, idempotency keys, and a change feed.
