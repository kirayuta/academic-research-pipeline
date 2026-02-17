# 🔬 Academic Research AI Pipeline

> 把 [Gemini Antigravity](https://blog.google/technology/google-deepmind/gemini-model-policy-updates-january-2025/) 变成你的**论文审稿导师 + 数值审计员 + 魔鬼代言人**——一键启动完整多轮迭代审稿流程。

---

## 为什么这样设计

### 核心观点：输出质量 = 模型 × 架构

直接把论文丢给 AI 说"帮我审稿"，结果一定是泛泛而谈——不是模型不够聪明，而是**没有架构**。

模型会不断升级（Gemini 1.5 → 2.0 → ...），每次升级自动提升输出质量。但**架构不会自动出现**——它是你设计的，是你对"什么是好审稿"这件事的理解的编码。

> **模型是引擎，架构是赛道。引擎年年换代，但赛道设计决定了能跑多快。**

### 为什么要分块（Chunking）

| 问题 | 解决方案 |
|------|----------|
| 一篇 8000 字论文塞给 AI，注意力被稀释，细节被忽略 | 拆成 ≤4000 token 的小块，AI 聚焦在一个章节上精审 |
| AI 倾向于给"整体还不错"的笼统评价 | 小块强迫 AI 逐句检查，无处偷懒 |
| 不同章节需要不同审查重点（结果看数据、讨论看逻辑） | 每个 chunk 可以有独立的审查策略和复杂度权重 |

### 为什么要迭代（Multi-Round Iteration）

单次审稿只能发现表面问题。真正的审稿是**多轮对话**——导师看一遍提意见，学生改完再看，改完再看。

本架构模拟的就是这个过程：
- **R1 发现问题** → **R2 修改并验证没有引入新问题** → **R3 从对立面攻击** → **R4 审计数据** → **R5 终审打磨**
- 连续 3 轮无重大问题才算通过——这不是重复劳动，而是**逐层递进**

### 为什么要角色轮换（Role Rotation）

同一个人反复看同一段文字，会产生"审美疲劳"——AI 也一样。如果 5 轮都用相同 prompt，第 3 轮开始就会重复前面的意见。

角色轮换解决这个问题：
- **导师**只管物理正确性和写作规范
- **魔鬼代言人**只管找漏洞和 alternative explanations
- **数值审计师**只管公式、量纲、统计
- 每个角色有独立的检查清单（Skill），不互相干扰

### 你可以（而且应该）修改这套架构

这套工具包不是"最终答案"——它是一个**起点**。

你完全可以让 Antigravity 帮你修改它自己的规则：

```
你: 帮我修改 research.md，把 R3 的魔鬼代言人角色改成更温和的同行评审
你: 帮我在 academic-reviewer 的检查清单里加一条：检查是否引用了 2024 年以后的文献
你: 帮我新建一个 skill，专门做化学领域的命名法检查
```

**Antigravity 可以读取并修改自己的 workflow 和 skill 文件**——所以你不需要懂编程，用自然语言告诉它你想改什么就行。

> [!TIP]
> 想法：如果你发现某类问题反复出现（比如每次都漏统计检验），就把它加到对应 skill 的检查清单里。这样你的架构会随着使用不断进化。

---

## 前提

- 已安装 [**Antigravity**](https://blog.google/technology/google-deepmind/gemini-model-policy-updates-january-2025/)（Google DeepMind 的 AI 编程助手）
- 用 Antigravity 打开一个**项目文件夹**

> [!NOTE]
> 本工具包通过 workflow 和 skill 文件扩展 Antigravity 的能力，使其能执行完整的学术论文审稿流程。无需编程基础。

---

## 部署

### 方法一：`git clone` 新建项目（推荐）

```bash
# 1. Clone 仓库作为你的工作目录
git clone https://github.com/kirayuta/academic-research-pipeline.git my-paper-review
cd my-paper-review

# 2. 把你的稿件（.txt）放进来
# Windows:
copy "C:\path\to\my_paper.txt" .
# Mac/Linux:
# cp ~/path/to/my_paper.txt .

# 3. 用 VS Code 打开
code .
```

在 Antigravity 对话中输入 `/research`，流程自动启动。

### 方法二：添加到已有项目

```bash
# 1. Clone 到临时目录
git clone https://github.com/kirayuta/academic-research-pipeline.git _temp

# 2. 复制到你的项目（Windows PowerShell）
$dst = "D:\YourProject"                                         # ← 改成你的路径
New-Item -ItemType Directory -Path "$dst\.agent\workflows" -Force | Out-Null
Copy-Item "_temp\.agent\workflows\*" "$dst\.agent\workflows\" -Recurse
Copy-Item "_temp\skills" "$dst\skills" -Recurse -Force
Copy-Item "_temp\README.md" "$dst\README.md" -Force             # 可选

# 3. 清理
Remove-Item "_temp" -Recurse -Force
```

### 验证部署

检查你的项目文件夹结构是否如下：

```
你的项目/
├── .agent/
│   └── workflows/
│       ├── research.md    ✅
│       └── imo.md         ✅
├── skills/
│   ├── academic-analyst/     ✅ (共 11 个子文件夹)
│   ├── academic-editor/      ✅
│   ├── academic-interviewer/ ✅
│   ├── academic-reviewer/    ✅
│   ├── academic-writer/      ✅
│   ├── manuscript-preprocessor/ ✅
│   ├── super-analyst/        ✅
│   ├── super-editor/         ✅
│   ├── super-fact-checker/   ✅
│   ├── super-interviewer/    ✅
│   └── super-writer/         ✅
└── your_manuscript.txt       ← 你的稿件
```

> [!IMPORTANT]
> `.agent` 是隐藏文件夹。Windows 资源管理器中需开启 **查看 → 显示 → 隐藏的项目** 才可见。

---

## 30 秒上手

部署完成后，3 步开始审稿：

```
1️⃣  将稿件（.txt）放到项目文件夹下
2️⃣  VS Code 打开项目 → 打开 Antigravity 对话框
3️⃣  输入 /research → 全自动执行，不用额外指令
```

> [!TIP]
> `/research` 是 Antigravity 的斜杠命令。AI 会自动读取 workflow 文件并按 9 步流程执行——你只需提供稿件，不用手动指引每一步。

---

## 工作原理

本工具包由 **2 层** 组成：

| 层 | 位置 | 作用 |
|----|------|------|
| **Workflow** | `.agent/workflows/*.md` | 定义 AI 执行的完整步骤流程（"做什么"） |
| **Skill** | `skills/*/SKILL.md` | 定义 AI 在每步中的角色、检查清单、输出格式（"怎么做"） |

输入 `/research` 后，Antigravity 读取 workflow → 在每个步骤中调用对应的 skill → 一切自动串联。

### 核心术语

| 术语 | 含义 |
|------|------|
| **Chunk** | 论文被拆成 ≤4000 token 的小块（A=摘要, B=引言, C/D=结果, E=讨论），逐块审稿 |
| **Frozen** | 不参与审稿的章节（Methods, References），锁定在 `frozen/` 目录 |
| **P0 / P1 / P2** | 问题优先级：P0 必须改 · P1 强烈建议改 · P2 建议改 |
| **AQ-N** | Author Query #N，需要作者确认的问题，汇总在 `author_queries.md` |
| **R1–R5** | 5 种审稿角色，每个 chunk 轮流审稿（见下表） |

---

## /research — 论文审稿

**一句话：** 把原稿变成 Nature 级终稿 + 审稿意见 + Cover Letter。

### 使用

```
# 审已有论文
你: /research

# 从主题生成论文
你: /research
你: 帮我写一篇关于 [主题] 的论文
```

### 9 步流程

| 步骤 | 名称 | 做什么 |
|------|------|--------|
| **Step 0** | 预处理 | 原稿 → Markdown 格式化 → 拆分成 chunk A–E + frozen 目录 |
| Step 1–2 | 生成草稿 | *仅从主题生成时*：初始写作 + 自我改进 |
| **Step 3** | 🔁 逐块审稿 | **核心步骤**：5 种角色轮换 × 动态轮数（详见下方） |
| **Step 4** | 质量门 | 声明-证据审计 + 风险评分 + 审稿人预测 |
| **Step 5** | 重组 | 合并所有 chunk → 一致性检查（术语/数值/引用） |
| Step 6 | 图文对齐 | 检查图表说明 vs 正文引用是否一致 |
| Step 7 | 对抗审稿 | 模拟最严苛 Reviewer #3 的全面攻击 |
| Step 8 | Cover Letter | 生成投稿信 + 意义声明 + 推荐审稿人 |

### Step 3 核心：5 种角色轮换

每个 chunk 依次经过这 5 种角色的审查：

| 轮次 | 角色 | 做什么 | 对应 Skill |
|------|------|--------|-----------|
| **R1** | 🔬✍️ 导师双轨 | 同时检查物理正确性 + 写作规范 | `academic-reviewer` |
| **R2** | 🔄 修改验证 | 将 R1 意见转化为 diff 修改，检查回归 | `academic-reviewer` |
| **R3** | 🎭 魔鬼代言人 | 攻击核心假设，查缺失对照实验 | `academic-interviewer` |
| **R4** | 📐 数值审计 | 公式推导、量纲分析、统计检验 | `academic-analyst` |
| **R5** | 📝 终审润色 | 期刊风格、影响力校准、可读性打分 | `academic-editor` |

**自动收敛机制：**
- 每个 chunk 根据复杂度计算最少轮数（3–6 轮），最多 10 轮
- 连续 3 轮无 P0 问题 → 自动通过
- 到达上限仍有问题 → 标记为"有条件接受"，问题记入 `author_queries.md`

### 使用技巧

| 技巧 | 说明 |
|------|------|
| 📄 稿件格式 | 纯文本 `.txt` 效果最好（Word → 另存为 → 纯文本） |
| 📁 一篇一目录 | 把稿件单独放在一个空子文件夹中，避免混淆 |
| 🎯 优先看什么 | 完成后**第一个打开 `author_queries.md`**——这是你的 TODO 清单 |
| 🏷️ 指定期刊 | 对话中说"目标期刊是 ACS Nano"→ AI 自动调整审稿标准 |

---

## /imo — 数学竞赛求解

```
你: /imo
你: [粘贴数学题目]
```

| 步骤 | 做什么 |
|------|--------|
| Step 1 | 专家求解：生成完整证明 |
| Step 2 | 自我改进：纠错 + 补充论证 |
| Step 3 | IMO 评委验证：逐步检查逻辑链 |
| Step 4 | 审查验证：过滤误报 |
| Step 5 | 修正：回应评委发现的问题 |
| Step 6 | 判定：连续 5 次无 Critical Error → ✅ 通过；10 次失败 → ❌ 输出最佳尝试 |

Step 3–6 循环执行，直到通过或达到上限。

---

## Skills 技能模块

每个 skill = `skills/<name>/SKILL.md`（Markdown 文件，定义角色 + 检查清单 + 输出格式）。

### /research 自动调用的

| Skill | 做什么 | 在哪步 |
|-------|--------|--------|
| `manuscript-preprocessor` | 原稿 → Markdown 分块 | Step 0 |
| `academic-reviewer` | 🔬✍️ 物理 + 写作双轨审稿 | Step 3 R1–R2 |
| `academic-interviewer` | 🎭 攻击假设、查缺失对照 | Step 3 R3 |
| `academic-analyst` | 📐 公式推导、统计审计 | Step 3 R4 |
| `academic-editor` | 📝 Nature 风格终审 | Step 3 R5 |
| `academic-writer` | 生成初始论文 | Step 1 |

### 可独立使用的

不用启动完整流程，也可以在对话中直接引用：

```
你: 用 super-fact-checker 帮我核查这段话里的数据
你: 用 academic-reviewer 的清单帮我审一下第三段
你: 用 super-analyst 分析一下这个问题
```

| Skill | 做什么 |
|-------|--------|
| `super-analyst` | 判断复杂度 → 选框架 → 系统化分析 |
| `super-editor` | 高级润色 |
| `super-fact-checker` | 事实核查 + 引用验证 |
| `super-interviewer` | 苏格拉底式深度追问 |
| `super-writer` | 高级学术写作 |

---

## 输出文件速查

运行 `/research` 完成后生成的文件：

### ⭐ 优先查看

| 文件 | 你需要做什么 |
|------|-------------|
| **`author_queries.md`** | **你的 TODO 清单**——逐条确认或回答，按 P0→P2 排列 |
| **`final_manuscript.md`** | 审稿后的终稿——搜索 `[PLACEHOLDER` 找到所有待确认处 |
| **`cover_letter_draft.md`** | 投稿信草稿——检查推荐/排除审稿人列表 |

### 📋 详细审稿记录

| 文件 | 说明 |
|------|------|
| `chunk_X_review.md` | 每个 chunk 的逐轮审稿过程 |
| `adversarial_review.md` | Reviewer #3 模拟攻击 |
| `quality_gate.md` | 质量门报告 + 风险评分 |

### 🔍 质量矩阵

| 文件 | 说明 |
|------|------|
| `claim_evidence_matrix.md` | 声明 vs 证据：类型 + 支撑强度 |
| `consistency_checklist.md` | 术语 / 数值 / 引用 一致性 |
| `novelty_statement.md` | 提炼的创新点 |
| `preflight_results.md` | 预检：过度声明、讨论结构、引用覆盖 |

### 📁 中间文件

| 文件 | 说明 |
|------|------|
| `draft_v1.md` | 格式化后的初始稿件 |
| `chunk_A/B/C/D/E.md` | 拆分后的章节块 |
| `frozen/` | 锁定章节：Methods, References, SI 等 |

---

## 高级用法

### 部分运行

```
你: /research
你: 只做 Step 0（预处理分块），不要开始审稿

你: /research
你: chunk 文件已经在了，从 Step 3 开始

你: /research
你: 跳过 Step 7（对抗审稿）
```

### 重审特定 chunk

```
你: 重新审稿 chunk_C，统计部分需要更严格
```

### 调整目标期刊

```
你: /research
你: 目标期刊是 ACS Nano
```

### 自定义 Skill

每个 `SKILL.md` 就是 Markdown 文件，可以自由修改：

- **增删检查项**：编辑 `skills/academic-reviewer/SKILL.md` 中的清单
- **新建 skill**：创建 `skills/my-new-skill/SKILL.md`
- **改角色分配**：编辑 `.agent/workflows/research.md` 中 R1–R5 的 Skill 对应关系

---

## FAQ

<details>
<summary><b>稿件格式有要求吗？</b></summary>

纯文本 `.txt` 效果最好。从 Word 导出时建议去掉页眉、页脚、页码。Markdown `.md` 也支持。
</details>

<details>
<summary><b>跑一次要多久？</b></summary>

取决于稿件长度：
- 短稿 (~3000 字)：约 20–40 分钟
- 长稿 (8000+ 字)：约 1–2 小时

主要耗时在 Step 3（逐块多轮审稿）。
</details>

<details>
<summary><b>会修改我的原文吗？</b></summary>

不会。原始 `.txt` 文件完全不受影响。AI 生成独立的 `final_manuscript.md`，所有不确定的修改用 `[PLACEHOLDER:AQ-N]` 标记，等你在 `author_queries.md` 中确认。
</details>

<details>
<summary><b>可以中途停下来吗？</b></summary>

可以。每个 chunk 的审稿结果独立保存在 `chunk_X_review.md` 中。下次新开对话，输入 `/research` 并告诉 AI "从 Step X 继续"即可。
</details>

<details>
<summary><b>报 "skill not found" 怎么办？</b></summary>

检查两点：
1. `skills/` 文件夹在**项目根目录**下（不是在 `.agent/` 里面）
2. 每个 skill 子文件夹中有 `SKILL.md` 文件
</details>

<details>
<summary><b>对话上下文用完了怎么办？</b></summary>

长稿审稿可能消耗大量 token。如果对话被截断：
1. 新开一个 Antigravity 对话
2. 输入 `/research`，告诉 AI "从 Step X 继续，chunk 文件已在文件夹中"
3. AI 会读取已有文件并接续流程
</details>

<details>
<summary><b>适用于哪些学科？</b></summary>

默认针对**物理/光学/材料**领域 + **Nature 系列期刊**优化。但通过调整目标期刊和 skill 检查清单，可适用于大部分实验科学领域。
</details>

---

## License

MIT
