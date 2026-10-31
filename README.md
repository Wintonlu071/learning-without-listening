# learning-without-listening-teacher

通过结合课堂ppt，作业，课堂录音来制作复习资料和预测考题

## 介绍
上课走神？听不懂？顾着和ta聊天？然后quiz的时候发现啥都不会？
没事的，只要你有能够复现课堂信息的资料，就让AI替你学，让AI像老师和出题人一样陪伴你。
## 功能特性

基于Codex的Agent skill，能够通过课堂ppt，作业，课堂录音来整理知识框架和复习指引。
对于一些不是“上课1+1,考试造飞机”的选修课，经测试可以预测接近一半的考题，亲测有效：
预测：
<img width="720" height="663" alt="image" src="https://github.com/user-attachments/assets/b49f9987-a06a-4739-9a90-dcaa8488eac0" />
<img width="591" height="638" alt="image" src="https://github.com/user-attachments/assets/e9055b4e-7d4b-464c-9fa2-8c37cd70fa97" />

考题：
<img width="972" height="626" alt="image" src="https://github.com/user-attachments/assets/89dce5df-4226-4fb6-a61d-56be05e0fd2b" />
<img width="721" height="662" alt="image" src="https://github.com/user-attachments/assets/563a87ce-d1d8-45af-bc30-1aba81363e6c" />
<img width="807" height="688" alt="image" src="https://github.com/user-attachments/assets/098fa797-e2f9-4652-a9f3-65e8825a377f" />

这门课主播课上基本没听或者听不懂，但是幸亏到了AI时代.....
<img width="993" height="273" alt="image" src="https://github.com/user-attachments/assets/ab56ce2a-7196-495d-95a0-8f506b6ac9ca" />
<img width="1009" height="328" alt="image" src="https://github.com/user-attachments/assets/f807d686-87d1-48cd-8b4b-f5fb49435d2b" />
<img width="746" height="558" alt="image" src="https://github.com/user-attachments/assets/75dc3146-8264-46a5-be89-c4a24b99b0a1" />

## 安装

1. **环境要求**
   - Python 3.10+
   - Chrome 或 Edge（用于把 HTML 打印成 PDF）
   - Codex（本技能作为 Agent skill 运行）
2. **安装 Python 依赖**
   ```bash
   pip install -r scripts/requirements.txt
   ```
3. **安装技能**：把仓库克隆到 Codex 技能目录
   ```bash
   git clone https://github.com/Wintonlu071/learning-without-listening-lecture.git ~/.codex/skills/learning-without-listening-teacher
   ```
   Windows 对应路径：`%USERPROFILE%\.codex\skills\learning-without-listening-teacher`
   （也可通过 Codex 的 skill-installer 从本 GitHub 仓库安装。）

## 使用

直接在 Codex 里说“帮我整理这门课的复习资料和知识体系”，技能会按 `SKILL.md` 的流程执行。核心步骤：

1. **抽取素材 + 判定语言**
   ```bash
   python scripts/extract_sources.py "D:\BNBU\caculus" --out "D:\BNBU\caculus\review"
   ```
   生成 `course_sources.md`（全部文本素材）与 `language.txt`（`en`/`zh`）。
2. **写 Markdown 源稿**：按 `references/review-guide.md` 与 `references/knowledge-system.md` 的结构撰写。
3. **渲染 HTML**
   ```bash
   python scripts/render_html.py review.md review.html --title "课程名" --tag "REVIEW" --subtitle "副标题"
   ```
4. **转 PDF**
   ```bash
   python scripts/html_to_pdf.py review.html review.pdf
   ```

## 输入与输出

**输入**（课程目录内，自动扫描）
- 课件 PPT/PDF（英文或中文）
- 课堂录音纪要 `*AI纪要*.docx`
- 作业 PDF

**输出**（默认写到 `<课程目录>/review/`）
- `*_Review_Guide` 复习资料：`.md` / `.html` / `.pdf`
- `*_Knowledge_System` 结构化知识体系：`.md` / `.html` / `.pdf`

## 语言规则

按 PPT 主导语言决定输出语言，判定只依据 PPT 文本（不受纪要语言影响）：

- **英文 PPT 为主** → 英文讲解 + 陌生术语加中文注释（如 `outlier（异常值）`）+ 中英术语表
- **中文 PPT 为主** → 全中文讲解，术语保留英文原文

## 目录结构

```
learning-without-listening-teacher/
├── SKILL.md               # 技能入口与工作流
├── references/            # 语言规则、复习资料/知识体系的结构与质量要求
│   ├── language-rule.md
│   ├── review-guide.md
│   └── knowledge-system.md
├── scripts/
│   ├── extract_sources.py # 扫描课程目录、抽取文本、判定语言
│   ├── render_html.py     # Markdown → 样式化 HTML（MathJax）
│   ├── html_to_pdf.py     # HTML → PDF（Chrome headless）
│   ├── check_no_secrets.py# 提交前密钥扫描
│   └── requirements.txt
└── assets/
    └── template.html      # HTML 模板（封面/表格/公式样式）
```

## 依赖

- Python 3.10+
- PyMuPDF —— PPT/PDF 文本抽取
- python-docx —— 读取 AI 纪要 docx
- Chrome / Edge —— headless 打印 PDF
- MathJax —— 生成 HTML 中的公式渲染（CDN 加载，无鉴权）

一键安装：`pip install -r scripts/requirements.txt`

## 安全说明

- 本技能与脚本**不含任何 API 密钥**，不调用任何需要鉴权的接口。
- 不读取、不复制、不提交 `auth.json`、`config.toml` 等凭证文件。
- 不把 PPT、音频、纪要原件提交到公开仓库。
- 提交/推送前可运行 `python scripts/check_no_secrets.py` 做密钥扫描。
- 音频转写不是默认能力，只在用户明确要求时才询问。

## License

MIT（建议；如需其他协议请自行修改，并可在仓库根目录补充对应 LICENSE 文件）
