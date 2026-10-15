---
name: readable-coding
description: "Rules for writing code that a human will read and run locally, in R, Python and Linux shell (bash): R scripts / R Markdown / Quarto, Python scripts / Jupyter notebooks, shell commands and bash scripts. Short one-purpose chunks, existing package functions and standard tools instead of hand-rolled code, default output and plots, a short step / input / output comment at the top of every block, surfacing assumptions, surgical edits, and doing only what the user asked. R Markdown / Quarto deliverables are written as knitted HTML reports (numbered sections, floating TOC, results shown as tables, code folding with code shown by default). Use whenever delivering code, a notebook, a report or command lines to the user, especially for bioinformatics / machine learning analyses (preprocessing, PCA, clustering, Leiden, random forest, scikit-learn, mixOmics, multi-omics data preparation, command-line file processing). Style modelled on teaching code such as the ML4LS course (payamemami.com)."
---

# Readable coding · R / Python / Linux

## Scope

Apply these rules to **all code delivered to the user**:
- **R**: `.R` scripts, `.Rmd` / `.qmd` notebooks
- **Python**: `.py` scripts, Jupyter notebooks (`.ipynb`)
- **Linux**: command lines, bash scripts (`.sh`)

Code snippets pasted in chat follow the same rules.

Checks I run myself in the sandbox (assertions, comparisons with earlier results, numeric checks) are not bound by these rules, **but none of that checking code goes into the deliverable**. The one exception is reader-facing data checks in a rendered report (section 4.4): those belong in the report because the reader needs to see them.

**Core principle: the reader runs the code step by step in RStudio / Jupyter / a terminal.** For every step, ask: can the reader tell what this step does just from the function or command name?

---

## 1. Before coding: state assumptions
(adapted from karpathy-guidelines · Think Before Coding)

- When a request can be read in more than one way (e.g. "notebook" could mean .Rmd, .qmd or .ipynb), **ask first; don't silently pick one**.
- For parameters the user did not specify but I have to choose (thresholds, number of PCs, k, resolution, train/test split, threads), **say in the reply what I chose and why**, and how to change it.
- If a simpler approach exists, say so; if the user's plan has an obvious problem, point it out.

---

## 2. General rules (all three languages)

### 2.1 Do only what the user asked
- If the user says "check X, filter Y, run PCA", the deliverable contains exactly those three steps.
- Extra analyses I think are useful (more metrics, parameter grid scans, stability checks, extra output files) **stay out of the code**; suggest them in one sentence at the end of the reply.
- No branches or fallback code for "might need it later".

### 2.2 One step per block
- Each code block / cell / command is usually 1–8 lines: read, inspect, filter, fit, predict, evaluate, plot — one block each.
- Don't mix several ideas in one block.

### 2.3 Use existing tools; don't hand-write algorithms
- If a package or standard tool already does it, call it; don't re-implement it to "avoid an install".
- List the required packages or tools once at the top (install commands or an import block).

### 2.4 Let default output speak
- Show objects and results directly; don't build formatted strings or hand-assembled summary tables (unless asked).
- Exception: in a rendered report, a small `data.frame()` that lines up several numbers for the reader (dataset / rows / columns, check / value) is fine; see section 4.4.

### 2.5 Default plot style
- One plot per question; no label tweaking, custom palettes or multi-panel layouts unless asked or colouring by group is really needed.

### 2.6 A header comment on every block; few other comments
- **Every code block / cell / script section starts with a short header comment of three lines: what this step does, its input, its output.**
  ```r
  # Step: keep genes expressed in at least 3 samples
  # Input:  counts (gene x sample matrix)
  # Output: counts_filtered (same format, fewer genes)
  ```
  Name the actual objects or files and their format (e.g. `counts`, gene x sample matrix; `data/meta.csv`). If a step only displays something, write `Output: printed table` or `Output: plot`. Use `#` in R, Python and bash.
- Write these comments in the language the user writes in (Chinese user → Chinese comments), unless the user asks otherwise.
- In notebooks, the header comment goes inside the block; add one sentence of prose after the block on the result. In rendered reports, the prose goes before the block instead (section 4.3).
- Apart from the header, inline comments only for key numbers or parameter meanings.

