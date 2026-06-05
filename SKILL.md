---
name: nau-beamer
description: 使用南京审计大学（南审/NAU）Beamer 模板制作学术演示文稿。当用户提到"南审模板"、"NAU模板"、"南京审计大学PPT/幻灯片/汇报"、"用NAU做beamer"、"用南审beamer"、"迁移到南审模板"，或任何需要将内容制作成南审风格演示文稿的场景时，必须使用此技能。也适用于将现有PPT/PDF内容迁移至此模板，以及修改已有的南审Beamer文件。
---

# NAU Beamer 演示文稿制作

南京审计大学（NAU）Beamer 模板，深海军蓝配色，支持中文，XeLaTeX 编译。

## 模板位置（Skill 内置，自包含）

所有模板资源打包在 skill 目录下，无需依赖外部项目路径：

```
C:\Users\fangj\.claude\skills\nau-beamer\assets\
├── collegeBeamer.sty       ← 核心样式文件
├── template.tex            ← 空白起始模板
└── src\NAU\
    ├── color-logo.png      ← 彩色校徽
    ├── trans-logo.png      ← 透明校徽
    └── background.png      ← 首页背景图
```

**完整的历史示例**（可参考）：`d:\A-Gin-AI\AA-projects\A汇报\润智年会\NAU\251220_NAU.tex`

## 初始化新演示文稿

在目标目录中复制 assets 后再创建 `.tex` 文件，因为 `collegeBeamer.sty` 和图片均用相对路径：

```powershell
# 1. 将 assets 内容复制到工作目录（一次性操作）
$skill = "C:\Users\fangj\.claude\skills\nau-beamer\assets"
$dest  = "你的目标目录路径"          # 例如 "d:\A-Gin-AI\AA-projects\A汇报\某次汇报"
Copy-Item "$skill\*" $dest -Recurse -Force

# 2. 在 $dest 目录下创建或编辑 .tex 文件，编译：
Set-Location $dest
xelatex 文件名.tex
xelatex 文件名.tex
```

也可以**直接在 assets\ 目录下**创建新 `.tex` 并编译，适合快速出稿。

---

## 标准前导区（直接复用）

```latex
\documentclass{beamer}
\usepackage{xeCJK}
\usepackage[nau,zh]{collegeBeamer}

\usepackage{booktabs}
\usepackage{amsmath,amsfonts,amssymb}
\usepackage{multirow}
\usepackage{array}
\usepackage{tabularx}

% NAU 配色（深海军蓝 RGB 0,62,114）
\definecolor{NAUBlue}{RGB}{0, 62, 114}
\setbeamercolor{palette primary}{bg=NAUBlue,fg=white}
\setbeamercolor{palette secondary}{bg=NAUBlue!80!black,fg=white}
\setbeamercolor{palette tertiary}{bg=NAUBlue!60!black,fg=white}
\setbeamercolor{structure}{fg=NAUBlue}
\setbeamercolor{item projected}{bg=NAUBlue,fg=white}
\setbeamercolor{block title}{bg=NAUBlue,fg=white}
\setbeamercolor{block body}{bg=NAUBlue!10,fg=black}

\setbeamertemplate{items}[circle]
\setbeamertemplate{blocks}[rounded][shadow=true]

% 自定义结束页文字（可按需修改）
\renewcommand{\qaStr}{\textbf{感谢您的聆听！\\ 欢迎批评指正！}}

% 元数据
\title[短标题]{完整论文标题}
\subtitle{副标题}
\author{作者甲$^1$ \quad 作者乙$^2$ \\[4pt]
{\small $^1$南京审计大学经济学院 \quad $^2$某某大学某某学院}}
\date{XXXX年X月X日}
```

---

## 文档正文结构

```latex
\begin{document}

\maketitle   % 首页（自动加载背景图和校徽）

% 四大节（节切换时自动生成深蓝目录过渡页）
\section{引言与理论}
\section{研究设计与核心发现}
\section{机制与深化分析}
\section{结论与建议}

\QApage   % 结束的 Q&A 深蓝页

\end{document}
```

**不要**手动添加 `\begin{frame}{\tocStr}\tableofcontents\end{frame}`——`\AtBeginSection` 已自动生成带高亮的目录切换页，手动添加会造成重复。

---

## 帧（Frame）常用模式

### 普通内容帧
```latex
\begin{frame}{帧标题}
    % 正文内容
\end{frame}
```
框架副标题由模板自动注入当前节编号和名称，无需手动写。

### 内容较多时加 shrink
| 内容类型 | 建议 shrink |
|---------|------------|
| 纯文字/block | `[shrink=10]` |
| 含1个中等表格 | `[shrink=15]` |
| 多个公式 | `[shrink=20~25]` |
| 极密集内容 | `[shrink=30]` |

