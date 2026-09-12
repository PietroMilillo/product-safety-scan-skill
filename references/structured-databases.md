# Structured databases — direct query patterns

Query these directly before falling back to general web search. They're free, structured, and more current than search snippets.

**Environment caveat — test it, do not assume it.** Some Claude surfaces restrict `web_fetch`
so that it will only open a URL that has already appeared verbatim in a search or fetch
result, and will refuse a query URL you construct yourself even when the syntax is correct
and the API is free and keyless. This behaviour differs between surfaces and changes between
releases, so check it once at the start of a scan rather than trusting either answer here.

- **If direct fetch works** (Claude Code, Cowork, anything with bash and curl): call the
  query patterns below. This is the better route whenever it is available.
- **If it refuses**: web-search the firm name plus the database name or domain
  (`<company> site:fda.gov recall`, or just `<company> FDA recall`), let the search surface
  the actual recall announcement, and cite that. Do not keep hand-building URLs.

## openFDA — food/drug/device enforcement (recalls)

Base: `https://api.fda.gov`. No key required (higher rate limit with a free key, not needed for occasional use).

- Food recalls by firm: `https://api.fda.gov/food/enforcement.json?search=recalling_firm:"<company name>"&limit=10`
- Food recalls by product/brand: `https://api.fda.gov/food/enforcement.json?search=product_description:"<brand or product name>"&limit=10`
- Same pattern works for `/drug/enforcement.json` and `/device/enforcement.json` if relevant (e.g. a supplement or a device-adjacent product).
- Covers 2004–present, updated weekly. Read `reason_for_recall`, `classification` (Class I = most severe), `status`, `distribution_pattern`.
- A firm name won't always match exactly — try variations (with/without "Inc", "Co Ltd", etc.) before concluding there's nothing.

## USDA FSIS recalls (meat, poultry, egg products)

openFDA does NOT cover these — check separately when the product is meat/poultry/egg-based.
- Search: `FSIS recall <company name>` or check `fsis.usda.gov/recalls` — no simple keyless JSON endpoint as clean as openFDA, so this may require a web_fetch of the FSIS recall search page or a web_search fallback.

## CPSC — consumer product recalls (non-food, household/consumer goods)

- `https://www.cpsc.gov/Recalls` has a searchable interface; the underlying open data feed is public domain (per CPSC's own recalls.gov API). Use for anything household/cosmetic-adjacent that isn't strictly food (appliances, personal care devices, children's products).
- Search by company or product name; read hazard type, remedy, and whether it's a Class action or voluntary recall.

## Non-US recall registers — check these, not only the FDA

A brand can have an empty FDA record and a long history somewhere else. These publish, they are
free, and they are where sibling-product recalls of the same brand family turn up.

- **FSANZ** (Australia / New Zealand) — `foodstandards.gov.au/food-recalls`. Recall alerts name
  the recalling importer, the exact product and size, the reason, the affected best-before
  dates, and where it was sold. Individual alerts have stable URLs.
- **UK FSA** — allergy alerts carry references of the form `FSA-AA-NN-YYYY`. **The alert pages
  have been archived and now return HTTP 410 while still appearing in search results.** Cite the
  reference number if no live page exists.
- **CFIA** (Canada) — `recalls-rappels.canada.ca`.
- **Hong Kong CFS** — `cfs.gov.hk`, frequently republishes FSANZ and mainland notices, which
  makes it a useful second confirmation.
- **EU RASFF** — the rapid alert portal, plus the published RASFF analyses. Useful for whole
  commodity categories: the 23-year analysis (Eissa et al., *J. Food Safety*, 2024) ranks the
  most-notified herbs and spices and their dominant hazards.

**Recency rule.** Search the current year and the previous one by name, not just the brand. Then
check the recalled batches' best-before dates against today. A recall whose stock is still in
date is a live hazard, not a historical pattern, and the report must say which it is.

**Link rule.** Open every URL before citing it. Agencies migrate and archive constantly, and a
search result is not proof a page resolves.

## OEHHA Prop 65 chemical list

- The authoritative list of chemicals that trigger California's Prop 65 warning is maintained by OEHHA. Check whether a specific ingredient found on a label (e.g. an additive, a heavy metal, a specific dye) is actually on this list before asserting a warning is "about" that ingredient — don't guess from pattern-matching alone if a direct check is feasible.
- Search: `OEHHA Prop 65 list <chemical name>` or fetch the current list page if linked in search results.

## OpenCorporates — global business registry search

- `https://api.opencorporates.com/v0.4/companies/search?q=<company name>` — works unauthenticated at low volume; a free API token (`&api_token=<token>`) raises the rate limit and is worth getting if this skill gets used often.
- Covers ~140 jurisdictions, so it's useful for the *foreign* manufacturer, not just the US-side distributor — a real advantage over US-only registries like a single state's Secretary of State bizfile.
- Read: entity status (active/dissolved/inactive), incorporation date, jurisdiction, officers if available.

## Reading results together

A hit in openFDA/CPSC/FSIS is a confirmed, sourced recall — this alone can drive a 🔴 verdict per the verdict rubric. A clean result across these databases is a real (though not conclusive) positive data point — note it plainly ("no FDA enforcement actions found for X") rather than treating absence as proof of safety, especially for foreign manufacturers who may not show up in US-centric databases regardless of their actual safety record.
