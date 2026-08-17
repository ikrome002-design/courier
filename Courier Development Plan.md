# Courier by Citrus — Production Software Development Plan

**Owner:** Citrus Labs Limited  
**Product:** Courier by Citrus  
**Plan type:** Production implementation plan for an IDE-based AI coding agent and human engineering team  
**Plan date:** 27 June 2026  
**Authoritative product source:** `Courier_by_Citrus_Project_Scope.md`, version `2.0.0-corrected`, effective 27 June 2026  
**Technical and brand source:** `COURIER COMBINED.txt`  
**Companion schema artifact:** `Courier_by_Citrus_Database_Catalog.md`

## Source-of-truth rule

The corrected project scope is normative for product behavior, role boundaries, lifecycle states, billing rules, release classification, referral integration, and acceptance. The combined technical specification is normative for the required engineering stack, frontend behavior, accessibility, responsive behavior, dark mode, deployment, and production-readiness expectations. Where a generic SaaS requirement conflicts with an explicit Courier rule, the Courier rule prevails and the exception must be documented in an Architecture Decision Record.

## Engineering evidence record required for every task

Every implementation task, defect correction, migration, or architectural change must create or update an evidence record with these fields:

| Field | Required content |
|---|---|
| Requirement | Exact scope section, capability ID, route ID, state action, or acceptance criterion. |
| Proven gap | File, schema, route, test, runtime trace, or missing artifact proving the work is needed. |
| Root cause | Underlying cause; never merely the visible symptom. |
| Smallest correct change | Files and behavior changed, with unrelated behavior explicitly excluded. |
| Omission failure | Security, data-integrity, operational, financial, compliance, or UX failure created by not implementing the item. |
| Verification | Tests, API examples, database checks, screenshots, logs, or denial proof. |
| Residual risk | Known limitation, follow-up, or operational dependency. |

No phase is complete because code compiles. It is complete only when the required evidence record and verification artifacts exist.


# 1. Executive Architecture Summary

Courier by Citrus shall be implemented as a **modular monolith** in one repository, with independently testable bounded contexts and extraction-ready integration boundaries. A microservice-first design is rejected for launch because the product contains tightly coordinated transactions across delivery, payment, billing, proof, assignment, compensation, cash-up, and audit. Splitting these domains prematurely would add distributed transaction, observability, deployment, and consistency risk without proven load requiring it.

## 1.1 Chosen baseline

| Layer | Production baseline |
|---|---|
| Backend | Laravel 13, PHP 8.5, strict types, REST API under `/api/v1`. |
| Frontend | Vue 3, TypeScript, Vite, Vue Router, Pinia for client state, TanStack Query for server state. |
| Styling | Tailwind CSS with design tokens derived from the Courier brand system. |
| Database | PostgreSQL 18, UUIDv7 primary keys, foreign keys, checks, partial indexes, exclusion constraints where necessary, and row-level security as defense in depth. |
| Authentication | Laravel Sanctum same-origin SPA sessions; merchant Magic Links; merchant-scoped sender Magic Link/OTP; recipient tracking token/OTP; platform password/passkey plus mandatory MFA. |
| Cache/queues/locks | Redis; Laravel Horizon; queue priorities by business criticality. |
| Realtime | Laravel Reverb/WebSocket channels for dispatch, assignment, payment status, and tracking; polling fallback. |
| Files | S3-compatible private object storage, quarantine bucket/prefix, malware scanning, signed delivery URLs. |
| Search | PostgreSQL FTS/trigram at launch; Meilisearch introduced behind an index abstraction when measured thresholds justify it. |
| API contracts | OpenAPI 3.1 generated from the canonical route registry; generated TypeScript client. |
| State machines | Versioned YAML registries compiled into PHP enums/transition maps and TypeScript display metadata. |
| Integration reliability | Transactional outbox, inbox/idempotency tables, detached signatures, retry/backoff, dead-letter queues. |
| Deployment | Docker images, immutable releases, blue/green or rolling deployment, migration gates, automated rollback. |

## 1.2 Bounded contexts

1. Identity and Authentication.
2. Platform Access and Governance.
3. Merchant and Branch Management.
4. Membership, Employment, Roles, and Onboarding.
5. Business Customer Organizations.
6. Customer, Contact, Address, and Consent.
7. Delivery Catalogue, Zones, Pricing, and Quotes.
8. Delivery Requests and State Machines.
9. Dispatch, Assignment, Fleet, and Routing.
10. Tracking, Proof, OTP, Incidents, Returns, and Redelivery.
11. Merchant-Customer Finance.
12. Merchant-to-Citrus Platform Billing.
13. Compensation, Earnings, and Payouts.
14. Branch Day, Cash-Up, and Financial Locks.
15. Files and Private Access.
16. Notifications and Communications.
17. Search, Reports, and Exports.
18. Audit, Security Events, and Observability.
19. Business APIs and Webhooks.
20. Citrus Refer & Earn Integration.

## 1.3 Architectural invariants

- One concept has one authoritative writer.
- Identity, membership, employment, role grant, branch assignment, and credential are separate records.
- Request, quote, approval, payment requirement, invoice settlement, payment transaction, assignment, custody, physical delivery, redelivery, dispute, refund, record lifecycle, cash-up, and financial-period state remain orthogonal.
- All money uses integer minor units and ISO currency.
- All mutating business transitions use named commands, idempotency keys where retries can duplicate effects, and optimistic concurrency.
- Tenant, branch, organization, and own-scope enforcement happens server-side and is repeated at the database boundary for high-risk data.
- Platform staff never become merchant staff implicitly.
- Courier emits factual referral events; it never calculates or pays referral rewards.

## 1.4 Realistic delivery projection

A production launch covering the mandatory MVP, launch entitlements, M-Pesa integration, personnel PWA, audits, financial controls, and referral integration is realistically a **12–18 month programme** for a stable cross-functional team of approximately 8–12 experienced contributors. The likelihood of delivering the full production scope in 12 months is approximately **65–75%** with that team, frozen scope, prompt provider access, and disciplined release gates. A solo developer, even with an AI coding agent, has roughly a **15–25%** likelihood of delivering the entire production-grade scope within 12 months; a more credible solo timeline is 24–36+ months because security review, QA, payment certification, operational support, and production hardening cannot be safely compressed into code generation alone.


# 2. Assumptions and Constraints

## 2.1 Product assumptions

- The corrected scope is authoritative and supersedes earlier contradictory drafts.
- Kenya is the launch jurisdiction; KES is the default currency, while currency fields remain configurable.
- Merchant tenants share one database and schema at launch. Dedicated databases may be offered later only after a measured isolation or enterprise requirement.
- Merchant users use Magic Link authentication because the scope explicitly specifies it; the generic email/password requirement is therefore not applied to merchant accounts.
- Internal Citrus platform users use password or passkey plus mandatory MFA because their privileges require hardened step-up authentication.
- Goods recipients do not require persistent accounts at launch.
- Basic distance, ETA, route creation, and stop reordering are launch scope. Full multi-vehicle optimization is post-MVP and must not appear in production routes, UI, entitlements, or marketing before implementation.
- The centralized Citrus Refer & Earn platform is an external bounded system. Courier stores only product facts, local attribution snapshots, delivery state, and event-delivery evidence.
- No merchant impersonation by platform staff exists at launch.

## 2.2 Delivery constraints

- M-Pesa credentials, sandbox access, callback URLs, certificate requirements, and production approval must be available before payment certification.
- Google Maps Platform or an approved replacement must be contractually and technically available in launch countries.
- SMS/WhatsApp sender approval timelines are external dependencies.
- Data retention, tax, invoice, privacy, and employment requirements must receive Kenyan legal/accounting review before production activation.
- The application must remain usable at 200% browser zoom and at the three required viewport classes.
- Private customer and operational files must never be placed behind public object URLs.

## 2.3 Engineering constraints

- No jQuery.
- No direct state mutation by generic PATCH.
- No broad `withoutGlobalScopes()` or unscoped repositories in tenant request paths.
- No money represented as float/double.
- No production use of SQLite for integration tests that depend on PostgreSQL constraints, JSONB, RLS, partial indexes, advisory locks, or exclusion constraints.
- No release without a generated route registry, permission registry, state-machine registry, OpenAPI contract, migration manifest, and capability-to-test traceability manifest sharing the same release checksum.
- No direct third-party calls inside a database transaction. Persist intent/outbox atomically, then perform network calls asynchronously.

## 2.4 Team assumptions

Minimum accountable roles:

| Role | Accountability |
|---|---|
| Product owner/domain lead | Requirement decisions, scope acceptance, release classification. |
| Technical lead | Architecture, code review, dependency decisions, ADR approval. |
| Backend engineers | Domain services, API, state machines, database, integrations. |
| Frontend engineers | Role surfaces, PWA, accessibility, responsive behavior. |
| QA automation engineer | Test architecture, E2E, regression, evidence pack. |
| DevOps/SRE | Infrastructure, CI/CD, secrets, backups, monitoring, incident readiness. |
| Security reviewer | Threat model, access-control review, penetration testing, secure release sign-off. |
| Finance/payment SME | Invoice, payment, refund, reconciliation, cash-up, compensation acceptance. |

One person may hold more than one project role, but production maker/checker rules in the application and release approval must still be respected.


# 3. Non-Negotiable Security Rules

1. **Default deny.** Every protected route, command, file, export, websocket channel, search index, queue job, and webhook requires explicit authorization.
2. **Opaque identifiers.** Public APIs expose UUIDv7/ULID identifiers and controlled references, never enumerable integer IDs.
3. **Tenant before lookup.** Tenant-owned records are queried by `(merchant_id, public_id)` or through a scoped repository. Fetch-then-check patterns are prohibited for normal tenant APIs.
4. **Branch is not permission.** Branch assignment and permission are evaluated separately.
5. **Own-scope is explicit.** Delivery Personnel repositories require `personnel_id` matching the authenticated personnel record.
6. **Platform separation.** Platform sessions are rejected on merchant routes. Platform database access uses a separate connection role and dedicated repositories.
7. **Server enforcement.** Frontend role checks only hide unavailable UI; every command is reauthorized server-side.
8. **Command-only state changes.** State columns, assignments, approvals, refunds, payments, proofs, and locks cannot be mass-assigned.
9. **Concurrency.** Every mutable aggregate carries `aggregate_version`; commands require `If-Match` or an expected version and return `409 state_conflict` when stale.
10. **Idempotency.** Payment, refund, registration, webhook, proof submission, state transition, invoice issue, outbox replay, and integration commands use idempotency keys and request hashes.
11. **No secrets in source or logs.** Secrets live in a secret manager; logs redact credentials, tokens, OTPs, payment data, personal identifiers, and provider payloads.
12. **Private files.** Uploads enter quarantine, are scanned, metadata validated, and promoted to private storage only after acceptance.
13. **Signed callbacks.** M-Pesa, payment, notification, and central referral callbacks require signature verification, replay windows, nonce/event deduplication, and source validation where supported.
14. **Financial immutability.** Issued invoices, settled allocations, receipts, reward facts, ledger entries, approved cash-ups, and audit records are corrected through append-only adjustments, never destructive edits.
15. **Maker/checker.** The requester cannot approve the same refund, compensation plan, payout run, high-risk platform grant, break-glass request, or designated security restriction.
16. **Audit integrity.** Sensitive actions write an append-only audit row and an outbox event in the same transaction. Hash-chain verification runs continuously.
17. **RLS defense in depth.** Tenant data uses PostgreSQL row-level security. Application scoping remains mandatory; RLS is not a substitute for policies.
18. **Secure sessions.** Cookies are Secure, HttpOnly, SameSite=Lax or Strict as appropriate; session rotation occurs after authentication and privilege changes.
19. **Rate limiting.** Public auth, tracking, OTP, registration, referral validation, webhook ingress, exports, searches, and sensitive mutations have named limiters.
20. **Safe errors.** Error responses use the canonical envelope and never reveal cross-tenant existence, stack traces, provider secrets, fraud rules, or internal SQL.
21. **No unsafe redirects.** Return URLs are allowlisted and signed.
22. **No disabled zoom or device detection.** Responsive behavior is CSS viewport-based.
23. **Dependency governance.** Every dependency requires an ADR or dependency record, pinned version, license check, vulnerability scanning, and removal owner.
24. **Production fail closed.** Missing state registries, permission registries, signing keys, RLS policies, or required configuration blocks startup/deployment.


# 4. System Architecture

## 4.1 Context architecture

```text
Browser / PWA / Business API Client
        |
        v
CDN + WAF + TLS termination
        |
        v
Reverse proxy / ingress
        |
        +--> Vue SPA static assets
        |
        +--> Laravel API application
                 |
                 +--> PostgreSQL primary + read replicas
                 +--> Redis cache/queues/locks/rate limits
                 +--> S3-compatible private object storage
                 +--> Reverb/WebSocket service
                 +--> Search service through SearchGateway
                 +--> Email/SMS/WhatsApp providers
                 +--> Maps provider
                 +--> M-Pesa/payment providers
                 +--> Central Citrus Refer & Earn platform
                 +--> Central logging, metrics, traces, error tracking
```

## 4.2 Repository structure

