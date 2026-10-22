# Real-Time Duel & Ephemeral Credentials

**Live:** [F2 Combat](https://personaltraining.ai/f2combat/) · [client source](https://github.com/thejavaarchitect/f2-combat)

Two strangers, two phones, two cellular carriers — seeing and hearing each other in real time while
they play. That's a WebRTC problem sitting on a NAT problem sitting on a credential problem, and the
credential problem is the one most people get wrong. This is how I handled it without putting a
secret in a page anyone can read.

## The problem

Most consumer connections can't reach each other directly, so when two phones can't, something in
the middle has to relay the media — and a relay has to authenticate its callers. The naive build
hardcodes a relay username and password into the page. On a client I ship to the public, that
"secret" is one *View Source* away from the whole internet.

## The principles

- **The client is public, so the relay secret can't live in it.** Same rule I hold across the fleet:
  anything I ship to a browser, I assume a competitor is already reading. A credential printed into
  client code is a published credential.
- **Delegate the authentication; don't disclose the secret.** The client never needs the shared
  secret — only *a* credential that checks out. So the secret stays on my server and the client gets
  derivations of it.
- **A credential should die on its own.** The safest one expires by construction: no revocation list
  to keep, and a leaked value is worthless within the hour.
- **A relay is an abuse surface before it's a feature.** Bandwidth caps, per-session quotas, and a
  refusal to relay into private address space are part of the design — not a patch I added after the
  first bill.

## The shape of the system

- **Media is peer-to-peer; my server only brokers the handshake.** Phones negotiate directly over
  STUN and run a data channel for live round-and-rep state. A thin same-origin WebSocket carries the
  signaling and nothing else. The relay is the fallback, not the default.
- **The relay runs on minted, signed credentials (the TURN REST pattern).** When a duel starts, the
  client asks an endpoint for a credential. The server returns a username that *is* an expiry
  timestamp and a password that's an HMAC of it under a secret only the server holds. coturn
  re-derives the same HMAC and checks the clock — nothing stored, nothing to revoke. The client
  refreshes before expiry. The secret never ships.
- **The relay is hardened like the abuse surface it is.** It won't relay to loopback or private
  ranges, it enforces per-user and total bandwidth ceilings, and the minting endpoint is
  rate-limited per caller.

## What running it taught me

- **"Rotate the credential" is the wrong unit of work; "remove the credential" is the right one.** A
  static secret in a public client is a standing liability no matter how often you rotate it — every
  rotation just publishes a fresh one. Minted, expiring credentials retired the whole category.
- **Verify at the layer that actually fails.** I didn't trust the handshake — I watched a real
  browser gather a *relay* ICE candidate off a freshly minted credential, which only happens if the
  whole chain (endpoint, secret, signature, clock) is right, and confirmed that a tampered or expired
  credential gets refused.
- **The cheap security posture and the cheap cost posture are the same posture.** The caps that keep
  a relay from being abused are the caps that keep it from becoming a surprise bandwidth bill.

---
*Same "client is public, authenticate at the rail" stance as
[Governed agents in production](governed-agents-in-production.md) — applied to real-time media
instead of agent actions.*
