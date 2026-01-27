# SnwHist

这是一个供 Codex 之间交接与检索的统一接口仓库。按下面步骤即可开始使用。

## 使用步骤

1. 克隆仓库到本机（通常放在 `$HOME/` 下）。
   - 若需要同时拉取子仓库：
     `git clone --recurse-submodules git@github.com:ShengNW/SnwHist.git`
2. 复制下面这段提示词到需要继承背景信息的 Codex 会话中（按实际存放目录修改）：

```
对话历史存放在$HOME/SnwHist中，请阅读index.md找到与本次对话相关的交接文件
```

# 更新子仓库（人工）

1. 进入 SnwHist 根目录。
2. 拉取子仓库最新提交并更新子模块指针：

```
git -C <子仓库路径> fetch origin
git -C <子仓库路径> checkout main
git -C <子仓库路径> pull --ff-only
git add <子仓库路径>
git commit -m "Update submodule <子仓库名>"
git push
```

> 若子仓库默认分支不是 `main`，请改为实际分支名（如 `master`）。

## 更新脚本（复制即用）

将以下脚本保存为 `update_submodule.sh`，执行 `chmod +x update_submodule.sh`，然后在 SnwHist 根目录运行：

```
./update_submodule.sh <子仓库路径或名称>
```

脚本内容：

```
#!/usr/bin/env bash
set -euo pipefail

if [ "$#" -ne 1 ]; then
  echo "Usage: $0 <submodule_path_or_name>"
  exit 1
fi

path="$1"

if [ ! -d "$path/.git" ]; then
  echo "Submodule not found: $path"
  exit 1
fi

git -C "$path" fetch origin

branch="$(git -C "$path" symbolic-ref --short -q refs/remotes/origin/HEAD | sed 's|^origin/||')"
if [ -z "$branch" ]; then
  if git -C "$path" show-ref --verify --quiet refs/remotes/origin/main; then
    branch="main"
  else
    branch="master"
  fi
fi

git -C "$path" checkout "$branch"
git -C "$path" pull --ff-only origin "$branch"

git add "$path"
echo "Updated submodule pointer for $path. Now commit and push in SnwHist."

# ignore this script in parent git repo if applicable
if [ -f ".gitignore" ]; then
  grep -qxF "update_submodule.sh" .gitignore || echo "update_submodule.sh" >> .gitignore
fi
```
3. 在 `index.md` 中找到与本次对话相关的交接文件或子目录。
4. 需要创建新的交接仓库时，让使用的 Codex 参考 `Interface.md`。

## 人工推送子仓库（简要步骤）

1. 在 GitHub 创建空仓库（使用 SSH）。
2. 进入子仓库目录：`./SnwHist/<子仓库名>_repo/`。
3. 执行：

```
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin git@github.com:USER/REPO.git
git push -u origin main
```

## 一键脚本（复制即用）

将以下脚本保存为 `init_subrepo.sh`，执行 `chmod +x init_subrepo.sh`，然后在子仓库目录中运行：

```
./init_subrepo.sh <repo_name> <remote_ssh_url>
```

脚本内容：

```
#!/usr/bin/env bash
set -euo pipefail

if [ "$#" -ne 2 ]; then
  echo "Usage: $0 <repo_name> <remote_ssh_url>"
  exit 1
fi

repo_name="$1"
remote_ssh_url="$2"

if [ ! -d ".git" ]; then
  git init
fi

git add .
git commit -m "init" || true
git branch -M main
git remote remove origin >/dev/null 2>&1 || true
git remote add origin "$remote_ssh_url"
git push -u origin main

# ignore this script in parent git repo if applicable
if [ -f "../.gitignore" ]; then
  grep -qxF "init_subrepo.sh" ../.gitignore || echo "init_subrepo.sh" >> ../.gitignore
fi
```