```text
courier-by-citrus/
├── apps/
│   ├── api/                         # Laravel 13 application
│   └── web/                         # Vue 3 TypeScript SPA/PWA
├── packages/
│   ├── contracts/                   # generated OpenAPI types/client
│   ├── design-system/               # shared tokens and Vue components
│   ├── eslint-config/
│   └── tsconfig/
├── registries/
│   ├── capabilities/
│   ├── permissions/
│   ├── routes/
│   ├── state-machines/
│   ├── errors/
│   ├── audit-events/
│   ├── event-schemas/
│   └── release-manifest/
├── docs/
│   ├── adr/
│   ├── architecture/
│   ├── schema/
│   ├── runbooks/
│   ├── threat-model/
│   ├── test-evidence/
│   └── user-flows/
├── infra/
│   ├── docker/
│   ├── terraform-or-pulumi/
│   ├── kubernetes-or-ecs/
│   ├── monitoring/
│   └── scripts/
├── .github/workflows/               # or equivalent CI provider
├── docker-compose.yml
├── Makefile
└── README.md
```

## 4.3 Deployment units

Launch deployment units:

1. `web`: static Vue assets served through CDN/ingress.
2. `api`: stateless Laravel HTTP application.
3. `queue-critical`: payment callbacks, state transitions, outbox delivery, security jobs.
4. `queue-default`: notifications, webhooks, routine processing.
5. `queue-heavy`: exports, reports, file processing, thumbnails, search indexing.
6. `scheduler`: one active Laravel scheduler leader protected by a distributed lock.
7. `reverb`: websocket server.
8. `horizon`: queue supervisor and monitoring UI, restricted to platform operators.

## 4.4 Request execution pipeline

For a merchant-protected API request:

1. TLS/WAF validation.
2. Request correlation ID creation or validation.
3. Sanctum session authentication.
4. Session status and reauthentication-age check.
5. Merchant context resolution from the merchant-bound session; route parameters may only confirm, never choose, tenant context.
6. Database connection context and RLS variables set.
7. Billing-status allowlist gate.
8. Entitlement gate.
9. Branch/organization/own-scope resolution.
10. Form Request validation.
11. Policy/command authorization.
12. Idempotency reservation when required.
13. Domain command transaction with aggregate version check.
14. Audit and outbox rows written atomically.
15. Resource response produced through an API Resource.
16. Structured log and metrics emitted with sensitive-field redaction.
17. Tenant/database context cleared in `finally`, including Octane/long-lived worker safeguards if Octane is introduced later.

## 4.5 Modular-monolith communication rules

- Contexts call public application services/interfaces, not another context's Eloquent models.
- Cross-context read needs use query services or read projections.
- Cross-context writes emit domain events or invoke an explicit command interface.
- A context owns its tables and migrations; direct writes from other contexts are prohibited.
- Shared kernel is limited to identifiers, money value objects, clock, result/error types, tenant context, idempotency, audit metadata, and outbox abstractions.
- Circular dependencies fail architecture tests.

## 4.6 Transaction boundaries

A transaction may atomically include:

- aggregate mutation;
- state-history append;
- audit append;
- outbox message append;
- idempotency completion;
- projection update required for immediate consistency.

Network calls, email, SMS, M-Pesa requests, file scanning, search indexing, and webhook delivery occur after commit. Their failures create retryable operational records, not rollback of an already valid business transaction.


# 5. Backend Architecture

## 5.1 Laravel application layout

```text
apps/api/app/
├── Application/
│   └── <Context>/
│       ├── Commands/
│       ├── Queries/
│       ├── DTOs/
│       ├── Handlers/
│       └── Validators/
├── Domain/
│   └── <Context>/
│       ├── Aggregates/
│       ├── Entities/
│       ├── ValueObjects/
│       ├── Policies/
│       ├── Events/
│       ├── Exceptions/
│       ├── Services/
│       └── Contracts/
├── Infrastructure/
│   └── <Context>/
│       ├── Persistence/
│       ├── Providers/
│       ├── Jobs/
│       ├── Listeners/
│       └── Adapters/
├── Http/
│   ├── Controllers/Api/V1/
│   ├── Middleware/
│   ├── Requests/
│   └── Resources/
└── Support/
    ├── Tenancy/
    ├── Idempotency/
    ├── Audit/
    ├── Outbox/
    ├── Money/
    └── Concurrency/
```

Controllers remain orchestration-only: authenticate context, invoke one command/query handler, and return a resource. Business rules do not live in controllers, jobs, listeners, policies, or Vue components.

## 5.2 Required shared services

| Service | Responsibility |
|---|---|
| `TenantContext` | Immutable merchant identity for the current execution. Throws when absent on tenant work. |
| `BranchScopeService` | Confirms branch belongs to current merchant and actor assignment permits scope. |
| `PersonnelOwnScopeService` | Restricts personnel queries/commands to current personnel record. |
| `AuthorizationService` | Resolves permission registry, role grants, scope, step-up, maker/checker. |
| `EntitlementService` | Evaluates active subscription snapshot, add-ons, limit consumption, effective dates. |
| `BillingStatusGate` | Enforces active/restricted/suspended allowlists. |
| `IdempotencyService` | Reserves key, stores request hash/result, detects conflicting replay. |
| `AggregateConcurrency` | Validates expected version and increments atomically. |
| `StateMachineRegistry` | Loads versioned transitions and validates actor, state, evidence, side effects. |
| `PeriodLockService` | Central authority for financial mutation eligibility. |
| `FileAccessService` | Upload authorization, quarantine, scanning, signed access, download audit. |
| `AuditService` | Append-only normalized audit events and hash chaining. |
| `OutboxService` | Writes and publishes reliable internal/external events. |
| `NotificationService` | Template resolution, privacy filtering, preferences, channel delivery. |
| `WebhookDeliveryService` | Signed outbound webhooks, retry, dead-letter, replay. |
| `Money` value object | Integer minor units, currency matching, safe arithmetic and rounding. |

## 5.3 State-machine implementation

Each state registry file contains:

```yaml
domain: operational_delivery
registry_version: 1
transitions:
  - action: complete_pickup
    from: pickup_verification_complete
    to: picked_up
    permission: delivery.personnel.pickup.complete
    scope_rule: personnel_own_scope
    evidence: [proof_of_pickup]
    idempotency: required
    audit_event: delivery.pickup.completed
```

Build tooling must:

1. Validate YAML schema.
2. Reject duplicate actions/from-state combinations.
3. Generate PHP backed enums and transition metadata.
4. Generate TypeScript display types without exposing privileged rules.
5. Generate transition test cases.
6. Compare route `state_action` values against registry actions.
7. Store registry version on long-running aggregates.
8. Block deployment when registry checksum differs from the release manifest.

No controller may set a state string directly. The command handler calls the state-machine service, checks evidence and concurrency, appends history, emits domain events, and increments aggregate version.

## 5.4 Query architecture

- Eloquent models are internal persistence records, not API contracts.
- Use query objects with explicit filters, sort allowlists, eager-load lists, and selected columns.
- Collection queries always paginate; cursor pagination is preferred for high-volume append-only lists.
- Search results return IDs then hydrate through tenant-scoped repositories.
- Read models may denormalize dashboard metrics, but are rebuildable and never authoritative writers.
- Avoid hidden N+1 queries by failing tests when query-count budgets are exceeded on critical endpoints.

## 5.5 Error handling

All domain exceptions map to the canonical envelope:

```json
{
  "error": {
    "code": "invalid_transition",
    "message": "The delivery cannot move to that state.",
    "correlation_id": "cor_...",
    "details": {"allowed_actions": ["..."]},
    "retryable": false
  }
}
```

Rules:

- Validation details identify fields without echoing secrets.
- Cross-tenant or scope-sensitive misses use opaque `404 not_found`.
- Permission denial uses `403 forbidden` only when existence disclosure is safe.
- Provider errors are translated to `502 provider_error` or `503 dependency_unavailable` with internal details logged under correlation ID.
- Unhandled exceptions produce a generic `500 internal_error`; stack traces remain in protected error tracking only.

## 5.6 Events and outbox

`outbox_messages` must include `id`, `tenant_id nullable`, `aggregate_type`, `aggregate_id`, `event_type`, `event_version`, `payload`, `headers`, `occurred_at`, `available_at`, `attempt_count`, `published_at`, and `last_error_code`. A unique key on `(event_type, aggregate_id, aggregate_version)` prevents duplicate publication where appropriate.

Consumers use an inbox/deduplication record keyed by `event_id` and consumer name. At-least-once delivery is assumed. Consumers must be idempotent.

## 5.7 Code quality gates

- PHPStan/Larastan maximum practical level with a documented baseline only for legacy migration code.
- Laravel Pint.
- Rector dry-run for approved automated transformations.
- Composer audit and license policy.
- Architecture tests enforcing context dependencies.
- Migration lint for money naming, tenant keys, foreign keys, indexes, and forbidden generic delivery status.
- Route lint for duplicate writers and missing policy/capability metadata.


# 6. Frontend Architecture

## 6.1 Application structure

```text
apps/web/src/
├── app/
│   ├── router/
│   ├── providers/
│   ├── layouts/
│   └── boot/
├── modules/
│   ├── auth/
│   ├── platform/
│   ├── merchant/
│   ├── branches/
│   ├── staff/
│   ├── customers/
│   ├── catalogue/
│   ├── deliveries/
│   ├── dispatch/
│   ├── personnel-pwa/
│   ├── finance/
│   ├── billing/
│   ├── compensation/
│   ├── cash-up/
│   ├── support/
│   ├── audit/
│   ├── reports/
│   └── integrations/
├── shared/
│   ├── api/
│   ├── components/
│   ├── composables/
│   ├── directives/
│   ├── forms/
│   ├── i18n/
│   ├── permissions/
│   ├── theme/
│   ├── types/
│   └── utils/
└── pwa/
```

## 6.2 State ownership

| State type | Tool and rule |
|---|---|
| Server data | TanStack Query; cache keys include merchant and branch context. Never place authoritative financial state only in Pinia. |
| Session/context | Pinia store populated from `/api/v1/session-context`; clear completely on logout/context change. |
| Forms | VeeValidate plus Zod schemas generated or aligned with API contracts. |
| URL state | Filters, sort, pagination, tabs, and date ranges belong in route query parameters when shareable. |
| Realtime | Query cache updates from typed websocket events; every event includes tenant and resource identity and is authorization-filtered server-side. |
| Offline PWA commands | IndexedDB queue with encrypted-at-rest browser storage where practical, command IDs, aggregate versions, timestamps, and explicit sync states. No long-lived secrets. |

## 6.3 Routing and role surfaces

Separate layout trees:

- `/platform/*` for internal Citrus roles.
- `/app/*` for merchant staff.
- `/sender/*` for Goods Sender and business-customer users.
- `/personnel/*` for Delivery Personnel PWA.
- `/track/:token` for recipient access.
- public marketing, registration, referral campaign, and authentication routes.

The route guard checks authentication and basic context for UX, then calls a session-context endpoint. It never substitutes for API authorization. Routes not granted to the user are omitted from navigation but direct URL attempts still rely on backend denial.

## 6.4 Component layers

1. **Primitives:** button, link, input, select, checkbox, radio, switch, dialog, popover, tooltip, badge, skeleton.
2. **Composites:** data table, filter bar, form section, command confirmation, status timeline, money display, file uploader, audit diff, map address picker.
3. **Domain components:** delivery card, quote editor, assignment panel, proof viewer, invoice allocation table, payout approval panel.
4. **Pages:** assemble modules; pages do not implement server business rules.

Every component defines loading, empty, error, disabled, success, keyboard, screen-reader, light, dark, mobile, and 200% zoom behavior.

## 6.5 API client

- Generated TypeScript types from OpenAPI.
- Same-origin credentials enabled for Sanctum.
- CSRF bootstrap before mutating browser requests.
- Request correlation ID attached.
- `Idempotency-Key` generated for eligible commands and persisted through retry.
- `If-Match` or expected aggregate version attached to concurrent mutations.
- Error mapper converts the canonical envelope into form, toast, page, or retry states.
- Automatic retry only for safe GETs and explicitly retryable idempotent commands. Never automatically retry non-idempotent financial mutations without the original key.

## 6.6 PWA behavior

Delivery Personnel PWA supports:

- app shell caching;
- assignment list and current assignment read cache;
- controlled offline capture of proof metadata and files;
- explicit pending/syncing/synced/conflict/failed states;
- background sync when supported, manual sync fallback;
- server validation of assignment ownership, state, evidence, event time, and aggregate version;
- conflict UI that never silently overwrites server state;
- device storage quotas and cleanup after server acknowledgment;
- no offline access to unrelated customer history.

## 6.7 Frontend testing and quality

- ESLint, TypeScript strict mode, Prettier.
- Vitest unit tests.
- Vue Testing Library for component behavior.
- axe-core accessibility checks.
- Playwright for role workflows, responsive breakpoints, dark mode, and PWA sync.
- Bundle budgets enforced by CI.


# 7. Database Architecture

The companion file `Courier_by_Citrus_Database_Catalog.md` is part of this plan and provides the per-table purpose, core columns, ownership, foreign keys, indexes, unique constraints, deletion/retention policy, security notes, and migration requirements for every canonical launch table.

## 7.1 Physical conventions

