---
type: brief
tags: [moove, moove-assure, brief]
---
Project note: [[Moove-Assure]]

# Moove Assure: Product Brief

*For the marketing landing page. Prepared for Malcolm Govender, Moove Digital. 31 October 2026.*

> **Source of truth.** Everything below comes from the private repo `MalcolmGov/Moove-Assure` (`main` branch) as of 31 Oct 2026, including merged PRs #9–#15. Anything not built is marked **Roadmap / not built**. Halcyon Bank, Lumen Mutual and MA-001 are **fictional demo tenants**. There are no customers, metrics, certifications or prices in this brief, and the landing page should not suggest any.

> **Status check.** Open PRs that matter for launch: **#16** fixes the API Docker image build that the Railway preview needs, and **#17** rebuilds the public landing page for enterprise buyers. Both are unmerged. The brand reads "Moove Digital" in the repo and LICENSE but "Move Digital" on movedigital.africa. Pick one before launch.

---

## 1. Positioning

### One-liner
**Moove Assure lets AI draft your software delivery work, makes a named person sign off at every checkpoint, and keeps tamper-evident proof of every decision.**

### Short tagline options
- *AI drafts. People decide. The ledger proves it.*
- *Governed AI-assisted delivery for regulated teams.*

### Elevator pitch (about 30 seconds)
Banks, insurers and other regulated teams want the speed of AI-assisted delivery, but their regulators, auditors and risk committees still need a clear answer to "who approved this, against what standard, and where is the proof?" Moove Assure answers that question while the work happens. Each change is placed in a risk lane (Fast, Standard or Assured). Stage agents draft the artefact for each checkpoint, from the opportunity brief to the release pack, and score it against a rubric. A named human approves, rejects or requests changes. Agents and API tokens can't approve anything. Each decision, quality score, artefact hash and policy result goes onto a hash-chained evidence ledger, and the Controls page maps that evidence to NIST SSDF, NIST AI RMF, ISO/IEC 42001, DORA and the OWASP Top 10 for LLM apps. Gaps are shown, not hidden.

---

## 2. The problem

1. **AI writes faster than governance can review.** AI coding assistants and LLMs now produce code, PRDs, designs and test plans in minutes. Approval workflows, change advisory boards and evidence collection still run at human speed, often in documents and spreadsheets.
2. **Accountability gets blurry.** When an AI drafts a requirement or a pull request, it is often unclear who accepted the risk. Regulators expect a named, accountable person. NIST AI RMF and ISO/IEC 42001 both centre on human accountability for AI, and DORA expects ICT change to be controlled.
3. **Evidence is reconstructed after the fact.** Audit evidence is often pulled together from Jira, Confluence, email and CI logs weeks after release. It is slow and incomplete, and nothing proves it wasn't changed.
4. **Controls live in one tool and delivery in another.** GRC tools track controls. Delivery tools track work. The link between them (this decision, on this artefact, met this control) is usually manual.
5. **Regulated industries feel this most.** Banks, insurers, fintechs, telcos and the public sector carry the heaviest change-control, data-protection and outsourcing duties, so they are often the slowest to adopt AI in delivery.

---

## 3. What we built (feature inventory)

Every item below exists in code or docs on `main`. Each has a plain-English benefit.

### 3.1 Risk-based delivery lanes and checkpoints
| Capability | What it does | Benefit |
|---|---|---|
| **Three risk lanes** | Fast (70% quality bar), Standard (80%), Assured (85%). A lane recommender asks about money movement, sensitive data, AI decisioning and change size, and gives its reasons. | Governance effort matches the risk: light for small changes, full for money and data. |
| **10 checkpoints: CP1–CP9 plus G-UX** | Opportunity framing, PRD, UX, prototype usability/accessibility (G-UX, WCAG 2.2 AA), architecture, risk and controls (CP5, Assured only), backlog readiness, build and secure, test and assurance, release and evidence. | One consistent route from idea to go-live that everyone can follow. |
| **Lane → gate mapping** | Fast: CP2, CP7, CP8, CP9. Standard: CP1–CP4, G-UX, CP6–CP9. Assured: Standard plus CP5. | Small fixes skip ceremony. High-risk work cannot skip the risk sign-off. |
| **Quality bars with conditions** | Approving below the lane bar is allowed only if the decision records conditions (enforced by policy POL-LANE-01). | Exceptions are allowed, but always on the record. |
| **Lane downgrade guard** | POL-LANE-02 blocks moving work out of Assured without a recorded exception. | Nobody can quietly downgrade risky work to dodge controls. |

### 3.2 AI stage agents (draft only)
| Capability | What it does | Benefit |
|---|---|---|
| **A stage agent per checkpoint** | Analyst, Product Manager, UX Designer, Architect, Risk Analyst, Scrum Master, Developer, QA, Release Manager. Each drafts its gate's artefact into a fixed section structure. | First drafts in minutes, in a consistent shape reviewers recognise. |
| **Rich artefacts** | UX, prototype review and architecture packs can include C4 diagrams, sequence, deployment, data-flow, STRIDE, ER, journey, contrast and token figures, rendered in the browser. | Architecture and UX reviews get real diagrams, not only prose. |
| **Grounded drafting** | Prompts use the project brief, approved upstream artefacts and uploaded documents. Uploaded text is fenced as data, so instructions hidden in it (including "approve this gate") are ignored. | Drafts build on what was already agreed, with a basic guard against prompt injection in uploads. |
| **Rubric scoring** | A deterministic checklist covers the gate rubric, required headings, no placeholders (TBD/TODO/lorem) and citation of upstream IDs (FR-, ADR-, RK-, ST-, AC-). An optional AI judge adds one row. | Reviewers see objectively what is missing before they spend time reading. |
| **Streaming drafts** | Server-sent events stream the draft, then the score and the ledger hash. | Fast, visible feedback. |
| **Provenance on every draft** | Records provider, model, prompt version and hashes of all inputs. Prompt text and keys are not stored. | Auditors can see exactly what produced a draft. |
| **Bring-your-own model** | Any OpenAI-compatible endpoint. A deterministic mock is used when no key is set. Per-tenant keys are sealed with AES-256-GCM. | No lock-in to one LLM vendor. Works offline for demos. |
| **PII redaction (default on)** | Emails, phone numbers, SSNs, CVVs and Luhn-valid card numbers are redacted before the model sees the prompt. | Lowers the risk of personal data reaching a third-party model. |
| **Monthly AI budget** | Per-tenant monthly cap. Every call is metered. A draft is refused once the cap is reached. | Predictable AI spend. |
| **Complete missing sections** | Drafts only the gaps in an existing document and keeps the existing text. | Reuse the work teams already have. |

