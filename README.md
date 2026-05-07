# 🧬 NCBI BioScraper Pro v2

> **The most comprehensive open-source biomedical literature scraper for
> Google Colab — free-tier compatible, no GPU required.**

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/crisprking/ncbi-bioscraper/blob/main/NCBI_BioScraper_Pro_v2.ipynb)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.8+](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://www.python.org/)
[![NCBI API](https://img.shields.io/badge/API-NCBI%20Entrez-green.svg)](https://www.ncbi.nlm.nih.gov/home/develop/api/)

---

## Table of Contents
- [Features](#-features)
- [Quick Start](#-quick-start)
- [Configuration](#-configuration)
- [Output Files](#-output-files)
- [Architecture](#-architecture)
- [API Keys](#-api-keys)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)
- [Citation](#-citation)
- [License](#-license)

---

## Features

### Core Scraping
| Feature | Details |
|---------|---------|
| **PubMed** | Batch fetch up to 10,000 records with full metadata |
| **Gene DB** | Gene ID, symbol, organism, chromosome location, summary |
| **Nucleotide** | GenBank records + optional FASTA download |
| **GEO** | Dataset metadata and sample counts |
| **SRA** | Run info, platform, spots, bases |
| **Smart caching** | SQLite cache — never re-fetches the same PMID twice |
| **Resumable runs** | Picks up exactly where it left off after a timeout |

### Free Enrichment (no API key required)
| Enrichment | Source | Fields added |
|-----------|--------|-------------|
| Citation counts | [OpenAlex](https://openalex.org) | `oa_citations`, `oa_fwci`, `oa_references`, `oa_top_concepts` |
| Open Access PDFs | [Unpaywall](https://unpaywall.org) | `up_pdf_url`, `up_oa_status`, `up_license` |
| Retraction flag | OpenAlex | `oa_retracted` |

### Analysis and NLP
- **TF-IDF keywords** — top n-grams ranked by corpus weight
- **LDA topic modeling** — auto-tuned topic count
- **Word cloud** — 180-word visual summary
- **Trend analysis** — emerging vs fading terms year-over-year
- **Study type classifier** — RCT, systematic review, meta-analysis, cohort, in-vitro
- **Entity extraction** — drugs, diseases, genes from MeSH terms (no GPU)
- **Semantic deduplication** — near-duplicate detection (>=92% title similarity)

### Visualisation and Reports
- **9-panel dashboard** (Matplotlib, publication-quality PNG)
- **Interactive co-author network** (PyVis HTML, physics-based layout)
- **Concept co-occurrence network** (MeSH major headings)
- **PRISMA-style flow diagram** (screen and print ready)
- **Self-contained HTML report** (all charts base64-embedded, fully shareable)

### Export Formats
CSV, XLSX (auto-formatted), JSON, BibTeX, RIS (Zotero/EndNote), Parquet

---

## Quick Start

1. Click the Colab badge above
2. **Cell 2** — set your search query and year range
3. *(Optional)* add your NCBI API key for 10 req/s instead of 3
4. **Runtime > Run All** — ~3 min setup, then fully automated
5. **Cell 17** — download your ZIP

---

## Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `SEARCH_QUERY` | `'CRISPR cancer therapy'` | Any valid PubMed query |
| `MAX_RESULTS` | `500` | Records to fetch (NCBI caps at 10,000) |
| `START_2026` | `2018` | Publication year filter |
| `END_2026` | `2025` | Publication year filter |
| `ENRICH_OPENALEX` | `True` | Citation count, FWCI, retraction flag |
| `ENRICH_UNPAYWALL` | `True` | Free PDF links |
| `DEDUP_ABSTRACTS` | `True` | Flag near-duplicate titles |
| `EXTRACT_ENTITIES` | `True` | Drug/disease/gene from MeSH |
| `CLASSIFY_STUDIES` | `True` | Auto-classify study type |
| `RUN_NLP` | `True` | TF-IDF, LDA, word cloud |
| `BUILD_NETWORK` | `True` | Co-author and concept networks |
| `TREND_ANALYSIS` | `True` | Emerging vs fading terms |
| `GENERATE_HTML_ncbi-bioscraperRT` | `True` | Standalone HTML report |
| `SAVE_TO_DRIVE` | `True` | Auto-save to Google Drive |
| `USE_CACHE` | `True` | SQLite resumable-run cache |

---

## Output Files

```
PROJECT_pubmed.csv              # Main flat dataset
PROJECT_pubmed.xlsx             # Auto-formatted spreadsheet
PROJECT_pubmed.json             # Machine-readable records
PROJECT_pubmed.parquet          # Analytics-ready (pandas / DuckDB)
PROJECT_pubmed.bib              # BibTeX (LaTeX / Zotero)
PROJECT_pubmed.ris              # RIS (Zotero / EndNote)
PROJECT_keywords.csv            # TF-IDF keyword ranking
PROJECT_topics.csv              # LDA topic table
PROJECT_trends.txt              # Emerging / fading terms
PROJECT_dashboard.png           # 9-panel overview chart
PROJECT_wordcloud.png           # Word cloud
PROJECT_prisma.png              # PRISMA flow diagram
PROJECT_coauthor_network.html   # Interactive author graph
PROJECT_concept_network.html    # Interactive MeSH graph
PROJECT_ncbi-bioscraperRT.html             # Self-contained shareable report
_cache.sqlite                   # Resumable cache (excluded from ZIP)
```

---

## Architecture

```
NCBI_BioScraper_Pro_v2.ipynb
├── Cell  1  — Environment setup (idempotent, ~2 min)
├── Cell  2  — Configuration  <-- edit here
├── Cell  3  — Imports, SQLite cache, directory setup
├── Cell  4  — NCBIScraper class (core engine + bug fixes)
├── Cell  5  — PubMed scrape
├── Cell  6  — OpenAlex citation enrichment
├── Cell  7  — Unpaywall open-access detection
├── Cell  8  — Study type classifier + entity extraction
├── Cell  9  — Semantic deduplication
├── Cell 10  — Multi-format export
├── Cell 11  — Static visualization dashboard
├── Cell 12  — NLP: TF-IDF, LDA, word cloud, trend analysis
├── Cell 13  — Co-author and concept co-occurrence networks
├── Cell 14  — PRISMA-style flow diagram
├── Cell 15  — Standalone HTML report
├── Cell 16  — Gene / Nucleotide / GEO / SRA (optional)
├── Cell 17  — Bundle and download ZIP
└── Cell 18  — Publish to GitHub (this cell)
```

---

## API Keys

### NCBI Entrez API Key (recommended, free)
Without key: 3 req/s. With key: 10 req/s (3x faster for large datasets).

1. Create a free account at https://www.ncbi.nlm.nih.gov/account/
2. Settings > API Key Management > Create new key
3. Paste into `NCBI_API_KEY` in Cell 2

### GitHub Token (Cell 18 only)
1. GitHub > Settings > Developer settings > Personal access tokens > Fine-grained
2. Grant **Contents: Read and Write** on your target repo
3. Paste when Cell 18 prompts you — it is hidden as you type

---

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| `HTTP Error 429` | Add your NCBI API key in Cell 2 |
| Drive mount failed | Set `SAVE_TO_DRIVE = False` |
| `kaleido` errors | `pip install kaleido --upgrade` |
| Empty results | Broaden query or extend year range |
| Stale cache | Delete `_cache.sqlite` from your output folder |
| Push rejected 403 | Confirm PAT has the repo or Contents R/W scope |
| Repo not found 404 | Create the empty repo on GitHub first, then rerun Cell 18 |

---

## Contributing

Contributions, issues, and feature requests are welcome.
See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## Citation

If you use this tool in your research, please cite:

```
@software{ncbi_bioscraper_pro,
  title   = {NCBI BioScraper Pro},
  author  = {crisprking},
  year    = {2026},
  url     = {https://github.com/crisprking/ncbi-bioscraper},
  version = {2.0.0},
}
```

---

## License

MIT (c) 2026 crisprking — see [LICENSE](LICENSE) for details.