| Concern | Rule |
|---|---|
| Primary keys | UUIDv7 generated by the application or approved database function; never expose serial IDs. |
| Time | `timestamptz` in UTC; store branch/user timezone separately for local calculations. |
| Money | `bigint *_minor` plus `char(3) currency`; check amount ranges and currency consistency. |
| Percentages | integer basis points or fixed-scale numeric strings; never binary float. |
| Tenant ownership | Every tenant row has `merchant_id uuid not null`; branch rows also have `branch_id uuid not null`. |
| Composite ownership | Composite foreign keys or trigger-backed constraints ensure a branch/organization/personnel record belongs to the same merchant. |
| Concurrency | Mutable aggregates have `aggregate_version bigint not null default 1`. |
| State | Domain-specific enums/check constraints; no generic writable `status` on `delivery_requests`. |
| Soft delete | Only lifecycle entities use `deleted_at`; financial, proof, state-history, audit, and integration evidence are append-only. |
| JSONB | Allowed for provider payloads, snapshots, or schema-versioned metadata; not a substitute for relational columns used in policy/query logic. |
| Encryption | Highly sensitive values use application envelope encryption with key version; searchable contacts store normalized hashes separately. |
| Partitioning | Monthly partitions for driver locations, audit logs, notification deliveries, webhook attempts, integration events, and high-volume state histories when volume thresholds are met. |

## 7.2 Required framework/infrastructure tables

In addition to the domain catalog:

| Table | Required design |
|---|---|
| `cache`, `cache_locks` | Laravel database fallback only; Redis is production primary. No tenant business data. |
| `jobs` | Database fallback only; Redis production queues. Payloads contain tenant identifiers but no secrets. |
| `failed_jobs` | UUID, connection, queue, redacted payload, exception fingerprint, failed timestamp, tenant context where safe. Retain 90 days after resolution. |
| `job_batches` | UUID batch ID, counts, options, timestamps; options must not contain secrets. |
| `personal_access_tokens` | Used only for approved token use cases; hash token, abilities, last-used, expiry. Browser SPA uses sessions. |
| `sessions` | Hashed/opaque session ID, identity ID, tenant context, IP prefix, user-agent hash, last activity, expiry. |
| `migrations` | Framework-managed; release manifest records migration checksum and deployment order. |
| `outbox_messages` | Reliable event publication; partition and retain according to business/audit class. |
| `inbox_messages` | Consumer deduplication keyed by event and consumer. |
| `idempotency_keys` | Scope, actor, route, key hash, request hash, response code/body hash, expiry. |

## 7.3 Key aggregate schemas

### `merchants`

- `id uuid PK`, `public_id uuid unique`, `slug citext unique`, `status merchant_operational_status`, `billing_status merchant_billing_status`, `setup_completed_at`, `default_currency char(3)`, `timezone text`, `aggregate_version bigint`, timestamps, optional `deactivated_at`.
- Indexes: `(status)`, `(billing_status)`, `(created_at)`, partial index for active merchants.
- Hard delete prohibited after any child business record.

### `merchant_user_memberships`

- `id`, `merchant_id`, `identity_id`, `membership_status`, `joined_at`, `suspended_at`, `aggregate_version`, timestamps.
- Unique active membership on `(merchant_id, identity_id)`.
- Membership is not employment and not a role grant.

### `delivery_requests`

- Fields specified by the authoritative scope, including distinct `request_status`, `payment_requirement_status`, `operational_status`, and `record_lifecycle_status`.
- Unique `(merchant_id, reference_number)`.
- Composite FKs ensure branch, sender, service, active quote, and preferred request share merchant ownership.
- State fields are protected from generic updates by database trigger/privilege strategy and application command rules.

### `delivery_assignments`

- `id`, `merchant_id`, `branch_id`, `delivery_id`, `personnel_id`, `vehicle_id nullable`, `assignment_status`, `assignment_mode`, offer/accept/reject/complete timestamps, `assigned_by_actor_type/id`, predecessor/successor IDs, `aggregate_version`.
- Partial unique index preventing more than one active assignment per delivery unless a route/batch policy explicitly permits multiple personnel roles.

### `customer_invoices`

- `id`, tenant/branch/customer references, invoice number, issue/due dates, settlement state, subtotal/tax/discount/total/balance minor units, currency, immutable pricing snapshot, aggregate version.
- Unique `(merchant_id, invoice_number)`.
- Issued invoices cannot be edited; corrections use credit/adjustment records.

### `platform_billing_invoices`

- Separate from customer invoices. Stores captured plan, billing mode, fee tier, tax, discount, add-ons, SMS, and percentage-fee lines.
- STK initiation does not mutate invoice to paid; allocations derived from validated transactions do.

### `audit_logs`

- `id`, `merchant_id nullable`, `branch_id nullable`, actor domain/id/role snapshot, action, target type/id, before/after safe JSON, reason, severity, IP/user-agent hashes, correlation ID, previous hash, row hash, timestamp.
- Append-only database permissions; update/delete revoked from application roles.

## 7.4 Index strategy

Every migration must include a query-evidence note. Required index patterns include:

- `(merchant_id, public_id)` for public resource lookup.
- `(merchant_id, branch_id, state, created_at desc)` for operational queues.
- Partial indexes for active memberships, active assignments, pending payments, open tickets, and unprocessed outbox rows.
- GIN indexes for approved JSONB/search-vector fields.
- GiST indexes for geography and exclusion constraints.
- BRIN indexes for very large time-ordered append tables.
- Unique idempotency and provider-reference indexes.

Unused or duplicate indexes are identified through production statistics before removal. Index removal requires a reversible migration and query-plan evidence.

## 7.5 Migration rules

1. Expand/contract deployment for breaking changes.
2. Add nullable column or new table first; deploy dual-read/controlled dual-write only when documented; backfill in bounded batches; validate; switch reads; remove old writer; later enforce `NOT NULL`; finally remove legacy field.
3. Large indexes use `CREATE INDEX CONCURRENTLY` outside a transaction through a migration runner supporting it.
4. Foreign keys on large populated tables are added `NOT VALID`, backfilled/reconciled, then validated.
5. Enum changes use lookup tables or safe PostgreSQL enum migration procedures; never remove an enum value while rows use it.
6. Every migration includes forward validation, rollback limits, data-loss assessment, and production runbook.
7. Rollback must never reactivate a deprecated contradictory writer.


# 8. Multi-Tenancy and Data Isolation Model

## 8.1 Tenant resolution

- Merchant context is established at authentication and stored in the server-side session.
- For users with memberships in multiple merchants, context switching is an explicit signed POST command that validates membership, rotates session identifiers, clears caches, and emits an audit event.
- Route slugs are presentation/confirmation only; they cannot override session tenant context.
- Sender authentication is always under `/merchants/{merchant_slug}/sender-auth/*` and creates a merchant-bound session.
- Recipient access resolves a single delivery from a hashed tracking token and never creates general tenant context.
- Platform users have no tenant context and use `/platform/*` plus a separate database connection/repository boundary.

## 8.2 Request storage and propagation

`TenantContext` contains:

```text
merchant_id
identity_id
membership_id
role_grant_ids
branch_scope_ids
organization_scope_ids
personnel_id nullable
session_id
correlation_id
```

It is immutable during one request. Middleware sets it and database transaction-local settings. A `finally` block clears all context.

## 8.3 Background jobs

Every tenant job implements `TenantAwareJob` and serializes only:

- merchant UUID;
- actor/system identity reference;
- resource public UUID;
- command/event ID;
- registry version;
- correlation ID.

On execution, middleware:

1. Resolves the merchant and confirms it still exists.
2. Establishes tenant DB context.
3. Reauthorizes system capability where required.
4. Loads the resource through a scoped repository.
5. Aborts with `missing_tenant_context` before any query when context is absent.
6. Clears context after execution.

Serializing full Eloquent models in jobs is prohibited.

## 8.4 Database enforcement

- `courier_tenant_app` role is subject to RLS on tenant tables.
- `courier_platform_app` is used only by platform repositories and has separate policies/privileges.
- `courier_migrator` owns schema changes and is unavailable to runtime containers.
- RLS policy compares `merchant_id` to a transaction-local setting set by trusted middleware.
- Tenant app role cannot switch to platform role.
- Platform route code cannot call tenant controllers through internal HTTP shortcuts.

## 8.5 Cache, search, files, realtime, and exports

| Surface | Isolation rule |
|---|---|
| Redis cache | Key prefix includes environment, merchant ID, branch/organization scope, resource, and version. |
| Rate limits | Keys include actor/contact hash, merchant where known, IP risk bucket, and endpoint purpose. |
| Search | One filterable `merchant_id` field; server injects filter and hydrates results through scoped DB queries. Shared index result IDs are never trusted alone. |
| Files | Object key begins with environment/merchant UUID; access always passes FileAccessService. |
| Websockets | Private channel authorization checks tenant, branch, resource, and permission. Client-supplied merchant IDs are ignored. |
| Exports | Export query is reconstructed from a signed server-side specification containing tenant and actor scope; the job refuses missing scope. |
| Notifications | Notification record carries merchant ID and recipient identity; templates receive privacy-filtered DTOs. |
| Webhooks | Subscription belongs to one merchant/organization; event payload and signing key are resolved from that ownership. |

## 8.6 Denied-case proofs

| Case | Required result and proof |
|---|---|
| Account A requests Account B delivery ID | Opaque `404 not_found`; zero query result under tenant filter; audit security event. |
| Member has branch assignment but lacks permission | `403 forbidden`; branch scope passes, permission policy fails. |
| Queue job lacks tenant context | Job fails before repository access; no SQL against tenant tables; alert and failed-job record. |
| Export omits tenant filter | Export specification validation fails; no file produced. Architecture test rejects direct model export. |
| Valid public ID belongs to another merchant | Opaque 404, identical timing band where practical, no existence disclosure. |
| Personnel requests another driver's assignment | Opaque 404 under own-scope repository. |
| Platform session calls merchant route | `403 identity_domain_mismatch` or 404 route surface; no merchant DB connection. |
| Sender reuses session across merchant slug | Context mismatch; reauthentication required; no cross-merchant sender data. |

## 8.7 Super-admin safety

“Super Administrator” is only the UI label for `platform_owner`. Platform workflows:

- use separate identity/session tables;
- require MFA and step-up for high-risk commands;
- use maker/checker for owner grants, break-glass, billing policy, and high-risk restrictions;
- do not impersonate merchant users;
- expose masked tenant details by default;
- require reason and audit for exports or sensitive detail views;
- cannot create merchant tenants or first merchant owners.


# 9. Authentication Model

## 9.1 Identity domains

1. Global human identity (`user_identities`).
2. Internal platform identity profile (`platform_users`).
3. Merchant membership and employment.
4. Goods Sender membership, including business-organization roles.
5. Delivery-scoped recipient token access.
6. Non-human integration principals.

An email address may identify one human identity while memberships remain tenant-scoped. Authentication does not automatically grant any merchant or organization access.

## 9.2 Merchant staff Magic Link

Flow:

1. User submits email on the merchant login surface.
2. Server normalizes and hashes email, applies neutral response and rate limits.
3. Matching active membership is checked without disclosing existence.
4. A 256-bit random token is generated; only its hash is stored.
5. Challenge stores purpose, merchant, identity, membership, expiry, attempt/use state, requested IP/device risk, and nonce.
6. Email contains a single-use HTTPS link.
7. Consumption locks the challenge row, verifies hash/expiry/status, marks used, invalidates sibling unused challenges, checks membership/employment/emergency suspension/role status, then creates a Sanctum-backed browser session.
8. Session ID rotates and login event is audited.

Default expiry: 10–15 minutes, configurable. Reuse returns a neutral invalid/expired response. Deactivation or material role reassignment revokes outstanding tokens.

## 9.3 Sender authentication

- Merchant-scoped Magic Link or OTP.
- OTP stored as a slow hash with per-challenge salt; never logged.
- Attempt caps, resend cooldown, expiry, risk scoring, and lockout.
- Neutral responses for unknown sender/contact.
- Session carries merchant and sender membership.
- Multi-merchant users explicitly switch/re-authenticate context.

## 9.4 Recipient access

- Tracking token is high-entropy, single-delivery scoped, hashed in DB, purpose-bound, expiring, revocable, and optionally OTP-gated for sensitive actions.
- GET may expose only privacy-approved status/ETA data.
- Proof confirmation, availability, or issue submission requires stronger token purpose and CSRF-equivalent signed request protection.
- Link previews and crawlers must not consume one-time actions.

## 9.5 Platform authentication

- Email plus strong password or passkey.
- Argon2id hashing through Laravel defaults/configuration.
- Mandatory TOTP or WebAuthn/passkey MFA; recovery codes encrypted and single-use.
- Short idle timeout, absolute session lifetime, trusted-device policy only after security approval.
- Step-up within five minutes for owner grants, break-glass, billing configuration, secrets, exports, suspension/reactivation, and integration key rotation.
- Device/session management and immediate revocation.
- Password reset uses single-use tokens, rate limits, session revocation, and security notification.

## 9.6 API authentication

- Same-origin SPA: Sanctum cookies and CSRF.
- Business API: high-entropy client credential or OAuth client credentials only when external OAuth2 interoperability is proven necessary. Credentials are hashed or stored in a secret manager, scoped, versioned, expiring, rotatable, and non-interactive.
- Internal Courier-to-referral integration: service identity plus detached request/event signature.
- No bearer token in URL query strings.

## 9.7 Rate limits

Named limiters with configurable values:

