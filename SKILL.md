---
id: github-branch-access
name: GitHub Branch Access
description: Provides guidance for AI assistants to access and work with the vpk-180-bringup GitHub repository branch.
applyTo:
  - "/**"
---

# GitHub Branch Access Skill

This repository is initialized as a Git repository and pushed to GitHub at:

- `https://github.com/knightkim-sketch/vpk-180-bringup.git`

The local branch is currently `master`.

## Purpose

Allow Codex, Claude, or other AI assistants to understand the GitHub repository context and branch details for the current project.

## Instructions

- When asked to inspect or modify repository files, assume the repository is the GitHub repository above.
- Use the branch `master` for current branch operations unless the user explicitly requests a different branch.
- If remote operations are needed, the remote named `origin` points to the GitHub repository above.

## Notes

- This file is a local skill prompt helper and does not directly change GitHub access permissions.
- Actual GitHub repository permissions must still be managed through GitHub settings and access controls.
