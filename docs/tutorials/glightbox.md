# GLightbox 图片灯箱

## 概述

GLightbox 是 Zensical 内置的图片灯箱扩展，点击图片即可在页面上弹出全屏浮层，支持缩放、导航切换和键盘操作。配合 [图片配置](images.md)（对齐、图片说明、懒加载、明暗模式切换图片）一起使用，能显著提升文档的阅读体验。

> 💡 灯箱功能默认对**所有图片自动启用**，无需手动添加任何 HTML 或 JavaScript。

## 快速启用

只需在 `zensical.toml` 中添加一行配置即可启用 GLightbox：

```toml
[project.markdown_extensions.zensical.extensions.glightbox]
```

启用后，Markdown 中直接写的图片会自动获得灯箱效果：

```markdown
![图片说明](image.jpg)
```

点击图片，即可在全屏浮层中查看大图，支持左右箭头切换、键盘 ESC 关闭等交互。

## 配置选项

### auto — 自动包装

控制图片是否自动被包装为可点击灯箱。

**默认启用（`auto = true`）：**
- 所有图片自动获得灯箱效果
- 可通过 `off-glb` CSS 类排除特定图片

**手动模式（`auto = false`）：**
- 只有添加了 `on-glb` 类名的图片才会启用灯箱

```toml
[project.markdown_extensions.zensical.extensions.glightbox]
auto = false
```

```markdown
![普通图片](normal.jpg)              <!-- 无灯箱效果 -->
![特殊图片](special.jpg){ .on-glb }  <!-- 有灯箱效果 -->
```

### skip_classes — 排除特定类

当 `auto = true` 时，可以通过 `skip_classes` 排除特定 CSS 类的图片：

```toml
[project.markdown_extensions.zensical.extensions.glightbox]
skip_classes = ["no-lightbox", "inline-icon"]
```

```markdown
![这张图不会弹出](no-effect.jpg){ .no-lightbox }
![内联图标](icon.svg){ .inline-icon }
```

也可以使用 `off-glb` 类快速排除单张图片（无需配置 `skip_classes`）：

```markdown
![不需要灯箱的图片](decoration.jpg){ .off-glb }
```

### auto_themed — 明暗模式图片分组

设置为 `true` 后，亮色和暗色模式的图片会被自动分到不同的灯箱图组中，支持 `#only-light` / `#only-dark` 两种 URL 后缀：

```toml
[project.markdown_extensions.zensical.extensions.glightbox]
auto_themed = true
```

```markdown
![Logo](logo-light.png#only-light)
![Logo](logo-dark.png#only-dark)
```

这样点击亮色图片时，灯箱只在该图组内切换；点击暗色图片时同理，互不干扰。

### width / height — 灯箱尺寸

设置灯箱弹出层的默认宽高（默认 `auto`，即自适应内容）：

```toml
[project.markdown_extensions.zensical.extensions.glightbox]
width = "800px"
height = "600px"
```

支持 CSS 单位：`px`、`%`、`vw`、`vh`。

### auto_caption — 自动图片说明

设置为 `true` 后，图片的 `alt` 属性会自动作为灯箱的说明文字：

```toml
[project.markdown_extensions.zensical.extensions.glightbox]
auto_caption = true
```

```markdown
![配置界面截图](screenshot.png)
```

点击图片时，灯箱底部会显示「配置界面截图」。

如果想手动指定说明文字，可通过 `data-title` 属性设置，`alt` 将被忽略：

```markdown
![配置界面截图](screenshot.png){ data-title="完整的 zensical.toml 配置示例" }
```

### caption_position — 图片说明位置

设置灯箱说明文字的默认位置，有效值：`bottom`（默认）、`top`、`left`、`right`：

```toml
[project.markdown_extensions.zensical.extensions.glightbox]
caption_position = "bottom"
```

单个图片也可以通过 `data-caption-position` 覆盖全局设置：

```markdown
![图片](image.jpg){ data-caption-position="right" }
```

## 高级用法

### 进阶属性（通过 Attribute Lists 添加）

