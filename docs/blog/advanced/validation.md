# 链接与脚注验证

## 概述

断链是文档维护中最容易被忽视却影响最严重的问题——页面被重命名或移动后，引用静默失效，用户体验大打折扣。Zensical 在**构建时**自动验证所有内部链接和脚注，覆盖范围包括：

- 内联链接（如 `[文本](url)`）
- 引用式链接（如 `[文本][id]` + `[id]: url`）
- 脚注引用（如 `[^1]`）
- 链接定义和脚注定义
- 标题锚点

发现问题后会输出文件路径、行号和详细错误信息，让你能够快速定位并修复。

> 💡 启用 `--strict` 严格模式后，一旦发现任何问题，构建将直接中止并返回退出码 `1`，非常适合集成到 CI/CD 流水线中。

## 基本配置

链接验证**默认已启用**，且 8 种检查项全部开启：

```toml
[project.validation]
unresolved_references = true
unresolved_footnotes = true
unused_definitions = true
unused_footnotes = true
shadowed_definitions = true
shadowed_footnotes = true
invalid_links = true
invalid_link_anchors = true
```

如需完全禁用验证：

```toml
[project]
validation = false
```

## 8 种验证检查项详解

### 1. unresolved_references — 未解析的引用

**检查对象**：链接或图片引用没有对应的定义。

```toml
[project.validation]
unresolved_references = true
```

**示例：**

`index.md` 中写了引用但没有定义：
```markdown
这是一个 [未解析的引用][id]。
```

**构建输出：**
```
Warning: unresolved link reference
 ╭─[ index.md:1:35 ]
 │
 1 │ 这是一个 [未解析的引用][id]。
 │ ─┬
 │ ╰── unresolved link reference
───╯
```

### 2. unresolved_footnotes — 未解析的脚注

**检查对象**：脚注引用没有对应的脚注定义。

```toml
[project.validation]
unresolved_footnotes = true
```

**示例：**

```markdown
这是一个未解析的脚注[^id]。
```

**构建输出：**
```
Warning: unresolved footnote reference
 ╭─[ index.md:1:33 ]
 │
 1 │ 这是一个未解析的脚注[^id]。
 │ ─┬
 │ ╰── unresolved footnote reference
───╯
```

### 3. unused_definitions — 未使用的链接定义

**检查对象**：定义了链接但从未被引用。

```toml
[project.validation]
unused_definitions = true
```

**示例：**

```markdown
[id]: https://example.com
```

**构建输出：**
```
Warning: unused link definition
 ╭─[ index.md:1:2 ]
 │
 1 │ [id]: https://example.com
 │ ─┬
 │ ╰── unused link definition
───╯
```

### 4. unused_footnotes — 未使用的脚注定义

**检查对象**：定义了脚注但从未被引用。

```toml
[project.validation]
unused_footnotes = true
```

**示例：**

```markdown
[^id]: 这个脚注从未被引用。
```

**构建输出：**
```
Warning: unused footnote definition
 ╭─[ index.md:1:3 ]
 │
 1 │ [^id]: 这个脚注从未被引用。
 │ ─┬
 │ ╰── unused footnote definition
───╯
```

### 5. shadowed_definitions — 重复的链接定义

**检查对象**：同一个链接 ID 被定义了多次（后者会覆盖前者，容易造成混淆）。

```toml
[project.validation]
shadowed_definitions = true
```

**示例：**

```markdown
这个 [引用][id] 有两个定义。

[id]: https://example.com/shadowed
[id]: https://example.com
```

**构建输出：**
```
Warning: shadowed link definition
 ╭─[ index.md:3:2 ]
 │
 3 │ [id]: https://example.com/shadowed
 │ ─┬
 │ ╰── shadowed link definition
───╯
```

### 6. shadowed_footnotes — 重复的脚注定义

**检查对象**：同一个脚注 ID 被定义了多次。

```toml
[project.validation]
shadowed_footnotes = true
```

**示例：**

