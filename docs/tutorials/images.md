# 图片配置

## 概述

图片是 Markdown 的核心语法之一，但在实际使用中常常遇到对齐、说明文字、懒加载等问题。Zensical 提供了一套完整的图片处理方案，让你能够轻松控制图片的展示效果。

> 💡 配合 [GLightbox 图片灯箱](glightbox.md) 使用，点击图片即可全屏查看，体验更佳。

## 前置配置

要使用图片的高级功能，需要在 `zensical.toml` 中启用以下扩展：

```toml
[project.markdown_extensions.attr_list]
[project.markdown_extensions.md_in_html]
[project.markdown_extensions.pymdownx.blocks.caption]
```

| 扩展 | 作用 |
|------|------|
| `attr_list` | 为图片添加 `align`、`loading` 等属性 |
| `md_in_html` | 在 HTML 标签中使用 Markdown 语法 |
| `pymdownx.blocks.caption` | 为图片添加说明文字（图注） |

## 图片对齐

启用 `attr_list` 后，可以通过 `align` 属性控制图片对齐方式：

### 左对齐

```markdown
![图片标题](https://dummyimage.com/600x400/eee/aaa){ align=left }
```

图片会浮动到左侧，文字环绕在右侧。如果视口空间不足（如移动端），图片会自动拉伸到全宽。

### 右对齐

```markdown
![图片标题](https://dummyimage.com/600x400/eee/aaa){ align=right }
```

图片会浮动到右侧，文字环绕在左侧。

> ⚠️ **为什么没有居中对齐？**
> HTML5 中 `align` 属性已被弃用，且不支持 `center` 值。如需居中显示图片，请使用下方的「图片说明」语法，图注默认居中显示。

## 图片说明（图注）

Markdown 原生语法不支持图片说明，但 Zensical 提供了两种替代方案：

### 方案一：Caption 扩展（推荐）

使用 `pymdownx.blocks.caption` 扩展的语法：

```markdown
![图片标题](https://dummyimage.com/600x400/){ width="300" }
/// caption
这是图片的详细说明文字
///
```

**效果：**
- 图片下方显示居中的说明文字
- 说明文字与图片作为一个整体（figure）渲染
- 支持多行说明文字

### 方案二：HTML 标签

使用原生的 `figure` 和 `figcaption` 标签：

```markdown
<figure markdown="span">
  ![图片标题](https://dummyimage.com/600x400/){ width="300" }
  <figcaption>这是图片的详细说明文字</figcaption>
</figure>
```

> 💡 添加 `markdown="span"` 属性后，可以在 `figure` 标签内部使用 Markdown 语法。

## 图片懒加载

现代浏览器支持通过 `loading=lazy` 属性实现原生懒加载：

```markdown
![图片标题](https://dummyimage.com/600x400/){ loading=lazy }
```

**作用：**
- 图片进入视口（viewport）时才开始加载
- 提升页面初始加载速度
- 不支持懒加载的浏览器会自动降级为立即加载

> 💡 建议对页面下方的长图、大图使用懒加载，首屏关键图片保持默认加载即可。

## 明暗模式图片

如果你的站点配置了[色彩方案切换](../theme-customization.md)，可以为不同模式准备不同的图片：

```markdown
![Logo](logo-light.png#only-light)
![Logo](logo-dark.png#only-dark)
```

**URL 后缀说明：**
- `#only-light` — 仅在亮色模式显示
- `#only-dark` — 仅在暗色模式显示
- `#gh-light-mode-only` / `#gh-dark-mode-only` — GitHub 兼容写法

### 自定义配色方案的支持

如果你使用了[自定义配色方案](../theme-customization.md)，需要在 CSS 中添加对应的选择器：

**亮色方案：**

```css
[data-md-color-scheme="custom-light"] img[src$="#only-dark"],
[data-md-color-scheme="custom-light"] img[src$="#gh-dark-mode-only"] {
  display: none; /* 在亮色模式下隐藏暗色图片 */
}
```

**暗色方案：**

```css
[data-md-color-scheme="custom-dark"] img[src$="#only-light"],
[data-md-color-scheme="custom-dark"] img[src$="#gh-light-mode-only"] {
  display: none; /* 在暗色模式下隐藏亮色图片 */
}
```

> ⚠️ 将 `"custom-light"` 和 `"custom-dark"` 替换为你实际的配色方案名称。

## 图片尺寸控制

通过 `attr_list` 扩展，可以为图片设置宽高：

```markdown
![图片](image.jpg){ width="300" height="200" }
![图片](image.jpg){ width="50%" }
![图片](image.jpg){ width="80vw" }
```

支持的单位：
- `px` — 像素（如 `width="300"`）
- `%` — 百分比（相对于容器宽度，如 `width="50%"`）
- `vw` — 视口宽度百分比（如 `width="80vw"` = 视口宽度的 80%）
- `vh` — 视口高度百分比（如 `height="50vh"` = 视口高度的 50%）

**常用场景：**
- 固定宽度：`width="400"`（适合截图、示意图）
- 响应式宽度：`width="100%"` 或 `width="80%"`（适合大图、banner）
- 视口适配：`width="90vw"`（适合全宽展示的图片）

## 灯箱与缩放

Zensical 内置 [GLightbox](glightbox.md) 扩展，点击图片即可在全屏浮层中查看：

```markdown
![图片说明](image.jpg)
```

启用 GLightbox 后，所有图片自动获得点击放大功能，支持：
- 全屏查看
- 缩放控制
- 键盘导航（左右箭头切换、ESC 关闭）
- 图组浏览

详见 [GLightbox 图片灯箱](glightbox.md) 文档。

## 完整配置示例

```toml
# 图片相关扩展
[project.markdown_extensions.attr_list]
[project.markdown_extensions.md_in_html]
[project.markdown_extensions.pymdownx.blocks.caption]

# GLightbox 灯箱（可选）
[project.markdown_extensions.zensical.extensions.glightbox]
auto = true
auto_caption = true
```

## 综合示例

以下是一个包含多种特性的图片示例：

```markdown
![架构图](architecture.png){ align=right width="400" loading=lazy }
/// caption
系统架构图 - 展示各组件间的数据流向
///
```

**效果：**
- 图片右对齐，文字环绕左侧
- 宽度固定为 400px
- 懒加载，进入视口时才加载
- 下方显示居中的说明文字
- 点击可全屏查看（如启用了 GLightbox）

## 注意事项

1. **图片路径**：建议使用相对路径（如 `images/screenshot.png`），便于本地预览和部署。
2. **图片格式**：WebP 格式体积更小，推荐使用；如需兼容性，可准备 JPEG/PNG 备用。
3. **图片压缩**：大图片建议压缩后再使用，可使用 [TinyPNG](https://tinypng.com/) 等工具。
4. **可访问性**：始终为图片添加有意义的 `alt` 文本，提升无障碍访问体验。
