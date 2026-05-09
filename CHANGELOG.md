# Changelog

## v3.5.9 — 2026-05-09
- Publish cell hardened: actual notebook auto-detection (the previous
  release defined `_find_or_build_notebook` but never called it,
  causing `Path(None)` TypeError).
- Token now flows through a per-process credential helper instead
  of being embedded in the clone URL.
- All git invocations now check return codes and redact tokens
  (including PAT-shaped strings) from any error output.
- No global `os.chdir`; working directory is restored on exit.

## v3.5.8 — 2026-05-09
- Canonical metrics block: `df.attrs['coverage_metrics']` exposes
  `any_oa_link_pct`, `full_text_url_pct`, `extracted_text_pct`,
  `total_extracted_chars` for programmatic use.
- Honest README: numbers come from actual runs, not aspirational targets.

All notable changes to this project will be documented in this file.

## [3.5.1] — 2026-05-08

### Fixed
- **Crash on empty PDF extraction.** `int(NaN)` was being called when zero PDFs yielded text (because every download hit a silent failure). Now guarded by `if n_extracted > 0`.
- **Silent PDF download failures.** PDF extraction was completing in suspicious ~80ms per file, indicating downloads were bailing before any bytes were transferred. Two fixes:
  - Route PDF downloads through the same retry-enabled `requests.Session` used by the resolver chain. Connection pooling + automatic retries on 429/5xx are essential — PMC's HTTPS endpoint rate-limits aggressive fetchers.
  - Accept `application/octet-stream` content-type (PMC's FTP-over-HTTPS endpoint sometimes returns this) in addition to `application/pdf`.
  - Added 150ms politeness delay between PDF requests.

### Added
- **Diagnostic counters in PDF extraction.** Every download outcome is now categorized (`http_403`, `not_pdf_content_type`, `too_large`, `pdf_parse_error`, `timeout`, `ok`, etc.). When zero PDFs extract, the breakdown prints automatically with troubleshooting tips. No more black-box failures.

### Notes
- A real `NCBI_EMAIL` lifts full-text yield significantly. Unpaywall and CrossRef are auto-skipped on placeholder emails — without them, you're relying on PMC + Europe PMC + OA Button + preprint sources, which top out around 60-65% on broad PubMed sets. With Unpaywall enabled, expect 75-80%.

## [3.5.0] — 2026-05-07

### Changed (vs the v3.5.2 MAX file that this consolidates)
- Single, definitive notebook. The previous file contained ~8 stacked rewrites of Cell 7 (v3, v3.1, v3.3, v3.4, v3.5.2 MAX), totaling 32,308 lines. The consolidated version is 1,708 code lines.
- OA waterfall expanded to 7 providers in priority order: PMC → Europe PMC → Unpaywall → CrossRef → OA Button → bioRxiv direct → bioRxiv title-search.
- Europe PMC moved to position 2 (was lower) — it returns full-text URLs directly, better for downstream PDF extraction than landing-page redirects.
- Email validation auto-skips Unpaywall and CrossRef on placeholder emails (no more 50 consecutive 422s).
- Dashboard panel #6 now shows OA-source breakdown (which provider resolved each record) instead of a generic `is_oa` pie.
- Removed embedded GitHub-publish cells (better as a separate operation, not in a research notebook).

### Preserved from v2.0
- 4-digit year parsing
- DOI cleanup (strips `[pii]` artifacts)
- ISSN cleanup (strips `(Electronic)`/`(Linking)` tags)
- All export formats (CSV, XLSX, JSON, Parquet, BibTeX, RIS)
- SQLite cache for resumable runs
- PRISMA flow diagram, PyVis networks, self-contained HTML report

## [2.0.0] — 2026-05-07

Initial public release.