### 3.3 Human decisions (the core control)
| Capability | What it does | Benefit |
|---|---|---|
| **Named decision owner per gate** | Roles map to gates, e.g. product owner for CP2/CP6, architect for CP4, risk for CP5, release manager for CP9. | Accountability is clear and specific to each gate. |
| **Approve / reject / request changes** | Request changes records the reviewer's notes on the ledger and opens a new scored draft linked to the prior version. The gate stays open. | A real review loop, fully recorded. |
| **Agents and tokens cannot approve** | Enforced in roles, the API and policy POL-HUMAN-01. MCP `request_gate` stays `pending_human`. Auditors are read-only. SAML/SCIM sign-in cannot decide. | The hard line regulators care about: AI cannot sign off its own work. |
| **No admin bypass** | `tenant_admin` cannot approve another owner's gate. | Separation of duties holds even for admins. |
| **Two named approvers at Assured CP9** | POL-REL-02 requires a second named approver on Assured releases. | Four-eyes on high-risk go-lives. (This is a name on the decision, not a second signed-in session.) |

### 3.4 Evidence ledger (evidence by default)
| Capability | What it does | Benefit |
|---|---|---|
| **Hash-chained ledger** | SHA-256 chain over canonical JSON from a genesis hash. Decisions are written in the same transaction as the ledger entry. | Any edit, deletion or reordering of history can be detected. |
| **Verify on demand** | `POST /v1/projects/:code/ledger/verify` recomputes the chain. Tamper tests cover payload, hash, previous-hash, deletion and reorder. | Proof in one click, not "trust us". |
| **What gets recorded** | Drafts, imports, approvals, rejections, change requests, policy evaluations, evidence, release packs, labels, trace links, connector events, token create/revoke and approval-card requests. Each entry names the actor and how they authenticated. | The audit trail builds itself as work happens. |
| **Backup/restore drill in CI** | `scripts/ledger-drill.sh` dumps Postgres, restores into a throwaway database and verifies every chain. CI runs it as the `ledger-drill` job. | Recovery is tested, not just written down. |
| **Ledger view in the UI** | Hash-chain visual per project. | Auditors and executives can see the proof. |

### 3.5 Policy as code
| Capability | What it does | Benefit |
|---|---|---|
| **OPA/Rego on WASM** | Policies are Rego compiled by the pinned OPA CLI (1.21.4) and run in-process with opa-wasm. There is no TypeScript fallback. | Uses the open standard policy language. Policies really run. |
| **Starter pack (8 policies)** | POL-TM-04 (threat model at Assured CP4), POL-DP-07 (PAN/CVV/card/secret/email/phone in diffs), POL-REL-02 (two approvers), POL-SAST-01 (zero critical SAST at CP7), POL-AI-01 (AI-change provenance), POL-LANE-01, POL-HUMAN-01, POL-LANE-02. | Sensible bank-grade guardrails from day one. |
| **Tenant-authored, versioned policies** | Every save creates a new version. Invalid Rego is rejected. Only tenant admin or risk can change policies, and API tokens cannot. | Risk teams own their rules, with history. |
| **Block or warn** | A block returns 422 and leaves the gate undecided. The evaluation is still written to the ledger with policy versions. | Refusals are evidence too. |
| **Dry-run and diff checks** | Try a policy against sample input. `check_change` scans a unified diff and returns file/line findings. | Developers find issues before review. |
| **Control tags** | Tags for NIST SSDF, OWASP LLM, ISO 42001 and EU AI Act. | Policies link back to frameworks. |

### 3.6 Regulatory control mapping
| Capability | What it does | Benefit |
|---|---|---|
| **Controls page** | 24 rows across NIST SSDF, NIST AI RMF, ISO/IEC 42001, DORA and OWASP Top 10 for LLM. Each is marked **met / partial / gap**, with the gap stated. | Honest, specific answers for risk and audit. |
| **Live evidence links** | Each row links to the latest matching ledger entry for this tenant (project, gate, kind, sequence, hash). | Controls point to real evidence, not policy documents. |
| **CSV and PDF export** | Export includes ledger hashes. | Drops straight into an audit pack or GRC tool. |
| **Explicitly not a certification** | The page and docs say so. Titles are our own labels, not quotes from the standards. | Credibility with sophisticated buyers. |

*Current status mix (from `controls.ts`): 2 met (AI RMF MANAGE 2, OWASP LLM06 Excessive agency), most partial, and 4 gaps (SSDF RV.1, ISO 42001 Clause 9, DORA Art. 14, OWASP LLM01).*

