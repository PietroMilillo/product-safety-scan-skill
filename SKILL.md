---
name: product-safety-scan
description: Analyze photos of a food, cosmetic, or household product's packaging/label to assess trustworthiness — flags additives on the user's avoid-list, checks the manufacturer and distributor for recalls/lawsuits/regulatory action, weighs country-of-origin, growing-region contamination and factory-condition risk, explains any warnings in plain language against the thresholds that actually apply, and can suggest better-vetted alternatives. Use this whenever the user shares photos of a product label, jar, box, or ingredient panel and asks whether it's safe, trustworthy, "sketchy," or how it compares to alternatives — even if they don't name the skill directly. Also trigger on requests like "check this product," "any red flags with this brand," or "is this company okay."
---

# Product Safety Scan

> **What this produces, and what it does not.** This skill returns a summary of what is publicly
> findable about a product and what it means against the rules that apply. It is **not** a safety
> assessment, a certification, a laboratory result, or medical, dietary or legal advice. It cannot
> test anything. It reports records, measurements and thresholds published by other people, and
> those can be incomplete, outdated or wrong.
>
> **Never state or imply that a product is unsafe to consume.** Report the findings, name the
> thresholds each one clears or fails, and say who disagrees with whom. The reader draws the
> conclusion. This is both the honest position and the one that keeps a verdict from becoming an
> assertion the user would have to defend.
>
> A verdict colour is shorthand for what the scan found. It is not a pronouncement about a company,
> and republishing one as a claim about a named business is the user's own act and the user's own
> risk.


A workflow for turning photos of a product's packaging into a grounded, sourced read on whether to trust it — not a definitive safe/unsafe verdict, but an evidence-based summary the user can act on.

## Step 0: Read the label

From the photos, extract:
- Product name, net weight, barcode
- Manufacturer name + address
- Distributor/importer name + address (often different company, different country)
- Full ingredient list
- Any warnings (Prop 65, allergens, "keep refrigerated," etc.)

**Allergen labelling is not the same everywhere, and a US label is the thinnest of the major regimes.** The US requires nine (milk, egg, fish, crustacean shellfish, tree nuts, peanuts, wheat, soy, and sesame since 2023). The EU and UK require fourteen. **The five the US does not require are celery, mustard, sulphites, lupin and molluscs.** If the user's allergy is one of those five, a compliant US label will tell them nothing, and a recall for that allergen in Britain or Australia may be the only signal that exists. Search the brand family in those jurisdictions before telling anyone a product is clear for an allergen outside the US nine.
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

Search the **brand family**, not only the exact SKU. A recall of a sibling product by a
different importer in a different country is a real signal about the maker's labelling and
process control, and it is the finding most often missed.

**Search the jurisdictions that actually publish recalls, not just the US.** A brand can have a
clean FDA record and a long history elsewhere:

- **FSANZ** (Australia/NZ) — `foodstandards.gov.au/food-recalls`
- **UK FSA** — alerts carry a reference like `FSA-AA-32-2025`
- **CFIA** (Canada) — `recalls-rappels.canada.ca`
- **Hong Kong CFS** — often republishes FSANZ and mainland notices
- **EU RASFF** — the EU rapid alert portal

**Check recency explicitly, and check whether affected stock is still in date.** Search the
current year and the previous one by name. A recall is not history if the recalled batches carry
best-before dates that have not passed yet — say so, with the date, because that is the
difference between a pattern and a live hazard.

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

## Step 3d: Check the ground, not just the company

Run this whenever the product is an agricultural commodity that concentrates soil contaminants:
spices, herbs, tea, rice, cocoa, roots, leafy greens, seaweed, mushrooms.

See `references/origin-risk.md` for the full procedure. In brief: a legitimacy check answers
*is this firm real*; it does not answer *what is in the soil where this grew*. A hundred-year-old
listed company can still be farming a floodplain downstream of a smelter. Locate the growing
region rather than the head office, find out whether its contamination is industrial or high
geological background, and then **go looking for a published measurement of the commodity
itself** rather than stopping at regional soil data.

Before borrowing any study, check its **sample frame** — market, origin, sampling year, commodity
form. A study only speaks about what it sampled. If it does not match the product in front of
you, say so and find one that does; do not soften the mismatch into "studies suggest."

**Never infer disease from geography.** Regional illness statistics have their own attributed
causes. Report what the epidemiology attributes, and never pin it on the product.

## Step 4: Explain warnings

Translate any on-label warning into plain language: what triggers it, and who it is actually relevant to (pregnant women, people with a soy allergy, and so on).

Then establish whether the warning is specific to this product or carried across its whole
category — **and do not stop there if it is category-wide.** A category-wide warning is not
noise. It is weak evidence about the category, and the category is what the product is made of.
"Most chilli products carry this label" and "there is measurable lead in chilli" are both true,
and only the second one is informative.

So: find the number. See `references/contaminant-thresholds.md`. Report the measured level, when
anyone has measured it, against **every threshold that applies** rather than the one that
supports the conclusion you already reached:

- an exposure-based **warning trigger** (Prop 65 lead: 0.5 µg/day),
- a hard **concentration ceiling** (EU lead in dried *Capsicum*: 0.60 mg/kg),
- the **national limit** where it was grown,
- and any published **risk assessment** (THQ/TTHQ below 1, TCR below 10⁻⁴).

These disagree with each other by design. A product can trigger a Californian warning, fail an
EU ceiling, pass its home country's limit, and still show a hazard quotient below 1. Report all
four. Do not collapse them into "safe" or "unsafe," and do not dismiss the warning as boilerplate
just because it is common.

## Step 5: Alternatives (only if something was flagged, or the user asks)

