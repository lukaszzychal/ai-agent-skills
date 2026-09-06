---
name: git-commits
description: "Rules for committing code with git. Always triggers when running git commit."
author: "Łukasz/Lukasz Zychal"
tags: ["Łukasz/Lukasz Zychal", "git", "commits", "semver", "conventional-commits"]
---

# Git Commits Rules
> **Autor / Twórca:** Łukasz / Lukasz Zychal

When using git to commit changes on behalf of the user, you MUST ALWAYS follow this rule:
1. Write the commit message entirely in **English**. Even if the user requested the conversation to be in another language (e.g. Polish), the commit message itself (`git commit -m "..."`) MUST be in English.
2. Use **Conventional Commits** for all commit messages. The format should be `<type>: <description>`. 
   Valid types include:
   - `feat:` for new features
   - `fix:` for bug fixes
   - `refactor:` for code refactoring
   - `chore:` for maintenance, dependencies, etc.
   - `docs:` for documentation updates
   - `test:` for adding or updating tests
3. Use **Semantic Versioning (SemVer)** when tagging releases:
   The format is `v[MAJOR].[MINOR].[PATCH]-[PRE-RELEASE]`.
   - Alpha releases (development/WIP milestones): `vX.X.X-alpha.X` (e.g. `v1.0.0-alpha.1`)
   - Beta releases (feature complete, testing phase): `vX.X.X-beta.X` (e.g. `v1.0.0-beta.1`)
   - Release Candidates (potential production builds): `vX.X.X-rc.X` (e.g. `v1.0.0-rc.1`)
   - Production releases: `vX.X.X` (e.g. `v1.0.0`)

4. **Tagging & Pushing Workflow**:
   - Always create annotated tags using `git tag -a vX.X.X-alpha.X -m "<Release Description>"`.
   - Always push commits and tags together using `git push origin <branch> --follow-tags` (or `git push origin --tags`).
