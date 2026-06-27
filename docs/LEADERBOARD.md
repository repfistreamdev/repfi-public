# Leaderboard & athlete profiles

Public rankings and athlete stats on [repfi.stream/leaderboard](https://repfi.stream/leaderboard).

---

## Leaderboard

All athletes with verified reps appear in a **live-updating table** (refreshed about every second).

| Column | Description |
|--------|-------------|
| Rank | Sorted by **lifetime verified push-ups** (most reps = #1) |
| Athlete | Public nickname |
| Reps | Total verified push-ups |
| Balance | Current USD balance ($0.01 per rep) |

Top three ranks show medal icons (🥇 🥈 🥉).

Athletes currently streaming show **● LIVE** with a link to their room.

---

## Athlete profiles

Click any nickname to open a profile page:

- **Avatar** — unique color/pattern per nickname
- **Stats** — reps, balance, total earned, withdrawn
- **Rank** — current leaderboard position
- **Solana wallet** — public payout address (copy + Solscan)
- **Activity** — member since, rep rate, live status

Example: `https://repfi.stream/profile?nick=opusdev`

Profiles update in the background while open — no full page reload needed.

---

## Privacy

Public profiles show:

- Nickname
- Rep counts and balances
- Solana **public** payout address

They do **not** show email, password, or private keys.

---

## API (public read)

| Endpoint | Returns |
|----------|---------|
| `GET /api/leaderboard` | Ranked rows + version stamp |
| `GET /api/athlete/:nickname` | Full public profile for one athlete |

No authentication required for read-only leaderboard data.

---

## Related

- [HOW_IT_WORKS.md](HOW_IT_WORKS.md) — earning flow
- [ARCHITECTURE.md](ARCHITECTURE.md) — system design
