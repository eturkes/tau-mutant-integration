# Repository Guidelines

## Project Structure & Module Organization
The R notebooks in `R/` drive day-to-day work: `integration.Rmd` produces the primary report, `testing.Rmd` captures regression checks, and `utils.R` holds shared helpers. The Rcpp kernel lives in `src/calculateScores.cpp`; rebuild it with `Rcpp::sourceCpp` before calling `genefunnel()`. Place inputs in `data/`, keep heavyweight archives in git-ignored `storage/`, write knitted artefacts to `results/`, and allow notebooks to populate transient caches in `cache/`.

## Build, Test, and Development Commands
Regenerate the integration report with `Rscript -e "rmarkdown::render('R/integration.Rmd', output_dir='results')"`, and rerun QA with `Rscript -e "rmarkdown::render('R/testing.Rmd', output_file='results/testing.html')"` before every PR. After editing C++, call `Rscript -e "Rcpp::sourceCpp('src/calculateScores.cpp')"` in the current R session to rebuild the shared object. Use `docker compose up rstudio` (set `PORT`) for the curated rocker environment, or `docker compose run --rm all` to execute the bundled batch pipeline.

## Coding Style & Naming Conventions
Follow tidyverse style: two-space indents, snake_case objects, explicit library calls, and chunk labels that mirror the step being executed. Keep roxygen headers in `R/utils.R`, and align figure outputs with their narrative sections. In C++, match the existing style—brace-on-same-line, minimal scope variables, and exports annotated with `// [[Rcpp::export]]`. Prefer lower_snake_case filenames for data, caches, and derived artefacts.

## Testing Guidelines
Render `R/testing.Rmd` after any change touching analysis logic or inputs; it writes `results/testing.html` for review. Ensure required caches exist via deterministic filenames in `cache/` (never commit the directory), and create them in setup chunks when needed. Prefix validation chunks with `test_` and guard expectations with `stopifnot()` so failures surface in the HTML. When the Rcpp layer changes, include comparison chunks that summarise score deltas or runtime.

## Commit & Pull Request Guidelines
Keep commits small and use imperative summaries (e.g., `Refine score overlap`), noting data or cache impacts in the body when relevant. PRs should state intent, list the commands run, link refreshed artefacts in `results/`, and attach visuals when plots change. Flag new dependencies, update the Dockerfile when versions shift, and keep large binaries in `storage/` rather than the tracked tree.

## Environment & Data Notes
Match package versions to the Dockerfile (Seurat, GSVA, scuttle, edgeR) and describe deviations in PR notes. Keep raw cohort files inside `storage/data`; redact identifiers before sharing derived artefacts. `.env` is ignored—document required secrets out of band and never commit credentials.
