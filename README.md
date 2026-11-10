<p align="center">
  <img src="assets/banner.png" alt="openlab-mate" width="100%">
</p>

<p align="center">English | <a href="README.zh-CN.md">简体中文</a></p>

openlab-mate is the home repo for working through open courses together with AI agents. The root holds the rules and skills that every course shares and that get refined over time. Each course lives in its own repo (notes, lab code and so on) and is mounted here as a git submodule under `courses/`.

## Layout

```
openlab-mate/
├── AGENTS.md              shared agent rules; CLAUDE.md imports it
├── skills/<name>/         shared skills, one SKILL.md per directory
├── courses/<id>/          one submodule per course; index in courses/README.md
├── templates/course/      starting files for a new course repo
└── assets/                banner
```

`.agents/skills` and `.claude/skills` are symlinks to `skills/`, so Codex, Cursor and Claude Code all pick up the shared skills from one place.

## Working with agents

Start the agent at the root of this repo, not inside a course directory. Some agents treat a submodule as a separate repo root and would miss the shared rules and skills. From the root, the agent reads `AGENTS.md`, then reads the course's own `AGENTS.md` before working on that course.

How the agent should work alongside you in a course (how much to explain, when to give full solutions) is deliberately not fixed yet. A practice that proves useful starts in that course's `AGENTS.md`, and moves up to the root `AGENTS.md` or `skills/` once it holds across courses.

## Clone

```bash
git clone --recurse-submodules https://github.com/Livinfly/openlab-mate.git
cd openlab-mate
git config push.recurseSubmodules check   # refuse to push while a course commit is still unpushed
```

If you cloned without `--recurse-submodules`, run `git submodule update --init`. To check out a single course, run `git submodule update --init courses/<id>`.

On Windows, `.agents/skills` and `.claude/skills` only work as symlinks: enable Developer Mode and clone with `git clone -c core.symlinks=true ...`, otherwise they are checked out as plain text files.

## Add a course

Course IDs are `<school>-<code>` in lowercase, for example `mit-6.5840` or `berkeley-cs61a`.

1. Create the course repo and copy in the files from `templates/course/`. Fill in `AGENTS.md` (course info, the course's policy on publishing solutions, environment and commands) and `README.md`, then commit and push. A submodule needs at least one commit.
2. Mount it here:

   ```bash
   git submodule add -b main https://github.com/Livinfly/<course-repo>.git courses/<id>
   ```

3. Add a row to [`courses/README.md`](courses/README.md) and commit `.gitmodules`, `courses/<id>` and the index together.

## Day to day

Course work is committed in the course repo. The root only records which commit each course is at.

```bash
cd courses/<id>
git switch main && git pull            # submodules start on a detached HEAD
# study, commit and push as usual
cd ../..
git add courses/<id>
git commit -m "Update <id>"
git push
```

To pull the latest of every course, for example on another machine:

```bash
git pull
git submodule update --init --remote --merge
```

## Course policies

Many open courses ask students not to publish lab or homework solutions. Record each course's policy in its `AGENTS.md`, and keep the course repo private if solutions must not be public. This repo can still reference a private course repo; people without access just can't check it out.

## Skills

Each shared skill is a directory `skills/<name>/` with a `SKILL.md`: YAML front matter with `name` (matching the directory) and `description`, followed by the instructions. A skill lands here only after it has proven useful across courses; anything specific to one course stays in that course's repo.

## License

[MIT](LICENSE). Course repos under `courses/` carry their own licenses, and course materials belong to their original authors.