- registration by IP/device/contact/business fingerprint;
- Magic Link request and consume;
- OTP request/verify/resend;
- platform login/MFA/password reset;
- invitation acceptance;
- tracking token reads and actions;
- file upload;
- exports;
- M-Pesa initiation;
- webhooks;
- API clients by credential and tenant.

Rate-limit responses include safe retry-after values. High-risk patterns create security events and may require CAPTCHA or manual review without treating shared IP alone as proof of abuse.


# 10. Authorization, Roles, and Permissions Model

## 10.1 Implementation approach

Use Laravel Policies/Gates backed by a **custom versioned permission registry**, not a generic five-role-only model. A mature package may be used for low-level helpers only after proving it supports separate platform, merchant, branch, organization, and own-scope grants without conflating membership or employment. The authoritative scope requires more than a flat role table.

Permission registry entry:

```yaml
key: delivery.assignment.create
actor_domains: [merchant]
allowed_roles: [dispatch_customer_operations]
scope_rule: branch_permission
step_up: false
maker_checker_group: null
audit_event: delivery.assignment.created
release_class: mandatory_mvp
```

## 10.2 Canonical merchant roles

| Role | Scope | Principal authority | Explicit denials |
|---|---|---|---|
| Merchant Administrator/Owner | Merchant-wide oversight | Merchant profile, branches, plan/billing, high-risk approval, emergency access suspension, reporting. | No routine dispatch, payment validation, HR lifecycle, branch catalogue, or all-purpose override. |
| Branch Manager | Assigned branches | Branch profile, calendar, services, zones, pricing, fleet records, branch day, cash-up submission. | No live assignment, staff lifecycle, payment validation, refunds, payout settlement. |
| Merchant Human Resources | Merchant/assigned branches | Invitations, membership/employment lifecycle, roles, branch assignments, eligibility, shifts, compensation preparation. | No customer payment validation, dispatch, cash-up approval, payout settlement. |
| Merchant Finance | Merchant/assigned branches | Payment validation/reconciliation, invoices review, refunds, disputes, cash-up approval, locks, compensation verification, payout settlement, platform billing. | No route assignment, HR employment control, proof mutation. |
| Dispatch & Customer Operations | Assigned branches | Request intake, customers, addresses, quotes, invoice creation, payment recording, assignments, routing, customer communication. | No payment validation, refund approval, role management, compensation approval. |
| Delivery Personnel | Own-scope | Assigned jobs, route, status commands, proofs, incidents, COD evidence, own earnings. | No merchant-wide search, other personnel jobs, contact export, invoice validation. |
| Merchant Support | Merchant/branch support scope | Tickets, masked context, communication, escalation. | No source financial/operational mutation. |
| Merchant Audit | Assigned branches, read-only | Audit review, flags, notes, escalation, governed export. | No source business mutation or self-approval. |
| Goods Sender | Own or organization scope | Requests, quotes, payments, tracking, history, documents, support. | No merchant operational data, staff, other senders. |

The generic Owner/Admin/Manager/Member/Viewer labels from the base SaaS requirement are mapped only where needed for reusable account UI; they do not replace the Courier domain roles.

## 10.3 Internal platform roles

- `platform_owner` (UI: Super Administrator).
- Platform Billing and Reconciliation Operator.
- Platform Support Operator.
- Platform Security and Risk Operator.
- Platform Integration Operator.
- Platform Auditor.
- Executive Read-Only.

Rules: no self-grant, no self-approval, no final-owner removal, mandatory MFA, effective dates, immediate revocation, product-bound grants, complete audit.

## 10.4 Business-customer roles

- Business Customer Administrator.
- Business Customer Operator.
- Business Customer Finance Viewer.
- Business Customer Integration Administrator.

All are Goods Sender identities with both `merchant_id` and `business_customer_organization_id` scope. The final active organization administrator cannot be removed without an accepted replacement.

## 10.5 Authorization evaluation order

1. Identity domain valid for route.
2. Session/authenticator valid and not revoked.
3. Merchant/organization/personnel context resolved.
4. Operational and billing status allows operation.
5. Subscription entitlement and limit available.
6. Membership/employment/role grant active at current time.
7. Branch/organization/own scope matches resource.
8. Permission granted.
9. State preconditions satisfied.
10. Maker/checker and step-up rules satisfied.
11. Financial-period lock allows mutation.
12. Evidence and idempotency requirements satisfied.

Failure is safe and audited where sensitive.

## 10.6 Ownership transfer and removals

- Merchant ownership transfer is a named command with incoming owner accepted/verified, current owner step-up, independent approval when configured, audit, session revocation, and last-owner protection.
- Removing a user revokes role grants and sessions but preserves identity, historical actions, employment history, financial ownership, proof attribution, and audit.
- Emergency security suspension blocks access without changing employment or compensation.
- Organization/member removal preserves historical requests and invoices.

## 10.7 Frontend permission handling

The session-context API returns permitted capability keys and scope summaries. The UI uses them to build navigation and disable unavailable actions. It must not receive hidden fraud logic, approval thresholds not needed for display, or unrestricted tenant IDs. Backend policies remain authoritative.


# 11. API Design

## 11.1 Standards

- Base path `/api/v1` for public/product APIs; `/internal/v1` for service-to-service APIs.
- JSON UTF-8; timestamps RFC 3339 UTC; money as `*_minor` plus `currency`.
- OpenAPI 3.1 is generated and validated in CI.
- API Resources control output fields and masking.
- All lists paginate and define stable sort order.
- Filters and sorts are allowlisted; unknown values return `422 validation_failed`.
- Mutations accept `Idempotency-Key` where required.
- Concurrent aggregate mutations accept `If-Match: "<version>"` or explicit `expected_version`.
- Responses include `X-Correlation-ID`; rate-limited responses include `Retry-After`.

## 11.2 Route middleware stacks

```text
public: correlation, secure-headers, request-size, rate-limit
merchant: auth:sanctum, identity-domain:merchant, tenant-context, rls-context,
          billing-gate, entitlement, branch/own-scope as required, policy
platform: auth:sanctum, identity-domain:platform, mfa, step-up where required,
          platform-db-connection, platform-policy, audit
internal: service-auth, signature, timestamp, replay-protection, idempotency, audit
tracking: token-resolver, purpose-check, privacy-filter, rate-limit
```

## 11.3 Canonical endpoint families

The implementation must reproduce the normative route registry. Principal families include:

- Public registration and authentication.
- Merchant and branches.
- Staff lifecycle and emergency security.
- Business organizations, members, API clients, and webhooks.
- Catalogue, zones, senders, recipients, and addresses.
- Deliveries, quotes, approvals, preferred personnel requests, confirmation, assignment, handover, cancellation, returns, redelivery.
- Basic routing and stop ordering.
- Personnel own-scope operational commands.
- Customer invoices, payments, validation, refunds, disputes, cash-up, locks, reopen workflow.
- Compensation plans, earnings, payout runs.
- Platform billing and M-Pesa attempts.
- Support, audit flags, notifications, receipts.
- Platform internal users, billing settings, plan prices, promotions, merchant governance, reconciliation, audit.
- Courier-to-central referral commands and event status/replay.
- Inbound and outbound webhooks.

## 11.4 Example resource

```json
{
  "data": {
    "id": "019...",
    "reference_number": "CBY-NBO-2026-00001234",
    "request_status": "confirmed",
    "payment_requirement_status": "satisfied",
    "operational_status": "assigned",
    "record_lifecycle_status": "active",
    "version": 7,
    "links": {"self": "/api/v1/deliveries/019..."}
  },
  "meta": {"correlation_id": "cor_..."}
}
```

## 11.5 Pagination

- Offset pagination for small administrative lists.
- Cursor pagination for deliveries, audit logs, locations, notifications, events, and payment histories.
- Maximum page sizes are enforced server-side.
- Total counts are optional and omitted on expensive cursor endpoints unless a separate count query is justified.

## 11.6 Idempotency contract

The server stores actor, tenant, operation ID, key hash, request hash, result status, response body hash or replayable body, resource ID, and expiry. Same key plus same request returns original result. Same key plus different request returns `409 idempotency_conflict`. In-progress duplicate returns `409 idempotency_in_progress` or waits within a bounded lock.

## 11.7 Webhooks

Outbound webhook envelope:

```json
{
  "event_id": "evt_...",
  "event_type": "delivery.completed",
  "event_version": "1.0",
  "occurred_at": "2026-06-27T12:00:00Z",
  "merchant_id": "mer_...",
  "data": {},
  "metadata": {"correlation_id": "cor_..."}
}
```

Signature covers method, path, timestamp, body digest, event ID, key ID, and algorithm. Retries use exponential backoff with jitter and attempt caps. A 2xx acknowledgment completes delivery; selected 4xx errors stop retry; 408/429/5xx retry. Replay is explicit and audited.

## 11.8 API logging

Log operation ID, actor domain/id, tenant, branch/organization scope, status, latency, correlation ID, query count, response size, idempotency outcome, and error code. Do not log raw bodies for auth, payments, proofs, files, contact details, OTP, or secrets.


# 12. UI/UX Design System

## 12.1 Brand direction

The interface is a “warm operating system for delivery”: operationally precise, calm, and human. Use the approved Courier palette:

| Token | Light value | Intended use |
|---|---|---|
| `brand-plum` | `#4A0637` | Navigation, headings, strong brand surfaces. |
| `brand-mulberry` | `#2B0425` | Dark surfaces and high-emphasis text. |
| `brand-coral` | `#FF5A45` | Primary CTA and route emphasis; not small text on white. |
| `brand-apricot` | `#FFB84D` | Warm proof/confirmation accents. |
| `surface-cream` | `#FFF4E8` | Marketing/onboarding/empty-state background. |
| `surface-white` | `#FFFFFF` | Main cards and application surface. |
| `text-primary` | `#24111F` | Body text. |
| `text-muted` | `#7A6A73` | Secondary text subject to contrast testing. |
| `border` | `#EADDD7` | Dividers and controls. |
| `info` | `#3B82F6` | Route/ETA/system information. |
| `success` | `#22A06B` | Delivered, validated, paid. |
| `warning` | `#F59E0B` | Pending/caution. |
| `error` | `#D92D20` | Failed/cancelled/disputed. |

Typography: Manrope for brand/headings/buttons, Inter for dense operational UI, tabular numerals for money/metrics.

## 12.2 Token architecture

CSS custom properties define semantic tokens, never raw color names in domain components:

```css
:root {
  --color-bg-canvas: ...;
  --color-bg-surface: ...;
  --color-text-primary: ...;
  --color-action-primary: ...;
  --color-border-default: ...;
  --color-status-success: ...;
  --radius-control: 0.75rem;
  --space-4: 1rem;
}
```

Tailwind maps utilities to these properties. The design-system package contains tokens, components, Storybook/Histoire examples, accessibility tests, and visual regression snapshots.

## 12.3 Required components

- Buttons: primary, secondary, tertiary, danger, loading, icon-only.
- Inputs: text, email, phone, money, date/time, address autocomplete, OTP, file.
- Data display: cards, metric tiles, tables, timelines, status badges, audit diffs, money lines.
- Feedback: inline errors, alert banners, toasts, progress, skeletons, empty states.
- Navigation: desktop sidebar, mobile drawer, top bar, breadcrumbs, command palette, profile menu.
- Overlays: dialog, sheet, popover, confirmation, destructive confirmation with typed reason where required.
- Domain: map picker, route stop list, delivery state timeline, quote builder, allocation grid, cash-up reconciliation, payout approval, proof viewer.

## 12.4 Status presentation

Every state uses text plus icon; color alone is forbidden. Derived labels are read-only and must not hide the underlying orthogonal states when operationally relevant. Financial screens show transaction, allocation, invoice settlement, dispute, and refund states separately.

## 12.5 Role landing pages

Each role sees action queues and exceptions, not a generic dashboard. Examples:

- Dispatch: unquoted requests, confirmed unassigned deliveries, rejected assignments, pickup delays, failed attempts.
- Finance: payments awaiting validation, reconciliation exceptions, refund approvals, cash-up reviews, period locks.
- Personnel: next assignment, required proof, offline sync conflicts, own earnings.
- Platform Integration: failed product events, webhook dead letters, key rotation, provider health.

## 12.6 Motion

Motion is restrained and functional. Respect `prefers-reduced-motion`. No animated state transition may delay command confirmation or obscure audit-important changes.


# 13. Responsive Layout Strategy

## 13.1 Breakpoints

- Mobile: `<= 767px`.
- Tablet: `768px–1024px`.
- Desktop: `>= 1025px`.

Use CSS media/container queries only. JavaScript must not determine layout mode or device type.

## 13.2 Layout behavior

| Surface | Desktop | Tablet | Mobile |
|---|---|---|---|
| App shell | Fixed/collapsible sidebar + top bar + content. | Collapsed rail or overlay drawer. | Top bar + off-canvas drawer; bottom actions where task-critical. |
| Dashboard | 12-column grid, multiple panels. | 8-column, panels wrap. | Single column, priority queues first. |
| Data tables | Full columns, sticky header/actions. | Hide nonessential columns; column chooser. | Card/list representation or controlled horizontal region for irreducible tabular data, never whole-page scroll. |
| Forms | Two-column logical groups where safe. | One/two column by available width. | Single column; sticky submit area for long operational forms. |
| Settings | Left sub-navigation + panel. | Compact tabs/drawer. | Stacked sections with anchor menu. |
| Profile menu | Anchored popover. | Anchored/edge-aware popover. | Full-width sheet. |
| Modals | Centered bounded dialog. | Centered or side sheet. | Full-screen dialog/sheet with visible close/back. |
| Dispatch board | Multi-column/board plus map. | Toggle board/map. | Queue list and task detail; no dense drag-and-drop requirement. |
| Personnel PWA | Compact operational cards. | Same, wider detail. | Primary target; one-handed actions and 44px+ controls. |