### 3.7 Traceability
| Capability | What it does | Benefit |
|---|---|---|
| **Typed trace graph** | Requirement → story → artefact version → decision → policy result → Jira issue → PR → commit → test → evidence → release. | You can trace any requirement to the release that shipped it. |
| **Gaps shown in red** | Missing tests, missing evidence, policy fails and gates waiting on a person are highlighted. | Gaps surface before audit does. |
| **Impact analysis** | Shows what is affected downstream if a node changes. | Safer change decisions. |
| **Coverage matrix + evidence bundle** | CSV matrix and a zip with a SHA-256 manifest. | Hand auditors a verifiable package. |
| **Interactive graph** | React Flow explorer with filters and focus mode. | Easy for non-engineers to explore. |

### 3.8 Library, import and records management
| Capability | What it does | Benefit |
|---|---|---|
| **Import existing documents** | `.docx`, text PDF, Markdown, text or paste. Each is classified to a gate and scored against the rubric, with missing sections listed. Can open a new project from a PRD or brief. | Start with your current documents. No big-bang migration. |
| **Safe parsing** | Size limits. No macros, PDF JavaScript or XML external entities. Zip-bomb guard. | Uploads don't become an attack path. |
| **Hybrid search** | Postgres full text plus a 1536-dimension pgvector embedding, scoped to the tenant. | Finds "consent" when you search "permission". |
| **Facets, saved views, version diff** | Built-in views such as "Awaiting my approval", "Assured-lane releases" and "Legal hold". | Everyone sees their own queue. |
| **Evidence zip** | Manifest with per-file SHA-256 and ledger proofs (seq, hash, prevHash). | A self-verifying audit export. |
| **Access labels** | Internal / confidential / restricted. Restricted projects are hidden (404) from roles without access. | Need-to-know is enforced by the system. |
| **Retention and legal hold** | Retention up to 99 years. Under legal hold, nobody except a tenant admin (on the record) can weaken the label, shorten retention or release the hold. | Supports records-management and litigation-hold duties. |
| **Restricted-view audit** | Each opening of a restricted artefact is logged for auditors and admins. | Shows who saw sensitive material. |

### 3.9 Integrations
| Capability | What it does | Benefit |
|---|---|---|
| **GitHub App** | Publishes the check **"Moove Assure gate"**. It goes green only after the linked gate is approved. Links PRs to stories, checks acceptance-criteria coverage, runs policy on the diff and labels AI-assisted PRs with a provenance comment. Least-privilege scopes. | Merges are tied to human approval in the tool developers already use. |
| **Branch protection guide + dry-run script** | Shows admins how to make the check required. Changes nothing unless `--apply` is passed. Moove Assure never changes repo settings itself. | Easy to enforce, and safe by design. |
| **Jira Cloud** | API token or OAuth 3LO. Pushes the epic, stories and subtasks only after CP6 approval, syncs status both ways without loops, and imports existing epics and stories. | Keeps Jira as the team's work tool, with governance on top. |
| **SonarQube + SARIF (CP7)** | Records scanner findings as CP7 evidence. Any SARIF 2.1 scanner works. Feeds POL-SAST-01. | Uses the scanners you already run. |
| **Slack and Microsoft Teams cards** | Sends the gate owner an approval request that links back to the gate. The decision is still made in Moove Assure. | Fast nudges without losing the audit trail. |
| **Sandbox mode everywhere** | Every connector works without real credentials for demos and CI. | Low-risk evaluation. |
| **Sealed credentials** | Connector secrets are encrypted with AES-256-GCM and never logged, returned or written to the ledger. | Fewer secrets-handling worries. |

### 3.10 MCP server for coding agents
| Capability | What it does | Benefit |
|---|---|---|
| **5 MCP tools** | `get_story`, `get_policies`, `check_change`, `submit_evidence`, `request_gate`. None can approve. Uses scoped API tokens (read / evidence / gate:request). | AI coding assistants work inside the governance model instead of around it. |

### 3.11 Enterprise identity and multi-tenancy
| Capability | What it does | Benefit |
|---|---|---|
| **OIDC** | Verifies tokens against the issuer's JWKS. | Works with your existing IdP. |
| **SAML 2.0** | SP metadata and ACS. Only users who are already provisioned can sign in. The role comes from the directory, not the assertion. | Enterprise SSO without account sprawl. |
| **SCIM 2.0** | Provisions users and groups onto tenant roles. Unknown roles are rejected, with no default to a role that can decide. | Joiners, movers and leavers are handled by your IdP. |
| **Row-level security** | Every tenant table has `tenant_id` with forced RLS. The API connects as `moove_app` and sets the tenant per transaction from the verified token. `X-Tenant-Id` is never trusted for authorization. CI tests cross-tenant isolation. | Database-level separation between customers. |
| **Scoped API tokens** | Only a SHA-256 hash is stored. Plaintext is shown once. Tokens cannot decide or mint other tokens. | Safe automation. |
| **Roles** | Tenant admin, product owner, UX designer, architect, risk, developer, QA, release manager, auditor (read-only). | Matches how regulated delivery teams are organised. |

### 3.12 Supply-chain and release evidence
| Capability | What it does | Benefit |
|---|---|---|
| **CycloneDX SBOM + SLSA provenance** | CI writes `sbom.cdx.json` (CycloneDX 1.5) and `provenance.intoto.json` (SLSA v1). CP9 stores both and hashes them onto the ledger. The gate shows component count, hashes and builder. | Release evidence regulators and customers increasingly ask for, attached where the go-live decision is made. |

### 3.13 Operations and UI
| Capability | What it does | Benefit |
|---|---|---|
| **Runbooks** | Incident, ledger tamper alert, restore, key rotation, tenant onboarding/offboarding. | Operational maturity buyers can review. |
| **Preview deploy** | Vercel (web) plus Railway (API, Postgres with pgvector). Production mode refuses the demo login and the seed. | A safe, shareable demo environment. |
| **Premium UI** | Next.js 16, Tailwind 4, dark/light themes, ⌘K command palette, KPI overview, gate timeline, ledger view. Motion animations (the library formerly called Framer Motion) respect reduced-motion settings. | An enterprise-grade first impression. |

