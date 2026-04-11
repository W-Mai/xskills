---
name: "interactive-form"
description: "结构化交互表单：弹出浏览器窗口展示富表单（单选/多选/文本/截图/文件/代码/图片预览等），收集用户的精确输入并返回 JSON 结果。当需要用户做选择、填写参数、确认方案、上传文件或查看对比时使用。"
---

# Interactive Form

当你需要向用户收集结构化输入时，**必须**使用此 skill，**禁止**在聊天里用文字列选项让用户回复字母/数字。

## 何时触发（必须使用的场景）

以下场景**全部**必须弹表单，没有例外：

- 🔀 **二选一或多选一**：方案 A vs B、风格选择、配置选项
- ✅ **确认操作**：删除、发布、重构、破坏性变更
- 📝 **收集参数**：名称、版本号、路径、配置值
- 🎯 **优先级排序**：多个任务/功能让用户排优先级
- 🔧 **配置生成**：生成配置文件前收集用户偏好
- 📋 **Review 确认**：展示变更摘要让用户确认
- 🤔 **任何不确定的事**：你拿不准的，构造表单让用户决定

**判断标准**：如果你即将写出"你选 1 还是 2"或"你想要 A 还是 B"这样的文字，**停下来，改用表单**。

## 一行命令启动

**必须使用固定路径**，这样用户只需授权一次：

```bash
python3 ~/.kiro/skills/interactive-form/form.py /tmp/interactive-form.json
```

**完整流程**：
1. 用 `write` 工具创建 `/tmp/interactive-form.json`（表单 JSON，每次覆盖同一个文件）
2. 用 `shell` 工具执行 `python3 ~/.kiro/skills/interactive-form/form.py /tmp/interactive-form.json`
3. 读取 stdout 的 JSON 结果

**关键**：路径永远是 `/tmp/interactive-form.json`，命令永远是同一条，用户授权一次后续自动通过。

## 表单 JSON 格式

```json
{
  "title": "表单标题",
  "description": "说明文字（支持 markdown）",
  "fields": [
    { "id": "choice", "type": "radio", "label": "选择方案", "options": ["A. xxx", "B. yyy"], "required": true },
    { "id": "name", "type": "text", "label": "名称", "placeholder": "输入名称" },
    { "id": "features", "type": "checkbox", "label": "功能", "options": ["X", "Y", "Z"] },
    { "id": "desc", "type": "textarea", "label": "补充说明", "rows": 3 }
  ],
  "submitText": "确认"
}
```

**注意**：脚本会自动在底部追加 `_extra` 字段（"还有别的想说的吗？"），不需要手动加。

## 支持的字段类型

| type | 说明 | 返回值 |
|---|---|---|
| `text` | 单行文本 | string |
| `textarea` | 多行文本 | string |
| `number` | 数字输入 | number |
| `radio` | 单选（最常用） | string |
| `checkbox` | 多选 | string[] |
| `select` | 下拉选择 | string |
| `slider` | 滑块 | number |
| `toggle` | 开关 | boolean |
| `image` | 粘贴/拖拽图片 | base64 data URL |
| `file` | 上传文件 | 临时文件路径 |
| `code` | 代码编辑器 | string |
| `display_image` | 只读图片展示 | (无返回值) |
| `markdown` | 只读 Markdown | (无返回值) |
| `confirm` | 确认按钮 | boolean |
| `tags` | 标签输入 | string[] |
| `color` | 颜色选择 | hex string |
| `date` | 日期选择 | ISO date string |

## 设计表单的原则

1. **选项要具体**：不要写"方案 A"，要写"A. 用 HashMap 缓存，O(1) 查找"
2. **给默认值**：用 `default` 字段预选最合理的选项，减少用户操作
3. **必填标记**：关键字段加 `"required": true`
4. **简洁优先**：一个表单 3-6 个字段最佳，超过 8 个考虑拆分
5. **radio 优先**：2-5 个选项用 radio，6+ 用 select

## 返回值处理

- 正常提交：stdout 输出 `{"field_id": value, ...}`
- 用户关闭窗口：stdout 输出 `NO_SUBMIT`
- `_extra` 字段包含用户的自由补充，**必须阅读并纳入考虑**

## 依赖

- Python 3（标准库，无第三方依赖）
- Chromium 系浏览器（--app 模式）
