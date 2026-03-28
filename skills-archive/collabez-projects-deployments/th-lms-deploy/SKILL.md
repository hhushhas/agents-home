---
name: th-lms-deploy
description: Deploy or debug Chalk releases for TH LMS. Covers `th-lms-client` (Amplify) and `th-lms-server` (Elastic Beanstalk) in me-south-1 using profile `q9labs`.
---

# TH LMS Deploy

Read first:
- `/Users/macmini/Desktop/Code/th-lms/CHALK_WORKFLOW.md`

Use when:
- user says update/ship/deploy TH LMS
- Chalk version bump for Tuition Highway
- TH Amplify / EB / package-auth trouble during release

Ground rules:
- workspace root is not a git repo
- frontend repo: `/Users/macmini/Desktop/Code/th-lms/th-lms-client`
- backend repo: `/Users/macmini/Desktop/Code/th-lms/th-lms-server`
- backend first, frontend second
- trust `CHALK_WORKFLOW.md` + live AWS + live workflows over old TH docs

Fast path:
1. Read `CHALK_WORKFLOW.md`
2. Check dirty trees + branch drift in both repos
3. Export required vars before local installs:
   - `GITHUB_PACKAGES_TOKEN`
   - `NEXT_PUBLIC_BASE_URL`
4. Backend gate:
   - `yarn install --immutable`
   - `yarn ts.check`
   - `yarn build`
   - if repo deploys checked-in `dist`, verify `dist/` updated
5. Frontend gate:
   - `yarn install --immutable`
   - `yarn build`
   - `npx tsc --noEmit`
6. Commit scoped files only
7. Push backend, watch exact GH run id, verify EB version label + smoke check
8. Push frontend, watch Amplify job, verify `https://portal.tuitionhighway.com`

High-signal gotchas only:
- TH backend CI may depend on checked-in `dist/`; do not assume CI rebuilds everything for you
- TH client build can fail locally if `NEXT_PUBLIC_BASE_URL` is missing
- Amplify APIs in me-south-1 can intermittently return `InternalServerErrorException`; retry once, then inspect branch/job state
- EB can show degraded/red while deploy is still acceptable; trust workflow result + expected version label + smoke check
- old TH deploy docs are noisy; `CHALK_WORKFLOW.md` is the current playbook

Escalate instead of guessing if:
- local `main` / `dev` is behind and rollout intent is unclear
- dirty tree overlaps release scope
- EB env naming / active prod target looks inconsistent
- prod health is bad and smoke checks fail