### 2.7 Repetition is fine; avoid over-abstraction
- Don't define functions or classes used only once; use loops only for real parameter scans or batch file processing, and keep loop bodies short.

### 2.8 Run top to bottom; parameters at the top
- Runs from top to bottom with no hidden state.
- Fix the seed for any random step.
- Paths and parameters the user may change go in one block at the top (data dir, output dir, `k`, `resolution`); all output is saved to the output dir. In R Markdown / Quarto reports, put them in the YAML `params:` field and use `params$seed` etc. in the code.

### 2.9 Plain names
- Use names like `expr`, `pca`, `keep`, `cluster`, `train`, `test`, `input_dir`; avoid cryptic abbreviations (e.g. `zf`, `cm`, `md`).

### 2.10 When editing existing code, change only what must change
(adapted from karpathy-guidelines · Surgical Changes)
- If the user says "save the results to out_dir", change only the path-related lines; don't reorder, rename, add comments or "improve" anything else.
- Keep the existing style, even if I would write it differently.
- Remove only code that my change made redundant; mention other unrelated dead code in the reply instead of deleting it.
- Test: **every changed line traces directly to the user's request.**

### 2.11 Simplicity test
(adapted from karpathy-guidelines · Simplicity First)
- Don't handle impossible cases; don't add configurability or generality nobody asked for.
- After writing, ask: **"Would an experienced user find this too complicated?"** If 100 lines could be 40, rewrite it.

---

## 3. Language-specific notes

### R
- Use `package::function()` to show where a function comes from (e.g. `bluster::makeSNNGraph()`, `caret::confusionMatrix()`, `randomForest::tuneRF()`); use `library()` only for packages called repeatedly.
- **Don't restrict yourself to base R to avoid installs**; put an `install.packages()` / `BiocManager::install()` block at the top.
- Output: `print(model)`, `head()`, `dim()`, `table()`, `summary()`; not `cat(sprintf(...))`.
- Plots: `plot()`, `boxplot()`, `hist()`, `barplot()`, `varImpPlot()`; no `png()` / `dev.off()` in the deliverable unless the user wants images saved.
- Randomness: `set.seed()`.
- In Rmd, no `setwd()`; use `file.path(data_dir, ...)`.

### Python
- Prefer ready-made pandas / numpy / scikit-learn / scipy functions, e.g. `train_test_split`, `RandomForestClassifier`, `classification_report`, `PCA`, `StandardScaler`, `silhouette_score`.
- Output: in notebooks, let the last line of a cell show the result (`df.head()`, `df.shape`, `model`); in scripts, a plain `print(df.head())`, no elaborate f-string reports.
- Plots: default `df.plot()`, `plt.scatter()`, `plt.hist()`; no styling, no complex subplot layouts.
- Randomness: pass `random_state=42` (scikit-learn) or use `np.random.seed()`.
- No unrequested `argparse`, type hints, `logging`, `class` or `if __name__ == "__main__":`; write simple scripts top to bottom.
- No broad `try/except` that swallows errors; let it fail loudly.
- Paths: `pathlib.Path` or plain strings, defined once at the top.

### Linux (command line / bash)
- **One command per line, one job per command.** Pipes usually have at most 3 stages; split longer ones into steps and check intermediate results with intermediate files or `head`.
- Prefer standard tools: `ls`, `head`, `wc -l`, `cut`, `sort`, `uniq -c`, `grep`, `awk`, `sed`, `find`; no complex awk programs or one-liner tricks in place of simple commands.
- Readability first: use long options where helpful (e.g. `sort --numeric-sort`, `grep --count`) so the reader understands the flags.
- Define variables at the top of a script (e.g. `INPUT_DIR=...`, `OUTPUT_DIR=...`, `THREADS=4`) and always quote them: `"$INPUT_DIR"`.
- `set -e` (stop on error) is fine, but no piles of defensive checks like `if [ ! -f ... ]` unless asked.
- No single-use functions; batch over files with a simple `for f in *.tsv; do ...; done`.
- Commands that delete or overwrite files (`rm`, `mv`, `>`) must be flagged clearly in the prose.

---

## 4. Rendered HTML reports (R Markdown / Quarto)

When the deliverable is an `.Rmd` or `.qmd`, write it as a report that knits to HTML: the reader sees the code, what each step did and the result right under it, and can fold the code away. **Code is shown by default** (`code_folding: show`); the reader hides it with the per-chunk buttons or the "Code → Hide All Code" menu.

