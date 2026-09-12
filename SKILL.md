---
name: product-safety-scan
description: Analyze photos of a food, cosmetic, or household product's packaging/label to assess trustworthiness — flags additives on the user's avoid-list, checks the manufacturer and distributor for recalls/lawsuits/regulatory action, weighs country-of-origin and factory-condition risk, explains any warnings in plain language, and can suggest better-vetted alternatives. Use this whenever the user shares photos of a product label, jar, box, or ingredient panel and asks whether it's safe, trustworthy, "sketchy," or how it compares to alternatives — even if they don't name the skill directly. Also trigger on requests like "check this product," "any red flags with this brand," or "is this company okay."
---

# Product Safety Scan

A workflow for turning photos of a product's packaging into a grounded, sourced read on whether to trust it — not a definitive safe/unsafe verdict, but an evidence-based summary the user can act on.

## Step 0: Read the label

From the photos, extract:
- Product name, net weight, barcode
- Manufacturer name + address
- Distributor/importer name + address (often different company, different country)
- Full ingredient list
- Any warnings (Prop 65, allergens, "keep refrigerated," etc.)
- Nutrition panel only if relevant to the user's question (usually skip — not the point of this skill)

If any of this is illegible or cut off across the photos, say so rather than guessing.

## Step 1: Ask how deep to go

Ask before researching, unless the request already settles it. Offer roughly:
- **Quick** — label read + flag known additives, 1-2 targeted searches on the manufacturer/distributor
- **Deep** — full pass: recalls, lawsuits, complaints, factory/plant history, investigative journalism, country-of-origin risk

Skip asking only if the user's message already made the depth obvious (e.g. "just tell me if the additives are bad" = quick; "dig into this company" = deep).

## Step 2: Check ingredients against the avoid-list

See `references/avoid-list.md` for the user's standing flags. **This file ships empty.** If it has no entries yet, say so plainly, skip to Step 3, and offer to help the user write it — never substitute a list of your own, and never treat the example file as though it were theirs. For each listed ingredient present:
- Name it plainly and explain in one sentence what it is / why it's on the list (mechanism, not scare language)
- Distinguish "regulatory-flagged" (e.g. Prop 65, banned in EU) from "commonly avoided but not regulatorily restricted" (e.g. MSG, seed oils) — don't conflate the two

## Step 3: Query structured databases (if quick or deep chosen)

These free, structured sources are more current and reliable than general search snippets — but see `references/structured-databases.md` first for an environment caveat: some surfaces refuse a query URL you build yourself and open only URLs that already appeared in a search result. Test it once, then route accordingly: where direct fetch works (Claude Code and anywhere with shell access), call the query patterns below; where it does not, web-search the firm name plus the database name or domain and cite what comes back. In brief, the databases worth checking:

- **openFDA food/drug/device enforcement** (`api.fda.gov`) — the single best first stop for food products; free, keyless, weekly-updated, back to 2004. Query by firm name (manufacturer and distributor separately).
- **CPSC recalls** (`cpsc.gov`) — free, public, covers household/consumer products (non-food). Query by company/brand name.
- **OEHHA Prop 65 chemical list** — the actual official list; use it to confirm whether a specific ingredient found on the label is regulatory-listed, rather than inferring from lawsuit patterns alone.
- **OpenCorporates** — aggregates official company registries across ~140 jurisdictions (works for foreign manufacturers, not just US distributors); a free API token is worth getting for reliability but unauthenticated/rate-limited queries still work for one-off checks.
- **USDA FSIS recalls** — separate database for meat/poultry/egg products, not covered by openFDA.

Treat a hit from any of these as a sourced, high-confidence finding. Treat a clean result as a real (if weaker) data point, same as an empty web search — note it, don't oversell it as a certification.

## Step 3b: General web research (fills gaps the databases above don't cover)

Search separately for the manufacturer and the distributor by name — they're often unrelated companies in different countries with different track records.

