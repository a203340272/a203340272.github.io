---
title: git代码统计
date: 2026-8-14 12:00:00
tags:
  - git
categories:
  - git
published: true
---
# Git代码统计与TortoiseGit常用操作

## 1. 整个仓库代码变更统计（按当前用户）

```bash
git log --all --author="$(git config --get user.name)" --pretty=tformat: --numstat \
| awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines %s, removed lines %s, total lines %s\n", add, subs, loc }'
```

- 说明：统计当前 Git 用户在所有分支的新增、删除、净增行数。
- 参数含义：
- `--all`：遍历所有分支。
- `--author=...`：按作者过滤。
- `--numstat`：输出每次提交的增删行数。
- `awk`：汇总总新增、总删除、总净增。

## 2. TortoiseGit（小乌龟）常用流程
## Git + TortoiseGit 极简速查

- 拉取：右键仓库 `TortoiseGit -> Pull...`
- 提交：右键仓库 `Git Commit -> 当前分支...`
- 推送：右键仓库 `TortoiseGit -> Push...`
- 合并：切到目标分支后 `TortoiseGit -> Merge...`
- 忽略：右键文件/目录 `TortoiseGit -> Add to ignore list`

## 2.1 拉取（Pull）

- 仓库目录右键 `TortoiseGit` -> `Pull...`
- 确认远程分支（如 `origin/main`）后执行。
- 建议：提交前先拉取，减少冲突。

## 2.2 提交（Commit）

- 仓库目录右键 `Git Commit -> "当前分支"...`
- 勾选要提交的文件。
- 填写提交信息（建议：`feat/fix/docs/chore` 开头）。
- 点击 `Commit`（仅本地）或 `Commit & Push`（提交并推送）。

## 2.3 推送（Push）

- 仓库目录右键 `TortoiseGit` -> `Push...`
- 检查本地分支到远程分支映射（如 `main -> origin/main`）。
- 点击 `Push`。

## 2.4 合并（Merge）

- 先切换到目标分支（如 `main`）：
- 右键仓库 -> `TortoiseGit` -> `Switch/Checkout...`
- 再执行合并：
- 右键仓库 -> `TortoiseGit` -> `Merge...`
- 选择来源分支（如 `feature/xxx`）并确认。
- 若有冲突：
- 打开冲突编辑器解决。
- 标记已解决后重新提交一次合并结果。

## 2.5 忽略（Ignore）

- 文件或目录右键 -> `TortoiseGit` -> `Add to ignore list`
- 可选择：
- 仅忽略当前文件。
- 按后缀忽略同类文件（如 `*.log`）。
- 推荐统一维护 `.gitignore`，示例：

```gitignore
# IDE
.idea/
.vscode/

# Build
dist/
build/

# Logs
*.log

# OS
.DS_Store
Thumbs.db
```

- 注意：`ignore` 仅对“未被跟踪”的文件生效。
- 已被跟踪文件需要先取消跟踪：

```bash
git rm -r --cached .
git add .
git commit -m "chore: apply .gitignore rules"
```

## 3. 推荐日常顺序

- `Pull`：先同步远程。
- `Commit`：本地提交变更。
- `Push`：推送到远程。
- `Merge`：按分支策略合并。
- `Ignore`：维护无关文件过滤规则。

