<p align="center">
  <img src="assets/banner.png" alt="openlab-mate" width="100%">
</p>

<p align="center"><a href="README.md">English</a> | 简体中文</p>

openlab-mate 是和 AI agent 一起学习公开课的主仓库。根目录放各门课共用、逐步改进的规则和 skills；每门课有自己的仓库（笔记、lab 代码等），以 git submodule 的形式挂在 `courses/` 下。

## 目录

```
openlab-mate/
├── AGENTS.md              共用的 agent 规则；CLAUDE.md 引用它
├── skills/<名称>/          共用 skills，每个目录一个 SKILL.md
├── courses/<id>/          每门课一个 submodule；索引见 courses/README.md
├── templates/course/      新课程仓库的初始文件
└── assets/                banner
```

`.agents/skills` 和 `.claude/skills` 是指向 `skills/` 的软链接，Codex、Cursor、Claude Code 都从同一处读取共用 skills。

## 和 agent 一起用

在本仓库根目录启动 agent，不要在课程目录里启动。有些 agent 会把 submodule 当作独立的仓库根目录，从而看不到共用的规则和 skills。从根目录启动时，agent 先读 `AGENTS.md`，处理某门课前再读该课程自己的 `AGENTS.md`。

Agent 在课程里怎么配合你（讲解到什么程度、什么时候给完整解答）目前有意不做规定。某个做法好用，先写进该课程的 `AGENTS.md`；确认对其他课程也成立后，再提升到根目录的 `AGENTS.md` 或 `skills/`。

## 克隆

```bash
git clone --recurse-submodules https://github.com/Livinfly/openlab-mate.git
cd openlab-mate
git config push.recurseSubmodules check   # 课程仓库还有未推送的提交时，拒绝推送主仓库
```

克隆时没加 `--recurse-submodules` 的话，运行 `git submodule update --init`。只检出一门课：`git submodule update --init courses/<id>`。

在 Windows 上，`.agents/skills` 和 `.claude/skills` 需要作为软链接检出：打开开发者模式，并用 `git clone -c core.symlinks=true ...` 克隆，否则它们会变成普通文本文件。

## 新增课程

课程 ID 用小写的 `<学校>-<课程号>`，例如 `mit-6.5840`、`berkeley-cs61a`。

1. 新建课程仓库，复制 `templates/course/` 下的文件。填好 `AGENTS.md`（课程信息、课程对公开答案的要求、环境和命令）与 `README.md`，提交并推送。submodule 至少需要一个提交。
2. 挂到本仓库：

   ```bash
   git submodule add -b main https://github.com/Livinfly/<课程仓库>.git courses/<id>
   ```

3. 在 [`courses/README.md`](courses/README.md) 加一行，把 `.gitmodules`、`courses/<id>` 和索引一起提交。

## 日常使用

课程内容在课程仓库里提交，主仓库只记录每门课停在哪个提交。

```bash
cd courses/<id>
git switch main && git pull            # submodule 检出后处于 detached HEAD
# 正常学习、提交、推送
cd ../..
git add courses/<id>
git commit -m "Update <id>"
git push
```

拉取所有课程的最新内容（例如换了一台机器）：

```bash
git pull
git submodule update --init --remote --merge
```

## 课程政策

很多公开课要求学生不要公开 lab 或作业的答案。每门课的要求记在它的 `AGENTS.md` 里；不允许公开答案的课程，课程仓库设为私有。本仓库仍可以引用私有的课程仓库，没有权限的人只是无法检出它。

## Skills

每个共用 skill 是一个目录 `skills/<名称>/`，内含 `SKILL.md`：开头是 YAML front matter，写 `name`（与目录名一致）和 `description`，后面是具体说明。skill 在多门课里证明有用后才放到这里；只适用于一门课的内容留在该课程仓库。

## 协议

[MIT](LICENSE)。`courses/` 下的课程仓库各自声明协议，课程材料归原作者所有。