```latex
\begin{frame}[shrink=20]{理论框架：模型推导}
    ...
\end{frame}
```

---

## Block 用法

```latex
% 深蓝标题 + 浅蓝body（默认）
\begin{block}{结论}
    内容文字
\end{block}

% 居中标题的 block
\begin{block}{\centering 核心发现}
    \small 关键结论放这里
\end{block}
```

---

## 双栏布局

```latex
\begin{columns}[T]
    \begin{column}{0.48\textwidth}
        \begin{block}{左侧}...
        \end{block}
    \end{column}
    \begin{column}{0.48\textwidth}
        \begin{block}{右侧}...
        \end{block}
    \end{column}
\end{columns}
```

---

## 表格规范

### 变量定义表（自适应宽度）
用 `tabularx{\textwidth}` + `l l c X`，避免固定 `p{}` 在 shrink 后截断文字：

```latex
\small\centering
\begin{tabularx}{\textwidth}{l l >{\centering\arraybackslash}m{1.2cm} X}
    \toprule
    \textbf{类型} & \textbf{变量名称} & \textbf{代码} & \textbf{定义与测度方式} \\
    \midrule
    被解释 & 企业资本跨区域流动 & \textbf{INV} & 母公司在异地建立子公司取1，否则取0 \\
    核心解释 & 地区金融竞争程度 & \textbf{FMC} & 1/HHI指数 \\
    \midrule
    \multirow{3}{*}{机制/调节} & 金融科技水平 & \textit{FTE} & 百度搜索指数 \\
     & 地区融资约束 & \textit{LDR} & 金融机构存贷比 \\
     & 地区创业活力 & \textit{VET} & 新增注册企业数对数 \\
    \bottomrule
\end{tabularx}
```

### 回归结果表（宽表缩放）
用 `\resizebox{\textwidth}{!}` 处理多列回归表，内部用 `\scriptsize`：

```latex
\scriptsize\centering
\resizebox{\textwidth}{!}{%
\begin{tabular}{lcccc}
    \toprule
     & (1) & (2) & (3) & (4) \\
    \midrule
    核心变量 & $0.134^{***}$ & ... & ... & ... \\
     & (22.539) & ... & ... & ... \\
    \midrule
    控制变量 & 控制 & 控制 & 控制 & 控制 \\
    固定效应 & 控制 & 控制 & 控制 & 控制 \\
    $R^2$    & 0.469 & ... & ... & ... \\
    \bottomrule
\end{tabular}}
```

---

## 数学公式

公式编号会跨帧自动递增，无需手动设置。长分式可用 `\bigl(\bigr)` 加大括号：

```latex
\begin{equation}
    J_q = \int_0^\infty e^{-rt}\pi_q(t)\,dt
\end{equation}

% 并排公式
\begin{align}
    \pi_\alpha &= vf(K_{out}) - \phi(\lambda_A,\lambda_B)K_{out} - \frac{\xi}{2}u_\alpha^2 \\
    \pi_\beta  &= vf(K_{in})  - \phi(\lambda_B,\lambda_A)K_{in}  - \frac{\xi}{2}u_\beta^2
\end{align}
```

---

## 首页作者格式

多单位用上标数字区分；第一作者放左，字母顺序或按贡献排列：

```latex
\author{方京$^1$ \quad 李伶俐$^2$ \quad 董宸硕$^2$ \quad 王定祥$^2$ \\[4pt]
{\small $^1$南京审计大学经济学院 \quad $^2$西南大学经济管理学院}}
```

---

## 编译方式

```powershell
# 在 NAU\ 目录下运行，需两次以生成正确目录
Set-Location "d:\A-Gin-AI\AA-projects\A汇报\润智年会\NAU"
xelatex 文件名.tex
xelatex 文件名.tex
```

或用 LaTeX Workshop（VSCode）配置 XeLaTeX recipe，同样需要两次编译。

---

## 迁移 PPT/PDF 内容的工作流

1. **读取原始内容**：用 Read 工具读取 PDF，或用 markitdown skill 转换 PPTX
2. **梳理章节结构**：识别 4 个左右的 `\section{}`
3. **逐页映射**：每个原始幻灯片 → 一个 `\begin{frame}...\end{frame}`
4. **输出文件**：新建 `文件名_NAU.tex` 放在 `NAU\` 目录
5. **编译验证**：运行两次 xelatex，根据 overfull 警告调整 `[shrink=N]`

---

## 常见注意事项

- **引号**：中文环境用 `"..."` 或直接用直引号 `"..."` 均可，不要用 `"..."` 英文弯引号
- **百分号**：正文中写 `12.4\%`，避免裸 `%` 被当作注释
- **中文破折号**：用 `——`（两个全角横线）而非 `--` 或 `---`
- **字体警告**：xeCJK 下偶有 Font shape 警告可忽略，不影响输出
