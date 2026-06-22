---
title: RSS 插件配置
date: 2025-01-22
authors:
  - name: Wcowin
    email: wcowin@qq.com
categories:
  - 插件系统
---

# RSS 插件配置

> 为你的 Zensical 网站添加 RSS 订阅功能

!!! warning "功能尚未实现"
    RSS 功能目前在 Zensical 的 [Tier 2 规划列表](https://zensical.org/compatibility/plugins/) 中，**尚未实现**。
    本文档中的配置暂时无效，请等待官方发布。

## 什么是 RSS？

RSS (Really Simple Syndication) 是一种网站内容聚合格式，允许用户订阅网站更新：

- ✅ **自动通知** - 用户无需手动检查更新
- ✅ **内容聚合** - 在 RSS 阅读器中统一查看
- ✅ **标准格式** - 被各种阅读器广泛支持

## 基本配置

在 `zensical.toml` 中启用 RSS 插件：

```toml
[project.plugins.rss]
```

## 配置选项

### 完整配置示例

```toml
[project.plugins.rss]
# 启用/禁用
enabled = true

# Feed 设置
feed_ttl = 1440              # 缓存时间（分钟）
date_from_meta = true        # 从 front matter 获取日期

# 内容设置
abstract_chars_count = 160   # 摘要字符数
abstract_delimiter = "<!-- more -->"  # 摘要分隔符

# 图片设置
image = "assets/logo.png"    # Feed 图标

# 分类设置
categories = ["categories", "tags"]  # 分类来源

# URL 设置
url_parameters = ""          # URL 参数
```

### 配置说明

| 选项 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `enabled` | 布尔值 | `true` | 是否启用 RSS |
| `feed_ttl` | 整数 | `1440` | Feed 缓存时间（分钟） |
| `abstract_chars_count` | 整数 | `160` | 摘要字符数 |
| `image` | 字符串 | - | Feed 图标路径 |
| `date_from_meta` | 布尔值 | `true` | 从 front matter 获取日期 |

## 使用方法

### 在文章中设置日期

确保每篇文章都有日期信息：

```markdown
---
title: 我的文章
date: 2025-01-22
---
```

### 设置摘要

使用分隔符标记摘要结束位置：

```markdown
---
title: 我的文章
date: 2025-01-22
---

这是文章摘要，会显示在 RSS Feed 中。

<!-- more -->

这部分内容不会出现在摘要中。
```

## 访问 RSS Feed

RSS Feed 通常位于以下地址：

- **主 Feed**: `https://your-site.com/feed_rss_created.xml`
- **更新 Feed**: `https://your-site.com/feed_rss_updated.xml`

## 添加 RSS 链接

在页面中添加 RSS 订阅链接：

```html
<a href="/feed_rss_created.xml">
    <span class="twemoji">📰</span> RSS 订阅
</a>
```

或在导航中添加：

```toml
nav = [
    # ... 其他导航项
    { "RSS 订阅" = "feed_rss_created.xml" },
]
```

## 推荐的 RSS 阅读器

- **Feedly** - 跨平台 RSS 阅读器
- **Inoreader** - 功能强大的 RSS 服务
- **NetNewsWire** - macOS/iOS 免费阅读器
- **Fluent Reader** - 开源桌面阅读器

## 常见问题

### RSS Feed 不更新

**问题**：发布新文章后 RSS 不更新

**解决方案**：

1. 确保文章有正确的 `date` 字段
2. 重新构建网站：`zensical build --clean`
3. 检查 Feed 缓存设置

### 摘要显示不正确

**问题**：摘要内容过长或过短

**解决方案**：

1. 调整 `abstract_chars_count` 值
2. 使用 `<!-- more -->` 手动标记摘要

## 参考资源

- [Zensical 官方文档](https://zensical.org/docs/)
- [RSS 2.0 规范](https://www.rssboard.org/rss-specification)

---

**提示**：RSS 订阅是让读者保持关注的好方法！
