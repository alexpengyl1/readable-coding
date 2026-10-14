---
name: readable-coding
description: "Rules for writing code that a human will read and run locally, in R, Python and Linux shell (bash): R scripts / R Markdown / Quarto, Python scripts / Jupyter notebooks, shell commands and bash scripts. Short one-purpose chunks, existing package functions and standard tools instead of hand-rolled code, default output and plots, a short step / input / output comment at the top of every block, surfacing assumptions, surgical edits, and doing only what the user asked. Use whenever delivering code, a notebook or command lines to the user, especially for bioinformatics / machine learning analyses (preprocessing, PCA, clustering, Leiden, random forest, scikit-learn, mixOmics, command-line file processing). Style modelled on teaching code such as the ML4LS course (payamemami.com)."
---

# Readable coding · R / Python / Linux（写给人读的代码）

Each rule is given in English first, then in Chinese. The two versions say the same thing.
每条规则先写英文，再写中文，两者内容相同。

## Scope / 适用范围

Apply these rules to **all code delivered to the user**:
**交给用户的代码**都按本规则写：
- **R**: `.R` scripts, `.Rmd` / `.qmd` notebooks / `.R` 脚本、`.Rmd` / `.qmd` notebook
- **Python**: `.py` scripts, Jupyter notebooks (`.ipynb`) / `.py` 脚本、Jupyter notebook（`.ipynb`）
- **Linux**: command lines, bash scripts (`.sh`) / 命令行、bash 脚本（`.sh`）

Code snippets pasted in chat follow the same rules.
聊天中贴出的代码片段也一样。

Checks I run myself in the sandbox (assertions, comparisons with earlier results, numeric checks) are not bound by these rules, **but none of that checking code goes into the deliverable**.
我自己在沙盒中做的验证（断言、与旧结果比对、数值检查）不受本规则限制，**但这些验证代码一律不放进交付物**。

**Core principle: the reader runs the code step by step in RStudio / Jupyter / a terminal.** For every step, ask: can the reader tell what this step does just from the function or command name?
**核心原则：读者是在 RStudio / Jupyter / 终端里一步一步运行的人。** 每一步都先问：读者看函数名或命令名，能不能马上知道这一步在做什么？

---

## 1. Before coding: state assumptions / 动手之前：先说出假设
(adapted from karpathy-guidelines · Think Before Coding / 借鉴 karpathy-guidelines · Think Before Coding)

- When a request can be read in more than one way (e.g. "notebook" could mean .Rmd, .qmd or .ipynb), **ask first; don't silently pick one**.
  需求有多种理解时（例如"notebook"可以指 .Rmd、.qmd 或 .ipynb），**先问，不要自己默默选一个**。
- For parameters the user did not specify but I have to choose (thresholds, number of PCs, k, resolution, train/test split, threads), **say in the reply what I chose and why**, and how to change it.
  用户没指定、而我必须选的参数（阈值、PC 个数、k、resolution、训练/测试比例、线程数），**在回复中明确写出选了什么、为什么**，并说明怎么改。
- If a simpler approach exists, say so; if the user's plan has an obvious problem, point it out.
  如果有更简单的做法，直接说出来；用户的方案明显有问题时，指出问题。

---

## 2. General rules (all three languages) / 通用规则（三种语言都适用）

### 2.1 Do only what the user asked / 只做用户说的
- If the user says "check X, filter Y, run PCA", the deliverable contains exactly those three steps.
  用户说"检查 X、过滤 Y、做 PCA"，交付物里就只有这三步。
- Extra analyses I think are useful (more metrics, parameter grid scans, stability checks, extra output files) **stay out of the code**; suggest them in one sentence at the end of the reply.
  我认为有用的额外分析（多种指标、参数网格扫描、稳定性检验、额外输出文件），**不写进代码**，在回复末尾用一句话作为建议提出。
- No branches or fallback code for "might need it later".
  不为"以后可能用到"写分支代码或备用代码。

### 2.2 One step per block / 一步一块
- Each code block / cell / command is usually 1–8 lines: read, inspect, filter, fit, predict, evaluate, plot — one block each.
  每个代码块 / cell / 命令通常只有 1–8 行：读取、检查、过滤、拟合、预测、评估、画图，各占一块。
- Don't mix several ideas in one block.
  不要在一块里混入多个概念。

### 2.3 Use existing tools; don't hand-write algorithms / 用现成工具，不手写算法
- If a package or standard tool already does it, call it; don't re-implement it to "avoid an install".
  包或标准工具里已有的功能直接调用，不要为了"免安装"重新实现。
- List the required packages or tools once at the top (install commands or an import block).
  需要的包或工具在开头列一次（安装命令或 import 块）。

