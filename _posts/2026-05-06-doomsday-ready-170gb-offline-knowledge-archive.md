---
layout: post
title: "Doomsday Ready: A 170 GB Offline Knowledge Archive"
date: 2026-05-06
---

So far, on my KendiEgemen channel and across my blog posts, I have repeatedly covered topics like offline LLMs, solar panels, and generators. But offline LLMs hallucinate, which means we also need solid offline data sources. That's exactly what this post is about.

There are three "what if" scenarios:

1. **War breaks out** — fiber lines are cut, regulators shut things down, cellular networks go dark.
2. **Natural disaster** — earthquake, hurricane, grid failure that lasts for weeks.
3. **Just personal** — you're on a flight, out at sea, stuck in a mountain village, and internet is unreliable.

In all three cases, you need the same thing: **knowledge that works without internet**. This folder (`/mnt/d/wikipedia`) is exactly that. Roughly **170 GB** of data — fits on an external drive, easily fits on an SSD, can be backed up, can be carried.

What's inside, and why it's there — in order.

## Inventory at a glance

| Category | Size | Purpose |
|---|---|---|
| English Wikipedia (3 formats) | ~140 GB | General knowledge — the starting point for almost everything |
| Turkish Wikipedia (3 formats) | ~10 GB | Same idea, local language |
| OpenStax books (122 textbooks) | 11 GB | College-level education — math, physics, biology, anatomy, law… |
| Medical/field archive (`offline-medical-survival/`) | 622 MB | When there is no doctor / system collapse |
| Kiwix Desktop + DuckDB tools | 470 MB | To actually open and use everything |
| **Total** | **~170 GB** | |

A 256 GB or 512 GB portable SSD handles this comfortably. Cost: a few hundred dollars. Payoff: when your connection to the world breaks, you still hold a meaningful chunk of humanity's knowledge.

## Wikipedia — why three different formats?

Same Wikipedia, different formats for different jobs:

### `wikipedia_en_all_maxi_2026-02.zim` (124 GB) and `wikipedia_tr_all_maxi_2026-02.zim` (9 GB)

Kiwix ZIM format. **Full Wikipedia including images**. You get the familiar browsing experience — just offline. Snapshot from February 2026.

How to use: run `kiwix-desktop.exe` from `kiwix-desktop_windows_x64_2.5.1/` → File → Open ZIM. Or host it on your local network with `kiwix-serve.exe` so phones can access it too.

> **Doomsday tip:** this is the single most critical file set. Back it up to a second disk first.

### `enwiki-latest-pages-articles.xml.bz2` (24 GB) and `trwiki-latest-pages-articles.xml.bz2` (983 MB)

Raw Wikipedia XML dump. Fresher than ZIM (May 2026), but no images and raw wikitext structure. Best for people building pipelines, writing custom parsers, or wanting newer text.

### `hf_wikipedia/20231101.{en,tr}/*.parquet` (12 GB)

HuggingFace's cleaned parquet Wikipedia snapshot (November 2023). Older, but the text is **clean** — templates expanded, markup stripped. Perfect for SQL search, pandas analysis, and ML workflows.

With DuckDB CLI from `duckdb_cli-windows-amd64.zip`:

```sql
SELECT title FROM 'D:/wikipedia/hf_wikipedia/20231101.tr/*.parquet'
WHERE title ILIKE '%Atatürk%';
```

The three formats complement each other: **ZIM = reading**, **Parquet = search/analysis**, **XML = freshest + your own tools**.

## OpenStax — a portable college library

Inside `openstax/`: **122 PDFs** — the full OpenStax (Rice University) open textbook catalog. Peer-reviewed, free, openly licensed.

Coverage includes math (algebra, calculus 1-3, contemporary math), physics, chemistry, biology (general + AP + concepts), anatomy and physiology, astronomy, micro/macro economics, business ethics, business law, U.S. government, sociology, psychology, anthropology, clinical nursing skills, plus Spanish calculus translations.

A motivated student can self-study a huge chunk of high school + early undergraduate curriculum with this alone. As long as you have minimal electricity — and even without a grid, a charged tablet plus a small solar setup solves that.

> **Doomsday tip:** if you have kids (or might), this folder is one of the strongest education assets you can store. Adding Khan Academy/Kolibri offline packs is a good next project.

