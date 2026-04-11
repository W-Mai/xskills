---
name: code-review
description: 结构化代码审查。当用户说「review」「审查」「帮我看看这段代码」「review 这个 PR」时使用。支持审查代码片段、文件、git diff、PR。
---

# Code Review Skill

对代码进行结构化审查，输出分类明确、可操作的 review 结果。

## 使用方式

用户可能以以下方式触发：
- "review 这个文件"
- "帮我看看这段代码"
- "review 最近的 diff"
- "审查一下 src/main.rs"
- 直接贴代码让你 review

## 审查流程

### Step 0: 自动化辅助（如果可用）

检查当前环境是否有 `cha` 命令（[Cha 代码异味分析器](https://github.com/W-Mai/Cha)）：

```bash
which cha >/dev/null 2>&1 && echo "available" || echo "not found"
```

如果可用，**先跑一遍 cha 分析**获取结构化结果，作为 review 的输入：

```bash
cha analyze <待审查的文件或目录> --format json
```

将 cha 的 findings 纳入 review 输出，标注来源为 `[cha]`。cha 能覆盖的维度（复杂度、命名、重复代码、耦合、设计模式等）直接引用其结果，人工 review 聚焦 cha 覆盖不到的维度（正确性、安全性、业务逻辑）。

如果 `cha` 不可用，跳过此步骤，完全人工 review。

安装 cha（可选）：
```bash
curl --proto '=https' --tlsv1.2 -LsSf https://github.com/W-Mai/Cha/releases/latest/download/cha-cli-installer.sh | sh
```

### Step 1: 获取代码

根据用户输入确定审查范围：
- **文件路径** → 读取文件
- **贴的代码** → 直接使用
- **"最近的 diff"** → 执行 `git diff HEAD~1` 或 `git diff --cached`
- **"PR #N"** → 如果有 GitHub CLI，执行 `gh pr diff N`

### Step 2: 按 8 个维度审查

对代码逐一检查以下维度，**只报告发现问题的维度**，没问题的跳过：

#### 1. 正确性 (Correctness)
- 逻辑是否正确？边界条件处理？
- off-by-one 错误？空值/空集合处理？
- 并发安全？

#### 2. 安全性 (Security)
- 输入验证？注入风险？
- 硬编码密钥/凭据？
- 权限检查？敏感数据泄露？

#### 3. 性能 (Performance)
- 不必要的循环/分配？
- N+1 查询？
- 算法复杂度合理？

#### 4. 可维护性 (Maintainability)
- 命名清晰？职责单一？
- 魔法数字/字符串？
- 代码重复？

#### 5. 错误处理 (Error Handling)
- 异常是否被正确捕获和传播？
- 资源是否在错误时正确释放？
- 错误信息是否有用？

#### 6. 测试 (Testing)
- 是否有对应测试？
- 边界条件是否被测试覆盖？

#### 7. 文档 (Documentation)
- 公共 API 是否有文档？
- 复杂逻辑是否有注释解释 why？

#### 8. 架构 (Architecture)
- 是否符合项目现有架构模式？
- 依赖方向是否正确？
- 是否引入不必要的耦合？

### Step 3: 输出格式

使用以下格式输出，**严格遵守**：

```
## Code Review: <文件名或范围>

### 🔴 必须修复 (Must Fix)
- **[维度]** 文件:行号 — 问题描述
  → 建议修复方式

### 🟡 建议改进 (Suggestion)
- **[维度]** 文件:行号 — 问题描述
  → 建议

### 🟢 做得好 (Good)
- 简要列出代码中值得肯定的做法（1-3 条）

### 📊 总结
- 审查范围：X 个文件，Y 行代码
- 问题：N 个必须修复，M 个建议
- 整体评价：一句话
```

## 规则

1. **只报告真正的问题**，不要为了凑数而挑刺
2. **必须修复 vs 建议** 要严格区分：安全漏洞、逻辑错误是必须修复；风格偏好、可选优化是建议
3. **给出具体行号**，不要泛泛而谈
4. **解释 why**，不只是说 what
5. **承认好的做法**，review 不只是找问题
6. 如果代码质量很好，直接说"代码质量良好，没有发现需要修复的问题"，不要硬找问题

## 安装

本 skill 来自 [xskills](https://github.com/W-Mai/xskills) 仓库。如果尚未安装，执行：

```bash
git clone https://github.com/W-Mai/xskills.git /tmp/xskills && cp -r /tmp/xskills/skills/code-review ~/.kiro/skills/ && rm -rf /tmp/xskills
```

其他 IDE 请将 `~/.kiro/skills/` 替换为对应目录（参见仓库 README）。
