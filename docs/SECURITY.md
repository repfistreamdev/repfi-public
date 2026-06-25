# Security model

How RepFi protects user accounts, payouts, and platform integrity.

---

## What we never store or request

| Item | Status |
|------|--------|
| Seed phrases / recovery words | **Never requested** |
| Private keys | **Never requested** |
| Wallet extension signatures | **Not required** |
| Payment to register | **Not required** |

Users provide only a **public Solana address** for payouts.

---

## Password storage

- Passwords are hashed with **scrypt** + unique per-user salt
- Plaintext passwords are never written to disk or logs
- Legacy SHA-256 hashes are upgraded to scrypt on next login

---

## Session security

| Control | Implementation |
|---------|----------------|
| Session token | HMAC-signed, httpOnly cookie |
| Client storage | localStorage mirror for UI only (no password) |
| API auth | Reward & withdraw require valid session cookie |
| Cookie flags | `httpOnly`, `secure` in production, `sameSite=lax` |
| Secret | `AUTH_SECRET` env var (min 32 chars, server-only) |

Session survives page reload and new tabs via `/api/auth/session`.

---

## API hardening

| Endpoint | Protection |
|----------|------------|
| `/api/auth/register` | Rate limit (8/min per IP), field validation |
| `/api/auth/login` | Rate limit (12/min per IP), constant-time password check |
| `/api/reward` | Requires session; rate limit 45/min per user |
| `/api/withdraw` | Requires session; pool solvency check |

**Reward endpoint does not accept email in the request body.** The server reads identity from the signed session only — spoofing another user's email does not work.

---

## User database

- Stored server-side in `data/repfi.json` (path configurable via `REPFI_DATA_DIR`)
- Contains: email, password hash, nickname, public Solana address, balance
- **Not** exposed to the browser or public API
- Gitignored — never committed to any repository

---

## Source code protection

| Layer | Approach |
|-------|----------|
| Application repo | Private (`repfistreamdev/repfi`) |
| Public repo | Documentation only — no API keys, no route handlers |
| Client bundle | Pose tracking runs in browser; payout logic stays server-side |
| Env vars | `AUTH_SECRET`, pool wallets — server environment only |

Attackers cannot read server route code from the public repo or browser DevTools network tab alone.

---

## Solana payouts

- Withdrawals send to the **registered public address** on the user's profile
- Pool wallet private key lives **only** in server infrastructure (not in frontend, not in git)
- On-chain transfer confirmation required before crediting donations (production)

---

## Production checklist

```bash
# Required on Vercel / production host
AUTH_SECRET=<random 64-char hex>
REPFI_DATA_DIR=/var/data/repfi   # or mounted volume
```

Generate secret:
```bash
openssl rand -hex 32
```

---

## Reporting issues

Contact [@0pusdev](https://x.com/0pusdev) or open an issue in this public repo for documentation bugs. Do **not** post exploit details publicly before a fix.