### 2.4 Let default output speak / 让默认输出说话
- Show objects and results directly; don't build formatted strings or hand-assembled summary tables (unless asked).
  直接显示对象或结果，不拼接格式化字符串、不自己组装汇总表（除非用户要求）。

### 2.5 Default plot style / 图用默认样式
- One plot per question; no label tweaking, custom palettes or multi-panel layouts unless asked or colouring by group is really needed.
  一张图对应一个问题；不调标签位置、不配色板、不拼多面板，除非用户要求或确实需要按组上色。

### 2.6 A header comment on every block; few other comments / 每段代码开头写说明注释，其他注释从简
- **Every code block / cell / script section starts with a short header comment of three lines: what this step does, its input, its output.**
  **每个代码块 / cell / 脚本段落开头都写三行说明注释：这一步做什么、输入是什么、输出是什么。**
  ```r
  # Step: keep genes expressed in at least 3 samples
  # Input:  counts (gene x sample matrix)
  # Output: counts_filtered (same format, fewer genes)
  ```
  Name the actual objects or files and their format (e.g. `counts`, gene x sample matrix; `data/meta.csv`). If a step only displays something, write `Output: printed table` or `Output: plot`. Use `#` in R, Python and bash.
  写清楚具体的对象或文件名和格式（如 `counts`，基因 × 样本矩阵；`data/meta.csv`）。如果这一步只是显示结果，写 `Output: printed table` 或 `Output: plot`。R、Python、bash 都用 `#`。
- Write these comments in the language the user writes in (Chinese user → Chinese comments), unless the user asks otherwise.
  注释语言跟随用户使用的语言（用户用中文就写中文注释），除非用户另有要求。
- In notebooks, the header comment goes inside the block; add one sentence of prose after the block on the result.
  notebook 中，说明注释写在代码块内部开头；代码块后用一句话写结论。
- Apart from the header, inline comments only for key numbers or parameter meanings.
  除开头的说明注释外，行内注释只标关键数字或参数含义。

### 2.7 Repetition is fine; avoid over-abstraction / 允许重复，避免过度抽象
- Don't define functions or classes used only once; use loops only for real parameter scans or batch file processing, and keep loop bodies short.
  不定义只用一次的函数或类；循环只在确实需要扫描参数或批量处理文件时使用，循环体保持简短。

### 2.8 Run top to bottom; parameters at the top / 顺序执行，参数集中放在最前面
- Runs from top to bottom with no hidden state.
  从上到下运行即可，不依赖隐藏状态。
- Fix the seed for any random step.
  有随机性的步骤固定种子。
- Paths and parameters the user may change go in one block at the top (data dir, output dir, `k`, `resolution`); all output is saved to the output dir.
  用户需要修改的路径和参数放在最前面一个块里（如数据目录、输出目录、`k`、`resolution`），输出统一保存到输出目录。

### 2.9 Plain names / 名字直白
- Use names like `expr`, `pca`, `keep`, `cluster`, `train`, `test`, `input_dir`; avoid cryptic abbreviations (e.g. `zf`, `cm`, `md`).
  用 `expr`、`pca`、`keep`、`cluster`、`train`、`test`、`input_dir` 这类名字，不用难懂的缩写（如 `zf`、`cm`、`md`）。

### 2.10 When editing existing code, change only what must change / 修改已有代码时，只改必须改的
(adapted from karpathy-guidelines · Surgical Changes / 借鉴 karpathy-guidelines · Surgical Changes)
- If the user says "save the results to out_dir", change only the path-related lines; don't reorder, rename, add comments or "improve" anything else.
  用户说"把结果保存到 out_dir"，就只改路径相关的那几行，不要顺手重排、改名、补注释或"优化"其他部分。
- Keep the existing style, even if I would write it differently.
  沿用已有的写法和风格，即使我会换一种写法。
- Remove only code that my change made redundant; mention other unrelated dead code in the reply instead of deleting it.
  只删除因我这次修改而变得多余的代码；发现其他无关的多余代码，在回复里提一句，不要直接删。
- Test: **every changed line traces directly to the user's request.**
  检验标准：**每一行改动都能直接对应到用户的要求。**

### 2.11 Simplicity test / 简单性测试
(adapted from karpathy-guidelines · Simplicity First / 借鉴 karpathy-guidelines · Simplicity First)
- Don't handle impossible cases; don't add configurability or generality nobody asked for.
  不处理不可能发生的情况，不加用户没要求的可配置项或通用性。
- After writing, ask: **"Would an experienced user find this too complicated?"** If 100 lines could be 40, rewrite it.
  写完问自己：**"一个有经验的用户会不会觉得这太复杂？"** 如果 100 行能写成 40 行，就重写。

