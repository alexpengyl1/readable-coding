---
name: readable-coding
description: "Rules for writing code that a human will read and run locally, in R, Python and Linux shell (bash): R scripts / R Markdown / Quarto, Python scripts / Jupyter notebooks, shell commands and bash scripts. Short one-purpose chunks, existing package functions and standard tools instead of hand-rolled code, default output and plots, explanation in prose, surfacing assumptions, surgical edits, and doing only what the user asked. Use whenever delivering code, a notebook or command lines to the user, especially for bioinformatics / machine learning analyses (preprocessing, PCA, clustering, Leiden, random forest, scikit-learn, mixOmics, command-line file processing). Style modelled on teaching code such as the ML4LS course (payamemami.com)."
---

# Readable coding · R / Python / Linux（写给人读的代码）

## 适用范围

**交给用户的代码**都按本规则写：
- **R**：`.R` 脚本、`.Rmd` / `.qmd` notebook
- **Python**：`.py` 脚本、Jupyter notebook（`.ipynb`）
- **Linux**：命令行、bash 脚本（`.sh`）

聊天中贴出的代码片段也一样。

我自己在沙盒中做的验证（断言、与旧结果比对、数值检查）不受本规则限制，**但这些验证代码一律不放进交付物**。

**核心原则：读者是在 RStudio / Jupyter / 终端里一步一步运行的人。** 每一步都先问：读者看函数名或命令名，能不能马上知道这一步在做什么？

---

## 一、动手之前：先说出假设（借鉴 karpathy-guidelines · Think Before Coding）

- 需求有多种理解时（例如"notebook"可以指 .Rmd、.qmd 或 .ipynb），**先问，不要自己默默选一个**。
- 用户没指定、而我必须选的参数（阈值、PC 个数、k、resolution、训练/测试比例、线程数），**在回复中明确写出选了什么、为什么**，并说明怎么改。
- 如果有更简单的做法，直接说出来；用户的方案明显有问题时，指出问题。

---

## 二、通用规则（三种语言都适用）

### 1. 只做用户说的
- 用户说"检查 X、过滤 Y、做 PCA"，交付物里就只有这三步。
- 我认为有用的额外分析（多种指标、参数网格扫描、稳定性检验、额外输出文件），**不写进代码**，在回复末尾用一句话作为建议提出。
- 不为"以后可能用到"写分支代码或备用代码。

### 2. 一步一块
- 每个代码块 / cell / 命令通常只有 1–8 行：读取、检查、过滤、拟合、预测、评估、画图，各占一块。
- 不要在一块里混入多个概念。

### 3. 用现成工具，不手写算法
- 包或标准工具里已有的功能直接调用，不要为了"免安装"重新实现。
- 需要的包或工具在开头列一次（安装命令或 import 块）。

### 4. 让默认输出说话
- 直接显示对象或结果，不拼接格式化字符串、不自己组装汇总表（除非用户要求）。

### 5. 图用默认样式
- 一张图对应一个问题；不调标签位置、不配色板、不拼多面板，除非用户要求或确实需要按组上色。

### 6. 解释写在正文里，代码里少写注释
- notebook 中：代码块前用一两句话说明"做什么、为什么"，代码块后一句话写结论。
- 脚本中：每段上方一行注释即可；行内注释只标关键数字或参数含义。

### 7. 允许重复，避免过度抽象
- 不定义只用一次的函数或类；循环只在确实需要扫描参数或批量处理文件时使用，循环体保持简短。

### 8. 顺序执行，参数集中放在最前面
- 从上到下运行即可，不依赖隐藏状态。
- 有随机性的步骤固定种子。
- 用户需要修改的路径和参数放在最前面一个块里（如数据目录、输出目录、`k`、`resolution`），输出统一保存到输出目录。

### 9. 名字直白
- 用 `expr`、`pca`、`keep`、`cluster`、`train`、`test`、`input_dir` 这类名字，不用难懂的缩写（如 `zf`、`cm`、`md`）。

### 10. 修改已有代码时，只改必须改的（借鉴 karpathy-guidelines · Surgical Changes）
- 用户说"把结果保存到 out_dir"，就只改路径相关的那几行，不要顺手重排、改名、补注释或"优化"其他部分。
- 沿用已有的写法和风格，即使我会换一种写法。
- 只删除因我这次修改而变得多余的代码；发现其他无关的多余代码，在回复里提一句，不要直接删。
- 检验标准：**每一行改动都能直接对应到用户的要求。**

### 11. 简单性测试（借鉴 karpathy-guidelines · Simplicity First）
- 不处理不可能发生的情况，不加用户没要求的可配置项或通用性。
- 写完问自己：**"一个有经验的用户会不会觉得这太复杂？"** 如果 100 行能写成 40 行，就重写。

---

## 三、按语言补充

### R
- 用 `包名::函数名` 标明出处（如 `bluster::makeSNNGraph()`、`caret::confusionMatrix()`、`randomForest::tuneRF()`）；`library()` 只用于反复调用的包。
- **不要为了免安装把自己限制在 base R**；开头写一个 `install.packages()` / `BiocManager::install()` 块。
- 输出：`print(model)`、`head()`、`dim()`、`table()`、`summary()`；不用 `cat(sprintf(...))`。
- 作图：`plot()`、`boxplot()`、`hist()`、`barplot()`、`varImpPlot()`；交付物里不写 `png()` / `dev.off()`，除非用户要求保存图片。
- 随机性：`set.seed()`。
- Rmd 中不用 `setwd()`，用 `file.path(data_dir, ...)`。

