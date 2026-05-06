# Git 基礎 LAB：多人協作、分支管理與衝突解決

## 環境需求

- 安裝 Git：https://git-scm.com/download/win
- 有 GitHub 帳號（免費）：https://github.com/signup
- 文字編輯器（VS Code 建議）

---

## LAB 1：建立第一個 Repository

### 目標：在 GitHub 建立 Repo，並在本機初始化

```bash
# Step 1：在 GitHub 網站建立新 Repo
# 進入 https://github.com → New Repository
# Repository name: MyProject
# 選 Public 或 Private → Create repository

# Step 2：本機建立資料夾並初始化
mkdir C:\GitLab\MyProject
cd C:\GitLab\MyProject
git init

# Step 3：建立第一個檔案
echo "# MyProject" > README.md

# Step 4：加入追蹤並 commit
git add .
git commit -m "first commit"

# Step 5：連結遠端並推送
git remote add origin https://github.com/你的帳號/MyProject.git
git branch -M main
git push -u origin main
```

### 預期結果
- GitHub 上看到 README.md 出現 ✅

---

## LAB 2：建立分支並開發新功能

### 目標：模擬大改版，在新分支開發，不影響 main

```bash
# 確認目前在 main
git branch
# 輸出：* main

# 建立並切換到新分支 VER2
git checkout -b VER2

# 確認已切換
git branch
# 輸出：
#   main
# * VER2   ← 星號代表目前所在分支

# 在 VER2 新增一個檔案
echo "這是 VER2 的新功能" > feature.txt

# 存檔
git add .
git commit -m "VER2：新增 feature.txt"

# 推送 VER2 到遠端
git push -u origin VER2
```

### 驗證
```bash
# 切回 main，確認 feature.txt 不存在（分支獨立）
git checkout main
dir feature.txt
# 找不到檔案 = 正確 ✅

# 切回 VER2，確認檔案存在
git checkout VER2
dir feature.txt
# 找到檔案 = 正確 ✅
```

---

## LAB 3：模擬未 commit 時 pull 的保護機制

### 目標：驗證 Git 不會蓋掉未存檔的修改

```bash
# 確保在 main 分支
git checkout main

# 修改 README.md 但不 commit
echo "我修改了但還沒存檔" >> README.md

# 嘗試 pull（故意在有未 commit 修改時拉取）
git pull origin main

# 預期輸出：
# error: Your local changes to the following files would be overwritten by merge:
#         README.md
# Please commit your changes or stash them before you merge.
# ← Git 拒絕，保護你的修改 ✅
```

### 解法一：先 commit 再 pull
```bash
git add .
git commit -m "先存檔"
git pull origin main
```

### 解法二：用 stash 暫存
```bash
# 收進抽屜
git stash

# 拉取最新
git pull origin main

# 從抽屜拿回來
git stash pop
```

---

## LAB 4：模擬合併衝突

### 目標：兩個分支修改同一行，觀察衝突如何產生與解決

```bash
# Step 1：在 main 修改 README.md 第一行
git checkout main
# 用文字編輯器把 README.md 內容改為：
# Hello World

git add .
git commit -m "main：改為 Hello World"

# Step 2：在 VER2 修改同一行
git checkout VER2
# 用文字編輯器把 README.md 內容改為：
# Hello Taiwan

git add .
git commit -m "VER2：改為 Hello Taiwan"

# Step 3：切回 main，嘗試合併 VER2
git checkout main
git merge VER2

# 預期輸出：
# CONFLICT (content): Merge conflict in README.md
# Automatic merge failed; fix conflicts and then commit the result.
```

### 解決衝突
```bash
# 開啟 README.md，會看到：
# <<<<<<< HEAD
# Hello World
# =======
# Hello Taiwan
# >>>>>>> VER2

# 手動編輯，決定保留哪個（或合併兩者）：
# Hello World and Taiwan
# 刪掉 <<<<< ===== >>>>> 標記後存檔

# 標記衝突已解決
git add README.md
git commit -m "解決合併衝突：整合 main 與 VER2"
git push
```

---

## LAB 5：邀請協作者（需兩個 GitHub 帳號）

### 目標：授權另一個帳號對 Repo 有 Write 權限

```
GitHub 網頁操作：
1. 進入 Repo → Settings
2. 左側 Collaborators → Add people
3. 輸入對方 Username 或 Email
4. 選權限：Write
5. 對方收到邀請信 → 點 Accept

對方接受後，用自己帳號 clone 並嘗試 push：
```

```bash
# 對方電腦執行
git clone https://github.com/你的帳號/MyProject.git
cd MyProject
echo "對方新增的檔案" > partner.txt
git add .
git commit -m "協作者新增 partner.txt"
git push
# ✅ 成功 push（有 Write 權限）
```

---

## LAB 6：stash 進階操作

```bash
# 查看 stash 清單
git stash list
# 輸出：
# stash@{0}: WIP on main: abc1234 上次 commit 訊息
# stash@{1}: WIP on VER2: def5678 ...

# 拿回指定的 stash
git stash pop stash@{1}

# 丟棄不需要的 stash
git stash drop stash@{0}

# 清空所有 stash
git stash clear
```

---

## 常用指令速查表

| 指令 | 說明 |
|------|------|
| `git init` | 初始化 Repo |
| `git clone <url>` | 複製遠端 Repo 到本機 |
| `git status` | 查看目前變更狀態 |
| `git add .` | 標記所有變更準備 commit |
| `git commit -m "說明"` | 存一個版本點 |
| `git push` | 推送到遠端 |
| `git pull` | 拉取遠端最新版本 |
| `git branch` | 查看所有分支 |
| `git checkout -b 分支名` | 建立並切換到新分支 |
| `git checkout 分支名` | 切換分支 |
| `git merge 分支名` | 合併指定分支到目前分支 |
| `git stash` | 暫存未 commit 的修改 |
| `git stash pop` | 還原暫存的修改 |
| `git log --oneline` | 簡潔查看 commit 歷史 |

---

## 分支視覺化指令

```bash
# 在終端機顯示分支圖
git log --oneline --graph --all

# 範例輸出：
# * a1b2c3d (HEAD -> main) 解決合併衝突
# |\
# | * d4e5f6 (VER2) VER2：改為 Hello Taiwan
# * | g7h8i9 main：改為 Hello World
# |/
# * j0k1l2 first commit
```
