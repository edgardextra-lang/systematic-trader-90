# Stripe Product — Founding Tier

Paste the fields below into Stripe Dashboard → **Products → + Add product**. After saving, generate a **Payment link** with the settings noted at the bottom.

---

## Product details

**Name**
```
Systematic Trader 90 — Founding Cohort (1:1)
```

**Description** (shown on Stripe-hosted checkout page)
```
A 90-day private 1:1 program for traders with $25k–$250k who want to stop guessing and ship a working bot.

In 90 days you walk away with:
• A documented trading system (entry, exit, sizing, what to skip)
• A risk framework that survives a bad week
• RiskGuard configured and live on your Hyperliquid account
• One HyperForge (https://edgardextra-lang.github.io/hyperforge-site/) bot trading your edge — paper-traded for 2 weeks, then live
• Lifetime alumni access (Discord) + 90 days free + 50% off 12 months of HyperForge SaaS at launch

Format: weekly 1h call with Eddy + weekly 30-min trade journal review. ~3–4 hours per week of your time.

Founding price: $4,500 paid in full, capped at 5 spots. The next cohort runs at $7,500 in a group of 6–8 — this is the only window for 1:1 access at half price.

Refund policy: pro-rated within 14 days, no questions. After day 14, no refunds (the upfront work is done by then). If you finish the 90 days and feel you didn't get what was promised, I'll do another 30 days at no cost to make it right.

By purchasing you confirm you have read the application page, completed a 30-min discovery call with Eddy, and been invited to enroll. This product is education and not financial advice.
```

**Image**
- Use a 1200×630 hero image rendered from `index.html` (screenshot the hero section). Stripe will crop to a square thumbnail.

**Statement descriptor** (shows on credit-card statement, max 22 chars)
```
EDDY ST90 FOUNDING
```

**Tax behavior**
- `Exclusive` (price excludes tax). If you have tax IDs configured, Stripe will compute it; otherwise leave default.

---

## Price

| Field | Value |
|------|-------|
| Pricing model | **Standard pricing** |
| Price | **$4,500.00** |
| Currency | **USD** |
| Recurring | **No — One-time** |

---

## Payment Link settings (Stripe Dashboard → Payment Links → New)

After product is saved:

| Setting | Value |
|---------|-------|
| Product | Systematic Trader 90 — Founding Cohort (1:1) |
| Quantity | Customer can adjust → **OFF** (always 1) |
| Limit number of payments | **5** total *(this auto-disables the link after the founding cohort fills)* |
| Collect customer information | Name, Email, Phone, **Billing address: required** |
| Custom field 1 | Label: `Telegram handle (for RiskGuard)`, Type: text, Required: yes |
| Custom field 2 | Label: `Hyperliquid wallet address`, Type: text, Required: yes |
| Custom field 3 | Label: `Discovery call date`, Type: text, Required: yes |
| Allow promotion codes | **OFF** (no discounts on founding rate) |
| After payment | **Show confirmation page** with custom text (below) |
| Confirmation message | `Welcome aboard. Check your email in the next 30 minutes for the welcome doc, week-1 checklist, and the link to book your first weekly call. — Eddy` |
| Confirmation email | **ON** (Stripe sends receipt) |
| Save payment method | **OFF** (one-time only) |

---

## Recommended Stripe webhook events to listen for

If/when you set up automation later (Zapier, n8n, custom backend), subscribe to:

- `checkout.session.completed` → trigger welcome email + onboarding doc + Calendar link for week-1 call
- `payment_intent.payment_failed` → personal follow-up DM
- `charge.refunded` → off-board (revoke Discord, archive client folder)

For the founding 5, manual follow-up is fine — no automation needed.

---

## After you create the link

Drop the URL in this file at the bottom and replace the placeholder in `index.html` (search for `STRIPE_LINK_HERE` if I left it; the current sales page sends people to the application form first, then you send the Stripe link after the discovery call closes).

```
STRIPE_PAYMENT_LINK = <paste here>
```

**Don't put the Stripe link on the public sales page.** The funnel is:
1. Sales page → Apply (intake form) →
2. Eddy reviews application → invites to discovery call →
3. Discovery call → if fit, Eddy DMs Stripe link →
4. Payment → automated welcome email → first weekly call booked

This keeps tire-kickers off the payment page and ensures every buyer has been pre-qualified.