### 4.1 YAML header

R Markdown:
```yaml
---
title: "TCGA BRCA multi-omics: data preparation and train/test split"
subtitle: "mRNA, DNA methylation and protein with clinical data"
author: "Name"
date: "`r Sys.Date()`"
output:
  html_document:
    toc: true
    toc_float: true
    number_sections: true
    code_folding: show
    df_print: kable
params:
  seed: 123
  train_prop: 0.7
  data_dir: "."
---
```
- `code_folding: show` is the default; use `hide` only if the user asks for code hidden at first.
- `df_print: kable` prints every data frame as a clean HTML table.
- Every value the user might change (seed, split ratio, thresholds, paths) goes in `params:`.

Quarto equivalent (also works for Python code and `.ipynb` rendered with `quarto render`):
```yaml
format:
  html:
    toc: true
    toc-location: left
    number-sections: true
    code-fold: show
    code-tools: true
    df-print: kable
```

### 4.2 Report structure
1. **Hidden setup chunk** (`{r setup, include=FALSE}`): `library()` calls and small helpers used many times, such as a sample-flow tracker. Loading messages stay out of the report.
2. **Overview**: what data, a numbered list of the steps, and the general principle that holds throughout (e.g. same patients in the same order in every dataset; split at patient level; anything data-driven is learned on the training set only).
3. **One numbered section per step** (`# Sample selection`, `## Primary tumours with all three omics`), each with prose → chunk → result.
4. **Summary**: the sample-flow table and one line on the recommended next step.
5. **Session information**: `sessionInfo()` as the last chunk.

### 4.3 Prose around each chunk
- Before the chunk, 1–3 plain sentences: what this step does and **why** (e.g. "Male breast cancer is rare and biologically different, so only female patients are kept.").
- Explain messages that look like errors but are not (e.g. "harmonizing input: removing … sampleMap rows is not an error").
- Flag slow or memory-heavy steps before they run.
- When a step depends on a mapping or a choice, show it as a small markdown table in the prose (e.g. original stage → outcome category).
- After the result, a sentence only when the result needs interpreting (e.g. "No duplicated patients were found.").
- The three-line step / input / output header (2.6) still goes inside each chunk.

### 4.4 Show results the reader can check
- Inspect before you filter: print `table(x, useNA = "ifany")` in its own chunk, then filter in the next chunk.
- Summarise several objects in one small table rather than several prints:
  ```r
  data.frame(dataset = c("mRNA", "Protein", "Clinical"),
             rows    = c(nrow(rna), nrow(prot), nrow(clin)),
             columns = c(ncol(rna), ncol(prot), ncol(clin)))
  ```
- Data-integrity checks the reader should see (sample order matches across datasets, no train/test overlap) are shown as a `check` / `value` table of TRUE/FALSE, not as hidden `stopifnot()`.
- Track the number of samples through every filter with a flow table, printed in the Summary:
  ```r
  # in the setup chunk
  flow <- data.frame(step = character(), samples = integer())
  add_step <- function(flow, step, n) rbind(flow, data.frame(step = step, samples = n))

  # after each filter
  flow <- add_step(flow, "Female", nrow(clin))
  ```
- For big objects show `head()`, `dim()` or a `dry.run`-style listing, never the whole object.
- When the report saves files, end that section with a table of file name / rows / columns.

---

## 5. Verification happens on my side
(adapted from karpathy-guidelines · Goal-Driven Execution)

Decide what "done" means before starting and verify it in the sandbox; verification code stays out of the deliverable (reader-facing checks in reports, 4.4, are the exception). For example:
1. Filter low-expressed genes → verify: gene count matches expectation and the removed genes are all low-expressed.
2. Rewrite an Rmd / notebook → verify: it knits or runs from scratch without errors and numbers match the previous version.
3. Switch to a package function → verify: compare with the old implementation (e.g. ARI) and tell the user about any difference.
4. Shell script → verify: run it on test data and check the line counts and contents of the output files.
5. Rendered report → verify: it knits to HTML, the code is visible by default, and the tables appear under their chunks.

Simple tasks don't need a verification list every time.

---

## 6. Pre-delivery checklist

