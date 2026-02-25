---
sidebar_position: 2
title: "Git 工作流程最佳實踐"
description: "整理日常開發中 Git 的使用技巧和最佳實踐，建立高效的版本控制工作流程，包含分支管理、提交規範等。"
keywords: [git, 版本控制, 工作流程, 分支管理, 團隊協作, 開發工具]
---

# Git 工作流程最佳實踐

整理日常開發中 Git 的使用技巧和最佳實踐，幫助建立高效的版本控制工作流程。

## 🚀 基本工作流程

### 日常開發循環

```bash
# 1. 更新本地代碼
git pull origin main

# 2. 創建功能分支
git checkout -b feature/new-feature

# 3. 開發並提交
git add .
git commit -m "Add new feature implementation"

# 4. 推送到遠端
git push origin feature/new-feature

# 5. 創建 Pull Request
# 在 GitHub/GitLab 介面操作

# 6. 合併後清理
git checkout main
git pull origin main
git branch -d feature/new-feature
```

### 分支命名規範

```bash
# 功能開發
feature/user-authentication
feature/payment-integration

# 問題修復
bugfix/login-error
hotfix/security-patch

# 文檔更新
docs/api-documentation
docs/readme-update

# 重構代碼
refactor/database-optimization
refactor/component-structure
```

## 📝 提交訊息規範

### Conventional Commits 格式

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

### 提交類型

- **feat**: 新功能
- **fix**: 錯誤修復
- **docs**: 文檔更新
- **style**: 代碼格式（不影響功能）
- **refactor**: 代碼重構
- **test**: 測試相關
- **chore**: 構建過程或輔助工具變動

### 實際範例

```bash
# 新功能
git commit -m "feat(auth): add JWT token validation"

# 錯誤修復
git commit -m "fix(api): resolve user data fetching issue"

# 文檔更新
git commit -m "docs: update installation guide"

# 重構
git commit -m "refactor(components): extract reusable button component"

# 設定變更
git commit -m "chore: update webpack configuration"
```

### 完整提交範例

```
feat(blog): add comment system

- Implement comment posting functionality
- Add comment validation and sanitization
- Create comment display components
- Add database migration for comments table

Closes #123
```

## 🌿 分支管理策略

### Git Flow

適合發布週期較長的專案：

```bash
# 主分支
main/master    # 生產環境代碼
develop        # 開發環境代碼

# 支持分支
feature/*      # 功能開發
release/*      # 發布準備
hotfix/*       # 緊急修復
```

### GitHub Flow

適合持續部署的專案：

```bash
# 簡化分支結構
main           # 主分支（隨時可部署）
feature/*      # 功能分支（從 main 分出，合併回 main）
```

### 選擇建議

- **小團隊/個人專案**: GitHub Flow
- **大團隊/企業專案**: Git Flow
- **開源專案**: Forking Workflow

## 🔧 實用 Git 技巧

### 提交歷史整理

```bash
# 交互式 rebase（整理最近 3 個提交）
git rebase -i HEAD~3

# 修改最後一次提交訊息
git commit --amend

# 將多個提交壓縮成一個
git rebase -i HEAD~3
# 在編輯器中將 'pick' 改為 'squash'
```

### 暫存變更

```bash
# 暫存當前變更
git stash

# 暫存包含未追蹤檔案
git stash -u

# 查看暫存清單
git stash list

# 恢復暫存的變更
git stash pop

# 恢復特定暫存
git stash apply stash@{1}

# 清空所有暫存
git stash clear
```

### 檢視歷史與差異

```bash
# 美化的提交歷史
git log --oneline --graph --all

# 檢視特定檔案的歷史
git log --follow -- filename

# 檢視兩個分支的差異
git diff main..feature-branch

# 檢視特定提交的變更
git show commit-hash

# 檢視檔案在特定提交的內容
git show commit-hash:path/to/file
```

### 撤銷操作

```bash
# 撤銷工作區變更
git checkout -- filename

# 撤銷暫存區變更
git reset HEAD filename

# 撤銷最後一次提交（保留變更）
git reset --soft HEAD~1

# 完全撤銷最後一次提交
git reset --hard HEAD~1

# 撤銷已推送的提交
git revert commit-hash
```

## 🛡️ Git 安全實踐

### .gitignore 最佳實踐

```gitignore
# 依賴套件
node_modules/
vendor/

# 編譯檔案
*.log
dist/
build/

# 環境設定
.env
.env.local
.env.production

# 編輯器設定
.vscode/
.idea/
*.swp
*.swo

# 系統檔案
.DS_Store
Thumbs.db

# 暫存檔案
*.tmp
*.temp
```

### 敏感資料保護

```bash
# 移除已追蹤的敏感檔案
git rm --cached sensitive-file
echo "sensitive-file" >> .gitignore
git commit -m "Remove sensitive file from tracking"

# 完全清除敏感檔案歷史（危險操作）
git filter-branch --force --index-filter \
'git rm --cached --ignore-unmatch sensitive-file' \
--prune-empty --tag-name-filter cat -- --all
```

### 提交前檢查

```bash
# 使用 pre-commit hooks
# 安裝 husky
npm install --save-dev husky

# 設置 pre-commit hook
npx husky add .husky/pre-commit "npm test && npm run lint"
```

## 🤝 團隊協作

### Pull Request 最佳實踐

1. **明確的標題和描述**
   - 簡潔地說明變更內容
   - 解釋為什麼需要這個變更

2. **小而頻繁的 PR**
   - 避免巨大的 PR
   - 每個 PR 專注於單一功能或修復

3. **程式碼審查**
   - 仔細檢查變更內容
   - 提供建設性的反饋
   - 確保符合團隊標準

### 衝突解決

```bash
# 當合併發生衝突時
git merge feature-branch
# Auto-merging file.txt
# CONFLICT (content): Merge conflict in file.txt

# 1. 編輯衝突檔案，解決衝突標記
# <<<<<<< HEAD
# 你的變更
# =======
# 他人的變更
# >>>>>>> feature-branch

# 2. 標記衝突已解決
git add file.txt

# 3. 完成合併
git commit
```

## 📊 Git 別名設定

在 `~/.gitconfig` 中添加實用別名：

```ini
[alias]
    st = status
    co = checkout
    br = branch
    ci = commit
    unstage = reset HEAD --
    visual = !gitk

    # 美化的 log
    lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit

    # 顯示分支圖
    tree = log --graph --pretty=format:'%Cred%h%Creset %Cgreen%d%Creset %s %Cblue(%an)%Creset' --abbrev-commit --all

    # 快速提交
    ac = !git add -A && git commit -m

    # 撤銷最後一次提交
    undo = reset HEAD~1 --mixed
```

## 🎯 總結

### 核心原則

1. **提交要有意義** - 每個提交都應該是一個邏輯單元
2. **訊息要清晰** - 提交訊息應該能說明變更的原因
3. **分支要簡潔** - 保持分支結構簡單易懂
4. **歷史要整潔** - 適當使用 rebase 保持歷史清晰

### 推薦工具

- **SourceTree** - Git GUI 工具
- **GitKraken** - 視覺化 Git 工具
- **VS Code Git 擴展** - 編輯器整合
- **Git Hooks** - 自動化檢查

---

*良好的 Git 工作流程是團隊協作成功的基礎，持續優化和調整以適應團隊需求。*