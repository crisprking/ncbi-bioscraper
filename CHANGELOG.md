# Changelog

## [2.0.0] - 2026

### Added
- Citation enrichment via OpenAlex (cited_by_count, FWCI, reference list, concepts, retraction flag)
- Open Access detection via Unpaywall (free PDF URL, OA status, license, version)
- Study type classifier: RCT, systematic review, meta-analysis, cohort, in-vitro, case report, preprint
- Drug / disease / gene entity extraction from MeSH (no GPU required)
- Semantic deduplication with RapidFuzz (token_set_ratio >= 92%)
- Trend analysis: emerging vs fading keyword terms year-over-year
- PRISMA-style flow diagram
- Resumable runs via SQLite checkpoint cache
- RIS export (Zotero / EndNote)
- Parquet export (pandas, DuckDB, Polars)
- Self-contained HTML report with all charts base64-embedded
- Interactive PyVis co-author network
- MeSH concept co-occurrence network

### Fixed
- Year parsing bug (was returning '20' instead of the full 4-digit year)
- DOI extraction strips [pii] artifacts from LID/AID fields
- ISSN cleanup removes (Electronic)/(Linking) annotation tags

## [1.0.0] - PREV_2026

### Added
- Initial release: PubMed, Gene, Nucleotide, GEO, SRA scrapers
- TF-IDF keywords, LDA topic modeling, word cloud
- Co-author network graph
- CSV, Excel, JSON, BibTeX export
- Google Drive auto-save
