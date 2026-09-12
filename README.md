# Product safety scan — a Claude skill

Hand Claude photographs of a product label. It reads the ingredient panel and both company
names, checks the ingredients against a list **you** wrote in advance, searches the
manufacturer and the importer separately for recalls and regulatory action, tests whether
both businesses actually exist and ship things, explains any warning on the package in
plain language, and returns 🟢 / 🟡 / 🔴 with the single finding that produced it.

It tells you what is findable and what it means. It does not certify that anything is safe,
and it will say so if you ask it to.

## What it checks

- **The label** — ingredients against a list you write, allergens, warnings.
- **The two companies** — manufacturer and importer are usually different firms in different
  countries with different records. Searched separately.
- **Recalls across jurisdictions** — FSANZ, UK FSA, CFIA, Hong Kong CFS and EU RASFF as well as
  the FDA, for the whole brand family rather than the exact SKU, with a check on whether
  recalled stock is still inside its best-before dates.
- **The ground it grew in** — for spices, herbs, tea, rice and other crops that concentrate soil
  metals, the growing region rather than the head office, and whether anyone has published a
  measurement of the commodity itself.
- **The warning, against every threshold that applies** — a Prop 65 exposure trigger, an EU
  concentration ceiling, the national limit where it grew, and any published risk assessment.
  These disagree by design, and the disagreement is the finding.

## The verdict rule

**Worst finding wins.** One confirmed recall sets red. One company you cannot verify sets
red. Nothing averages it back down, however clean the rest of the scan came out.

Rating systems drift toward the mean because averaging feels fair. Averaging is the wrong
operation for hazard, where the question is never how a product scores overall but whether
anything about it is disqualifying.

## `avoid-list.md` ships empty, and that is the point

The skill flags an ingredient because it appears in `references/avoid-list.md`, and for no
other reason. That file arrives with category headers and nothing under them.

My own list is in `references/avoid-list.example.md`. It is there as an example of the
format and not as advice. It contains things whose evidence base is weaker than my kitchen
habits imply, which is allowed for a personal preference and not allowed for a checklist
that other people read as findings.

So the skill keeps two kinds of entry apart and reports them differently:

- **regulatory-restricted** — a named authority has banned or limited it. A fact about the world.
- **personally avoided** — you would rather not. A fact about you.

Software that blurs those two is worse than no software. Write your list before you scan
anything, because a list assembled while looking at a product you already distrust will
agree with you.

## A category-wide warning is not nothing

The first version of this skill treated a Prop 65 label as boilerplate once it turned out most
of the category carried one, and stopped there. That was wrong, and an essay written from its
output nearly shipped the error.

Most imported chilli products do carry the warning. A 2024 study in *Scientific Reports* also
measured 130 batches of dried chilli across nine Guizhou prefectures and found mean lead of
0.89 mg/kg. The EU ceiling for dried *Capsicum* is 0.60. Chinese national limits were exceeded
in 11% of samples. The authors' hazard quotient came out below 1 everywhere.

Four rules, one chilli, four answers. The skill now reports all four instead of picking the one
that settles the question. See `references/contaminant-thresholds.md`.

## Install

```
git clone https://github.com/PietroMilillo/product-safety-scan-skill.git \
  ~/.claude/skills/product-safety-scan
```

Then open `references/avoid-list.md`, fill it in, and ask Claude whether the jar on your
counter is any good.

Works in Claude Code, Claude Desktop and the claude.ai apps wherever skills are supported.
One caveat worth knowing: in some surfaces `web_fetch` will refuse a query URL you
construct yourself and only open URLs that already appeared in a search result, which
breaks direct calls to openFDA and OpenCorporates. `references/structured-databases.md`
explains how to test this once and which route to take in each case.

## What it found when I pointed it at my own kitchen

A Chinese chili crisp whose importer lists a Gmail address, which I read as amateurism,
and which turns out to have 1,364 customs shipments on file going back to 2007. A Prop 65
reproductive-harm warning that turned out to be about lead in agricultural soil and to sit
on most of the imported spice aisle. A half gallon of milk that was just milk.

I wrote about where that left my original suspicion in
[*Eight Thousand Hectares*](https://theaiprof.substack.com/p/us-food-additives-eight-thousand-hectares)
on The AI Prof.

Fact-checking that essay is what produced most of this skill's later revisions: the origin
step, the threshold comparison, the recency clock on red verdicts, and the note about US
allergen labelling. Every one of them exists because the first version of the skill got
something wrong and the checking caught it.

## Disclaimer

This is a research aid, not a safety assessment. It cannot test anything. It summarises records,
measurements and regulatory thresholds published by other people, any of which may be incomplete,
outdated or wrong, and it reasons about them with a language model that can make mistakes.

**Nothing it produces is a certification, a laboratory result, or medical, dietary or legal
advice.** Do not use it to decide whether something is safe to eat. Use it to find out what is on
the record and which rules apply, then decide for yourself, and consult a professional where it
matters.

The skill is instructed never to state that a product is unsafe. It reports findings against
thresholds and names who disagrees. If you republish an output as a claim about a named company,
that is your act and your responsibility, not the tool's.

Provided as-is, without warranty of any kind, under the MIT licence below. The authors accept no
liability for any decision taken on the basis of anything it produces.

## Licence

MIT. See [LICENSE](LICENSE). Take it, adapt it, keep the notice.

Pull requests welcome, with one exception: no additions to `avoid-list.md`. That file stays
empty in this repository.