---

## 4. How it works (end to end)

```
Intake ──► Lane ──► [Draft ► Score ► Policy ► Human decision ► Ledger] × each gate ──► Release evidence ──► Controls export
```

1. **Register or import.** Create a project with an intent and risk answers, or import an existing PRD or brief. The lane recommender proposes Fast, Standard or Assured and gives its reasons.
2. **The lane sets the route.** It fixes the checkpoints (4, 9 or 10) and the quality bar (70 / 80 / 85).
3. **Draft.** At each gate, the stage agent drafts the artefact, grounded in approved upstream work and uploaded documents, with PII redacted. Provenance is recorded.
4. **Score.** The rubric checklist scores the draft and lists the gaps.
5. **Policy.** On approval, the relevant Rego policies run (threat model, SAST, data leakage, two approvers, lane bar, no agent approval). A block stops the approval and is still logged.
6. **Human decision.** The named owner approves (with conditions if below the bar), rejects or requests changes. Slack/Teams cards can nudge them. Agents, tokens and auditors cannot decide.
7. **Ledger.** The decision, score, artefact hash, policy versions and actor are appended to the hash chain.
8. **Build.** At CP6, approved stories go to Jira. PRs are linked to stories, checked against policy and blocked by the "Moove Assure gate" check until CP7 is approved. SonarQube/SARIF evidence lands on CP7.
9. **Release.** At CP9, the SBOM and SLSA provenance are attached and hashed. Assured releases need two named approvers.
10. **Prove.** The trace graph shows the full chain and its gaps. Controls maps ledger evidence to frameworks. You export CSV/PDF, the evidence zip and the trace matrix, and verify the chain.

---

## 5. The moat and differentiation

### What is hard to copy
1. **Evidence by default.** The ledger is not a report generated afterwards. Every decision is written to a SHA-256 hash chain in the same database transaction, and the backup/restore/verify drill runs in CI. Adding this to a workflow tool after the fact is hard.
2. **Human-decision enforcement in four layers.** The role model, API checks, Rego policy (POL-HUMAN-01) and token scopes each prevent AI self-approval, and there is no admin bypass. That makes "AI cannot sign off its own work" an enforced property of the system, not a policy statement.
3. **Policy as code on an open standard.** OPA/Rego, versioned per tenant and compiled to WASM. Each evaluation and its policy versions are on the ledger. Risk teams can write their own rules.
4. **Regulatory mapping tied to live evidence.** Controls rows point to real ledger hashes and show their gaps. Few delivery tools connect a framework control to the specific decision that evidences it.
5. **The full delivery lifecycle, not just code.** Ten checkpoints from opportunity to release, including UX accessibility (G-UX), architecture (C4, STRIDE) and risk acceptance. Most AI and CI tools cover only the code and build stages.
6. **Enterprise multi-tenancy from the start.** Postgres RLS enforced through a non-superuser role, SAML/SCIM/OIDC and tenant-sealed keys were designed in from Phase 0.
7. **Built for, and from, Africa's regulated market.** Moove Digital already builds fintech, fraud and eKYC platforms for banks and telcos in Johannesburg, so this is a domain we know.

### Fair comparison with alternatives (public, general knowledge)
| Alternative | What it does well | Where Moove Assure differs |
|---|---|---|
| **Jira + Confluence** (with approval add-ons) | The standard for work tracking and documentation, with a huge ecosystem and existing approval and workflow plugins. | Moove Assure doesn't replace Jira; it syncs with it. It adds AI-drafted, rubric-scored artefacts, enforced human-only decisions, policy-as-code at gates, and a tamper-evident ledger. Confluence page history is not a hash-chained evidence chain. |
| **GRC/compliance automation (Vanta, Drata and similar)** | Continuous monitoring of cloud, HR and device controls, auditor workflows and broad framework coverage such as SOC 2 and ISO 27001. | These tools monitor the organisation's control posture. Moove Assure governs individual changes and produces the delivery-level evidence (who approved this release, on what artefact, under which policy) that a GRC tool could take in. It complements them. It is not a certification tool. |
| **CI/CD gates** (GitHub branch protection, required reviews, environment approvals, pipeline policy checks) | Mature and native, and close to the code. Required reviews and deployment approvals are well understood. | They govern the code and deploy steps. Moove Assure governs the stages before them too (requirements, UX, architecture, risk), adds lane-based quality bars, and publishes its own required check so merges wait for the business gate. It works with CI gates, not instead of them. |
| **AI coding assistants** (GitHub Copilot, Cursor and similar) | Big productivity gains in writing code, with enterprise controls for data and policy. | They speed up producing work. Moove Assure governs the decision to accept it. Through MCP, an assistant can read stories and policies, check a change, submit evidence and request a gate, but it cannot approve. Moove Assure adds to the value of these assistants in regulated settings. |
| **Spreadsheets and email "gate packs"** | Cheap and familiar. | Manual, unverifiable and reconstructed after the fact. This is the status quo Moove Assure replaces. |

---

## 6. Value and benefits by stakeholder