GLightbox 支持通过 `attr_list` 扩展添加 `data-*` 属性，实现更精细的控制：

| 属性 | 说明 |
|------|------|
| `data-src` | 为灯箱指定不同于页面显示的图片源（大图） |
| `data-title` | 自定义灯箱说明文字 |
| `data-description` | 添加额外描述文字 |
| `data-caption-position` | 覆盖该图片的说明位置 |
| `data-gallery` | 手动指定图组名称，实现跨页图片分组 |

> ⚠️ 使用这些进阶属性，需要同时启用 `attr_list` 扩展。参见下方「完整配置」部分。

### 指定灯箱大图

页面中显示缩略图，点击后弹出大图：

```markdown
![缩略图](thumbnail.jpg){ data-src="large.jpg" }
```

### 手动图组分组

如果希望跨多个页面或非连续的图片属于同一个图组，可以用 `data-gallery` 手动指定：

```markdown
![架构图-1](arch-1.jpg){ data-gallery="架构图" }
![架构图-2](arch-2.jpg){ data-gallery="架构图" }
```

点击「架构图-1」后，在灯箱中可以用左右箭头导航到「架构图-2」。

## 配合图片配置使用

GLightbox 通常与 Zensical 的图片扩展一起使用，提供更完整的图片展示能力。需要在配置中额外添加：

```toml
[project.markdown_extensions.attr_list]
[project.markdown_extensions.md_in_html]
[project.markdown_extensions.pymdownx.blocks.caption]
```

这些扩展提供了：

- **attr_list** — 为图片添加 `align`、`data-*` 等属性
- **md_in_html** — 在 HTML 中使用 Markdown 语法
- **pymdownx.blocks.caption** — 为图片添加说明文字

### 带对齐的图片灯箱

```markdown
![截图](screenshot.jpg){ align=right data-title="这是右侧对齐的截图" }
```

### 带懒加载的图片灯箱

```markdown
![大图](large-image.jpg){ loading=lazy }
```

懒加载可以让图片在进入视口时才加载，配合灯箱使用，既提升页面加载速度，又不影响大图查看体验。

### 带图片说明的图片灯箱

两种写法效果相同：

**写法一（推荐）**：使用 Caption 扩展语法：

```markdown
![图片说明](image.jpg)
/// caption
图片的详细说明文字
///
```

**写法二**：使用 HTML figure 标签：

```markdown
<figure markdown="span">
  ![图片说明](image.jpg)
  <figcaption>图片的详细说明文字</figcaption>
</figure>
```

## 完整配置示例

```toml
# 基础扩展
[project.markdown_extensions.attr_list]
[project.markdown_extensions.md_in_html]
[project.markdown_extensions.pymdownx.blocks.caption]

# GLightbox 配置
[project.markdown_extensions.zensical.extensions.glightbox]
auto = true
auto_themed = true
width = "900px"
height = "auto"
auto_caption = true
caption_position = "bottom"
skip_classes = ["no-lightbox"]
```

## 交互快捷键

| 操作 | 快捷键 |
|------|--------|
| 关闭灯箱 | `Esc` |
| 上一张 | `←` / `↑` |
| 下一张 | `→` / `↓` |
| 放大 | `+` |
| 缩小 | `-` |
| 重置缩放 | `0` |
| 全屏 | `F` |

## 注意事项

1. **明暗模式图片必须配合 `auto_themed = true`**：否则两种模式的图片会被混在同一个灯箱图组中，切换时体验混乱。
2. **`auto_themed` 依赖图片 URL 的 `#only-light` / `#only-dark` 后缀**：这需要与色彩方案配置配合。如果使用了自定义配色方案，需要额外添加 CSS 选择器来隐藏对应模式的图片。
3. **GLightbox 与即时导航**：Zensical 的即时导航（`navigation.instant`）功能不会影响 GLightbox，灯箱效果在所有页面都能正常工作。
4. **移动端适配**：GLightbox 天然支持移动端，点击图片会弹出适合移动端尺寸的灯箱，支持手势滑动切换图片。