### Python
- 优先用 pandas / numpy / scikit-learn / scipy 的现成函数，例如 `train_test_split`、`RandomForestClassifier`、`classification_report`、`PCA`、`StandardScaler`、`silhouette_score`。
- 输出：notebook 中让 cell 的最后一行直接显示结果（`df.head()`、`df.shape`、`model`）；脚本中用简单的 `print(df.head())`，不用复杂的 f-string 格式化报告。
- 作图：`df.plot()`、`plt.scatter()`、`plt.hist()` 这类默认图即可；不调样式、不写复杂的 subplot 布局。
- 随机性：传 `random_state=42`（scikit-learn）或 `np.random.seed()`。
- 不加用户没要求的 `argparse`、类型注解、`logging`、`class`、`if __name__ == "__main__":` 结构；简单脚本按顺序写即可。
- 不写宽泛的 `try/except` 来吞掉错误；出错就让它报错。
- 路径用 `pathlib.Path` 或简单字符串，在开头定义一次。

### Linux（命令行 / bash）
- **一行一个命令，一个命令做一件事。** 管道一般不超过 3 段；更长就拆成几步，并用中间文件或 `head` 查看中间结果。
- 优先用标准工具：`ls`、`head`、`wc -l`、`cut`、`sort`、`uniq -c`、`grep`、`awk`、`sed`、`find`；不要写复杂的 awk 程序或一行式技巧代替简单命令。
- 可读性优先：必要时用长参数（如 `sort --numeric-sort`、`grep --count`），让读者看懂选项含义。
- 脚本开头集中定义变量（如 `INPUT_DIR=...`、`OUTPUT_DIR=...`、`THREADS=4`），变量一律加双引号：`"$INPUT_DIR"`。
- 脚本中可以写 `set -e`（出错即停），但不要写大量 `if [ ! -f ... ]` 之类的防御性检查，除非用户要求。
- 不定义只用一次的函数；批量处理文件用简单的 `for f in *.tsv; do ...; done`。
- 会删除或覆盖文件的命令（`rm`、`mv`、`>`）要在正文里明确提醒。

---

## 四、验证在我这边完成（借鉴 karpathy-guidelines · Goal-Driven Execution）

动手前先确定"怎样算完成"，并在沙盒中验证；验证代码不进入交付物。例如：
1. 过滤低表达基因 → 验证：基因数与预期一致，被删的都是低表达基因
2. 改写 Rmd / notebook → 验证：完整 Knit 或从头运行无报错，结果与上一版数值一致
3. 换用包函数 → 验证：与旧实现比较（如 ARI），有差异就告诉用户
4. shell 脚本 → 验证：在测试数据上运行，检查输出文件行数和内容

简单任务可以灵活处理，不必每次都列出验证清单。

---

## 五、交付前自查清单

- [ ] 每一步都是用户要求的吗？多余的已删掉并改为文字建议？
- [ ] 我自己选的参数和假设，是否都在回复中说明了？
- [ ] 如果是修改已有代码：是否只改了用户要求的部分？
- [ ] 有没有手写的、其实包或标准工具里已有的功能？
- [ ] 有没有超过约 10 行的代码块，或超过 3 段的管道？能否拆开？
- [ ] 验证代码（断言、比对）是否都已删除？
- [ ] 画图代码是否只是默认样式？
- [ ] 路径和参数是否只需在开头改一处？
- [ ] 我已在沙盒中完整运行过一遍，确认没有报错（这一步在我这边做，不写进交付物）。

---

## 六、对比示例

### R · Leiden 聚类

不要这样写（手写 SNN 图）：
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

应该这样写：
```r
pcs <- pca$x[, 1:10]
g <- bluster::makeSNNGraph(pcs, k = 10, type = "jaccard")
```
```r
set.seed(42)
leiden <- igraph::cluster_leiden(g, objective_function = "modularity", resolution = 0.5)
table(igraph::membership(leiden))
```

### Python · 随机森林

不要这样写：
```python
class RFPipeline:
    def __init__(self, n_trees=100, seed=42, verbose=True): ...
    def fit(self, X, y):
        try:
            ...
        except Exception as e:
            logging.warning(f"fit failed: {e}")
```

应该这样写：
```python
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)
```
```python
model = RandomForestClassifier(n_estimators=100, random_state=42)
model.fit(X_train, y_train)
```
```python
print(classification_report(y_test, model.predict(X_test)))
```

### Linux · 统计每个样本的行数

不要这样写：
```bash
find data -name "*.tsv" -print0 | xargs -0 -I{} sh -c 'echo "$(basename {} .tsv)\t$(tail -n +2 {} | wc -l)"' | sort -k2,2nr
```

应该这样写：
```bash
INPUT_DIR="data"

for f in "$INPUT_DIR"/*.tsv; do
    echo "$f"
    tail -n +2 "$f" | wc -l      # number of rows without the header
done
```

---
部分原则改编自 forrestchang/andrej-karpathy-skills（karpathy-guidelines，MIT License）。