| Stakeholder | What they care about | What Moove Assure gives them |
|---|---|---|
| **CIO / CTO** | Delivery speed, AI adoption without incidents, tool sprawl. | Safe AI use across the lifecycle. Risk lanes so low-risk work moves fast. Works with GitHub, Jira, SonarQube, Slack/Teams and any OpenAI-compatible model. Portfolio KPIs. |
| **CISO** | Secure SDLC, supply chain, data leakage, identity, tenant isolation. | Threat-model, SAST and secret/PII-in-diff policies. SBOM and SLSA at CP9. PII redaction before the model. SAML/SCIM/OIDC. RLS isolation. Sealed secrets. Restricted-view audit. Least-privilege connectors. |
| **CRO / Head of Compliance** | Accountable humans, risk acceptance, regulatory alignment. | Named owner per gate. Explicit CP5 risk acceptance on the Assured lane. Conditions recorded for every below-bar approval. Controls mapped to NIST AI RMF, ISO 42001 and DORA with honest gap statements. Tenant-owned policies. |
| **Internal audit** | Independent, verifiable evidence, without chasing teams. | Read-only auditor role. One-click chain verification. Exports with hashes (CSV, PDF, evidence zip, trace matrix). Who-viewed-what logs. Legal hold. |
| **Delivery / product leads** | Predictable flow, less admin, clear "ready" criteria. | AI first drafts. Rubric scores that show exactly what is missing. "Awaiting my approval" views. Request-changes loop. Jira sync after CP6. Approval nudges in Slack/Teams. |
| **Engineers** | Staying in their tools, fast feedback, no busywork. | One GitHub check that explains what is blocking. Policy findings by file and line. MCP tools for their AI assistant. SARIF from any scanner. AI-assisted PRs labelled automatically. |

---

## 7. Target customers and ICP

### Primary ICP
Regulated organisations of **roughly 200+ engineers or several delivery teams** that are **adopting AI coding/drafting tools** and face **external change-control or AI-governance scrutiny**. Typical champions: Head of Engineering or Delivery, CISO, CRO, Head of Internal Audit.

### Segments
| Segment | Why it fits |
|---|---|
| **Banks** (tier 1–2, digital banks) | Money movement and card data fit the Assured lane and POL-DP-07. Change-advisory culture. Prudential supervision. |
| **Insurers** (life, short-term, health) | Sensitive personal data, AI in underwriting and claims, actuarial and model-risk governance. |
| **Fintechs and payment providers** | Need bank-grade evidence to win bank partnerships and pass partner due diligence. |
| **Telcos and mobile money** | Large delivery estates, financial services licences, customer data. |
| **Public sector and SOEs** | Accountability, records retention and legal hold, procurement scrutiny. |

### South African and African regulatory context (positioning only, not product mappings)
> **Important.** The Controls page today maps **only** NIST SSDF, NIST AI RMF, ISO/IEC 42001, DORA and OWASP LLM. It does **not** map POPIA, Prudential Authority / FSCA standards or King IV. Say "helps produce evidence for", never "compliant with". Have counsel or compliance confirm the references below before using them in copy.

- **POPIA (Protection of Personal Information Act):** security safeguards for personal information and limits on solely automated decisions. Relevant features: PII redaction before the model, POL-DP-07 (personal data in diffs), access labels, restricted-view audit and retention.
- **SARB Prudential Authority / FSCA:** the joint standards on IT governance and risk management, and on cybersecurity and cyber resilience, expect governed change management and evidence. Relevant features: gates, the ledger, runbooks and the backup drill.
- **King IV:** the governing body is responsible for technology and information governance. Relevant features: named accountability, portfolio KPIs and board-ready exports.
- **DORA (EU) 2022/2554:** for SA/African institutions with EU subsidiaries, EU clients or EU-regulated ICT supply chains. DORA Art. 9, 11 and 12 are mapped (partial). Art. 14 is a gap.
- **Pan-African:** comparable data-protection laws (for example Kenya's and Nigeria's) are an expansion path. They are not mapped.

### Buying triggers
- An enterprise rollout of an AI coding assistant, and risk or audit asking "how do we govern this?"
- An internal or external audit finding on change management or SDLC evidence.
- A regulator review, a new joint standard coming into force, or DORA scope reaching an EU-linked entity.
- An AI-governance programme being set up (ISO/IEC 42001 readiness, an NIST AI RMF adoption, or an AI policy approved by the board).
- A failed or delayed release where nobody could show who approved it.
- A fintech going through a bank partner's due diligence.
- A CAB that has become a bottleneck and needs to move to risk-based lanes.

---

## 8. Use cases

1. **Governed AI-assisted feature delivery.** A bank ships "instant payout to card" on the Assured lane. Agents draft the PRD, architecture (with STRIDE) and risk register. Named owners decide each gate, and the merge waits for CP7.
2. **Bringing AI coding assistants inside the guardrails.** Developers' assistants use MCP to pull the story and policies, check their diff and request the gate. They never approve.
3. **Audit-ready change evidence.** Internal audit exports the controls CSV/PDF, the trace matrix and the evidence zip, and verifies the hash chain on its own.
4. **Risk-based change advisory.** Fast-lane fixes go through four checkpoints, while money-movement changes go through all ten with risk acceptance. CAB effort goes where the risk is.
5. **Onboarding existing documents.** Import current PRDs and briefs, score them against the rubric, and complete only the missing sections.
6. **Supply-chain evidence at go-live.** The SBOM and SLSA provenance are attached and hashed at CP9, next to the release decision.
7. **Restricted and legal-hold programmes.** Label sensitive projects restricted, log every view and apply legal hold during an investigation.
8. **Our own dogfooding.** MA-001 shows Moove Assure's own release passing an Assured lane with only human approvals. It is a fictional walkthrough. Nothing was shipped.

---

## 9. Proof points available today

Use these, and only these.

