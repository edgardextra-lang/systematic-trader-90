# Systematic Trader 90 — Launch Assets

All assets for the boutique program launch. Built per spec at
[`docs/superpowers/specs/2026-04-25-systematic-trader-90-design.md`](../../docs/superpowers/specs/2026-04-25-systematic-trader-90-design.md).

## Files

| File | What it is | Status |
|------|-----------|--------|
| [`index.html`](index.html) | Sales page (hero, outcome, who/who-not, 90-day arc, pricing, FAQ, CTA) | Ready to deploy |
| [`application.html`](application.html) | Intake form (capital, history, P&L, pains, goals, commitment) | **Wire form to Formspree/Tally before deploying** |
| [`STRIPE-PRODUCT.md`](STRIPE-PRODUCT.md) | Stripe product description + payment link config | Paste into Stripe dashboard |
| [`WELCOME.md`](WELCOME.md) | Week-1 onboarding doc client receives after payment | Send via email/Telegram on signup |
| [`RISKGUARD-CLIENT-RUNBOOK.md`](RISKGUARD-CLIENT-RUNBOOK.md) | Eddy's runbook for deploying RiskGuard to a new client | Internal — adjust commands at deploy time |
| [`OUTBOUND-DM.md`](OUTBOUND-DM.md) | DM scripts for the 5 [HyperForge](https://edgardextra-lang.github.io/hyperforge-site/) waitlist leads | Personalize per lead and send |
| [`TIKTOK-SCRIPTS.md`](TIKTOK-SCRIPTS.md) | 3 TikTok scripts + posting plan + don'ts | Record + post day 0 / 2 / 4 |

## Deploy steps (in order)

1. **Wire the application form** — `application.html` form `action=` is currently a placeholder. Sign up at [Formspree](https://formspree.io) or [Tally](https://tally.so), get a form ID, replace `REPLACE_WITH_FORMSPREE_ID` in `application.html`.
2. **Deploy** — push `Product/systematic-trader-90/` to wherever you host `riskguard-landing` (GitHub Pages: create repo `systematic-trader-90`, enable Pages on `main`/root, copy URL).
3. **Update sales-page links** — once deployed, replace `https://[your-url]/...` placeholders in `OUTBOUND-DM.md` and `TIKTOK-SCRIPTS.md` with the real URL.
4. **Stripe** — follow [`STRIPE-PRODUCT.md`](STRIPE-PRODUCT.md) to create the product + payment link with quantity cap of 5.
5. **Send the 5 DMs** — use [`OUTBOUND-DM.md`](OUTBOUND-DM.md), personalize per lead, send. Track responses in a Google Sheet.
6. **Record + post 3 TikToks** — day 0 / 2 / 4 per [`TIKTOK-SCRIPTS.md`](TIKTOK-SCRIPTS.md).
7. **Watch the funnel** — application form fills → discovery calls booked → Stripe link sent (DM only, NOT public) → payment.

## Funnel summary

```
TikTok / DM → Sales page → Application form → 30-min discovery call → Stripe link (DM only) → Payment → Welcome doc
```

## Success metric (next 45 days)

- 5 founding spots filled at $4,500 = **$22,500**
- ≥3 testimonials by day 90 (with anonymization option)
- TikTok→Discord conversion baseline established
- ≥1 HyperForge product insight per client logged in HF backlog
