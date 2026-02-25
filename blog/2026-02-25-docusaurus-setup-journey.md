---
slug: docusaurus-setup-journey
title: 從零開始建立 Docusaurus 網站：完整設置紀錄
authors: [ypin]
tags: [docusaurus, tech-notes, website-journal]
---

今天終於開始架設自己的技術部落格了！選擇了 Docusaurus 作為框架，這篇文章記錄了從初始化專案到完成基本設置的完整過程。

<!-- truncate -->

## 為什麼選擇 Docusaurus？

在決定建立個人技術部落格時，我考慮了幾個選項：
- **Hexo**: 熟悉的靜態網站生成器
- **Gatsby**: React 生態豐富
- **Docusaurus**: Facebook 出品，專為文件和部落格設計

最終選擇 Docusaurus 的原因：
1. **開箱即用** - 無需複雜配置就能快速啟動
2. **React 原生支持** - 可以輕鬆整合 React 組件
3. **文件 + 部落格** - 同時支持技術文件和部落格文章
4. **現代化設計** - 預設主題美觀且響應式
5. **GitHub Pages 友好** - 部署簡單

## 設置過程記錄

### 1. 初始化專案

```bash
npx create-docusaurus@latest myDocusaurus classic --typescript
cd myDocusaurus
npm install
```

選擇 TypeScript 版本是為了更好的開發體驗和類型安全。

### 2. 啟動開發服務器

```bash
npm start
```

第一次看到 `http://localhost:3000` 的預設頁面時還是很興奮的！整個過程非常順暢。

### 3. 個人化設置

接下來需要將預設的 "My Site" 改為個人品牌。主要修改 `docusaurus.config.ts` 檔案：

```typescript
const config: Config = {
  title: 'ypin.dev',
  tagline: '分享技術筆記與生活思考的角落',

  // GitHub Pages 設置
  url: 'https://nicole_jiang.github.io',
  baseUrl: '/myDocusaurus/',
  organizationName: 'nicole_jiang',
  projectName: 'myDocusaurus',

  // 更新導覽列
  navbar: {
    title: 'ypin.dev',
    // ...
  },

  // 更新版權資訊
  footer: {
    copyright: `Copyright © ${new Date().getFullYear()} ypin.dev. Built with Docusaurus.`,
  },
};
```

### 4. Git 版本控制

```bash
git add .
git commit -m "Initial Docusaurus project setup"
```

好習慣是隨時提交進度，避免工作丟失。

### 5. 添加作者資訊

在 `blog/authors.yml` 中加入自己的資訊：

```yaml
ypin:
  name: Nicole Jiang
  title: Developer & Blogger at ypin.dev
  url: https://github.com/nicole_jiang
  image_url: https://github.com/nicole_jiang.png
  page: true
```

## 遇到的小驚喜

### 熱重載超快速
修改配置檔案後，開發服務器立即自動重新編譯，而且速度很快！這讓開發體驗非常流暢。

### 預設內容很實用
Docusaurus 預設提供了很多範例內容：
- 部落格文章範例
- 文件結構範例
- 各種 Markdown 功能展示

這些範例不只是展示功能，還教會了我如何組織內容。

### 配置簡潔但功能強大
`docusaurus.config.ts` 的配置結構很直觀，基本設置只需要修改幾個關鍵欄位，但同時提供了豐富的自訂選項。


## 總結

Docusaurus 的設置過程比想像中簡單，大約 30 分鐘就完成了基本配置。它確實做到了「開箱即用」，讓我能專注於內容創作而不是技術細節。

對於想要快速建立技術部落格的開發者，我會推薦 Docusaurus。特別是如果你：
- 熟悉 React 生態
- 需要同時管理文件和部落格
- 希望有現代化的閱讀體驗
- 想要簡化部署流程

期待在這個新家分享更多技術學習心得！ 如果你也在考慮建立技術部落格，歡迎留言交流！