Suggest 2-3 comparable products, ranked on a **weighted mix** of:
- Cleaner ingredient list / fewer avoid-list hits
- Better-documented safety or labor record for the maker
- Fit for the same use case (don't suggest something that doesn't actually substitute)

Be explicit about the tradeoffs — e.g. "cleaner ingredients but pricier" or "similar profile, no better-documented track record, just less press coverage."

## Step 5b: Assign a verdict — 🟢 / 🟡 / 🔴

Lead the summary with one of these three, decided by **worst-finding-wins**: the single worst thing found sets the color, full stop. A red finding is never softened by an otherwise-clean scan, and is never averaged against unrelated positives.

**🔴 RED** — any one of these is sufficient on its own:
- A confirmed recall tied to *this* manufacturer, importer or brand family that is **live**: affected stock still inside its best-before dates, or the recall is under twelve months old
- A recall of any age for a **pathogen or an undeclared allergen**, where the same failure mode plausibly recurs (repeat recalls by the same firm, or repeat failures of the same kind)
- Regulatory action or a credible lawsuit against *this* manufacturer or distributor specifically (not just the product category in general)
- Manufacturer or distributor legitimacy doesn't hold up: no findable business registration, no trade/shipment history, an unverifiable entity — especially stacked with a weak contact channel
- A warning that is specific and unexplained, with no plausible industry-wide cause
- A regulatory-restricted ingredient (banned or restricted somewhere), as opposed to merely personally-avoided
- A published measurement of this product or its commodity-and-origin **above a hard regulatory ceiling in the market where it is being sold** (not merely above a warning trigger, which is a much lower bar)

**🟡 YELLOW** — none of the above, but:
- One or more avoid-list ingredients present, though none regulatorily restricted, and the company otherwise checks out
- A warning is present and turns out to be category-wide rather than specific to this maker (e.g. Prop 65 lead on a chilli product). **Category-wide still means yellow, not green** — the finding is real, it is just not about this firm. Report the measured levels and which thresholds they clear or fail
- A commodity-and-origin measurement sits above a warning trigger or a foreign ceiling but inside the limit of the market where it is sold, with a published risk assessment below the concern threshold
- Nobody has measured the commodity from this origin, so the question stays open
- A **closed** recall older than twelve months whose stock is out of date and which does not form a repeating pattern. Name it, date it, say who conducted it, and say it is closed. Do not hide it and do not treat it as disqualifying
- A recall of a product carrying this brand but run by a **contract co-packer** rather than the named maker. Report it and attribute it to the co-packer by name; it bears on the brand's supply chain, not on its own plant
- Minor soft legitimacy flags (address mismatch, personal-email contact, thin search coverage) on top of a business that's otherwise real, with no adverse history
- Some part of the picture genuinely couldn't be verified either way

**🟢 GREEN** — none of the above:
- No avoid-list ingredients, or nothing beyond something trivial
- No warnings; or a warning explained as category-wide **and** the commodity measured within every applicable limit
- Manufacturer and distributor both verifiably legitimate with no adverse findings

**Why 🔴 carries a clock.** Every food company of any size has a recall somewhere in its history. If any recall at any date set red, every mature firm would be red and the colour would carry no information. Recency, severity and repetition are what separate a live hazard from a company that has been operating long enough to have had a bad batch. Say which one you found.

State the verdict and the one worst (or best) finding driving it in the same breath — don't just show a color with no reasoning attached.

## Step 6: Deliver as a chat summary

Default output is a short conversational summary:
- Verdict (🟢/🟡/🔴) + the finding driving it, stated up front
- Ingredients flagged (if any)
- What the manufacturer/distributor search turned up (or didn't)
- Warnings explained
- Alternatives, if applicable

Only build a longer structured report/artifact if something is genuinely flagged (a confirmed recall, a real regulatory issue, a plant with credible bad press) or if the user asks for one. Don't produce a report for a clean scan — a paragraph is enough.

## Notes on epistemic care and on what not to assert

**Say what was found, not what it means for safety.** "Lead in this commodity from this region
averaged 0.89 mg/kg, above the EU ceiling of 0.60 and inside the Chinese limit, with a published
hazard quotient below 1" is reportable and defensible. "This product is dangerous" is neither, and
it is the sentence that turns a research summary into a claim somebody may have to answer for.

**Do not allege intent.** No fraud, no cover-up, no "they knew." Nothing in a recall register
supports a state of mind, and asserting one is the fastest way to turn a true finding into an
actionable one.

**Attribute every adverse finding to the body that published it**, by name and date, with a link
that resolves. An accurately repeated public record is very different from a characterisation of
a company.


- This skill produces a "here's what's findable and what it means" summary, not a certification of safety. Say so if asked directly.
- Never invent a recall, lawsuit, or factory incident. If search comes up empty, that's a real (if weaker) finding — report it as such.
- Distinguish speculation/forum chatter from sourced reporting; don't launder the former as the latter.
- **Check that every URL you cite actually resolves.** Agencies migrate and archive; a search engine returning a link is not evidence the link is live. The UK FSA's alert pages now return 410 while still appearing in search results. If the page is gone and no live mirror exists, cite the alert reference number in plain text rather than substituting a weaker source — and never cite an aggregator that describes its own pages as automatically generated.
- **A study only speaks about what it sampled.** Check market, origin, sampling year and commodity form before applying any finding to the product in hand. If the frame does not match, say the study cannot speak to this product.
- **Separate the question the evidence answers from the question that was asked.** "The manufacturer is a real company with a large farm" answers a legitimacy question and not a contamination question. When you catch yourself reassured by the wrong evidence, name it.
