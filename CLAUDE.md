# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file pricing calculator for TikTok Shop México sellers ("Tienda Dopamina"), with three tabs (two TikTok, one card show):

- **Cuánto cobrar** (`secA`, `render()`): the seller enters the payout they want (la liquidación) and the page solves backwards for the **precio lista** (pays out 100% of the objective) and the **precio flash** (post-coupon price paying out a chosen 85–100% of the objective; fees are charged on this price). The **cupón flash** is the flat peso difference lista − flash, and ROI comes from the actual flash payout.
- **Cuánto recibo** (`secB`, `renderRev()`): the reverse — enter the published price, derive the payout. Optional flat coupon (default none), and a checkbox (`rComm`, default checked) for whether the full platform commission applies; the full-commission rate is the shared `pComm` parameter (default 6%, but public guides report 3–6% by category, so it's editable).
- **Card show** (`secC`, `renderShow()`): not TikTok. Enter a base price for a card/slab/product; the sale price is base × (1 + markup) from the `RANGOS` tier table (under 100 +40%, 100–299 +30%, 300–499 +25%, 500–999 +20%, 1000–2499 +15%, 2500–4999 +10%, 5000–9999 +8%, 10000+ +5%), rounded up to the whole peso (`arriba`, which rounds to cents first to dodge float noise). The tier is chosen by lower bound (99.50 → +40%). Each tier has a floor of its lower bound × (1 + previous tier's markup), so a pricier item never sells for less than a cheaper one at a tier edge.

The platform-parameters panel and footer are shared by the two TikTok tabs (outside the sections) and hidden on the card-show tab. All UI text is Spanish (es-MX); keep it that way.

Everything lives in `index.html`: markup, CSS, and vanilla JS. There is no build step, no dependencies, no test suite, no lint. To run it, open `index.html` in a browser. Deployment is GitHub Pages serving the `main` branch.

## The math (the part worth understanding)

The core is a solve-for-price inversion (`despeje(t)` in `render()`), run twice:

```
lista = (objetivo + tarifa_por_artículo) ÷ (1 − SFP% − comisión% − tr)      // pays out 100%
flash = (objetivo × cupón% + tarifa_por_artículo) ÷ (same denominator)      // pays out the chosen %
cupón = lista − flash                                                        // flat pesos, never a %
```

where `tr = (ISR% + IVA_ret%) / (1 + IVA%)` — the ISR/IVA retentions apply to the IVA-exclusive base, so they're converted to an effective rate on the IVA-inclusive price (≈9.05% with defaults).

Key invariants:
- **85% floor**: the coupon's "% to receive" (`recibe`, slider `recibeBar` + number input, kept in sync) must be ≥ 85 (`MIN_RECIBE`). Below that, `render()` shows a warning and blanks all results — it must not compute anything. Values above 100 are clamped to 100 (coupon = $0).
- **SFP cap**: SFP is 8% of the price but capped at $40. If the uncapped solution exceeds the cap, the solve switches to a second branch treating SFP as the fixed $40 (`k2` branch). Both branches must stay consistent with the forward breakdown below them.
- **Fee base**: all fees are computed on the flash price (the actual post-coupon sale price). TikTok-funded discounts don't touch the payout.
- **Rounding**: both lista and flash round *up* to the nearest `pRnd` (default $5) so payouts are never below their targets. The forward breakdown (table) recomputes fees from the rounded flash, so the shown liquidación can exceed the target — the "te sobran" note reports that surplus.
- The footer formula and README document the same math; if fee logic changes, update both.

Default parameter values (8% SFP, $40 cap, $6/item, 2.5% ISR, 8% IVA ret.) were verified against nine real settlement statements to the cent (as of August 2026). Don't change defaults casually; users can override them in the "Parámetros de la plataforma" panel.

## Conventions

- Guard against degenerate inputs: the solve checks `k1 > 0` / `k2 > 0` and shows an em-dash state instead of NaN. Preserve that behavior when touching the math.
- Currency formatting goes through `mx` (rounded, no decimals) and `mx2` (two decimals) using `es-MX` locale; percentages through `pct`.
- The design is a deliberate "paper ledger" aesthetic (Newsreader serif + JetBrains Mono, light-only color scheme, CSS custom properties in `:root`). Match it rather than introducing frameworks or new fonts.
