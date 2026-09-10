# Git 工具实用指南
> 一把 Git 钥匙，打开协作之门

---

## 目录

1. [Git 简史](#一git-简史)
2. [Git、GitHub 与 GitLab](#二gitgithub-与-gitlab)
3. [关于 License](#三关于-license)
4. [核心模型：三大工作区域](#四核心模型三大工作区域)
5. [本地库基本操作](#五本地库基本操作)
   - [git init & git clone](#git-init--git-clone)
   - [git add](#git-add)
   - [git commit](#git-commit)
   - [git status & git diff](#git-status--git-diff)
   - [git log](#git-log)
6. [版本撤销与救急](#六版本撤销与救急)
   - [git reset](#git-reset)
   - [git reflog](#git-reflog)
7. [分支流模型管理](#七分支流模型管理)
   - [git branch](#git-branch)
   - [git switch & checkout](#git-switch--checkout)
   - [git merge](#git-merge)
   - [git rebase](#git-rebase)
   - [git stash](#git-stash)
8. [多端远程同步与协作](#八多端远程同步与协作)
   - [认识 origin](#认识远程分支origin-到底是什么)
   - [git fetch vs git pull](#git-fetch-vs-git-pull)
   - [git push](#git-push)
9. [GitHub & GitLab 平台应用](#九github--gitlab-平台应用)
   - [PR / MR 的一生](#pull-request--merge-request-的一生)
   - [协作模式对比](#企业协同工作模式对比)
   - [CI/CD 流程简介](#自动化阀门现代-cicd-流程简介)
10. [冲突管理与避坑指南](#十冲突管理与避坑指南)

---

## 一、Git 简史

Git 是目前世界上最流行的**分布式版本控制系统**，其诞生充满了技术创新与实际需求的驱动。

- **2002 年以前**：Linux 内核团队以手动合并方式管理版本，效率极低。Linus Torvalds 转而使用商业工具 BitKeeper。
- **2005 年**：BitKeeper 所有者收回开源社区的免费使用权。
- **2005 年 4 月**：Linus Torvalds 用 **两周时间**，以 C 语言开发出 Git，并迅速将 Linux 内核项目切换至 Git 管理。

---

## 二、Git、GitHub 与 GitLab

| 平台 | 定位 | 核心特点 |
|------|------|----------|
| **GitHub** | 全球最大开源托管平台 | Pull Request、Issues、GitHub Actions；程序员的"作品集" |
| **GitLab** | 一站式 DevOps 解决方案 | 支持 SaaS 与私有化部署；内置强大的 CI/CD 流水线 |

> **简记**：GitHub 更适合开源社交协作，GitLab 更适合企业级一体化开发运维。

---

## 三、关于 License

在将代码托管至 GitHub/GitLab 时，需为项目选择合适的开源许可证（License），它决定了他人使用、修改和分发你代码的权利边界。

常见许可证：

| License | 特点 |
|---------|------|
| **MIT** | 最宽松，允许任意使用，需保留版权声明 |
| **Apache 2.0** | 宽松，附加专利授权条款 |
| **GPL v3** | 强 Copyleft，衍生作品须同样开源 |

---

## 四、核心模型：三大工作区域

理解三个区域的数据流转，是掌握所有 Git 撤销与提交指令的核心钥匙：

```
工作区 ──git add──▶ 暂存区 ──git commit──▶ 本地仓库
```

| 区域 | 别名 | 说明 |
|------|------|------|
| **工作区** (Working Directory) | — | 本地物理目录，直接编辑代码的场所 |
| **暂存区** (Staging Area) | Index / 索引 | 二进制缓冲文件，临时记录即将提交的变更 |
| **本地仓库** (Local Repository) | — | 包含全部提交历史、分支指针和版本快照 |

---

## 五、本地库基本操作

### git init & git clone

```bash
# 将当前目录初始化为本地 Git 仓库（创建 .git 目录）
git init

# 克隆远程仓库（含所有历史节点和分支）
git clone <url>
```

---

### git add

将工作区的文件变化缓存进暂存区，准备写入下一个版本。

```bash
git add <file>          # 单文件暂存（最安全，推荐）
git add .               # 全量暂存（当前目录及子目录所有变更）
git add -p              # 交互式拆分暂存（进阶：可选择特定行）
```

---

### git commit

```bash
git commit -m "feat: 新增手机验证登录"

# 向上一次提交追加漏掉的文件，不生成新节点
git add forgotten_file.js
git commit --amend --no-edit
```

| 功能 | 说明 |
|------|------|
| **拍摄版本快照** | 提交后暂存区内容被记录为唯一 SHA-1 哈希节点 |
| **规范化描述** | 优秀的提交消息是回溯 Bug 时的救命药 |
| **`--amend`** | 在未推送前修改最近一次提交信息或补充文件 |

---

### git status & git diff

```bash
git status                # 概括：哪些文件被追踪 / 修改 / 未追踪
git diff                  # 精细对比：工作区 vs 暂存区
git diff --cached         # 精细对比：暂存区 vs 本地仓库
```

- `git status`：高概括、低细节
- `git diff`：逐行精细对比，`+` 绿色为新增，`-` 红色为删减

---

### git log

审查全部提交历史，支持多种参数过滤和格式化：

| 参数 | 作用 | 示例 |
|------|------|------|
| `--oneline` | 单行简洁打印，哈希值缩短为7位 | `git log --oneline` |
| `-n <数量>` | 限制最近几次输出 | `git log -n 5` |
| `--graph --all` | 终端绘制分支合并关系图 | `git log --graph --oneline --all` |
| `--author` | 按提交者筛选历史 | `git log --author="Alex"` |

---

## 六、版本撤销与救急

### git reset

```bash
git reset [--soft | --mixed | --hard] <commit-id>
```

| 模式 | 保留工作区 | 保留暂存区 | 说明 |
|------|-----------|-----------|------|
| `--soft` | ✅ | ✅ | 仅回退 HEAD，代码回到暂存区，可重新组合提交 |
| `--mixed`（默认）| ✅ | ❌ | 回退 HEAD，撤回暂存区，代码保留在工作区 |
| `--hard` | ❌ | ❌ | ⚠️ 极其危险！完全清空，彻底还原到目标快照 |

---

### git reflog

> 执行了 `git reset --hard`，代码真的丢了吗？

**没有！** 只要在本地提交过，Git 会在引用日志中悄悄记录每一次 HEAD 的跳动。

```bash
$ git reflog
9fa38c1 HEAD@{0}: reset: moving to HEAD~1
f312c98 HEAD@{1}: commit: feat: 这是一个被误删的提交
a02b11e HEAD@{2}: checkout: moving to main

# 挽救方案：找到目标 commit-id，穿梭回去
git reset --hard f312c98
```

**步骤**：
1. `git reflog` 找到误删前的 `commit-id`
2. `git reset --hard <commit-id>` 恢复代码

---

## 七、分支流模型管理

### git branch

> 分支本质上只是指向特定提交的可变 SHA-1 指针，创建/删除开销极小。

```bash
git branch                  # 列出所有本地分支（* 号为当前分支）
git branch <branchname>     # 基于当前节点新建分支（不切换）
git branch -d <branchname>  # 安全删除（未完全合并时拦截）
git branch -D <branchname>  # 强制删除（无视合并状态，谨慎使用）
```

---

### git switch & checkout

```bash
# checkout（经典，同时承担分支切换与文件撤销，容易歧义）
git checkout dev          # 切换分支
git checkout -b dev       # 创建并切换
git checkout -- file.txt  # 撤销文件修改

# switch（Git 2.23+ 推荐，语义单一清晰）
git switch dev            # 切换分支
git switch -c dev         # 创建并跳转新分支
```

> 💡 **推荐使用 `git switch`** 作为日常规范。

---

### git merge

```bash
# 合并 feature 分支至主分支
git switch main
git merge feature

# 显式禁止快进合并（强行生成合并提交节点）
git merge --no-ff feature
```

Git 自动判定两种合并形态：

| 合并类型 | 触发条件 | 结果 |
|---------|---------|------|
| **Fast-forward（快进）** | 主分支自建立后无新提交 | 仅移动 HEAD 指针，不生成新节点 |
| **Three-way Merge（三方）** | 各分支均有独立提交 | 对比两末端及共同祖先，生成合并提交节点 |

---

### git rebase

将当前分支的修改"剪切"后逐个贴到目标分支最前方，使提交历史保持**完美直线**。

```bash
git rebase main
```

> ⚠️ **黄金法则**：**绝对不要对已推送到远程的公共分支执行变基！** 否则将导致其他协作者的历史全部冲突错乱。

---

### git stash

> 场景：功能开发到一半无法 commit，却需紧急切换分支修复线上 Bug。

```bash
git stash           # 1. 冷冻当前工作区和暂存区，还原干净状态
git stash list      # 2. 查看所有已保存的冷冻记录
git stash pop       # 3. 恢复最近一次藏匿，并从列表中删除
```

---

## 八、多端远程同步与协作

### 认识远程分支：origin 到底是什么？

`origin` 只是克隆或绑定的**第一个远程仓库的默认别名**，并非保留字。

```bash
# 查看所有远程端及其 URL
git remote -v

# 绑定第二个远程端（例如 GitLab 备份源）
git remote add backup git@gitlab.com:learn/git.git
```

---

### git fetch vs git pull

```bash
# fetch：只拉取，不合并（最安全）
git fetch origin
git diff main origin/main   # 手动检查差异后再决定是否合并

# pull：拉取 + 合并（= fetch + merge）
git pull origin main

# 推荐：以 rebase 方式拉取，避免污染线性历史
git pull --rebase origin main
```

| 命令 | 是否修改当前分支 | 适用场景 |
|------|----------------|---------|
| `git fetch` | ❌ 仅更新远程缓存 | 需要先审查再决策时 |
| `git pull` | ✅ 直接合并 | 快速同步，信任远端时 |

---

### git push

```bash
# 首次推送并建立追踪关系（之后可直接 git push）
git push -u origin feature-auth

# 安全强制覆盖（解决重写提交后的冲突）
git push --force-with-lease
```

| 要点 | 说明 |
|------|------|
| `-u` / `--set-upstream` | 建立本地与远程分支的追踪关系，后续可直接 `git push` |
| 推送被拒绝 | 远程有新提交时，须先 `pull` 合并无冲突后再推送 |
| `--force-with-lease` | 替代暴力 `-f`；只有远程未更新时才执行覆盖，防误操作 |

---

## 九、GitHub & GitLab 平台应用

### Pull Request / Merge Request 的一生

PR（GitHub）和 MR（GitLab）是开源社区及企业协作的核心机制：

```
1. 起源分支   → 开发者在独立 Feature 分支修改代码
2. 发起申请   → 提交完毕，发起 PR / MR
3. 代码评审   → 资深成员 + 自动化 CI 工具展开差异比对与审查
4. 自动化测试 → 触发流水线构建、扫描、验证
5. 合并入主干 → 审查通过，负责人点击合并
```

---

### 企业协同工作模式对比

| 模式 | 特点 | 适用场景 |
|------|------|---------|
| **共享仓库模型** (Shared Repository) | 所有成员为 Collaborators，拥有直接读写权限；用受保护分支规范管控 | 中小型内部团队 |
| **Fork-and-Pull 模型** | 协作者无主库写权限，须 Fork 后发起跨仓库 PR/MR | Linux、Vue、React 等大型开源项目 |

---

### 自动化阀门：现代 CI/CD 流程简介

在 GitHub Actions / GitLab CI/CD 驱动下，每次 PR 发起或推送时自动触发：

| 阶段 | 工具示例 | 目标 |
|------|---------|------|
| **自动化单元测试** | Jest、PyTest | 成功方可合并 |
| **静态安全扫描** | SAST 工具 | 排除 Token 泄漏、SQL 注入等隐患 |
| **自动构建与部署** | Docker Build + CD | 合并后自动构建镜像、热发上线 |

---

## 十、冲突管理与避坑指南

### 冲突标记符号解读

当多人修改同一行时触发冲突，打开冲突文件会看到：

```
<<<<<<< HEAD
本地当前分支的代码
=======
待合并外部分支拉回的代码
>>>>>>> dev
```

手动裁决保留正确内容，删除所有标记符，再 `git add` + `git commit` 完成合并。

---

### 预防与规避冲突的金牌守则

| 守则 | 说明 |
|------|------|
| **小步快跑** | 拒绝积累大量未提交修改，保持频繁本地提交 |
| **早拉取，勤同步** | 每天开始前先执行 `git pull --rebase` 同步最新代码 |
| **杜绝全局乱排** | 尽量避免多人并发修改公共配置文件，容易引发巨型冲突 |

---
