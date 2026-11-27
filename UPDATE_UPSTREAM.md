# 同步上游仓库（GitHub）更新命令指南

本项目的 Git 远程仓库与分支结构如下：

- **`upstream`**：上游原作者仓库（`https://github.com/N3urda/hongguoTV.git`）
- **`origin`**：你自己的 GitHub 仓库（`https://github.com/zysaii/hongguoTV.git`）
- **`main` 分支**：用于跟进上游 `upstream/main` 最新代码
- **`my-custom` 分支**：你的定制分支（包含 `hongguoTV-zy` 改名、全局跳过片头片尾、触控手势与全屏旋转、固定签名 `release.jks` 以及自动编译工作流）。**向 `origin/my-custom` 推送代码会自动触发 GitHub Actions 打包 APK。**

---

## 一、标准更新命令（在项目根目录执行）

打开终端（PowerShell / Git Bash），进入项目目录 `e:\zy-wj\vue\hongguo_tv\hongguoTV`，按顺序执行以下命令：

```powershell
# 1. 确保工作区干净（如有未提交的修改先暂存或提交）
git status

# 2. 拉取上游仓库的最新提交和标签
git fetch upstream

# 3. 同步本地 main 分支到上游最新状态，并更新你自己的 origin/main
git checkout main
git merge --ff-only upstream/main
git push origin main

# 4. 切回定制分支 my-custom，将上游更新合并进来
git checkout my-custom
git merge upstream/main

# 5. 推送合并后的 my-custom 到 GitHub（会自动触发编译最新 APK）
git push origin my-custom
```

### 一行快捷版

> Windows PowerShell 5.1 不支持 `&&`，请使用下方**分号 `;` 版本**；若使用 Git Bash 或 PowerShell 7+，可将 `;` 替换为 `&&`。

```powershell
git fetch upstream; git checkout main; git merge --ff-only upstream/main; git push origin main; git checkout my-custom; git merge upstream/main; git push origin my-custom
```

---

## 二、常用辅助命令

```powershell
# 如果尚未配置过 upstream 远程仓库（仅首次需要）
git remote add upstream https://github.com/N3urda/hongguoTV.git

# 查看当前配置的远程仓库
git remote -v

# 查看上游比当前分支多出的最新提交
git log --oneline HEAD..upstream/main
```

---

## 三、如果合并（`git merge upstream/main`）出现代码冲突怎么办？

由于 `my-custom` 分支修改了 `MainActivity.kt`、`build.gradle.kts` 等文件，上游恰好也改动同一位置时会提示 `CONFLICT`：

1. 查看冲突文件列表：
   ```powershell
   git status
   ```
2. 打开冲突文件，找到 `<<<<<<< HEAD`、`=======`、`>>>>>>> upstream/main` 标记，**结合两边修改保留你的定制功能与上游的新修复**（注意保留 `hongguoTV-zy` 应用名、跳过片头片尾、全屏旋转与触控逻辑、`release.jks` 签名配置）。
3. 解决完所有冲突后，标记完成并提交推送：
   ```powershell
   git add -A
   git commit -m "chore: 合并上游更新"
   git push origin my-custom
   ```
4. 如果想放弃本次合并回到合并前状态：
   ```powershell
   git merge --abort
   ```