- **Live demo with fictional tenants.** Halcyon Bank (portfolio including HB-090, HB-097, HB-099, HB-101 and MA-001) and Lumen Mutual (second tenant, for the isolation proof). Scripted walkthroughs are in the README: approve above the bar, approve below the bar (conditions required), request changes, generate a draft, and an auditor being refused.
- **Controls export.** CSV and PDF with met/partial/gap and ledger hashes. Gaps are shown openly, which is itself a credibility point.
- **CI evidence on every push:** lint, typecheck, migrate, seed, the full Vitest suite (including the cross-tenant isolation test run as `moove_app` on real Postgres), build, the **CycloneDX SBOM + SLSA provenance** artifact, and the separate **ledger backup/restore/verify drill** job.
- **Tamper detection tests** for payload, hash, prev-hash, deletion and reorder.
- **Policy tests**, one per starter Rego policy, compiled with the pinned OPA.
- **Sandbox integrations.** GitHub check, Jira, SonarQube, Slack and Teams all demoable without credentials.
- **Public documentation set.** Architecture, security, policy, AI, connectors, SSO, traceability, backup, controls, runbooks and 5 ADRs, all suitable for a security-questionnaire pack (under NDA, since the product is proprietary).

**Not available, so don't claim:** customer logos, case studies, ROI or time-saved figures, uptime/SLA, certifications (SOC 2, ISO 27001, ISO 42001), penetration-test results, pricing, a production GA release.

---

## 10. Objections and answers

| Objection | Answer |
|---|---|
| "We already have Jira and Confluence." | Keep them. Moove Assure pushes approved backlogs to Jira and syncs status. It adds what they don't have: enforced human decisions, policy-as-code and a tamper-evident ledger. |
| "We have Vanta/Drata/[GRC tool]." | They monitor your control posture. We produce the change-level evidence (decision, artefact hash, policy result) that feeds it. Export is CSV/PDF today. |
| "GitHub branch protection already gates merges." | Yes, and our required check works inside it. Branch protection covers code review. We cover the business, risk, UX and architecture sign-offs that come before the merge. |
| "Can the AI approve its own work?" | No. Agents, MCP tools, API tokens, SAML/SCIM sign-ins and auditors cannot decide. This is enforced in roles, the API and a Rego policy, and tenant admins cannot bypass another owner's gate. |
| "Where does our data go? Which LLM?" | Bring your own OpenAI-compatible endpoint, with a sealed per-tenant key and a monthly budget. PII redaction is on by default. A mock model is used if you don't connect one. |
| "How is tenant data separated?" | Postgres row-level security, forced on every tenant table. The app runs as a non-superuser role with the tenant set per transaction from the verified token. Cross-tenant tests run in CI. |
| "Are you certified / does this make us compliant?" | No, and we don't claim it. The Controls page maps public frameworks honestly (met/partial/gap) and links live evidence. It supports your compliance programme. It is not a certification. |
| "Is the ledger really tamper-proof?" | It is tamper-evident. Any change to history breaks the SHA-256 chain on verification, and the backup/restore/verify drill runs in CI. It is not signed by an external timestamp authority (see roadmap). |
| "Will this slow us down?" | Lanes put effort where the risk is. Fast-lane work has four checkpoints at a 70% bar. Agents write the first draft, and rubric scores tell reviewers exactly what is missing. |
| "Do you map POPIA / PA standards / King IV?" | Not yet in the Controls page. The features support evidence for them (redaction, access labels, audit, change control). Local mappings are on the roadmap. |
| "What about support and uptime?" | We're pre-GA. The status page and support path are being finalised. Engage as a design partner for now. |
| "Is it open source?" | No. It is proprietary software from Moove Digital, licensed under a separate written agreement. It is built on open standards (OPA/Rego, CycloneDX, SLSA, SAML, SCIM, OIDC, MCP, SARIF). |

---

## 11. Suggested landing page structure

### Headline options
1. **AI drafts. People decide. The ledger proves it.** *(recommended: short, true, distinctive)*
2. **Ship AI-assisted software your regulator can trust.**
3. **Governed AI delivery for banks, insurers and the teams that answer to regulators.**
4. **Every AI-drafted change, signed off by a named human, with proof.**
5. **Move at AI speed. Keep audit-grade evidence.**

### Sub-headline options
- "Moove Assure routes every change through risk-based checkpoints. AI agents draft the artefacts, named owners make the decisions, and every decision lands on a tamper-evident ledger mapped to NIST, ISO/IEC 42001 and DORA."
- "Built in Johannesburg for regulated delivery teams adopting AI."

