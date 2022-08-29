---
layout: default
title: Git Commit Convention
nav_order: 4
---


# Git Commit Convention

Try to use [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/) as much as possible.


Short description: A commit should have a `prefix(scope): description`.

Common prefixes:

```
fix:
feat:
docs:
build:
chore:
ci:
style:
refactor:
perf:
test:
```

Scope examples:
```
(api)
(lang)
(users)
(db model)
```

After the `description` add at least 2 new lines and you can start a more detailed description there.
