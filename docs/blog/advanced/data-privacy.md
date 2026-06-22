# Cookie 同意表单配置

## 概述

Zensical 内置了原生的 Cookie 同意表单解决方案，旨在帮助网站遵守 GDPR 等数据隐私法规。在向第三方服务（如 Google Analytics）发送请求之前，系统会先请求用户明确同意。

> 💡 如果你的网站使用了 Google Analytics 或其他第三方服务，强烈建议配置 Cookie 同意表单，以符合欧盟《通用数据保护条例》（GDPR）等隐私法规的要求。

## 基本配置

在 `zensical.toml` 中添加以下配置：

```toml
[project.extra.consent]
title = "Cookie 同意"
description = """
  我们使用 Cookie 来识别您的重复访问和偏好设置，并衡量文档的有效性，
  以及用户是否能找到他们需要的内容。您的同意将帮助我们不断完善文档。
"""
```

| 配置项 | 必填 | 说明 |
|--------|------|------|
| `consent.title` | ✅ | Cookie 同意表单的标题，显示在表单顶部，必须为非空字符串 |
| `consent.description` | ✅ | 表单描述，显示在标题下方，支持原始 HTML（如链接到隐私政策页面） |
| `consent.cookies` | ❌ | 自定义 Cookie 或修改内置 Cookie 的名称和初始状态 |
| `consent.actions` | ❌ | 按钮配置，控制显示哪些按钮及顺序 |

## 内置 Cookie

Zensical 内置了以下 Cookie，无需额外配置：

| Cookie 标识 | 说明 | 默认状态 |
|-------------|------|---------|
| `analytics` | Google Analytics | 默认**开启** |
| `github` | GitHub 相关功能 | 默认**开启** |

> ⚠️ 当 Google Analytics 配置好后，Cookie 同意表单会自动添加对应的设置项。

## 自定义 Cookie

### 修改 Cookie 名称

如果需要修改内置 Cookie 的显示名称：

```toml
[project.extra.consent.cookies]
analytics = "自定义分析"
```

### 修改初始状态

如果需要在用户首次访问时不勾选某个 Cookie（需用户主动同意后才启用）：

```toml
[project.extra.consent.cookies]
[project.extra.consent.cookies.analytics]
name = "Google Analytics"
checked = false
```

### 添加自定义 Cookie

如果你的网站使用了其他第三方服务，可以添加自定义 Cookie：

```toml
[project.extra.consent.cookies]
analytics.name = "Google Analytics"
custom = "自定义追踪"
```

> ⚠️ 如果你添加了自定义 Cookie，必须显式将 `analytics` Cookie 重新添加回去，否则 Google Analytics 将不会被触发。

## 按钮配置

Cookie 同意表单提供三种按钮类型：

| 按钮标识 | 说明 |
|----------|------|
| `accept` | 接受选中的 Cookie |
| `reject` | 拒绝所有 Cookie |
| `manage` | 管理 Cookie 设置 |

默认显示 `accept` 和 `manage` 两个按钮：

```toml
[project.extra.consent]
title = "Cookie 同意"
description = "..."
actions = ["accept", "manage"]
```

如需显示全部三个按钮（接受/拒绝/管理）：

```toml
[project.extra.consent]
actions = ["accept", "reject", "manage"]
```

> 💡 如果 `actions` 中省略了 `manage` 按钮，设置面板将始终显示。

## 在页脚添加 Cookie 设置链接

为遵守 GDPR，用户应能随时更改 Cookie 设置。你可以在页脚版权声明中添加一个链接：

```toml
[project]
copyright = """
  Copyright &copy; 2025～2026 Wcowin –
  <a href="#__consent">更改 Cookie 设置</a>
"""
```

这样用户在页面底部就可以随时修改 Cookie 偏好。

## 自定义 Cookie 的 JavaScript 交互

当你添加了自定义 Cookie 后，可以通过 JavaScript 查询用户的同意状态：

```javascript
var consent = __md_get("__consent")

if (consent && consent.custom) {
  // 用户已同意自定义 Cookie
} else {
  // 用户拒绝了自定义 Cookie
}
```

将此脚本保存为 `docs/javascripts/consent.js`，并在配置中引入：

```toml
[project]
extra_javascript = [
    "javascripts/extra.js",
    "javascripts/consent.js"
]
```

## 完整配置示例

```toml
[project]
copyright = """
  Copyright &copy; 2025～2026 Wcowin –
  <a href="#__consent">更改 Cookie 设置</a>
"""

extra_javascript = [
    "javascripts/extra.js",
    "javascripts/consent.js"
]

[project.extra.consent]
title = "Cookie 同意"
description = """
  我们使用 Cookie 来识别您的重复访问和偏好设置，并衡量文档的有效性，
  以及用户是否能找到他们需要的内容。您的同意将帮助我们不断完善文档。
  <a href="/privacy/">了解更多</a>
"""
actions = ["accept", "reject", "manage"]

[project.extra.consent.cookies]
[project.extra.consent.cookies.analytics]
name = "Google Analytics"
checked = false
github = "GitHub"
```

## 与 Google Analytics 集成

当配置好 Cookie 同意表单后，Google Analytics 的触发将完全受用户同意状态控制：

1. 用户首次访问 → 弹出 Cookie 同意表单
2. 用户选择同意 → Google Analytics 正常工作
3. 用户选择拒绝 → Google Analytics 不会被加载
4. 用户随时可更改设置 → 通过页脚链接重新打开设置

这意味着你的网站只有在用户明确同意后才会收集分析数据，完全符合 GDPR 等法规要求。

## 注意事项

1. **隐私政策页面**：建议创建一个 `/privacy.md` 页面，详细说明你使用了哪些 Cookie 及它们的作用。
2. **Cookie 刷新**：用户同意或更改设置后，页面会重新加载，Cookie 状态才会生效。
3. **本地预览**：Cookie 同意表单在 `zensical serve` 本地预览时也能正常工作。
