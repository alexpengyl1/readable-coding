# readable-coding

[English](./README.md) | 简体中文

这是一个给 AI 编程助手用的 skill。它让助手写出**人能读懂、能自己运行的 R、Python 和 Linux 命令行代码**，而不是又长又复杂的代码。

## 为什么需要它

AI 助手写的代码常常结果正确但难以阅读，常见问题有：
- 把很多步骤塞进一个代码块；
- 自己重新实现包里已有的函数；
- 加上没人要求的检查和选项；
- 花大量代码美化图表。

这个 skill 用一组规则，让交付的代码接近写得好的教学代码。

## 包含哪些内容

| 部分 | 内容 |
|---|---|
| 动手之前 | 需求有歧义时先问；助手自己选定的参数要明确说明 |
| 通用规则 | 只做用户要求的；一步一块；用现成工具；默认输出和默认作图；每段代码开头注明做什么、输入、输出；不过度抽象；参数放在最前面；名字直白；修改代码时只改必须改的；简单性测试 |
| R | `包名::函数名`、R Markdown 写法、`set.seed()`、base 作图 |
| Python | pandas / scikit-learn 现成函数、Jupyter cell、`random_state`；不加没要求的 `argparse` / `class` / `logging` / 宽泛的 `try/except` |
| Linux 命令行 | 一行一个命令、管道要短、用标准工具、变量在开头定义并加引号 |
| 渲染报告（R Markdown / Quarto） | 输出 HTML：章节编号、浮动目录、代码可折叠（默认显示）；每个代码块前用几句话说明，结果以表格显示，附样本流转表，最后是 `sessionInfo()` |
| 验证 | 助手自己检查结果，检查用的代码不放进交付的代码里 |
| 自查清单和示例 | 交付前的自查清单，以及 R、Python、bash 各一组"不要这样写 / 应该这样写"的对比 |

`SKILL.md` 中的规则用英文写成。

## 使用方法

`SKILL.md` 使用通用的 skill 格式（开头写 `name` 和 `description`），同一个文件可以同时用于 Claude 和 Codex。把本仓库克隆到对应工具的 skills 文件夹即可。

**Claude Code**

```bash
git clone https://github.com/alexpengyl1/readable-coding.git ~/.claude/skills/readable-coding
```

**Codex（OpenAI）**

```bash
git clone https://github.com/alexpengyl1/readable-coding.git ~/.agents/skills/readable-coding
```

只想在某个项目里用：克隆到项目里的 `.claude/skills/readable-coding`（Claude Code）或 `.agents/skills/readable-coding`（Codex）。如果没出现，重启一下工具。以后更新：`git -C <skill 文件夹> pull`。

**Claude 应用（claude.ai / 桌面版）**：把本仓库下载为 ZIP，在 设置 → Capabilities → Skills 里上传。

- **其他助手（Cursor、Copilot 等）**：把 `SKILL.md` 的正文（开头 `---` 之间那段之后的内容）粘贴到助手的规则文件或自定义指令里。

## 致谢

- 代码风格参考了 ML4LS 课程的教学代码（Payam Emami，payamemami.com）。
- 部分原则改编自 [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills)（`karpathy-guidelines`，MIT License），包括 Think Before Coding、Simplicity First、Surgical Changes 和 Goal-Driven Execution。