### Page sections and copy hooks
| # | Section | Copy hook | Visual |
|---|---|---|---|
| 1 | **Hero** | Headline + sub + CTAs: "Book a demo" / "See the controls map". | Animated Halcyon/MA-001 mock: lane bar, named owner, "0 agent approvals", gates filling in, hash ticking (already in PR #17). |
| 2 | **Framework strip** | "Mapped to public frameworks" with NIST SSDF, NIST AI RMF, ISO/IEC 42001, DORA, OWASP LLM Top 10. | Text marquee. **No logos implying endorsement.** |
| 3 | **Problem** | "AI writes in minutes. Governance still works in weeks." | Three pain cards: speed gap, blurred accountability, after-the-fact evidence. |
| 4 | **Draft → Decide → Prove** | "Agents draft. A named person decides. The ledger proves it." | Three-step animation. |
| 5 | **Lanes** | "Governance that matches the risk." Fast 70 / Standard 80 / Assured 85. | Lane cards with gate counts 4 / 9 / 10. |
| 6 | **Human-in-control** | "AI can't sign off its own work, and nor can a token or an admin." | Decision form with Approve / Reject / Request changes. |
| 7 | **Evidence ledger** | "Evidence by default. Tamper-evident by design." Mention the CI restore drill. | Hash-chain visual and a "Verify" button state. |
| 8 | **Policy as code** | "Your risk rules, in Rego, enforced at every gate." | Starter-pack list and a policy-block example. |
| 9 | **Controls** | "Honest about gaps." met / partial / gap with export. | Controls table preview showing a gap row on purpose. |
| 10 | **Works with your stack** | GitHub required check, Jira, SonarQube/SARIF, Slack, Teams, any OpenAI-compatible model, MCP for coding agents. | Integration tiles (text names; check trademark guidelines before using logos). |
| 11 | **Enterprise security** | SAML 2.0, SCIM 2.0, OIDC, Postgres RLS, sealed keys, PII redaction, access labels, legal hold, view audit, SBOM + SLSA. | Bento grid. |
| 12 | **For each stakeholder** | Tabs: CIO/CTO, CISO, Risk & Compliance, Audit, Delivery, Engineers (from §6). | Tabbed panel. |
| 13 | **Built for regulated Africa** | "From Johannesburg, for banks, insurers, fintechs, telcos and the public sector. Helps produce evidence for POPIA, PA/FSCA and King IV programmes, and DORA for EU-linked entities." | Map or regional panel. Get compliance to review the wording. |
| 14 | **Facts band** | 3 lanes · 10 checkpoints · 85% Assured bar · 0 agent approvals · 8 starter policies · 5 frameworks mapped. | Product facts only. |
| 15 | **FAQ** | Use §10 objections. | Accordion. |
| 16 | **CTA** | "Become a design partner" / "Book a guided demo". | Contact form to malcolm@movedigital.africa or whichever support path is chosen. |

### What NOT to claim
- ❌ "Compliant with", "certified", "audited", "approved by" any regulator or standard (ISO 42001, SOC 2, DORA, POPIA, PA, King IV).
- ❌ That POPIA, PA/FSCA or King IV are **mapped in the product**. They aren't, yet.
- ❌ Customer names, logos, testimonials or case studies. Halcyon Bank, Lumen Mutual and MA-001 are fictional and must be labelled as demo data wherever they appear.
- ❌ Metrics such as "X% faster", "saves Y hours", ROI, or uptime/SLA numbers.
- ❌ Pricing or plan tiers (none are defined).
- ❌ "Tamper-proof" or "immutable". Use **"tamper-evident"** and "verifiable".
- ❌ "Generally available" or "production-ready" before GA items close.
- ❌ "24/7 support" or a status page (neither exists).
- ❌ "Prevents prompt injection" (OWASP LLM01 is a gap; uploads are fenced, that's all), or "blocks all data leakage" (redaction is a gateway option).
- ❌ "Signed releases" or "external timestamping" (the ledger is a hash chain; policy bundles aren't signed).
- ❌ "Second approver authenticates separately" (the CP9 second approver is a named person on the decision).
- ❌ "Live SAST scanning" (SAST is submitted evidence; SonarQube is a pull of results).
- ❌ Third-party logos (GitHub, Atlassian, Slack, Microsoft, NIST, ISO, OWASP) used in a way that suggests partnership or endorsement.
- ❌ "Open source".

---

## 12. Open gaps and roadmap before GA

### Must close before calling it GA
| Gap | Status / source |
|---|---|
| **Status page** | Not built. Waiting on the hosting choice (`docs/runbooks.md`, `docs/roadmap.md`). |
| **Support path** | Not built. No public mailbox or ticket queue (`docs/runbooks.md`). |
| **Production release** | Only the preview stack exists (Vercel + Railway, fictional data). `MA-001` is a fictional self-release, and "nothing was shipped". PR #16 (API Docker image fix) is still open. |
| **Public landing page** | PR #17 is open and not merged. |
| **Production backup retention** | Off-site retention and the backup schedule are not chosen (DORA Art. 12 row; `docs/runbooks.md`). |
| **Client/incident notification** | No status page or notification path (DORA Art. 14 = gap). |

### Known product follow-ups (documented in the repo)
- **AI:** strict JSON-schema responses, retrieval over pgvector for grounding, a calibrated judge, budget stop mid-stream, reopening an approved gate (`docs/ai.md`).
- **Security/AI risk:** prompt-injection detection (OWASP LLM01 gap), poisoned-embedding detection, a hard per-call spend stop, model/provider attestation.
- **Policy:** a Rego editor in the UI (today API only), signed policy bundles, an OPA sidecar option, a cryptographic second approver for CP9, a live SAST connection (`docs/policy.md`).
- **Library:** OCR for scanned PDFs, shared saved views, legal hold that blocks deletion (`docs/library.md`).
- **Traceability:** saved layouts, intent as the first trace column (`docs/traceability.md`).
- **Connectors:** company-managed Jira epic links. Other catalogue connectors are labels only (`docs/connectors.md`).
- **Controls:** vulnerability intake and disclosure (SSDF RV.1 gap), management review and internal audit programme (ISO 42001 Cl. 9 gap).
- **Market fit (suggested, not in the repo):** POPIA, PA/FSCA joint standards and King IV control mappings; external timestamping/anchoring of ledger heads; customer-facing security pack (pen test, DPA, hosting region choice, ideally SA data residency).

---

## Appendix A: Repo file paths backing key claims

| Claim | Files |
|---|---|
| Positioning, phases, demo walkthroughs, proprietary licence | `README.md`, `LICENSE` |
| Architecture, lanes table, RLS, ledger design | `docs/architecture.md`, `docs/adr/ADR-0001-stack.md` |
| Lanes, quality bars 70/80/85, lane recommender | `packages/domain/src/lanes.ts`, `packages/domain/src/lanes.test.ts` |
| CP1–CP9 + G-UX definitions, owners, agents, public basis | `packages/domain/src/stages.ts` |
| Rubrics and scoring | `packages/domain/src/quality.ts`, `packages/agents/src/stage-agent.ts`, `packages/agents/src/templates.ts` |
| Stage agents, gateway, BYO model, redaction, budget, provenance | `docs/ai.md`, `docs/adr/ADR-0003-stage-agents.md`, `packages/agents/src/gateway.ts`, `packages/agents/src/redact.ts`, `packages/agents/src/usage.ts`, `packages/agents/src/crypto.ts`, `apps/api/src/draft.ts` |
| Request changes loop | `packages/domain/src/decisions.ts`, `apps/api/src/changes.test.ts`, `apps/api/migrations/0010_request_changes.sql`, PR #9 |
| Roles, no agent/token/auditor approval, no admin bypass | `docs/security.md`, `packages/domain/src/roles.ts`, `packages/policy/rego/POL-HUMAN-01.rego`, `docs/adr/ADR-0002-auth.md` |
| Hash-chained ledger and tamper tests | `packages/evidence-ledger/src/chain.ts`, `packages/evidence-ledger/src/chain.test.ts` |
| Backup/restore drill in CI | `scripts/ledger-drill.sh`, `apps/api/scripts/verify-ledgers.mjs`, `docs/backup.md`, `.github/workflows/ci.yml` (`ledger-drill` job) |
| Policy as code, OPA WASM, starter pack | `docs/policy.md`, `docs/adr/ADR-0004-policy-engine.md`, `packages/policy/src/engine.ts`, `packages/policy/rego/*.rego`, `apps/api/src/policy-service.ts`, `apps/api/migrations/0006_policy.sql` |
| Controls mapping, met/partial/gap, CSV/PDF export, not a certification | `docs/controls.md`, `packages/domain/src/controls.ts`, `apps/api/src/controls-routes.ts`, `apps/web/app/controls/page.tsx`, `apps/web/app/controls/export/route.ts`, PR #13 |
| Traceability graph, matrix, evidence bundle | `docs/traceability.md`, `packages/domain/src/trace.ts`, `apps/api/src/trace-service.ts`, `apps/web/components/trace/*`, `apps/api/migrations/0009_trace.sql` |
| Library, import, hybrid pgvector search, labels, legal hold, view audit | `docs/library.md`, `packages/domain/src/library.ts`, `apps/api/src/library-routes.ts`, `packages/documents/src/parse.ts`, `packages/agents/src/embed.ts`, `apps/api/migrations/0002_vector.sql`, `0008_library.sql`, `0012_access_views.sql`, `apps/web/app/audit/page.tsx`, PR #11 |
| GitHub check, Jira, SonarQube/SARIF, Slack/Teams, sandbox, sealed creds | `docs/connectors.md`, `docs/adr/ADR-0005-connectors.md`, `packages/connectors/src/github-handler.ts`, `jira-sync.ts`, `scanner.ts`, `notify.ts`, `seal.ts`, `apps/api/src/integrations.ts`, PR #10 |
| Branch protection (no repo changes unless `--apply`) | `docs/github-branch-protection.md`, `scripts/github-branch-protection.sh` |
| MCP server, 5 tools, none can approve | `packages/mcp-server/src/tools.ts`, `packages/mcp-server/src/index.ts` |
| OIDC, SAML 2.0, SCIM 2.0 | `docs/sso.md`, `apps/api/src/auth.ts`, `apps/api/src/saml.ts`, `apps/api/src/sso.ts`, `apps/api/src/scim-map.ts`, `apps/api/migrations/0013_sso_release.sql`, `apps/web/app/directory/page.tsx`, PR #12 |
| Tenant isolation / RLS / `moove_app` | `apps/api/migrations/0003_rls.sql`, `apps/api/src/tenant-isolation.test.ts`, `apps/api/src/sql-repo.ts`, `docs/security.md` |
| API tokens (hashed, scoped, cannot decide) | `docs/security.md`, `apps/web/app/tokens/*`, `apps/api/migrations/0004_auth.sql` |
| CycloneDX SBOM + SLSA provenance at CP9 | `scripts/write-release-pack.mjs`, `apps/api/src/release-pack.ts`, `apps/api/src/release-routes.ts`, `apps/web/components/release-pack.tsx`, `.github/workflows/ci.yml` |
| Runbooks, status page/support path open | `docs/runbooks.md`, `docs/roadmap.md` |
| Preview deploy (Vercel + Railway), production refuses demo login/seed | `docs/deploy.md`, `railway.json`, `Dockerfile`, `apps/web/vercel.json`, PR #15; Docker fix PR #16 (open) |
| Premium UI, motion, landing | `apps/web/package.json` (`motion`, `next` 16, `@xyflow/react`), `apps/web/components/landing.tsx`, `apps/web/components/motion/reveal.tsx`, `apps/web/app/globals.css`, PR #14; new landing PR #17 (open) |
| Fictional demo data (Halcyon Bank, Lumen Mutual, MA-001) | `apps/api/src/seed.ts`, `apps/api/src/lumen.ts`, `packages/domain/src/catalogue.ts`, `README.md`, `docs/controls.md` |
| Roadmap and phase status | `docs/roadmap.md` |

## Appendix B: Merged PRs reviewed
| PR | Title | Merged (SAST) |
|---|---|---|
| #9 | Close Phase 1 with scored drafts and request changes | 31 Oct 2026, 08:44 |
| #10 | Add CP7 scanner evidence and Slack and Teams approval cards | 31 Oct 2026, 08:50 |
| #11 | Add hybrid search and enforce access labels | 31 Oct 2026, 09:04 |
| #12 | Add SAML, SCIM, and CP9 release evidence | 31 Oct 2026, 09:25 |
| #13 | Add control mapping, runbooks, and a demo self-release | 31 Oct 2026, 10:04 |
| #14 | Polish the web app for enterprise demos | 31 Oct 2026, 10:36 |
| #15 | Add a Railway and Vercel preview deploy | 31 Oct 2026, 10:52 |

Open and relevant: #16 (API image TypeScript config fix), #17 (enterprise landing page rebuild).