## 13.3 Reflow and zoom

- Test 200% zoom at 1280 CSS pixels and WCAG reflow widths.
- No fixed heights for text containers.
- Use `minmax(0, 1fr)` and `min-width: 0` to prevent overflow.
- Long IDs and URLs wrap or truncate with accessible full-value disclosure.
- Maps and complex charts have accessible list/table alternatives.
- Only intentionally scrollable components may overflow horizontally, with labels and keyboard support.

## 13.4 Touch and field use

- Minimum target 44×44 CSS pixels.
- Bottom action bars avoid browser chrome/safe areas.
- Camera/file inputs work on mobile without requiring native apps.
- Offline indicators remain visible but do not block emergency task completion.


# 14. Dark Mode Strategy

## 14.1 Behavior

- Light is the default for first visit.
- User chooses `light`, `dark`, or optionally `system` after launch approval.
- Authenticated preference persists in user settings; local storage/cookie provides pre-auth and immediate bootstrap preference.
- Server renders/serves a tiny inline theme bootstrap script before CSS to prevent flash. It reads only a non-sensitive theme value.

## 14.2 Tokens

Dark mode remaps semantic tokens; components do not use ad hoc dark classes for every raw color. Plum/mulberry surfaces are adjusted to preserve hierarchy; coral/apricot are used as accents, not body text. Borders, focus rings, validation errors, disabled states, map controls, charts, and proof imagery remain visible.

## 14.3 Persistence conflict

On login, server preference wins unless the account has never stored a preference, in which case local preference is adopted. Preference updates are optimistic but roll back on API failure.

## 14.4 Testing

- Automated contrast checks on both themes.
- Visual regression for every component state.
- E2E persistence across reload/login/logout.
- No flash test using screenshot at first paint.
- Keyboard/focus tests in both themes.


# 15. Accessibility Strategy

Target WCAG 2.2 AA for product surfaces.

## 15.1 Required implementation

- Semantic landmarks, headings, lists, tables, and buttons.
- Skip links and logical focus order.
- Visible focus that meets contrast requirements.
- Labels for all controls; placeholders never replace labels.
- `aria-describedby` links errors/help to inputs.
- Error summary receives focus after invalid submit and links to fields.
- Dialog focus trap, initial focus, Escape behavior, and focus return.
- Status changes announced through restrained live regions.
- Icons have accessible names or are hidden when decorative.
- Drag/drop has keyboard alternatives.
- Maps have textual address, route, and stop alternatives.
- Charts have data tables or summaries.
- OTP inputs support paste and screen readers without forced auto-advance traps.
- Respect reduced motion, forced colors, text spacing, browser zoom, and user font scaling.

## 15.2 Testing

- ESLint accessibility rules.
- axe-core in component and E2E tests.
- Keyboard-only test scripts for critical flows.
- NVDA + Chrome/Firefox and VoiceOver + Safari spot checks before release.
- 200% and 400% zoom/reflow tests on critical pages.
- Manual accessibility acceptance evidence stored per release.


# 16. Forms and Input Behavior Strategy

## 16.1 Form architecture

- Zod schema on frontend for immediate feedback.
- Laravel Form Request is authoritative server validation.
- Shared constraints may be generated from OpenAPI, but complex business rules remain server-side.
- Each form has a typed DTO and explicit mapping; never submit an entire reactive model blindly.
- Backend uses `validated()` data and explicit command constructors; `$request->all()` is forbidden.

## 16.2 Behavior

- Required fields marked in label and accessible metadata.
- Submit button disables only while the same command is in flight; idempotency key protects double submit.
- Unsaved-change warnings only when actual dirty state exists.
- Long forms are step-based with server-side draft persistence where abandonment risk is material.
- Field errors are specific and corrective; global errors preserve entered non-sensitive values.
- Server conflict `409` presents refresh/compare/retry choices, never silent overwrite.
- Money fields parse decimal display into minor units using currency rules; floats never reach API.
- Phone fields store normalized E.164 plus display format; contacts are verified separately.
- Address form stores provider IDs, coordinates, formatted address, manual instructions, and immutable delivery snapshot.

## 16.3 Sensitive forms

Payment, MFA, secret rotation, refund, payout, and high-risk approval forms require reauthentication/step-up, explicit confirmation, and reason where required. Secret values are shown once and are not re-rendered after submission.

## 16.4 Error and edge cases

- Network timeout after submit: query idempotency result before offering retry.
- Validation changes while draft exists: version draft schema and migrate or request correction.
- File scan pending: form shows pending state and prevents dependent finalization until accepted.
- Browser back/reload: recover safe draft; never store OTP, passwords, secrets, or complete payment payloads.


# 17. User Profile and Account UI Strategy

## 17.1 Profile control

The header profile unit combines avatar/initials, display name, active merchant or platform context, role summary, and menu trigger. It has clear hover/focus, 44px target, keyboard operation, and edge-aware placement.

Menu contents:

- Profile.
- Security and active sessions.
- Notification preferences.
- Theme.
- Active merchant/organization switcher where authorized.
- Role/onboarding status.
- Help/support.
- Sign out.

## 17.2 Account switcher

- Lists only active memberships returned by the server.
- Context switch is a POST command requiring CSRF and session rotation.
- Clears all client caches, websocket subscriptions, persisted filters, and PWA data belonging to the previous tenant.
- Displays pending/suspended context without allowing entry where prohibited.
- Platform users do not see merchant switcher.

## 17.3 Profile preview and settings

- Desktop preview is anchored and collision-aware.
- Mobile uses a full-width sheet.
- Profile photo upload uses the private file pipeline and approved image transformations.
- Identity contact changes require verification and may revoke outstanding authentication challenges.
- High-risk changes emit security notifications.

## 17.4 Role-specific account areas

Merchant staff can view role grants and branch scope but cannot self-edit privileged grants. Delivery Personnel sees availability/security/earnings settings. Business-customer administrators manage organization members in a separate organization settings surface. Platform users see MFA, devices, grants, onboarding, and privileged-session history.


# 18. Billing and Plan Enforcement Strategy

## 18.1 Separate financial domains

1. **Merchant-customer finance:** the sender/customer pays the merchant for delivery services.
2. **Platform billing:** the merchant pays Citrus Labs for subscription, percentage fees, SMS, branches, storage, and add-ons.

Tables, services, permissions, invoices, payments, receipts, reconciliation, reports, and audit are separate. Customer money must never be allocated directly to Citrus platform invoices.

## 18.2 Platform billing modes

- `fixed_amount`.
- `percentage_on_merchant_customer_invoice`.
- `fixed_amount_plus_percentage_on_merchant_customer_invoice`.

Effective-dated `platform_billing_mode_versions` are the source of truth. Fixed recurring charges come from versioned plan prices. There is no unsupported fixed fee repeated on every customer invoice.

## 18.3 Entitlement enforcement

`EntitlementService` evaluates:

- active subscription and effective plan version;
- plan entitlements and numerical limits;
- add-ons and branch-specific allocation;
- scheduled plan changes;
- billing status/restricted mode;
- feature flags that cannot create an entitlement not sold to the tenant.

Enforcement occurs in command handlers and policies, not only UI. Limit increments use database locks or atomic counters. A failed entitlement check returns `403 entitlement_required` or `409 limit_reached` without partial writes.

## 18.4 Billing lifecycle

`trialing → active_subscription → overdue → read_only_grace → suspended_billing`, with configured recovery. Operational merchant status remains separate. Payment can clear only billing-based suspension; fraud/security/legal/manual suspension remains.

Restricted mode uses one server-side allowlist, for example:

- view billing overview/invoices;
- initiate/monitor platform payment;
- view/export legally required historical records within policy;
- contact support;
- security/profile actions.

New delivery intake, assignments, or privileged exports are blocked as specified.

## 18.5 M-Pesa platform payment

1. Authorized user initiates STK Push for a specific invoice.
2. Create payment attempt with idempotency and payer context.
3. Queue provider request after commit.
4. Store provider identifiers and safe response.
5. Callback verifies provider authenticity and deduplicates reference.
6. Create transaction and allocation only after validation.
7. Reconcile invoice balance.
8. Generate receipt/notification.
9. Run billing-only reactivation when eligible.

Initiation never marks paid. Duplicate or excess payments create controlled overpayment handling, not silent loss.

## 18.6 Percentage fee

Customer invoice issue/settlement creates or updates percentage-fee ledger entries from captured billing configuration. Refunds, reversals, voids, and credits append adjustments. Periodic aggregation creates platform invoice lines. Issued platform invoices preserve snapshots.

## 18.7 Referral facts

Courier emits factual subscription invoice/payment/activity events to the central platform. It stores central attribution reference and event evidence only. A central outage cannot block merchant registration or payment; rewards remain pending centrally until events reconcile.


# 19. File Upload and Storage Strategy

## 19.1 File classes

| Class | Examples | Default max |
|---|---|---|
| Images | goods, pickup/delivery proof, incidents, profile, vehicle | 10 MB each before server-side normalization. |
| Documents | invoices, receipts, staff/vehicle/business docs, disputes | 20 MB. |
| Signatures | vector/raster signature capture | 2 MB. |
| Exports | generated CSV/XLSX/PDF/ZIP | system-generated only, size-limited by job policy. |

Limits are configurable by file purpose and plan. Accepted MIME types are allowlisted per purpose. Extension and MIME must agree; magic-byte inspection is mandatory.

## 19.2 Upload flow

1. Authorize upload purpose and tenant/resource ownership.
2. Create `file_assets` row with `quarantined` state and one-time upload intent.
3. Upload to quarantine prefix using signed multipart upload with content-length/type constraints.
4. Finalize endpoint verifies object existence, size, digest, declared metadata, and ownership.
5. Queue malware scan and image/document inspection.
6. On pass, move/copy to immutable private object key and mark available.
7. On fail, isolate/delete according to security policy and create security/audit event.
8. Attach through an explicit domain command; attachment itself is authorized.

## 19.3 Object keys

```text
private/{environment}/{merchant_uuid}/{purpose}/{yyyy}/{mm}/{file_uuid}/{version}
quarantine/{environment}/{upload_intent_uuid}
exports/{environment}/{merchant_uuid}/{export_uuid}
```

Original user filenames are metadata only, sanitized, and never used as object keys.

## 19.4 Access

Downloads use an authenticated endpoint or short-lived signed URL created after `FileAccessService` policy evaluation. URL lifetime is purpose-sensitive. Sensitive downloads create `file_download_events`. CDN caching is disabled for private objects unless signed/private CDN behavior is proven safe.

## 19.5 Processing and cleanup

- Strip unsafe image metadata unless required for proof evidence; preserve necessary original in protected form when legally needed.
- Generate thumbnails asynchronously.
- Detect decompression bombs, polyglots, macro-enabled documents, oversized dimensions, malformed files, and password-protected archives.
- Orphan upload intents expire and are deleted by scheduled job.
- Legal/audit holds override normal deletion.
- Object lifecycle policies match database retention and must not delete referenced evidence early.


# 20. Queue, Jobs, Notifications, and Scheduled Task Strategy

## 20.1 Queue topology

| Queue | Work | Target |
|---|---|---|
| `critical` | payment callbacks, transaction allocation, billing recovery, security revocation, state-critical outbox | Low latency, isolated workers. |
| `integration` | referral events, outbound webhooks, M-Pesa requests/reconciliation | High retry discipline. |
| `notifications` | email, SMS, WhatsApp, in-app fan-out | Provider-aware throttling. |
| `default` | routine domain listeners, projections | General pool. |
| `files` | scanning, thumbnails, metadata | CPU/memory bounded. |
| `reports` | exports, statements, analytics | Heavy isolated pool. |
| `search` | index updates/rebuilds | Best-effort, replayable. |

Jobs have explicit timeout, retry count, backoff, uniqueness/overlap locks, tags, tenant context, and failure classification. Infinite retries are prohibited.

## 20.2 Required scheduled jobs

- Platform invoice generation.
- Percentage-fee aggregation.
- Trial/grace/overdue/suspension transitions.
- Overdue reminders.
- Plan price and scheduled plan-change activation.
- M-Pesa and customer gateway reconciliation.
- Salary accrual and commission calculation.
- Payout statement generation.
- SMS cost rollup.
- Notification and webhook retry.
- Tracking token expiry.
- File scan/orphan cleanup and document expiry reminders.
- Stale delivery/assignment alerts.
- Billing-only reactivation.
- Outbox publication, dead-letter alerting, referral event reconciliation, safe replay review, key-rotation readiness.
- Audit hash verification, retention, partitions, and backup verification.

Time windows and monetary values come from versioned configuration, never hardcoded.

## 20.3 Notifications

A domain event creates a notification intent. `NotificationService`:

1. Determines recipients and scope.
2. Applies consent/preferences and mandatory transactional exceptions.
3. Selects versioned localized template.
4. Builds a privacy-minimized DTO.
5. Persists `notifications` and channel deliveries.
6. Dispatches through provider adapter.
7. Records delivery status and retries.

