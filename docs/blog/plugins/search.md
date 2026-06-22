---
title: 搜索功能配置
date: 2025-01-22
authors:
  - name: Wcowin
    email: wcowin@qq.com
categories:
  - 插件系统
---

# 搜索功能配置

> Zensical 内置的强大搜索功能

Zensical 内置了客户端搜索功能，**无需额外配置即可使用**。搜索是内置功能，不是插件，因此不需要在 `[project.plugins]` 中配置。

## 基本配置

搜索功能默认启用。如需调整搜索行为，可以在主题配置中设置：

```toml
[project.theme]
features = [
    "search.suggest",    # 搜索建议
    "search.highlight",  # 搜索结果高亮
    "search.share",      # 搜索结果分享
]
```

## 搜索排除

### 排除整个页面

在页面的 front matter 中添加：

```markdown
---
search:
  exclude: true
---
```

### 排除页面中的某个章节

使用 `data-search-exclude` 属性：

```markdown
## 内部备注 { data-search-exclude }

这部分内容不会被搜索索引。
```

### 排除特定内容块

```markdown
<div data-search-exclude>

这部分内容不会被搜索索引。

</div>
```

## 中文搜索优化

对于中文内容，搜索默认支持中文。如需进一步优化，可以配置搜索分隔符：

```toml
[project.theme]
features = [
    "search.suggest",
    "search.highlight",
    "search.share",
]
```

搜索会自动处理中文内容，无需特殊配置。

## 注意事项

1. **搜索是内置功能**：不需要 `[project.plugins.search]` 配置
2. **客户端搜索**：所有搜索在浏览器端完成，无需后端服务
3. **自动索引**：构建时自动生成搜索索引
4. **多语言支持**：搜索支持多种语言，包括中文

## 参考资源

- [Zensical 官方文档 - 搜索](https://zensical.org/docs/setup/search/)
