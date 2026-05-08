# 🧬 NCBI BioScraper Pro v3.5

The most comprehensive open-source biomedical literature scraper for Google Colab. Free tier compatible. No GPU required. ~3 min cold start.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/crisprking/ncbi-bioscraper/blob/main/ncbi_bioscraper_pro_v3.5.ipynb)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

---

## What it does

Pulls up to thousands of PubMed records by query + year range, then enriches each one with:

- **Citation data** (OpenAlex): `cited_by_count`, FWCI, references, retraction flags, top-5 concepts
- **Full-text URLs** via a 7-stage waterfall (PMC → Europe PMC → Unpaywall → CrossRef → OA Button → bioRxiv direct → bioRxiv title-search). Real full-text yields of **60–70%** on broad PubMed sets, vs ~25% for naive Unpaywall-only pipelines.
- **PDF text extraction** (optional, capped) — pulls plain text from resolved OA PDFs via `pypdf`
- **LLM summaries** (optional) — TL;DR + key findings via Groq's free `llama-3.1-8b-instant`
- **Study-type classification** — RCT / meta-analysis / cohort / case report / preprint / etc.
- **MeSH entity extraction** — drugs, diseases, genes
- **Semantic deduplication** — token-set ratio ≥ 92%

Then it generates:

- A 9-panel matplotlib dashboard
- TF-IDF top keywords + LDA topics + word cloud
- Year-over-year emerging-vs-fading term analysis
- Interactive PyVis co-author and concept networks (HTML)
- A PRISMA-style flow diagram
- A self-contained, share-ready HTML report
- Exports in **CSV, XLSX, JSON, Parquet, BibTeX, RIS**
- A single ZIP bundle of everything

All cached to SQLite — re-running on the same query is incremental, not from scratch.

---

## Quick start

1. **Open the notebook in Colab** (badge above).
2. **Cell 2:** put a real email in `NCBI_EMAIL` (Unpaywall and CrossRef require this — placeholders are auto-skipped). Optionally add an `NCBI_API_KEY` from https://www.ncbi.nlm.nih.gov/account/ for a 3× speedup.
3. Set `SEARCH_QUERY`, `MAX_RESULTS`, `START_YEAR`, `END_YEAR`.
4. Runtime → Run all. The final cell zips and offers a download.

That's it. ~3 minutes for setup, then ~3–10 minutes for 500 records depending on enrichment toggles.

---

## OA waterfall — the real bottleneck

PubMed pipelines that report inflated OA percentages typically get there by counting *any* OA flag from a single source (usually Unpaywall). The number that matters is whether you can actually pull a PDF or full XML — and PMC IDs don't map cleanly to PubMed IDs across publishers, so Unpaywall-only leaves a lot on the table.

This notebook chains **seven providers** in priority order and stops at the first one that returns a real downloadable URL:

| # | Provider           | Best at                                                    |
|---|--------------------|------------------------------------------------------------|
| 1 | PMC OA             | NIH-funded papers, gold-standard PMC OA subset             |
| 2 | Europe PMC         | European publishers, fills gaps PMC misses                 |
| 3 | Unpaywall          | The widest DOI→OA index for everything else                |
| 4 | CrossRef           | CC-licensed papers <30 days old not yet in Unpaywall       |
| 5 | OA Button          | Author-deposited and repository copies                     |
| 6 | bioRxiv direct     | `10.1101/*` DOIs (preprints)                               |
| 7 | bioRxiv title-search | Last resort: preprint twins under different DOIs         |

Each record gets a transparent provenance trail in the `oa_attempts` and `oa_source` columns.

---

## Configuration cheat sheet

All toggles live in **Cell 2**. The defaults are sensible. Common tweaks:

```python
# Faster, fewer enrichments
ENRICH_OPENALEX   = True   # citations
EXTRACT_PDF_TEXT  = False  # skip PDF download
GROQ_SUMMARIES    = False  # skip LLM
PREPRINT_TITLE_SEARCH = False  # skip title-similarity fallback

# Maximum yield
ENRICH_OPENALEX   = True
EXTRACT_PDF_TEXT  = True
PDF_TEXT_MAX_RECORDS = 200  # raise the cap
GROQ_SUMMARIES    = True   # also: os.environ['GROQ_API_KEY'] = 'gsk_...'
PREPRINT_TITLE_SEARCH = True
```

---

## Output schema

The DataFrame written to CSV/XLSX/JSON has these columns (per record):

**Core PubMed:** `pmid`, `title`, `abstract`, `authors`, `first_author`, `last_author`, `n_authors`, `journal`, `year`, `doi`, `pmcid`, `mesh_terms`, `mesh_major`, `keywords`, `affiliations`, `country`, `pubtype`, `language`, `grant_ids`, `url`, `doi_url`, `pmc_url`

**OpenAlex enrichment:** `oa_citations`, `oa_references`, `oa_fwci`, `oa_is_oa`, `oa_oa_status`, `oa_type`, `oa_retracted`, `oa_top_concepts`

**OA waterfall:** `oa_source` (which provider resolved it), `oa_pdf_url`, `oa_xml_url`, `oa_html_url`, `oa_license`, `oa_status` (gold/green/bronze/closed), `oa_full_text` (bool), `oa_attempts` (provider trace)

**Optional:** `oa_full_text_extracted` (PDF text), `oa_full_text_chars`, `llm_tldr`, `llm_key_findings`, `llm_methods`

**Analysis:** `study_type`, `entities_drugs`, `entities_diseases`, `entities_genes`, `is_duplicate`, `duplicate_of_pmid`

---

## License

MIT. See `LICENSE`.

## Citation

If this saves you time in a publication, a short acknowledgment is appreciated:

> Article metadata was retrieved from PubMed via the NCBI BioScraper Pro pipeline (https://github.com/crisprking/ncbi-bioscraper).
