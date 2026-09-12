# Product safety scan — a Claude skill

Hand Claude photographs of a product label. It reads the ingredient panel and both company
names, checks the ingredients against a list **you** wrote in advance, searches the
manufacturer and the importer separately for recalls and regulatory action, tests whether
both businesses actually exist and ship things, explains any warning on the package in
plain language, and returns 🟢 / 🟡 / 🔴 with the single finding that produced it.

It tells you what is findable and what it means. It does not certify that anything is safe,
and it will say so if you ask it to.

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

I wrote about where that left my original suspicion in an essay called *Eight Thousand
Hectares* on [The AI Prof](https://theaiprof.substack.com). Direct link goes here when it
publishes.

## Licence

MIT. See [LICENSE](LICENSE). Take it, adapt it, keep the notice.

Pull requests welcome, with one exception: no additions to `avoid-list.md`. That file stays
empty in this repository.