Provider callbacks update delivery evidence idempotently. Templates cannot execute arbitrary code and do not receive whole Eloquent models.

## 20.4 Failure handling

- Retryable provider failures use exponential backoff and circuit breaker.
- Permanent validation errors move to dead letter with safe reason.
- Financial jobs never silently discard partial failures.
- Payout runs record per-item result; one item failure does not falsely mark the whole run settled.
- Operators receive dashboards/alerts with replay controls restricted by permission and idempotency.


# 21. Search Strategy

## 21.1 Launch approach

Use PostgreSQL full-text search, `pg_trgm`, normalized searchable columns, and scoped indexes for launch. This avoids an unnecessary second source of query truth while data volume is unproven.

Searchable domains:

- delivery reference, sender/recipient display names, normalized phone/email hashes, addresses;
- invoice/receipt/payment references;
- staff and vehicles within permitted scope;
- support tickets;
- audit metadata for authorized auditors.

Sensitive exact matches use normalized hashes and policy-controlled display; never expose global contact search.

## 21.2 Search abstraction

`SearchGateway` exposes typed queries. The application never calls Meilisearch directly from controllers. When measured thresholds are exceeded, Meilisearch can be enabled with:

- merchant ID as mandatory filter;
- branch/organization/personnel scope filters;
- per-index allowed searchable/filterable fields;
- outbox-driven indexing;
- delete/update tombstones;
- rebuild from PostgreSQL;
- hydration and final authorization through PostgreSQL.

## 21.3 Trigger thresholds

Introduce external search when one or more are sustained:

- P95 scoped search latency exceeds 300 ms after index/query optimization;
- a tenant/domain exceeds roughly one million searchable rows;
- fuzzy/multifield relevance requirements cannot be met acceptably in PostgreSQL;
- search load materially affects transactional database capacity.

## 21.4 Security

Search results never bypass policies. No cross-tenant autocomplete. Logs do not store raw sensitive search strings beyond approved redacted telemetry. Rate limits and minimum query lengths prevent enumeration.


# 22. Observability and Audit Logging Strategy

## 22.1 Telemetry

- Structured JSON logs with correlation, trace, actor domain, tenant, operation ID, route, status, latency, and safe error code.
- OpenTelemetry traces across HTTP, queues, database, Redis, external providers, outbox, and webhooks.
- Metrics for request rate/error/latency, DB pools, slow queries, queue depth/age/failures, cache hit ratio, websocket connections, provider success, billing/reconciliation exceptions, file scans, and event dead letters.
- Error tracking with source maps and PII scrubbing.
- Synthetic checks for registration, login, tracking, billing payment initiation, callback endpoint health, and file access.

## 22.2 Audit events

Audit at minimum:

- registration/setup and identity/security events;
- merchant/branch status and governance;
- roles, permissions, branch assignments, invitations, employment;
- catalogue, zones, pricing, quote override/approval;
- customer/contact/address access and changes;
- delivery create/edit/cancel and every state transition;
- assignment, reassignment, custody handover, route changes;
- proof, OTP, signature, photo, file access;
- incidents, failures, returns, disputes, refunds;
- invoice, payment, validation, allocation, receipt, reference override;
- cash-up, lock, reopen;
- compensation, payout, earnings query;
- billing mode, plan price, promotions, platform fee, M-Pesa recovery;
- API credentials, webhooks, integration events, replay, dead letters;
- exports/downloads;
- denied/unauthorized high-risk attempts.

## 22.3 Audit integrity

- Append-only DB permissions.
- Hash chain by partition/tenant stream with periodic signed checkpoint.
- Safe before/after values; secrets and unnecessary PII excluded.
- Time synchronized through trusted NTP.
- Verification job detects gaps/hash mismatch and raises critical alert.
- Audit export requires permission, reason, scope, and signed expiring download.

## 22.4 Alerts and SLOs

Initial service objectives:

- Core API availability: 99.9% monthly excluding approved maintenance.
- P95 ordinary API latency: under 500 ms; P99 under 1.5 s, excluding provider-dependent operations.
- Critical queue age: under 30 seconds normal operation.
- Payment/referral callback acknowledgment: under provider timeout, with internal processing queued.
- RPO: 15 minutes maximum for primary DB; target lower with continuous WAL archiving.
- RTO: 2 hours for core service, 4 hours for noncritical reporting.

Alert on error-budget burn, failed backup, replica lag, queue age, payment exception rate, dead letters, audit hash failure, secret expiry, certificate expiry, disk/storage pressure, and abnormal cross-tenant denial patterns.


# 23. Performance and Scalability Plan

## 23.1 Likely bottlenecks

| Bottleneck | Mitigation |
|---|---|
| Dispatch board polling/realtime fan-out | Event-driven updates, branch channels, delta payloads, query cache, polling fallback. |
| Driver location writes | Sampling policy, Redis latest-position cache, batch persistence, time partitioning, retention/downsampling. |
| Dashboard aggregates | Incremental projections/materialized views, asynchronous refresh, bounded freshness labels. |
| Invoice/payment reconciliation | Indexed provider references, idempotency, dedicated critical workers, advisory locks. |
| File proof uploads | Direct multipart S3 upload, asynchronous scan/thumbnail, size limits. |
| Reports/exports | Queue jobs, read replica, snapshot query, chunked writers, expiring download. |
| Audit/history growth | Partitioning, BRIN indexes, archival tiers, immutable retention. |
| External providers | Timeouts, circuit breakers, bulkheads, retries, degraded mode, operator exception queues. |
| Tenant hotspots | Per-tenant rate/queue fairness, cache isolation, query budgets, future sharding readiness. |

## 23.2 Database practices

- Explicit select lists and eager loads.
- Cursor pagination for high-volume chronological records.
- Query-plan review for critical endpoints using production-like data.
- Connection pooling with transaction mode compatible with RLS settings.
- Read replicas for reports only; never read-after-write critical decisions unless consistency is assured.
- Advisory locks for singleton financial generation/reconciliation by tenant/period.
- Bounded batch sizes and resumable backfills.

## 23.3 Caching

Cache only derived/read data. Keys include tenant and version. Invalidation occurs through domain events. Never cache authorization decisions beyond the session/role-grant version without revocation capability. Payment, lock, and transition decisions read authoritative state.

## 23.4 Frontend performance

- Route-level code splitting.
- Lazy-load maps, charts, rich editors, proof viewers.
- Virtualize long lists only with accessible alternatives.
- Image responsive sizing and modern formats for non-evidence imagery.
- Bundle budget per route and total initial JS budget.
- Avoid large global stores and deep reactive copies of server datasets.

## 23.5 Capacity validation

Before launch, load test at least:

- 500 concurrent authenticated users across tenants;
- 100 concurrent dispatch users with realtime updates;
- 5,000 active tracking sessions;
- 50 location updates/second sustained with bursts;
- 20 payment callbacks/second burst;
- 100,000 deliveries and related history in a large tenant dataset;
- export and report jobs while core API remains within SLO.

These are initial validation targets, not claimed final limits. Production telemetry determines scaling thresholds.


# 24. Security Threat Model

| Threat | Attack path | Controls | Verification |
|---|---|---|---|
| Cross-tenant IDOR | Valid UUID from another tenant | Tenant-first query, RLS, policy, opaque errors | Automated matrix across every tenant resource. |
| Broken branch/own scope | User accesses unassigned branch/job | Separate scope service + permission policy | Branch and personnel denial tests. |
| Privilege escalation | Self-grant, stale role/session | Maker/checker, grant version, session revocation, last-owner protection | Role-grant and revoked-session tests. |
| Magic-link theft/replay | Forwarded/reused token | Short expiry, hash, single use, device/risk checks, sibling invalidation | Replay and deactivation tests. |
| OTP brute force | Repeated guesses | Slow hash, attempt cap, cooldown, rate limit, risk event | Boundary and distributed-attempt tests. |
| CSRF/session fixation | Forged browser mutation | Sanctum CSRF, SameSite, origin checks, session rotation | CSRF and session ID tests. |
| SQL injection/mass assignment | Crafted filters/payload | Query allowlists, binding, Form Requests, explicit command DTOs | Fuzz/API security tests. |
| Stored XSS | Notes/messages/names | Escaped rendering, sanitized limited rich text, CSP | Component and browser payload tests. |
| File malware/polyglot | Malicious proof/document | Quarantine, magic bytes, scan, parser isolation, signed access | EICAR/polyglot/decompression tests. |
| SSRF | Webhook/provider URL | HTTPS, DNS/IP validation, private range block, re-resolution, egress policy | SSRF test suite. |
| Webhook spoof/replay | Forged provider callback | Signature, timestamp, nonce/event ID, idempotency, secret rotation | Invalid signature and replay tests. |
| Payment duplication | Callback retry/race | Unique provider reference, row/advisory locks, idempotent allocation | Concurrent callback tests. |
| Financial maker/checker bypass | Same actor uses multiple steps | Actor separation at policy and DB constraints, approval invalidation after edit | Approval matrix tests. |
| State race | Two assignment/refund commands | Aggregate version, transaction locks, first-commit wins | Concurrent integration tests. |
| Sensitive log leakage | Provider/body exception logs | Central redaction, logging allowlist, secret scanners | Log assertion tests. |
| Object URL leakage | Guess/public bucket | Private bucket, block public access, signed short URLs | Cloud policy and anonymous access tests. |
| Search leakage | Missing tenant filter | Server-injected filter + DB hydration | Search cross-tenant tests. |
| Cache collision | Unscoped key | Tenant/version key builder | Cache isolation tests. |
| Websocket leakage | Guess channel | Private auth, tenant/resource policy | Channel auth tests. |
| Referral event forgery | Fake qualifying facts | Service identity, detached signature, event schema/version, outbox | Signature and duplicate-event tests. |
| Insider platform misuse | Broad admin access | Separate platform roles, masking, step-up, reason, audit, no impersonation | Privileged workflow review and audit proof. |
| Supply-chain compromise | Malicious dependency/image | Lockfiles, SBOM, signed images, dependency scanning, provenance | CI gates and image verification. |
| Backup exposure | Unencrypted backup | Encryption, isolated credentials, access logs, restore policy | Quarterly restore and access review. |

A formal STRIDE/LINDDUN review must be updated per release. High/critical findings block release until fixed or explicitly accepted by authorized security/product owners with expiry.


# 25. Testing Strategy

## 25.1 Test layers

1. Unit tests for value objects, calculations, transition predicates, permission evaluation, and adapters.
2. Domain/application tests for command handlers and state machines.
3. Feature/API tests against PostgreSQL and Redis.
4. Contract tests for OpenAPI, providers, webhooks, and referral events.
5. Vue component tests.
6. Playwright E2E tests for critical role workflows.
7. Security tests for access control, abuse, secrets, upload, and headers.
8. Concurrency/idempotency tests.
9. Migration and rollback tests on production-sized anonymized fixtures.
10. Load, soak, failover, backup/restore, and disaster-recovery tests.

## 25.2 Naming and location

```text
apps/api/tests/Unit/<Context>/...
apps/api/tests/Feature/Api/V1/<Context>/...
apps/api/tests/Feature/Authorization/...
apps/api/tests/Feature/Tenancy/...
apps/api/tests/Feature/Concurrency/...
apps/api/tests/Contract/...
apps/web/src/**/*.spec.ts
apps/web/e2e/**/*.spec.ts
infra/tests/...
```

## 25.3 Required module matrix

| Module | Positive | Negative/validation | Isolation/authorization | Edge/concurrency |
|---|---|---|---|---|
| Registration/auth | Valid registration, Magic Link, sender OTP, platform MFA | Expired/reused token, invalid OTP, suspended user | Neutral unknown user/merchant; identity-domain mismatch | Concurrent consume; revocation during consume. |
| Merchant/branch | Create/update within entitlement | Invalid fields, closure blockers | Other tenant, unassigned branch, missing permission | Last branch, active jobs, plan downgrade. |
| Staff/roles | Invite, accept, activate, grant | Duplicate invite, incompatible role | Self-grant, final owner/admin, cross-branch | Concurrent role change, stale session revocation. |
| Delivery/quotes | Complete request/quote/confirm | Missing address/goods, expired quote | Sender/dispatch/branch scope | Duplicate confirm, stale quote, maker/checker. |
| Assignment/state | Assign, accept, pickup, deliver | Ineligible personnel/vehicle, invalid transition | Other personnel own-scope | Two assignments, offline stale command, handover atomicity. |
| Proof/files | Valid OTP/photo/signature | Bad MIME, malware, missing evidence | Unauthorized file/download | Duplicate proof, scan pending/fail. |
| Customer finance | Invoice, record/validate, allocate, receipt | Over-allocation, duplicate ref, invalid currency | Dispatch cannot validate; other tenant | Concurrent callbacks, partial/split, refund ceiling. |
| Platform billing | Invoice, STK, callback, recovery | Invalid payer, duplicate payment | Dispatch denied by default | Double callback, overpayment, nonbilling suspension. |
| Compensation/payout | Prepare, verify, approve, settle | Invalid rule/amount | Same actor prohibited | Edit invalidates approval, partial payout failures. |
| Cash-up/locks | Submit, correct, approve, lock/reopen | Missing evidence | Role separation | Mutation during lock, concurrent reopen. |
| Referral integration | Capture, outbox, sign, acknowledge | Invalid code/signature/schema | No central reward data exposure | Duplicate/out-of-order/replay/outage. |
| UI/PWA | Critical workflows per role | Error/empty/loading | Hidden action still denied by API | Offline sync conflict, responsive/dark/a11y. |

