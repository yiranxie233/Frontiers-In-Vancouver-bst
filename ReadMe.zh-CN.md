# Frontiers Vancouver BibTeX 样式：本地修改的 `@article` 排版

[English](ReadMe.md) | 简体中文

## 项目说明

本仓库保留 Frontiers 提供的原始样式作为未修改的对照基线，同时提供一个针对期刊论文参考文献的本地修改版。仓库作者在 Frontiers 网站上观察到的期刊论文版式与基线文件输出不同，因此制作了此修改版进行复现。

对于包含 `journal`、`year`、`volume` 以及 `eid` 或 `pages` 定位信息的常规 `@article` 条目，修改版希望得到以下片段：

```text
期刊名. (年份) 卷号:文章号或页码.
```

其中，“文章号或页码”优先取自 `eid`；没有 `eid` 时使用 `pages`。

修改版并非 Frontiers 官方发布。出版社要求和模板可能更新，投稿前请务必将实际生成的参考文献与目标 Frontiers 期刊当时的作者指南进行核对。

## 文件说明

| 文件 | 用途 |
| --- | --- |
| [`Frontiers-Vancouver.bst`](Frontiers-Vancouver.bst) | Frontiers 提供的原始文件，本仓库中未作修改。 |
| [`Frontiers-Vancouver (1).bst`](<Frontiers-Vancouver (1).bst>) | 本文所述的本地修改版。 |

## 修改内容

实际生效的修改仅针对 `@article` 条目：

- 在期刊名后添加句点；
- 将带圆括号的年份移到卷号之前；
- 卷号由粗体改为正常字重；
- 使用冒号连接卷号与文章号/页码，冒号两侧不留空格；
- 存在 `eid` 时使用 `eid`，否则自动回退到 `pages`；
- 页码范围仍会经过原样式的连字符规范化处理。

下面仅展示一条文献中“期刊名/年份/卷号/文章号或页码”的部分：

| 输入字段 | Frontiers 原始基线 | 修改版 |
| --- | --- | --- |
| `eid = {e12345}` | *Journal* **12** (2025) e12345 | *Journal*. (2025) 12:e12345 |
| `pages = {101--109}` | *Journal* **12** (2025) 101–109 | *Journal*. (2025) 12:101–109 |

作者、标题、DOI、引用标签、排序规则以及非 `@article` 条目均沿用原始基线，本次没有修改。

## 在 LaTeX 中使用

这是传统的 BibTeX `.bst` 样式，不是 `biblatex`/Biber 样式。

### 1. 准备样式文件

修改版当前的文件名包含空格和括号，在不同 TeX/BibTeX 工具链中可能产生兼容问题。建议保留仓库中的两个文件用于对照，并将修改版复制一份、改成不含空格的名称：

```text
Frontiers-Vancouver (1).bst  ->  Frontiers-Vancouver-modified.bst
```

将 `Frontiers-Vancouver-modified.bst` 放在主 `.tex` 文件所在目录。Overleaf 的用法相同：把文件上传到项目根目录，并使用上述简洁文件名。

### 2. 选择参考文献样式

一个可以独立使用的最小示例如下：

```tex
\documentclass{article}

% 如果文档类或 Frontiers 模板已经加载 natbib，请删除下一行。
\usepackage[numbers,sort&compress]{natbib}

\begin{document}

An example citation~\cite{smith2025}.

% 填写样式文件的基本名称，不要带 .bst 扩展名。
\bibliographystyle{Frontiers-Vancouver-modified}
% 填写文献数据库的基本名称，不要带 .bib 扩展名。
\bibliography{references}

\end{document}
```

如果 Frontiers 模板已经加载并配置了 `natbib`，请不要重复加载；保留模板原有的引用设置，只需在文档末尾附近加入上述两个参考文献命令。

### 3. 准备 BibTeX 数据

使用文章号时，将其写入 `eid` 字段：

```bibtex
@article{smith2025,
  author  = {Smith, Jane and Doe, John},
  title   = {An Example Article},
  journal = {Frontiers in Example},
  year    = {2025},
  volume  = {12},
  eid     = {e12345},
  doi     = {10.1234/example.12345}
}
```

如果使用传统页码范围，请省略 `eid` 并填写：

```bibtex
pages = {101--109},
```

如果同时存在 `eid` 和 `pages`，修改版会优先使用 `eid`。DOI 请填写 `10.1234/example.12345` 这种纯 DOI；生成的 `.bbl` 会调用 `\doi{...}`，并在该命令尚未定义时提供带 `doi:` 前缀的后备定义。样式会将输入的 `journal` 值排成斜体，但不会自动生成期刊缩写，因此应填写目标期刊要求的全名或缩写。

### 4. 编译

在两轮 LaTeX 编译之间运行 BibTeX：

```console
pdflatex main.tex
bibtex main
pdflatex main.tex
pdflatex main.tex
```

`bibtex` 后面的参数是主文档的任务名，而不是 `.bib` 文件名。也可以用 XeLaTeX 或 LuaLaTeX 替代 `pdflatex`，但各轮 LaTeX 编译应使用同一个引擎。使用 `latexmk` 时，通常可以执行：

```console
latexmk -pdf -bibtex main.tex
```

Overleaf 通常会自动识别并运行 BibTeX。切换样式后，如果仍然显示旧的 `.bbl` 结果，请使用 **Recompile from scratch** 重新完整编译。

## 使用未修改的原始基线

如果希望使用 Frontiers 提供的原始文件，请将 `Frontiers-Vancouver.bst` 放在主文档旁边，并选择：

```tex
\bibliographystyle{Frontiers-Vancouver}
```

每次更换样式后都需要重新运行 BibTeX。

## 适用范围与限制

- 本补丁只修改 `@article` 条目的文后参考文献排版。
- 本次没有修改引用标签、参考文献排序、作者格式、DOI 处理或其他条目类型。
- 底层样式使用 BibTeX 和兼容 natbib 的标签；正文中的数字引用由 Frontiers 模板或 natbib 选项控制，不是由本补丁改变的。
- 不能通过 `\usepackage[style=...]{biblatex}`、`\addbibresource`、`\printbibliography` 或 Biber 加载本样式。
- 原始基线的文件头说明，该样式面向英文文稿，并用于 Frontiers 健康领域期刊或 *Frontiers in Physics*；用于其他期刊前请核对适用性。
- 任何样式都不能保证稿件被接收；请始终以目标期刊当前的作者指南和最终生成的 PDF 为准。

## 许可证与来源

两个 `.bst` 文件的文件头均说明：该样式由 `merlin.mbs`/`physjour.mbs` 生成，版权归 Patrick W. Daly 所有，并可依据 LaTeX Project Public License 第 1 版或后续版本进行再分发和/或修改。再分发修改版时，请保留原有版权与许可证声明。