---

## 3. Language-specific notes / 按语言补充

### R
- Use `package::function()` to show where a function comes from (e.g. `bluster::makeSNNGraph()`, `caret::confusionMatrix()`, `randomForest::tuneRF()`); use `library()` only for packages called repeatedly.
  用 `包名::函数名` 标明出处（如 `bluster::makeSNNGraph()`、`caret::confusionMatrix()`、`randomForest::tuneRF()`）；`library()` 只用于反复调用的包。
- **Don't restrict yourself to base R to avoid installs**; put an `install.packages()` / `BiocManager::install()` block at the top.
  **不要为了免安装把自己限制在 base R**；开头写一个 `install.packages()` / `BiocManager::install()` 块。
- Output: `print(model)`, `head()`, `dim()`, `table()`, `summary()`; not `cat(sprintf(...))`.
  输出：`print(model)`、`head()`、`dim()`、`table()`、`summary()`；不用 `cat(sprintf(...))`。
- Plots: `plot()`, `boxplot()`, `hist()`, `barplot()`, `varImpPlot()`; no `png()` / `dev.off()` in the deliverable unless the user wants images saved.
  作图：`plot()`、`boxplot()`、`hist()`、`barplot()`、`varImpPlot()`；交付物里不写 `png()` / `dev.off()`，除非用户要求保存图片。
- Randomness: `set.seed()`.
  随机性：`set.seed()`。
- In Rmd, no `setwd()`; use `file.path(data_dir, ...)`.
  Rmd 中不用 `setwd()`，用 `file.path(data_dir, ...)`。

### Python
- Prefer ready-made pandas / numpy / scikit-learn / scipy functions, e.g. `train_test_split`, `RandomForestClassifier`, `classification_report`, `PCA`, `StandardScaler`, `silhouette_score`.
  优先用 pandas / numpy / scikit-learn / scipy 的现成函数，例如 `train_test_split`、`RandomForestClassifier`、`classification_report`、`PCA`、`StandardScaler`、`silhouette_score`。
- Output: in notebooks, let the last line of a cell show the result (`df.head()`, `df.shape`, `model`); in scripts, a plain `print(df.head())`, no elaborate f-string reports.
  输出：notebook 中让 cell 的最后一行直接显示结果（`df.head()`、`df.shape`、`model`）；脚本中用简单的 `print(df.head())`，不用复杂的 f-string 格式化报告。
- Plots: default `df.plot()`, `plt.scatter()`, `plt.hist()`; no styling, no complex subplot layouts.
  作图：`df.plot()`、`plt.scatter()`、`plt.hist()` 这类默认图即可；不调样式、不写复杂的 subplot 布局。
- Randomness: pass `random_state=42` (scikit-learn) or use `np.random.seed()`.
  随机性：传 `random_state=42`（scikit-learn）或 `np.random.seed()`。
- No unrequested `argparse`, type hints, `logging`, `class` or `if __name__ == "__main__":`; write simple scripts top to bottom.
  不加用户没要求的 `argparse`、类型注解、`logging`、`class`、`if __name__ == "__main__":` 结构；简单脚本按顺序写即可。
- No broad `try/except` that swallows errors; let it fail loudly.
  不写宽泛的 `try/except` 来吞掉错误；出错就让它报错。
- Paths: `pathlib.Path` or plain strings, defined once at the top.
  路径用 `pathlib.Path` 或简单字符串，在开头定义一次。

### Linux (command line / bash) / Linux（命令行 / bash）
- **One command per line, one job per command.** Pipes usually have at most 3 stages; split longer ones into steps and check intermediate results with intermediate files or `head`.
  **一行一个命令，一个命令做一件事。** 管道一般不超过 3 段；更长就拆成几步，并用中间文件或 `head` 查看中间结果。
- Prefer standard tools: `ls`, `head`, `wc -l`, `cut`, `sort`, `uniq -c`, `grep`, `awk`, `sed`, `find`; no complex awk programs or one-liner tricks in place of simple commands.
  优先用标准工具：`ls`、`head`、`wc -l`、`cut`、`sort`、`uniq -c`、`grep`、`awk`、`sed`、`find`；不要写复杂的 awk 程序或一行式技巧代替简单命令。
- Readability first: use long options where helpful (e.g. `sort --numeric-sort`, `grep --count`) so the reader understands the flags.
  可读性优先：必要时用长参数（如 `sort --numeric-sort`、`grep --count`），让读者看懂选项含义。