- [ ] Was every step asked for? Extras removed and turned into a one-line suggestion?
- [ ] Are all parameters and assumptions I chose stated in the reply?
- [ ] When editing existing code: did I change only what was asked?
- [ ] Any hand-written code that a package or standard tool already provides?
- [ ] Any block longer than ~10 lines, or pipe longer than 3 stages? Can it be split?
- [ ] Is all verification code (assertions, comparisons) removed, apart from reader-facing check tables in reports?
- [ ] Does every block start with the three-line header (step / input / output)?
- [ ] Are plots default style only?
- [ ] Do paths and parameters need changing in one place only, at the top (or in `params:`)?
- [ ] Rmd / qmd: numbered sections, floating TOC, `code_folding: show`, `df_print: kable`, hidden setup chunk, prose before each chunk, sample-flow table, `sessionInfo()` at the end?
- [ ] Have I run it end to end in the sandbox without errors (done on my side, not in the deliverable)?

---

## 7. Examples

### R · Leiden clustering

Don't write this (hand-built SNN graph):
```r
d  <- as.matrix(dist(pcs))
nn <- t(apply(d, 1, function(x) order(x)[1:(k + 1)]))
A  <- matrix(0, nrow(pcs), nrow(pcs))
for (i in 1:nrow(pcs)) A[i, nn[i, ]] <- 1
shared <- A %*% t(A)
snn <- shared / (2 * (k + 1) - shared)
snn[snn < 1/15] <- 0; diag(snn) <- 0
g <- igraph::graph_from_adjacency_matrix(snn, mode = "undirected", weighted = TRUE)
```

Write this:
```r
# Step: build a shared nearest-neighbour graph from the first 10 PCs
# Input:  pca (prcomp object)
# Output: g (igraph graph)
pcs <- pca$x[, 1:10]
g <- bluster::makeSNNGraph(pcs, k = 10, type = "jaccard")
```
```r
# Step: Leiden clustering on the graph
# Input:  g (igraph graph)
# Output: leiden (clustering result); printed cluster sizes
set.seed(42)
leiden <- igraph::cluster_leiden(g, objective_function = "modularity", resolution = 0.5)
table(igraph::membership(leiden))
```

### R Markdown · one report section

````markdown
## Female patients

Male breast cancer is rare and biologically different, so only female patients are kept.

```{r}
# Step: count patients by gender
# Input:  clin (patients x clinical variables)
# Output: printed table
table(clin$gender, useNA = "ifany")
```

```{r}
# Step: keep female patients and record the count
# Input:  clin
# Output: clin (female patients only), flow (one more row)
clin <- clin[which(clin$gender %in% "female"), ]
flow <- add_step(flow, "Female", nrow(clin))
```
````

### Python · Random forest

Don't write this:
```python
class RFPipeline:
    def __init__(self, n_trees=100, seed=42, verbose=True): ...
    def fit(self, X, y):
        try:
            ...
        except Exception as e:
            logging.warning(f"fit failed: {e}")
```

Write this:
```python
# Step: split samples into training and test sets
# Input:  X (feature table), y (labels)
# Output: X_train, X_test, y_train, y_test
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)
```
```python
# Step: fit a random forest
# Input:  X_train, y_train
# Output: model (fitted RandomForestClassifier)
model = RandomForestClassifier(n_estimators=100, random_state=42)
model.fit(X_train, y_train)
```
```python
# Step: evaluate on the test set
# Input:  model, X_test, y_test
# Output: printed classification report
print(classification_report(y_test, model.predict(X_test)))
```

### Linux · Row count per sample

Don't write this:
```bash
find data -name "*.tsv" -print0 | xargs -0 -I{} sh -c 'echo "$(basename {} .tsv)\t$(tail -n +2 {} | wc -l)"' | sort -k2,2nr
```

Write this:
```bash
INPUT_DIR="data"

# Step: count data rows in each sample file
# Input:  data/*.tsv (one file per sample, with a header line)
# Output: printed file name and row count
for f in "$INPUT_DIR"/*.tsv; do
    echo "$f"
    tail -n +2 "$f" | wc -l      # number of rows without the header
done
```

---
Some principles are adapted from forrestchang/andrej-karpathy-skills (karpathy-guidelines, MIT License).