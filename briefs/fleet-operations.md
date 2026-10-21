# One-Operator Fleet Operations

Running 40+ production sites and services — e-commerce taking real payments, voice agents on public
phone numbers, AI platforms with per-token costs — with one operator and AI agents as the ops team.
These are the standing disciplines that make that tractable.

## Reuse before rebuild

The constitutional rule of the fleet: **operate first, build last**. Before anything new is created,
the existing implementation is located, read, and extended. A parallel implementation of something
that already exists is treated as a defect, not a shortcut — it destroys institutional memory and
doubles the maintenance surface. In a one-operator fleet, institutional memory *is* the capacity.

## Deploy discipline

Every production change follows the same path: backup (outside the webroot) → stage → lint with the
exact runtime PHP binary → swap → live verify → append to the operations ledger. JavaScript gets a
syntax check before it ships. Content fixes are surgical deltas to the changed resource — never
"redeploy the site" to fix a page.

## Deny-by-default webroots

Fleet-wide server rules, maintained as global includes so control-panel regenerations can't silently
drop them: XML-RPC denied everywhere, dotfiles denied (`.git`, `.env`), backup extensions
(`.bak`, `.sql`, `.zip`) denied in docroots, PHP execution denied in upload directories. The
corollary discipline: backups never live in the webroot in the first place.

## Verify the money path

A change near checkout is not verified by "the page loads." It's verified by exercising the path:
add to cart, confirm the cart cookie, render the checkout with payment fields present — server-side
with a cookie jar, where the cache layers can't fool you. (Cache layers *will* fool you: a CDN plus
a reverse-proxy cache can make a broken cart look healthy and a healthy cart look broken.)

## Patch velocity as a practice

When a critical PHP CVE dropped with vendor patches pending, the fleet had the advisory's interim
mitigations applied, verified live, and logged across both hosting providers within hours — with
the one judgment call documented: a mitigation that trades RCE-path reduction for realistic
whole-box OOM risk on a shared host is declined, and compensating request-size caps applied instead.
Security response is a muscle, and it's exercised on a schedule: standing audits per site, tracked
findings, and a written close-out checklist per incident.

## Agents as the ops team — with a ledger

Multiple AI agents (from different vendors) work this fleet. Coordination runs through an
append-only operations ledger: every production write is announced with actor, scope, and reason;
every audit hands off findings to the component's owner; peers' in-flight work is checked before
touching shared files. The ledger is what makes multi-agent operations auditable instead of
archaeological.
