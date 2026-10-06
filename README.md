# 卢怡然 C4A —— 技能提交自动评审

> 挑战：C4A 技能提交自动评审 ｜ 交付日期：2026-10-06 ｜ 截止：2026-12-31
> 一句话：**不是做一个技能，是做一个评审别人技能的东西——并且它自己也被测过。**

---

## 〇、本包里有什么

| 必交物 | 文件 |
|---|---|
| 方案设计 | `卢怡然_C4A_方案设计.md` |
| skill-evaluator | `c4-skill-evaluator/`（+ `c4-skill-evaluator.skill` 打包件） |
| 评审报告 | `卢怡然_C4A_评审报告.md`（3 份真实提交） |
| AI日志 | `卢怡然_C4A_AI日志.md` |
| 复盘 | `卢怡然_C4A_AAR.md` |
| Demo | `demo/`（一条命令 + 真跑出来的输出示例） |

---

## 一、评审器怎么跑

```bash
# 0) 自检：13 项必须全 PASS
python c4-skill-evaluator/scripts/c4_evaluator.py selftest

# 1) 评审一个装着技能提交的文件夹
python c4-skill-evaluator/scripts/c4_evaluator.py evaluate "C:/群文件/C4_提交" \
    --out ./评审输出 --json ./评审输出/result.json

# 2) 测评审器自己的误判率（8 例人工标注黄金集）
python c4-skill-evaluator/scripts/golden_test.py --out ./evidence/golden.txt
```

**环境**：Python 3.9+，**零第三方依赖**（YAML 自解析、文本抽取自实现）。
`openpyxl` 可选——装了才出 Excel 详表，没装只出 Markdown/JSON，不报错。

**输出**：`C4评审报告.md`（主）+ `C4评审详表.xlsx`（可选）+ JSON（可选）。

---

## 二、它判什么

### 完整性（5 槽位）

Skill 说明文档 / 可执行内容 / Demo / 教学说明 / AI 生成日志。
文件名模式 + 内容信号双通道；只命中文件名时会标注 `filename`，提示人工复核。

### 质量（4 条件 × 5–6 检测项）

| 条件 | 关注 | 真实执行的项 |
|---|---|---|
| 可复用 Reusable | 换台机器能用吗 | — |
| 可执行 Executable | 真能跑吗 | `py_compile` 语法校验、`.skill` 包解包校验 |
| 可验证 Verifiable | 怎么证明它对 | Demo 是否真实产物（读字节数、判空/判占位） |
| IO 明确 Clear I/O | 输入输出说清了没 | — |

**每条判定都带证据锚点**（`SKILL.md:78` 或 `未发现：C:\Users\, /Users/, ...`），可当场核对。

### 四条防作弊

证据锚点 / `real:true` 真实校验（缺证即 fail）/ 负信号硬地板（硬编码路径·密钥·占位符 → 封顶）/
反堆砌（重复关键词不累计加分）。

---

## 三、实测数字（不是估计）

### 3.1 评审器自己的准确率（黄金集 8 fixture）

| 指标 | 数值 |
|---|---|
| 完整性判定（n=40） | 97.5%（误报 1、漏报 0） |
| 质量评级（n=32） | 精确匹配 59.4% ｜ ≤1 级 96.9% |
| **严重误判（✅↔❌）** | **3.1%** |
| 作者识别 | 8/8 |

⚠️ 精确匹配不到 60% 是**故意**的：测试集含边界样本，打满 100% 说明测试集太简单或标签照输出填。
原始报告：`evidence/golden_test_v3.txt`。

### 3.2 评审 3 份真实提交的结果

| 提交 | 完整性 | 综合分 | 主要缺口 |
|---|---|---|---|
| `challenge-submit-guard`（我的 C4 技能） | 4/5 | 84.5 | Demo |
| `c4a-skill-evaluator-starter`（官方样例） | 4/5 | 84.5 | Demo |
| `wechat-doc-mapper`（基座技能） | 3/5 | 76.5 | Skill 说明文档、Demo |

原始输出：`evidence/reviews/`（每份 Markdown + Excel + JSON）。

---

## 四、目录

```
C4A/
├─ 卢怡然_C4A_方案设计.md      设计原则、架构、防作弊、测试策略、已知边界
├─ 卢怡然_C4A_评审报告.md      3 份真实提交的评审结论 + 评审器自身准确率
├─ 卢怡然_C4A_AI日志.md        时间线、AI 参与的三处、反向举证、4 条失败
├─ 卢怡然_C4A_AAR.md           复盘：4 个失败 + 1 个未做项 + 自评
├─ README.md                   本文件
├─ c4-skill-evaluator/         ★ 评审器（可独立运行）
│  ├─ SKILL.md                 技能说明（frontmatter + 用法）
│  ├─ 教学说明.md              分步教学
│  ├─ 拿来说明.md              拿了谁的 / 改了什么 / 没拿什么
│  ├─ scripts/c4_evaluator.py  引擎（1176 行，含 --self-test）
│  ├─ scripts/build_golden_set.py / golden_test.py
│  ├─ references/c4_rubric_v2.yaml        机器可读评分标准
│  └─ references/golden_set/              8 个人工标注 fixture
├─ c4-skill-evaluator.skill    打包件（zip）
├─ demo/                       Demo：一条命令 + 真跑出来的输出
└─ evidence/
   ├─ golden_test_v3.txt       评审器误判率实测
   ├─ reviews/                 3 份真实提交的评审输出
   ├─ skill安装验证.txt        .skill 解包到另一目录后真跑的记录
   ├─ submit_guard_audit.txt   提交前体检报告（4/4 交付物，粗估 90.0）
   └─ 审计说明_空文件与命名告警.md   ⚠️ 评审如见「空文件」红线告警请先读它
```

> ⚠️ **给评审的提示**：自检器会报 6 个"空文件"和若干命名告警，
> 全部来自黄金测试集里**故意**造的空文件 fixture（`G07_empty_files`）与第三方参考件，
> **不是交付物缺失**——说明见 `evidence/审计说明_空文件与命名告警.md`。

`_starter/`（官方 starter）与 `_upstream/`（基座技能）不进自己的 GitHub 仓库。

---

## 五、诚实清单（没做的事）

- [ ] **黄金集只有 1 个标注者（我自己）** —— 这是误判率数字的可信度上限，没有第二人可找，也不编
- [ ] **没有结构类 fixture**（带子目录的技能包）—— 本次"把 scripts/ 当作者"的 bug 就是它测不出来的
- [ ] **不用 LLM 做内容级评估** —— 刻意选择：评审器是确定性的，可复现可审计
- [ ] 不评价技能"有没有用"——那是人的活，机器不越界

---

## 六、已知缺陷（修完也要写下来）

| 缺陷 | 状态 |
|---|---|
| `scripts/` `references/` 被当成作者名 | **v3 已修**（`detect_single_skill` + `SKILL_SUBDIRS` 白名单） |
| `YOUR_` 大小写误伤 `your_file.py` | **已修**（显式 `case_sensitive`） |
| `real:true` 未实现"缺证即 fail" | **已修**（`REAL_CHECKS` 路由） |
| 负信号硬地板没接上评级 | **已修** |
| YAML 中文引号嵌套导致自检失败 | **已修** |
| 结构类输入形态无测试覆盖 | **未修**，列为 v4 第一优先项 |
