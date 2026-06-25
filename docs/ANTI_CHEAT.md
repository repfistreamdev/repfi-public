# Anti-cheat: pose verification

How RepFi counts push-ups and why simple hacks fail.

---

## Design principle

**Client detects form. Server credits balance.**

The browser runs MediaPipe Pose and applies strict rules. Only when a rep passes all checks does the client call `/api/reward`. The server then credits $0.01 — but only if a valid signed session exists and rate limits are not exceeded.

Spoofing the API alone is insufficient: you need a valid account session **and** the client only fires after local verification.

---

## MediaPipe Pose

- **33 body landmarks** tracked per frame
- Runs **on-device** in the browser (no video uploaded for counting)
- Skeleton overlay visible to viewers — transparent verification

---

## Push-up rules (strict)

A rep counts only when **all** conditions pass:

### 1. Camera angle
| Angle | Requirement |
|-------|-------------|
| Front | Both shoulders visible, span ≥ 11% frame width |
| Side | Shoulder span ≤ 7.5% — one side dominant |
| Unknown | **No reps counted** |

### 2. Body position
- Not standing (hip-to-shoulder ratio check)
- **Plank position**: shoulders and hips aligned (body line angle ≥ 155°)
- Hands on floor: wrists below shoulder line

### 3. Elbow depth (state machine)
```
UP (elbows extended ≥ 155°)
  → DOWN (elbows bent ≤ 95°, held ≥ 350ms, shoulders drop ≥ 2.8%)
    → UP (full extension)
      → REP +1 (if inForm throughout)
```

### 4. Cooldown
- **900ms** minimum between counted reps — blocks rapid arm flapping

### 5. Form gate
`rep && inForm` — both must be true. Partial reps, hip sag, or wrong angle = zero credit.

---

## What this blocks

| Cheat attempt | Result |
|---------------|--------|
| Waving arms while standing | `isStanding()` → blocked |
| Half push-ups | Elbow angle thresholds not met |
| Fast arm pumps | Cooldown + min down duration |
| Calling `/api/reward` from console | Needs httpOnly session + rate limit |
| Sending another user's email | **Removed** — server uses session only |
| Fake landmark JSON | Must pass sequential state machine over time |

---

## Known limitations (honest)

| Vector | Mitigation status |
|--------|-------------------|
| Pre-recorded video fed to camera | Planned: liveness checks |
| Multiple people in frame | Single-pose mode (`numPoses: 1`) |
| Server trusts client rep signal | Rate limits + session; future server-side sampling |

We continue hardening. See [ROADMAP.md](ROADMAP.md).

---

## Code reference (public summary)

The tracker enforces these thresholds (from production client):

```
DOWN_ELBOW = 95°      // must bend this far
UP_ELBOW   = 155°     // must extend this far
MIN_DOWN_MS = 350     // hold at bottom
COOLDOWN_MS = 900     // between reps
MIN_SHOULDER_DROP = 2.8%  // actual body movement required
```

Form hints shown live: `plank`, `hands`, `depth`, `extend`, `angle`, `visible`.

---

## Why source stays private

Publishing the exact server validation logic and payout hooks would help attackers map bypass targets. Public documentation explains **what** we check; private code implements **how** we enforce it.
