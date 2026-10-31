---
type: decision-log
tags: [moove, moove-assure, decision]
---
# Moove Assure decisions log
## 2026-10-31: Hosting
- Decision: Next.js web app on Vercel; Fastify API and Postgres (pgvector) on Railway.
- Why: Vercel suits Next.js; the API is an always-on server, which suits Railway.
- Preview: https://moove-assure.vercel.app, fictional demo data only (Halcyon Bank, Lumen Mutual). Demo sign-in and seed are blocked in production.

## 2026-10-31: PRs #9 to #18 merged
- [#9](https://github.com/MalcolmGov/Moove-Assure/pull/9) Close Phase 1 with scored drafts and request changes
- [#10](https://github.com/MalcolmGov/Moove-Assure/pull/10) Add CP7 scanner evidence and Slack and Teams approval cards
- [#11](https://github.com/MalcolmGov/Moove-Assure/pull/11) Add hybrid search and enforce access labels
- [#12](https://github.com/MalcolmGov/Moove-Assure/pull/12) Add SAML, SCIM, and CP9 release evidence
- [#13](https://github.com/MalcolmGov/Moove-Assure/pull/13) Add control mapping, runbooks, and a demo self-release
- [#14](https://github.com/MalcolmGov/Moove-Assure/pull/14) Polish the web app for enterprise demos
- [#15](https://github.com/MalcolmGov/Moove-Assure/pull/15) Add a Railway and Vercel preview deploy
- [#16](https://github.com/MalcolmGov/Moove-Assure/pull/16) Fix the API image TypeScript config copy
- [#17](https://github.com/MalcolmGov/Moove-Assure/pull/17) Rebuild the public landing page for enterprise buyers
- [#18](https://github.com/MalcolmGov/Moove-Assure/pull/18) Make the signed-in home role-aware without splitting the overview

Related: [[Moove-Assure]] · [[GA checklist]]
