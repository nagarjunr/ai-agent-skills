# Git and GitHub operations

Git and GitHub operations are cheap to run and expensive to undo. These are the git-specific steps behind the general rules in `../SKILL.md`: look first, back up, do the smallest operation that works, then prove the result.

## 1. Look before touching

```bash
git status -sb; git remote -v; git branch -a
git log --oneline origin/main..HEAD        # what is unpushed
git log --oneline HEAD..origin/main        # what you would lose or merge
gh repo view OWNER/REPO --json visibility,forkCount,defaultBranchRef
gh api repos/OWNER/REPO/branches --jq '[.[].name]'   # other branches, PRs, tags
```

If local and remote show unrelated histories (`ahead N, behind M` with no merge base), the remote was rewritten. Do not assume your commits are the ones to keep: check whether they exist elsewhere (an archive repo) before discarding.

## 2. Public versus private triage

Before anything goes to a public repo, read it and decide whether it belongs there.

- Scan the files for secrets, tokens, keys, internal hostnames and IPs, personal paths and emails, and unpublished work. Use real `grep` plus `find` over the whole tree: some shells alias `grep` to a tool that skips gitignored folders.
- Generic methods and tools are fine to publish. Personal infrastructure notes, measurements of one person's hardware, unpublished research workflow, credentials, and anything with course answers or private data belong in a private repo.
- When moving a file from public to private: add it to the private repo first, verify, then remove it from the public one. Removing it in a later commit does not remove it from history (see section 4).

## 3. Backup first, then the smallest operation

- Keep the old state reachable: `git branch backup/pre-<op>-<shortsha> <branch>` or push to an archive repo.
- To match upstream exactly, use `git fetch && git reset --hard origin/<branch>` (after a clean `git status` and a backup branch), not a merge that leaves the branch ahead.
- Squashing public history to one clean commit: `git checkout --orphan clean && git add -A && git commit`, then inspect `git ls-files` and scan the tree before pushing.

## 4. Force-push only when asked, and do it safely

Never force-push on your own initiative. When the user has asked for it:

```bash
EXPECT=$(git rev-parse origin/main)          # the FULL sha you inspected
git push --force-with-lease=main:$EXPECT origin main:main
```

- Use the full SHA. An abbreviated or mistyped one is rejected ("cannot parse expected object name").
- Do not hide errors with `|| true`. A failed push that looks like success is how stale remotes survive.
- After a rewrite, delete leftover remote branches that still point at old commits.
- Be honest about limits: old commits stay reachable by SHA, and merged PR refs (`refs/pull/N/head`) remain, until GitHub garbage-collects or support purges them. Check with `gh api repos/OWNER/REPO/commits/<oldsha>`.

## 5. Renaming a repo

Do it in this order, verifying each step:

1. Check the new name is free on GitHub and locally.
2. `gh repo rename NEW -R OWNER/OLD --yes`, then `git remote set-url origin <new url>`. The old URL redirects.
3. Update the README title and the GitHub description; push.
4. Move the local folder. Then fix anything that hard-coded the old path: virtualenv scripts (`.venv/bin/pip`, `activate`, `pyvenv.cfg`), local permission or settings files, symlinks, and any agent memory keyed by folder path. Use `find` plus the real `grep` to confirm nothing still mentions the old path, including gitignored files.
5. A running agent session keeps its old working directory; tell the user to restart it from the new folder.

## 6. Authorship and hygiene

- Check `git config user.name`, `user.email` and `git log -1 --format='%an <%ae>'` before the first push in a repo. Wrong author emails are annoying to fix once pushed.
- Add AI co-author trailers or "generated with" lines only if the user wants them. If they do not, omit them from commit messages and pull request text.
- Do not commit `.env`, tokens, `settings.local.json`, virtualenvs, caches or large downloads. Check `.gitignore`.

## 7. Prove it

After every push or rename:

```bash
git fetch && git status -sb                       # in sync, no ahead/behind
git rev-parse HEAD; git ls-remote origin HEAD     # same sha
gh repo view OWNER/REPO --json visibility,url     # visibility is what you expect
```

Report what you verified and what you could not (for example, GitHub-side caches).
