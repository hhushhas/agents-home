---
name: et-lms-deploy
description: Deploy or debug Chalk releases for ET LMS. Covers `et-lms-client` (Amplify) and `et-lms-server` (Elastic Beanstalk) in us-east-1 using profile `yahya`.
---

# ET LMS Deploy

Read first:
- `/Users/macmini/Desktop/Code/et-lms/CHALK_WORKFLOW.md`

Use when:
- user says update/ship/deploy ET LMS
- Chalk version bump for ET LMS
- ET Amplify / EB / package-auth trouble during release

Ground rules:
- workspace root is not a git repo
- frontend repo: `/Users/macmini/Desktop/Code/et-lms/et-lms-client`
- backend repo: `/Users/macmini/Desktop/Code/et-lms/et-lms-server`
- default Chalk release is client-first; backend only if ET server actually changed
- trust `CHALK_WORKFLOW.md` + live AWS + live workflows over old notes

Fast path:
1. Read `CHALK_WORKFLOW.md`
2. Check dirty trees
3. Export required vars:
   - `GITHUB_PACKAGES_TOKEN`
   - `NEXT_PUBLIC_BASE_URL`
4. Frontend gate:
   - `yarn install --immutable`
   - `yarn build`
   - `npx tsc --noEmit`
5. If backend touched:
   - `yarn install`
   - `yarn ts.check`
   - `yarn build`
6. Commit scoped files only
7. Push frontend `main`, watch Amplify, verify prod
8. Only push backend if the release actually changed ET server code or env

High-signal gotchas only:
- client install/build needs `GITHUB_PACKAGES_TOKEN`
- client build effectively needs `NEXT_PUBLIC_BASE_URL`
- ET server usually does not need a Chalk package bump; do not invent one
- checked-in server workflows can be prod-centric; re-read workflow before assuming dev backend auto-deploy
- capture exact GH / Amplify run ids before watching

Escalate instead of guessing if:
- backend necessity is unclear
- dirty tree overlaps release scope
- env/branch mapping looks different from `CHALK_WORKFLOW.md`
- rollback target is ambiguous
