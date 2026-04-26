# RiskGuard — Client Deployment Runbook

**Purpose**: Standardized procedure for deploying RiskGuard to a new client's Hyperliquid account during week 1–2 of Systematic Trader 90. This runbook is for **Eddy's use** when onboarding a client, but client-facing screenshots can be lifted from it.

**Reference landing page** (what the client has already seen): <https://edgardextra-lang.github.io/riskguard-landing/>

---

## What RiskGuard does

A Telegram bot connected to the client's Hyperliquid account. It watches the account in real time and:

- **Cancels orders** that exceed configured size or leverage limits before they fill
- **Force-closes positions** that breach the daily loss cap
- **Pings the client on Telegram** when limits are approached or breached
- **Pauses new entries** for a configurable cooldown after a loss event

It does NOT trade for the client. It only enforces guardrails the client has agreed to.

---

## Pre-flight (before the week 1 call)

Confirm before the call:

- [ ] Client has installed Telegram and messaged @cryptoeddy with their handle
- [ ] Client has shared their Hyperliquid wallet address (e.g. `0x...`)
- [ ] Client has 90+ days of trade history exported and sent to you
- [ ] You've reviewed the trade history and identified the top 3 blowup risks (oversized positions / over-leverage / no stops / correlated trades / weekend gambling — whatever the actual pattern is for THIS client)

---

## Step 1 — Risk audit (call 1, minutes 10–35)

Walk through the client's last 30 days of trades **with the client on screen-share**. For each trade flagged in your pre-call review, ask:

1. *"Why did you take this one?"* (listen for "felt like it" / "FOMO" / "revenge")
2. *"What was your stop?"* (listen for "I didn't set one" / "I moved it")
3. *"What size was this vs your account?"* (calculate live)

Goal: get the client to **say out loud** what their pattern is. RiskGuard is much easier to deploy when the client agrees with the rules instead of feeling controlled by them.

End the audit with three numbers, agreed by the client:

| Limit | Default starting value | Adjust based on |
|-------|------------------------|-----------------|
| **MAX_POSITION_USD** | 15% of account equity | Largest historical winner: cap at 1.5× avg win size |
| **MAX_LEVERAGE** | 5x for majors, 3x for alts | Whatever ≥80% of profitable trades used |
| **DAILY_LOSS_USD** | 3% of account equity | Whatever the client can sleep with after a bad day |
| **COOLDOWN_AFTER_LOSS** | 4 hours | Long enough that the client has eaten/walked/slept |

Write these on a shared doc the client signs off on. This is the contract.

---

## Step 2 — Provision RiskGuard for the client

> **NOTE — placeholders below.** RiskGuard's exact CLI/UI is product-specific and may evolve. Substitute the current commands or web flow at deploy time. The structure of the runbook (audit → provision → test → handoff) is the contract; the exact command names will move.

### 2a. Create the client's RiskGuard config

Create a new config file under the RiskGuard repo:

```
RiskGuard/clients/<client_slug>.yaml
```

Template:

```yaml
client:
  name: "<Client Name>"
  slug: "<client-slug>"
  telegram_handle: "@<handle>"
  telegram_chat_id: "<numeric ID — see step 2b>"
  hl_wallet: "0x<wallet>"
  enrolled_program: "ST90-FOUNDING-2026Q2"
  start_date: "2026-04-25"

limits:
  max_position_usd: 15000     # agreed in step 1
  max_leverage: 5
  daily_loss_usd: 800
  cooldown_after_loss_hours: 4

scope:
  # Enforce on these coins (empty = all coins)
  coins: []
  # Skip enforcement on these coins (e.g. coins managed by another bot)
  exempt_coins: []

alerts:
  on_order_blocked: true
  on_position_close: true
  on_daily_loss_warn: 0.5    # ping at 50% of daily loss limit
  on_daily_loss_breach: true

actions:
  block_oversized_orders: true
  force_close_on_daily_loss: true     # IMPORTANT: client signs off explicitly
  pause_entries_after_loss: true
```

