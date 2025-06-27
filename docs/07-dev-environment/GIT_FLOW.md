# Git Flow

This document describes the Git branching and workflow strategy for the Egypt Edututor platform.

## Overview

We use a simplified Git flow:
- `main`: production-ready code
- `dev`: integration branch for new features
- `feature/*`: feature branches
- `bugfix/*`: bugfix branches
- `release/*`: release preparation
- `hotfix/*`: urgent production fixes

## Workflow
1. Create feature branch from `dev`
2. Open PR to `dev` for review
3. Merge to `main` after QA and approval
4. Tag releases on `main`
5. Hotfixes branch from `main` and merge back to `dev`

## Pull Request Checklist
- [ ] Code passes linting and tests
- [ ] PR description includes context and screenshots
- [ ] Linked to relevant issue/task
- [ ] Reviewer assigned

## Acceptance Criteria
- [ ] All code changes follow branching strategy
- [ ] PRs reviewed and approved before merge

## See Also
- [Code Style](CODE_STYLE.md)
- [Local Setup](LOCAL_SETUP.md)