## 25.4 Tenant isolation suite

Create two merchants with overlapping-looking data and iterate every resource route. For every operation, assert:

- Account A cannot list, fetch, mutate, export, search, subscribe to, or download Account B data.
- Errors do not reveal existence.
- SQL query includes tenant constraint or RLS denies.
- No cache/search/websocket/file collision.
- Audit records remain correctly scoped.

This suite is generated from the canonical route registry so a new tenant route cannot ship without an isolation case.

## 25.5 State-machine tests

Generate one positive test for every transition and negative tests for:

- every invalid from-state;
- wrong actor/permission/scope;
- missing evidence;
- billing/entitlement/lock denial;
- stale aggregate version;
- duplicate idempotency key conflict;
- state history and audit/outbox atomicity.

## 25.6 Coverage and release gates

Coverage percentages are not accepted as sole quality proof. Minimum practical targets:

- 90%+ branch coverage for money, permission, state-machine, idempotency, and lock services.
- 80%+ overall backend line coverage.
- Critical frontend workflows covered by component and E2E tests.
- Zero skipped critical security/isolation tests.
- Mutation testing for selected financial and authorization modules.

## 25.7 Example commands

```bash
make lint
make test-unit
make test-feature
make test-tenant-isolation
make test-contract
make test-frontend
make test-e2e
make test-security
make test-migrations
make test-load-smoke
```

Every phase evidence pack includes command, environment, commit SHA, result, failed-test disposition, API examples, DB assertions, and screenshots where applicable.


# 26. Deployment and CI/CD Strategy

## 26.1 Environments

- Local development.
- Ephemeral pull-request environment where cost permits.
- Shared integration.
- Staging mirroring production topology and providers' sandbox.
- Production.

No production secrets/data are copied into lower environments. Sanitized representative fixtures are used.

## 26.2 Containers

Images:

- `courier-api` multi-stage PHP-FPM/runtime image.
- `courier-web` static build image/artifact.
- worker and scheduler use same API image with different commands.
- `reverb` same code image or dedicated process.

Run as non-root, read-only filesystem where possible, dropped capabilities, health endpoints, pinned base image digest, SBOM, and signed image provenance.

## 26.3 CI pipeline

1. Validate lockfiles and generated artifacts are current.
2. Secret scanning and commit policy.
3. Dependency/license audit.
4. PHP lint, Pint, PHPStan, architecture tests.
5. TypeScript, ESLint, frontend unit/component tests.
6. Registry/schema/OpenAPI semantic lint.
7. PostgreSQL feature, authorization, tenancy, idempotency, migration tests.
8. Playwright critical E2E.
9. Build assets/images.
10. Container and IaC scanning.
11. Generate SBOM and sign artifacts.
12. Deploy ephemeral/staging.
13. Smoke/contract/security checks.
14. Manual approval for production by required owners.

## 26.4 Deployment sequence

1. Confirm backups and restore point.
2. Apply backward-compatible expand migrations.
3. Deploy API/worker version capable of old and new schema.
4. Deploy web assets.
5. Run post-deploy smoke and synthetic tests.
6. Start bounded backfills if required.
7. Validate metrics/error budget.
8. Switch feature flag/read path.
9. Apply contract/enforcement migration in later release.
10. Record release checksum and evidence.

## 26.5 Rollback

Application rollback must remain schema-compatible. Destructive migrations are never in the same release as first code use. Rollback runbooks define:

- image rollback;
- feature flag disable;
- queue pause/drain;
- provider failover/degraded mode;
- migration forward-fix or restore decision;
- event replay protection;
- communication and incident ownership.

## 26.6 Infrastructure

Provision with Terraform/Pulumi or equivalent:

- network/private subnets/security groups;
- load balancer/WAF/TLS;
- container runtime;
- managed PostgreSQL with PITR, replicas, encryption;
- managed Redis;
- object storage with block-public-access and lifecycle;
- secret manager/KMS;
- logging/metrics/tracing;
- DNS/CDN;
- backup vault and cross-region/account copy where feasible.

## 26.7 Health endpoints

- `/health/live`: process only.
- `/health/ready`: DB/Redis/required registry loaded; no expensive third-party calls.
- `/health/dependencies`: restricted platform endpoint with provider status.
- `/health/version`: release SHA/checksum, no secrets.

## 26.8 Backup and disaster recovery

- Continuous WAL/PITR and daily snapshots.
- Object versioning and retention.
- Encrypted backup copies under separate credentials.
- Quarterly restore drills and annual full DR exercise.
- Restore validation includes tenant isolation, audit chain, files, and reconciliation state.


# 27. Step-by-Step Development Roadmap

Each phase must use the task evidence record and cannot proceed through its gate with unresolved critical defects.

## Phase 0 — Scope compilation and normative registries

**Objective:** Convert the authoritative scope into machine-verifiable contracts before application code.

**Create:** `registries/**`, `docs/adr/**`, `docs/traceability/capabilities.yaml`, schema and event JSON Schemas.

**Tasks:**

- Extract canonical roles, permissions, routes, state domains, errors, audit events, release classes, entitlements, and referral events.
- Assign stable capability and operation IDs.
- Create schema validators and checksum manifest.
- Prove no duplicate writer or contradictory enum remains.

**Tests/commands:** registry lint, duplicate semantic detection, route-to-permission/state coverage, checksum generation.

**Gate:** Every launch capability maps to owner, permission, route/command, UI surface where applicable, audit event, error behavior, and tests.

**Rollback/correction:** change registry version and migration map; never silently edit an active registry version.

## Phase 1 — Repository, Docker, environments, and quality tooling

**Create:** monorepo layout, Dockerfiles, Compose, Makefile, CI skeleton, `.env.example`, secret policy.

**Backend:** initialize Laravel 13; strict configuration; health endpoints.

**Frontend:** initialize Vue 3 TypeScript/Vite; lint/test scaffolding.

**Database:** PostgreSQL 18 local service; migration role/runtime roles.

**Security:** non-root containers, secret scanning, secure headers baseline.

**Commands:** `make bootstrap`, `make lint`, `make test`.

**Gate:** one-command local boot, no committed secret, reproducible builds, CI green.

## Phase 2 — Shared kernel, database conventions, tenant context, audit, idempotency, outbox

**Create:** `Support/Tenancy`, `Money`, `Concurrency`, `Idempotency`, `Audit`, `Outbox`.

**Tasks:** UUIDv7, money object, canonical errors, correlation IDs, tenant middleware, RLS roles/policies, audit append/hash, outbox/inbox, idempotency store.

**Tests:** missing tenant fails closed; cross-tenant RLS; duplicate idempotency; transaction rollback removes audit/outbox; money overflow/currency mismatch.

**Gate:** no domain module starts until these foundations pass.

## Phase 3 — Identity, sessions, platform authentication, Magic Links, OTP, recipient tokens

**Database:** global identity/auth tables, platform user tables, session/challenge/token tables.

**Backend:** platform password/passkey/MFA, merchant Magic Link, merchant-scoped sender auth, tracking token service, revocation.

**Frontend:** login/request/consume/MFA/recovery/token-expired screens.

**Security:** neutral responses, rate limits, session rotation, step-up.

**Tests:** replay, expiry, deactivation, brute force, cross-merchant sender context, identity-domain mismatch.

**Gate:** complete authentication threat-model suite and session revocation proof.

## Phase 4 — Merchant registration, merchant/branch foundation, onboarding

**Database:** merchants, profiles/settings/documents, branches, calendars, branch days, governance notes.

**Backend:** public self-registration transaction, setup workflow, branch CRUD/closure protection, role-specific onboarding progress.

**Frontend:** registration, plan selection shell, first branch, merchant/branch settings, get-started.

**Tests:** duplicate business signals, registration idempotency, no platform merchant creation route, branch closure blockers, tenant isolation.

**Gate:** merchant can self-register and enter isolated dashboard without manual platform approval.

## Phase 5 — Membership, employment, roles, permissions, invitations, emergency suspension

**Database:** merchant membership/employment/role/branch assignment/invitation/history tables.

**Backend:** permission registry evaluator, policies, HR lifecycle, owner protections, emergency suspension.

**Frontend:** staff list, invitation, role/scope editor, employment status, security suspension.

**Tests:** role matrix, self-approval, final owner, incompatible roles, revoked sessions, branch-without-permission denial.

**Gate:** generated authorization matrix passes for every active route.

## Phase 6 — Platform internal roles and governance

Implement platform users, role grants, onboarding, break-glass, merchant monitoring/suspension, billing configuration permission shells, audit views.

**Tests:** no platform user in merchant queries, mandatory MFA, last owner, no self-approval, break-glass expiry, no impersonation route.

## Phase 7 — Customer, organization, contact, address, consent

Implement senders, recipients, business organizations/memberships, merchant-scoped identity, address autocomplete/validation/pinning, consent and communication preferences.

**Tests:** duplicate/merge rules, cross-merchant contact isolation, final organization administrator, provider degradation/manual address path.

## Phase 8 — Catalogue, zones, vehicles, personnel eligibility/availability

Implement branch services, goods rules, pricing rule versions, zones/geography, vehicle/fleet records, personnel qualifications/documents/shifts/eligibility.

**Tests:** branch ownership, date-effective versions, overlapping zones/rules, expired qualification prevents assignment, maintenance vehicle denial.

## Phase 9 — Delivery request aggregate, items, stops, snapshots, request state machine

Implement draft creation, validation, address snapshots, goods restrictions, edits, references, request state history.

**Tests:** atomic request creation, command-only fields, restricted goods, concurrent edit, tenant/branch/sender scope.

## Phase 10 — Pricing, quote, approval, preferred-personnel request

Implement pricing engine, quote snapshots/lines, approval workflow, send/accept/reject/supersede, preferred-personnel fee/request/reservation without assignment authority.

**Tests:** calculation examples, rounding, expired rules, maker/checker, stale quote, preference cannot create assignment.

## Phase 11 — Customer invoices and payment-requirement state

Implement invoice creation by Dispatch, prepay/postpay/COD/credit/split/no-charge requirements, invoice settlement projection, receipt trigger framework.

**Tests:** money/currency, immutable issued invoice, requirement gating, credit limit, partial/split payment.

## Phase 12 — Dispatch, assignments, fleet checks, basic routing

Implement dispatch board, eligibility service, assignment offer/accept/reject/reassign, custody handover, routes, ETA, stop ordering, realtime events.

**Tests:** double assignment, ineligible personnel/vehicle, stale command, branch scope, atomic handover, no full optimizer route.

## Phase 13 — Personnel PWA and operational delivery state

Implement own-scope assignment list, pickup/transit/destination commands, offline command queue, location sampling, conflict UI.

**Tests:** own-scope denial, every transition, offline replay/idempotency, stale aggregate, reduced connectivity, mobile accessibility.

## Phase 14 — Proof, OTP, files, incidents, failures, returns, redelivery, disputes

Implement private file pipeline, proof methods, OTP, signatures, incidents, failed attempts, return/redelivery/dispute states.

**Tests:** malware/polyglot, unauthorized download, OTP replay, proof evidence requirements, delivered state survives dispute/refund.

## Phase 15 — Customer payment integration, offline maker/checker, COD, refunds

Implement gateway adapters, callbacks, attempts/transactions/allocations/reconciliation, offline payment, COD handover, refunds with approvals, disputes.

**Tests:** duplicate callbacks, signature failure, same actor denial, refund ceiling, partial failure, provider outage/reconciliation.

## Phase 16 — Platform subscription billing and M-Pesa recovery

Implement plans/prices/entitlements, billing modes, subscriptions, percentage fee ledger, invoices, M-Pesa attempts, grace/suspension/recovery, promotions/add-ons/SMS charges.

**Tests:** all three billing modes, snapshots, no mid-cycle proration, double payment, overpayment, restricted allowlist, nonbilling suspension not cleared.

## Phase 17 — Compensation, earnings, payout, branch cash-up and locks

Implement compensation versions/approvals, salary/commission ledgers, payout runs/results, cash-up revisions, period locks/reopen.

**Tests:** maker/checker, edit invalidation, high-value approval, partial payout failure, locked mutation denial, controlled reopen.

## Phase 18 — Notifications, support, audit surfaces, reports and exports

Implement templates/preferences/channel adapters, support workflow, audit flags, role dashboards, export jobs and signed downloads.

**Tests:** privacy filtering, opt-out rules, export scope, orphan cleanup, report totals versus source ledgers.

## Phase 19 — Business API, outbound webhooks, integration console

Implement integration principals/clients/credentials/scopes, webhook endpoints/subscriptions, signing, retry/dead letters, console.

**Tests:** one-time secret, rotation overlap, revoked credential, SSRF, signature/replay, plan downgrade.

## Phase 20 — Citrus Refer & Earn integration

Implement referral code capture, minimal snapshot, central validation, transactional product-event outbox, detached signatures, retries, status/replay, reconciliation cases, native campaign/attribution UI.

**Tests:** central outage does not block registration, duplicate/out-of-order events, incorrect fact hold, key rotation, no central payout/private data in Courier.

## Phase 21 — Search, performance, observability, reliability hardening

