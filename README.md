# NCBI BioScraper Pro

Zero-cost Colab notebook for biomedical literature mining: PubMed search,
OpenAlex citation enrichment, and Open Access full-text resolution with
actual text extraction (not just URLs).

## What it does

**PubMed scrape:** configurable query, date range, and result cap.
Resumable via SQLite cache. Roughly 500 records / 3 minutes on free Colab
without an NCBI API key (key triples throughput).

**OA full-text resolution** uses a six-provider waterfall:
PMC → Unpaywall → Europe PMC → CrossRef license → Semantic Scholar →
bioRxiv/medRxiv direct lookup. Optional preprint title-similarity
fallback via Europe PMC catches preprint twins of paywalled papers.

**Text extraction:**
- PMC content uses NCBI's BioC API (officially endorsed for automated
  retrieval — returns parsed plain text, no PDF parsing needed).
- Non-PMC OA content uses pypdf with a browser-style User-Agent.
- Failed extractions are logged with specific failure reasons
  (HTTP 403, 429, redirect-to-login, parse error, etc.) for inspection
  via `df.attrs['pdf_extract_failures']`.

**Other modules in the notebook:**
study-type classification, entity extraction (drugs/diseases/genes from
abstracts), semantic near-duplicate detection, and multi-format export
(CSV, XLSX, JSON, BibTeX, Parquet, RIS).

## What it does NOT do

- No Sci-Hub fallback (deliberate — copyright)
- No automated scraping of paywalled publisher landing pages
- No OCR — scanned PDFs are skipped, not extracted
- No LLM API calls unless you explicitly enable `GROQ_SUMMARIES = True`
  and provide `GROQ_API_KEY`

## Measured performance

Run the notebook on your query and check `df.attrs['coverage_metrics']`
for the canonical metrics dict. Yields vary by query: PMC-heavy
queries reach 65–75% extracted_text_pct; Elsevier/Nature/Wiley-heavy
queries land in the 35–45% range.

Yields are query-dependent — publish numbers from your own runs, not
a placeholder taken from someone else's data.

## Setup

1. Open the notebook in Colab.
2. In Cell 2, set `NCBI_EMAIL = 'you@your.domain'` (a real email is
   required by Unpaywall and CrossRef per their ToS).
3. (Optional) Get a free [NCBI API key](https://www.ncbi.nlm.nih.gov/account/)
   to triple PubMed throughput.
4. Run cells in order. Cell 7 (OA resolution) is self-contained and
   will rebuild from the SQLite cache or re-scrape PubMed if `df` is
   missing from the runtime.

## Repository layout

```
ncbi-bioscraper/
├── README.md            # this file
├── LICENSE              # MIT
├── CHANGELOG.md         # version history
├── requirements.txt     # Python dependencies
├── .gitignore           # standard Python + Colab + data artifacts
└── notebooks/
    └── NCBI_BioScraper_Pro.ipynb
```

## Acknowledgements

This pipeline relies on free public services from NCBI/PMC, Crossref,
Unpaywall (OurResearch), Europe PMC (EMBL-EBI), Semantic Scholar (AI2),
and bioRxiv/medRxiv. Please respect each service's terms when you
configure email contact addresses and rate limits.

## License

MIT — see `LICENSE`.
