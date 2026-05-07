# Contributing to NCBI BioScraper Pro

Thank you for your interest! 🎉

## Report a bug
Open an issue with: your NCBI query, the failing cell number,
the full traceback, and Colab runtime info (Runtime > View runtime information).

## Request a feature
Open an issue tagged `enhancement`. Describe the research task it enables.

## Submit a pull request
1. Fork the repo
2. `git checkout -b feature/my-feature`
3. Test end-to-end in Colab with `MAX_RESULTS = 10`
4. Never commit real tokens or API keys
5. Open a PR describing what changed and why

## Code conventions
- PEP 8, docstrings on public methods
- Each cell must be self-contained and idempotent
- Cache every remote API call
- Clear outputs before committing (Cell 18 does this automatically)

## Questions?
Open a Discussion — Issues are for bugs and concrete feature requests.