Implement scoped FTS, projections, cache/event invalidation, telemetry dashboards, alerts, load tests, chaos/provider failure tests, partition jobs.

**Gate:** SLO/load targets met or documented capacity limit and mitigation approved.

## Phase 22 — Security hardening and external review

Run static/dynamic scans, access-control review, threat-model update, penetration test, dependency/IaC/container audit, privacy review, key rotation drill.

**Gate:** zero open critical/high findings; medium findings have owners and deadlines accepted by security/product.

## Phase 23 — Staging certification and production launch

- Provider production certification.
- Data retention/legal/accounting approval.
- Backup restore and DR drill.
- Migration rehearsal with production-size data.
- Role-by-role UAT.
- Accessibility manual pass.
- Runbooks/on-call/support training.
- Launch checklist and rollback rehearsal.

## Phase 24 — Post-launch stabilization

For the first 30 days: daily reconciliation review, error-budget review, slow-query analysis, queue/dead-letter review, security anomaly review, customer feedback triage. No advanced optimizer or architecture extraction until stability gates pass.


# 28. IDE Agent Execution Instructions

## 28.1 Mandatory execution loop

For every task, the IDE agent must:

1. Read the authoritative scope section, capability registry, route registry, permission registry, state registry, relevant ADRs, and existing tests.
2. Search the repository for existing models, migrations, commands, routes, policies, UI components, and duplicate logic.
3. Produce the evidence record before editing.
4. Identify root cause or proven missing capability.
5. Propose the smallest correct file set.
6. Add or update tests first where practical.
7. Implement through the owning context and service; do not bypass central tenancy, authorization, state, billing, lock, file, audit, or outbox services.
8. Run targeted tests, then module suite, then required regression suite.
9. Show actual command output summary and failures.
10. Demonstrate API/DB/UI behavior and denial cases.
11. Update OpenAPI, registries, docs, migration map, and traceability when affected.
12. Record residual risk.

## 28.2 Forbidden behavior

- Guessing missing requirements instead of recording a decision request/ADR.
- Editing generated files directly.
- Broad refactors unrelated to the proven gap.
- Using frontend hiding as a security fix.
- Adding `withoutGlobalScopes`, raw unscoped model queries, or platform DB connection in merchant code.
- Generic status update endpoints.
- Catching exceptions and returning success.
- Disabling tests, static analysis, RLS, CSRF, or authorization to make a test pass.
- Logging payloads containing secrets/PII.
- Creating a second source of truth for referral rewards, invoice settlement, role grants, or state.

## 28.3 Bug Fix Protocol

```text
Observed problem:
Evidence:
Requirement violated:
Affected files/routes/tables/components:
Root cause:
Why this is the root cause rather than a symptom:
Correct smallest fix:
Files changed:
Data migration/backfill impact:
Security/tenant/financial impact:
Tests added or updated:
Test commands:
Actual test result:
API/DB/UI proof of resolution:
Authorization and tenant-denial proof:
Remaining risk:
```

## 28.4 Definition of done for a code change

- Requirement and capability ID linked.
- Code reviewed.
- Tests green.
- Static analysis/lint green.
- Migration safe and rehearsed where applicable.
- API contract regenerated.
- Authorization/tenant tests present.
- Audit/outbox behavior verified for sensitive mutations.
- Observability added.
- User-facing states complete.
- Documentation/runbook updated.
- No unresolved high-severity risk.


# 29. Acceptance Criteria

The application is accepted only when all of the following are demonstrated in a release evidence pack.

## 29.1 Architecture and data

- Modular boundaries and architecture tests pass.
- Canonical schema has no duplicate authority or ambiguous delivery status.
- Every tenant table has ownership and RLS policy.
- Money, timestamps, public IDs, versioning, and immutable history follow conventions.
- Migrations pass empty, populated, upgrade, rollback-limit, and production-size rehearsal tests.

## 29.2 Identity and access

- Merchant Magic Link, sender scoped auth, recipient token/OTP, and platform MFA work securely.
- Identity, membership, employment, role, branch, organization, and integration credentials are distinct.
- Every route has an operation ID, permission, scope rule, and policy test.
- Cross-tenant, cross-branch, cross-organization, own-scope, and identity-domain denials are proven.
- Last-owner/final-administrator and maker/checker protections pass.

## 29.3 Delivery operations

- End-to-end request → quote → approval → payment requirement → confirmation → assignment → pickup → transit → proof → completion works.
- Failure, return, redelivery, custody handover, dispute, and refund paths work without overwriting physical truth.
- Every state transition has positive and negative generated tests.
- Personnel PWA works on mobile, intermittent connectivity, and conflict recovery.

## 29.4 Finance and billing

- Customer and platform billing are separate.
- Integrated/offline/COD/split/credit payment workflows reconcile correctly.
- Receipts follow cleared payment.
- Refunds cannot exceed cleared funds and require correct approvals.
- Fixed, percentage, and fixed-plus-percentage platform modes produce correct snapshots and invoices.
- M-Pesa duplicate/retry/overpayment/recovery cases pass.
- Cash-up, compensation, payout, and period locks enforce segregation of duties.

## 29.5 Files, integrations, referral

- Private upload/download, malware scan, signed access, retention, and audit pass.
- Webhooks are signed, scoped, idempotent, retryable, and SSRF-protected.
- Courier referral events are factual, signed, versioned, outbox-driven, replay-safe, and privacy-minimized.
- Central referral outage never blocks valid merchant registration or alters Courier billing truth.
- Courier exposes no central reward ledger, payout method, fraud case, or cross-product earnings.

## 29.6 UX and accessibility

- Role-specific landing/get-started screens exist.
- Desktop/tablet/mobile layouts pass without whole-page horizontal scrolling.
- 200% zoom and keyboard workflows pass.
- Light/dark themes preserve contrast, focus, validation, and affordances.
- Critical flows pass automated and manual WCAG checks.

## 29.7 Operations

- CI/CD, signed artifacts, secrets, backups, restore, health checks, monitoring, alerts, queue/scheduler, and rollback are operational.
- Load targets are met.
- Critical runbooks and on-call ownership exist.
- No open critical/high security defect.
- Release manifest, registries, OpenAPI, migrations, tests, and source checksum agree.


# 30. Risk Register with Mitigation Steps

| Risk | Probability | Impact | Mitigation | Trigger/owner |
|---|---:|---:|---|---|
| Scope breadth exceeds team capacity | 75% | Critical | Freeze launch classification, phase by capability, reject hidden post-MVP work, maintain traceability. | Burn rate/missed gates; Product + Tech Lead. |
| Solo development creates false confidence | 80% for solo path | Critical | Independent QA, security, finance, and DevOps review; no self-certification of all gates. | Repeated unreviewed releases; Founder. |
| Cross-tenant leakage | 15% without controls; <3% target | Critical | Tenant-first repos, RLS, generated isolation suite, separate platform DB role. | Any isolation test/alert; Security. |
| Role/authority contradictions reappear | 30% | High | Versioned permission registry, semantic lint, no duplicate role keys, change-impact review. | Registry mismatch; Tech Lead. |
| State-machine inconsistency | 35% | Critical | Machine-readable registries, command-only transitions, generated tests/checksum. | Unknown/duplicate transition; Domain Lead. |
| M-Pesa/provider instability | 60% | High | Async adapters, reconciliation, idempotency, degraded mode, operator queues. | Failure/latency threshold; Payments Owner. |
| Payment double allocation | 10% before hardening; <1% target | Critical | Unique provider refs, locks, idempotency, append-only allocation. | Duplicate ref alert; Finance Engineering. |
| Maps cost/availability | 45% | Medium/High | Quotas, caching permitted results, usage budgets, manual address fallback, provider abstraction. | Spend/error threshold; Platform Ops. |
| Offline PWA conflict/data loss | 40% | High | Command IDs, aggregate versions, explicit conflict UI, local durability tests. | Sync conflict/error rate; Frontend Lead. |
| File malware/privacy exposure | 25% | Critical | Quarantine, scanning, private storage, short URLs, parser isolation. | Scan failure/access alert; Security. |
| Audit volume/cost | 55% | Medium | Partitioning, tiered retention, safe compression, selective before/after, budget monitoring. | Storage/query threshold; SRE. |
| External search leaks data | 20% | Critical | Delay until justified, mandatory filters, DB hydration/policy, isolation tests. | Search enablement; Tech Lead. |
| Referral system outage/inconsistency | 45% | Medium | Outbox, pending attribution, reconciliation, dead letters, Courier independence. | Event backlog/SLA; Integration Ops. |
| Regulatory/tax/privacy misunderstanding | 35% | Critical | Kenyan legal/accounting/privacy review before launch; configurable retention/tax. | Pre-launch sign-off; Product Owner. |
| Dependency vulnerability | 50% annually | High | Minimal dependencies, pinning, SBOM, automated updates/scans, emergency patch runbook. | Critical advisory; Security/SRE. |
| Backup exists but cannot restore | 20% | Critical | Quarterly restore drill, automated integrity checks, separate credentials. | Failed drill; SRE. |
| Performance degradation from hot tenants | 40% | High | Per-tenant fairness, indexes, projections, partitions, capacity tests, scale thresholds. | P95/SLO burn; SRE. |
| Notification/SMS cost abuse | 35% | Medium | Consent, quotas, preview, permission, rate limits, charge allocation, anomaly alerts. | Cost/volume anomaly; Platform Billing. |
| Maker/checker bypass through multiple roles | 25% | Critical | Actor-based separation, incompatible-role rules, command history, approval invalidation. | Same identity in prohibited stages; Security/Finance. |
| AI agent broad unintended changes | 55% | High | Evidence protocol, smallest-change rule, diff limits, mandatory tests/review, generated-file protection. | Large/unrelated diff; Tech Lead. |

The probabilities are planning estimates, not measured production rates. They must be revised from incident and delivery data.


# 31. Final Verification Checklist

## 31.1 Source and contract integrity

- [ ] Corrected scope version/checksum recorded.
- [ ] Capability, permission, route, state, error, audit, event, entitlement, and release registries validate.
- [ ] OpenAPI and generated client match route registry.
- [ ] No duplicate authoritative model/table/route/state writer.
- [ ] Full optimizer and other post-MVP capabilities are absent from launch surface.

## 31.2 Backend and database

- [ ] Strict static analysis and architecture tests pass.
- [ ] Every tenant table has merchant ownership, indexes, FKs, and RLS.
- [ ] Platform runtime uses separate DB role/repositories.
- [ ] Money uses minor units/currency.
- [ ] Aggregate version and idempotency are enforced.
- [ ] Audit/outbox atomicity proven.
- [ ] No generic delivery state mutation endpoint.
- [ ] Financial records and audits are append-only where required.

## 31.3 Authentication and authorization

- [ ] Magic Link/OTP/token/MFA flows pass replay, expiry, revocation, and rate-limit tests.
- [ ] Session rotation and secure cookies verified.
- [ ] Every route has policy/permission/scope test.
- [ ] Cross-tenant/branch/organization/own-scope denial suite passes.
- [ ] Maker/checker, final-owner, final-admin, and step-up tests pass.
- [ ] Frontend permission checks are not relied on for security.

## 31.4 Delivery and finance

- [ ] End-to-end standard delivery passes.
- [ ] Failure/return/redelivery/dispute/refund paths pass.
- [ ] Every state transition and invalid transition is tested.
- [ ] Assignment eligibility and concurrency pass.
- [ ] Customer payment modes and reconciliation pass.
- [ ] Platform billing modes and M-Pesa recovery pass.
- [ ] Cash-up, compensation, payout, locks/reopen pass.

## 31.5 Files, integrations, and referral

- [ ] Upload quarantine, malware, MIME, size, and signed download tests pass.
- [ ] Webhook signature/replay/SSRF/retry/dead-letter tests pass.
- [ ] Referral event signature, idempotency, ordering, outage, replay, and privacy tests pass.
- [ ] Courier stores no central referral payout/private ledger data.

## 31.6 Frontend and accessibility

- [ ] All required role screens and onboarding exist.
- [ ] Desktop/tablet/mobile layouts pass.
- [ ] No whole-page horizontal scroll at normal content/200% zoom.
- [ ] Light and dark mode pass contrast/focus/error checks.
- [ ] Keyboard, screen-reader spot checks, axe, reduced motion, and touch targets pass.
- [ ] PWA offline and conflict behavior passes.

## 31.7 Operations and release

- [ ] CI/CD gates pass on release SHA.
- [ ] Images/SBOM are signed and vulnerability policy passes.
- [ ] Secrets are managed and rotation tested.
- [ ] Backup and restore drill passes.
- [ ] Health checks, dashboards, alerts, queue/scheduler, and synthetic tests are live.
- [ ] Load/capacity test meets approved targets.
- [ ] Provider production certification complete.
- [ ] Legal/accounting/privacy sign-off complete.
- [ ] Rollback and incident runbooks rehearsed.
- [ ] No open critical/high defects.
- [ ] Product, engineering, QA, security, finance, and operations sign the release evidence pack.

---

# Closing implementation directive

The coding agent must not begin by generating controllers and screens. It must begin with the normative registries, traceability, architecture records, tenant/security foundations, and test harness. Courier by Citrus has financial, identity, location, proof, and multi-tenant consequences. A visually complete application without server-side scope, state, idempotency, audit, reconciliation, and release evidence is a failed implementation, not a partial success.