### 2b. Get the client's Telegram chat_id

1. Have client message the RiskGuard bot once: `/start <client_slug>`
2. RiskGuard logs the chat_id; copy it into the YAML
3. Send a test message: `riskguard ping --client <client_slug>` → confirm client receives

### 2c. Connect to Hyperliquid

RiskGuard reads HL via the public account address — no key needed for monitoring/cancelling-via-public-API actions. For force-close to work, the client must either:

- **Option A**: grant RiskGuard a sub-account API key with `trade` scope only (no withdraw) — client generates this on Hyperliquid and pastes via PrivateBin link
- **Option B**: client uses RiskGuard in **alert-only mode** for the first 2 weeks to build trust, then upgrades to enforce-mode

**Recommend Option B for the founding cohort.** Two weeks of alerts builds trust; then the client opts into enforcement.

### 2d. Start the client's RiskGuard instance

```bash
# On the RiskGuard server
cd /path/to/RiskGuard
./start_client.sh <client_slug>

# Verify
./status.sh <client_slug>
# → expected: status=running, last_check=<ts within 60s>, alerts_sent_today=0
```

---

## Step 3 — Test in a dry run (before call 1 ends)

While still on the call:

1. Have client place a **deliberately oversized** test order (1 share over the limit, easy to cancel)
2. Confirm RiskGuard catches it within 5 seconds
3. Confirm Telegram alert lands on client's phone
4. Have client cancel the test order

If any step fails, debug live. Do not end the call until the client has seen RiskGuard fire at least once.

---

## Step 4 — Handoff doc to client

After the call, send the client the following message via Telegram:

```
Hey <name>,

RiskGuard is live on your account. Here's what you have:

✅ Max position: $<MAX_POSITION_USD>
✅ Max leverage: <MAX_LEVERAGE>x
✅ Daily loss cap: $<DAILY_LOSS_USD>  (4h cooldown if breached)
✅ Mode: ALERT-ONLY for the next 14 days, then we flip to ENFORCE

You'll get a Telegram ping when:
• An order is blocked (alert mode just notifies; doesn't cancel yet)
• You hit 50% of your daily loss
• You breach the daily loss cap
• A trade closes (so you have a real-time log)

For the next 2 weeks, just trade normally and read the alerts.
We review them on every call.

If anything feels off, message me. I check Telegram at least once a day.

— Eddy
```

---

## Step 5 — Week 2 review

On the week 2 call:

- Pull the RiskGuard alert log for the past 7 days
- Walk through every alert with the client — what triggered it, what the client did, was the limit right?
- Adjust limits if needed (almost always: tighten, not loosen)
- **Get explicit client sign-off to flip from ALERT-ONLY to ENFORCE mode**
- Make the change live during the call, test once more

After week 2, RiskGuard runs in the background. We only revisit it if (a) limits need adjusting, (b) the client's account size changes materially, or (c) a real loss event triggers a debrief.

---

## Off-boarding

When a client graduates (week 12) or churns:

- Switch RiskGuard to **alert-only** mode (don't strand them with enforcement they didn't renew)
- Rename their config: `RiskGuard/clients/_alumni/<client_slug>.yaml` (or `_churned/`)
- Send a final message:

```
You graduated. RiskGuard is now in alert-only mode on your account
(no enforcement). It will keep ping-ing you on breaches as a free perk
for ST90 alumni — keep it as long as you want.

If you want to flip enforce back on, message me. If you want me to
fully off-board, message me with "remove riskguard" and I'll shut
the instance down.

You earned it.
— Eddy
```

---

## Audit checklist (run quarterly)

For each active client:

- [ ] RiskGuard process running (`./status.sh <client>` returns OK)
- [ ] Last alert/heartbeat within 24h
- [ ] Limits still match the contract (no silent edits)
- [ ] Telegram chat_id still valid (send a `ping`)
- [ ] Client account still active on Hyperliquid (wallet still has positions or balance)

If any check fails, message the client before fixing. Don't silently restart — they should know.
