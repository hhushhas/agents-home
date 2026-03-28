---
name: collabdash-deploy
description: Deploy or debug Chalk releases for CollabDash. Covers `CollabDash` (Amplify) and `CollabDashServer` (EC2 + PM2) in us-east-1 using profiles `th` and `q9labs`.
---

# CollabDash Deploy

Read first:
- `/Users/macmini/Desktop/Code/collabdash/CHALK_WORKFLOW.md`

Use when:
- user says update/ship/deploy CollabDash
- Chalk version bump for CollabDash
- Amplify / EC2 / PM2 / package-auth trouble during CollabDash release

Ground rules:
- workspace root is not a git repo
- frontend repo: `/Users/macmini/Desktop/Code/collabdash/CollabDash`
- backend repo: `/Users/macmini/Desktop/Code/collabdash/CollabDashServer`
- backend first, frontend second
- trust `CHALK_WORKFLOW.md` + live AWS + live GitHub Actions over old notes

Fast path:
1. Read `CHALK_WORKFLOW.md`
2. Check dirty trees in both repos
3. Pick Chalk version from `/Users/macmini/Desktop/Code/chalk`
4. Backend gate:
   - `npm run ts.check`
   - `npm run build`
   - `npm run build:atomic`
5. Frontend gate:
   - `npm run lint`
   - `npx tsc --noEmit`
   - `npm run build`
6. Commit scoped files only
7. Push backend, watch exact GH run id, verify `https://backend.collabdash.io`
8. Push frontend, watch Amplify job, verify `https://app.collabdash.io`

High-signal gotchas only:
- package auth matters on both surfaces
  - GitHub Actions backend deploy needs repo secret `NPM_TOKEN`
  - Amplify frontend build needs app env `NPM_TOKEN`
  - repo `.npmrc` expects `${NPM_TOKEN}`
- backend deploy truth: GH Actions -> SSH -> `/opt/collabdash` -> `npm install` -> `npm run build:atomic` -> `pm2 startOrReload`
- ignore unrelated noise unless in release scope:
  - frontend `public/chalk-logo.svg`
  - backend `.yarn/`, `.yarnrc.yml`
- if Amplify fails instantly, inspect `statusReason` before touching code

Escalate instead of guessing if:
- AWS account / billing / support restriction
- package auth still fails after token rotation
- prod-risk tradeoff unclear
- dirty tree overlaps release scope in a non-obvious way
- rollback path needs judgment