## `offline-medical-survival/` — medicine for collapse conditions

This folder is the smallest (622 MB), but arguably the most critical. No doctor, no pharmacy, no ambulance scenarios.

It has 13 subfolders, each targeting a different problem area:

- `hesperian/` — *Where There Is No Doctor* (2025) and *Where There Is No Dentist* (2020)
- `military/` — U.S. Army field manuals: medical, first aid, survival, water/wells, tactical comms, emergency war surgery
- `who-iris/` — WHO core clinical handbooks (hospital care, surgery, essential medicines, maternal care, IMCI/IMAI, etc.)
- `who-icrc/` — WHO/ICRC Basic Emergency Care
- `first-aid/` — IFRC and ICRC first aid references
- `merck-msd/` — MSD manual pages as backup references
- `msf/` — Médecins Sans Frontières field clinical guides
- `icrc-war-surgery/` — ICRC War Surgery Vol. 1
- `cdc/` — emergency supply guidance + travel medicine pages
- `drug-labels/` — DailyMed + FDA Orange Book pages
- `openstax/` — OpenStax Anatomy & Physiology duplicate for quick reference
- `maritime/` — WHO International Medical Guide for Ships
- `niosh/` — NIOSH Pocket Guide to Chemical Hazards

This archive was built by `health.sh` (wget-based) and `health_fixup.py` (Python for WHO IRIS DSpace 7 API). Both are **idempotent** — rerun them and they only fetch missing files.

**Gap:** `wilderness/` is currently empty. Wilderness Medical Society guidelines and several PMC/Sage resources are paywalled. `military/` survival docs and Hesperian material partly cover the need, but not fully.

## Tools

Two `.zip` files in `/mnt/d/wikipedia`:

- **kiwix-desktop_windows_x64_2.5.1.zip** — for reading ZIM files (`kiwix-desktop.exe`)
- **duckdb_cli-windows-amd64.zip** — for querying parquet data (`duckdb.exe`)

The older `README.md` explains usage details. This post explains the "why."

## Threat model — what this archive defends against

| Scenario | Does this archive help? | Notes |
|---|---|---|
| Short internet outage (hours) | Massively | Wikipedia + OpenStax are enough alone |
| Long local outage (weeks) | Yes, strongly | Medical archive + Wikipedia can be life-saving |
| Regional war | Yes, materially | Field medicine, water sourcing, tactical comms |
| Long social collapse (years) | Limited but critical | Foundation for education and rebuilding |
| Personal offline need (flight/boat/mountain) | Of course | Everything you need is already there |

What's still missing:
- Agriculture & food (FAO publications, Survivor Library, Plants For A Future)
- Local maps (no OpenStreetMap dump yet)
- Veterinary medicine (no Merck Veterinary Manual)
- Legal corpus (no official gazette archive)
- Classic literature (no Project Gutenberg mirror)

All addable. Each one is a weekend project.

## Refresh and maintenance

Knowledge decays:

- Refresh Wikipedia ZIM files monthly from `download.kiwix.org/zim/wikipedia/`
- Run `health.sh` and `health_fixup.py` yearly (they skip existing files with `wget -c`)
- WHO/ICRC/MSF guides typically update every 2-5 years
- `_openstax_download.py` pulls in newly published books
- Drug information stales fastest — refresh `drug-labels/` yearly

Keep disks in **two locations**: one primary working copy, one in a separate physical location for fire, theft, flood.

## Final word

Building this folder took a weekend. If you ever need it, it can be worth a decade of accumulated knowledge. Preparedness is not paranoia; it's **insurance**. You don't buy insurance because you hope disaster happens — you buy it because you hope it doesn't, and you still want a fallback.

What you have now: the world's largest encyclopedia (in two languages), a university-level curriculum, field-grade life-saving medical manuals, military field references, water/comms/navigation basics. Pull the cable — it still works.

Good news: communities like Kiwix make this possible with thousands of ZIMs. Bad news: if you don't build your own kit, no one builds it for you.

And one more thing: if you finished reading this, back it up. Now.

In a potential disaster scenario, you can run RAG on top of these documents with your LLM (LM Studio supports this natively) and reach critical factual information with dramatically lower hallucination risk.