For each, look for:
- Confirmed recalls or regulatory action not caught by Step 3 (EU RASFF, other countries' agencies, etc.)
- Lawsuits, formal complaints, or credible investigative journalism (not forum speculation)
- Country-of-origin manufacturing risk gets **high weight** by default: if the plant is in a country/region with a documented pattern of food-safety or labor issues, say so explicitly and look specifically for factory-condition reporting (labor practices, environmental violations, contamination incidents) — not just product-specific recalls
- If nothing turns up, say that plainly — absence of news is not proof of a clean record, especially for smaller or foreign manufacturers with less press coverage in English

Cite what you find. If a claim can't be sourced, don't include it.

## Step 3c: Distributor/importer legitimacy check (deep scans, or when something looks off)

Beyond news/recall search, check whether the businesses on the label actually check out. See `references/legitimacy-check-sources.md` for the source list and how to use each one. In brief:

- **Customs/trade shipment history** (e.g. Panjiva, ImportGenius) — paywalled beyond snippets, so this stays a web-search-and-cite check, not a direct query. A real, sustained shipping relationship between the named manufacturer and distributor is a strong positive signal and hard to fake. No history at all for a supposedly established importer is a real red flag.
- **FDA Food Facility Registration** — for food specifically, US importers/manufacturers are required to register; check whether the named entity shows up.
- **OpenCorporates / state registry lookup** (see Step 3 above) — confirms the entity exists, is active (not dissolved/suspended), and who's behind it.
- **Address consistency** — compare the label address against registry and shipping-record addresses. A mismatch (e.g. warehouse vs. office) isn't necessarily disqualifying but is worth naming.
- **Home-country legitimacy signals** — for the manufacturer, look for state/industry recognition, disclosed revenue, "top enterprise" listings, or similar. For a company of real size, being invisible to its home country's business press is itself a signal.
- **Contact-channel professionalism** — personal webmail (Gmail, etc.) vs. a company domain, working phone number, real website. Treat this as a soft signal only — plenty of legitimate small/lean importers use personal email — and weight it up or down based on what the other checks show.

Weigh these together rather than any single one alone. A personal-email contact plus a real, multi-year shipment history reads very differently than a personal-email contact plus no findable registration and no shipment history at all.

## Step 4: Explain warnings

Translate any on-label warning into plain language: what triggers it, who it's actually relevant to (e.g. pregnant women, people with soy allergies), and whether it's a legal-boilerplate warning (like blanket Prop 65 labels many products carry) versus something specific to this product.

## Step 5: Alternatives (only if something was flagged, or the user asks)

Suggest 2-3 comparable products, ranked on a **weighted mix** of:
- Cleaner ingredient list / fewer avoid-list hits
- Better-documented safety or labor record for the maker
- Fit for the same use case (don't suggest something that doesn't actually substitute)

Be explicit about the tradeoffs — e.g. "cleaner ingredients but pricier" or "similar profile, no better-documented track record, just less press coverage."

## Step 5b: Assign a verdict — 🟢 / 🟡 / 🔴

Lead the summary with one of these three, decided by **worst-finding-wins**: the single worst thing found sets the color, full stop. A red finding is never softened by an otherwise-clean scan, and is never averaged against unrelated positives.

**🔴 RED** — any one of these is sufficient on its own:
- A confirmed recall, regulatory action, or credible lawsuit tied to *this* manufacturer or distributor specifically (not just the product category in general)
- Manufacturer or distributor legitimacy doesn't hold up: no findable business registration, no trade/shipment history, an unverifiable entity — especially stacked with a weak contact channel
- A warning that is specific and unexplained, with no plausible industry-wide/boilerplate cause
- A regulatory-restricted ingredient (banned or restricted somewhere), as opposed to merely personally-avoided

**🟡 YELLOW** — none of the above, but:
- One or more avoid-list ingredients present, though none regulatorily restricted, and the company otherwise checks out
- A warning is present but explainable as industry-wide/boilerplate (e.g. Prop 65 lead on a chili product) rather than specific to this maker
- Minor soft legitimacy flags (address mismatch, personal-email contact, thin search coverage) on top of a business that's otherwise real, with no adverse history
- Some part of the picture genuinely couldn't be verified either way

**🟢 GREEN** — none of the above:
- No avoid-list ingredients, or nothing beyond something trivial
- No warnings, or a warning fully explained as boilerplate
- Manufacturer and distributor both verifiably legitimate with no adverse findings

State the verdict and the one worst (or best) finding driving it in the same breath — don't just show a color with no reasoning attached.

## Step 6: Deliver as a chat summary

Default output is a short conversational summary:
- Verdict (🟢/🟡/🔴) + the finding driving it, stated up front
- Ingredients flagged (if any)
- What the manufacturer/distributor search turned up (or didn't)
- Warnings explained
- Alternatives, if applicable

Only build a longer structured report/artifact if something is genuinely flagged (a confirmed recall, a real regulatory issue, a plant with credible bad press) or if the user asks for one. Don't produce a report for a clean scan — a paragraph is enough.

## Notes on epistemic care

- This skill produces a "here's what's findable and what it means" summary, not a certification of safety. Say so if asked directly.
- Never invent a recall, lawsuit, or factory incident. If search comes up empty, that's a real (if weaker) finding — report it as such.
- Distinguish speculation/forum chatter from sourced reporting; don't launder the former as the latter.
