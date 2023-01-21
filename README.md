# Semantic Versioning

I want to write a GitHub Action that automates the process of creating and re-assigning multiple GitHub repository tags.
I want to be able to support this in a repo-per-module world, and a monorepo-for-all-modules world.

## Ideas

- can use which modules (subdirectories) particular git commits have touched
- need a human to decide whether this is a `patch`, `minor`, or `major` revision
- balance how much a human needs to know to do this right, with how much of the process is automated

## Process 1

### Requirements

- a single PR touches only one module at a time
- squash every PR to a single commit
- rebase your feature branch onto main before opening your PR
- commit message includes exactly one of `patch`, `minor`, `major`

### Process

- look at which directory was affected by the commit
- ignore .github
- update the tags for that module as appropriate, so they can be referenced as `myRepo//myModule?ref=myModule/major.minor.patch`

### Initial Thoughts

- requires a specific human git process (some of this is unavoidable, especially in monorepo setting)
- module source is a bit repetitive
- we get what we wanted initially, automagic updates to instances of the modules to the users' comfort

There could be other processes, but perfect is the enemy of the good.

### semver update logic

1. if no tags exist for this module, create them starting with `0.0.1` (so, `myModule/0`, `myModule/0.0`, `myModule/0.0.1` for the monorepo situation)
2. else, find latest tags (3), noting the latest major.minor.patch.
    - If `patch` release, create `major.minor.patch+1` and move `major` and `minor` to point to this latest SHA
    - If `minor` release, create `major.minor+1.0` and move `major` to point to this latest SHA
    - If `major` release, create `major+1.0.0`