- Define variables at the top of a script (e.g. `INPUT_DIR=...`, `OUTPUT_DIR=...`, `THREADS=4`) and always quote them: `"$INPUT_DIR"`.
  脚本开头集中定义变量（如 `INPUT_DIR=...`、`OUTPUT_DIR=...`、`THREADS=4`），变量一律加双引号：`"$INPUT_DIR"`。
- `set -e` (stop on error) is fine, but no piles of defensive checks like `if [ ! -f ... ]` unless asked.
  脚本中可以写 `set -e`（出错即停），但不要写大量 `if [ ! -f ... ]` 之类的防御性检查，除非用户要求。
- No single-use functions; batch over files with a simple `for f in *.tsv; do ...; done`.
  不定义只用一次的函数；批量处理文件用简单的 `for f in *.tsv; do ...; done`。
- Commands that delete or overwrite files (`rm`, `mv`, `>`) must be flagged clearly in the prose.
  会删除或覆盖文件的命令（`rm`、`mv`、`>`）要在正文里明确提醒。

---

## 4. Verification happens on my side / 验证在我这边完成
(adapted from karpathy-guidelines · Goal-Driven Execution / 借鉴 karpathy-guidelines · Goal-Driven Execution)

Decide what "done" means before starting and verify it in the sandbox; verification code stays out of the deliverable. For example:
动手前先确定"怎样算完成"，并在沙盒中验证；验证代码不进入交付物。例如：
1. Filter low-expressed genes → verify: gene count matches expectation and the removed genes are all low-expressed.
   过滤低表达基因 → 验证：基因数与预期一致，被删的都是低表达基因
2. Rewrite an Rmd / notebook → verify: it knits or runs from scratch without errors and numbers match the previous version.
   改写 Rmd / notebook → 验证：完整 Knit 或从头运行无报错，结果与上一版数值一致
3. Switch to a package function → verify: compare with the old implementation (e.g. ARI) and tell the user about any difference.
   换用包函数 → 验证：与旧实现比较（如 ARI），有差异就告诉用户
4. Shell script → verify: run it on test data and check the line counts and contents of the output files.
   shell 脚本 → 验证：在测试数据上运行，检查输出文件行数和内容

Simple tasks don't need a verification list every time.
简单任务可以灵活处理，不必每次都列出验证清单。

---

## 5. Pre-delivery checklist / 交付前自查清单

- [ ] Was every step asked for? Extras removed and turned into a one-line suggestion? / 每一步都是用户要求的吗？多余的已删掉并改为文字建议？
- [ ] Are all parameters and assumptions I chose stated in the reply? / 我自己选的参数和假设，是否都在回复中说明了？
- [ ] When editing existing code: did I change only what was asked? / 如果是修改已有代码：是否只改了用户要求的部分？
- [ ] Any hand-written code that a package or standard tool already provides? / 有没有手写的、其实包或标准工具里已有的功能？
- [ ] Any block longer than ~10 lines, or pipe longer than 3 stages? Can it be split? / 有没有超过约 10 行的代码块，或超过 3 段的管道？能否拆开？
- [ ] Is all verification code (assertions, comparisons) removed? / 验证代码（断言、比对）是否都已删除？
- [ ] Does every block start with the three-line header (step / input / output)? / 每段代码开头是否都有三行说明（做什么 / 输入 / 输出）？
- [ ] Are plots default style only? / 画图代码是否只是默认样式？
- [ ] Do paths and parameters need changing in one place only, at the top? / 路径和参数是否只需在开头改一处？
- [ ] Have I run it end to end in the sandbox without errors (done on my side, not in the deliverable)? / 我已在沙盒中完整运行过一遍，确认没有报错（这一步在我这边做，不写进交付物）。

---

## 6. Examples / 对比示例

### R · Leiden clustering / R · Leiden 聚类

Don't write this (hand-built SNN graph) / 不要这样写（手写 SNN 图）：
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

Write this / 应该这样写：
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

### Python · Random forest / Python · 随机森林

Don't write this / 不要这样写：
```python
class RFPipeline:
    def __init__(self, n_trees=100, seed=42, verbose=True): ...
    def fit(self, X, y):
        try:
            ...
        except Exception as e:
            logging.warning(f"fit failed: {e}")
```

Write this / 应该这样写：
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

### Linux · Row count per sample / Linux · 统计每个样本的行数

Don't write this / 不要这样写：
```bash
find data -name "*.tsv" -print0 | xargs -0 -I{} sh -c 'echo "$(basename {} .tsv)\t$(tail -n +2 {} | wc -l)"' | sort -k2,2nr
```

Write this / 应该这样写：
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
部分原则改编自 forrestchang/andrej-karpathy-skills（karpathy-guidelines，MIT License）。
