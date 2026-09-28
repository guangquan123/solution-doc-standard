# solution-doc-standard

**不是让 AI 凭感觉改稿，而是让每一次表达修改都有证据、有确认、有验证。**

把一份中文方案稿从草稿打磨到可交付：先诊断问题在哪一层，再逐轮优化，每个改动可核对、可回滚。

[![version](https://img.shields.io/badge/version-v2.1.0-blue)]() [![license](https://img.shields.io/badge/license-MIT-green)]() [![language](https://img.shields.io/badge/%E8%AF%AD%E8%A8%80-%E4%B8%AD%E6%96%87-orange)]()

---

## 它真正在干什么

一份方案稿通常死在三个地方：

> 词是生造的，读者听不懂 → 改稿凭感觉，越改越乱 → 说改了，实际没改

本技能把这条路拆成四轮可暂停、可核对的优化流程：

> 接稿诊断 → 结构 → 内容 → 语言 → 格式

每轮先出问题清单（编号分级），改动走对照表逐条确认，替换后跑脚本验证。语料是唯一裁判：一个词是不是生造词，不靠感觉，靠"文档高频＋语料零命中"的检索证据。

## 它和普通 AI 改稿哪里不一样

| 普通 AI 改稿 | solution-doc-standard |
|---|---|
| 一段提示词全凭手感 | 先诊断后动手，问题编号可核对 |
| 生造词换生造词 | 替换词必须语料有出处，出处入证据表 |
| 说改了就信改了 | 计数断言＋零残留＋结构不变，三重验证 |
| AI 直接改正文 | 对照表逐条批准后才动笔 |
| 越改口径越偏 | 语言与口径两条线，口径变更单独确认 |

一句话：普通工具帮你把稿子改完，这套流程帮你知道每次修改为什么成立。

## 一份稿子怎样走完

```mermaid
flowchart LR
    D["⓪ 接稿诊断<br/>四层体检＋轮次计划"] --> S["第 1 轮 结构<br/>骨架/导读/表格化"]
    S --> C["第 2 轮 内容<br/>依据/责任/异常路径"]
    C --> L["第 3 轮 语言<br/>扫描→对照→替换→验证"]
    L -->|"停点：对照表逐条批准"| F["第 4 轮 格式<br/>交付格式＋五组自检"]
    F --> V["交付：四件套留痕"]
```

三个停点由人拍板：轮次计划（⓪ 诊断后）、对照表（动正文前）、每轮小结（继续/跳过/停止）。

## 四轮，各自只做一件事

| 轮 | 负责什么 | 明确不做什么 |
|---|---|---|
| ⓪ 诊断 | 四层体检报告＋轮次计划 | 不改稿 |
| 1 结构 | 补骨架章、背景重组"先问题后价值"、每章导读 | 不动内容结论 |
| 2 内容 | 依据分类、责任到人、异常路径、判定形式化 | 不做结构大重组 |
| 3 语言 | 生造词/隐喻词/破折号/绝对化断言/行业歧义＋页数压缩 | 不动口径 |
| 4 格式 | 交付格式转换＋五组自检 | 不重复造格式化工具 |

## 安装

```powershell
git clone https://github.com/guangquan123/solution-doc-standard.git
# 把 skill 目录拷贝到你的 agent 技能目录，例如：
Copy-Item -Recurse solution-doc-standard .opencode/skills/solution-doc-standard
```

依赖：Python 3（md/txt 零依赖；docx 需 `pip install python-docx`）。

## 第一次跑

```
这是我的方案草稿，优化到可交付
```

没给稿或没给语料时不会动笔，AI 会先出必需输入清单（①方案稿 ②语料目录 ③可选：页数预算/行业背景/只跑某轮）。

## 日常用法

不要为了跑完整流程而走完四轮。只需要去AI味，就直接说：

```
这份方案做一轮去AI味，语料在 <语料目录>，先出对照表给我确认
方案 49 页要压到 43 页，别动口径
把这套 0-2 打分制改成勾选式
"生产阻断"在制造业语境有歧义，检查这类行业歧义词
```

## 三脚本（可脱离对话直接跑）

```powershell
# 扫描：生造词候选 + 隐喻词命中 + 破折号 + 绝对化断言 + 行业歧义词
python -X utf8 scripts/term_scan.py <目标文档.md> <语料目录> --top 30
python -X utf8 scripts/term_scan.py <方案.md> <语料目录> --industry manufacturing

# 替换（先 dry-run 断言，再实跑；pairs.json 来自批准后的对照表）
python -X utf8 scripts/fix_terms.py pairs.json --dry-run
python -X utf8 scripts/fix_terms.py pairs.json

# 验证：旧词零残留（自动排除修订历史区）/ 新词就位 / 结构不变
python -X utf8 scripts/check_terms.py check.json
```

可照跑的端到端演示见 `examples/README.md`。

## 仓库内容

```
solution-doc-standard/
├── README.md
├── LICENSE
├── SKILL.md          # 技能执行指令（工作流、13 条硬约束）
├── scripts/          # term_scan / fix_terms / check_terms
└── examples/         # 端到端最小示例（示例稿＋语料＋配置）
```

> 开发工作区另含版本存档与质量记录（CHANGELOG、versions/、references/），属内部过程资产，不随仓库分发。

## 已知局限（实测截至 2026-09）

1. 语言层工具链经 1.5 个真实文档检验（一份实战＋一份自测），未经第三个项目检验；
2. 四轮管线中的第 1/2/4 轮刚定义，未跑过完整四轮实战；
3. 扫描主表会混入跨词切片伪候选，靠对照表人工过滤（机器出证据、人拍板）；
4. 语料需万字级以上，小语料会把通用词误判为候选；
5. docx 只提取段落与表格，文本框、图内文字拿不到；pdf 语料跳过；
6. check 的结构断言仅支持 md/txt 标题计数。

预置词表（隐喻词、行业歧义词）基于数据治理类文档实测，**换领域必须用新语料重新验证，结论不继承**。

## License

[MIT](LICENSE)