```markdown
这个脚注[^id] 有两个定义。

[^id]: 第一个定义
[^id]: 第二个定义
```

**构建输出：**
```
Warning: shadowed footnote definition
 ╭─[ index.md:3:3 ]
 │
 3 │ [^id]: 第一个定义
 │ ─┬
 │ ╰── shadowed footnote definition
───╯
```

### 7. invalid_links — 指向不存在的页面

**检查对象**：链接指向的页面文件不存在。

```toml
[project.validation]
invalid_links = true
```

**示例：**

```markdown
[这个页面]: non-existent.md
```

**构建输出：**
```
Warning: page does not exist
 ╭─[ index.md:3:14 ]
 │
 3 │ [这个页面]: non-existent.md
 │ ───────┬───────
 │ ╰───────── page does not exist
───╯
```

### 8. invalid_link_anchors — 指向不存在的锚点

**检查对象**：链接指向的标题锚点不存在。

```toml
[project.validation]
invalid_link_anchors = true
```

**示例：**

```markdown
[这个章节]: page.md#不存在的锚点
```

**构建输出：**
```
Warning: anchor does not exist
 ╭─[ index.md:3:31 ]
 │
 3 │ [这个章节]: page.md#不存在的锚点
 │ ──────┬─────
 │ ╰─────── anchor does not exist
───╯
```

## 严格模式（Strict Mode）

如果希望在发现问题时**直接中止构建**（退出码为 `1`），适合在 CI/CD 流水线中使用：

```bash
zensical build --strict
```

**示例输出：**

```markdown
Warning: unresolved link reference
 ╭─[ index.md:1:35 ]
 │
 1 │ 这是一个 [未解析的引用][id]。
 │ ─┬
 │ ╰── unresolved link reference
───╯

1 issue found
Aborted because --strict flag is set
```

### GitHub Actions 集成示例

在 GitHub Actions 工作流中使用严格模式，确保每次推送都验证链接：

```yaml
name: ci
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      - name: Install Zensical
        run: pip install zensical
      - name: Build site with strict validation
        run: zensical build --strict
```

## 转义字符

如果想用方括号包裹一段文字但不创建链接，可以用反斜杠转义开括号：

```markdown
这不是一个 \[链接](https://example.com)。
```

> 💡 即使在没有链接定义的情况下转义也是推荐的做法，可以避免后续添加定义时意外创建链接，同时防止验证系统误报未解析的引用。

## 注意事项与已知限制

### 临时限制

1. **Autoref 兼容性**：目前使用 Autoref 插件的链接会被报告为「未解析的引用」，团队正在解决中。
2. **不要嵌套方括号**：链接文本中不要使用嵌套方括号，例如 `[某[nested]](href)` 或 `[某[nested]][id]`。
3. **基于正则而非 AST**：当前链接检测依赖于正则表达式（与 Python Markdown 内部机制相同），这是临时限制。Zensical 计划迁移到 CommonMark 后将提供完整的 AST，届时验证将更加准确。

### 常见场景

#### 多语言站点

如果你有多语言版本的文件（如 `page.en.md` 和 `page.zh.md`），跨语言链接可能会触发 `invalid_links` 警告。这是预期行为——验证系统按单语言站点处理。可以在 `zensical.toml` 中针对性调整验证规则，或在 CI 中暂时降低验证严格度。

#### 自定义页面钩子

如果你的文档系统包含动态生成的页面（如通过插件生成的标签页、博客索引页），这些页面可能无法被验证系统追踪。建议对这些页面使用 `data-raw` 标记或加入验证排除列表。

## 推荐配置

对于大多数项目，建议保持默认配置（8 项全开），并在 CI/CD 中启用 `--strict` 模式：

```toml
# 开发环境：宽松模式，仅警告
[project.validation]
unresolved_references = true
invalid_links = true

# 生产构建通过 CI 的 --strict 来严格检查
```

或者在本地开发时运行 `zensical serve`（验证功能在预览模式下同样生效），发现问题随时修复。
