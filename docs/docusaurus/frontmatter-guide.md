---
sidebar_position: 3
title: "Docusaurus Frontmatter 完整指南"
description: "詳細解析 Docusaurus 中 docs 和 blog 支援的 frontmatter 欄位，包含實際測試結果和使用技巧。"
keywords: [docusaurus, frontmatter, metadata, seo, 文檔配置]
---

# Docusaurus Frontmatter 完整指南

記錄 Docusaurus 中各種 frontmatter 欄位的實際效果和使用技巧，避免踩坑。

## 📋 Docs vs Blog 支援差異

### 🔍 實測發現

經過實際測試，發現 Docusaurus 中 **docs** 和 **blog** 對 frontmatter 的支援不完全相同：

| 欄位 | Docs 支援 | Blog 支援 | 實際效果 |
|------|-----------|-----------|----------|
| `title` | ✅ | ✅ | 頁面標題、瀏覽器標籤 |
| `description` | ✅ | ✅ | SEO meta 標籤 |
| `keywords` | ✅ | ✅ | SEO meta 標籤 |
| `tags` | ❌ | ✅ | Blog 有標籤頁面，Docs 無效果 |
| `sidebar_label` | ✅ | ❌ | 側邊欄顯示名稱 |
| `sidebar_position` | ✅ | ❌ | 側邊欄排序 |
| `hide_title` | ✅ | ✅ | 隱藏自動生成的標題 |

## 📝 Docs 專用 Frontmatter

### 基本配置

```yaml
---
# 頁面配置
title: "自訂頁面標題"
sidebar_label: "側邊欄顯示名稱"
sidebar_position: 2
hide_title: false

# SEO 優化
description: "頁面描述，用於搜尋結果和社交分享"
keywords: [關鍵字, 陣列, 支援中英文]
image: "https://example.com/social-image.jpg"

# 進階設定
custom_edit_url: "https://github.com/user/repo/edit/main/file.md"
hide_table_of_contents: false
pagination_next: null
pagination_prev: null
---
```

### 實際效果測試

#### ✅ 有效的欄位

**`sidebar_label`**
```yaml
sidebar_label: "🎣 Hooks 指南"
```
- **效果**: 左側邊欄顯示指定文字
- **位置**: 導航欄中的連結文字
- **注意**: 支援 emoji

**`hide_title`**
```yaml
title: "我的標題"
hide_title: true
```
- **效果**: 隱藏從 frontmatter 自動生成的頁面標題
- **重要**: 不會隱藏手動寫的 `# Markdown 標題`
- **用途**: 想要自訂標題樣式時使用

**`keywords` 和 `description`**
```yaml
description: "頁面描述文字"
keywords: [react, hooks, frontend, 前端開發]
```
- **效果**: 生成 `<meta>` 標籤，提升 SEO
- **查看方式**: 瀏覽器開發者工具 → Elements → `<head>` 區域
- **支援中文**: 關鍵字可以是中英文混合

#### ❌ 無效的欄位

**`tags`** (在 docs 中)
```yaml
tags: [frontend, react]  # 無效果
```
- **問題**: Docs 不支援標籤功能
- **替代**: 使用資料夾結構組織內容
- **注意**: Blog 中 tags 完全正常

## 📖 Blog 專用 Frontmatter

### 基本配置

```yaml
---
# 文章資訊
title: "部落格文章標題"
slug: custom-url-slug
date: 2026-02-25

# 作者和分類
authors: [ypin]
tags: [技術筆記, react, frontend]

# SEO 優化
description: "文章摘要"
keywords: [關鍵字列表]
image: "https://example.com/social-image.jpg"

# 顯示設定
hide_title: false
---
```

### Blog 特有功能

**`tags` 系統**
```yaml
tags: [技術筆記, react, frontend]
```
- **效果**: 生成標籤頁面 `/blog/tags/react`
- **配置**: 需要在 `blog/tags.yml` 中定義標籤
- **導航**: 自動生成標籤索引頁

**`authors` 系統**
```yaml
authors: [ypin, guest-author]
```
- **效果**: 顯示作者資訊和頭像
- **配置**: 需要在 `blog/authors.yml` 中定義作者
- **功能**: 支援多作者協作

## 🎯 最佳實踐

### SEO 優化建議

```yaml
---
title: "具體明確的標題 (< 60 字元)"
description: "簡潔有力的描述 (115-145 字元)"
keywords: [主要關鍵字, 次要關鍵字, 相關技術, 中文關鍵字]
image: "https://your-domain.com/social-share-image.jpg"
---
```

### 標題管理策略

#### 方案 1: 使用自動標題
```yaml
---
title: "React Hooks 指南"
hide_title: false  # 預設值
---

<!-- 不要手動添加 # 標題 -->
內容直接開始...
```

#### 方案 2: 自訂標題樣式
```yaml
---
title: "React Hooks 指南"  # 用於 SEO 和瀏覽器標籤
hide_title: true           # 隱藏自動標題
---

# 🎣 React Hooks 完整學習指南

<!-- 手動控制標題樣式 -->
```

### 側邊欄優化

```yaml
---
sidebar_label: "簡短名稱"      # 側邊欄顯示
title: "完整詳細的頁面標題"    # 頁面頂部和 SEO
sidebar_position: 2           # 排序位置
---
```

## 🚨 常見問題

### Q: 為什麼 `hide_title: true` 沒有效果？

**A**: 檢查是否有手動的 `# 標題`

```markdown
<!-- 問題 -->
---
hide_title: true
---
# 我的標題    ← 這個不會被隱藏

<!-- 解決 -->
---
hide_title: true
---
直接開始內容...   ← 現在頁面頂部沒有標題了
```

### Q: 為什麼 docs 中的 `tags` 沒有作用？

**A**: Docs 不支援標籤功能，只有 Blog 支援

```yaml
# ❌ 在 docs 中無效
---
tags: [frontend, react]
---

# ✅ 改用資料夾結構
docs/
├── react/           # React 相關
├── vue/            # Vue 相關
└── javascript/     # JavaScript 相關
```

### Q: 如何檢查 SEO meta 標籤是否生效？

**A**: 使用瀏覽器開發者工具

1. 按 `F12` 打開開發者工具
2. 切換到 `Elements` 標籤
3. 搜尋 `<head>` 區域
4. 尋找 `<meta name="description">` 和 `<meta name="keywords">`

或者使用線上工具：
- [Facebook Sharing Debugger](https://developers.facebook.com/tools/debug/)
- [Twitter Card Validator](https://cards-dev.twitter.com/validator)

## 📚 參考資源

### 官方文檔
- [Docusaurus Docs Plugin API](https://docusaurus.io/docs/api/plugins/@docusaurus/plugin-content-docs)
- [Docusaurus Blog Plugin API](https://docusaurus.io/docs/api/plugins/@docusaurus/plugin-content-blog)
- [Markdown Front Matter](https://docusaurus.io/docs/markdown-features#front-matter)

### 實用工具
- [YAML Validator](https://yamlchecker.com/) - 檢查 YAML 語法
- [Meta Tags Tester](https://metatags.io/) - 測試社交分享效果

---

*這份指南基於實際測試結果編寫，會隨著 Docusaurus 版本更新而調整。*