# Codebase Issue Review: Proposed Tasks

## 1) Typo fix task
**Task:** Correct spelling/grammar mistakes in comments and usage text.

- In `gwas.R`, fix comment typos:
  - `necesarry` -> `necessary`
  - `formated` -> `formatted`
- In `convert_for_rrblup.pl`, fix the usage string typo where `"n$0 ..."` should be `"\n$0 ..."`.

**Why this matters:** Reduces confusion for learners using this exercise and makes CLI/help output readable.

---

## 2) Bug fix task
**Task:** Fix `debug_mode` handling in `gwas.R` so it correctly respects command-line input.

- Current code parses `debug_mode <- args[7]` (string), but compares using `if(debug_mode == TRUE)`, which never becomes `TRUE` for the string `"TRUE"`.
- Convert to logical explicitly (e.g., `debug_mode <- tolower(args[7]) == "true"`).

**Why this matters:** The script currently ignores the debug mode flag and always reads the full genotype file, which can cause long runs or memory pressure.

---

## 3) Documentation/comment discrepancy task
**Task:** Align comments with actual behavior in `gwas.R` around row sampling.

- There is a commented line indicating genotype row sampling (`#genotypes <- genotypes[sample(nrow(genotypes), nrow(genotypes)/2),]`) but active logic samples **columns/lines** when `n_lines < 503`.
- Update comments to clearly state that current thinning logic samples genotype **lines (columns)**, not markers (rows), and under what condition.

**Why this matters:** The current comments can mislead students about what data is being subsampled and impact interpretation of GWAS results.

---

## 4) Test improvement task
**Task:** Add a lightweight regression test for `convert_for_rrblup.pl` conversion behavior.

- Create a small fixture CSV and expected output.
- Add a shell test script (or `prove`-style Perl test) that runs:
  - `perl convert_for_rrblup.pl -i <fixture> -o <tmp> -f <start> -l <stop>`
  - compares generated output to expected output.
- Include one `NA` mapping case and one heterozygous/homozygous mapping case.

**Why this matters:** This script performs deterministic recoding and is a great candidate for fast regression tests to prevent silent data-format breakage.
