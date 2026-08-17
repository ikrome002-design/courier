# Courier by Citrus — Integrated Authoritative Platform Project Scope — Corrected and Regenerated Edition

**Product:** Courier by Citrus  
**Owner / Operator:** Citrus Labs Limited  
**Platform Type:** Multi-tenant, branch-aware, subscription-first logistics and point-to-point goods-delivery SaaS web platform  
**Primary Market:** Courier companies, delivery businesses, motorcycle-delivery operators, van and truck operators, last-mile logistics businesses, retail-delivery teams, e-commerce fulfilment partners, pharmacies, wholesalers, distributors, warehouses, and other goods-transport merchants in Kenya and the broader African market  
**Primary Delivery Model:** Pickup at **Point A** and delivery at **Point B**, with support for one-to-one, one-to-many, many-to-one, multi-stop, return, redelivery, failed-delivery, and branch-transfer workflows  
**Billing Architecture:** Subscription-first, configurable, M-Pesa-integrated, with fixed, percentage, and fixed-plus-percentage platform billing modes available at launch  
**Merchant-Customer Payment Architecture:** Configurable merchant collection methods covering integrated payments, offline payment recording, cash-on-delivery, invoiced credit customers, and split payments  
**Currency:** KES by default, configurable  
**Document Status:** Corrected, regenerated, publication-candidate authoritative scope. This edition integrates the complete contradiction-resolution specification into the owning normative sections and removes historical precedence text.
**Scope Version:** 2.0.0-corrected  
**Effective Date:** 27 June 2026  
**Supersedes:** All earlier Courier by Citrus project-scope drafts, embedded correction registers, contradictory route lists, and conflicting state/schema definitions  
**Normative Annexes:** Machine-readable route registry, state-machine registry, release manifest, permission registry, event schemas, and migration maps generated under this scope  
**Referral Architecture:** Courier by Citrus integrates with the centralized Citrus Labs Refer & Earn platform. Courier owns product facts and product-native referral entry points; the central platform owns referrer identity, attribution, rewards, payout, fraud, support, and cross-product reporting.
**Scope Principle:** No feature in the earlier delivery outline is intentionally discarded. Every delivery-specific capability is retained, assigned to a defined account user, protected by explicit scope, connected to a data model, exposed through policy-controlled APIs, represented in the user interface, and covered by tests and acceptance criteria.

---

# 0. Manifesto Governance, Authority, Resolution Control, and Change Impact

## 0.1 Governing Manifesto

Every requirement, correction, implementation decision, migration, and acceptance decision in this scope is governed by the following five mandatory controls.

1. **Prove the problem.** A defect, omission, contradiction, security weakness, ownership ambiguity, or integration failure must be supported by identifiable evidence in prose, state definitions, schema, routes, permissions, UI, events, release metadata, tests, or production behaviour. Assumptions are not accepted as proof.
2. **Perform root-cause analysis.** The correction must identify the underlying cause, including duplicated sources of truth, conflated identity/state domains, incomplete lifecycle modelling, missing ownership, absent segregation of duties, inconsistent release classification, or missing enforcement.
3. **Fix with precision.** The authoritative fix must resolve the root cause and identify the owner, exclusions, server-side policy, schema effect, API/command effect, UI effect, audit effect, migration effect, error model, edge cases, and affected users.
4. **Test thoroughly.** Every corrected capability requires positive, negative, authorization, tenant-isolation, branch-scope, organization-scope, own-scope, validation, entitlement, financial-lock, concurrency, idempotency, privacy, audit, migration, degraded-mode, and recovery tests where applicable.
5. **Demonstrate resolution.** Revised prose alone is not evidence of resolution. Resolution requires consistent terminology, registries, schemas, generated contracts, policies, routes, screens, jobs, events, migrations, tests, and acceptance evidence. A contradiction is unresolved while the former behaviour remains executable or publishable.

## 0.2 Normative Authority and Source Hierarchy

This regenerated document is the human-readable normative scope. The following machine-readable artifacts are normative when generated from and checksum-linked to this scope:

- identity-domain and role registry;
- permission and separation-of-duty registry;
- state-machine registries;
- canonical route and internal-command registry;
- schema catalog and migration/deprecation map;
- release-class manifest and plan-entitlement manifest;
- product-event JSON Schemas and signature profile;
- error-code registry;
- audit-event registry;
- capability-to-route-to-test traceability manifest.

No historical correction register, ticket, comment, diagram, SDK, code sample, marketing page, or earlier scope may override this document. Historical rationale is retained only in the non-normative resolution-evidence annex. Where generated artifacts disagree with the approved scope version or checksum, publication and deployment fail closed until regenerated and reviewed.

## 0.3 Global Correction Conventions

### One concept, one authority

Every business fact has exactly one authoritative owner. Other representations are explicitly identified as projections, cached read models, calculated rollups, or display labels. Projections may be rebuilt and must never accept direct mutation.

### Identity, role, membership, employment, and credential separation

An identity is the authenticating subject. A role grant gives permissions to an identity. A membership binds an identity to a tenant or organization. Employment is a separate merchant-domain record. A Magic Link, OTP, recipient token, API key, webhook secret, session, branch, invitation, attribution, campaign snapshot, or credential version is not an account.

### State-domain separation

Request, quote, quote approval, payment requirement, invoice settlement, payment transaction, payment allocation, assignment, custody handover, operational delivery, redelivery, dispute, refund, administrative record lifecycle, branch-day operation, cash-up revision, and financial-period lock/reopen state are separate domains. No generic `status` field may combine them.

### Monetary representation

Every persisted or transmitted monetary value uses an integer field ending in `_minor` and an ISO 4217 currency code. Under the project convention, KES 5,000.00 is represented as `500000` minor units. Normative APIs and events do not use an ambiguous field named `amount` for money. Percentage rates use an explicitly scaled representation such as basis points or a fixed-scale decimal string.

### Command-specific mutation

State changes occur only through named commands bound to one registry action. Generic PATCH operations may update only explicitly allowlisted non-state metadata that remains editable at the current lifecycle stage. Clients cannot assign state values directly.

### Concurrency, idempotency, and atomicity

Every mutable aggregate uses an ETag, version, or expected-state precondition. Every financial, identity, credential, state-transition, and integration mutation accepts an idempotency key where retries could duplicate effects. The first valid committed command wins. Stale commands return a conflict and never partially apply. State changes and their outbox/audit records are committed atomically.

### Audit and migration

Any correction replacing a source of truth requires a declared canonical source, field/table/route deprecation map, profiling and reconciliation, deterministic backfill, immutable migration audit, bounded compatibility period, deprecated-use telemetry, removal version, rollback plan, and proof that rollback cannot reactivate contradictory writers.

## 0.4 Canonical Account, Access, Role, and Identity Inventory

Courier exposes **eleven principal human/customer access classifications: ten authenticated human role profiles and one delivery-scoped recipient access mode**.

The ten principal authenticated human role profiles are:

1. Platform Owner (`platform_owner`), displayed as **Super Administrator** in Courier.
2. Merchant Administrator / Merchant Owner.
3. Branch Manager.
4. Merchant Human Resources.
5. Merchant Finance.
6. Dispatch & Customer Operations.
7. Delivery Personnel.
8. Merchant Support.
9. Merchant Audit.
10. Goods Sender.

Goods Recipient Access is the eleventh classification. It is a delivery-scoped secure-link or OTP access mode and is not a persistent account at launch.

Internal Citrus personnel use the `platform_users` identity domain with product-scoped role grants:

1. Platform Owner.
2. Platform Billing and Reconciliation Operator.
3. Platform Support Operator.
4. Platform Security and Risk Operator.
5. Platform Integration Operator.
6. Platform Auditor.
7. Executive Read-Only.

Business-customer humans authenticate as Goods Sender identities and receive organization-scoped role grants:

1. Business Customer Administrator.
2. Business Customer Operator.
3. Business Customer Finance Viewer.
4. Business Customer Integration Administrator.

The centralized Citrus Refer & Earn platform owns Central Referrer identities and its own separated referral-operations roles. Those identities are external to Courier's merchant-user domain.

Courier recognizes two principal non-human identity classes:

1. Business Customer Integration Identity.
2. System Integration Identity.

Merchant Branch, prospective registrant, invitation, tracking token, API key, credential version, referral attribution, and Courier referral snapshot are not accounts.

## 0.5 Canonical Error Contract

All API and internal-command failures use a stable safe envelope:

```json
{
  "error": {
    "code": "machine_readable_code",
    "message": "Safe user-facing message",
    "correlation_id": "cor_...",
    "details": {},
    "retryable": false
  }
}
```

The envelope excludes secrets, fraud-rule internals, stack traces, provider credentials, private identifiers, and cross-tenant existence signals.

The common response semantics are:

- `400 invalid_request` for malformed syntax or missing command structure;
- `401 unauthenticated` for absent, invalid, expired, revoked, or insufficiently stepped-up authentication;
- `403 forbidden` for permission, scope, segregation-of-duty, entitlement, or policy denial;
- `404 not_found` where existence must remain opaque or the scoped resource genuinely does not exist;
- `409 state_conflict`, `invalid_transition`, `duplicate_active_record`, or `idempotency_conflict` for state, uniqueness, concurrency, or replay conflicts;
- `422 validation_failed`, `evidence_required`, or another domain-safe validation code;
- `423 financial_period_locked`, `record_locked`, or `security_hold_active` for controlled locks;
- `429 rate_limited` with a safe retry-after value;
- `502 provider_error` where a dependency responded unsuccessfully;
- `503 dependency_unavailable` where a dependency outage prevents safe local continuity.

## 0.6 Change-Impact Obligation

Every change record identifies affected actor classes, data owners, machine identities, migration owners, operational teams, and customers. Changes affecting one role must also evaluate indirect effects on adjacent makers/checkers, notifications, reporting, support, audit, billing, and integrations. The full forty-three-item change-impact and resolution-evidence register is contained in Section 47 and is non-normative: it proves how the final rules were derived but cannot override them.

## 0.7 Resolution Status

This document is a publication candidate, not a self-proving declaration of correctness. It becomes release-authoritative only when the publication gates in Sections 42 and 48 pass against the same version and checksum. A failed gate reopens the relevant decision and blocks publication or deployment.

# 1. Platform Purpose and Positioning

Courier by Citrus enables delivery merchants to receive, price, schedule, assign, route, track, complete, reconcile, audit, and report goods-delivery jobs from Point A to Point B through one secure, modern, multi-tenant web platform.

The platform supports:

1. Merchant self-registration and guided onboarding.
2. Subscription plan selection, trial, payment, and automated account recovery.
3. Merchant branches, depots, hubs, service zones, and operating calendars.
4. Staff identity, access, employment status, availability, delivery eligibility, and compensation.
5. Sender registration, address books, recipient records, recurring routes, requests, payment, history, receipts, support, and ratings.
6. Recipient secure tracking, availability confirmation, ETA updates, proof confirmation, and issue reporting.
7. Manual and customer-created delivery requests.
8. Point A and Point B address search, pinning, validation, geocoding, route calculation, and ETA.
9. Goods description, category, quantity, weight, dimensions, declared value, flags, photos, documents, and handling instructions.
10. Manual quotes and configurable automatic pricing.
11. Service zones, vehicle classes, service levels, urgency, time windows, waiting fees, failure fees, return fees, taxes, discounts, and manual overrides.
12. Delivery acceptance, dispatch, driver/rider and vehicle assignment, batching, re-routing, and status transitions.
13. Mobile-first delivery personnel workflows.
14. Public but secure delivery tracking.
15. Pickup proof, delivery proof, failed-attempt proof, return proof, signatures, OTPs, photos, timestamps, names, notes, and coordinates.
16. Integrated and offline customer payment methods, customer invoices, receipts, refunds, credit accounts, and reconciliation.
17. Fleet records, capacity, availability, compliance documents, maintenance status, and utilization.
18. Salary, commission, and salary-plus-commission compensation.
19. Branch cash-up, Finance approval, discrepancy control, and period locking.
20. Branch-scoped and platform-level audit.
21. Email, SMS, WhatsApp-ready, in-app, push-ready, and webhook notifications.
22. Maps, payment, storage, messaging, accounting, ERP, API, and webhook integrations.
23. Platform-wide, merchant-wide, branch-level, finance, fleet, dispatch, customer, route, delivery, compensation, and audit reports.
24. Strict tenant isolation, branch scope, personnel own-scope, private storage, field-level masking, and server-side enforcement.

Courier by Citrus is not merely a tracking page, dispatch board, fleet list, invoicing tool, or payment gateway. It is an **operating-control platform for delivery merchants**, combining logistics workflow, financial control, people control, customer visibility, platform billing, and audit.

> **Courier by Citrus is a multi-tenant, subscription-first logistics SaaS platform from Citrus Labs Limited that enables delivery businesses to manage branches, staff, senders, recipients, goods, quotes, dispatch, vehicles, routes, live tracking, proof, invoices, payments, compensation, cash-up, and audit from one secure platform, while integrating natively and independently with the centralized Citrus Labs Refer & Earn platform for product-specific merchant referral attribution and trusted product events.**

---

# 2. Core Product Principles

## 2.1 Multi-Tenant Isolation

Each merchant is an isolated tenant. A merchant's deliveries, customers, recipients, addresses, quotes, vehicles, drivers, routes, invoices, payments, files, reports, compensation, API keys, webhooks, billing records, and audit events must never be accessible, inferable, editable, exportable, or enumerable by another merchant.

Enforcement requires:

- `merchant_id` on every tenant-owned record.
- branch ownership where branch scope applies.
- global scopes or repository constraints.
- authorization policies on every resource.
- opaque UUID/ULID public identifiers.
- no sequential-ID trust.
- tenant-bound cache keys, queue jobs, exports, signed links, search indexes, websockets, and webhook events.
- automated cross-tenant denial tests.

## 2.2 Branch Scope Is Not Permission

A user may be assigned to a branch without receiving every permission in that branch. Every branch endpoint must validate both:

1. the user's branch assignment; and
2. the role/permission required for the requested action.

A Branch Manager may view the dispatch board but cannot assign a driver unless explicitly granted Dispatch permission. A Finance user may view invoices but cannot edit route assignments. An HR user may manage staff but cannot validate payments. A route prefix or branch identifier never substitutes for policy authorization.

## 2.3 Separate Customer and Platform Billing Relationships

Courier by Citrus maintains two independent financial relationships:

### Customer-to-Merchant Delivery Billing

The sender or business customer pays the merchant for delivery services. Merchant-configurable methods may include:

- M-Pesa STK Push.
- M-Pesa PayBill/Till.
- card gateway.
- bank transfer.
- cash.
- cash on delivery.
- voucher.
- credit-account invoice.
- split payment.
- other merchant-defined method.

Integrated transactions are validated through gateway callbacks and reconciled by Finance. Offline methods are recorded by Dispatch/Customer Operations by default and validated by Finance. Customer payment never directly pays Citrus Labs Limited.

### Merchant-to-Citrus Platform Billing

The merchant pays Citrus Labs Limited for subscription, percentage platform fees where active, SMS charges, extra branches, storage/add-ons, and other approved billing lines. These invoices are paid through integrated M-Pesa and automatically reconciled. Payment recovery remains available even during billing suspension.

## 2.4 Subscription-First Platform Billing

The platform billing mode is controlled by authorized Super Administrators and may be:

```text
fixed_amount
percentage_on_merchant_customer_invoice
fixed_amount_plus_percentage_on_merchant_customer_invoice
```

All three are launch-capable. No value is hardcoded. Percentage fee tiers apply only when a percentage component is active:

```text
customer_centric
shared
business_centric
```

## 2.5 Separate Operational and Billing Status

```text
merchants.status          → pending_setup | active | suspended | deactivated
merchants.billing_status  → trialing | active_subscription | read_only_grace | overdue | suspended_billing
```

Paying an overdue platform invoice may clear `suspended_billing` only when the suspension reason is unpaid billing. Payment does not automatically reverse fraud, security, legal, compliance, manual, or deactivation restrictions.

## 2.6 Merchant Self-Registration

Only the public Merchant Administrator registration flow creates a merchant and first Merchant Administrator. Super Administrator cannot create a merchant or complete merchant setup on the merchant's behalf. Post-registration governance includes monitoring, abuse control, billing enforcement, suspension, reactivation, deactivation, and governance notes.

## 2.7 Magic-Link Merchant Authentication

Merchant users authenticate through single-use, expiring email Magic Links. Every login verifies email ownership, tenant membership, user status, role status, suspension state, branch assignment, and token validity. Unused tokens are invalidated when the user is deactivated, suspended, or materially reassigned.

Sender accounts may use Magic Link or verified OTP depending on configured authentication policy. Recipient delivery access is secure-link/OTP-first. Super Administrators use hardened platform authentication with MFA.

## 2.8 Server-Side Enforcement

Every authorization decision is server-side:

- authentication.
- tenant scope.
- branch scope.
- own-scope.
- role and permission.
- operational status.
- billing status and restricted-mode allowlist.
- subscription entitlement.
- delivery-state transition.
- quote-state transition.
- payment-state transition.
- financial-period lock.
- file-access permission.
- field-level masking.
- rate limit.
- idempotency for financial and webhook operations.

Frontend hiding is usability only.

## 2.9 Delivery Personnel Own-Scope

Delivery Personnel see only their assigned jobs, stops, route data, proofs, incidents, customer context required for those jobs, earnings, payout records, and permitted communication workflows. They cannot search merchant-wide customers, inspect another driver's jobs, export contacts, download other personnel statements, or enumerate unassigned delivery references.

## 2.10 Audit-Ready Operation

Sensitive actions produce append-only, tamper-evident audit records with actor, tenant, branch, role, action, entity, old values, new values, reason, status, severity, timestamp, request metadata, and chained hash. Merchant Audit reads are branch-scoped and field-masked. Audit may manage only flagged-event metadata and never mutate the source business record.

## 2.11 Private-by-Default Files

Goods photos, proofs, signatures, documents, invoices, receipts, exports, dispute evidence, staff files, vehicle documents, and business documents are private by default. Access requires server-side authorization and expiring signed URLs or authenticated streaming. Public object URLs are forbidden for private content.

## 2.12 Modern Experience

Every authenticated human account has a role-specific landing page and guided get-started page. Goods Recipient Access receives a delivery-scoped action page rather than account onboarding. Non-human integration identities are managed through authorized human consoles. The interface is responsive, accessible, action-first, status-aware, empty-state-friendly, keyboard-operable, mobile-first where appropriate, and usable at 200% browser zoom without whole-page horizontal scrolling.

---


## 2.13 Centralized Citrus Refer & Earn Integration

Courier by Citrus must not implement a duplicated product-local referrer account, payout profile, reward ledger, fraud engine, or payout engine. It integrates independently with the centralized Citrus Labs Refer & Earn platform.

Courier remains authoritative for:

- Merchant Administrator registration and verification;
- merchant tenant creation;
- subscription plan selection;
- platform subscription invoices and payment facts;
- merchant billing and operational statuses;
- branch and delivery activity;
- product-specific activity qualification facts;
- Courier campaign entry points and native marketing content;
- local referral-code snapshot and central attribution reference;
- signed Courier product events.

The centralized Citrus Refer & Earn platform remains authoritative for:

- referrer identity and authentication;
- universal and Courier-specific referral codes and links;
- referral attribution;
- campaign versions and reward rules;
- reward calculations and ledgers;
- qualification periods, holds, reversals, and adjustments;
- referrer payment methods and compliance data;
- payout runs, attempts, reconciliation, and statements;
- referral fraud, duplicate detection, support, email, audit, and cross-product reporting.

Courier must not directly pay a referrer or store a separate full copy of referrer payout details. The central platform must not directly query Courier operational tables to infer delivery completion. Courier emits trusted factual events, and the central platform evaluates campaign and reward consequences.

# 3. Per-User Landing, Get-Started, Token-Access, and Integration-Console Experience

## 3.1 Human Role Landing Pages

The first authenticated human screen is a live role home rather than a generic dashboard.

| Human user | Landing priorities |
|---|---|
| Platform Owner / Super Administrator (UI label) | merchant registrations, billing health, referral integration health, product-event failures, M-Pesa exceptions, suspicious activity, system health, critical audit events. |
| Platform Billing and Reconciliation Operator | merchant-to-Citrus reconciliation, centralized referral reward/payout reconciliation handoffs, exceptions, reversals, and settlement queues. |
| Platform Support Operator | Citrus support cases, masked merchant context, referral-support handoffs, integration incidents, and escalations. |
| Platform Security and Risk Operator | suspicious registrations, referral fraud signals, self-referral, duplicate identities, credential compromise, API abuse, and merchant risk actions. |
| Platform Integration Operator | product event delivery, signatures, schema versions, dead letters, webhooks, queues, and integration credentials. |
| Platform Auditor | privileged actions, billing, referral, integration, break-glass, and governance audit. |
| Executive Read-Only | aggregate merchant, subscription, delivery, referral acquisition, liability, payout, risk, and platform-health indicators. |
| Merchant Administrator | subscription, branch performance, staff, revenue, payout liabilities, billing recovery, and limited referral attribution status for the merchant’s own registration. |
| Branch Manager | branch state, service capacity, fleet, staff availability, deliveries, failures, day close, and cash-up submission. |
| Merchant Human Resources | invitations, identity/membership/employment status, eligibility, availability, documents, compensation plans, and payout preparation. |
| Merchant Finance | payment validation, gateway exceptions, invoices, COD, refunds, disputes, cash-up, compensation liabilities, payout runs, period locks, and platform billing. |
| Dispatch & Customer Operations | requests, addresses, quotes, assignments, routes, exceptions, customer communication, invoices, and payment recording. |
| Delivery Personnel | next assignment, route, proof, incidents, own earnings, and payout status. |
| Merchant Support | support tickets, SLA, delivery exceptions, customer complaints, and handoffs. |
| Merchant Audit | branch-scoped high-risk events, access violations, financial changes, assignment/proof changes, files, and flagged-event metadata. |
| Goods Sender | create delivery, active deliveries, quotes, payment, tracking, history, documents, receipts, support, and business integration console where entitled. |

## 3.2 Guided Get-Started Pages

Every authenticated human account receives a progress-tracked, deep-linked, resumable, accessible, and dismissible checklist appropriate to every active role grant. Completion is stored per module and role grant rather than as one ambiguous user flag.

### Common Internal Citrus Onboarding

Every internal platform user completes acceptable-use acknowledgement, mandatory MFA, recovery setup, device/session review, data-classification training, privacy and audit notice, incident reporting, product-bound authority explanation, and separation-of-duty acknowledgement.

Role-specific internal modules are mandatory:

- **Platform Owner:** platform configuration, internal-role governance, last-owner protection, maker/checker, break-glass, billing-policy changes, release gates, and post-use review.
- **Platform Billing and Reconciliation Operator:** platform invoices, payment allocation, exception handling, reversal controls, evidence, settlement handoffs, and prohibition on merchant-customer validation.
- **Platform Support Operator:** masking, safe troubleshooting, escalation, no-impersonation launch boundary, referral handoff, and prohibited mutation.
- **Platform Security and Risk Operator:** evidence handling, proportional restrictions, maker/checker, appeal/review, credential-compromise response, and audit preservation.
- **Platform Integration Operator:** product registration, key custody, event schemas, detached signatures, retries, dead letters, secret rotation, provider incidents, and replay safety.
- **Platform Auditor:** append-only records, export governance, evidence integrity, independence, and prohibited mutation.
- **Executive Read-Only:** aggregate metrics, privacy minimization, export restrictions, interpretation caveats, and prohibition on operational commands.

A new platform role grant remains inactive until required modules are completed, except a separately authorized break-glass session. A material policy change can require re-acknowledgement. Revoking a role hides its modules from active work but preserves completion and audit history.

### Merchant and Customer Onboarding

- **Merchant Administrator:** email verification, plan selection, merchant profile, first branch, service areas, initial Branch Manager and HR appointments, billing phone, ownership safeguards, and setup completion. Referral attribution is shown only when supplied and cannot be changed after central confirmation through ordinary Courier UI.
- **Branch Manager:** branch profile, hours, exception calendar, services, zones, vehicle classes, pricing, branch-day responsibilities, cash-up submission, and closure protection.
- **Merchant Human Resources:** staff invitations, role/branch grants, personnel eligibility, shifts, documents, compensation, lifecycle controls, final-role-holder safeguards, and segregation of duties.
- **Merchant Finance:** payment validation, allocations, reconciliation, cash-up revision review, refund/dispute controls, compensation review, payout settlement, financial locks, controlled reopen execution, and platform billing.
- **Dispatch & Customer Operations:** sender registration, request creation, address validation, quote, approval handoff, assignment, basic routing, payment recording, proof monitoring, and exception management.
- **Delivery Personnel:** profile, availability, compensation acknowledgement, assignment acceptance, pickup proof, delivery proof, custody handover, offline conflict recovery, incident reporting, security, and own earnings.
- **Merchant Support:** ticket workflow, masking, escalation, communication, dispute/refund handoff, and central referral-support boundary.
- **Merchant Audit:** branch scope, masking, flag lifecycle, export governance, source-record immutability, and test filters.
- **Goods Sender:** merchant-scoped verification, addresses, recipients, first request, payment preference, tracking, support, cancellation rules, and privacy controls.

### Business Customer Organization Onboarding

Business-customer humans authenticate as Goods Sender identities and complete modules for each organization role:

- **Business Customer Administrator:** organization profile, member invitations, final-administrator protection, address/recipient governance, policy defaults, and delegation limits.
- **Business Customer Operator:** organization request, quote, recurring-route, tracking, support, and permitted cancellation workflows.
- **Business Customer Finance Viewer:** invoice, allocation, receipt, credit statement, export masking, and read-only financial boundaries.
- **Business Customer Integration Administrator:** API clients, one-time secret issuance, scopes, webhook verification, rotation, revocation, usage, dead letters, and incident response.

### Onboarding Error Handling and Edge Cases

A privileged action attempted before required completion returns `403 onboarding_incomplete` with safe module identifiers. A removed or superseded module must not permanently block access; migration maps it to a replacement or an approved exemption. A user holding multiple roles completes the union of required modules without duplicate completion. Incompatible roles remain blocked even when onboarding is complete. Emergency access records any skipped module and creates mandatory post-event review. All modules require keyboard, screen-reader, low-bandwidth, and alternate accessible formats.

## 3.3 Goods Recipient Action Page

Goods Recipient Access is not treated as a persistent account at launch. A valid secure tracking token or OTP opens a delivery-scoped action page showing only permitted delivery information, ETA, availability, delivery OTP/proof actions, permitted driver/vehicle context, issue reporting, link expiry, and privacy information.

Expired, revoked, consumed, rate-limited, or invalid recipient credentials must show a safe recovery route without exposing whether unrelated deliveries or recipient records exist.

## 3.4 Business Customer Integration Console

A human business customer administrator with entitlement manages non-human Business Customer Integration Identities through a secure console containing:

- business organization status;
- API clients;
- one-time secret issuance;
- scopes and branch grants;
- credential versions, expiry, rotation, and revocation;
- webhook endpoints and verification;
- delivery/idempotency test tools;
- usage, quota, and rate limits;
- failed webhook deliveries;
- security alerts;
- audit history;
- sandbox and production state;
- documentation and support.

The API client itself has no landing page and cannot authenticate interactively.

## 3.5 Cross-Role UX Rules

- consistent navigation within each human surface;
- role-aware search and command palette;
- KES formatting by default;
- status text plus icon, never colour alone;
- clear offline, pending, queued, synchronized, failed, held, reconciled, and validated states;
- billing and entitlement banners visible only to permitted users;
- referral attribution status visible only to permitted merchant and internal users, with referrer payout information excluded from Courier;
- full keyboard and screen-reader labelling;
- no whole-page horizontal scrolling at supported breakpoints or 200% zoom;
- machine identities never receive human navigation;
- frontend visibility never substitutes for server authorization.

---

# 4. Platform Users and Account Types

Courier exposes eleven principal human/customer access classifications: ten authenticated human role profiles and one delivery-scoped recipient access mode. The ten role profiles are Platform Owner (displayed as Super Administrator), Merchant Administrator, Branch Manager, Merchant Human Resources, Merchant Finance, Dispatch & Customer Operations, Delivery Personnel, Merchant Support, Merchant Audit, and Goods Sender. Goods Recipient Access is a token/OTP access mode, not a persistent account.

Internal Citrus personnel use `platform_users` with product-scoped role grants. Business-customer humans authenticate as Goods Sender identities with organization-scoped grants. Business Customer Integration and System Integration are non-human identity classes. Merchant Branch, invitations, credentials, sessions, tokens, and referral records are not accounts.

## 4.1 Platform Owner Account — Displayed as Super Administrator

### Purpose

The Platform Owner (`platform_owner`), displayed as Super Administrator in Courier, is the single highest Citrus Labs Limited Courier platform role governing the SaaS ecosystem across all merchants. This role does not belong to a merchant and cannot be inserted into merchant staff, branch assignment, driver, dispatcher, or customer tables.

### Core Functionalities

1. Configure platform-wide settings, currencies, time zones, country rules, and feature flags.
2. Configure active billing mode: fixed, percentage, or fixed-plus-percentage.
3. Configure global billing behaviour, default billing period, enabled periods, free-period length, grace length, overdue reminders, and suspension window.
4. Create, version, schedule, activate, cancel, and audit plan prices.
5. Configure Starter, Growth, Pro Branch, and Multi-Branch entitlements, limits, storage, API access, route-optimization access, staff limits, vehicle limits, driver limits, branch limits, and extra-branch charges.
6. Configure percentage fee rates, fee bases, tier allocation, and exemptions. Fixed recurring charges are configured only through versioned plan prices and approved add-on prices; no independent per-transaction fixed fee exists at launch.
7. Configure promotional discounts, targets, durations, eligibility, usage limits, and free-period offers.
8. Configure launch-capable Preferred Delivery Personnel Fee Rules.
9. Configure platform SMS rates and charge aggregation.
10. Configure and monitor merchant subscription M-Pesa integration.
11. Configure payment-gateway connectors made available to merchants, without accessing merchant secret material beyond controlled integration administration.
12. Configure Maps integration settings, quotas, allowed countries/regions, and platform monitoring.
13. View all self-registered merchants, branches, plans, billing statuses, operational statuses, subscription invoices, platform-fee liabilities, M-Pesa attempts, and reconciliation records.
14. View platform-wide delivery, revenue, subscription, API, notification, system-health, failure, route, and suspicious-activity analytics.
15. Monitor registration abuse, duplicate-business signals, suspicious trial patterns, prohibited goods patterns, transaction anomalies, and API abuse.
16. Suspend, reactivate, or deactivate an existing merchant through audited governance rules.
17. Add governance notes and evidence attachments to merchant records.
18. View and resolve platform billing reconciliation exceptions.
19. Manage global prohibited/restricted goods categories and policy templates.
20. Manage global notification templates while preserving merchant branding placeholders.
21. Manage system integrations, API settings, webhook infrastructure, queue health, scheduler health, failed jobs, and error telemetry.
22. Manage internal Citrus Labs Limited platform roles and permissions.
23. View platform-level audit logs and critical merchant-governance events.
24. Review escalated disputes without mutating merchant financial records outside a controlled governance workflow.
25. Export platform reports through permission-gated, reason-required, signed downloads.

### Explicit Exclusions

The Super Administrator must not:

- create merchant tenants;
- create the first Merchant Administrator;
- approve ordinary registration as a prerequisite to initial merchant dashboard access;
- complete merchant setup;
- create a merchant branch on behalf of the merchant;
- create sender delivery requests in ordinary operations;
- configure a merchant branch's services, zones, operating hours, vehicles, drivers, prices, or routes;
- assign or reassign merchant deliveries;
- create merchant-customer invoices;
- record or validate merchant-customer payments;
- generate merchant receipts manually;
- act as merchant HR or Finance;
- impersonate merchant users at launch;
- normally record merchant platform-billing payments manually;
- insert itself into merchant tenant records;
- bypass tenant isolation through ordinary UI/API paths.

### Authentication

Hardened platform authentication with mandatory MFA, device/session controls, short idle timeout, reauthentication for high-risk changes, and complete audit logging.

### Landing and Get-Started Impact

The Platform Owner / Super Administrator landing page includes billing configuration status, plan-price schedule, M-Pesa health, platform deliveries, failed background jobs, suspicious registrations, webhook failures, Maps spend/usage, and critical audit events. The get-started page prevents launch readiness from being marked complete until required billing, M-Pesa, plan, entitlement, maps, notification, and security settings have been reviewed.


## 4.1A Internal Citrus Labs Limited Platform Roles

Internal Citrus work is performed by separately governed `platform_users`; no shared unrestricted Super Administrator account is permitted. `Super Administrator` is only the Courier UI label for the canonical `platform_owner` role and is not a second role key.

### Platform Owner (`platform_owner`)

Owns Courier platform-wide configuration, internal-user governance, billing-mode and plan governance, product registration, high-risk platform approval, release gates, and break-glass approval. A Platform Owner has no implicit merchant role and no implicit central Refer & Earn role.

No user may grant themselves `platform_owner`, approve their own owner grant, approve their own break-glass request, reverse their own deactivation, or remove the final active Platform Owner. High-risk grants require a separate authorized approver and step-up authentication.

### Platform Billing and Reconciliation Operator

Reconciles merchant-to-Citrus platform payments and handles central-referral settlement handoffs. It cannot validate merchant-customer payments, mutate merchant operations, calculate central rewards, or execute central payouts without an independently granted central role.

### Platform Support Operator

Handles Citrus support and referral-support handoffs using masked context. It cannot impersonate merchant users at launch, mutate merchant business records, expose unmasked private data, or use support access as an operational role.

### Platform Security and Risk Operator

Investigates suspicious registration, self-referral, credential compromise, duplicate identities, event abuse, and platform risk. Designated restrictions and releases enforce maker/checker so one operator cannot initiate and clear the same high-risk restriction.

### Platform Integration Operator

Manages product registrations, key references, event schemas, callback delivery, queues, dead letters, webhook infrastructure, and provider configuration. Plaintext secrets are displayed once where permitted and are never retrievable later.

### Platform Auditor

Reads append-only platform, billing, privileged-action, break-glass, and referral-integration audit records. It cannot mutate source records, approve its own access, or execute operational commands.

### Executive Read-Only

Views aggregate operational, billing, merchant, acquisition, liability, payout, risk, and system-health indicators with data minimization. It cannot open unnecessary customer/staff detail or mutate any source record.

### Canonical Internal Identity Model

```text
user_identities
platform_users
platform_roles
platform_permissions
platform_role_permissions
platform_role_grants
platform_user_mfa_methods
platform_user_sessions
platform_user_status_history
platform_user_security_events
platform_break_glass_requests
platform_break_glass_sessions
platform_privileged_action_approvals
```

`platform_users` must not contain `merchant_id`, `branch_id`, merchant employment, or merchant memberships. Platform sessions are rejected on merchant routes unless a future separately approved support-access capability exists; merchant impersonation is outside launch scope.

### Authentication, Revocation, and Error Handling

All internal roles require MFA, short sessions, device/session controls, reauthentication for high-risk actions, effective-dated grants, immediate suspension, and complete privileged-action audit. Duplicate grants return `409 duplicate_active_role_grant`. Removing the final owner returns `422 last_platform_owner_required`. Self-approval returns `403 separation_of_duty_violation`. An expired break-glass session returns `401 break_glass_session_expired` and revokes derived access. A merchant membership payload supplied to an internal user command returns `422 invalid_identity_domain`.

## 4.2 Merchant Administrator / Merchant Owner Account

### Purpose

The Merchant Administrator is the registering business owner, manager, or authorized operator. The account is the merchant's top-level ownership and oversight role, not an operational superuser.

### Self-Registration Rule

```text
Merchant Administrator submits public registration.
System validates registration and abuse controls.
System creates merchant tenant.
System assigns registering user as Merchant Owner / Merchant Administrator.
System sends Magic Link verification.
Merchant selects a plan and completes first-time setup.
Ordinary access is not held for manual Super Administrator approval.
Super Administrator governance begins after creation.
```

### First-Time Setup

1. Verify email by Magic Link.
2. Select Starter, Growth, Pro Branch, or Multi-Branch.
3. Select billing period.
4. Confirm business identity, registration details where voluntarily provided, business category, contacts, logo, billing email, and M-Pesa billing phone.
5. Create at least one Merchant Branch.
6. Complete first branch address, operating area, contact, time zone, and calendar.
7. Invite initial Branch Manager and HR users where needed.
8. Review entitlement limits.
9. Confirm terms, privacy, prohibited-goods policy, and platform billing disclosure.
10. Complete setup and enter the Merchant Administrator landing page.

After completion:

```text
merchants.status = active
merchants.setup_completed_at = now()
merchants.billing_status remains trialing until paid activation or configured trial transition
```

### Core Functionalities

1. Self-register the merchant.
2. Maintain merchant profile, logo, legal/business display details, contacts, business categories, default currency, and default settings.
3. Select and schedule plan or billing-period changes.
4. View active billing mode, current plan, period, captured price, discounts, SMS/add-ons, extra branches, percentage-fee exposure, next invoice, overdue amounts, trial/grace status, and scheduled price changes.
5. Pay platform billing invoices through M-Pesa.
6. View and download platform invoices, payment attempts, and reconciliation status.
7. Create, view, suspend, reactivate, and deactivate Merchant Branches subject to closure protection and entitlement limits.
8. Add only Branch Manager and HR email addresses through the Merchant Administrator setup/control flow.
9. View all merchant staff, roles, branches, employment statuses, access statuses, and invitation states.
10. View merchant user identity, membership, invitation, employment, role, and access states; manage Branch Manager and HR appointments; approve defined high-risk role changes; and perform audited emergency access suspension without changing employment or compensation.
11. View merchant-wide delivery volume, revenue, outstanding invoices, payment-method totals, COD exposure, refund exposure, route performance, failed-delivery reasons, customer activity, fleet utilization, and SLA performance.
12. View branch comparisons on Multi-Branch.
13. View branch service catalogues, delivery zones, pricing, fleet, driver eligibility, and operating calendars in read-only mode unless separately permissioned.
14. View staff and personnel performance by branch and individual.
15. View compensation according to each personnel member's configured model: salary-only, commission-only, or salary-plus-commission.
16. View salary liabilities, commission liabilities, payout status, payout history, compensation approvals, high-value exceptions, and earnings-query trends.
17. Approve or reject high-value payout runs when the configured maker/checker threshold requires Merchant Administrator approval.
18. Approve exceptional financial-period reopen requests where configured; Finance executes the reopen.
19. Receive daily branch day-close and cash-up reports by email/PDF or secure link.
20. View audit summaries and high-risk events according to plan entitlement.
21. Manage merchant-level API/customer integration access where entitled, without exposing raw secrets in the frontend.
22. Perform emergency access suspension for suspected compromise, fraud, abuse, data leakage, or immediate operational danger; routine employment suspension and termination remain HR-owned for operational staff.
23. Contact platform support and manage merchant-level support cases.

### Explicit Exclusions

The Merchant Administrator must not by default:

- create operational staff other than Branch Manager and HR appointments through the defined owner flow;
- directly create Delivery Personnel, Dispatch, Finance, Support, or Audit users—HR owns those;
- assign personnel to branches, vehicles, delivery service types, or routes—HR and Dispatch own those domains;
- configure branch delivery service catalogue, service zones, branch prices, or branch calendar—Branch Manager owns those;
- assign or transfer deliveries—Dispatch owns that;
- create merchant-customer invoices—Dispatch/Customer Operations owns that;
- record ordinary customer payments—Dispatch is default maker;
- validate customer payments—Finance owns checker control;
- approve routine cash-up—Finance owns that;
- execute routine period locks—Finance owns that;
- directly edit compensation—HR owns setup;
- mark payout runs paid—Finance owns settlement;
- operate as an all-purpose branch user merely because the user owns the merchant;
- routinely activate, suspend employment, terminate, or reactivate operational staff outside HR workflows;
- use emergency suspension to alter employment, compensation, completed work, payout liability, or historical ownership.

### Dormancy and Deactivation

Dormancy is assessed using subscription status plus login and operational inactivity over configurable retention windows. Non-payment follows trial/grace/suspension, not destructive deletion. Historical deliveries, proofs, files, invoices, receipts, payments, customer records, audit events, compensation, and platform billing records are retained according to policy.

### Authentication

Email Magic Link with tenant, user, role, status, and token validation.

## 4.3 Branch Manager Account

### Purpose

A Merchant Branch is a tenant-owned entity and authorization scope. It cannot authenticate, receive a Magic Link, hold a session, possess MFA, or own a human role. A Merchant Branch represents a physical or operational delivery location such as a depot, dispatch office, hub, warehouse-linked operation, neighbourhood delivery centre, or city-zone business unit. The Branch Manager manages only assigned branches and does not create merchant tenants.

### Branch Catalogue Ownership

The Branch Manager owns the branch delivery catalogue and operational framework:

| Action | Owner |
|---|---|
| Create delivery service type, e.g. same-day motorcycle delivery | Branch Manager |
| Set service price or pricing rule within merchant policy | Branch Manager |
| Set vehicle class allowed for the service | Branch Manager |
| Set expected service duration/SLA | Branch Manager |
| Configure branch service zones | Branch Manager |
| Configure branch operating hours and exception calendar | Branch Manager |
| Enable/disable a delivery service at branch level | Branch Manager |
| Assign a driver/rider as eligible for a vehicle/service | HR |
| Assign a live delivery to a driver/vehicle | Dispatch |
| Validate customer payment | Finance |

### Core Functionalities

1. View branch landing and get-started pages.
2. Manage assigned branch profile, code, contacts, location, business category, and service area.
3. Manage weekly hours, breaks, public-holiday exceptions, special closures, emergency closure, and closure reason.
4. Create, edit, price, schedule, activate, deactivate, and archive delivery service types.
5. Configure branch delivery zones, zone boundaries, pickup areas, destination areas, serviceability, and restricted areas.
6. Configure base fees, distance bands, weight bands, vehicle-class pricing, urgency/time-window surcharges, minimum fees, waiting-time fees, failed-attempt fees, return fees, taxes, and approved branch discounts within policy.
7. Configure allowed goods categories and branch-specific restricted categories within platform policy.
8. Add and manage branch fleet records where plan and permission allow, including vehicle types, capacity, plate number, availability, compliance documents, service area, and maintenance state.
9. View HR-controlled personnel assignment, vehicle/service eligibility, availability, shifts, temporary unavailability, and queue-ready state in real time, but not edit them.
10. View branch delivery inbox, dispatch board, route map, pickups, in-transit jobs, delivery exceptions, failed attempts, returns, and customer issues in read-only operational mode unless separately permissioned.
11. View branch invoices, payments, receipts, refunds, COD position, cash-up, revenue, and financial reports according to permission.
12. View branch-level audit logs according to plan entitlement.
13. Open, pause, close, and reopen the branch day with reason.
14. Submit branch cash-up and reconciliation records.
15. Pay the merchant's platform billing invoice from branch context through M-Pesa.
16. View branch performance: delivery count, success rate, SLA, average pickup time, average delivery time, failure reasons, route efficiency, driver utilization, vehicle utilization, revenue, outstanding balances, and customer activity.

### Explicit Exclusions

The Branch Manager must not by default:

- create other branches;
- manage users in other branches;
- invite, activate, suspend, or deactivate operational staff;
- assign staff roles or branch access;
- edit HR-owned personnel eligibility, availability, compensation, or employment status;
- assign, reassign, batch-assign, transfer, or route live delivery jobs;
- select preferred delivery personnel for a job;
- create merchant-customer invoices;
- validate, reject, or correct payments;
- approve or reject cash-up;
- create refunds or manage finance disputes;
- reverse or reissue receipts;
- mark payout runs paid;
- mutate locked financial periods.

### Branch Status Rules

| Status | Effect |
|---|---|
| Active | Accept requests, quotes, assignments, pickups, deliveries, invoices, and payments. |
| Paused | Temporarily blocks new branch operations while allowing controlled resolution of active jobs. |
| Suspended | Blocks new requests and assignments; preserves historical and recovery access. |
| Closed/Archived | No longer operational; requires closure-protection checks. |

### Branch Closure Protection

A branch cannot close/archive while any of the following exist:

- unassigned confirmed jobs;
- accepted active assignments;
- pickups in progress;
- in-transit deliveries;
- pending delivery proofs;
- unresolved failed-delivery/return jobs;
- unpaid or partially paid customer invoices requiring action;
- pending payment validations;
- unissued receipts for cleared payments;
- open disputes/refunds;
- unclosed branch day;
- unresolved cash-up discrepancy;
- unpaid personnel payout run tied to the branch where policy blocks closure;
- active vehicles or staff assignments requiring reassignment.

### Minimum Branch Profile

Branch name, unique merchant-level branch code, physical address, town/city/area, GPS coordinates, phone, optional email, service-area definition, time zone, business category, status, operating calendar, and branch manager assignment.

### Branch Day Workflow

```text
Open branch day
Confirm branch services and zones
Confirm active vehicles
Confirm available/eligible personnel
Confirm payment and COD float controls
Open request/dispatch operations
Run delivery intake, quotes, assignments, pickups, transit, deliveries, invoices, and payment recording
Review active and exception jobs
Review pending validations, receipts, COD, refunds, and disputes
Close operational intake
Submit day-close record
Submit cash-up and reconciliation
Finance approves/rejects
Lock according to plan/governance
```

### Authentication

Magic Link after active invitation and branch assignment.

## 4.4 Merchant Human Resources Account

### Purpose

HR manages staff identity, employment status, branch-scoped role assignment, invitation, driver/rider eligibility, vehicle/service eligibility, availability, shifts, personnel documents, access lifecycle, compensation setup, and payout-run preparation. HR acts only within assigned branch scope unless a Multi-Branch entitlement and explicit multi-branch HR permission exists.


### Authoritative Lifecycle Ownership

HR owns routine lifecycle for Delivery Personnel, Dispatch, Finance, Support, Audit, and other approved operational roles. HR creates staff profiles, issues/revokes invitations, activates verified memberships, assigns branch roles within scope, manages employment status, leave, ordinary suspension, termination, and reactivation.

Merchant Administrator manages initial and replacement Branch Manager/HR appointments, approves configured high-risk or cross-branch access, and may issue an emergency access suspension. Emergency suspension immediately blocks sessions and unused Magic Links but does not terminate employment, change compensation, remove historical branch ownership, or settle active assignments. HR and relevant governance users receive an immediate review task.

Identity, membership, employment, invitation, role-grant, and emergency-security statuses remain separate. No single generic `user_status` may substitute for all six domains.

### Staff Creation Rule

HR:

1. adds staff email and phone;
2. selects account type: Delivery Personnel, Dispatch & Customer Operations, Finance, Support, Audit, or other approved branch role;
3. selects role subtype, e.g. motorcycle rider, van driver, truck driver, dispatcher, customer-support agent, finance officer;
4. assigns the branch;
5. assigns eligible service types, vehicle classes, weight classes, restricted-goods authorization, and route/zone qualifications where relevant;
6. configures employment and compensation;
7. sends invitation;
8. monitors pending activation;
9. resends or revokes invitation;
10. records all changes in audit.

### Core Functionalities

1. HR dashboard with staff counts, pending invitations, active/suspended/deactivated staff, unavailable personnel, expiring licences/documents, missing compensation, and open earnings queries.
2. Create and edit staff profiles.
3. Invite, resend, revoke, and monitor staff activation.
4. Assign predefined roles and show a permission preview before save.
5. Enforce self-escalation prevention.
6. Assign staff to the HR user's permitted branch.
7. Maintain role, branch, status, and employment history.
8. Manage driver/rider licence class, vehicle-class eligibility, delivery-service eligibility, zone eligibility, restricted-goods qualifications, and capacity/handling qualifications.
9. Manage shifts, working days, hours, breaks, leave, unavailable dates, emergency unavailability, on-duty/off-duty state, and availability calendar.
10. Manage required staff documents, expiry dates, compliance status, and reminders.
11. Configure compensation models: commission-only, salary-plus-commission, salary-only.
12. Configure commission basis, rate, fixed commission, distance/weight/vehicle/service modifiers, preferred-personnel fee inclusion, failure/return treatment, effective dates, and caps where permitted.
13. Configure salary amount, pay period, effective dates, and salary-plus-commission terms.
14. Maintain compensation change history and collect personnel acknowledgement.
15. Prepare and submit payout runs.
16. Search/filter staff and export staff roster only, subject to permission and masking.
17. Deactivate staff with reason, immediate access revocation, token invalidation, active-session invalidation, and live-job reassignment checks.
18. View HR audit activity for staff/access/eligibility/compensation changes.

### Explicit Exclusions

HR must not:

- manage staff in an unauthorized branch;
- manage Merchant Administrator activation;
- create merchant tenants or branches;
- self-assign a higher-risk role;
- assign live delivery jobs;
- modify route sequences;
- create customer invoices;
- record or validate customer payments;
- approve cash-up;
- mark payout runs paid;
- access merchant-wide customer lists or export customer/payment data;
- pay platform subscription invoices by default;
- edit compensation outside branch scope;
- bypass entitlement limits.

### Mandatory Staff Fields

First name, last name, display name, email, phone, profile photo where policy requires, role, subtype, employment type, employment status, primary branch, start date, invitation actor, licence/qualification fields where relevant, emergency contact where policy allows, and compensation status.

### Duplicate Prevention

The platform uses globally verifiable identity attributes with tenant-specific merchant memberships. Within one merchant, the same verified email cannot create duplicate active memberships; a deactivated membership is reactivated through controlled workflow. The same person may hold isolated memberships under different merchants without cross-tenant disclosure. Phone duplication is evaluated under merchant-aware policy so shared business numbers do not create unsafe global blocking.

### Authentication

Magic Link after active invitation and branch assignment.

## 4.5 Merchant Finance Account

### Purpose

Finance controls customer payment validation and reconciliation, gateway exceptions, invoices, receipts, COD, outstanding balances, taxes, refunds, disputes, cash-up approval, financial reporting, platform billing visibility/payment, compensation liability, payout-run verification, and financial-period locks within assigned branch scope.

### Maker/Checker Rule

| Action | Default owner |
|---|---|
| Create delivery invoice | Dispatch & Customer Operations |
| Record offline customer payment | Dispatch & Customer Operations |
| Initiate integrated payment request | Sender or Dispatch according to workflow |
| Validate/reject offline payment | Finance |
| Reconcile integrated gateway payment | Automated service with Finance exception review |
| Correct payment reference | Finance |
| Override duplicate reference | Finance with permission and reason |
| Approve/reject cash-up | Finance |
| Prepare payout run | HR |
| Verify/standard-approve payout | Finance |
| High-value payout approval | Merchant Administrator where threshold requires |
| Mark payout paid | Finance |

Finance may record a customer payment only through an explicit back-office exception permission. The same user must not both create and validate the same offline payment where maker/checker policy is active.

### Core Functionalities

1. View branch-scoped delivery invoices and line items.
2. Validate, partially validate, reject, dispute, request correction, void, or reconcile payment records according to state rules.
3. Review integrated M-Pesa/card callbacks and reconciliation exceptions.
4. Correct references and override duplicate references with permission, reason, and audit.
5. Track outstanding, partially paid, paid, overdue, voided, disputed, and refunded invoices.
6. Manage cash, COD, M-Pesa, bank, card-terminal, voucher, credit-account, split-payment, and custom-method reconciliation.
7. Approve/reject branch cash-up; request correction; record discrepancy resolution.
8. Lock days/months and perform controlled reopen through `PeriodLockService`.
9. Create and approve external refund records according to permission and approval thresholds.
10. Review and resolve payment and delivery-charge disputes.
11. Verify, standard-approve, reject, and mark personnel payout runs paid.
12. Create controlled compensation adjustments.
13. Respond to personnel earnings queries.
14. View salary and commission liabilities.
15. View/pay merchant platform billing invoices via M-Pesa.
16. View detailed platform-payment attempts and reconciliation.
17. Generate permission-controlled finance reports and exports.
18. Manage Finance task inbox and notifications.

### Granular Permissions

Permissions are separate for invoice view, payment validation, reference edit, duplicate override, reconciliation, receipt reissue, dispute review, refund create/approve, cash-up review, period lock, controlled reopen, payout verify, payout approve, payout mark-paid, compensation view, platform billing view/pay, finance export, and audit view.

### Customer Payment Statuses

```text
initiated
recorded
pending_validation
partially_validated
validated
rejected
correction_requested
disputed
voided
external_refund_recorded_in_separate_adjustment
reconciliation_required
failed
cancelled
```

Required payment fields include merchant, branch, invoice, payment method, amount, reference, gateway identifiers where applicable, recorded/initiated actor, validated actor, validation status, notes, rejection reason, timestamps, IP/user agent where relevant, and idempotency key.

### Method Rules

| Method | Minimum validation rule |
|---|---|
| Cash | Amount, collector, branch day, cash-up reference. |
| COD | Amount, collecting personnel, delivery reference, branch day, handover/cash-up state. |
| M-Pesa | Unique transaction/receipt code, callback or evidence, amount, phone/reference. |
| Bank transfer | Bank reference/deposit evidence. |
| Card terminal | Terminal reference and batch where available. |
| Voucher | Voucher code, issuer, approval state, value. |
| Credit account | Approved customer credit terms, invoice due date, ledger entry. |
| Split payment | Each leg has independent method, amount, reference, and validation state. |
| Other | Merchant-defined mandatory fields configured server-side. |

### Automatic Receipt Rule

A receipt is generated automatically after the cleared/validated amount is applied. A partial receipt reflects only cleared amounts. The invoice becomes paid only when the cleared total equals the payable total. Duplicate receipt generation is prevented by database constraint/idempotency.

### Locked-Period Rule

Every Finance mutation calls one central `PeriodLockService`. Locked periods block payment edits, late validation into the locked period, invoice voids, paid-invoice adjustments, receipt reversal/reissue, refunds, cash-up edits, compensation adjustments, and payout modifications, unless an authorized controlled reopen succeeds.

### Disputes and Refunds

Disputes support open, under review, evidence requested, resolved, rejected, escalated, and closed. Refunds support full/partial amount, method, reference, reason, evidence, approvals, invoice/payment impact, and complete audit. A refund cannot exceed the cleared amount less prior refunds.

### Finance Exports

Exports are permission-controlled, reason-required for sensitive data, branch/date/report scoped, private, generated asynchronously, delivered by expiring signed URL, download-counted, masked according to permission, and audited.

### Navigation

Finance Overview; Pending Validations; Gateway Exceptions; Customer Invoices; Payment Records; COD; Receipts; Partial & Split Payments; Credit Accounts; Disputes; Refunds; Cash-Up & Reconciliation; Financial Periods; Payout Runs; Salary & Commission Liabilities; Platform Billing; Finance Reports; Exports; Audit Activity; Notifications; Settings.

### Authentication

Magic Link after active invitation and branch assignment.

## 4.6 Merchant Dispatch & Customer Operations Account

### Purpose

Dispatch & Customer Operations is the branch-scoped operational role that receives and creates delivery requests, coordinates senders and recipients, applies or generates quotes within authority, assigns delivery personnel and vehicles, manages delivery-state movement, creates merchant-customer invoices, and records offline customer payments by default.

This role is the delivery-domain equivalent of Front Office in the upgraded operating model.

### Core Functionalities

1. View an action-first dispatch landing page and guided get-started page.
2. Search senders, recipients, delivery references, phone numbers, invoice numbers, payment references, vehicle plates, personnel names, pickup areas, destination areas, and statuses within branch scope.
3. Register senders and business customers; retrieve existing records; enforce duplicate prevention.
4. Create and maintain recipient records used for delivery purposes.
5. Create manual delivery requests on behalf of phone, walk-in, branch, or business customers.
6. Review sender-created delivery requests.
7. Validate pickup and destination details, pins, landmarks, contact fields, and time windows.
8. Add goods items, categories, quantities, weight, dimensions, declared value, photos, documents, handling requirements, and notes.
9. Apply automatic pricing or create a manual quote within permission limits.
10. Submit out-of-policy or high-value quote overrides for approval.
11. Accept or reject delivery requests according to merchant rules.
12. Create one-to-one, return, redelivery, multi-stop, branch-transfer, and recurring deliveries where entitled.
13. Assign or reassign eligible delivery personnel and vehicles.
14. Batch-assign jobs and construct route groups where entitled.
15. Select next-available, manual, or preferred delivery personnel.
16. Prevent assignment to unavailable, suspended, ineligible, over-capacity, document-expired, or conflicting personnel/vehicles.
17. Monitor pickup, transit, destination, proof, failure, return, and exception states.
18. Contact senders and recipients through approved in-platform channels.
19. Reschedule pickup/delivery windows within rules.
20. Transfer a delivery between eligible personnel, vehicles, or route groups with reason and audit.
21. Create merchant-customer invoices after quote acceptance or according to configured milestone.
22. Record cash, COD, bank, card-terminal, voucher, and other offline payments and submit for Finance validation.
23. Initiate M-Pesa/card payment requests where permitted.
24. View pending validation, payment cleared, receipt ready/issued, and balance statuses.
25. Pay the merchant's platform billing invoice through simplified M-Pesa UX where permission exists.
26. Create support handoffs, incident records, and dispute intake records.
27. View daily branch activity, current jobs, exceptions, customer communications, and SLA warnings.

### Delivery Creation Atomicity

Creating a delivery request must atomically create or attach:

```text
sender/customer
recipient
pickup address
one or more destination addresses
one or more goods items
branch
service type
pricing snapshot or quote request
tracking reference
customer communication preferences
initial status event
optional payment requirement
```

A failure in a required step rolls back the transaction or leaves a recoverable draft; it must not create orphan invoices, assignments, payments, or tracking tokens.

### Assignment Ownership

Dispatch owns operational assignment and transfer. HR owns eligibility; Branch Manager owns services/zones/calendar; Finance owns payment checking. Dispatch can assign only where eligibility and availability are valid.

### Explicit Exclusions

Dispatch must not:

- create branches or merchant tenants;
- manage staff roles, employment, eligibility, or compensation;
- edit branch pricing policy beyond permitted quote actions;
- validate its own offline payment record where maker/checker applies;
- approve cash-up;
- create uncontrolled refunds;
- alter locked-period finance records;
- view other branches without assignment;
- view another merchant's records;
- export customer contacts without a separately governed merchant-customer export permission;
- bypass prohibited-goods, capacity, address, payment, or billing-state controls.

### Dashboard Queues

New Requests; Address Review; Pending Quote; Quote Sent; Awaiting Customer Confirmation; Awaiting Payment; Ready for Assignment; Unassigned; Assigned; Pickup Due; Driver En Route; At Pickup; Picked Up; In Transit; Arriving; At Destination; Proof Pending; Delivered; Failed; Return Required; Disputed; Customer Contact Required; Invoice Required; Payment Pending Validation; Receipt Issued.

### Authentication

Magic Link after active invitation and branch assignment.

## 4.7 Merchant Delivery Personnel Account

### Purpose

Delivery Personnel are the field-service users who execute assigned pickups, transit, delivery, failed-attempt, return, and redelivery work. Examples include motorcycle riders, van drivers, truck drivers, couriers, delivery agents, loaders, and field runners.

### Own-Scope Enforcement

Delivery Personnel can access only:

| Area | Allowed data |
|---|---|
| Assignments | Jobs assigned to the authenticated personnel user. |
| Route groups | Route groups containing that user's assignments. |
| Stops | Pickup/delivery/return stops assigned to that user. |
| Customers | Minimum sender/recipient context required for active or historically performed jobs. |
| Proofs | Proofs created by or required from that user. |
| Incidents | Incidents raised by or assigned to that user. |
| Earnings | Own salary, commission, adjustments, payout items, and statements. |
| Communications | Approved in-platform communication for own assignments/served customers. |
| History | Own completed, failed, returned, cancelled, and reassigned jobs. |

One personnel user must not view, search, download, message, or infer another personnel user's assignments, customers, routes, earnings, proofs, incidents, or statements.

### Core Functionalities

1. Mobile-first landing page and get-started page.
2. View assigned jobs, route sequence, pickup/delivery windows, goods summary, handling instructions, payment/COD requirements, and proof requirements.
3. Accept or reject an assignment where merchant rules permit; rejection requires a reason and may trigger Dispatch reassignment.
4. Start navigation to pickup through approved map integration.
5. Mark en route to pickup.
6. Mark arrival at pickup with timestamp and optional geofence validation.
7. Verify sender/pickup custodian identity as required.
8. Confirm goods count, packaging condition, weight/dimensions where required, and exceptions.
9. Capture pickup OTP, name, signature, photo, document, barcode/QR, timestamp, coordinates, and notes according to service rules.
10. Mark pickup complete.
11. Start transit and provide permitted location updates.
12. View navigation to next stop.
13. Report delay, route blockage, accident, breakdown, unsafe condition, goods issue, address issue, customer unavailability, or other incident.
14. Mark arrival at destination.
15. Verify recipient identity and availability.
16. Collect COD where assigned and record handover information without validating the financial record.
17. Capture recipient OTP, signature, name, photo, timestamp, coordinates, and notes.
18. Mark delivery completed.
19. Record failed attempt with mandatory reason, evidence, contact attempts, timestamp, and coordinates.
20. Initiate return/redelivery workflow according to policy.
21. View own job history.
22. View own compensation model, estimated commission, salary where applicable, payout history, and terms.
23. Download own earnings statements through private signed URLs.
24. Raise own earnings queries.
25. Send approved in-platform messages/SMS to customers served through assigned deliveries, where enabled and consent permits.
26. Toggle limited operational availability states where policy permits.

### Availability States

```text
available
assigned
busy
on_break
offline
unavailable
suspended
```

HR controls persistent availability, employment, eligibility, and suspension. Delivery Personnel may control limited operational states where configured.

### Contact Export Permanently Removed

Delivery Personnel contact export does not exist in database flags, routes, permissions, UI, reusable table controls, CSV, Excel, PDF, JSON, clipboard actions, or APIs. Export-shaped requests return `404 Not Found` or policy denial according to security design and generate an unauthorized-access audit event.

### Customer Contact Protection

- Phone/email fields are masked until the delivery state requires contact.
- Full contact access may expire after a configurable post-completion period.
- Every reveal/call/SMS action is audited.
- Personnel cannot browse merchant-wide sender or recipient lists.
- In-platform communication hides raw contacts where supported.
- Promotional messaging requires customer consent and merchant policy.

### Authentication

Magic Link after invitation and branch assignment. A mobile PWA may retain a securely managed session; offline work uses encrypted local storage, minimal data, expiry, and sync controls.

## 4.8 Merchant Support Account

### Purpose

Support manages sender and recipient questions, delivery exceptions, complaints, communication, ticket SLAs, and handoff to Dispatch, Finance, Branch Manager, or Audit without receiving operational or financial superuser permissions.

### Core Functionalities

1. View support dashboard with open tickets, priority, SLA, branch, delivery status, and assigned owner.
2. Search delivery references, sender/recipient identity, invoice/receipt number, and communication history within branch scope.
3. View a field-masked delivery timeline and proof status.
4. Send approved email/SMS/WhatsApp-ready/in-app responses.
5. Record complaint, enquiry, damaged-goods allegation, missing-goods allegation, late delivery, wrong address, failed attempt, driver-conduct issue, payment issue, refund request, or proof dispute.
6. Attach private evidence.
7. Request information from Dispatch or Delivery Personnel.
8. Escalate operational issues to Dispatch/Branch Manager.
9. Escalate payment/refund/dispute matters to Finance.
10. Escalate suspicious conduct or access concerns to Audit/Super Administrator as appropriate.
11. Update support ticket status and resolution notes.
12. View sender/recipient satisfaction ratings and feedback.

### Explicit Exclusions

Support cannot assign deliveries, alter routes, validate payments, issue refunds, approve cash-up, edit compensation, modify proof, alter delivery status outside a tightly controlled correction workflow, or expose full customer contact data without permission.

### Authentication

Magic Link after active invitation and branch assignment.

## 4.9 Merchant Audit Account

### Purpose

Merchant Audit provides branch-scoped, read-only operational, financial, personnel, fleet, customer, proof, route, file, and compensation oversight, with a narrow exception for flagged-event metadata.

### Branch-Only Scope

An Audit user assigned to one branch cannot view another branch's deliveries, senders, recipients, personnel, vehicles, routes, invoices, payments, receipts, proofs, disputes, compensation, cash-up, or audit records. Every query is server-filtered.

### Read-Only Rule and Exception

Audit cannot create, edit, delete, validate, approve, reject, assign, reassign, dispatch, refund, reverse, pay, or mutate business records. Audit may only:

- create a flagged audit event;
- assign/update flagged-event review status;
- add audit review notes;
- resolve, dismiss, or escalate the flag.

The source business record remains immutable to Audit.

### Core Coverage

1. Delivery request creation and changes.
2. Address/pin changes.
3. Goods category, weight, value, and handling changes.
4. Quote creation, override, approval, discount, and price snapshot.
5. Assignment, rejection, reassignment, batch assignment, route changes, and preferred-personnel selection.
6. Personnel/vehicle eligibility and availability changes.
7. Pickup, transit, arrival, delivery, failure, return, and cancellation status changes.
8. GPS, proof, signature, OTP, photo, document, and proof-correction events.
9. Customer contact access, reveal, message, and prohibited export attempts.
10. Invoice, payment, validation, receipt, refund, dispute, cash-up, and period-lock events.
11. Platform billing visibility and payment events where permission exists.
12. Salary, commission, compensation-term, payout, adjustment, approval, and earnings-query events.
13. Staff, role, branch, status, and permission changes.
14. Vehicle compliance, capacity, maintenance, and assignment changes.
15. File upload, scan, access, export, and download.
16. API key, webhook, integration, and secret-governance events.
17. Unauthorized tenant, branch, own-scope, billing-state, locked-period, and file-access attempts.

### Field-Level Masking

Masked by default where permission does not justify full value:

- sender/recipient phone and email;
- exact residential details beyond required context;
- payment references;
- M-Pesa phones;
- card/bank metadata;
- salary amounts where policy restricts;
- internal sensitive notes;
- dispute evidence;
- API secrets;
- proof images containing sensitive data.

Exceptional unmasking is permission-gated, reason-required, time-bounded, and audited.

### Audit Exports

Branch-scoped, permission-gated, reason-required, masked, privately stored, signed, expiring, download-counted, and audited.

### Authentication

Magic Link after invitation and branch assignment.

## 4.10 Goods Sender Account

### Purpose

The Goods Sender is the person or business requesting transport from Point A. A sender may be a one-time consumer, frequent customer, business account, e-commerce merchant, warehouse, pharmacy, retailer, or enterprise integration customer.

### Core Functionalities

1. Register or sign in using Magic Link/verified OTP according to launch policy.
2. Create a delivery request.
3. Save pickup addresses, map pins, landmarks, access notes, and contact persons.
4. Save recipients and destination addresses.
5. Enter goods category, description, quantity, weight, dimensions, declared value, handling flags, photos, and documents.
6. Select available delivery service, vehicle category where allowed, urgency, pickup date/time, and delivery window.
7. Request or view a quote.
8. Accept or reject the quote before expiry.
9. Pay through configured method or request a business invoice/credit workflow.
10. Choose next-available or preferred delivery personnel where the merchant offers that option.
11. Track pickup and delivery status.
12. View ETA and permitted driver/vehicle details.
13. Receive pickup confirmation.
14. View proof of pickup and delivery according to privacy rules.
15. Cancel before cutoff and view cancellation fee before confirmation.
16. Request reschedule, return, or redelivery where policy permits.
17. View delivery, payment, invoice, receipt, refund, and dispute history.
18. Download invoices and receipts.
19. Open support tickets and disputes.
20. Rate delivery experience and provide feedback.
21. Manage consent and communication preferences.
22. Manage API keys/webhooks only for entitled business accounts through a separate secure business integration surface.

### Duplicate and Identity Rules

Sender records are tenant-owned. A verified email/phone may link to one sender identity within a merchant; duplicate attempts are reconciled rather than silently creating parallel profiles. The same person may hold separate records under different merchants because tenants are isolated.

### Privacy

A sender sees only recipient data necessary for the delivery and does not receive unrelated recipient history or merchant-internal notes.

## 4.11 Goods Recipient Access

### Purpose

The recipient receives the goods at Point B and requires delivery-scoped visibility and proof actions without being forced to create a full account.

### Launch Access Rule

```text
secure tracking link and/or OTP
expiring and revocable token
delivery-scoped authorization
rate-limited verification
no merchant-wide account access
no unrelated sender/recipient records
```

### Core Functionalities

1. Receive delivery notification.
2. Open secure tracking page.
3. Confirm or correct permitted contact/location details.
4. Confirm availability and preferred delivery window where merchant policy permits.
5. View expected delivery, ETA, status timeline, and permitted driver/vehicle details.
6. Receive delivery OTP.
7. Confirm delivery through OTP, digital signature, recipient name, photo, or other configured method.
8. Report wrong item, damaged goods, incomplete goods, failed attempt dispute, driver issue, or non-receipt.
9. View proof and receipt only where recipient is entitled.
10. Rate delivery experience.

### Full Recipient Account

A persistent recipient account remains optional. It may later provide recurring delivery history, saved addresses, remote instructions, and profile management. Initial launch does not require it.

## 4.12 Business Customer Human Accounts and Organization-Scoped Role Grants

Business-customer humans are Goods Sender identities with membership in one verified `business_customer_organization`. They are not separate global identity classes and are never merchant staff merely because their organization buys delivery services.

Canonical records are:

```text
sender_identities
business_customer_organizations
business_customer_memberships
business_customer_role_grants
business_customer_invitations
business_customer_membership_status_history
```

Every query is constrained by both `merchant_id` and `business_customer_organization_id`.

### Business Customer Administrator

Manages the organization profile, members, addresses, recipients, delivery-policy defaults, and permitted delegation. It cannot create merchant staff, validate merchant payments, view another customer organization, or remove the final active organization administrator without a replacement.

### Business Customer Operator

Creates and manages the organization's delivery requests, quotes, recurring routes, tracking, support cases, and permitted cancellations. It cannot view unrelated organizations, manage API secrets, approve merchant refunds, or validate merchant payments.

### Business Customer Finance Viewer

Reads organization invoices, payment allocations, receipts, credit-account statements, and permission-gated exports. It cannot mutate merchant finance records, approve refunds, validate payments, or view another organization's financial data.

### Business Customer Integration Administrator

Manages organization-owned API clients, credential versions, scopes, webhooks, usage, and revocation. It cannot retrieve a previous plaintext secret, exceed organization entitlements, or grant merchant/platform scopes.

### Invitation, Authentication, and Lifecycle

Authentication is merchant-scoped. Invitations include merchant, organization, intended role, inviter, expiry, and nonce. Business Customer Administrator and Integration Administrator require MFA. Finance Viewer requires MFA or step-up for sensitive exports. Deactivation revokes sessions and organization access while preserving requests, invoices, receipts, integrations, and audit history.

An invitation accepted under a different merchant returns `404 invitation_not_found`. Duplicate active membership returns `409 membership_exists`. Removing the final organization administrator returns `422 final_organization_administrator_required`. A suspended organization blocks all human memberships and machine clients without deletion. Multi-organization users explicitly select context; no default may combine records. Cross-organization access returns `404 not_found` to avoid existence disclosure.

## 4.13 Business Customer Integration Identity

### Purpose

A Business Customer Integration Identity is a non-human API/webhook identity owned by one active verified Business Customer Organization under one merchant. It cannot authenticate interactively, receive a Magic Link, or become merchant staff.

### Ownership Model

```text
Merchant
└── Business Customer Organization
    ├── Human Goods Sender members
    │   ├── Business Customer Administrator
    │   ├── Business Customer Operator
    │   ├── Business Customer Finance Viewer
    │   └── Business Customer Integration Administrator
    └── Business Customer Integration Identities
        ├── API clients
        ├── credential versions
        ├── scope grants
        └── webhook subscriptions
```

### Capabilities

Subject to entitlement, merchant policy, explicit scopes, business-organization ownership, and branch grants, an identity may create delivery requests idempotently, request or retrieve quotes, retrieve its organization’s delivery status, retrieve authorized proofs through signed access, retrieve its organization’s invoices and receipts, and manage webhook subscriptions through authorized human administrators.

It cannot create a merchant, create staff, perform Finance validation, access unrelated merchant customers, access merchant-wide reports, create a live assignment, bypass billing suspension, or operate outside granted branches.

A client may submit a preferred-personnel request but cannot create the authoritative assignment.

### Authentication and Credential Lifecycle

Use OAuth client credentials, signed JWT client assertion, or a high-entropy key/secret with request signing. Secrets are displayed once, stored securely, rotated by version, independently revocable, and rate-limited. The client cannot change its own scopes.

### Failure and Edge Handling

- An API client cannot exist without an active owning organization.
- Loss of the creating human administrator does not delete the client; another active organization administrator must assume governance or credentials are suspended.
- Plan downgrade disables new entitled operations after the effective date while retaining metadata, history, and revocation capability.
- Cross-tenant or cross-organization lookup returns an opaque not-found response.
- Valid authentication with insufficient scope returns `403`; invalid/revoked credentials return `401`.
- Compromise response supports immediate revocation, webhook-secret rotation, usage investigation, risk flag, and notifications.

## 4.14 System Integration Identities

Webhook processors, queues, schedulers, notification workers, payment callbacks, Maps adapters, file scanners, export workers, and Courier-to-Citrus Refer & Earn event publishers use non-interactive least-privilege service identities.

Service identities are environment-bound, purpose-bound, rotation-capable, non-human, and fully audited. They cannot receive human sessions or broad merchant access. The Courier referral event publisher may read only the minimum authoritative Courier facts required to produce approved product events and may not access central referrer payout details.

---

# 5. Orthogonal Delivery Lifecycle and Authoritative State Machines

Courier by Citrus does not use one monolithic delivery status. Each business domain has an independent state, immutable history, versioned transition registry, permission policy, evidence rule, audit event, error code, and migration rule. Frontend labels are derived projections only.

## 5.1 Registry Contract

Each machine-readable transition entry contains:

```text
domain
registry_version
action
from_state
to_state
permitted_actor_classes
required_permission
scope_rule
preconditions
required_evidence
financial_effect
assignment_effect
notification_effect
audit_event
idempotency_rule
concurrency_rule
failure_codes
```

The canonical files are:

```text
state-machines/request.v1.yaml
state-machines/quote.v1.yaml
state-machines/quote-approval.v1.yaml
state-machines/payment-requirement.v1.yaml
state-machines/assignment.v1.yaml
state-machines/custody-handover.v1.yaml
state-machines/operational-delivery.v1.yaml
state-machines/redelivery.v1.yaml
state-machines/dispute.v1.yaml
state-machines/refund.v1.yaml
state-machines/record-lifecycle.v1.yaml
```

Registry loading failure blocks deployment. Unknown actions fail closed. Active long-running records retain the registry/configuration version under which they started unless an approved migration explicitly changes them.

## 5.2 Standard Point A-to-Point B Flow

```text
Sender or Dispatch creates request draft
→ addresses are validated or returned for correction
→ request becomes ready for quote
→ quote is calculated or prepared
→ required quote approvals complete
→ quote is sent and accepted
→ configured payment requirement is satisfied, waived, or valid COD/credit terms are confirmed
→ request is confirmed
→ Dispatch or an authorized assignment service creates the assignment
→ personnel accepts where required
→ personnel travels to pickup
→ pickup arrival and verification are recorded
→ proof of pickup is captured
→ goods enter personnel/vehicle custody
→ transit and ETA updates occur
→ destination arrival is recorded
→ COD tender evidence is captured where applicable
→ proof of delivery is captured
→ physical operational status becomes delivered
→ invoice, payment, refund, dispute, compensation, cash-up, and record-lifecycle processing continue independently
```

## 5.3 Request and Commercial State

```text
draft
pending_address_validation
ready_for_quote
awaiting_customer_confirmation
confirmed
cancelled
```

Authoritative transitions:

```text
draft --submit_for_address_validation--> pending_address_validation
draft --cancel--> cancelled
pending_address_validation --validation_passed--> ready_for_quote
pending_address_validation --correction_required--> draft
pending_address_validation --cancel--> cancelled
ready_for_quote --send_commercial_offer--> awaiting_customer_confirmation
ready_for_quote --cancel--> cancelled
awaiting_customer_confirmation --customer_confirm--> confirmed
awaiting_customer_confirmation --revise_offer--> ready_for_quote
awaiting_customer_confirmation --cancel--> cancelled
confirmed --cancel_where_lifecycle_policy_allows--> cancelled
```

A confirmed request is never cancelled by direct field mutation. After goods enter custody, cancellation is replaced by return, hold, redelivery, lawful disposal, or dispute handling.

## 5.4 Quote State and Approval Workflow

The one authoritative quote enum is:

```text
draft
pending_approval
approved
sent
accepted
rejected
expired
superseded
cancelled
```

Approval detail is stored in `quote_approval_workflows` and `quote_approval_steps`, not in the quote enum. Allowed stage keys are:

```text
dispatch_tolerance_review
branch_manager_review
merchant_admin_review
finance_policy_review
```

`current_approval_stage` is a rebuildable projection of the first incomplete required step.

Authoritative transitions:

```text
draft --submit_for_approval--> pending_approval
draft --approve_within_delegated_tolerance--> approved
pending_approval --approve_required_step--> pending_approval
pending_approval --complete_final_required_step--> approved
pending_approval --return_for_changes--> draft
pending_approval --reject--> rejected
pending_approval --cancel--> cancelled
approved --send--> sent
approved --supersede_with_revision--> superseded
approved --cancel--> cancelled
sent --customer_accept--> accepted
sent --customer_reject--> rejected
sent --expire--> expired
sent --supersede_with_revision--> superseded
sent --cancel_where_policy_allows--> cancelled
```

Accepted, rejected, expired, superseded, and cancelled quote versions are immutable. A price or term change creates a new draft version. Commercial cancellation after acceptance is handled by request/delivery cancellation policy and never rewrites the accepted quote.

Error and edge handling:

- duplicate approval using the same idempotency key returns the existing result;
- a second distinct decision on a completed step returns `409 approval_step_already_decided`;
- missing approval permission returns `403 forbidden`;
- self-approval of a prohibited above-threshold override returns `403 separation_of_duty_violation`;
- quote expiry during approval closes the workflow and requires a new or repriced version;
- concurrent final approvals use aggregate versioning and the first valid commit wins.

## 5.5 Payment-Requirement State

```text
not_required
pending
partially_satisfied
satisfied
waived
failed
cancelled
```

Authoritative transitions:

```text
not_required --require_payment--> pending
pending --allocate_cleared_value_below_requirement--> partially_satisfied
pending --allocate_cleared_value_meeting_requirement--> satisfied
partially_satisfied --allocate_additional_cleared_value_below_requirement--> partially_satisfied
partially_satisfied --allocate_value_meeting_requirement--> satisfied
pending|partially_satisfied --waive_with_authority--> waived
pending|partially_satisfied --mark_requirement_failed--> failed
pending|partially_satisfied|failed --cancel_requirement--> cancelled
failed --retry_requirement--> pending
```

A refund or reversal that reduces cleared value below the requirement creates a recalculation event. Where progression remains legally and operationally valid, the original progression is retained and a financial exception is opened. Where policy makes progression revocable, the registry may return `satisfied` to `partially_satisfied` or `pending`; that rule must be explicit and snapshotted.

## 5.6 Assignment State

```text
unassigned
offered
accepted
rejected
expired
cancelled
superseded
handover_pending
completed
```

Authoritative transitions:

```text
unassigned --offer--> offered
unassigned --dispatch_accept_with_authority--> accepted
offered --assignee_accept--> accepted
offered --assignee_reject--> rejected
offered --expire--> expired
offered --cancel_offer--> cancelled
rejected --return_to_pool--> unassigned
expired --return_to_pool--> unassigned
accepted --begin_handover--> handover_pending
accepted --supersede_before_pickup--> superseded
accepted --cancel_where_policy_allows--> cancelled
accepted --complete_assignment--> completed
handover_pending --complete_handover--> superseded
handover_pending --cancel_handover--> accepted
```

Reassignment is an immutable assignment-domain event. It is not an operational delivery status. Before pickup, the old assignment may be superseded without custody transfer. After pickup, reassignment requires the custody-handover workflow.

## 5.7 Custody-Handover State

Handover coordinates two assignment records and one custody record:

```text
outgoing assignment: accepted -> handover_pending -> superseded
incoming assignment: unassigned/new -> offered -> accepted
custody handover: initiated -> outgoing_confirmed -> incoming_confirmed -> completed
```

Where direct dispatch acceptance is allowed, the incoming assignment may be created as `accepted`, with actor, authority, and reason recorded. The outgoing assignment becomes `superseded` only after completed custody transfer.

The custody record contains delivery, outgoing and incoming assignments, personnel, vehicles, authorization, reason, location, timestamps, goods condition, evidence, discrepancies, confirmations, and version.

Edge handling:

- incoming acceptance before initiation returns `409 handover_not_ready`;
- concurrent handovers return `409 active_handover_exists`;
- incoming decline cancels the handover and leaves the outgoing assignment accepted;
- a one-sided offline confirmation leaves the record pending and retryable;
- goods-condition discrepancy opens an incident and blocks completion;
- same-person or same-assignment transfer is rejected unless an explicit vehicle-only transfer policy applies.

## 5.8 Operational Delivery State

```text
awaiting_pickup
driver_en_route_to_pickup
arrived_at_pickup
pickup_verification_pending
picked_up
in_transit
arriving_at_destination
arrived_at_destination
delivery_proof_pending
delivered
failed_attempt
return_in_transit
returned
cancelled_before_custody
```

Authoritative transitions:

```text
awaiting_pickup --depart_for_pickup--> driver_en_route_to_pickup
awaiting_pickup --cancel_before_custody--> cancelled_before_custody
awaiting_pickup --report_failed_attempt--> failed_attempt
driver_en_route_to_pickup --arrive_at_pickup--> arrived_at_pickup
driver_en_route_to_pickup --report_failed_attempt--> failed_attempt
arrived_at_pickup --begin_pickup_verification--> pickup_verification_pending
arrived_at_pickup --report_failed_attempt--> failed_attempt
pickup_verification_pending --complete_pickup--> picked_up
pickup_verification_pending --report_failed_attempt--> failed_attempt
picked_up --start_transit--> in_transit
in_transit --mark_arriving--> arriving_at_destination
in_transit --report_failed_attempt--> failed_attempt
in_transit --start_return--> return_in_transit
arriving_at_destination --arrive_at_destination--> arrived_at_destination
arriving_at_destination --report_failed_attempt--> failed_attempt
arrived_at_destination --request_delivery_proof--> delivery_proof_pending
arrived_at_destination --report_failed_attempt--> failed_attempt
delivery_proof_pending --complete_delivery--> delivered
delivery_proof_pending --report_failed_attempt--> failed_attempt
failed_attempt --approve_pre_pickup_retry--> awaiting_pickup
failed_attempt --start_return--> return_in_transit
failed_attempt --create_linked_redelivery--> failed_attempt
return_in_transit --complete_return--> returned
```

`closed` is not an operational state. Physical terminal outcomes remain `delivered`, `returned`, or `cancelled_before_custody`. Refund, dispute, chargeback, compensation adjustment, administrative closure, or archival never rewrites the physical outcome.

## 5.9 Administrative Record Lifecycle

```text
open
administratively_closed
reopened
archived
```

Authoritative transitions:

```text
open --administratively_close--> administratively_closed
administratively_closed --reopen_for_follow_up--> reopened
reopened --administratively_close--> administratively_closed
administratively_closed --archive_after_retention_checks--> archived
```

Administrative closure requires required proof, completed mandatory notification, no unresolved operational exception, and no prohibited lock. A post-closure dispute can move lifecycle to `reopened` while operational state remains `delivered`. Archival is blocked by legal hold, open dispute, unsettled refund, pending retention duty, or required audit preservation.

## 5.10 Redelivery State

```text
not_required
requested
pending_quote
awaiting_customer_approval
scheduled
awaiting_assignment
in_progress
completed
rejected
expired
cancelled
```

Authoritative transitions:

```text
not_required --request_redelivery--> requested
requested --prepare_quote--> pending_quote
pending_quote --send_for_customer_approval--> awaiting_customer_approval
awaiting_customer_approval --customer_approve--> scheduled
awaiting_customer_approval --customer_reject--> rejected
awaiting_customer_approval --expire--> expired
requested|pending_quote|awaiting_customer_approval|scheduled|awaiting_assignment --cancel--> cancelled
scheduled --open_for_assignment--> awaiting_assignment
awaiting_assignment --assignment_accepted--> in_progress
in_progress --complete_redelivery--> completed
```

Redelivery creates a linked attempt or leg and never overwrites the original failed attempt. Once the new leg enters custody, simple cancellation is forbidden; return or controlled exception handling applies.

## 5.11 Dispute State

```text
none
open
evidence_requested
under_review
awaiting_external_resolution
resolved_upheld
resolved_partially_upheld
resolved_rejected
withdrawn
closed
```

Authoritative transitions:

```text
none --open_dispute--> open
open|under_review --request_evidence--> evidence_requested
evidence_requested --submit_evidence--> under_review
open|evidence_requested --begin_review--> under_review
under_review --refer_external--> awaiting_external_resolution
awaiting_external_resolution --resume_review--> under_review
open|evidence_requested|under_review|awaiting_external_resolution --withdraw--> withdrawn
under_review|awaiting_external_resolution --resolve_upheld--> resolved_upheld
under_review|awaiting_external_resolution --resolve_partially_upheld--> resolved_partially_upheld
under_review|awaiting_external_resolution --resolve_rejected--> resolved_rejected
resolved_upheld|resolved_partially_upheld|resolved_rejected|withdrawn --close--> closed
closed --reopen_with_privileged_authority--> under_review
```

A reopened dispute creates a new review cycle and immutable history. Resolution records evidence summary, reason, financial-impact decision, actor, timestamp, and configuration version.

## 5.12 Refund State

```text
none
requested
pending_approval
approved
processing
completed
failed
rejected
cancelled
reversal_pending
reversed
```

Authoritative transitions:

```text
none --request_refund--> requested
requested --submit_for_approval--> pending_approval
requested --cancel--> cancelled
pending_approval --approve--> approved
pending_approval --reject--> rejected
pending_approval --cancel_before_approval--> cancelled
approved --start_processing--> processing
approved --cancel_before_processing--> cancelled
processing --complete--> completed
processing --fail--> failed
failed --retry_processing--> processing
completed --request_reversal--> reversal_pending
reversal_pending --complete_reversal--> reversed
```

Refund completion creates append-only financial adjustments and never erases or rewrites the original payment, allocation, invoice, or delivered physical state.

## 5.13 Derived Display Status

The UI, PWA, reports, notifications, and read APIs may display a centrally versioned derived label, for example:

```text
operational_status = delivered, dispute_status = open
→ Delivered — Dispute Open

operational_status = delivered, refund_status = completed
→ Delivered — Refunded

request_status = awaiting_customer_confirmation, quote_status = sent
→ Quote Awaiting Acceptance

request_status = confirmed, payment_requirement_status = pending
→ Awaiting Required Payment
```

The label is not persisted as an authoritative state and cannot be patched.

## 5.14 Failed Delivery, Return, Cancellation, and Chain of Custody

Every failed attempt records reason code, notes where required, contact attempts, timestamp, location where permitted, evidence, goods condition, next action, fee snapshot, actor, and audit. After custody, cancellation is replaced by return, hold, redelivery, lawful disposal, or dispute. Every post-pickup personnel or vehicle transfer requires outgoing and incoming confirmation, location, time, goods-condition evidence, reason, Dispatch authorization, and immutable custody history.

## 5.15 Transition Enforcement and Error Handling

- Invalid source state: `409 invalid_transition`, including safe current state and allowed actions.
- Stale aggregate version: `409 state_conflict`.
- Missing evidence: `422 evidence_required`.
- Locked financial period: `423 financial_period_locked`.
- Assignment changed during offline submission: `403 assignment_no_longer_owned` or `409 assignment_changed`.
- Duplicate provider callback: idempotent acknowledgement when payload hash matches; conflict when the same idempotency/event ID carries different content.
- External side effect: outbox event recorded in the same transaction; delivery retry does not repeat the state mutation.
- Target state absent from enum: registry validation and deployment fail.

## 5.16 Delivery Reference and Tracking Tokens

Every delivery has a tenant-unique human reference, opaque internal identifier, and scoped tracking token. Tokens are delivery-bound, expiring, revocable, rate-limited, purpose-limited, and stored hashed where practical. They never reveal internal sequential identifiers or unrelated recipient/delivery existence.

# 6. Delivery Request, Goods, Address, and Customer Management

## 6.1 Delivery Request Creation

Requests may originate from:

- authenticated sender;
- business customer portal;
- business API;
- Dispatch manual creation;
- recurring delivery template;
- approved import;
- webhook/integration;
- branch-transfer workflow;
- return/redelivery workflow.

Every origin records `created_by_type`, `created_by_id`, branch, source channel, and audit context.

## 6.2 Required Request Fields

- merchant and branch;
- sender/customer;
- pickup contact;
- pickup address, coordinates, place ID where available, landmark, access notes;
- recipient contact;
- destination address, coordinates, place ID where available, landmark, access notes;
- service type;
- preferred pickup date/time or window;
- delivery window;
- goods items;
- communication consent/preferences;
- quote/payment policy state;
- reference number.

## 6.3 Goods Fields

| Field | Requirement |
|---|---|
| Goods category | Required. |
| Description | Required. |
| Quantity | Required. |
| Weight | Required where pricing/capacity depends on weight; otherwise recommended. |
| Dimensions | Required where vehicle/capacity depends on dimensions; otherwise recommended. |
| Declared value | Optional or required by merchant/insurance policy. |
| Fragile | Configurable flag. |
| Hazardous | Configurable flag; may require qualification or prohibition. |
| Perishable | Configurable flag. |
| Temperature-controlled | Configurable flag. |
| High-value | Configurable flag and approval threshold. |
| Special handling | Optional text plus structured instructions. |
| Photo/document | Recommended or required by category. |
| Barcode/QR | Optional launch field, advanced scanning later. |

## 6.4 Restricted and Prohibited Goods

The Super Administrator maintains global prohibited/restricted category policy. Merchants may add stricter rules but cannot weaken mandatory platform rules. The system can:

- block prohibited requests;
- flag restricted goods;
- require declarations;
- require qualified personnel/vehicle;
- require documents;
- require manual review;
- require insurance acknowledgement;
- audit all decisions.

## 6.5 Sender and Recipient Records

Customer records include profile, contacts, address book, frequent routes, delivery history, invoice/payment history, credit status, support history, consent, preferences, notes, and risk flags. Tenant isolation is strict.

## 6.6 Duplicate Prevention

Duplicate rules are merchant-specific and server-side. Recommended launch rules:

- same merchant + verified sender phone/email = link/recover existing sender rather than duplicate;
- same branch + recipient phone + normalized address may warn, not necessarily block, because recipients can receive repeated deliveries;
- duplicate business-customer registration numbers or email domains are flagged for review;
- duplicate delivery submissions with the same idempotency key are rejected safely;
- duplicate payment references are controlled by Finance rules.

## 6.7 Request Editing

A draft may be edited freely by authorized actors. After quote generation, fields affecting price require quote invalidation/recalculation. After assignment or pickup, edits are restricted, reason-required, versioned, and may create route, pricing, payment, and compensation adjustments.

## 6.8 File Uploads

Goods photos/documents use private storage. Server validation covers MIME/type signature, extension, size, dimensions where relevant, filename sanitation, malware-scanning hook, metadata stripping where appropriate, tenant path, branch path, access policy, and audit.

---

# 7. Delivery Service Catalogue, Quotation, Pricing, and Preferred Personnel

## 7.1 Branch Service Catalogue

Branch Manager creates delivery services such as:

- standard motorcycle delivery;
- express motorcycle delivery;
- same-day van delivery;
- scheduled truck delivery;
- refrigerated delivery;
- high-value secure delivery;
- document delivery;
- fragile-goods delivery;
- inter-city delivery;
- return delivery;
- multi-stop route;
- custom merchant service.

Each service defines name, description, status, service zones, vehicle classes, goods eligibility, weight/dimension limits, operating schedule, target SLA, proof requirements, payment policy, cancellation rules, failed-attempt policy, return policy, and pricing configuration.

## 7.2 Pricing Inputs

Launch-capable pricing inputs include:

1. fixed base fee;
2. distance fee;
3. weight fee;
4. dimensional/volume fee;
5. vehicle-type fee;
6. service-level fee;
7. urgency surcharge;
8. pickup/delivery time-window surcharge;
9. zone-based charge;
10. minimum fee;
11. waiting-time fee;
12. failed-delivery fee;
13. redelivery fee;
14. return fee;
15. multi-stop fee;
16. high-value handling fee;
17. fragile/hazardous/perishable/temperature-control fee;
18. toll/parking/pass-through charge;
19. preferred-personnel fee;
20. tax/VAT;
21. promotion/discount;
22. manual override with reason and approval threshold.

Illustrative formula:

```text
Total = Base Fee
      + Distance Fee
      + Weight/Volume Fee
      + Vehicle/Service-Level Fee
      + Zone/Time/Urgency/Handling Surcharges
      + Waiting/Stop/Pass-Through Charges
      + Preferred Personnel Fee
      - Discounts/Credits
      + Taxes
```

Every quote snapshots inputs, rule versions, distance, ETA, currency, tax, discount, creator, approver, and expiry.

## 7.3 Manual and Automatic Quotes

MVP must support both:

- manual quote creation; and
- basic automatic distance/service pricing.

Advanced dynamic pricing may expand later, but the data model and quote snapshot must support it. Manual overrides require permission, reason, old/new value, and approval where threshold is exceeded.


## 7.3A Quote Approval Authority and Maker/Checker

Automatic quotes generated entirely from active approved pricing rules are system-approved. Dispatch may approve manual adjustments within an effective-dated delegated tolerance. Above-tolerance branch exceptions require Branch Manager approval. High-value, below-cost, cross-branch, or strategic customer exceptions require Merchant Administrator approval. Finance reviews tax, currency, credit, or financial-policy exceptions only where required.

The creator cannot approve its own above-threshold override. A quote may not be accepted while an approval stage remains pending. If a quote expires during approval, the approval request closes and a new version must be created or re-priced. Concurrent approve/reject operations use optimistic locking.

## 7.4 Quote Status and Approval Projection

The canonical `quote_status` values and transition registry are defined in Section 5. Quote approval actors are stored in `quote_approval_steps`, never as detailed quote-state enum values. UI text such as “Awaiting Branch Manager review” is a derived label from the first incomplete required approval step.

A pricing or scope change creates a new quote version. Accepted, rejected, expired, superseded, and cancelled quote versions remain immutable. Commercial cancellation after acceptance belongs to the request/delivery cancellation workflow rather than rewriting the quote version.

## 7.5 Preferred Delivery Personnel Request and Fee Rules

A sender preference does not create a delivery assignment. Courier distinguishes:

```text
preferred_personnel_request
preferred_personnel_reservation
delivery_assignment
```

The sender may select only personnel made customer-visible by merchant policy. Preliminary checks consider branch, role, active employment, service eligibility, vehicle class, goods qualification, availability, working hours, capacity, conflicts, and privacy.

### Preferred-Personnel Workflow

```text
Sender selects preferred personnel
→ system performs preliminary eligibility and visibility checks
→ preferred-personnel fee is quoted
→ sender accepts quote and applicable fee terms
→ system records preferred-personnel request
→ system may create a short-lived atomic reservation
→ final payment, eligibility, vehicle, capacity, conflict, branch, and compliance checks run
→ Dispatch or authorized assignment service creates authoritative assignment
→ personnel accepts where required
→ reservation is consumed
```

A reservation expires automatically and does not guarantee assignment unless an explicitly sold guaranteed reservation product is configured.

### Request Statuses

```text
requested
preliminarily_eligible
reserved
awaiting_payment
ready_for_dispatch
fulfilled
unavailable
declined
expired
cancelled
refunded
```

### Fee Recognition

By default, the preferred-personnel fee becomes chargeable only after a valid assignment is created and accepted. If Courier cannot fulfil the preference, the fee is removed, credited, or refunded automatically according to the captured policy. Cancellation, substitution, failed delivery, return, and redelivery treatment must be explicit and snapshotted.

### Error and Edge Handling

- simultaneous reservations use atomic capacity checks;
- personnel becoming unavailable causes revalidation, release, notification, and fee correction;
- late payment after reservation expiry triggers fresh checks and never silently assigns unavailable personnel;
- Dispatch may override preference for safety, compliance, capacity, or operations with reason, sender notification, fee treatment, and audit;
- sender-visible lists must not enumerate all merchant staff;
- cancellation releases the reservation immediately;
- assignment and reward/commission calculations must prevent duplicated earnings after reassignment or failed reservation.

## 7.6 Pricing Governance

- Branch Manager owns catalogue/pricing rules within merchant policy.
- Dispatch applies rules and may create permitted manual quotes.
- Finance sees all monetary snapshots and reconciles invoices/payments.
- Merchant Administrator views merchant-wide pricing and performance but is not default editor.
- Super Administrator configures platform billing, not merchant delivery prices.
- Audit sees changes and overrides within branch scope.

---

# 8. Dispatch, Assignment, Fleet, Routing, and Live Tracking

## 8.1 Dispatch Board

The dispatch board supports list, kanban, timeline, and map views for active jobs. Filters include branch, service, status, pickup area, destination area, urgency, time window, goods type, weight, required vehicle, assigned personnel, unassigned state, payment state, customer, route group, and exception.

## 8.2 Eligibility and Availability

Assignment requires all of the following:

- active personnel account;
- active employment status;
- correct branch assignment;
- service eligibility;
- vehicle-class eligibility;
- goods/handling qualification where required;
- available state;
- no blocking document expiry;
- no conflicting assignment beyond configured capacity;
- active vehicle;
- vehicle capacity compatible with goods;
- service area compatible with route;
- branch open/operational;
- merchant operational/billing state permits mutation;
- plan entitlement permits the capability.

## 8.3 Assignment Functions

1. single assignment;
2. reassignment;
3. batch assignment;
4. route-group creation;
5. multi-stop sequence;
6. next-available selection;
7. manual selection;
8. preferred personnel;
9. vehicle assignment;
10. vehicle replacement;
11. assignment offer/acceptance;
12. rejection handling;
13. conflict prevention;
14. load/capacity view;
15. service-window warning;
16. audit trail.

## 8.4 Route Planning

Launch route features:

- Point A to Point B route;
- waypoint support;
- route polyline;
- distance and duration;
- traffic-aware ETA where available;
- manual route adjustment notes;
- map-based visualization;
- route snapshot on quote/assignment;
- re-route event logging.

Advanced route optimization supports multiple vehicles/stops, capacity, time windows, driver hours, shipment size/weight, balancing, and re-optimization after cancellation/failure.

## 8.5 Vehicle and Fleet Management

Vehicle fields:

- merchant and branch;
- internal fleet code;
- type: motorcycle, small van, pickup, box truck, lorry, refrigerated vehicle, bicycle, custom;
- plate/registration number;
- capacity by weight, volume, and item count where used;
- service area;
- assigned/default personnel;
- availability;
- active/inactive/suspended/maintenance status;
- insurance, inspection, licence, ownership/lease documents;
- expiry dates and reminders;
- refrigeration/special equipment;
- odometer/maintenance data where enabled;
- current job load;
- audit history.

## 8.6 Driver Location

Location tracking is purpose-limited, consented, transparent, and retained according to policy. The platform distinguishes active-job location from general personnel surveillance. Location updates are accepted only from authorized sessions/devices and are rate-limited, timestamped, sanity-checked, and associated with assignments.

## 8.7 Live Tracking

The secure tracking page may display:

1. delivery reference;
2. status timeline;
3. pickup/destination summary;
4. estimated pickup/delivery time;
5. route map;
6. live driver position where merchant policy permits;
7. driver/vehicle details where allowed;
8. communication/action prompts;
9. proof after completion;
10. issue-reporting action.

Privacy rules prevent unnecessary sender-to-recipient or recipient-to-sender data disclosure.

## 8.8 ETA and Wait Calculation

Estimated pickup/delivery time uses active personnel, current location where available, queue/job load, route distance, traffic, service duration, stop sequence, time windows, and branch operating state. The UI labels estimates as estimates, records the calculation timestamp, and never presents stale ETA as guaranteed.

---

# 9. Proof of Pickup, Proof of Delivery, Incidents, Failures, Returns, and Disputes

## 9.1 Proof Methods

Configurable proof methods include:

- OTP;
- digital signature;
- sender/recipient name;
- photo;
- timestamp;
- GPS coordinates;
- document upload;
- barcode/QR scan;
- goods-condition checklist;
- item count;
- personnel notes;
- witness name;
- failed-attempt evidence;
- return confirmation.

Recommended MVP proof: OTP, photo, timestamp, coordinates, and recipient name. Signature remains available where required.

## 9.2 Proof Rules

Proof requirements are configured by service, goods category, customer contract, payment method, and delivery state. A delivery cannot be marked complete until required proof passes validation. Proof updates after completion require a controlled correction workflow; original evidence remains preserved.

## 9.3 OTP Security

OTPs are one-time, short-lived, rate-limited, hashed at rest, attempt-limited, delivery-scoped, and invalidated after success or terminal cancellation. OTP values are never logged in plaintext.

## 9.4 Photo and Signature Security

Files are private, authorized, size/type validated, malware-scan-ready, metadata-managed, and accessed through expiring links. Signature images are treated as sensitive evidence.

## 9.5 Incident Management

Incident types include accident, breakdown, delay, unsafe route, address failure, customer unavailable, damaged goods, missing goods, package mismatch, refusal, payment/COD issue, prohibited goods, theft/loss allegation, weather, security event, or other. Incidents support severity, evidence, assigned owner, status, resolution, financial impact, customer communication, and audit.

## 9.6 Disputes

Disputes may relate to quote, delivery charge, payment, proof, condition, non-delivery, lateness, personnel conduct, refund, or return. Statuses:

```text
open
under_review
evidence_requested
resolved
rejected
escalated
closed
```

Audit cannot resolve the underlying dispute; Finance/Support/authorized management follows the business workflow.

---

# 10. Merchant-Customer Invoices, Payments, Receipts, Credit, Refunds, and Cash-on-Delivery

## 10.1 Invoice Creation

Dispatch & Customer Operations creates invoices from accepted quote snapshots. Invoices contain merchant logo, branch prefix, customer, delivery reference, service lines, surcharges, preferred-personnel fee, discounts, taxes, amount, currency, payment terms, and status. Invoice numbers are merchant-wide unique with optional branch prefix.

## 10.2 Invoice Settlement State and Derived Labels

The authoritative mutable invoice settlement states are:

```text
draft
issued
partially_settled
settled
overdue
voided
cancelled
```

Dispute and refund conditions are not invoice source states. They are held in separate dispute and refund aggregates. The invoice read model may expose derived labels such as `has_open_dispute`, `partially_refunded`, or `fully_refunded`, calculated from authoritative records and allocations. Derived labels cannot be directly written.

Voided and cancelled invoices retain invoice number, versions, lines, allocations, reasons, and audit history. A settled invoice is not rewritten to unpaid solely because a refund occurs; refund adjustments and resulting receivable/credit balances are represented explicitly.

## 10.3 Payment Methods

- M-Pesa STK Push;
- M-Pesa PayBill/Till;
- card gateway;
- bank transfer;
- cash;
- cash on delivery;
- card terminal;
- voucher;
- credit account;
- split payment;
- merchant-defined offline method.


## 10.3A Payment-Requirement Models

Every delivery request captures one authoritative requirement:

```text
prepaid
preauthorized
postpaid
cash_on_delivery
credit_account
split
no_charge
```

### Prepaid

Quote acceptance creates or finalizes the invoice. Cleared payment satisfies the requirement before confirmation or assignment. Partial payment proceeds only where a defined deposit policy permits it.

### Preauthorized

Authorization and capture remain separate events. Confirmation may follow successful authorization; capture occurs at the configured milestone.

### Post-paid

The delivery may proceed after quote acceptance. The invoice becomes due at the configured milestone and payment reconciliation continues independently of physical delivery state.

### Cash on Delivery

The COD amount is attached to the assignment. Delivery Personnel records collection evidence but does not validate the payment. Branch cash-up and Finance validation determine clearance and receipt generation.

### Credit Account

The system verifies active credit terms, limit, exposure, due date, and ledger reservation. Over-limit requests require approved override.

### Split Payment

Each leg has method, amount, timing, reference, validation state, and allocation. Failure of one leg does not erase cleared legs.

### No Charge

A zero-charge delivery requires an authorized waiver, reason, approver where required, and accounting record.

### Edge Handling

Pending provider callbacks, quote expiry, duplicate callbacks, overpayment, underpayment, currency mismatch, cancellation after payment, and partial split failure enter explicit reconciliation or adjustment workflows. No payment fact is silently discarded or used to overwrite operational delivery status.

## 10.4 Integrated Payment Lifecycle

```text
payment initiated
→ gateway request created
→ customer approves or fails/cancels
→ signed callback received
→ callback validated
→ transaction deduplicated
→ payment reconciled to invoice
→ cleared amount applied
→ invoice updated
→ receipt auto-generated
→ notifications and audit emitted
```

Finance handles exceptions, not ordinary successful callbacks.

## 10.5 Offline Payment Lifecycle

```text
Dispatch records payment
→ status pending_validation
→ Finance reviews evidence/reference
→ validates, partially validates, rejects, disputes, or requests correction
→ cleared amount applied
→ receipt auto-generated
```

## 10.6 Cash-on-Delivery

COD requires delivery assignment, expected amount, collecting personnel, collection status, handover status, cash-up linkage, discrepancy status, and Finance validation. Personnel can record collection/handover evidence but cannot validate or write off COD.

## 10.7 Split and Partial Payments

Each payment leg has its own method, reference, status, amount, actor, and validation. The system calculates remaining balance. Receipt totals include only cleared amounts.

## 10.8 Duplicate Reference Detection

Duplicate M-Pesa, bank, card, voucher, or custom references are blocked or warned within the merchant. Overrides require Finance permission and reason and are audited.

## 10.9 Refunds and Maker/Checker Control

Refund state and transitions are defined in Section 5. A refund never erases or rewrites the original payment attempt, payment, allocation, receipt, or invoice history. Completion produces append-only refund/adjustment records and recalculates derived invoice/customer balances.

By default, Dispatch or Support may capture a refund request and supporting reason; Finance prepares and submits the refund. The same Finance user cannot approve a refund they prepared when maker/checker is required. Merchant Administrator approval applies only to configured high-value or exceptional thresholds. Provider execution is performed only after all required approvals.

Self-approval returns `403 separation_of_duty_violation`. A request above the actor's threshold returns `403 approval_threshold_exceeded`. Duplicate provider callbacks are idempotent. A timeout leaves the refund in `processing` or `failed` according to provider evidence and triggers reconciliation; it must not be assumed completed. Cancellation is allowed only before processing starts. Refund against a locked period creates a current-period adjustment or controlled reopen request according to policy; it never silently mutates the locked ledger.

## 10.10 Tax and VAT

Tax configuration is merchant/country-aware, versioned, and snapshotted on invoices. Tax changes do not silently alter issued invoices.

## 10.11 Credit Customers

Entitled business customers may have approved credit limits, payment terms, billing contacts, account status, ageing, statements, and overdue controls. Credit approval is permissioned and audited.

---

# 11. Subscription-First Platform Billing and Pricing Architecture

## 11.1 Configurable Billing Modes

The Super Administrator selects one active platform billing mode without code deployment:

```text
fixed_amount
percentage_on_merchant_customer_invoice
fixed_amount_plus_percentage_on_merchant_customer_invoice
```

The configuration is effective-dated, audited, and read through a billing service. Historical invoices keep the mode snapshot under which they were generated.

## 11.2 Fixed-Amount Mode

The merchant is billed according to plan, billing period, branch count, storage/add-ons, SMS, discounts, credits, and taxes.

Supported periods:

```text
weekly
bi_weekly
monthly
quarterly
annual
```

## 11.3 Percentage Mode

Percentage billing is launch-supported. A platform-fee entry is calculated from a configured fee basis on merchant-customer invoices.

Permitted fee bases:

```text
merchant_customer_invoice_service_subtotal
merchant_customer_invoice_total
cleared_paid_amount
net_after_discount
invoice_item_subtotal
```

The basis is never implicit.

### Percentage-Fee Lifecycle

```text
merchant-customer invoice finalized
→ billing mode, tier, rate, fee basis, and monetary allocation snapshotted
→ provisional platform-fee ledger entry created
→ customer payment clears/validates
→ corresponding fee becomes billable
→ billable fee entries aggregate into merchant platform billing invoice
→ merchant pays through M-Pesa
→ reconciliation settles platform liability
```

Voids, refunds, corrections, and partial refunds create append-only adjustments. Original fee entries are never silently edited or deleted.

### Tier Behaviour

| Tier | Merchant-customer invoice treatment | Customer-shifted amount | Merchant liability to Citrus |
|---|---|---:|---:|
| `customer_centric` | Customer invoice contains merchant delivery price only; merchant absorbs the platform fee. | 0 | Full calculated fee |
| `shared` | Configured share of platform fee is added to customer invoice; merchant absorbs remainder. | Configured share | Full calculated fee |
| `business_centric` | Full platform fee is added to customer invoice. | Full calculated fee | Full calculated fee |

The tier changes commercial allocation, not traceability or the merchant's liability to Citrus.

## 11.4 Fixed Plus Percentage Mode

The merchant owes both the plan-based fixed charge and percentage-derived fee lines. Both appear on a unified platform billing invoice and share payment, reminder, reconciliation, and audit infrastructure.

## 11.5 Platform Billing Settings

`platform_billing_settings` stores global behaviour, not duplicate plan prices:

```text
active_billing_mode
default_currency
free_period_days
read_only_grace_days
overdue_reminder_days
suspension_after_days
billing_periods_enabled
default_billing_period
percentage_fee_rate
percentage_fee_basis
extra_branch_charge_minimum
extra_branch_charge_maximum
preferred_personnel_fee_settings
mpesa_payment_enabled
subscription_payment_recovery_enabled
sms_billing_enabled
promotion_engine_enabled
trial_offer_engine_enabled
high_value_payout_threshold
referral_product_integration_enabled
referral_event_retry_policy
referral_attribution_pending_timeout_days
```

Every change records actor, role, old value, new value, reason, effective dates, and timestamps. Fixed and percentage components are derived exclusively from `active_billing_mode`; independent component activation booleans and duplicated fixed plan amounts are forbidden.

## 11.6 Plan-Price Source of Truth

```text
platform_billing_settings  → global behaviour and defaults
subscription_plans         → plan identity, limits, positioning
subscription_plan_prices   → authoritative versioned amount by plan, period, currency, and effective date
```

No plan price is duplicated in settings. A scheduled price creates a new version; active rows are never overwritten. Issued invoices preserve their captured price.

## 11.7 No Mid-Cycle Proration

Plan upgrades, downgrades, billing-period changes, and extra-branch charge changes take effect at the next cycle. The current issued invoice is unchanged. Merchant plan changes and Super Administrator price changes are stored separately:

- `scheduled_plan_changes` for merchant-selected plan/period changes;
- `subscription_plan_prices` for versioned platform price changes.

## 11.8 Shared Overdue Escalation

Subscription, percentage-fee, SMS, extra-branch, storage, and approved add-on invoices use one escalation engine:

```text
grace_days
reminder_days
suspension_after_days
invoice_type
merchant_id
invoice_id
billing_status_change
audit_reason
notification_sent_at
```

Default reminders are day 3, day 7, and day 14; all remain configurable.

## 11.9 Real-Time Merchant Pricing Visibility

Merchant Administrator sees current plan, period, billing mode, price, fee rate/tier where applicable, discounts, credits, extra branches, SMS/add-ons, current invoice, next invoice estimate/date, outstanding balance, trial/grace/suspension state, and scheduled price/plan changes. The same information appears on landing, billing dashboard, subscription, and plan-management screens.

---

# 12. Subscription Plans and Entitlements

Plans are enforced server-side. A release class says whether a capability is implemented; an entitlement says who may use an implemented capability. Those concepts are never interchangeable.

## 12.1 Authoritative Capability Names

The routing capabilities are separated as follows:

- `routing.basic`: Point A-to-B and single-route distance, duration, ETA, map display, and manual multi-stop sequencing. Implemented at launch.
- `routing.multi_vehicle_optimization`: algorithmic allocation and balancing across multiple vehicles/stops with capacity, time-window, driver-hour, and re-optimization constraints. Post-MVP and absent from all launch plan promises.

The core business-customer API and outbound webhook capability is a **Launch Entitlement**. It is implemented and production-ready at launch, sold as an add-on on Growth, and included on Pro Branch and Multi-Branch. Starter has no business API entitlement.

The core Multi-Branch operating model is a **Launch Entitlement** and cannot be deferred while the Multi-Branch plan is purchasable.

## 12.2 Entitlement Enforcement

Every protected capability evaluates:

- active scope version and capability ID;
- merchant plan and effective-dated entitlement;
- add-on activation where applicable;
- current usage and limit;
- scheduled downgrade impact;
- operational and billing restrictions;
- actor permission and separation-of-duty rule;
- tenant, branch, organization, and own scope;
- feature release class and deployment status.

A hidden button is not enforcement. `architecture_ready` and `post_mvp` capabilities are not routable or sellable. Entitlement denial returns `403 entitlement_required`; usage-limit denial returns `403 entitlement_limit_reached` with safe upgrade/remediation guidance.

## 12.3 Starter Plan

### Positioning

Starter supports a viable small courier operation without converting the Merchant Administrator into an unrestricted operational superuser.

### Recommended Limits

```text
Branches: 1
Merchant Administrators: 1 or more within owner-governance policy
Staff users: up to 3
Delivery Personnel: up to 3
Vehicles: up to 3
Sender/customer records: unlimited fair-use
Recipient records: unlimited fair-use
Delivery service types: up to 10
Delivery requests: unlimited fair-use
Invoices/receipts: unlimited fair-use
Storage: basic quota
Routing: routing.basic
Business API/webhooks: not entitled
Reports: basic daily/weekly
```

### Included Operating Model

- one Merchant Administrator;
- up to three staff users assignable to the existing HR, Dispatch, and Finance role profiles;
- up to three Delivery Personnel;
- core staff administration: invitation, branch membership, essential eligibility, availability, suspension, and deactivation;
- merchant/branch setup, catalogue, sender/recipient records, delivery request, address validation, quote, assignment, PWA, tracking, proof, invoice, integrated payment, basic offline/COD recording, receipt generation after clearance, notifications, basic reporting, and minimal audit;
- integrated gateway payments may validate automatically;
- offline and COD validation requires a Finance checker who is not the maker for the same record.

Advanced compensation rules, document automation, full cash-up approval, period locks, sensitive exports, advanced refund/dispute governance, API/webhooks, and multiple branches require a higher plan or approved add-on.

### Starter Edge Cases

A solo merchant may hold Merchant Administrator plus Dispatch grants and use integrated payments. Maker/checker actions remain unavailable until a second compatible user exists. Offline payments remain `pending_validation`; the system never falsely marks them cleared. The Merchant Administrator appoints the initial HR user, and HR creates operational users. Emergency access suspension remains owner-controlled but does not replace HR employment lifecycle.

## 12.4 Growth Plan

Growth includes Starter plus broader staff lifecycle, compensation basics, standard Finance/Dispatch structure, standard reports, basic cash-up, and optional business API/webhooks add-on.

Recommended limits remain configurable, typically one branch, 8–12 staff, up to 10 Delivery Personnel, and up to 10 vehicles. Growth supports API clients and outbound webhooks only when the launch add-on is active.

## 12.5 Pro Branch Plan

Pro Branch includes Growth plus:

- advanced Finance, gateway reconciliation, COD reconciliation, refund/dispute controls, period locks, and controlled reopen;
- full branch audit, sensitive exports, advanced compensation and payout segregation;
- higher storage and retention;
- core business API clients and outbound webhooks;
- `routing.basic` with advanced manual route controls and higher limits;
- no full multi-vehicle optimization at launch.

Any plan or UI copy saying “route optimization included” is replaced with “advanced basic routing, ETA, map display, and manual multi-stop sequencing.”

## 12.6 Multi-Branch Plan

The Multi-Branch plan is launch-implemented and includes:

- multiple branch creation and lifecycle;
- branch-scoped authorization and explicit cross-branch grants;
- central branch switcher and consolidated dashboard;
- branch comparisons and merchant-wide reporting with preserved branch attribution;
- branch-specific cash-up and lock state;
- centralized subscription and extra-branch billing;
- controlled cross-branch role grants, staff/fleet reassignment, and inter-branch delivery/custody rules;
- centralized API/webhook governance;
- downgrade remediation when branch count exceeds the target plan;
- no silent branch deletion or cross-branch data visibility.

Phase 7 may add advanced forecasting, accounting consolidation, and multi-vehicle optimization, but not the controls required to operate the sold plan.

## 12.7 Authoritative Entitlement Matrix

| Capability | Starter | Growth | Pro Branch | Multi-Branch |
|---|---:|---:|---:|---:|
| Branches | 1 | 1 | 1 | 2 included + extras |
| Core staff users | Up to 3 | 8–12 configurable | Up to 25 configurable | 50+ configurable |
| Delivery Personnel / vehicles | 3 / 3 | 10 / 10 | 25 / 25 | 50+ / 50+ |
| Core delivery workflow | Yes | Yes | Yes | Yes |
| `routing.basic` | Basic | Standard | Advanced limits | Advanced multi-branch limits |
| `routing.multi_vehicle_optimization` | No; Post-MVP | No; Post-MVP | No; Post-MVP | No; Post-MVP |
| Core business API/webhooks | No | Launch add-on | Included | Included |
| Core HR lifecycle | Yes | Yes | Yes | Yes |
| Advanced HR/compensation | No | Basic | Advanced | Advanced |
| Finance validation | Basic viable model | Standard | Advanced | Advanced multi-branch |
| Cash-up | Basic | Submit + terminal approval | Full + locks/reopen | Full multi-branch |
| Refund/dispute governance | Basic support handoff | Limited | Full | Full |
| Sensitive finance export | No | Limited | Yes | Yes |
| Audit | Minimal | Basic | Full branch | Full multi-branch |
| Branch comparison | No | No | No | Yes |

## 12.8 Downgrade, Sales, and Contract Edge Cases

- Downgrade below current branch, user, vehicle, storage, or integration usage creates a scheduled remediation workflow; history is preserved.
- Existing sales material or contracts promising full optimization require commercial remediation and explicit customer communication.
- Feature flags cannot expose unimplemented capabilities.
- Marketing, plan seed data, UI copy, OpenAPI, SDKs, entitlement keys, roadmap, and release notes are generated or validated against one release manifest.

# 13. Promotions, Discounts, Free Periods, and Trial Offers

## 13.1 Promotional Discounts

Super Administrator can create percentage or fixed discounts with:

- name/code;
- description;
- amount/type;
- target scope;
- plan/period/currency filters;
- merchant/merchant-group/registration-window/geography filters;
- start/end dates;
- maximum uses;
- per-merchant limit;
- new-merchant-only rule;
- stacking rule;
- status;
- preview count;
- audit.

Targeting is structured in `promotional_discount_targets`; free-text scope strings are not authoritative.

## 13.2 Free-Period Offers

Configurable by plan, merchant target, registration dates, geography, campaign, or approved cohort. Trial starts at Merchant Administrator account creation. Completing setup early does not shorten the configured trial.

## 13.3 Discount Application

Discounts are evaluated server-side, snapshotted on billing invoices, and never retroactively alter issued invoices except through explicit credit/adjustment records.

---

# 14. Merchant Operational Status, Billing Status, Trial, Overdue, Grace, Suspension, and Recovery

## 14.1 Separate Status Domains

```text
merchants.status:
pending_setup | active | suspended | deactivated

merchants.billing_status:
trialing | active_subscription | read_only_grace | overdue | suspended_billing
```

Operational and billing restrictions are independently evaluated. Billing payment never clears fraud, legal, security, compliance, manual, or deactivation restrictions.

## 14.2 Authoritative Billing Lifecycle

```text
trialing → active_subscription | read_only_grace
read_only_grace → active_subscription | suspended_billing
active_subscription → overdue
overdue → active_subscription | suspended_billing
suspended_billing → active_subscription
```

`trialing → active_subscription` requires sufficient cleared and reconciled payment. `trialing → read_only_grace` occurs at trial expiry without sufficient settlement. `active_subscription → overdue` occurs after a blocking platform invoice passes its due date. `overdue → suspended_billing` occurs after the configured suspension threshold. Recovery requires all recovery-blocking liabilities to be settled or formally resolved.

## 14.3 Shared Restricted-Mode Allowlist

`read_only_grace` and `suspended_billing` use one centrally defined backend allowlist. They differ only in explanatory wording, age, notification cadence, and escalation severity.

Allowed:

- merchant-user authentication and logout;
- Magic Link recovery;
- billing-recovery landing;
- reason and status visibility;
- platform invoice and payment-attempt viewing;
- M-Pesa recovery initiation and result viewing;
- minimum merchant profile and billing phone update through controlled recovery;
- permitted read-only historical deliveries, invoices, receipts, proofs, and reports;
- support access;
- approved billing-dispute resolution;
- system reconciliation of pre-existing payment attempts and callbacks.

Blocked:

- new requests, quotes, assignments, route mutations, or delivery-state mutations;
- new customer invoices, payment records, refunds, or mutable disputes;
- staff, branch, service, vehicle, eligibility, compensation, payout, and cash-up mutations;
- new unrestricted exports;
- business API delivery creation and webhook-triggered merchant mutations;
- any mutation not required for recovery or integrity of a pre-existing financial transaction.

Queued jobs, callbacks, API calls, open browser sessions, and webhooks re-check billing status at execution time. No frontend state grants an exception.

## 14.4 Recovery Rules and Edge Cases

A successful STK prompt does not recover the merchant; verified settlement does. Partial payment does not recover unless a configured arrangement permits it. Paying one of several blocking invoices does not recover while another remains. Duplicate callbacks are idempotent. A reversed payment may create a new delinquency adjustment. Billing may recover while operational status remains suspended or deactivated.

## 14.5 Manual, Fraud, Security, Legal, and Compliance Suspension

Super Administrator or authorized Security and Risk action remains required. Payment has no authority over these restrictions. Every suspension records reason, evidence, actor, review status, notifications, and audit.

---

# 15. Merchant Subscription M-Pesa Payment and Automated Account Recovery

## 15.1 Eligible Platform-Billing Payers

The authoritative permission is `platform_billing.payment.initiate`. Default grants are:

- Merchant Administrator: permitted merchant-wide;
- Merchant Finance: permitted within authorized merchant/branch billing scope;
- Branch Manager: optionally permitted by explicit merchant policy and only for merchant platform invoices;
- Dispatch & Customer Operations: denied by default and enabled only through a separately approved merchant policy with audit and no access to platform billing configuration;
- HR, Delivery Personnel, Support, Audit, Sender, Recipient Access, Business Customer members, and machine clients: denied;
- Platform Owner and other internal Citrus roles: cannot ordinarily pay on behalf of a merchant.

A UI shortcut never grants authority. Every attempt evaluates the effective permission, merchant context, invoice payability, billing restriction, amount policy, and maker/checker rule. Policy changes are effective-dated and audited.

## 15.2 Shared Invoice, User-Specific Attempt

The invoice belongs to the merchant. Each attempt records initiating user, role, branch context, merchant, invoice, checkout request, receipt, amount, status, timestamp, IP, user agent, and idempotency key.

## 15.3 Methods

Launch-first: M-Pesa STK Push. Fallback: PayBill/Till with invoice reference.

## 15.4 Platform Invoice Statuses

```text
draft
issued
partially_paid
paid
overdue
reconciliation_required
voided
cancelled
```

## 15.5 Platform Payment Attempt State

```text
created
pending
provider_request_sent
customer_cancelled
timeout
successful
failed
expired
callback_received
validated
rejected
duplicate
unknown
reconciliation_required
applied_to_invoice
```

A payment attempt describes collection and application only. A later refund, reversal, chargeback, or external adjustment is a separate financial aggregate linked to the successful attempt/payment; `external_refund_recorded_in_separate_adjustment` is not an attempt state.

## 15.6 Validation

Validate unique receipt, positive amount, payable invoice, tenant match, partial-payment rule, callback security, timestamp, reference, phone, non-duplication, and unapplied status. Successful validation applies payment and updates billing status.

## 15.7 Double-Payment Protection

- short invoice payment lock;
- active-attempt indicator visible to eligible users;
- idempotency key;
- unique receipt constraint;
- invoice balance re-check before apply;
- overpayment route to reconciliation/credit policy;
- no silent loss or duplicate application.

## 15.8 Automatic Reactivation

Only:

```text
billing_status = suspended_billing
suspension_reason = unpaid_subscription
invoice fully paid
payment validated
```

Then clear billing suspension timestamps, invalidate stale caches, refresh session/permissions, audit, notify, and redirect to success/normal dashboard.

## 15.9 Per-Role UX

- Merchant Administrator: full billing dashboard and plan controls.
- Branch Manager: branch-context payment shortcut and shared invoice status.
- Finance: detailed attempts, reconciliation, and invoice lines.
- Dispatch: simplified “keep operations active” payment flow.
- Suspended merchant: dedicated payment-required recovery screen.

---

# 16. Merchant Self-Registration and First-Time Setup

## 16.1 Binding Rule

No Super Administrator, internal staff user, branch user, API call, support user, or migration helper may ordinarily create a production merchant and first Merchant Administrator outside the public self-registration domain service.

## 16.2 Public Registration Fields

- owner first/last/display name;
- work email;
- phone;
- merchant/business name;
- business category;
- operating country/city;
- expected branch count;
- expected vehicle/personnel scale;
- accepted terms/privacy/prohibited-goods declaration;
- consent/communication preferences;
- referral/campaign code where applicable;
- anti-abuse evidence such as CAPTCHA/device/rate-limit signals.

KYC documents are not a prerequisite to ordinary account creation unless a separately approved regulatory requirement applies. Optional business documents may be collected later for payment/integration features.

## 16.3 Initial State

```text
merchants.status = pending_setup
merchants.billing_status = trialing
merchant owner identity_status = pending_verification
merchant owner membership_status = active_pending_verification
merchant owner registration_status = submitted
merchant owner invitation_id = null
trial_started_at = merchant creation timestamp
```

## 16.4 Security

- rate limiting;
- CAPTCHA/risk scoring;
- normalized email/phone/business fields;
- duplicate-business warnings;
- disposable-email rules where configured;
- Magic Link expiry/single use;
- no plaintext secrets;
- registration audit;
- suspicious-trial and device pattern monitoring;
- no cross-tenant leakage in duplicate warnings.

## 16.5 Forbidden Super Administrator Routes

No route may create a merchant, first owner, branch, or operational setup under Super Administrator authority. Governance routes may view, note, suspend, reactivate, deactivate, or manage billing/risk after creation.

---

# 17. Personnel Compensation Model Management

## 17.1 Compensation Models

```text
commission_only
salary_plus_commission
salary_only
```

`employment_type` remains separate from `compensation_model`.

## 17.2 Commission-Only

Configuration may include:

- percentage or fixed amount;
- basis: cleared delivery revenue, service subtotal, item line, distance, stop, successful delivery, preferred fee, COD collection, or configured hybrid;
- service/vehicle/zone modifiers;
- exclusions for tax, pass-through charges, discounts, refunds, failed delivery, return, or platform fee;
- effective dates;
- caps/floors;
- approval and acknowledgement.

Commission becomes earned only at the configured event, normally successful delivery plus cleared payment, and is adjusted traceably for refund/reversal.

## 17.3 Salary Plus Commission

Stores salary terms and commission terms independently. Salary accrues by pay period; commission accrues by eligible delivery event. Payout statement shows both components and adjustments.

## 17.4 Salary-Only

Stores salary amount, pay period, effective dates, and ledger accrual. Delivery performance remains reportable but does not generate commission unless a later effective compensation plan changes.

## 17.5 HR Setup Flow

1. Select personnel.
2. Verify branch, role, employment status, and eligibility.
3. Select compensation model.
4. Configure salary/commission rules.
5. Define effective date.
6. Preview examples.
7. Enter reason.
8. Submit for approval where required.
9. Personnel receives terms and acknowledges.
10. System activates effective version and preserves prior version.

Backdating is high risk, permission-gated, reason-required, recalculated through adjustment entries, and audited.

## 17.6 Compensation Statuses

```text
draft
pending_finance_review
pending_merchant_admin_approval
pending_personnel_acknowledgement
scheduled
active
superseded
suspended
ended
rejected
```


## 17.6A Compensation Approval Ownership

HR prepares compensation plans. Finance verifies arithmetic, currency, liability, commission basis, caps, accounting classification, and approved compensation bands. Finance may approve standard plans within configured bands. Merchant Administrator approval is mandatory for new fixed-salary obligations, above-threshold increases/rates, backdating, related-party compensation, cross-branch exceptions, guarantees, and other high-value or off-policy terms. Personnel acknowledgment confirms receipt and understanding but is not employer approval.

No user may create and approve their own plan. HR personnel cannot manage their own compensation. Overlapping effective plans are blocked. Backdating creates append-only adjustment entries and never rewrites prior ledgers. A plan that produced ledger entries cannot be deleted.

## 17.7 Earnings and Ledgers

Commission and salary ledgers are append-only. Every entry links to source delivery/invoice/payment/pay period/adjustment. Original entries are never overwritten.

## 17.8 Payout Run Workflow and Segregation of Duties

1. HR prepares eligible compensation items and submits a payout proposal.
2. Finance reviews financial validity, exclusions, locked-period implications, duplicate liability, and funding evidence.
3. Merchant Administrator or an independently authorized approver approves configured high-value or exceptional runs.
4. A Finance settlement operator who did not prepare or approve the run executes settlement.
5. Each payout item records its own attempt, provider/manual reference, state, evidence, and reconciliation result.
6. The run becomes fully paid only when every included item is completed or explicitly removed through an approved adjustment. Partial success creates `partially_settled`; it never marks failed items paid.
7. Failed items are retried through idempotent item commands or moved to a new run with an immutable link. The original item history remains intact.

The same person cannot prepare, approve, and settle the same threshold-controlled run. A Finance user cannot approve their own preparation where maker/checker applies. Provider callbacks are idempotent and reconciled per payout item. Concurrent settlement returns `409 payout_run_state_conflict`. A duplicate provider reference returns `409 duplicate_settlement_reference`. Locked-period liabilities are settled through current-period payment records without altering the earned ledger. Reversal or clawback uses append-only adjustments and cannot delete the original payout.

## 17.9 Personnel My Earnings

Tabs:

- Overview;
- Commission;
- Salary;
- Compensation Terms;
- Payout History;
- Earnings Queries;
- Statements.

Personnel sees own values only. Statements are private signed downloads.

## 17.10 Earnings Queries

Types include missing commission, reversed commission, incorrect rate, salary amount, payout status, adjustment, missing delivery, refund impact, or other. HR/Finance respond according to ownership; all actions are auditable.

## 17.11 Compensation Permissions

- HR: create/edit draft plans, submit changes, prepare/submit payout runs, cancel draft.
- Finance: view liabilities, verify, standard-approve, reject, adjust, mark paid.
- Merchant Administrator: view all tenant compensation; approve/reject high-value runs when required.
- Personnel: own earnings, own statements, own queries.
- Audit: branch-scoped read/export with masking.
- Branch Manager: read-only personnel availability/eligibility and permitted performance, not pay editing.

---

# 18. Branch-Day Operations, Cash-Up Revision, Reconciliation, and Financial-Period Locks

The word “reopen” is not used without a domain qualifier. Three different operations exist and have separate states, permissions, records, routes, audit events, and lock effects:

```text
reopen_operational_branch_day
reopen_cash_up_revision
reopen_financial_period
```

## 18.1 Operational Branch Day

An operational branch day controls whether a branch can accept new requests and dispatch work. Branch Manager may open, pause, close, and where policy permits reopen the operational day. This action never unlocks financial records.

Reopening across a timezone boundary uses the original branch-day local date and an explicit effective window.

## 18.2 Cash-Up Definition and Ownership

Cash-up reconciles expected against recorded/cleared money for a branch day, including cash, COD, M-Pesa, bank, card, vouchers, credit, refunds, and adjustments.

Ownership is:

- Dispatch records ordinary customer payments; Delivery Personnel records COD collection/handover evidence only.
- Finance or an automated validated gateway service validates/reconciles.
- Branch Manager or an authorized operations maker prepares and submits cash-up.
- Finance requests correction, approves, terminally rejects, and locks according to policy.
- The authorized maker creates a new revision after correction request.
- Merchant Audit reads masked immutable history.

## 18.3 Cash-Up States and Revisions

```text
draft
submitted
correction_requested
resubmitted
approved
locked
rejected_terminal
```

Authoritative transitions:

```text
draft --submit--> submitted
submitted --request_correction--> correction_requested
correction_requested --create_revision_and_resubmit--> resubmitted
resubmitted --request_correction--> correction_requested
submitted|resubmitted --approve--> approved
approved --lock--> locked
submitted|resubmitted --reject_terminal--> rejected_terminal
```

A submitted, approved, or locked snapshot is immutable. Correction creates a new revision linked to the prior snapshot. `rejected_terminal` is reserved for duplicate, wrong branch/day, fabricated, impossible-source, or recreate-required records.

Only one active cash-up exists per branch day. Late provider callbacks create reconciliation adjustments and may trigger a correction request.

## 18.4 Financial-Period Reopen Requests

Financial-period reopening uses a dedicated aggregate:

```text
financial_period_reopen_requests
```

The request records period, branch/merchant scope, reason, affected record types, intended mutations, maker, approvals, threshold/policy snapshot, expiry, execution window, legal/security holds, execution result, and audit evidence.

Commands are:

```text
create request
approve
reject
execute
cancel before execution
close and re-lock
```

Finance executes. Merchant Administrator approval applies only where policy or threshold requires it. High-risk requests may require a second Finance approver or platform oversight.

Execution before approval returns `409 approval_required`. Expired authorization returns `409 reopen_authorization_expired`. Overlapping requests are rejected or merged under explicit policy. A new legal hold or lock arising after approval blocks execution and requires re-review. Execution is transactionally atomic or records explicit item-level partial failure with compensating recovery; silent partial reopen is forbidden.

## 18.5 PeriodLockService

All financial mutations consult one central `PeriodLockService`. It evaluates merchant, branch, period, record type, actor, permission, reopen authorization, execution window, legal/security holds, and audit reason. Scattered controller checks are forbidden.

Operational branch-day reopen does not unlock finance. Cash-up revision cannot mutate a locked period without an executed financial reopen. Financial reopen does not reopen operational intake.

## 18.6 Required Error Handling and Edge Cases

- Wrong-domain reopen action: `409 domain_action_mismatch`.
- Locked period: `423 financial_period_locked`.
- Missing required approval: `409 approval_required`.
- Self-approval where prohibited: `403 separation_of_duty_violation`.
- Duplicate execution: idempotent existing result or `409 already_executed` for a conflicting key.
- New hold after approval: `423 blocking_hold_active` and approval invalidation.
- Timezone/date ambiguity: explicit local date, timezone, and UTC execution timestamps are mandatory.

# 19. Notifications, Communications, and Messaging

## 19.1 Channels

Courier by Citrus supports:

- email;
- SMS;
- in-app notifications;
- WhatsApp Business integration where enabled;
- push notifications in a later phase or PWA-capable implementation;
- webhooks for entitled business customers and integrations.

## 19.2 Delivery Events

Notifications may be generated for:

1. account invitation or Magic Link;
2. merchant registration/setup;
3. plan/trial/grace/overdue/suspension events;
4. platform invoice issue/payment/reconciliation/reactivation;
5. delivery request created;
6. address correction required;
7. quote generated, expiring, accepted, or rejected;
8. payment request, payment received, validation failure, or balance due;
9. delivery confirmed;
10. assignment made, changed, or rejected;
11. driver en route to pickup;
12. arrival at pickup;
13. pickup completed;
14. in transit;
15. delayed or exception raised;
16. arriving soon;
17. arrival at destination;
18. OTP issued;
19. delivery completed;
20. delivery failed;
21. redelivery scheduled;
22. return initiated/completed;
23. invoice/receipt issued;
24. refund/dispute status;
25. support ticket updates;
26. payout/earnings statement events;
27. expiring staff or vehicle document;
28. cash-up submitted/approved/rejected;
29. financial period lock/reopen;
30. API/webhook failure.

## 19.3 Templates

Templates are versioned, localized-ready, channel-specific, role-aware, merchant-branded where permitted, and protect sensitive data. SMS messages avoid unnecessary PII and include secure links rather than full details.

## 19.4 Bulk SMS

Entitled users may send in-platform bulk SMS under controlled scope:

- Merchant/branch campaigns by authorized merchant roles;
- Delivery Personnel only to their own served-customer list where enabled;
- recipients selected within permitted scope;
- consent enforcement for promotional content;
- cost preview;
- branch billing allocation;
- rate limits;
- sender identity control;
- delivery result logging;
- audit.

Personnel sees this notice before send:

> SMS charges for this message will be billed to your branch together with the Courier by Citrus platform invoice. Continue?

Raw contact export is not required for sending.

## 19.5 Communication Privacy

The platform can proxy calls/messages or reveal masked contacts only when the workflow requires it. Access and reveal actions are logged. Communication retention follows policy.

---

# 20. Google Maps and Location Intelligence Scope

Google Maps Platform or an approved equivalent provides map display, places, geocoding, address validation, basic point-to-point/multi-stop routing, and ETA. Full multi-vehicle route optimization is Post-MVP and has no launch production endpoint. Integration is abstracted so the platform can support additional providers later.

## 20.1 Recommended Services

| Requirement | Google service |
|---|---|
| Web map | Maps JavaScript API |
| Address autocomplete | Places API / Place Autocomplete |
| Address to coordinates | Geocoding API |
| Coordinates to address | Reverse Geocoding |
| Address validation | Address Validation API |
| Point A-to-B route | Routes API |
| Distance and ETA | Routes API |
| Traffic-aware estimate | Routes API where available |
| Multi-stop optimization | Route Optimization API |
| Markers/polylines | Maps JavaScript API |

## 20.2 Address Search and Autocomplete

Requirements:

1. suggest as user types;
2. bias by merchant country/city/branch;
3. restrict to supported regions where needed;
4. store Place ID;
5. store latitude/longitude;
6. store formatted and normalized address;
7. preserve manual landmarks, apartment/floor/gate/access notes;
8. allow map pin correction;
9. validate incomplete/ambiguous address before confirmation;
10. show which fields changed after validation.

## 20.3 Address Validation

The platform records validation confidence, missing components, suggested correction, user confirmation, and manual-review flag. Landmark-first markets must support pinned coordinates and free-form directions rather than forcing unsuitable structured-address requirements.

## 20.4 Route Calculation

The platform calculates and snapshots:

- origin/destination/waypoints;
- distance;
- expected duration;
- traffic-aware duration;
- ETA;
- route polyline;
- alternatives where allowed;
- toll/pass-through metadata where available;
- provider response/version/timestamp;
- inputs used for quote.

Historical quote pricing never silently recalculates from a later route response.

## 20.5 Routing Capability Boundary

Launch implements `routing.basic`:

- point-to-point route calculation;
- traffic-aware ETA where available;
- route display and polyline;
- distance/time matrix where required for eligibility or display;
- manual stop ordering;
- optional provider-supported ordering for one route/vehicle only when explicitly contracted and not represented as fleet optimization.

Full multi-vehicle, multi-constraint, fleet-wide route optimization is classified `Post-MVP`. It has no launch production route, entitlement, screen, worker, or marketing promise. Architecture may reserve provider adapters and data structures, but reserved code cannot be reachable in production.

Provider outage returns a safe route-unavailable state and allows manual dispatch where policy permits. A stale ETA is labelled with calculation time. Impossible constraints return `422 route_constraints_unsatisfied`. Provider quota exhaustion returns `503 maps_dependency_unavailable` and triggers operational alerts. Basic route calculation must never auto-assign Delivery Personnel or vehicles.

## 20.6 Maps Cost Control

- lazy-load maps;
- debounce autocomplete;
- session tokens where supported;
- cache permissible route/address results according to provider terms;
- avoid unnecessary recalculation;
- quotas and billing alerts;
- per-merchant usage telemetry;
- rate limits;
- usage anomaly alerts;
- plan entitlement for expensive optimization.

## 20.7 API Key Security

1. separate browser and server keys;
2. browser key restricted by domain;
3. server key restricted by IP/environment where supported;
4. restrict keys to required APIs;
5. no unrestricted server key in frontend;
6. no keys in source maps or logs;
7. rotate carefully;
8. disable unused APIs;
9. monitor and alert;
10. use secret manager/environment injection.

---

# 21. Other Integrations

## 21.1 Customer Payment Gateways

Supported connectors may include:

- M-Pesa Daraja;
- Flutterwave;
- Paystack;
- Stripe;
- PayPal;
- local bank/card processors;
- manual bank transfer reconciliation.

Each connector has merchant-scoped configuration, encrypted secret storage, webhook verification, idempotency, retry strategy, reconciliation, and audit. Merchant secrets are never returned to browser clients.

## 21.2 Platform Subscription M-Pesa

Citrus Labs Limited controls the platform M-Pesa connector used for merchant subscription invoices. Merchant staff sees only payment UX and permitted transaction metadata.

## 21.3 SMS/WhatsApp

Possible providers include Africa's Talking, Twilio, Termii, local SMS aggregators, and WhatsApp Business API providers. The platform uses a provider abstraction, delivery-status callbacks, cost tracking, retry, opt-out/consent, and template governance.

## 21.4 Email

Possible providers: Amazon SES, Mailgun, SendGrid, Postmark. Requirements include domain authentication, bounce/complaint handling, template versioning, suppression handling, retry, and event logs.

## 21.5 File Storage

Possible providers: AWS S3, Cloudflare R2, Google Cloud Storage, DigitalOcean Spaces, or compatible object storage. Files are private by default and delivered through authorized expiring access.

## 21.6 Accounting and ERP

Post-MVP or plan-entitled connectors may include QuickBooks, Xero, Zoho Books, Sage, or structured CSV. Integration covers customer invoices, cleared payments, refunds, taxes, platform billing, and reconciliation, with mapping and error logs.

## 21.7 Launch Business API and Webhooks

Core business-customer API and webhooks are implemented launch capabilities for plans carrying the entitlement; they are not architecture-only placeholders. The launch package includes organization-owned API clients, one-time credential issuance, rotation/revocation, explicit scopes, idempotent request creation, request/quote/tracking reads, webhook subscriptions, endpoint verification, detached signatures, retries, dead letters, usage/quota logs, and audit.

Advanced connectors, bulk event families, very high quotas, and partner-specific ERP adapters can remain Launch Add-on, Architecture-ready, or Post-MVP only when separately named in the release manifest.

Generic and business-specific credential tables do not coexist as independent authorities. Canonical business integration records are defined in Section 25. Platform/system integrations use a separate system-identity domain. A credential authenticates an identity but is not itself the identity.

Credential creation returns plaintext secret material once. Retrieval later returns metadata only. Rotation supports a bounded overlap window. Revocation is immediate. A suspended business organization disables human and machine access without deleting history. Invalid scope returns `403 integration_scope_required`; reused idempotency key with a different payload returns `409 idempotency_conflict`; webhook verification failure leaves the subscription inactive.

## 21.9 Centralized Citrus Refer & Earn Platform Integration

Courier integrates with the centralized Refer & Earn platform under the complete normative contract in Section 46. Courier owns product-native campaign entry points, registration facts, subscription/payment/activity facts, local attribution snapshots, outbox delivery, and integration monitoring. The central platform owns campaign-rule evaluation, canonical attribution, qualification, rewards, payout, fraud, support, and cross-product reporting.

Product-specific campaign configuration is authored and activated in the central platform by central campaign roles. Courier may display centrally supplied campaign content/configuration and may manage only Courier-owned entry-point placement or product metadata. Courier Platform Owner cannot independently change reward formulas or central campaign versions.

Events use neutral factual names, `_minor` money fields, detached Ed25519 signatures, transactional outbox delivery, idempotent processing, and privacy minimization. Registration continues when the central dependency is unavailable; attribution remains pending until reconciliation.

## 21.8 Future Integrations

- barcode/QR scanners;
- warehouse management;
- e-commerce platforms;
- insurer/claims services;
- telematics/vehicle trackers;
- fuel-card providers;
- identity/KYC providers where legally necessary;
- tax authority e-invoicing where required.

---

# 22. Reports, Dashboards, Analytics, and Exports

## 22.1 Merchant Administrator Reports

- merchant-wide delivery volume;
- completed/failed/cancelled/returned/redelivered;
- revenue and outstanding invoices;
- payment-method mix;
- COD exposure;
- refund/dispute amounts;
- branch comparisons;
- service/zone/customer performance;
- driver/rider performance;
- vehicle utilization;
- route efficiency;
- average pickup and delivery time;
- SLA compliance;
- failure/delay reasons;
- customer retention/activity;
- salary/commission liabilities;
- payout status/history;
- platform billing and percentage fees;
- subscription/SMS/add-ons;
- audit/high-risk summary.

## 22.2 Branch Manager Reports

- branch deliveries by state;
- active backlog;
- service/zone demand;
- branch revenue;
- unvalidated payments;
- COD and cash-up state;
- staff availability/performance;
- vehicle availability/utilization;
- failures/returns;
- day-close;
- customer activity;
- SLA and queue/dispatch delay.

## 22.3 HR Reports

- staff roster;
- invitation/activation state;
- employment/role/branch history;
- availability/absence;
- qualifications/eligibility;
- document expiry;
- compensation model coverage;
- salary/commission liability;
- payout preparation;
- earnings-query trends.

## 22.4 Finance Reports

- invoice ageing;
- payment validation;
- gateway reconciliation;
- COD reconciliation;
- payment-method totals;
- refund/dispute;
- cash-up discrepancies;
- period locks;
- tax/VAT;
- credit accounts;
- payout runs;
- salary/commission liabilities;
- platform billing;
- sensitive export activity.

## 22.5 Dispatch Reports

- request-to-quote time;
- quote acceptance;
- time-to-assignment;
- unassigned jobs;
- assignment rejection;
- pickup delay;
- delivery delay;
- route group performance;
- exception reasons;
- customer-contact workload.

## 22.6 Personnel Reports

Delivery Personnel sees only own job counts, success/failure, time metrics, ratings where allowed, salary, commission, payout, and earnings statements.

## 22.7 Support Reports

- ticket volume;
- category;
- priority;
- SLA;
- resolution time;
- repeat complaints;
- delivery/payment/dispute correlations;
- satisfaction.

## 22.8 Audit Reports

- high-risk events;
- permission/role changes;
- customer contact reveals;
- prohibited export attempts;
- quote/payment/proof overrides;
- assignment/reassignment;
- locked-period mutation attempts;
- compensation/payout changes;
- file access;
- API/webhook/security events.

## 22.9 Super Administrator Reports

- total/active/trial/grace/suspended merchants;
- branches/users/vehicles/personnel;
- platform delivery volume;
- gross merchant-customer invoice value;
- platform revenue by fixed/percentage/add-on;
- subscription M-Pesa performance;
- reconciliation exceptions;
- API/Maps/SMS usage and cost;
- failed jobs/webhooks;
- registration abuse;
- suspicious activity;
- plan adoption/churn;
- system performance.

## 22.10 Export Governance

Exports require permission, scope, reason for sensitive reports, async generation, private storage, signed expiry, masking, download count, audit, and retention/deletion. Personnel contact export remains impossible.

---

# 23. Launch-Implemented Capability Modules

Every capability in this section is implemented and production-ready for the named release. Each capability carries exactly one release-class key in the release manifest:

```text
mandatory_mvp
launch_entitlement
launch_add_on
architecture_ready
post_mvp
```

Plan restriction does not make an implemented capability non-launch. `architecture_ready` means interfaces or extension points only and cannot be sold, enabled, routed, or represented as working.

## 23.1 Mandatory MVP Modules

- merchant self-registration and first Merchant Administrator;
- platform, merchant, sender, and recipient authentication/access boundaries;
- canonical identity, role, membership, employment, invitation, and emergency-suspension model;
- one-branch operation and branch-scoped authorization foundation;
- viable Starter staff model;
- delivery service catalogue, pricing, quote versions, and approvals;
- customer, recipient, address, goods, and request management;
- orthogonal state machines and command-specific transitions;
- Dispatch assignment, reassignment history, custody handover, fleet, and `routing.basic`;
- Delivery Personnel mobile-first PWA;
- tracking, ETA, proof, incidents, failure, return, and redelivery;
- merchant-customer invoices, integrated/offline/COD payments, receipts, refund/dispute foundations;
- merchant-to-Citrus subscription billing, overdue/grace/suspension/recovery;
- notifications, support, private files, audit foundation, dashboards, and baseline reports;
- Courier-native referral capture, local snapshot, transactional outbox, signed product events, pending attribution, and integration monitoring.

## 23.2 Launch Entitlement Modules

- core Multi-Branch controls and extra-branch billing;
- advanced compensation and payout governance for qualifying plans;
- financial-period locks and controlled reopen;
- enhanced reports, sensitive exports, and branch-level audit;
- core business-customer API clients, integration console, and outbound webhooks;
- higher routing/basic multi-stop limits and advanced manual sequencing;
- advanced Finance, refund, dispute, credit-account, and cash-up controls.

## 23.3 Launch Add-Ons

- approved message bundles;
- extra storage;
- extra branches;
- Growth business API/webhook add-on;
- separately approved guaranteed preferred-personnel reservation product, only where its commercial, operational, refund, and capacity rules are fully implemented.

## 23.4 Architecture-Ready Extensions

- future accounting/ERP connector interfaces beyond the implemented launch set;
- future event families and partner scopes;
- future warehouse/inventory boundaries;
- future native-app interfaces after PWA evidence.

Architecture-ready items do not appear in active production route discovery or launch marketing.

## 23.5 Post-MVP Capabilities

- full multi-vehicle route optimization;
- algorithmic capacity/driver-hours balancing and dynamic re-optimization;
- AI dispatch autonomy, AI ETA, and dynamic/surge pricing;
- native mobile apps;
- telematics and advanced fleet integrations;
- driver stored-value wallet, insurance underwriting, warehouse management, inventory, payroll statutory engine, and unapproved referral campaign types.

# 24. Recommended Technical Architecture

## 24.1 Backend

Recommended baseline:

- Laravel;
- PHP 8.3+ at implementation time, subject to supported production version;
- REST API under `/api/v1`;
- Laravel Sanctum or equivalent session/token architecture;
- Laravel Policies/Gates and explicit domain authorization services;
- PostgreSQL preferred; MySQL acceptable with equivalent constraints;
- Redis for cache, queues, locks, rate limiting, and realtime coordination;
- Laravel Horizon for queue monitoring;
- Laravel Scheduler;
- S3-compatible private storage;
- queue workers for notifications, exports, reconciliation, route optimization, files, reports;
- Meilisearch/Typesense/OpenSearch as later or plan-entitled search;
- OpenAPI specification;
- structured logs and telemetry;
- service-layer/domain-driven boundaries for billing, delivery, payments, compensation, locks, files, maps, and audit.

## 24.2 Frontend

- Vue.js or React.js;
- TypeScript;
- Vite;
- Tailwind CSS or Bootstrap 5; Tailwind recommended for design-system control;
- component-based architecture;
- accessible component library;
- shared typed API client;
- role-aware router/navigation;
- responsive CSS based on viewport, not device/user-agent detection;
- optional dark mode implemented through accessible design tokens;
- map components;
- form schema/validation;
- state management appropriate to framework;
- PWA support for Delivery Personnel;
- no jQuery;
- no secrets in client bundles/local storage.

## 24.3 Realtime

Options: Laravel Reverb/WebSockets, Pusher, Ably, Socket.io, or Firebase where justified. Realtime use:

- delivery status;
- driver location;
- dispatch board;
- assignment changes;
- payment/reconciliation status;
- notifications;
- platform billing recovery.

MVP may use 15–30 second polling for selected tracking views, but assignment and payment status should avoid unsafe stale state. Realtime is introduced where operational value justifies cost/complexity.

## 24.4 Domain Services

Required central services include:

- TenantContextService;
- BranchScopeService;
- PersonnelOwnScopeService;
- EntitlementService;
- BillingStatusGate;
- DeliveryStateMachine;
- QuotePricingService;
- DispatchEligibilityService;
- RouteService;
- CustomerPaymentService;
- CustomerPaymentReconciliationService;
- PlatformBillingService;
- PercentageFeeService;
- MpesaSubscriptionService;
- PeriodLockService;
- CompensationService;
- PayoutRunService;
- FileAccessService;
- AuditService;
- NotificationService;
- WebhookDeliveryService.

## 24.5 Infrastructure

- separate development/staging/production;
- managed database with backups;
- Redis;
- object storage;
- secret manager;
- CDN only for public/static assets;
- WAF/rate-limit layer where available;
- observability/APM;
- centralized logs with redaction;
- error tracking;
- queue/scheduler monitoring;
- uptime/health checks;
- deployment rollback;
- infrastructure-as-code recommended.

---

# 25. Canonical Multi-Tenant Data Model

All monetary amounts are integer minor units with currency. Dates/times are stored in UTC with explicit branch/user timezone context for display and local-business rules. Public identifiers are opaque UUIDs/ULIDs. Every entity is declared once. Later sections may reference or extend a declaration but may not create a second source of truth.

## 25.1 Global Human Identity and Authentication

```text
user_identities
identity_contacts
identity_authenticators
identity_sessions
identity_login_events
identity_security_events
identity_recovery_events
```

`user_identities` is the authenticating human subject. Contact identifiers, authenticators, sessions, memberships, role grants, employment, invitations, and emergency suspensions are separate.

## 25.2 Internal Citrus Platform Identity Domain

```text
platform_users
platform_roles
platform_permissions
platform_role_permissions
platform_role_grants
platform_user_mfa_methods
platform_user_sessions
platform_user_status_history
platform_user_security_events
platform_break_glass_requests
platform_break_glass_sessions
platform_privileged_action_approvals
platform_onboarding_modules
platform_role_onboarding_requirements
platform_user_onboarding_progress
```

A `platform_user` has no `merchant_id`, branch membership, or merchant employment. `platform_owner` is the one canonical highest Courier platform role; “Super Administrator” is its UI label. Platform grants are product- and scope-bound. Break-glass is a separate short-lived approved session, not an ordinary owner permission.

Constraints include last-active-owner protection, self-approval prohibition, MFA requirement, step-up rules, product-bound grants, effective dates, immediate revocation, and append-only privileged audit.

## 25.3 Merchant Human Membership, Employment, Roles, and Invitations

```text
merchant_user_memberships
merchant_roles
merchant_permissions
merchant_role_permissions
merchant_role_grants
branch_user_assignments
employment_records
staff_invitations
membership_status_history
employment_status_history
role_grant_history
emergency_security_suspensions
staff_profiles
```

The canonical migration/deprecation map is:

```text
users -> user_identities
merchant_users -> merchant_user_memberships + employment_records + merchant_role_grants
user_invitations -> staff_invitations or organization-specific invitation tables
user_status_history -> membership_status_history + employment_status_history + role_grant_history
employment_history -> employment_records + employment_status_history
```

Legacy structures may exist only as read-only compatibility views during a bounded migration. Application writes to legacy identity tables are prohibited.

## 25.4 Business-Customer Organizations and Human Role Grants

```text
sender_profiles
business_customer_organizations
business_customer_memberships
business_customer_roles
business_customer_role_grants
business_customer_invitations
business_customer_membership_status_history
```

Business-customer humans remain Goods Sender identities. Every membership query is constrained by `merchant_id` and `business_customer_organization_id`. The role keys are:

```text
business_customer_administrator
business_customer_operator
business_customer_finance_viewer
business_customer_integration_administrator
```

The final active organization administrator cannot be removed without an accepted replacement. Organization suspension disables human memberships and machine clients while preserving history.

## 25.5 Merchant and Branch

```text
merchants
merchant_profiles
merchant_settings
merchant_documents
merchant_branches
branch_profiles
branch_operating_hours
branch_calendar_exceptions
branch_days
service_zones
branch_service_zone_assignments
merchant_governance_notes
```

A branch is an operational entity and authorization boundary, never an account.

## 25.6 Personnel, Eligibility, Availability, and Fleet

```text
personnel_profiles
personnel_qualifications
personnel_documents
personnel_availability
personnel_shift_schedules
personnel_service_eligibility
personnel_vehicle_class_eligibility
vehicles
vehicle_types
vehicle_documents
vehicle_availability
vehicle_maintenance_records
vehicle_assignments
```

## 25.7 Customers, Contacts, Addresses, and Consent

```text
senders
recipients
customer_contacts
addresses
customer_addresses
frequent_routes
customer_consents
customer_communication_preferences
customer_risk_flags
```

## 25.8 Catalogue, Pricing, Quote, and Preference

```text
delivery_services
delivery_service_vehicle_classes
delivery_service_goods_rules
pricing_rules
pricing_rule_versions
quote_approval_thresholds
delivery_quotes
delivery_quote_lines
quote_approval_workflows
quote_approval_steps
preferred_personnel_fee_rules
preferred_personnel_requests
preferred_personnel_reservations
tax_rules
merchant_discounts
```

Preferred-personnel pricing is a quote/invoice line with configuration snapshot. `delivery_requests` does not store `preferred_personnel_id` as assignment evidence.

## 25.9 Delivery Operations and State History

```text
delivery_requests
delivery_items
delivery_stops
delivery_address_snapshots
delivery_assignments
delivery_assignment_events
delivery_custody_handovers
route_groups
route_group_stops
route_estimate_snapshots
driver_locations
delivery_incidents
delivery_failures
delivery_returns
delivery_redeliveries
delivery_tracking_tokens
delivery_ratings
request_state_history
quote_state_history
payment_requirement_state_history
assignment_state_history
custody_handover_state_history
operational_delivery_state_history
redelivery_state_history
dispute_state_history
refund_state_history
record_lifecycle_state_history
```

The authoritative request aggregate fields are explicit:

```text
id
merchant_id
branch_id
sender_id
service_id
source_channel
reference_number
request_status
payment_requirement_status
operational_status
record_lifecycle_status
active_quote_id nullable
active_preferred_personnel_request_id nullable
pickup_window_start nullable
pickup_window_end nullable
delivery_window_start nullable
delivery_window_end nullable
currency
aggregate_version
created_by_actor_type
created_by_actor_id
confirmed_at nullable
cancelled_at nullable
created_at
updated_at
deleted_at nullable
```

`current_assignment_status`, `current_redelivery_status`, `current_dispute_status`, `current_refund_status`, and derived display status may be read projections. They are not writable columns on the request aggregate. Quote status belongs to the quote record.

The authoritative assignment fields include assignment status, personnel, vehicle, mode, offer/accept/reject/complete timestamps, assigning actor, predecessor/successor links, aggregate version, and immutable events. Assignment mode `preferred` means a valid preference influenced Dispatch; it never means the sender assigned personnel.

## 25.10 Proof, OTP, Files, and Private Access

```text
proofs_of_pickup
proofs_of_delivery
proof_items
otp_challenges
signatures
file_assets
file_access_grants
file_download_events
malware_scan_results
```

## 25.11 Customer Finance

```text
customer_invoices
customer_invoice_lines
customer_payment_attempts
customer_payment_transactions
customer_payment_allocations
customer_payment_reconciliations
receipts
receipt_events
refund_cases
refund_transactions
payment_disputes
credit_accounts
credit_account_ledger_entries
cash_up_records
cash_up_revisions
cash_up_lines
financial_period_locks
financial_period_reopen_requests
financial_period_reopen_approvals
```

Authoritative invoice settlement states are:

```text
draft
issued
partially_paid
paid
overdue
voided
written_off
```

`has_open_dispute`, `refund_rollup`, and `net_refunded_minor` are derived projections. Payment attempts describe collection only and never become a refund state. Refunds and reversals are separate append-only records.

Every refund stores requester, submitter, approver, optional second approver, processor, policy snapshot, amount/currency, destination, source allocations, status, and aggregate version. Database/application policy enforces segregation of duties.

## 25.12 Platform Billing

```text
platform_billing_settings
platform_billing_mode_versions
subscription_plans
subscription_plan_entitlements
subscription_plan_prices
merchant_subscriptions
scheduled_plan_changes
platform_billing_invoices
platform_billing_invoice_lines
platform_payment_attempts
platform_payment_transactions
platform_payment_allocations
platform_payment_reconciliations
platform_fee_ledger_entries
platform_fee_adjustments
platform_fee_disputes
promotional_discounts
promotional_discount_targets
promotion_redemptions
free_period_offers
sms_usage_charges
add_on_charges
```

`platform_billing_invoice_lines` is declared only here.

`platform_fee_ledger_entries` contains percentage-derived provisional, billable, aggregated, settled, reversed, and adjusted entries only:

```text
id
merchant_id
branch_id nullable
source_customer_invoice_id
billing_mode_snapshot
service_fee_tier_snapshot
fee_basis_type
fee_basis_amount_minor
percentage_rate_bps_snapshot
gross_fee_amount_minor
customer_shifted_amount_minor
merchant_liability_amount_minor
currency
status
platform_billing_invoice_id nullable
configuration_reference_id
created_at
updated_at
```

There is no launch per-transaction fixed Citrus fee. Recurring fixed subscription charges are generated from `subscription_plan_prices` into invoice lines.

Platform payment-attempt states describe collection only:

```text
created
pending
stk_push_sent
customer_cancelled
timeout
successful
failed
expired
callback_received
validated
rejected
duplicate
unknown
reconciliation_required
applied_to_invoice
```

A later refund, overpayment return, reversal, chargeback, or credit is stored separately.

## 25.13 Compensation and Payout

```text
personnel_compensation_plans
compensation_plan_versions
compensation_approval_steps
commission_rules
commission_ledger_entries
salary_ledger_entries
personnel_payout_runs
personnel_payout_items
payout_approvals
payout_attempts
payout_item_results
compensation_adjustments
personnel_earnings_queries
earnings_statements
compensation_acknowledgements
```

Payout runs separately record HR preparer, Finance verifier, Finance approver, high-value Merchant Administrator approver where required, settlement recorder or integration identity, policy version, and per-item settlement results. Editing after verification invalidates downstream approvals.

## 25.14 Canonical Integration-Principal and Webhook Model

```text
integration_principals
integration_clients
integration_credentials
integration_scope_grants
integration_usage_events
webhook_endpoints
webhook_subscriptions
webhook_delivery_attempts
```

An integration principal has a constrained owner type such as `business_customer_organization`, `merchant`, `citrus_system_service`, or approved partner. Owner-link tables may be used instead of a polymorphic foreign key, but credentials exist only in `integration_credentials`. Generic and business-specific credential stores do not coexist.

Secrets are displayed once, stored hashed or in a secrets manager, versioned, expiring, rotatable, and revocable. Rotation overlap is bounded. Organization or merchant suspension disables clients immediately.

## 25.15 Support, Notifications, Audit, and Exports

```text
support_tickets
support_ticket_messages
notifications
notification_templates
notification_deliveries
sms_messages
audit_logs
flagged_audit_events
audit_review_notes
export_jobs
export_downloads
```

## 25.16 Courier Referral Integration Records

```text
courier_referral_snapshots
citrus_product_integration_events
citrus_product_event_delivery_attempts
citrus_product_event_dead_letters
citrus_product_event_reconciliation_cases
```

These are minimal integration and delivery records. Courier does not store central reward ledgers, referrer payout methods, central fraud cases, central payout runs, or authoritative campaign reward rules.

## 25.17 Key Constraints and Semantic Lint

1. Every tenant-owned record has `merchant_id`; branch-owned records carry a matching `branch_id` constraint.
2. Organization-owned records carry both merchant and business-organization constraints.
3. Personnel own-scope records carry `personnel_id` and matching merchant/branch ownership.
4. One active subscription per merchant; effective windows for plan price, billing mode, role grant, and compensation version do not overlap illegally.
5. One active credential authority, webhook authority, and usage authority.
6. One active preferred-personnel reservation per constrained capacity slot according to policy.
7. One active custody handover per delivery.
8. Audit logs are append-only and unavailable for application update/delete.
9. Tracking tokens store hash, purpose, expiry, and revocation.
10. Money fields end in `_minor` and include currency; ambiguous money fields fail schema lint.
11. Generic mutable `status` on `delivery_requests` is forbidden.
12. Duplicate table declarations, case/plural aliases, and competing ORM models fail CI.
13. Projection tables are marked read-only and rebuildable.
14. Soft-deleted historical records remain referenceable under retention policy.

# 26. Required Backend Enforcement Layers

Every protected request passes, in order appropriate to the route:

1. authentication or valid public token;
2. token/session expiry and revocation;
3. user/account active state;
4. merchant operational status;
5. billing status and restricted allowlist;
6. tenant ownership;
7. branch assignment;
8. role and permission;
9. plan entitlement/limit;
10. personnel own-scope where applicable;
11. field-level masking policy;
12. resource state transition;
13. financial period lock;
14. input validation;
15. prohibited-goods/capacity/eligibility rules;
16. idempotency for financial/integration mutations;
17. rate limit/abuse controls;
18. audit event.

No controller may directly bypass central tenant, billing, lock, compensation, payment, or file services.

---

# 27. Normative Launch API and Internal Command Contract

This section lists active normative launch operations. It is not illustrative. Reserved architecture-ready names and Post-MVP examples are excluded from production OpenAPI, SDKs, route discovery, and gateways.

## 27.1 Canonical Route Registry

Every route or internal command has one registry object containing:

```text
operation_id
capability_id
method
path_or_command
release_class
actor_classes
permission
scope_rule
state_action
idempotency_required
audit_event
deprecation_status
replacement_operation_id
```

One business mutation has one canonical operation ID. Temporary compatibility aliases must forward to the canonical command without weakening authorization, state, evidence, idempotency, or audit requirements; they emit deprecation telemetry and have a removal version.

## 27.2 Public Merchant Registration and Human Authentication

```text
POST /api/v1/merchant-registration
POST /api/v1/auth/magic-link/request
POST /api/v1/auth/magic-link/consume
POST /api/v1/merchants/{merchant_slug}/sender-auth/request
POST /api/v1/merchants/{merchant_slug}/sender-auth/verify
GET  /api/v1/tracking/{token}
POST /api/v1/tracking/{token}/availability
POST /api/v1/tracking/{token}/proof-confirmation
POST /api/v1/tracking/{token}/issues
```

Unscoped sender-authentication routes do not exist. Sender challenges bind merchant, identity candidate, intended membership, nonce, purpose, expiry, attempts, device/risk metadata, and contact hash. Unknown merchant/sender and invalid contact receive neutral responses. Sessions are merchant-bound; multi-merchant senders explicitly reauthorize context.

## 27.3 Merchant and Branch

```text
GET   /api/v1/merchant
PATCH /api/v1/merchant
GET   /api/v1/branches
POST  /api/v1/branches
GET   /api/v1/branches/{branch}
PATCH /api/v1/branches/{branch}
DELETE /api/v1/branches/{branch}
GET   /api/v1/branches/{branch}/calendar
PUT   /api/v1/branches/{branch}/calendar
POST  /api/v1/branches/{branch}/operational-days/{day}/open
POST  /api/v1/branches/{branch}/operational-days/{day}/pause
POST  /api/v1/branches/{branch}/operational-days/{day}/close
POST  /api/v1/branches/{branch}/operational-days/{day}/reopen
POST  /api/v1/branches/{branch}/cash-ups
```

Operational-day reopen affects intake only and cannot unlock finance.

## 27.4 Staff Lifecycle and Emergency Security

```text
GET  /api/v1/staff
POST /api/v1/staff/invitations
POST /api/v1/staff/invitations/{invitation}/resend
POST /api/v1/staff/invitations/{invitation}/revoke
POST /api/v1/staff/{staff}/activate
POST /api/v1/staff/{staff}/suspend-employment
POST /api/v1/staff/{staff}/reactivate-employment
POST /api/v1/staff/{staff}/deactivate
POST /api/v1/staff/{staff}/emergency-security-suspensions
POST /api/v1/staff/{staff}/emergency-security-suspensions/{suspension}/clear
PUT  /api/v1/personnel/{personnel}/eligibility
PUT  /api/v1/personnel/{personnel}/availability
```

`deactivate` is the one HR lifecycle deactivation operation. Emergency security suspension is a separate owner/governance action and does not change employment or compensation.

## 27.5 Business-Customer Memberships and Integration Administration

```text
GET/POST /api/v1/business-organizations
GET/PATCH /api/v1/business-organizations/{organization}
GET/POST /api/v1/business-organizations/{organization}/members
POST /api/v1/business-organizations/{organization}/invitations
POST /api/v1/business-organizations/{organization}/members/{member}/suspend
POST /api/v1/business-organizations/{organization}/members/{member}/reactivate
DELETE /api/v1/business-organizations/{organization}/members/{member}
GET/POST /api/v1/business-organizations/{organization}/api-clients
GET/PATCH /api/v1/business-organizations/{organization}/api-clients/{client}
POST /api/v1/business-organizations/{organization}/api-clients/{client}/credentials
POST /api/v1/business-organizations/{organization}/api-clients/{client}/rotate-credential
POST /api/v1/business-organizations/{organization}/api-clients/{client}/revoke
GET/POST /api/v1/business-organizations/{organization}/webhooks
POST /api/v1/business-organizations/{organization}/webhooks/{webhook}/rotate-secret
POST /api/v1/business-organizations/{organization}/webhooks/{webhook}/disable
```

## 27.6 Catalogue, Customers, and Addresses

```text
GET/POST /api/v1/delivery-services
GET/PATCH/DELETE /api/v1/delivery-services/{service}
GET/POST /api/v1/service-zones
GET/POST /api/v1/senders
GET/POST /api/v1/recipients
GET/POST /api/v1/addresses
```

## 27.7 Delivery Request, Quote, Preference, and Dispatch Commands

```text
GET/POST /api/v1/deliveries
GET /api/v1/deliveries/{delivery}
PATCH /api/v1/deliveries/{delivery}
POST /api/v1/deliveries/{delivery}/validate-addresses
POST /api/v1/deliveries/{delivery}/quotes
POST /api/v1/quotes/{quote}/submit-for-approval
POST /api/v1/quotes/{quote}/approval-steps/{step}/approve
POST /api/v1/quotes/{quote}/approval-steps/{step}/return-for-changes
POST /api/v1/quotes/{quote}/approval-steps/{step}/reject
POST /api/v1/quotes/{quote}/send
POST /api/v1/quotes/{quote}/accept
POST /api/v1/quotes/{quote}/reject
POST /api/v1/quotes/{quote}/supersede
POST /api/v1/deliveries/{delivery}/preferred-personnel-requests
POST /api/v1/deliveries/{delivery}/confirm
POST /api/v1/deliveries/{delivery}/assignments
POST /api/v1/deliveries/{delivery}/reassignment-requests
POST /api/v1/deliveries/{delivery}/custody-handovers
POST /api/v1/deliveries/{delivery}/cancel-before-custody
POST /api/v1/deliveries/{delivery}/returns
POST /api/v1/deliveries/{delivery}/redeliveries
```

Delivery PATCH accepts only an allowlist of non-state metadata. Fields for state, assignee, proof, invoice, payment, refund, dispute, and lifecycle return `422 immutable_or_command_only_field`.

## 27.8 Basic Routing at Launch

```text
POST /api/v1/routes
GET   /api/v1/routes/{route}
POST  /api/v1/routes/{route}/calculate-distance-eta
POST  /api/v1/routes/{route}/reorder-stops
```

There is no launch full-optimization route. Stop reordering cannot modify locked/completed stops or perform multi-vehicle allocation.

## 27.9 Personnel Own-Scope Commands

```text
GET  /api/v1/personnel/me/assignments
POST /api/v1/personnel/me/assignments/{assignment}/accept
POST /api/v1/personnel/me/assignments/{assignment}/reject
POST /api/v1/personnel/me/deliveries/{delivery}/en-route-to-pickup
POST /api/v1/personnel/me/deliveries/{delivery}/arrive-at-pickup
POST /api/v1/personnel/me/deliveries/{delivery}/submit-pickup-verification
POST /api/v1/personnel/me/deliveries/{delivery}/complete-pickup
POST /api/v1/personnel/me/deliveries/{delivery}/start-transit
POST /api/v1/personnel/me/deliveries/{delivery}/mark-arriving
POST /api/v1/personnel/me/deliveries/{delivery}/arrive-at-destination
POST /api/v1/personnel/me/deliveries/{delivery}/submit-delivery-proof
POST /api/v1/personnel/me/deliveries/{delivery}/report-failed-attempt
POST /api/v1/personnel/me/deliveries/{delivery}/start-return
POST /api/v1/personnel/me/deliveries/{delivery}/complete-return
POST /api/v1/personnel/me/deliveries/{delivery}/incidents
GET  /api/v1/personnel/me/earnings
GET  /api/v1/personnel/me/statements/{statement}
POST /api/v1/personnel/me/earnings-queries
```

No generic personnel status endpoint exists. Offline PWA commands carry client command ID, aggregate version, and original event time; server-side state, assignment, evidence, and policy validation still applies.

## 27.10 Customer Finance, Refund, Dispute, and Cash-Up

```text
GET/POST /api/v1/customer-invoices
POST /api/v1/customer-payments
POST /api/v1/customer-payments/{payment}/validate
POST /api/v1/customer-payments/{payment}/reject
POST /api/v1/customer-payments/{payment}/request-correction
POST /api/v1/customer-payments/{payment}/reconcile
POST /api/v1/refunds
POST /api/v1/refunds/{refund}/submit
POST /api/v1/refunds/{refund}/approve
POST /api/v1/refunds/{refund}/second-approve
POST /api/v1/refunds/{refund}/reject
POST /api/v1/refunds/{refund}/cancel
POST /api/v1/refunds/{refund}/start-processing
POST /api/v1/refunds/{refund}/complete
POST /api/v1/refunds/{refund}/fail
POST /api/v1/refunds/{refund}/retry
GET/POST /api/v1/payment-disputes
POST /api/v1/payment-disputes/{dispute}/request-evidence
POST /api/v1/payment-disputes/{dispute}/resolve
POST /api/v1/cash-ups/{cashup}/request-correction
POST /api/v1/cash-ups/{cashup}/resubmit
POST /api/v1/cash-ups/{cashup}/approve
POST /api/v1/cash-ups/{cashup}/reject-terminal
POST /api/v1/financial-periods/{period}/lock
```

The refund requester cannot approve the same refund. Changes to amount, destination, or source allocation invalidate approvals.

## 27.11 Financial-Period Reopen Workflow

```text
POST /api/v1/financial-period-reopen-requests
POST /api/v1/financial-period-reopen-requests/{request}/approve
POST /api/v1/financial-period-reopen-requests/{request}/reject
POST /api/v1/financial-period-reopen-requests/{request}/execute
POST /api/v1/financial-period-reopen-requests/{request}/cancel
POST /api/v1/financial-period-reopen-requests/{request}/close
```

No generic financial-period reopen operation exists.

## 27.12 Compensation and Payout

```text
GET/POST /api/v1/compensation-plans
POST /api/v1/compensation-plans/{plan}/submit
POST /api/v1/compensation-plans/{plan}/finance-approve
POST /api/v1/compensation-plans/{plan}/merchant-admin-approve
POST /api/v1/compensation-plans/{plan}/reject
POST /api/v1/compensation-plans/{plan}/acknowledge
POST /api/v1/compensation-plans/{plan}/suspend
POST /api/v1/compensation-plans/{plan}/end
POST /api/v1/payout-runs
POST /api/v1/payout-runs/{run}/submit
POST /api/v1/payout-runs/{run}/verify
POST /api/v1/payout-runs/{run}/approve-standard
POST /api/v1/payout-runs/{run}/approve-high-value
POST /api/v1/payout-runs/{run}/reject
POST /api/v1/payout-runs/{run}/record-settlement
POST /api/v1/payout-runs/{run}/cancel-draft
```

Settlement recorder differs from prohibited verifier/approver combinations under the snapshotted threshold policy.

## 27.13 Merchant Platform Billing

```text
GET  /api/v1/billing/overview
GET  /api/v1/billing/invoices
GET  /api/v1/billing/invoices/{invoice}
POST /api/v1/billing/invoices/{invoice}/mpesa/stk-push
GET  /api/v1/billing/payment-attempts/{attempt}
GET/POST /api/v1/billing/plan-change
GET/POST /api/v1/billing/platform-fee-disputes
```

Default payment initiators are Merchant Administrator and Merchant Finance. Branch Manager or Dispatch requires explicit delegated permission, merchant policy, scope, limits, phone policy, step-up where required, audit, and notification. Initiating an STK Push never marks an invoice paid.

## 27.14 Support, Audit, Notifications, and Receipts

```text
GET/POST /api/v1/support/tickets
POST /api/v1/support/tickets/{ticket}/messages
POST /api/v1/support/tickets/{ticket}/escalate
POST /api/v1/support/tickets/{ticket}/resolve
GET/POST /api/v1/audit/flags
POST /api/v1/audit/flags/{flag}/notes
POST /api/v1/audit/flags/{flag}/escalate
POST /api/v1/audit/flags/{flag}/resolve
GET  /api/v1/notifications
POST /api/v1/notifications/{notification}/read
GET/PATCH /api/v1/notification-preferences
GET  /api/v1/receipts/{receipt}
POST /api/v1/receipts/{receipt}/regenerate-document
POST /api/v1/receipts/{receipt}/send-copy
```

## 27.15 Platform Internal Users and Governance

```text
GET/POST /api/v1/platform/internal-users
GET/PATCH /api/v1/platform/internal-users/{user}
POST /api/v1/platform/internal-users/{user}/suspend
POST /api/v1/platform/internal-users/{user}/reactivate
POST /api/v1/platform/internal-users/{user}/role-grants
DELETE /api/v1/platform/internal-users/{user}/role-grants/{grant}
POST /api/v1/platform/break-glass-requests
POST /api/v1/platform/break-glass-requests/{request}/approve
POST /api/v1/platform/break-glass-requests/{request}/activate
POST /api/v1/platform/break-glass-sessions/{session}/terminate
GET/PATCH /api/v1/platform/billing/settings
GET/POST /api/v1/platform/billing/plan-prices
POST /api/v1/platform/billing/plan-prices/{price}/cancel
GET/POST /api/v1/platform/promotions
GET/POST /api/v1/platform/promotions/{promotion}/targets
GET /api/v1/platform/merchants
POST /api/v1/platform/merchants/{merchant}/suspend
POST /api/v1/platform/merchants/{merchant}/reactivate
POST /api/v1/platform/merchants/{merchant}/deactivate
GET /api/v1/platform/billing/reconciliation-exceptions
GET /api/v1/platform/audit
```

No platform route creates a merchant or impersonates a merchant user at launch.

## 27.16 Courier-to-Central Referral Commands

```text
POST /internal/v1/citrus-referral/code-validations
POST /internal/v1/citrus-referral/attribution-events
POST /internal/v1/citrus-referral/product-events
GET  /internal/v1/citrus-referral/product-events/{event_id}/status
POST /internal/v1/citrus-referral/product-events/{event_id}/replay
GET  /api/v1/merchant/referral-attribution
GET  /api/v1/public/referral-campaigns/courier/{campaign_code}
```

Courier never exposes central reward ledger, referrer payment method, cross-product earnings, fraud case, or payout statement routes.

## 27.17 Webhook Ingress and Outbound Delivery

```text
POST /api/v1/webhooks/mpesa/customer-payments
POST /api/v1/webhooks/mpesa/platform-billing
POST /api/v1/webhooks/payment-gateways/{provider}
POST /api/v1/webhooks/notifications/{provider}
```

Core business-customer outbound webhooks are launch-implemented. Ingress and egress require signature validation, timestamp/replay protection, idempotency, rate limits, private-network/SSRF protection, retry/backoff, attempt caps, dead letters, and audit.

## 27.18 Common Error and Compatibility Behaviour

All operations use the Section 0 error envelope. Deprecated aliases do not bypass canonical policy. Removed sender-auth routes return `404` after the bounded compatibility window or a documented `410 route_removed` without credential processing. Reserved/Post-MVP endpoints are not deployed. A feature-disabled implemented endpoint returns `403 entitlement_required` or `409 feature_not_enabled`; an unimplemented endpoint never returns a misleading entitlement error.

# 28. Frontend Structure and Required Screens

## 28.1 Shared Structure

- role-specific landing page;
- get-started page;
- left navigation;
- top header;
- global scoped search;
- notification centre;
- account menu;
- responsive drawers;
- status banners;
- permission-aware actions;
- consistent tables, filters, forms, timelines, maps, and file viewers.

## 28.2 Super Administrator Screens

Platform Overview; Merchants; Merchant Detail; Registration Monitoring; Billing Settings; Billing Modes; Plan Entitlements; Plan Prices & Schedule; Promotions/Trials; Preferred Personnel Fee; Platform Billing Invoices; M-Pesa Attempts; Reconciliation Exceptions; Percentage Fees; Fee Disputes; Maps/SMS/API Usage; Integrations; System Health; Failed Jobs; Audit; Internal Users; Settings.

## 28.3 Merchant Administrator Screens

Overview; Get Started; Merchant Profile; Branches; Staff Directory; Subscription & Plan; Platform Invoices; M-Pesa Payment; Pricing Notices; Merchant Reports; Branch Comparison; Fleet Summary; Customer Summary; Compensation Liabilities; Payout Approvals; Cash-Up Status; Period-Reopen Approvals; Audit Summary; Integrations; Support; Settings.

## 28.4 Branch Manager Screens

Branch Overview; Get Started; Branch Profile; Calendar; Delivery Services; Zones; Pricing; Goods Rules; Fleet; Personnel Availability/Eligibility Read View; Delivery Board Read View; Day Opening/Closing; Cash-Up Submission; Branch Reports; Audit Read View; Platform Payment Shortcut; Settings.

## 28.5 HR Screens

HR Overview; Staff Roster; Invitations; Staff Detail; Role/Permission Preview; Personnel Qualifications; Eligibility; Availability/Shifts; Documents/Expiry; Compensation Plans; Payout Preparation; Earnings Queries; HR Reports; Audit Activity; Settings.

## 28.6 Finance Screens

As defined in §4.5, including gateway exceptions, COD, platform billing, payouts, cash-up, locks, exports, and audit.

## 28.7 Dispatch Screens

Dispatch Overview; New Requests; Address Review; Quotes; Unassigned; Assignment Board; Route Map; Route Groups; Active Pickups; In Transit; Destination/Proof Pending; Failed/Returns; Customers; Invoices; Record Payment; Communications; Incidents; Support Handoffs; Reports; Platform Payment Shortcut; Settings.

## 28.8 Personnel PWA Screens

Today; Assignment Detail; Route/Map; Pickup Checklist; Pickup Proof; Transit; Destination Checklist; Delivery Proof; Failed Attempt; Incident; COD Handover; History; My Earnings; Statements; Earnings Queries; Availability; Help.

## 28.9 Support Screens

Support Overview; Ticket Queue; Ticket Detail; Delivery Context; Communications; Escalations; Satisfaction; Reports.

## 28.10 Audit Screens

Audit Overview; Logs; Flagged Events; Event Detail; Filters; Unauthorized Attempts; File Activity; Compensation Audit; Finance Audit; Exports.

## 28.11 Sender Screens

Sender Home; Create Delivery; Saved Addresses; Recipients; Goods; Quote; Payment; Active Tracking; Delivery History; Invoices/Receipts; Support; Ratings; Profile/Consent; Business API where entitled.

## 28.12 Recipient Screens

Secure Tracking; Availability; ETA/Map; OTP/Signature/Proof; Issue Report; Rating. No general tenant navigation.

---


## 28.13 Business Customer Organization and Integration Screens

Business Customer Administrator: Organization Overview; Get Started; Profile; Members; Invitations; Roles; Addresses; Recipients; Policy Defaults; Activity; Audit; Security.

Business Customer Operator: Active Requests; Create Request; Quotes; Recurring Routes; Tracking; Documents; Support; Permitted Cancellations.

Business Customer Finance Viewer: Invoices; Allocations; Receipts; Credit Statements; Export Requests; Download History.

Business Customer Integration Administrator: API Clients; Create Client; One-Time Secret; Scope Grants; Credential Versions; Rotation; Revocation; Webhook Subscriptions; Endpoint Verification; Delivery Attempts; Dead Letters; Usage; Quotas; Security Alerts; Audit; Sandbox.

Every screen is merchant- and organization-scoped. The final organization administrator cannot be removed without a replacement. Secret values are never redisplayed. Suspended organizations show recovery guidance but do not expose other organizations.

## 28.14 Courier Referral-Native Screens

Public Refer & Earn; Courier Campaign Landing; Registration Referral Code; Referral Attribution Pending/Confirmed/Rejected State; Merchant Referral Attribution Summary with privacy limits; Central Referrer Dashboard Redirect; Integration Health for authorized internal platform users.

Courier does not render referrer payment methods, central reward ledger, cross-product earnings, central payout history, or central referrer tax details.

## 28.15 Internal Citrus Platform Role Screens

Platform Users; Role Grants; Billing Reconciliation; Product Integrations; Referral Event Health; Dead Letters; Security and Risk; Platform Audit; Break-Glass Review; Executive Aggregate Reports.

# 29. Modern UX, Responsive Layout, and Accessibility

## 29.1 Responsive Design

CSS viewport-based behaviour only. Suggested breakpoints:

```text
mobile: <= 767px
tablet: 768px–1024px
desktop: >= 1025px
```

Do not rely on device names or user-agent classification.

Primary device expectations:

| User | Primary device |
|---|---|
| Super Administrator | Desktop |
| Merchant Administrator | Desktop/tablet |
| Branch Manager | Desktop/tablet |
| HR | Desktop/tablet |
| Finance | Desktop |
| Dispatch | Desktop/tablet |
| Delivery Personnel | Mobile |
| Support/Audit | Desktop/tablet |
| Sender | Mobile/desktop |
| Recipient | Mobile |

## 29.2 200% Zoom and Reflow

At 200% browser zoom:

- core actions remain reachable;
- text does not overlap;
- fixed headers/sidebars do not obscure content;
- tables use contained scrolling or card adaptation;
- maps have textual alternatives;
- whole-page horizontal scrolling is prohibited for ordinary content.

## 29.3 Accessibility

- keyboard navigation;
- visible focus;
- semantic headings/landmarks;
- proper labels and instructions;
- accessible validation and error summaries;
- high contrast;
- touch targets;
- status not colour-only;
- screen-reader text for map/status icons;
- reduced-motion support;
- accessible dialogs;
- captions/text for media evidence where necessary;
- date/time/currency clarity;
- consistent language.

## 29.4 Mobile Personnel UX

- large action buttons;
- one-task-at-a-time flow;
- clear offline/sync state;
- minimal typing;
- camera integration;
- location permission explanation;
- retry-safe actions;
- proof preview;
- no dense desktop tables;
- battery/data-conscious tracking.

---

# 30. Security, Privacy, and Compliance Controls

## 30.1 Access Security

- secure authentication;
- Magic Link single-use/expiry;
- MFA for Super Administrator and optional risk-based merchant MFA extension;
- session rotation/invalidation;
- CSRF protection;
- strict CORS;
- rate limits;
- IDOR prevention;
- tenant/branch/own scope;
- permission policies;
- reauthentication for high-risk actions;
- suspicious-session monitoring.

## 30.2 Secrets

No private keys, API secrets, gateway credentials, database credentials, M-Pesa secrets, webhook secrets, or reusable tokens in frontend assets, browser storage, source maps, logs, emails, or error messages. Public browser keys are restricted and treated as public identifiers, not secrets.

## 30.3 Logs

Log-redaction middleware removes or masks passwords, OTPs, Magic Link tokens, authorization headers, cookies, card data, M-Pesa secrets, API keys, webhook signatures, and sensitive PII. Debug logs are disabled in production.

## 30.4 Upload Validation

Server-side extension, MIME, magic-byte, size, dimension, file-count, filename, path, image-processing, malware-scan hook, and storage policy. Executable or unsafe active content is rejected. SVG handling is disabled or sanitized under strict policy.

## 30.5 Private Storage

Private bucket/container, no public ACL, tenant/branch object keys, authorized access service, expiring signed URL, download audit, revocation strategy, and retention policy.

## 30.6 Webhook Security

Signature validation, source/provider verification where possible, timestamp tolerance, replay prevention, idempotency, raw-body verification, safe retry, and reconciliation.

## 30.7 Encryption and Data Minimization

TLS in transit; encryption at rest through infrastructure; application-level encryption for selected high-risk fields where justified; only collect data necessary for delivery, billing, employment, or legal obligations; retention and deletion/anonymization policy.

## 30.8 Location Privacy

Location collection is disclosed, purpose-limited to active work, access-controlled, retained for a defined period, and not exposed to customers beyond configured tracking needs.

## 30.9 Audit Integrity

Append-only application permissions, database safeguards, hash/chained-hash, secure timestamping, monitoring for gaps, and export verification.

---

# 31. Performance, Scalability, Reliability, and Realtime

## 31.1 Performance Requirements

- paginated/cursor delivery lists;
- indexed tenant, branch, status, date, reference, phone hash/search, assignment, vehicle, and invoice fields;
- query budgets and N+1 prevention;
- queue notifications/reports/files/webhooks;
- lazy maps;
- optimized images;
- CDN for public assets;
- cached reference data;
- provider-compliant route cache;
- database monitoring;
- asynchronous exports;
- search service at scale;
- rate-limited tracking/location updates.

## 31.2 Reliability

- idempotent financial and delivery transition endpoints;
- queue retry with dead-letter visibility;
- webhook replay/retry;
- database transactions;
- optimistic/pessimistic locks where appropriate;
- duplicate prevention;
- health checks;
- backups and restore tests;
- graceful third-party outage handling;
- status reconciliation jobs.

## 31.3 Expected Scale Boundaries

Capacity planning tracks merchants, branches, active users, concurrent dispatchers, active tracked deliveries, location updates/minute, requests/day, proof uploads, notifications, webhook throughput, and report/export load.

## 31.4 Degraded Mode

When Maps, payment, SMS, email, or realtime provider is unavailable:

- show explicit degraded state;
- preserve draft/queued work safely;
- retry idempotently;
- do not falsely mark delivery/payment complete;
- allow manual address/route notes where safe;
- provide Finance/Dispatch exception queues;
- alert operators.

---

# 32. Audit Events and Background Jobs

## 32.1 Required Audit Events

- registration and setup;
- merchant/branch status;
- roles/permissions/branch assignment;
- staff/eligibility/availability/documents;
- service/zone/pricing/quote override;
- customer/address/contact access;
- delivery create/edit/cancel;
- assignment/reassignment/rejection/route change;
- every state transition;
- proof/OTP/signature/photo/file actions;
- incident/failure/return/dispute;
- invoice/payment/validation/reference override/receipt/refund;
- cash-up/lock/reopen;
- compensation/payout/query;
- billing mode/plan price/promotion/platform fee;
- M-Pesa platform payment/recovery;
- SMS/API/webhook/maps usage anomalies;
- exports/downloads;
- unauthorized attempts;
- secret or upload security events.

## 32.2 Scheduled and Queue Jobs

- platform invoice generation;
- percentage-fee aggregation;
- trial/grace/suspension transitions;
- shared overdue reminders;
- scheduled plan-price activation;
- scheduled plan changes;
- M-Pesa reconciliation;
- customer gateway reconciliation;
- salary accrual;
- commission calculation/adjustment;
- payout statement generation;
- SMS cost rollup;
- notification delivery/retry;
- webhook delivery/retry;
- route optimization;
- tracking token expiry;
- file scan/thumbnail processing;
- export/report generation;
- document expiry reminders;
- stale delivery/assignment alerts;
- billing-only reactivation.

No financial value or time window is hardcoded in jobs.

---


## 32.3 Citrus Referral Integration Audit Events and Jobs

Required audit events include referral code captured, code validation requested, local attribution snapshot created, attribution event queued, event signed, delivery attempted, acknowledged, rejected, dead-lettered, replayed, central attribution confirmed/rejected/held, source amount disputed, and integration credential rotated.

Required jobs include transactional-outbox publishing, signature generation, retry with backoff, event-status reconciliation, dead-letter alerting, safe replay, stale pending-attribution review, campaign cache refresh, and key-rotation readiness checks.

Merchant registration, merchant payment, and delivery transactions commit independently of central network availability. Event publication begins from committed outbox records.

# 33. Testing Strategy

## 33.1 Test Layers

- unit tests;
- domain/service tests;
- policy/authorization tests;
- feature/API tests;
- database constraint tests;
- integration contract tests;
- webhook tests;
- queue/scheduler tests;
- browser/end-to-end tests;
- responsive/accessibility tests;
- security/static scans;
- performance/load tests;
- backup/restore and disaster-recovery exercises.

## 33.2 Required Suites

1. MerchantSelfRegistrationOnlyTest.
2. SuperAdminCannotCreateMerchantTest.
3. TenantIsolationTest.
4. BranchScopeTest.
5. PersonnelOwnScopeTest.
6. MagicLinkSecurityTest.
7. SenderAuthenticationTest.
8. RecipientTrackingTokenTest.
9. BillingOperationalStatusSeparationTest.
10. GraceSuspensionAllowlistParityTest.
11. BillingRecoveryReactivationTest.
12. ManualSuspensionNotAutoReactivatedTest.
13. SubscriptionPlanEntitlementTest.
14. PlanPriceSourceOfTruthTest.
15. ScheduledPriceActivationTest.
16. NoProrationPlanChangeTest.
17. PlanChangeAndPriceChangeSeparationTest.
18. PercentagePlatformFeeLifecycleTest.
19. PlatformFeeAdjustmentAndDisputeTest.
20. PromotionTargetingTest.
21. DispatchVsBranchManagerAssignmentTest.
22. HRStaffEligibilityTest.
23. DeliveryAssignmentEligibilityTest.
24. VehicleCapacityConflictTest.
25. DeliveryStateMachineTest.
26. DeliveryCreationAtomicityTest.
27. AddressValidationSnapshotTest.
28. QuoteSnapshotImmutabilityTest.
29. PreferredPersonnelFeeTest.
30. DriverOwnAssignmentTest.
31. PersonnelContactExportAbsentTest.
32. PickupProofValidationTest.
33. DeliveryProofValidationTest.
34. OTPSecurityTest.
35. FailedDeliveryReturnTraceabilityTest.
36. CustomerPaymentMakerCheckerTest.
37. IntegratedPaymentWebhookTest.
38. DuplicatePaymentReferenceTest.
39. PartialSplitPaymentTest.
40. AutomaticReceiptAfterValidationTest.
41. CODReconciliationTest.
42. RefundLimitTest.
43. CashUpWorkflowTest.
44. GrowthCashUpTerminalStateTest.
45. PeriodLockOwnershipTest.
46. LockedPeriodMutationDenialTest.
47. CompensationModelTest.
48. CommissionCalculationTest.
49. SalaryAccrualTest.
50. PayoutMakerCheckerTest.
51. HighValuePayoutApprovalTest.
52. EarningsOwnScopeTest.
53. AuditBranchScopeMaskingTest.
54. AuditFlaggedEventTest.
55. AuditAppendOnlyHashTest.
56. PrivateStorageAuthorizationTest.
57. UploadValidationTest.
58. FrontendSecretScanTest.
59. LogRedactionTest.
60. WebhookSignatureReplayTest.
61. MapsKeyRestrictionConfigurationTest.
62. RouteEstimateSnapshotTest.
63. APIIdempotencyTest.
64. ExportGovernanceTest.
65. ResponsiveCSSViewportTest.
66. ZoomReflow200PercentTest.
67. HorizontalScrollTest.
68. AccessibilityKeyboardFocusTest.
69. NotificationPrivacyTest.
70. DeactivationHistoryPreservationTest.

## 33.3 Case Coverage

Each suite includes as applicable:

- positive success;
- negative validation;
- unauthenticated denial;
- wrong-role denial;
- wrong-permission denial;
- cross-tenant denial;
- cross-branch denial;
- personnel own-scope denial;
- billing read-only denial;
- suspension recovery allowance;
- plan entitlement denial;
- locked-period denial;
- duplicate/idempotency behaviour;
- audit assertion;
- sensitive-field masking;
- concurrency case.

## 33.4 Manifesto Proof and Demonstrated Resolution

A correction is not complete until the relevant tests pass and the old contradictory route, field, permission, UI control, status, or test is removed or migrated.

---


## 33.5 Corrected-Domain and Referral Compatibility Suites

```text
AccountTaxonomyConsistencyTest
HumanAndMachineIdentitySeparationTest
BranchEntityCannotAuthenticateTest
HRRoutineLifecycleOwnershipTest
MerchantAdminEmergencySuspensionTest
EmergencySuspensionDoesNotTerminateEmploymentTest
OrthogonalDeliveryStateTest
CompleteTransitionRegistryTest
ReassignmentIsEventNotDeliveryStatusTest
PreferredPersonnelCreatesRequestNotAssignmentTest
QuoteApprovalAuthorityTest
CompensationPlanApprovalOwnershipTest
BillingModeSingleSourceTest
BillingStatusCompleteLifecycleTest
GraceAndSuspensionAllowlistParityTest
FixedSubscriptionChargedOncePerPeriodTest
PlatformInvoiceAttemptStateSeparationTest
CashUpRevisionWorkflowTest
MerchantScopedSenderAuthenticationTest
SenderEnumerationProtectionTest
BusinessAPIClientOwnerRequiredTest
BusinessAPICredentialRevocationTest
APICapabilityTraceabilityTest
ReleaseClassificationUniquenessTest
TerminologyLintTest
CourierReferralRegistrationContinuityTest
CourierReferralEventSignatureTest
CourierReferralEventIdempotencyTest
CourierReferralEventVersionTest
CourierReferralDeadLetterReplayTest
CourierReferralIncorrectAmountHoldTest
CourierReferralProductIsolationTest
CourierReferralDataMinimizationTest
CourierReferralNoLocalPayoutTest
CourierReferralSelfReferralHoldTest
CourierReferralCrossProductAttributionIsolationTest
ContradictionRegisterGateTest
```

Every suite includes successful behavior and, where relevant, unauthenticated, wrong-role, wrong-permission, cross-tenant, cross-branch, own-scope, entitlement, billing restriction, locked period, stale state, duplicate, race, audit, masking, provider outage, replay, and historical-preservation cases.


## 33.6 Semantic Regression and Contradiction Suites

The release must also execute:

1. CanonicalPlatformOwnerRoleTest.
2. LastPlatformOwnerProtectionTest.
3. InternalIdentityDomainIsolationTest.
4. InternalRoleOnboardingCompletenessTest.
5. BusinessCustomerRoleAndOrganizationIsolationTest.
6. FinalBusinessCustomerAdministratorProtectionTest.
7. QuoteStatusApprovalSeparationTest.
8. CompleteTransitionRegistrySchemaTest.
9. AssignmentHandoverDualRecordTest.
10. OperationalDeliveredStatePersistenceTest.
11. InvoicePaymentDisputeRefundAuthoritySeparationTest.
12. PaymentAttemptNoRefundStateTest.
13. DeliveryRequestNoMonolithicStatusTest.
14. PreferredPersonnelNoDirectAssignmentFieldTest.
15. PercentageLedgerNoRecurringFixedChargeTest.
16. LegacyIdentitySchemaReadOnlyOrRemovedTest.
17. CanonicalAPIClientCredentialSchemaTest.
18. UniqueCanonicalTableDeclarationTest.
19. MerchantScopedSenderAuthenticationOnlyTest.
20. NormativeRouteTraceabilityTest.
21. GenericStateMutationRouteAbsenceTest.
22. FinancialReopenCommandSeparationTest.
23. FullOptimizerLaunchAbsenceTest.
24. RefundMakerCheckerTest.
25. RoutingReleaseEntitlementConsistencyTest.
26. APIWebhookLaunchImplementationTest.
27. MultiBranchLaunchImplementationTest.
28. LaunchModuleNoDeferredCapabilityTest.
29. StarterOperationalViabilityTest.
30. PlatformBillingPayerPolicyTest.
31. PayoutRunSegregationAndPartialSettlementTest.
32. BranchDayVsFinancialReopenTerminologyTest.
33. SenderPreferenceOwnershipTest.
34. CentralCampaignOwnershipTest.
35. CourierCentralRoleIsolationTest.
36. ReferralMinorUnitConventionTest.
37. NeutralReferralFactEventNameTest.
38. DetachedProductEventSignatureTest.
39. SingleNormativeScopeTest.
40. MarkdownArtifactIntegrityTest.
41. SemanticValidationGateTest.
42. EveryStateRegistryCompletenessTest.
43. DuplicateSemanticRouteDetectionTest.

Every suite must include positive, denial, concurrency, idempotency, failure-recovery, audit, migration, and edge-case coverage applicable to its domain. A skipped test requires an approved release exception and cannot cover tenant isolation, authorization, financial integrity, or credential security.

# 34. Deployment and Production Readiness

Launch requires:

1. environment separation;
2. database migrations reviewed and reversible;
3. seed/config for plans and entitlements;
4. no hardcoded production financial values;
5. secrets in managed storage;
6. M-Pesa/payment sandbox and production validation;
7. Maps keys restricted;
8. email/SMS domains/providers configured;
9. private storage verified;
10. malware-scan path configured or risk explicitly accepted;
11. queue workers and scheduler supervised;
12. logs redacted;
13. alerts for queue failure, payment callback failure, reconciliation exception, Maps spend, storage error, and security event;
14. backups and restore test;
15. retention policy;
16. audit integrity check;
17. rate limits and WAF rules;
18. health/status page internally;
19. runbooks for M-Pesa, delivery-state correction, failed webhook, suspended merchant, data incident, and restore;
20. support escalation and on-call ownership;
21. accessibility and responsive sign-off;
22. load test for expected dispatch/tracking/location traffic;
23. penetration/security review before sensitive production scale;
24. legal terms, privacy policy, acceptable-use/prohibited-goods policy, and merchant/customer disclosures.

---

# 35. Authoritative Release Classification

Every capability receives exactly one release class for one named release. One machine-readable release manifest drives scope text, plan catalog, entitlement seed data, feature flags, OpenAPI exposure, SDK generation, roadmap, test selection, marketing, contracts, and release notes.

## 35.1 Release Classes

```text
mandatory_mvp
launch_entitlement
launch_add_on
architecture_ready
post_mvp
```

- **Mandatory MVP:** implemented, deployed, and available to the required baseline users.
- **Launch Entitlement:** implemented and deployed at launch but plan/permission gated.
- **Launch Add-On:** implemented and deployed at launch but separately purchased or activated.
- **Architecture-Ready:** schemas/interfaces/extension points only; not routable, enabled, sold, or accepted as working.
- **Post-MVP:** excluded from launch implementation and commercial promise.

A capability cannot hold more than one class for the same release version.

## 35.2 Mandatory MVP

Mandatory MVP includes merchant self-registration, merchant-scoped sender authentication, recipient token access, canonical identities and roles, viable Starter operation, subscription billing and recovery, one-branch operation, orthogonal states, address validation, `routing.basic`, quote/approval, assignment and custody history, Personnel PWA, proof, integrated/offline/COD payments, invoices/receipts, Finance validation, fleet, Support, Audit, private files, product-native referral capture, local referral snapshot, transactional outbox, signed factual product events, pending attribution, and central integration monitoring.

## 35.3 Launch Entitlements

Launch Entitlements include core Multi-Branch controls, advanced compensation and payout, financial-period locks/reopen, enhanced reports/exports/audit, advanced Finance/refund/dispute controls, and core business-customer API clients/outbound webhooks.

## 35.4 Launch Add-Ons

Launch Add-Ons include approved message bundles, extra storage, extra branches, Growth API/webhook access, and separately approved guaranteed preferred-personnel reservation products.

## 35.5 Architecture-Ready

Architecture-ready covers only non-routable extension points for future connectors, event families, partner scopes, warehouse/inventory boundaries, and native-app interfaces. Feature flags cannot promote these into production.

## 35.6 Post-MVP

Post-MVP includes full multi-vehicle route optimization, algorithmic driver-hours/capacity balancing, dynamic/surge pricing, AI dispatch or ETA, native mobile apps, telematics, driver wallet, insurance underwriting, warehouse/inventory systems, statutory payroll, and unapproved referral campaign types.

## 35.7 Route Boundary

`routing.basic` means one vehicle/route, Point A-to-B, optional manually ordered waypoints, distance, duration, polyline, ETA, and manual stop reordering. Full optimization means several vehicles/stops, automatic allocation, capacity, time windows, driver hours, balancing, and re-optimization.

## 35.8 Release Error Handling

- A plan cannot advertise a capability absent from the release manifest as implemented.
- A Post-MVP capability cannot appear in production route discovery.
- Architecture-ready capability use fails publication/deployment before runtime rather than behaving partially.
- Downgrade blocks new entitled use while preserving history and starting remediation.
- “Entitled but unavailable” is a release defect, not a normal authorization result.
- Sales/contracts containing superseded capability names trigger commercial remediation and recorded customer communication.

# 36. Risk Assessment and Realistic Likelihoods

| Risk | Approx. likelihood | Impact | Mitigation |
|---|---:|---:|---|
| Address quality/landmark ambiguity | 75% | High | pin, validation, landmarks, contact confirmation, manual review. |
| Maps cost overrun | 65% | High | quotas, restrictions, lazy loading, caching within terms, plan entitlements, alerts. |
| Tenant leakage in rushed build | 70%; reduced to about 10–20% with disciplined controls/testing | Critical | tenant policies, constraints, opaque IDs, automated isolation tests. |
| Driver/rider adoption friction | 60% | High | mobile PWA, minimal steps, offline resilience, training. |
| Recipient ignores tracking/OTP | 45% | Medium | SMS fallback, resend limits, contact confirmation, alternate proof. |
| Customer payment reconciliation errors | 55% | High | webhook validation, unique references, Finance exception queue, idempotency. |
| COD leakage/discrepancy | 50–65% in cash-heavy operations | High | assigned collector, handover evidence, cash-up, maker/checker, audit. |
| Manual dispatch stops scaling | 70% after meaningful growth | High | batch assignment, route groups, optimization, operational metrics. |
| False delivery/non-delivery claims | 40% | Medium/High | OTP, photo, GPS, timestamps, signatures, immutable proof/audit. |
| Failed delivery due to wrong address | 65% | High | validation, pin, landmarks, recipient availability, proactive contact. |
| Double platform-invoice payment | 35–50% without locks | Medium/High | payment lock, idempotency, balance re-check, unique receipt. |
| Compensation disputes | 45% without transparent statements | High | effective-dated terms, source-linked ledgers, statements, queries, audit. |
| Scope creep | 80% | High | lock MVP around request → quote → pay/confirm → assign → pickup → transit → deliver → proof → reconcile. |
| Native app added too early | 65% delay risk | High | prove PWA workflow first. |
| Route optimization included too early | 55–70% delay risk | High | launch basic routes first; add optimization by entitlement/phase. |
| Security incident from exposed secrets/files | 35–60% if rushed | Critical | private storage, secret manager, scans, log redaction, signed access. |
| Subscription churn during trial conversion | 35–55% | High | clear pricing, useful Starter, in-product notices, grace/recovery, onboarding. |

**Most realistic MVP complexity:** high.  
**Estimated probability of successful MVP delivery with disciplined scope and experienced team:** approximately **70–80%**.  
**Estimated probability of material delay when native apps, advanced optimization, wallet, insurance, accounting, and AI are forced into MVP:** approximately **85–90%**.

---

# 37. Recommended Implementation Phases

The phases describe implementation order, not release classification. All Mandatory MVP and Launch Entitlement capabilities required for commercial launch must complete before the launch gate, even when plan-gated.

## Phase 1 — Specification, Registries, and Control Foundation

- finalize terminology and identity domains;
- create role, permission, segregation-of-duty, onboarding, and landing registries;
- create state-machine, error-code, audit-event, release, entitlement, and route registries;
- finalize tenant/branch/organization/own-scope architecture;
- finalize billing modes, plans, prices, and migration maps;
- threat model, privacy model, data model, API/event contracts, and design system;
- establish semantic lint and capability-to-test traceability.

## Phase 2 — Identity, Merchant, Branch, Platform Billing, and Multi-Branch Foundation

- global identity and internal `platform_users` domain;
- Merchant Administrator self-registration and Magic Links;
- merchant membership/employment/role/invitation separation;
- one-branch and core Multi-Branch entity/authorization model;
- plans, entitlements, trial, grace, overdue, suspension, and recovery;
- M-Pesa platform billing and reconciliation;
- internal role onboarding, MFA, owner protection, and break-glass;
- audit foundation.

## Phase 3 — Customers, Organizations, Addresses, Catalogue, and Quotes

- Goods Sender and business-customer organization memberships/subroles;
- sender/recipient/address books and consent;
- Maps autocomplete/geocoding/validation;
- service catalogue, zones, pricing, goods rules;
- request and quote snapshots;
- quote approval workflow;
- preferred-personnel request/reservation separation.

## Phase 4 — HR, Fleet, Dispatch, Routing Basic, API/Webhooks, and Personnel PWA

- staff invitations, lifecycle, essential Starter HR, role grants;
- personnel/fleet eligibility and availability;
- assignment, reassignment, custody handover;
- `routing.basic`, route display, and manual stop sequencing;
- mobile-first PWA and offline command queue;
- core business-customer API clients, credentials, scopes, integration console;
- outbound webhooks, signatures, retries, usage logs, dead letters;
- core Multi-Branch switcher and cross-branch controls.

## Phase 5 — Proof, Tracking, Notifications, and Customer Finance

- secure recipient tracking;
- OTP/photo/signature/location proof;
- incidents, failures, returns, redelivery;
- customer M-Pesa/offline/COD/credit/split payments;
- invoices, allocations, receipts, refunds, disputes;
- Finance validation and maker/checker;
- SMS/email/notification delivery.

## Phase 6 — Compensation, Payout, Cash-Up, Locks, Audit, Reports, and Referral Integration

- compensation plans and append-only ledgers;
- payout segregation and per-item settlement;
- cash-up revisions and controlled financial reopen;
- full audit/masking and role dashboards;
- consolidated and branch-filtered reports/exports;
- Courier referral snapshots, factual product events, detached signatures, dead-letter reconciliation, and central monitoring;
- full launch acceptance, migration rehearsal, disaster recovery, and security review.

## Phase 7 — Post-MVP Advanced Scale

- full multi-vehicle route optimization;
- advanced forecasting and analytics;
- expanded accounting/ERP and partner connectors;
- advanced event families and higher quotas;
- native apps after PWA evidence;
- AI/telematics and other separately approved capabilities.

Core API/webhooks and core Multi-Branch controls are not deferred to Phase 7.

# 38. Consolidated Acceptance Criteria

The platform is acceptable only when all of the following are proven:

1. Courier by Citrus is identified as a Citrus Labs Limited product.
2. Merchant self-registration is the only merchant creation path.
3. Super Administrator cannot create the first Merchant Administrator.
4. Merchant operational and billing statuses are separate.
5. Trial, grace, suspension, and recovery work without data loss.
6. Billing recovery remains accessible during billing-only suspension.
7. Manual/fraud/legal/security suspension is not auto-cleared by payment.
8. Fixed, percentage, and fixed-plus-percentage modes are launch-capable.
9. Plan prices have one versioned source of truth.
10. No mid-cycle proration occurs.
11. Issued invoice snapshots do not change after price/rule changes.
12. Starter, Growth, Pro Branch, and Multi-Branch entitlements are enforced server-side.
13. Merchant Administrator is not an operational superuser.
14. Branch Manager owns branch catalogue, zones, calendar, and branch framework.
15. HR owns staff, eligibility, availability, and compensation.
16. Dispatch owns delivery creation, assignment, transfer, invoice creation, and default offline payment recording.
17. Finance owns validation, reconciliation, refunds, disputes, cash-up approval, payout settlement, and period locks.
18. Delivery Personnel are own-scope across every API and screen.
19. Personnel contact export is absent.
20. Merchant Audit is branch-scoped, masked, append-only, and business-record read-only.
21. Sender can create a Point A-to-Point B delivery.
22. Recipient can track and confirm without a full account.
23. Address autocomplete, pin, validation, distance, route, and ETA are supported.
24. Route/quote inputs are snapshotted.
25. Coordinated request, quote, payment-requirement, assignment, operational delivery, redelivery, dispute, and refund state machines cover the complete lifecycle without overwriting one another.
26. Invalid state transitions are blocked.
27. Eligible personnel/vehicle checks precede assignment.
28. Driver/rider PWA supports pickup, transit, proof, failure, return, and incident workflows.
29. Secure sender/recipient tracking respects privacy.
30. Proof methods are validated and privately stored.
31. OTPs are secure and not logged in plaintext.
32. Customer payments support configured integrated/offline/COD/split methods.
33. Offline maker/checker separation is enforced.
34. Integrated callbacks are signed, idempotent, and reconciled.
35. Receipts are generated automatically after cleared payment.
36. Refunds cannot exceed cleared amount.
37. COD is linked to delivery, collector, handover, cash-up, and Finance validation.
38. Compensation supports salary-only, commission-only, and salary-plus-commission.
39. Payout maker/checker and high-value approval are enforced.
40. Cash-up roles and terminal states are correct by plan.
41. Locked financial periods block mutation centrally.
42. Files are private by default and accessed through authorization/expiry.
43. Uploads are validated server-side.
44. No secrets appear in frontend assets or logs.
45. Tenant, branch, own-scope, entitlement, billing-state, and lock checks are server-side.
46. Audit logs contain actor, context, before/after, severity, and integrity hashes.
47. Unauthorized attempts are logged.
48. Every authenticated human account has a role-appropriate landing and get-started page; token-only Recipient Access has a delivery action page, and non-human identities are managed through authorized human consoles.
49. Responsive design uses CSS viewport rules.
50. The app remains usable at 200% zoom.
51. Whole-page horizontal scroll is absent for normal content.
52. Maps keys are restricted and usage monitored.
53. API/webhooks use scope, signatures, rate limits, idempotency, and logs.
54. Deactivation preserves historical operational/financial/audit records.
55. Original delivery features—requests, quotes, dispatch, fleet, tracking, proof, notifications, payments, reports, integrations, architecture, database, MVP, risks, and build phases—remain represented.
56. No known contradictory duplicate rule remains active for release after the versioned contradiction, schema, route, permission, state-machine, migration, and automated-test gates pass.

---



57. Human accounts, token access, merchant branches, business API identities, system identities, and internal role profiles are distinct.
58. Account taxonomy and account counts match all role, screen, schema, and impact registers.
59. Merchant Branch cannot authenticate or hold human security state.
60. HR owns routine operational staff lifecycle; Merchant Administrator emergency suspension preserves employment and compensation.
61. Goods Recipient Access receives a delivery action page rather than forced account onboarding.
62. Business Customer Integration Identity belongs to one active business organization and cannot authenticate interactively.
63. Sender authentication is merchant-scoped and does not leak cross-merchant membership.
64. Preferred-personnel selection creates a request/reservation rather than an assignment.
65. Reassignment is immutable assignment history and never an undefined physical status.
66. Request, quote, payment requirement, assignment, operational delivery, redelivery, dispute, and refund states are orthogonal.
67. Delivered operational state survives dispute, refund, chargeback, and compensation adjustment.
68. Quote approval authority and maker/checker rules are deterministic.
69. Compensation review, high-risk approval, acknowledgment, and activation are deterministic.
70. Billing lifecycle covers trial, active subscription, overdue, grace, suspension, and recovery.
71. Grace and billing suspension use one tested restricted-mode allowlist.
72. Billing mode has one effective-dated source of truth.
73. Recurring fixed subscription charge is not repeated per customer invoice.
74. Platform invoice status remains separate from payment-attempt outcomes.
75. Cash-up correction and terminal rejection are distinct.
76. Every launch capability has route/command, policy, UI where applicable, audit, error behavior, and tests.
77. Every feature has one release classification per release.
78. Courier registration accepts product-specific referral context without depending on central availability.
79. Courier stores only minimal referral snapshots and never stores full central referrer payout details.
80. Courier emits signed, versioned, idempotent product events through a transactional outbox.
81. Duplicate events do not duplicate attribution, qualification, reward, reversal, or payout.
82. Incorrect or conflicting product facts place central reward processing on hold without changing Courier’s authoritative billing record.
83. Product-specific attribution is the default and Courier attribution does not claim Servana or other product registrations.
84. Courier does not calculate or pay referrer rewards.
85. Central referral services do not directly infer delivery facts by reading Courier operational tables.
86. Central referral outage never prevents valid merchant registration; attribution remains pending and no reward is released prematurely.
87. Referral event dead letters are visible, auditable, retryable, and safely replayable.
88. Internal Citrus roles follow least privilege and mandatory MFA.
89. Terminology, enum, transition, route, and release-classification lint passes.
90. Contradiction resolution is versioned and conditional on all specified verification gates.


## 38.1 Contradiction-Resolution Acceptance Addendum

The platform is additionally unacceptable until all of the following are demonstrated:

101. `platform_owner` is the only highest Courier platform role key and Super Administrator is only its UI label.
102. No final Platform Owner can be removed and no owner can approve their own owner grant or break-glass access.
103. Internal platform identities are represented by the canonical `platform_users` schema and are absent from merchant staff queries.
104. Business-customer subroles have complete invitation, authentication, MFA, organization-scope, onboarding, deactivation, and final-administrator controls.
105. Every internal role has mandatory common and role-specific onboarding.
106. Central referral operations use separated product-scoped roles and maker/checker controls.
107. Quote commercial state and quote approval workflow are separate authorities.
108. Every declared lifecycle domain has a complete machine-readable transition registry.
109. Custody handover atomically supersedes the old assignment and accepts the new assignment; no transition targets a sentence.
110. Physical operational state remains delivered or returned after administrative closure, dispute, refund, or chargeback.
111. Invoice settlement, payment transaction, dispute, and refund authorities are independent and derived labels cannot be written.
112. Platform payment attempts contain no post-settlement refund state.
113. `delivery_requests` contains no ambiguous monolithic status and no current assignment fields.
114. Preferred-personnel preference/reservation cannot directly create or represent an assignment.
115. Percentage fee ledger entries contain no recurring fixed subscription charge or unsupported fixed transaction field.
116. Legacy identity/invitation/membership schema is removed or strictly read-only during a bounded migration.
117. One canonical client/credential/webhook schema exists per human-business or system-integration domain.
118. Canonical schema catalog contains no duplicate table declaration.
119. Sender authentication challenges are merchant-scoped and tenant-bound.
120. Every launch capability has a normative route or internal command; no launch contract is merely illustrative.
121. Generic delivery state mutation and generic financial reopen endpoints are absent.
122. Full multi-vehicle optimizer has no launch production route, entitlement, screen, worker, or promise.
123. Refund preparation and approval enforce configured segregation of duties.
124. API/webhooks and core Multi-Branch functions are implemented wherever sold at launch.
125. Starter can complete an end-to-end operational and financial workflow without a missing role.
126. Platform-billing payer authority is permission-driven and Dispatch is denied by default.
127. Payout runs enforce independent preparation/approval/settlement and per-item partial-failure truth.
128. Courier-owned entry-point configuration and central-owned campaign/reward configuration are unambiguous.
129. Referral events use minor units, neutral factual names, detached signatures, replay protection, and privacy-minimized payloads.
130. The final file contains one normative scope, no embedded competing correction scope, no stray unmatched quote, and valid numbering/fences.
131. Presence-only lint is supplemented by semantic artifact comparison and runtime proof.
132. No section claims complete registries unless the registry files exist, validate, and are release-linked.
133. Duplicate or semantically overlapping route writers are absent from the active route registry and OpenAPI.
134. Each of the forty-three resolution items in Section 47 identifies evidence, root cause, precise fix, affected users, tests, and demonstrated-resolution criteria.
135. The Section 48 publication gate passes for the same document and generated-artifact checksums.

# 39. Human, Token-Access, Machine-Identity, and Referral Impact Register

## 39.1 Product-Facing Human and Access Types

| Capability | Super Administrator | Merchant Administrator | Branch Manager | HR | Finance | Dispatch | Personnel | Support | Audit | Sender | Recipient Access |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Merchant creation | Govern after creation | Self-register only | None | None | None | None | None | None | Read audit only | None | None |
| Staff lifecycle | Internal platform roles only | Branch Manager/HR appointments, high-risk approval, emergency access suspension | View permitted status | Routine operational lifecycle | Own role only | Own role only | Own account only | Own role only | Read-only | None | None |
| Branch catalogue/calendar | Platform policy only | Read-only unless separately permissioned | Own assigned branches | View eligibility context | View financial context | Use approved catalogue | View assigned service context | Masked support view | Read-only | View offered services | Delivery-scoped only |
| Live assignment | No ordinary mutation | No default mutation | No default mutation | Eligibility only | No | Creates/transfers | Accepts/executes own | No | Read-only | Preference request only | No |
| Customer payment validation | No | No | No | No | Validates/reconciles | Records maker entry | COD evidence only | Handoff only | Read-only | Initiates/tenders | May tender COD/confirm handover; cannot validate |
| Compensation | Platform governance only | Views and high-risk approval | Read-only limited | Prepares plans/runs | Reviews, approves standard, settles | No | Own terms/earnings | No | Read-only masked | None | None |
| Referral attribution | Configures integration and views governance | Views own merchant attribution status only | No | No | No reward details | No reward details | No | Support handoff | Audit event only | May become central referrer through native entry point | None |
| Referral payout | Central platform role separation, not Courier merchant operation | No | No | No | No merchant Finance authority over central referrer payout | No | No | Handoff only | Courier integration audit only | Central referrer dashboard only | None |

## 39.2 External Machine-Identity Impact

A Business Customer Integration Identity belongs to one merchant and one business customer organization. It may use only granted scopes and branches, retrieve only organization-owned data, and cannot create staff, validate payment, access merchant reports, or create authoritative assignments. Billing suspension and entitlement are rechecked server-side. Every call is rate-limited and audited.

## 39.3 Internal System Identity Impact

System identities execute bounded callbacks, queues, schedulers, exports, scans, notifications, Maps requests, and Courier referral event publishing. They are non-interactive, environment-bound, least-privilege, rotation-capable, and incapable of human login.

## 39.4 Referral Change Impact by User

- **Prospective Merchant Administrator:** may enter a Courier-specific referral code or arrive through a signed/product link; registration continues if central validation is unavailable.
- **Merchant Administrator:** can view limited attribution status for its own merchant, but not referrer payment or cross-product earnings.
- **Platform Owner / Super Administrator (UI label):** configures Courier product integration, campaign availability, signing keys, event versions, monitoring, and governance; central campaign/reward configuration remains in the centralized platform.
- **Platform Integration Operator:** monitors outbox, signatures, delivery attempts, dead letters, version compatibility, and replay.
- **Platform Security and Risk Operator:** reviews self-referral, duplicate identity, suspicious device/business relationships, and event abuse.
- **Platform Billing and Reconciliation Operator:** resolves product-payment fact discrepancies and central reward/payout accounting handoffs without changing merchant-customer finance.
- **Platform Support Operator:** coordinates referral-support cases using masked product and central references.
- **Merchant Finance:** has no authority over central referrer payouts; it remains responsible for merchant-customer and merchant-to-Citrus financial controls.
- **Dispatch, Personnel, Branch Manager, HR, Merchant Support, Merchant Audit:** operational permissions do not expand because a merchant was referred.
- **Goods Sender:** may use native “Become a Referrer” entry points and then authenticates in the central referrer platform.
- **Goods Recipient Access:** unaffected by referral identity or payout functionality.
- **Central Referrer:** uses one central account, one secured payment profile, product-specific links/codes, consolidated dashboard, statements, support, and payouts.

---

# 40. Original Delivery-Scope Preservation Register

| Earlier named area | Preserved/rebuilt in this document |
|---|---|
| Point A-to-Point B objective | §§1, 5, 6, 8 |
| Super Administrator | §4.1, platform billing, reports, APIs, screens, tests |
| Transport provider | Replaced precisely by merchant role family in §4 |
| Sender | §4.10 and delivery/customer modules |
| Recipient | §4.11 and secure tracking/proof |
| Delivery lifecycle/statuses | §5 with expanded state machine |
| Account/tenant management | §§2, 4, 16, 25, 26 |
| User management | §§4, 24–29 |
| Delivery request | §§5–6 |
| Goods fields/restricted goods | §6 |
| Quotation/pricing | §7 |
| Dispatch/assignment | §8 and §4.6 |
| Driver/delivery agent | §4.7 and §28.8 |
| Live tracking | §8.7 |
| Proof of delivery | §9 |
| Notifications | §19 |
| Payments/billing | §§10–18 |
| Fleet | §8.5 |
| Customer management | §6 and §4.10–4.11 |
| Reports/analytics | §22 |
| Google Maps | §20 |
| Payment gateway | §§10, 15, 21 |
| SMS/WhatsApp | §§19, 21 |
| Email | §21.4 |
| File storage | §§2.11, 21.5, 30.5 |
| Accounting/ERP | §21.6 |
| API/webhooks | §§21.7, 27 |
| Role functional requirements | §4 plus impact matrix |
| Security | §§26, 30, 33 |
| Performance | §31 |
| Responsiveness | §29 |
| Accessibility | §29.3 |
| Backend/frontend/realtime | §24 |
| Database entities | §25 |
| MVP/post-MVP | §35 |
| Risks/likelihoods | §36 |
| Success criteria | §38 |
| Build priority | §37 |

---

# 41. Servana Operating-Model Feature Preservation Register

The following definitive operating controls from the upgraded Servana scope have been domain-adapted rather than omitted:

1. multi-tenant isolation;
2. separate operational and billing status;
3. subscription-first billing;
4. fixed/percentage/fixed-plus-percentage launch modes;
5. service-fee tiers when percentage is active;
6. versioned plan-price source of truth;
7. Starter/Growth/Pro Branch/Multi-Branch entitlements;
8. promotions and free periods;
9. trial, read-only grace, suspension, shared overdue engine;
10. M-Pesa platform payment and recovery;
11. self-registration-only merchant creation;
12. role-specific landing/get-started pages;
13. Merchant Administrator account-owner boundary;
14. Branch Manager catalogue ownership;
15. HR staff/eligibility/compensation ownership;
16. Finance maker/checker control;
17. automatic receipt after validation;
18. period-lock central service;
19. compensation models, ledgers, payout runs, statements, queries;
20. soft deactivation/deletion semantics;
21. preferred personnel fee;
22. personnel own-scope and no contact export;
23. in-platform SMS with branch billing;
24. branch-scoped masked Audit;
25. append-only tamper-evident logs;
26. private storage and signed access;
27. server-side upload validation;
28. no frontend secrets or log credentials;
29. responsive CSS, 200% zoom, no page overflow;
30. neutral policy-protected payout routes;
31. structured promotion targeting;
32. background jobs and comprehensive automated testing.

---

# 42. Semantic Contradiction-Resolution Verification

Contradiction resolution is verified semantically, not by checking that headings or keywords exist. Every gate runs against the approved scope checksum, generated registries, schema migrations, OpenAPI, event schemas, policy code, user-interface capability manifest, and automated test evidence.

## 42.1 Mandatory Semantic Gates

1. Exactly one canonical highest Courier platform role key exists: `platform_owner`. “Super Administrator” is a UI label only.
2. Account, access-mode, role-grant, membership, employment, organization, machine-identity, token, and credential concepts are separated in schema and documentation.
3. Every state transition target exists in its domain enum and every enum value has defined entry/exit semantics.
4. No operational delivery transition targets `closed`; administrative closure is represented in `record_lifecycle_status`.
5. No mutable invoice or payment-attempt state independently duplicates dispute or refund authority.
6. No generic delivery status field or generic state-mutation route exists in the launch contract.
7. No request-level preferred-personnel field is treated as an assignment source of truth.
8. Fixed recurring subscription charges cannot enter the percentage transaction-fee ledger.
9. Legacy identity, invitation, membership, API-client, credential, and webhook tables have one canonical replacement or an explicitly read-only compatibility view.
10. Sender authentication is tenant-resolved before challenge issuance and verification.
11. Financial-period reopen uses separate request, approval, execution, cancellation, and closure commands.
12. Full multi-vehicle optimization is absent from launch production routes and entitlements unless a later separately approved release manifest activates it.
13. Core API/webhooks and core Multi-Branch capabilities are implemented at launch wherever sold as launch entitlements.
14. Starter can complete staff invitation, assignment, proof, invoicing, payment, and financial control without requiring an unavailable role.
15. Platform-billing payment authority is policy-driven and cannot silently extend to Dispatch.
16. Refund, compensation, payout, period-reopen, platform-owner, break-glass, campaign, reward-adjustment, and high-risk restriction workflows enforce maker/checker where required.
17. Courier and central Refer & Earn permissions remain product-scoped and independently authorized.
18. Every money field and event uses `_minor` plus currency; example values respect currency minor units.
19. Referral factual events use neutral fact names and do not claim central qualification or reward decisions.
20. Product-event signatures use the detached signature profile only; message-body signature fields are forbidden.
21. The document contains one normative scope and one non-normative evidence annex, not a second embedded scope with precedence power.
22. Every launch capability maps to one release class, one entitlement rule where applicable, one route/command, one policy, one screen or machine interface, one audit rule, and one test family.

## 42.2 Structural and Generated-Artifact Gates

The publication pipeline must:

- parse every Markdown heading and reject broken numbering or unmatched fences;
- generate and validate OpenAPI from the canonical route registry;
- validate state registries against enums, permissions, evidence schemas, error codes, and audit-event names;
- validate database migrations against the canonical schema catalog and deprecation map;
- validate that derived projections have no write handlers;
- validate that every active permission appears in the permission registry and at least one authorization test;
- validate that every role has landing, onboarding, session, and deactivation requirements or an explicit non-human/token-access exemption;
- validate that release-class and entitlement manifests contain no contradictory classifications;
- validate event schemas, canonicalization, detached-signature test vectors, replay protection, and backward-compatibility policy;
- compare route definitions for semantic duplicates even when paths differ;
- compare table/field definitions for duplicated authority even when names differ;
- reject deprecated route/table use after the declared removal version;
- produce a checksum-linked validation report retained with the release evidence.

## 42.3 Runtime Proof Gates

Required runtime evidence includes:

- cross-tenant, cross-branch, cross-organization, and own-scope denial tests;
- last-owner, final-organization-administrator, self-approval, and incompatible-role protection;
- idempotent retry and stale-version conflict tests for every financial and lifecycle command;
- transaction rollback tests proving no partial state, ledger, audit, outbox, notification, or provider-attempt effect;
- dependency outage, timeout, malformed callback, duplicate callback, out-of-order callback, and replay tests;
- migration reconciliation totals, orphan checks, uniqueness checks, and rollback rehearsal;
- locked-period denial and controlled-reopen tests;
- offline Personnel PWA conflict/recovery tests;
- restricted-billing-mode allowlist parity tests for UI, API, workers, and machine clients;
- privacy and masking tests for support, audit, executive, recipient, and integration surfaces;
- backup restoration, key rotation, secret revocation, and disaster-recovery exercises.

## 42.4 Gate Failure Handling

A failed Critical or High semantic gate blocks publication and deployment. A Medium or Low gate requires an approved, time-bounded exception containing owner, risk, compensating control, affected users, removal date, and acceptance authority. Exceptions cannot legalize a known tenant-isolation, authorization, monetary-integrity, credential, signature, or financial-ledger contradiction.

The validation service returns `409 specification_gate_failed` for a release candidate with unresolved contradictions and attaches only safe evidence identifiers. CI reports list the exact artifact and rule but never disclose production secrets or personal data.

## 42.5 Demonstrated Resolution Record

The final evidence bundle contains:

- scope version and SHA-256 checksum;
- generated-artifact checksums;
- migration reconciliation report;
- semantic-lint report;
- route and capability traceability report;
- state-machine validation report;
- authorization and segregation-of-duty report;
- event-contract/signature report;
- security and privacy test report;
- failed-gate exceptions, where permitted;
- named approval from Product, Architecture, Security, Finance/Compliance, Engineering, QA, and Operations.

A scope statement such as “all contradictions are resolved” is valid only when this bundle passes for the same release candidate.

# 43. Final Product Positioning Statement

> **Courier by Citrus, a product of Citrus Labs Limited, is a secure multi-tenant logistics operating platform that enables courier and delivery merchants to self-register, choose a subscription plan, establish branches, manage staff and fleet, receive Point A-to-Point B delivery requests, validate addresses, calculate routes and prices, dispatch eligible personnel and vehicles, provide live tracking, capture proof, invoice and reconcile customer payments, compensate personnel, close branch finances, audit operations, and recover platform access through automated M-Pesa billing—all through role-specific, branch-aware, mobile-responsive web experiences.**



---

# 44. Preserved Realism Assumptions and Likelihoods

The earlier delivery scope contained useful probability judgements. They are retained here as planning assumptions rather than guarantees:

| Planning judgement | Approximate likelihood | Scope consequence |
|---|---:|---|
| A separated internal merchant role model becomes necessary beyond a one-person operation | 85% | The split is implemented from the architecture stage instead of being retrofitted after data and permissions are entangled. |
| A full persistent recipient account is required in the first MVP | 35% | Launch remains secure-link/OTP-first. |
| A secure recipient tracking link is required in the first MVP | 95% | Recipient tracking is mandatory launch scope. |
| Manual quotation is needed early | 80% | Manual and basic automatic quotes are both included. |
| Fully automatic pricing works perfectly at launch without sustained calibration | 30% | Quote snapshots, manual override, and approval remain essential. |
| A desktop-only workflow fails field delivery personnel | 90% | Mobile-first PWA is mandatory. |
| M-Pesa is essential in Kenya/East Africa | 85% | Merchant platform billing and initial customer-payment integration prioritize M-Pesa. |
| Full route optimization is required in the first MVP | 40% | Point A-to-B route/ETA is launch scope; full optimization is advanced/entitled. |
| Route optimization becomes necessary after multi-vehicle growth | 80% | Data model and API extension points are launch-ready. |
| Accounting/ERP integration is required in MVP | 20% | Export-ready financial records are launch scope; direct connectors may follow. |
| Accounting/ERP integration becomes important for larger business customers | 65% | Connector architecture is preserved. |
| B2B API/webhook access becomes important for serious growth | 75% | API clients, scopes, webhooks, retries, and logs are specified. |
| Polling is enough for a disciplined early MVP | 70% | Polling may be used selectively, while stale-state-sensitive actions retain stronger coordination. |
| WebSockets/realtime become necessary after scaling | 75% | Realtime architecture is preserved and introduced by operational need. |
| Manual dispatch stops scaling after meaningful growth | 70% | Batch assignment, route groups, and optimization extension points are included. |
| Scope creep occurs without a locked delivery core | 80% | MVP remains centered on request → quote → payment/confirmation → assignment → pickup → transit → delivery → proof → reconciliation. |
| Disciplined MVP delivery succeeds | Approximately 75% | Assumes experienced implementation, test discipline, and scope control. |
| Material delay occurs when advanced optimization, native apps, wallet, accounting, insurance, and advanced analytics are forced into MVP | Approximately 85% | Those capabilities remain phased unless separately approved. |

These percentages are informed planning estimates, not contractual performance guarantees.

---

# 45. External Technical Reference Links Preserved from the Earlier Delivery Scope

1. [Google Route Optimization API overview](https://developers.google.com/maps/documentation/route-optimization/overview)
2. [Google Maps JavaScript API overview](https://developers.google.com/maps/documentation/javascript/overview)
3. [Google Routes API overview](https://developers.google.com/maps/documentation/routes/overview)
4. [Google Geocoding API overview](https://developers.google.com/maps/documentation/geocoding/overview)
5. [Google Places Autocomplete](https://developers.google.com/maps/documentation/places/web-service/place-autocomplete)
6. [Google Address Validation API overview](https://developers.google.com/maps/documentation/address-validation/overview)
7. [Google Maps Platform API security best practices](https://developers.google.com/maps/api-security-best-practices)

These links are implementation references, not a substitute for confirming current provider pricing, availability, quotas, terms, and API versions during implementation.

---

*End of authoritative upgraded Courier by Citrus platform project scope.*

---

# 46. Normative Citrus Refer & Earn Compatibility Specification

Courier by Citrus integrates independently with the centralized Citrus Labs Refer & Earn platform. Courier owns product facts and product-native entry points. The central platform owns referrer identity, attribution decisions, campaign evaluation, rewards, holds, reversals, payout, fraud, support, statements, and cross-product reporting. Neither product may obtain the other's authority merely because the same human holds a high-level role in both products.

## 46.1 Central Referral Human and Machine Authority

The central platform defines at minimum these product-scoped human roles:

1. Referral Platform Owner.
2. Campaign Administrator.
3. Referral Qualification Reviewer.
4. Referral Billing and Payout Operator.
5. Referral Fraud and Risk Operator.
6. Referral Support Operator.
7. Referral Auditor.
8. Executive Read-Only.

Campaign activation, manual attribution correction, qualification override, reward adjustment, fraud disqualification, payment-method override, payout approval, payout execution, and reversal use separately permissioned commands and threshold-based maker/checker controls. A campaign creator cannot be the sole activator. A reward-adjustment maker cannot approve the same adjustment. A high-risk payout preparer cannot execute the same run. Referral Auditor is immutable read-only.

Courier documents these roles as an external dependency only. Courier does not store them in merchant-user tables. A Courier `platform_owner` has no implicit central referral role. A Central Referral Platform Owner has no implicit Courier platform or merchant role.

Machine-to-machine access uses product registration, service identity, scoped credentials, key version, and explicit event permissions. Credential material is encrypted, displayed only once where applicable, rotated, revocable, and fully audited.

## 46.2 Product and Central Systems of Record

Courier is authoritative for:

- prospective Merchant Administrator registration facts;
- merchant tenant identity and verified registration state;
- selected subscription plan and billing period;
- Courier platform invoices, payment attempts, allocations, and billing status;
- merchant operational status;
- branch and delivery activity facts;
- product-specific factual activity thresholds;
- local referral-code snapshot and central attribution reference;
- product-event creation, outbox status, delivery attempts, and acknowledgements.

The central platform is authoritative for:

- Central Referrer identity and authentication;
- universal and product-specific referral codes and links;
- campaign versions, eligibility rules, qualification decisions, and reward formulas;
- canonical referral attribution;
- reward, hold, release, adjustment, reversal, and payout ledgers;
- referrer payment methods and compliance data;
- fraud, duplicate, self-referral, collusion, and abuse decisions;
- referral support cases and statements;
- cross-product acquisition and liability reporting.

Courier must not calculate or pay referral rewards, copy full referrer payment details, mutate a central qualification result, or directly read central private tables. The central platform must not directly query Courier operational tables to infer facts. It consumes authenticated factual events and documented query endpoints.

## 46.3 Registration Continuity and Attribution

Merchant registration must continue when the central referral service is unavailable. Courier validates locally available syntax and records a minimal referral snapshot with one of:

```text
not_supplied
pending_central_validation
confirmed
rejected
expired
conflicted
manual_review_required
```

A local snapshot contains only the submitted code or irreversible normalized hash where policy permits, product identifier, campaign hint where known, request timestamp, registration correlation identifier, and central references after confirmation. It does not contain reward amount, payout method, or central fraud notes.

The registration transaction never waits indefinitely on the central service. A timeout stores `pending_central_validation`, commits the merchant registration, and queues an idempotent reconciliation command. No reward can be released until the central platform confirms canonical attribution and later qualification.

A confirmed attribution is immutable from ordinary Courier UI. A correction requires a central privileged workflow, reason, evidence, maker/checker where configured, immutable old/new references, and a Courier synchronization event.

Duplicate, self-referral, mismatched-product, expired-code, ambiguous-code, and conflicting-attribution responses are safely surfaced without revealing another person's identity or campaign-sensitive logic.

## 46.4 Factual Courier Product Events

Courier emits facts, not central decisions. The launch event vocabulary includes:

```text
courier.merchant_registered.v1
courier.merchant_email_verified.v1
courier.merchant_setup_completed.v1
courier.subscription_started.v1
courier.subscription_invoice_issued.v1
courier.subscription_payment_cleared.v1
courier.subscription_became_overdue.v1
courier.subscription_suspended_for_billing.v1
courier.subscription_recovered.v1
courier.merchant_activity_threshold_met.v1
courier.merchant_activity_threshold_no_longer_met.v1
courier.merchant_suspended.v1
courier.merchant_deactivated.v1
courier.referral_snapshot_reconciled.v1
```

The neutral event `merchant_activity_threshold_met` means only that a versioned Courier-owned factual threshold was met. It does not mean the referral is qualified, eligible, payable, or fraud-cleared. Central services make those decisions.

Each event body uses the common envelope:

```json
{
  "event_id": "evt_01...",
  "event_type": "courier.subscription_payment_cleared.v1",
  "occurred_at": "2026-06-27T12:00:00Z",
  "producer": "courier_by_citrus",
  "producer_environment": "production",
  "schema_version": 1,
  "aggregate_type": "merchant_subscription",
  "aggregate_id": "sub_01...",
  "aggregate_version": 9,
  "merchant_id": "mrc_01...",
  "central_attribution_id": "atr_01...",
  "idempotency_key": "courier:subscription-payment:pay_01...:cleared",
  "data": {
    "platform_invoice_id": "pinv_01...",
    "payment_id": "ppay_01...",
    "amount_minor": 500000,
    "currency": "KES",
    "cleared_at": "2026-06-27T11:59:58Z"
  }
}
```

KES 5,000.00 is represented as `500000` minor units. No event uses an ambiguous money field named `amount`.

Events contain only required product facts. They exclude sender/recipient addresses, delivery proof, staff compensation, merchant payment secrets, referrer payout details, and unrestricted free text.

## 46.5 Detached Signature Profile

Product-event bodies do not contain a `signature` field. Courier signs the exact transmitted body using a detached Ed25519 signature.

Required HTTP headers are:

```text
Citrus-Event-Id
Citrus-Event-Timestamp
Citrus-Product-Id
Citrus-Key-Id
Citrus-Signature-Algorithm: ed25519
Citrus-Content-Digest: sha-256=:<base64 digest>:
Citrus-Signature: :<base64 signature>:
```

The signed message is the UTF-8 byte sequence:

```text
<Citrus-Event-Timestamp>\n<Citrus-Event-Id>\n<SHA-256 hex digest of the exact HTTP body bytes>
```

The receiver verifies product identity, key status, algorithm, timestamp skew, event-ID replay, content digest, and detached signature before parsing or processing the event. It stores event ID and body digest for idempotency. The same event ID with the same digest is an idempotent replay; the same event ID with a different digest is a security incident and returns `409 event_id_payload_conflict`.

Canonical JSON is recommended for reproducibility but signature verification always covers the exact transmitted bytes. Compression and intermediary transformations must preserve the signed representation contract or be disabled. Key rotation supports overlapping verification windows without overlapping signing authority beyond the declared cutover.

## 46.6 Delivery, Retry, Ordering, and Reconciliation

Courier commits the business state, audit record, and transactional outbox record atomically. An asynchronous publisher sends events. Delivery uses exponential backoff with jitter, bounded attempts, circuit breaking, observability, and a dead-letter queue. Provider outage never rolls back an already committed Courier business fact.

Consumers process idempotently by `event_id` and validate `aggregate_version`. Out-of-order events are held, safely ignored when obsolete, or reconciled through an authorized factual query. No consumer may infer missing facts by querying private Courier tables.

Manual dead-letter replay requires permission, reason, idempotency review, and audit. Replaying does not mint a new fact unless the original payload was invalid and a correction event is required. A corrected fact uses a new event ID and references the superseded event where the schema supports it.

Daily reconciliation compares Courier outbox states, central acknowledgements, confirmed attribution references, and central processing status without copying reward or payout ledgers into Courier. Mismatches create owned incidents for Platform Integration Operator and the corresponding central integration role.

## 46.7 Error Handling and Edge Cases

- Invalid detached signature returns `401 invalid_event_signature`; no domain processing occurs.
- Unknown or revoked key returns `401 unknown_or_revoked_key`.
- Excessive timestamp skew returns `401 event_timestamp_out_of_window` unless an explicitly governed replay mechanism applies.
- Unsupported schema version returns `422 unsupported_event_schema_version` and remains retryable only after compatibility deployment.
- Valid duplicate event returns the same acknowledgement without duplicate effects.
- Conflicting duplicate event ID returns `409 event_id_payload_conflict` and raises a security alert.
- Central dependency timeout during registration stores pending attribution and never blocks tenant creation.
- A central rejection after local registration updates only the referral snapshot; it never deactivates the merchant.
- Merchant deactivation does not erase attribution, reward, payout, or audit history.
- Billing recovery emits a factual recovery event but cannot clear central fraud or reward holds.
- Late payment callbacks are processed according to the platform billing state and allocation registry, then emitted once through the outbox.
- Product events containing prohibited personal data fail pre-publish schema/privacy validation and enter a quarantined queue.
- Central role authorization failure cannot be bypassed using a Courier token, platform-owner session, or shared email identity.

## 46.8 Required Tests and Demonstrated Resolution

The integration test suite proves:

- registration continuity during central outage;
- eventual attribution confirmation/rejection/conflict handling;
- no local reward calculation or payout path;
- no central direct operational-table access;
- detached signature positive/negative test vectors;
- key rotation, revocation, timestamp, replay, digest mismatch, and conflicting-ID handling;
- event idempotency and aggregate ordering;
- outbox atomicity, retries, dead-letter replay, and reconciliation;
- minor-unit amount correctness;
- neutral factual event naming;
- product-scoped role isolation;
- central campaign/reward/payout maker-checker controls;
- privacy minimization and audit evidence.

Launch acceptance requires approved schemas, public keys, test vectors, incident ownership, reconciliation dashboards, recovery runbooks, and a compatibility agreement versioned by both Courier and the central platform.

# 47. Non-Normative Manifesto Resolution Evidence and Affected-User Register

This annex preserves the complete evidence, analysis, implementation requirements, error handling, edge cases, migration requirements, and acceptance requirements that drove the regenerated normative scope. It is intentionally non-normative: the owning sections identified under each item are authoritative. The annex cannot reintroduce a deprecated route, table, state, role, release class, or ownership rule.

Each item follows the manifesto by recording the proven problem, explicit root cause, precise integrated location, affected users, required tests, and the evidence needed to demonstrate resolution. The original contradiction-resolution content is retained in full beneath those controls.


## 47.1 Duplicated Super Administrator and Platform Owner identity

**Severity:** High

### How the defect appears

The scope defines a Super Administrator as the Citrus Labs platform-owner role and later defines a separate “Platform Owner / Super Administrator” inside the internal-role taxonomy. The wording leaves four incompatible interpretations: one role with two labels, two levels in a hierarchy, one account plus one permission profile, or two separate accounts with overlapping authority.

### Authoritative correction

There is one internal human identity domain and one canonical highest privilege role:

```text
user_identity
└── platform_user
    └── platform_role_grant
        └── role_key = platform_owner
```

`Super Administrator` is the Courier product UI label for the `platform_owner` role. It is not another identity, role, table, or privilege tier. All normative schema, policy, audit, and API identifiers use `platform_owner`. UI copy may display “Super Administrator.”

A platform user can hold additional product-scoped grants, but `platform_owner` does not automatically grant central Refer & Earn payout authority or merchant-user authority.

### Required implementation changes

Add `platform_users`, `platform_roles`, `platform_role_grants`, and product/scope columns. Define permissions such as `platform.configuration.manage`, `platform.internal_users.manage`, `platform.billing_policy.manage`, and `platform.break_glass.approve`. High-risk configuration changes use maker/checker approval where configured.

Break-glass access is not ordinary `platform_owner` access. It requires a separate short-lived `platform_break_glass_session`, reason, ticket/reference, step-up authentication, approver, expiration, and post-use review.

No platform owner may deactivate or remove the last active platform owner. A user may not approve their own new owner grant, their own break-glass request, or their own deactivation reversal.

Replace every ambiguous phrase with one of the following:

- “Platform Owner (`platform_owner`), displayed as Super Administrator in Courier.”
- “Courier Super Administrator UI.”
- “Central Refer & Earn role,” where the authority belongs to the separate product.

### Error handling and edge cases

A duplicate active grant returns `409 duplicate_active_role_grant`. Removing the last active owner returns `422 last_platform_owner_required`. Self-approval returns `403 separation_of_duty_violation`. An expired break-glass session returns `401 break_glass_session_expired` and revokes any derived session. A platform user with both Courier and central-referral grants is authorized independently for each product; absence of one grant returns `403 product_role_required` without implying the other product's data exists.

### Acceptance requirements

There must be one role key for highest Courier platform authority, one UI alias, one policy source, and one audit vocabulary. Automated tests must prove that no `super_administrator` and `platform_owner` grants can coexist as separate equivalent roles and that the last-owner and self-approval protections are enforced transactionally.

---

### 47.1.A Explicit Root Cause

The same highest-privilege concept was modelled once as a product account and again as an internal role, without a canonical identity/role key or hierarchy.

### 47.1.B Authoritative Integration Location

Sections 0.4, 4.1, 4.1A, 25 and 42. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.1.C Users and Systems Affected

Platform Owner, all internal Citrus platform users, Platform Security and Risk, Platform Auditor, Executive Read-Only, release administrators.

### 47.1.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.2 Misleading project-wide account count

**Severity:** Medium

### How the defect appears

The scope says there are eleven product-facing human/customer account or access types, but the number includes recipient token access while excluding internal platform roles, business-customer subroles, central-referral accounts, and machine identities. A reader asked for “all account users” can therefore produce several defensible but incompatible totals.

### Authoritative correction

Delete any statement presenting eleven as the total number of project-wide accounts. Replace it with the canonical inventory in this specification and use the exact phrase:

> Courier exposes eleven principal human/customer access classifications: ten authenticated human role profiles and one delivery-scoped recipient access mode.

Then separately state the seven internal role profiles, four business-customer subroles, external central-referral identities, and two non-human identity classes.

### Required implementation changes

Documentation, onboarding, permissions, analytics, admin screens, and test fixtures must label each record with an `identity_domain`, `actor_type`, and `role_key`. Metrics must distinguish “active identities,” “active role grants,” “organization memberships,” “token-access sessions,” and “machine clients.”

Do not derive account totals from role rows because one identity may hold several roles. Do not count a recipient token as a human identity. Do not count a branch, invitation, credential, referral attribution, or API key as an account.

### Error handling and edge cases

Reporting APIs reject an ambiguous dimension such as `count=accounts` with `422 ambiguous_metric` unless the caller chooses `human_identities`, `role_grants`, `memberships`, `token_access_sessions`, or `machine_identities`. A user with two merchant memberships is one global identity and two tenant memberships. A person who is both a platform user and a sender is one underlying identity only when the identity-linking policy proves they are the same person; otherwise the systems must not merge records heuristically.

### Acceptance requirements

All scope references, diagrams, schemas, API docs, UI labels, and analytics must use the same taxonomy. Automated documentation lint must fail when “eleven accounts” or “eleven account users” appears without the ten-plus-one qualification.

---

### 47.2.A Explicit Root Cause

Human identities, role profiles, organization subroles, token access, external accounts, entities, and machine identities were forced into one misleading count.

### 47.2.B Authoritative Integration Location

Sections 0.4, 3, 4, 39 and 42. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.2.C Users and Systems Affected

All human users, recipient-access users, business-customer members, machine-identity administrators, analytics/report consumers, product and support teams.

### 47.2.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.3 Missing internal Citrus identity schema

**Severity:** Critical

### How the defect appears

The scope says internal Citrus personnel belong to `platform_users`, yet the primary data-model inventory contains only generic and merchant-oriented identity tables. The missing platform tables make the claimed separation non-executable.

### Authoritative correction

Use the following canonical internal identity model:

```text
user_identities
platform_users
platform_roles
platform_permissions
platform_role_permissions
platform_role_grants
platform_user_mfa_methods
platform_user_sessions
platform_user_status_history
platform_user_security_events
platform_break_glass_requests
platform_break_glass_sessions
platform_privileged_action_approvals
```

`user_identities` contains the global authentication subject and verified contact identifiers. `platform_users` contains the internal employment/engagement profile. `platform_role_grants` contains product, scope, effective dates, grantor, approver, reason, and status.

`platform_users` must not contain `merchant_id`, `branch_id`, merchant employment, or merchant-role membership. Database constraints and application policies must prevent a platform role grant from being used as merchant membership.

### Required implementation changes

Add mandatory MFA enrollment for all active internal users, step-up authentication for high-risk actions, session revocation, device/session visibility, effective-dated grants, immediate suspension, and append-only privileged-action audit. Plaintext recovery codes or secrets must never be retrievable.

Internal-user routes must operate under `/api/v1/platform/internal-users` or a private administration boundary. Merchant routes must reject platform-only sessions unless an explicitly approved, separately audited support capability exists. Launch scope forbids merchant impersonation.

### Error handling and edge cases

Creating a platform user with a merchant membership payload returns `422 invalid_identity_domain`. Using a merchant session on a platform route returns `403 platform_identity_required`. A suspended internal user loses active sessions and unused authentication challenges atomically. Failed MFA enrollment prevents activation. Key or MFA recovery requires two-person approval for platform owners. Orphan role grants, overlapping contradictory grants, and grants without effective dates fail database validation.

### Migration and acceptance

Profile existing internal users, map them to `user_identities` and `platform_users`, migrate role assignments, revoke shared accounts, and preserve old audit actor references through immutable legacy identifiers. Tests must prove tenant isolation, mandatory MFA, session revocation, last-owner protection, and absence of internal users from merchant staff queries.

---

### 47.3.A Explicit Root Cause

The prose declared an internal identity domain but the schema inventory remained merchant-centric and therefore could not enforce the boundary.

### 47.3.B Authoritative Integration Location

Sections 4.1A and 25. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.3.C Users and Systems Affected

All internal Citrus platform users, Security, Support, Billing, Integration, Audit, identity administrators, migration operators.

### 47.3.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.4 Under-specified business-customer human subroles

**Severity:** High

### How the defect appears

The ownership diagram lists Business Customer Administrator, Operator, Finance Viewer, and Integration Administrator, but the scope does not fully define their creation, authentication, organization membership, permissions, suspension, deactivation, landing pages, onboarding, data scope, or maker/checker controls.

### Authoritative correction

These are organization-scoped role grants applied to a Goods Sender identity. Use:

```text
sender_identities
business_customer_organizations
business_customer_memberships
business_customer_role_grants
business_customer_invitations
business_customer_membership_status_history
```

The membership binds one sender identity to one merchant-owned business customer organization. Every query is constrained by both `merchant_id` and `business_customer_organization_id`.

Role authority is:

- **Business Customer Administrator:** manages organization profile, members, addresses, recipients, policy defaults, and delegation. It cannot create merchant staff, validate merchant payments, or access another customer organization.
- **Business Customer Operator:** creates and manages the organization's delivery requests, quotes, recurring routes, tracking, support cases, and permitted cancellations.
- **Business Customer Finance Viewer:** reads organization invoices, payment allocation, receipts, credit-account statements, and exports. It cannot approve refunds, validate merchant payments, or change merchant finance records.
- **Business Customer Integration Administrator:** manages organization-owned API clients, scopes, credentials, and webhooks. It cannot retrieve old plaintext secrets or grant scopes beyond organization entitlement.

### Authentication and onboarding

Authentication is merchant-scoped. Invitations contain merchant, organization, intended role, expiry, nonce, and inviter. Business Customer Administrator and Integration Administrator require MFA; Finance Viewer uses MFA or step-up for sensitive exports; all roles require verified email or configured verified phone.

Each role receives a role-specific landing page and resumable onboarding modules. Deactivation revokes sessions and organization access but preserves request, invoice, receipt, audit, and integration history.

### Error handling and edge cases

An invitation accepted under the wrong merchant returns `404 invitation_not_found`. Duplicate active membership returns `409 membership_exists`. Removing the final Business Customer Administrator is blocked until another active administrator is appointed. A suspended organization blocks all members and machine clients without deleting them. A member belonging to multiple organizations must explicitly select organization context; no default may leak records between organizations. Integration credentials are revoked when the owning organization is suspended, while delivery webhooks already queued follow a documented cancellation/retry policy.

### Acceptance requirements

Permission tests must cover every role, cross-organization denial, merchant isolation, invitation replay, final-administrator protection, MFA enforcement, export masking, and deactivation history preservation.

---

### 47.4.A Explicit Root Cause

A diagram introduced organization roles without carrying them through identity, membership, authentication, permission, UI, lifecycle, and test contracts.

### 47.4.B Authoritative Integration Location

Sections 3.2, 4.12, 21.7, 25 and 28.13. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.4.C Users and Systems Affected

Goods Sender identities belonging to business organizations, Business Customer Administrators/Operators/Finance Viewers/Integration Administrators, Merchant Administrators, Support, Audit.

### 47.4.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.5 Missing get-started flows for internal platform roles

**Severity:** High

### How the defect appears

The scope promises a role-specific guided get-started experience for every authenticated human account, but only supplies a broad Super Administrator checklist for internal staff. Six internal roles therefore violate the universal UX requirement.

### Authoritative correction

Retain one common platform-employee onboarding framework with mandatory security modules and role-specific modules. Completion is tracked per module and per active role grant.

Common modules include acceptable-use acknowledgement, MFA, recovery setup, device/session review, data-classification training, audit notice, incident reporting, and product-bound access explanation.

Role-specific modules are:

- Billing and Reconciliation: invoice/payment reconciliation, exception queues, reversal controls, maker/checker, and settlement evidence.
- Support: masking, safe troubleshooting, escalation, no-impersonation boundary, and referral handoff.
- Security and Risk: case handling, restriction maker/checker, evidence, proportionality, and appeal/review.
- Integration: key custody, event schemas, signing, retries, dead letters, secret rotation, and provider incident response.
- Auditor: append-only records, export governance, evidence integrity, and prohibited mutation.
- Executive Read-Only: aggregate metrics, privacy limits, export restrictions, and interpretation caveats.

### Required implementation changes

Create `onboarding_modules`, `role_onboarding_requirements`, and `user_onboarding_progress`. A new role grant activates only after mandatory modules are complete, except emergency access explicitly governed by break-glass policy. A role change adds missing modules without resetting unrelated completed training. Material policy changes can require re-acknowledgement.

### Error handling and edge cases

A user attempting privileged work before completion receives `403 onboarding_incomplete` with safe required module identifiers. A module removed from policy must not leave users permanently blocked. Revoked roles hide their modules but retain completion audit. Temporary emergency access records the skipped module and creates a mandatory post-event review. Inaccessible training content must have an alternate accessible format and support path.

### Acceptance requirements

Every active internal role must resolve to at least one role-specific module set, and automated tests must fail when a new role is created without onboarding requirements or an explicitly approved exemption.

---

### 47.5.A Explicit Root Cause

The universal role-specific onboarding promise was implemented only for the broad owner role and not derived from the actual internal role registry.

### 47.5.B Authoritative Integration Location

Sections 3.2, 4.1A and 42. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.5.C Users and Systems Affected

Every internal Citrus role, Security, HR/people operations, role-grant administrators, Audit.

### 47.5.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.6 Under-specified central Refer & Earn staff taxonomy

**Severity:** High

### How the defect appears

The scope defines a Central Referrer and a central Super Administrator area, while the central platform owns campaign configuration, qualification review, reward calculation, fraud, payout, reconciliation, support, audit, reversals, and payment-method governance. Leaving these functions under one super role recreates concentrated authority.

### Authoritative correction

The central product must define product-scoped role grants at minimum for:

- Referral Platform Owner.
- Campaign Administrator.
- Referral Qualification Reviewer.
- Referral Billing and Payout Operator.
- Referral Fraud and Risk Operator.
- Referral Support Operator.
- Referral Auditor.
- Executive Read-Only.

Campaign activation, manual attribution correction, reward adjustment, fraud disqualification, payout approval, payout execution, and payment-method override require separate permissions and threshold-based maker/checker controls.

Courier documents these roles only as an external integration dependency. Courier must not store them in merchant-user tables or assume a Courier Platform Owner can exercise them.

### Error handling and edge cases

The same central user may hold compatible grants, but policy must prohibit combinations that defeat segregation for the same case or payout run. A campaign creator cannot be the sole activator. A reward-adjustment maker cannot approve the same adjustment. A payout preparer cannot execute the same high-risk run. An auditor cannot mutate source records. Cross-product access is denied unless the grant includes the relevant `product_id` or central scope.

### Acceptance requirements

The central platform's authorization matrix must be versioned and testable. Courier integration tests must prove that Courier events can be processed without giving Courier administrators central reward, payout, payment-method, fraud, or referrer-profile access.

---

### 47.6.A Explicit Root Cause

Referral operational functions were assigned to a central super-administrator concept instead of independently permissioned, auditable duties.

### 47.6.B Authoritative Integration Location

Sections 4.1A and 46.1. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.6.C Users and Systems Affected

Central referral staff, Central Referrers, Courier integration operators, Platform Owners in both products, Security, Audit, Finance/payout operations.

### 47.6.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.7 Conflicting quote status definitions

**Severity:** Critical

### How the defect appears

One section models approval actors as quote statuses, while another models one generic `pending_approval` status. Both are presented as authoritative, so teams can implement incompatible enums and transitions.

### Authoritative correction

Use one commercial quote state machine and a separate approval workflow:

```text
quote_status:
draft
pending_approval
approved
sent
accepted
rejected
expired
superseded
cancelled
```

Approval detail belongs in `quote_approval_workflows` and `quote_approval_steps`. A step records stage, sequence, required permission, status, actor, decision, reason, threshold snapshot, timestamps, and version. The allowed stage keys are:

```text
dispatch_tolerance_review
branch_manager_review
merchant_admin_review
finance_policy_review
```

`current_approval_stage` is a derived projection from the first incomplete required step. It is not a second mutable source of truth.

### Authoritative transitions

```text
draft --submit_for_approval--> pending_approval
draft --approve_within_delegated_tolerance--> approved
pending_approval --approve_required_step--> pending_approval
pending_approval --complete_final_required_step--> approved
pending_approval --return_for_changes--> draft
pending_approval --reject--> rejected
pending_approval --cancel--> cancelled
approved --send--> sent
approved --supersede_with_revision--> superseded
approved --cancel--> cancelled
sent --customer_accept--> accepted
sent --customer_reject--> rejected
sent --expire--> expired
sent --supersede_with_revision--> superseded
sent --cancel_where_policy_allows--> cancelled
```

Accepted, rejected, expired, superseded, and cancelled versions are immutable terminal quote versions. Commercial cancellation after acceptance belongs to the request/delivery cancellation workflow, not a quote-state rewrite.

### Error handling and edge cases

Approving an already completed step is idempotent for the same idempotency key and otherwise returns `409 approval_step_already_decided`. An actor cannot approve a step they lack permission for. A pricing change while approval is pending does not mutate the quote; it creates a new draft version and supersedes the old one after controlled submission. Concurrent final approvals use aggregate versioning. A quote expires only from `sent`, unless policy explicitly expires stale drafts through a separate cleanup action.

### Acceptance requirements

Only one `quote_status` enum may exist in schema, code, OpenAPI, tests, and documentation. No detailed actor-approval value may be stored in `quote_status`.

---

### 47.7.A Explicit Root Cause

Approval workflow stages were conflated with the commercial quote lifecycle, producing two competing enums.

### 47.7.B Authoritative Integration Location

Sections 5.4, 5.5 and 7.4. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.7.C Users and Systems Affected

Dispatch, Branch Manager, Merchant Administrator, Merchant Finance, Goods Sender, Support, Audit, API clients.

### 47.7.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.8 Incomplete authoritative transition registries

**Severity:** Critical

### How the defect appears

The scope claims complete transition registries and central invalid-transition rejection, but supplies only state lists for quote, payment requirement, redelivery, dispute, and refund. A list of states is not a transition registry.

### Authoritative correction

Maintain machine-readable registries under version control. Every entry includes domain, action, from state, to state, permitted actor class, permission, tenant/branch/own scope, preconditions, evidence, financial effect, assignment effect, notification effect, audit event, idempotency rule, concurrency rule, and failure code.

#### Payment-requirement registry

Use these states:

```text
not_required
pending
partially_satisfied
satisfied
waived
failed
cancelled
```

Required transitions are:

```text
not_required --require_payment--> pending
pending --allocate_cleared_value_below_requirement--> partially_satisfied
pending --allocate_cleared_value_meeting_requirement--> satisfied
partially_satisfied --allocate_additional_cleared_value_below_requirement--> partially_satisfied
partially_satisfied --allocate_value_meeting_requirement--> satisfied
pending|partially_satisfied --waive_with_authority--> waived
pending|partially_satisfied --mark_requirement_failed--> failed
pending|partially_satisfied|failed --cancel_requirement--> cancelled
failed --retry_requirement--> pending
```

Refunds or reversals that reduce cleared value below the requirement create a controlled recalculation event. They may move `satisfied` to `partially_satisfied` or `pending` only when policy says progression is revocable; otherwise the operational process remains valid and a financial exception is opened. The registry must state which rule applies.

#### Redelivery registry

Use:

```text
not_required
requested
pending_quote
awaiting_customer_approval
scheduled
awaiting_assignment
in_progress
completed
rejected
expired
cancelled
```

Required transitions include request, quote preparation, customer approval/rejection, schedule, assignment readiness, start, completion, expiry, and cancellation. A redelivery creates a linked delivery attempt/leg and never overwrites the original failed attempt. Cancellation after the new leg enters custody is forbidden; return or controlled exception handling applies.

#### Dispute registry

Use:

```text
none
open
evidence_requested
under_review
awaiting_external_resolution
resolved_upheld
resolved_partially_upheld
resolved_rejected
withdrawn
closed
```

`closed` is permitted here because it belongs to the dispute domain, not physical delivery state. Resolution requires reason, evidence summary, financial-impact decision, actor, and timestamp. Reopening a closed dispute is a separate privileged action that creates a new review cycle and immutable history.

#### Refund registry

Use:

```text
none
requested
pending_approval
approved
processing
completed
failed
rejected
cancelled
reversal_pending
reversed
```

Required actions cover request, submit, approve, reject, cancel before processing, start provider/manual processing, complete, fail, retry, and controlled reversal. Refund completion creates append-only financial adjustments and cannot erase the original payment.

#### Quote registry

The quote registry is the one specified in correction 7 and must reference quote-approval steps rather than actor names in the quote enum.

### Error handling and edge cases

An invalid source state returns `409 invalid_transition` with current state and allowed actions. Missing evidence returns `422 evidence_required`. Locked financial periods return `423 financial_period_locked`. Duplicate provider callbacks are idempotently acknowledged. A transition that has an external side effect records an outbox event in the same transaction; dispatch failure is retried without repeating the state change. A stale version returns `409 state_conflict`. No registry action may target a value absent from the domain enum.

### Acceptance requirements

CI must parse every registry, prove all target states exist, prove required metadata is present, flag unreachable nonterminal states, identify ambiguous duplicate transitions, and compare the registries against generated code, OpenAPI commands, UI actions, and test cases.

---

### 47.8.A Explicit Root Cause

State names were documented without a complete action/actor/precondition/effect registry, while the prose still claimed centralized transition enforcement.

### 47.8.B Authoritative Integration Location

Sections 5 and 42. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.8.C Users and Systems Affected

All actors participating in quote, payment, assignment, delivery, redelivery, dispute, refund, and integration workflows.

### 47.8.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.9 Invalid handover transition target

**Severity:** Critical

### How the defect appears

The assignment transition targets the sentence “accepted by incoming assignee,” which is not an enumerated assignment state. It also fails to identify whether the existing or replacement assignment changes state.

### Authoritative correction

Handover is a coordinated operation involving two assignment records and one custody-handover record.

```text
outgoing assignment: accepted -> handover_pending -> superseded
incoming assignment: unassigned/new -> offered -> accepted
custody handover: initiated -> outgoing_confirmed -> incoming_confirmed -> completed
```

Where policy permits direct dispatch acceptance, the incoming assignment may be created as `accepted`, but the actor and reason must be recorded. The outgoing assignment becomes `superseded` only when the custody handover completes. If handover is cancelled before completion, the outgoing assignment returns to `accepted` and the incoming offer is cancelled.

### Required implementation changes

`delivery_custody_handovers` must reference `delivery_id`, outgoing assignment, incoming assignment, outgoing personnel/vehicle, incoming personnel/vehicle, reason, authorization, location, goods condition, evidence, confirmations, timestamps, and status. The operation runs in one transaction for local state plus an outbox event.

After pickup, no reassignment may occur without custody evidence. Before pickup, reassignment can supersede the old assignment without a custody handover.

### Error handling and edge cases

Incoming acceptance before outgoing initiation returns `409 handover_not_ready`. Incoming and outgoing assignments cannot be the same record or same active personnel unless a vehicle-only transfer policy explicitly allows it. If the incoming assignee declines, the outgoing assignment remains accepted and the handover is cancelled. If connectivity fails after one party confirms, the record remains pending and is retryable; no automatic supersession occurs. A goods-condition discrepancy opens an incident and blocks completion until Dispatch resolves it. Concurrent handovers for one delivery are rejected with `409 active_handover_exists`.

### Acceptance requirements

No transition target may contain prose. Tests must cover pre-pickup reassignment, post-pickup personnel transfer, vehicle-only transfer, decline, timeout, evidence failure, concurrent attempts, and idempotent confirmation retries.

---

### 47.9.A Explicit Root Cause

A human-readable action result was written as a state target and old/new assignment ownership was not modelled separately.

### 47.9.B Authoritative Integration Location

Sections 5.7 and 5.8. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.9.C Users and Systems Affected

Outgoing and incoming Delivery Personnel, Dispatch, Sender, Recipient Access, Finance, Support, Audit.

### 47.9.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.10 Physical delivery state incorrectly changes from delivered to closed

**Severity:** High

### How the defect appears

The operational state machine includes `delivered -> closed`, while another rule says delivered goods remain operationally delivered after dispute, refund, chargeback, or compensation adjustment. Both cannot be true in one physical-state field.

### Authoritative correction

Remove `closed` from `operational_delivery_status`. Physical terminal states remain `delivered`, `returned`, or an explicitly defined pre-custody cancellation state. Introduce a separate administrative lifecycle:

```text
record_lifecycle_status:
open
administratively_closed
reopened
archived
```

Administrative closure means routine follow-up is complete. It does not rewrite the physical outcome. A delivered shipment can therefore have:

```text
operational_status = delivered
record_lifecycle_status = administratively_closed
dispute_status = open
refund_status = completed
```

Opening a post-closure dispute may move the lifecycle to `reopened` while leaving `operational_status = delivered`.

### Required implementation changes

Rename any close command to `administratively-close` and define eligibility: required proof complete, no unresolved operational exception, mandatory customer notification complete, and no prohibited lock condition. Reports must filter physical outcome and administrative lifecycle separately.

### Error handling and edge cases

Attempting to close with missing proof returns `422 proof_incomplete`. A refund after administrative closure does not reopen physical delivery; it may reopen administrative lifecycle if work is required. Archival is blocked while legal hold, open dispute, unsettled refund, or retention obligation exists. A failed attempt must not be administratively closed when a required return or redelivery decision is unresolved.

### Acceptance requirements

The operational enum must not contain `closed`. All historical `closed` records require a migration that derives the last physical state from state history and maps closure to lifecycle status. Ambiguous records go to a reconciliation queue rather than being guessed.

---

### 47.10.A Explicit Root Cause

Physical completion and administrative record closure were placed in one state domain.

### 47.10.B Authoritative Integration Location

Sections 5.9 and 5.10. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.10.C Users and Systems Affected

Delivery Personnel, Dispatch, Sender, Recipient Access, Finance, Support, Audit, reporting and integration consumers.

### 47.10.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.11 Duplicated invoice, payment, dispute, and refund authority

**Severity:** High

### How the defect appears

Invoice and payment enums contain `disputed`, `refunded`, or `partially_refunded`, while separate dispute and refund entities have their own state machines. This creates multiple mutable sources for the same business condition.

### Authoritative correction

Keep four authorities:

- Invoice settlement state.
- Payment transaction and allocation state.
- Dispute case state.
- Refund case and refund-transaction state.

A recommended invoice settlement enum is:

```text
draft
issued
partially_paid
paid
overdue
voided
written_off
```

A payment attempt/transaction enum describes collection outcome only. Payment allocations link cleared value to invoices. Dispute and refund status remain on their own records.

Invoice properties such as `has_open_dispute`, `refund_rollup = none|partial|full`, and `net_refunded_minor` are derived query projections. They are not directly patchable fields. An invoice can remain `paid` while also being fully refunded; its net economic balance is computed from allocations and adjustments rather than by rewriting history.

### Required implementation changes

Remove direct mutation paths for invoice `disputed/refunded/partially_refunded` and payment `disputed`. Create read-model logic, documented precedence, and reconciliation jobs. All financial reports use ledger effects, not status-name assumptions.

### Error handling and edge cases

A refund cannot exceed cleared allocations less prior completed refunds and reversals. A dispute can open on a partially paid invoice. A chargeback creates a payment reversal/adjustment plus a dispute or exception, not an invoice-state overwrite. Concurrent partial refunds use row/version locks and cumulative validation. Voiding an issued invoice with payment is forbidden; issue a credit/adjustment workflow instead.

### Acceptance requirements

Schema and OpenAPI must expose derived refund/dispute rollups as read-only. Tests must prove combinations such as paid plus open dispute, paid plus partial refund, paid plus full refund, partially paid plus dispute, and refund failure without corrupting settlement state.

---

### 47.11.A Explicit Root Cause

Invoice, payment, dispute, and refund facts were each permitted to claim authority over the same business condition.

### 47.11.B Authoritative Integration Location

Sections 5.6, 5.12, 5.13, 10 and 25. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.11.C Users and Systems Affected

Merchant Finance, Dispatch, Support, Sender/business-customer finance users, Audit, accounting integrations.

### 47.11.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.12 Refund state incorrectly placed on a platform payment attempt

**Severity:** Medium

### How the defect appears

`refunded_externally` appears as a platform payment-attempt status even though a payment attempt records collection, while refund is a later financial event.

### Authoritative correction

Use payment-attempt states such as:

```text
initiated
pending_provider
successful
failed
cancelled
expired
```

Track application through payment allocations or an `application_status`. Track later refunds, chargebacks, provider reversals, and manual adjustments in separate immutable entities. A successful attempt remains successful historically even when its value is later refunded.

### Required implementation changes

Add `platform_payment_refunds` or a generalized `platform_payment_adjustments` ledger with source attempt, amount, currency, reason, provider reference, state, actor, timestamps, and reconciliation. Remove `refunded_externally` from attempt enums, UI filters, and API contracts.

### Error handling and edge cases

An external refund discovered by reconciliation creates an adjustment record even when Courier did not initiate it. Duplicate provider references are idempotently ignored or linked to the existing adjustment. A refund larger than the original cleared amount is quarantined as `reconciliation_exception`. A successful attempt that was never allocated can still be refunded; allocation and refund logic must reconcile the resulting credit.

### Acceptance requirements

Historical attempt outcomes remain immutable. Reports must show original collection and later adjustments separately. Migration must create refund/adjustment records from legacy `refunded_externally` attempts before replacing the old status with the original successful outcome.

---

### 47.12.A Explicit Root Cause

A later refund event was treated as though it changed the historical outcome of the original platform payment attempt.

### 47.12.B Authoritative Integration Location

Sections 15.5 and 25. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.12.C Users and Systems Affected

Merchant Administrator, Merchant Finance, Branch Manager where permitted, Platform Billing Operator, Audit, reconciliation services.

### 47.12.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.13 Monolithic delivery_requests.status field

**Severity:** Critical

### How the defect appears

The detailed schema contains a generic `delivery_requests.status` even though the scope explicitly separates request, quote, payment requirement, assignment, operational, redelivery, dispute, and refund domains.

### Authoritative correction

`delivery_requests` may contain only `request_status`, representing the request/commercial lifecycle. Other authoritative states remain on their own aggregates:

```text
delivery_quotes.quote_status
payment_requirements.status
delivery_assignments.status
deliveries.operational_status
redelivery_cases.status
payment_disputes.status
refunds.status
```

A `delivery_status_projection` may denormalize current values for search and dashboards, but it must be marked read-only, rebuildable, versioned, and non-authoritative. Do not persist seven mutable “current_*_status” columns on the request unless they are explicitly projection columns maintained only by the projection service.

### Required implementation changes

Rename the field, update all repositories and queries, introduce domain foreign keys, and add state-history/outbox processing. Generic `status` filtering in APIs must be replaced by explicit domain filters or `display_status`, clearly labeled derived.

### Error handling and edge cases

Legacy rows with ambiguous status values are mapped using state history, timestamps, assignment, proof, invoice, and dispute evidence. Ambiguous rows enter a migration exception queue. Projection lag must be surfaced through version/watermark metadata for critical internal tooling; authoritative command validation always reads source aggregates. A projection rebuild must not emit duplicate business events.

### Acceptance requirements

Database lint must reject a generic mutable `status` column on `delivery_requests`. Tests must prove that dispute/refund changes do not rewrite request or operational status and that projection deletion/rebuild does not lose authoritative state.

---

### 47.13.A Explicit Root Cause

The schema retained a generic status field after the lifecycle had been split into orthogonal domains.

### 47.13.B Authoritative Integration Location

Sections 5 and 25. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.13.C Users and Systems Affected

Sender, Dispatch, Personnel, Finance, Support, Audit, reporting, API/webhook consumers.

### 47.13.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.14 Direct preferred personnel fields bypass request and reservation separation

**Severity:** Critical

### How the defect appears

`preferred_personnel_id` and `preferred_personnel_fee_minor` appear directly on `delivery_requests`, allowing them to be interpreted as an assigned person and bypassing the preferred-personnel request/reservation model.

### Authoritative correction

Store preference data in:

```text
preferred_personnel_requests
preferred_personnel_reservations
```

The request record includes requested personnel, requester, reason/source, request state, expiry, eligibility snapshot, and optional preference-policy reference. A reservation is short-lived and records preliminary eligibility, capacity hold, expiry, and release reason. The actual assignee exists only in `delivery_assignments`.

`delivery_requests` may expose a read-only `active_preferred_personnel_request_id` foreign key or derive the active request. It must not contain `preferred_personnel_id` as assignment evidence.

Preferred-personnel pricing is represented as a quote line and later invoice line with a configuration snapshot. It is not a mutable fee field on the request.

### Error handling and edge cases

An ineligible requested person returns a safe rejection or creates a request marked unavailable according to UX policy; it never assigns them. A reservation expiring before Dispatch assignment releases capacity and notifies relevant users. Concurrent reservations use unique active constraints. Personnel suspension, vehicle unavailability, branch mismatch, service mismatch, or shift change invalidates the reservation and requires reassessment. Sender cancellation removes the request/reservation but does not erase history. Dispatch can assign another eligible person with a recorded reason.

### Acceptance requirements

No code path may create an assignment by writing a preference field. Tests must cover unavailable preference, reservation expiry, eligibility changes, concurrent requests, price snapshots, alternative assignment, and customer messaging that describes preference rather than guaranteed assignment.

---

### 47.14.A Explicit Root Cause

Legacy request columns continued to look like an authoritative assignee despite introduction of preference request and reservation records.

### 47.14.B Authoritative Integration Location

Sections 5.7, 7.5 and 25. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.14.C Users and Systems Affected

Goods Sender, Dispatch, Delivery Personnel, HR, Branch Manager, Finance, Audit.

### 47.14.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.15 Fixed transaction fee reintroduced into the percentage fee ledger

**Severity:** High

### How the defect appears

The scope separates recurring fixed subscription charges from transaction percentage fees, yet `platform_fee_ledger_entries` contains `transaction_fixed_fee_snapshot_minor_nullable nullable`. No approved product rule defines a fixed per-transaction Citrus charge, and the field text itself is malformed.

### Authoritative correction

Remove the field. The launch billing modes mean:

- `fixed_amount`: recurring plan-period charge represented by platform invoice lines.
- `percentage_on_merchant_customer_invoice`: transaction-derived percentage ledger entries.
- `fixed_amount_plus_percentage_on_merchant_customer_invoice`: both of the above, each in its own authoritative structure.

`platform_fee_ledger_entries` stores only percentage-derived provisional, billable, aggregated, reversed, and adjusted entries. Fixed recurring charges are generated from `subscription_plan_prices` into `platform_billing_invoice_lines`.

### Error handling and edge cases

Changing billing mode mid-cycle follows the settled next-cycle rule and never retroactively inserts transaction fixed fees. Refunds create percentage ledger adjustments, not edits to recurring fixed lines. A zero-percentage configuration creates no transaction fee entry unless zero-value audit entries are explicitly required; it must not create a hidden fixed charge. Existing rows with non-null legacy fixed values are quarantined and reconciled before removal.

### Acceptance requirements

Schema, billing code, invoices, reports, and tests must contain no launch concept of a per-transaction fixed Citrus fee. Introduction of such a product later requires a new named billing component, effective-dated configuration, disclosure, pricing, invoice presentation, migration, and acceptance suite.

---

### 47.15.A Explicit Root Cause

Recurring plan charges and transaction-derived percentage fees remained conceptually mixed in one ledger schema.

### 47.15.B Authoritative Integration Location

Sections 14, 25 and 35. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.15.C Users and Systems Affected

Platform Owner, Platform Billing Operator, Merchant Administrator, Merchant Finance, Audit, billing services.

### 47.15.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.16 Coexisting old and corrected identity schemas

**Severity:** Critical

### How the defect appears

The model lists `users`, `merchant_users`, `user_invitations`, and `user_status_history`, then later adds `user_identities`, `merchant_user_memberships`, `staff_invitations`, `employment_records`, `role_grant_versions`, and `emergency_security_suspensions` without declaring replacement or compatibility relationships.

### Authoritative correction

Use this canonical model:

```text
user_identities
identity_contacts
identity_authenticators
identity_sessions
merchant_user_memberships
merchant_role_grants
employment_records
staff_invitations
membership_status_history
employment_status_history
role_grant_history
emergency_security_suspensions
```

`user_identities` is the authenticating human. Membership, employment, invitation, role, and emergency suspension remain distinct. The legacy names are deprecated and must be removed or implemented only as read-only compatibility views.

### Migration and deprecation mapping

- `users` becomes `user_identities` after field normalization.
- `merchant_users` splits into membership, employment, and role grants.
- `user_invitations` migrates to `staff_invitations` or organization-specific invitation tables.
- `user_status_history` splits by status domain.
- `employment_history` is reconciled into `employment_records` plus status history.

No application service may dual-write old and new tables indefinitely. A short controlled migration window may use one canonical writer and compatibility projections.

### Error handling and edge cases

Conflicting legacy status values are resolved by explicit precedence and manual review, never by arbitrary latest timestamp alone. Duplicate identities require verified merge workflow; no email-only automatic merge occurs. Reused email addresses after deactivation must preserve historical identity links according to policy. Pending invitations for deactivated or already-active users are revoked. Emergency security suspension never changes employment status.

### Acceptance requirements

Publish a field-level migration map, run reconciliation counts, preserve audit actor references, and remove write permissions from legacy structures. CI must fail when new code imports legacy repositories or writes legacy identity tables.

---

### 47.16.A Explicit Root Cause

Corrected identity records were added without retiring or constraining the legacy user, invitation, membership, and status records.

### 47.16.B Authoritative Integration Location

Sections 25, 33 and 48. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.16.C Users and Systems Affected

Merchant Administrator, HR, all merchant staff, identity/security teams, Audit, migration operators.

### 47.16.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.17 Coexisting generic and business-specific API client schemas

**Severity:** High

### How the defect appears

Generic `api_clients`, `api_keys`, `api_usage_logs`, and webhook tables coexist with business-specific equivalents, but no parent/child or replacement relationship is defined. Both could store active credentials.

### Authoritative correction

Use one integration-principal model:

```text
integration_principals
integration_clients
integration_credentials
integration_scope_grants
integration_usage_events
webhook_endpoints
webhook_subscriptions
webhook_delivery_attempts
```

`integration_principals` has a constrained owner type such as `business_customer_organization`, `merchant`, `citrus_system_service`, or `approved_partner`, and an owner reference validated by a registry/service. Client subtype metadata may live in one-to-one extension tables, but credentials exist only in `integration_credentials`.

Where strict relational foreign keys are preferred over a polymorphic owner, use separate owner-link tables pointing to the same `integration_clients` parent. Do not keep separate credential stores.

### Required implementation changes

Credential secrets are shown once, stored hashed or in a secrets manager, versioned, scoped, expiring, rotatable, and revocable. Webhook signing secrets follow the same rule. Usage records reference the canonical client ID. Business-specific names may survive only as views or API resource labels.

### Error handling and edge cases

Credential rotation supports overlap windows without permitting indefinite dual-active secrets. Revocation is immediate and invalidates caches. Organization suspension disables clients. Duplicate client names are scoped to owner. Scope escalation requires authorization and audit. A deleted webhook endpoint with queued deliveries follows a defined cancel/dead-letter policy. Unknown credential IDs return a neutral authentication failure, not ownership details.

### Acceptance requirements

There must be one active credential authority, one usage authority, and one webhook subscription authority. Tests must prove cross-owner isolation, rotation, revocation, expiry, scope denial, rate limiting, and no plaintext secret retrieval.

---

### 47.17.A Explicit Root Cause

Generic and business-specific client/credential/webhook tables were introduced without a parent-child or replacement rule.

### 47.17.B Authoritative Integration Location

Sections 21.7, 25 and 27. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.17.C Users and Systems Affected

Business Customer Integration Administrators, Platform Integration Operators, Security, Audit, API clients and webhook consumers.

### 47.17.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.18 Duplicate platform_billing_invoice_lines listing

**Severity:** Low

### How the defect appears

`platform_billing_invoice_lines` appears in both the main platform-billing list and the later “additionally requires” list, weakening the claim of a single authoritative model.

### Authoritative correction

Keep the table in the canonical Platform Billing section only. In later correction or extension sections, reference it by name and describe only added constraints or fields; do not list it as a newly required entity.

Add documentation lint that detects duplicate schema entity declarations unless the later occurrence is explicitly marked `extends` or `references`.

### Error handling and edge cases

A duplicate name in generated schema manifests must fail CI with `duplicate_entity_declaration`. Legitimate cross-references do not create a second migration or ORM model. Case or pluralization variants that resolve to the same physical table must also be flagged.

### Acceptance requirements

The final schema inventory contains one authoritative declaration and one migration ownership path for every table.

---

### 47.18.A Explicit Root Cause

The schema catalog was assembled by addition rather than canonical deduplication.

### 47.18.B Authoritative Integration Location

Sections 25, 42 and 48. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.18.C Users and Systems Affected

Engineering, data/migration teams, QA, Audit, reporting and operations teams.

### 47.18.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.19 Unscoped and merchant-scoped sender authentication routes coexist

**Severity:** Critical

### How the defect appears

The route list retains `/api/v1/sender/auth/request` and `/verify` while later adding merchant-scoped routes. The unscoped routes recreate the tenant-context weakness the scope claims to fix.

### Authoritative correction

The normative routes are merchant-scoped:

```text
POST /api/v1/merchants/{merchant_slug}/sender-auth/request
POST /api/v1/merchants/{merchant_slug}/sender-auth/verify
```

The challenge stores merchant, sender identity candidate, intended membership, nonce, purpose, expiry, attempts, and risk metadata. Verification issues a session bound to the merchant context.

Delete the unscoped routes from OpenAPI, code, tests, SDKs, and documentation. A separate public merchant-discovery endpoint may exist, but it may only resolve a merchant context and must not issue an authentication challenge. Signed invitation or referral context can preselect the merchant but must still be validated.

### Error handling and edge cases

Unknown merchant, unknown sender, and invalid contact use neutral responses to prevent enumeration. Rate limits apply by IP, contact hash, merchant, and device signal. Verification under the wrong merchant returns a neutral invalid/expired challenge. A sender belonging to several merchants receives isolated sessions or an explicit merchant switch that reauthorizes context. Deactivated membership invalidates unused challenges and active sessions for that merchant only. A renamed merchant slug supports controlled aliases without allowing tenant confusion.

### Acceptance requirements

Requests to the old routes must return `404` after the compatibility window or a documented `410 route_removed` without processing credentials. Security tests must prove tenant-bound tokens, no sender enumeration, replay prevention, and cross-merchant denial.

---

### 47.19.A Explicit Root Cause

Tenant context was added through new routes but the unsafe unscoped sender routes were left active in the same contract.

### 47.19.B Authoritative Integration Location

Sections 27 and 33. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.19.C Users and Systems Affected

Goods Sender, all business-customer members, Support, Audit, authentication and API teams.

### 47.19.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.20 Illustrative routes conflict with mandatory route traceability

**Severity:** High

### How the defect appears

The API section calls routes illustrative, while acceptance criteria require every launch capability to have a route or internal command. An illustrative list cannot be a release contract.

### Authoritative correction

Split API documentation into three clearly governed artifacts:

1. **Normative launch API and internal commands:** implemented, versioned, security-reviewed, tested, and present in OpenAPI or command registry.
2. **Reserved architecture-ready endpoints:** names reserved but not routable in production; excluded from launch OpenAPI and SDKs.
3. **Post-MVP examples:** non-normative design notes with no release promise.

Every launch UI action maps to one normative command. Background-only capabilities map to an internal command/job/event. Every route has owner, release class, permissions, request/response schema, idempotency, errors, audit, and tests.

### Error handling and edge cases

Calling a reserved endpoint must not silently perform partial behavior. It should not be deployed, or it returns a consistent `404` from the gateway. Feature-disabled launch endpoints return `403 entitlement_required` or `409 feature_not_enabled` only when the feature is implemented and commercially gated. Deprecated normative endpoints include sunset headers and telemetry during a bounded migration.

### Acceptance requirements

Release CI compares capability IDs against route/command IDs. No Mandatory MVP or Launch Entitlement capability may lack an implemented command. No Post-MVP capability may appear as an active production route.

---

### 47.20.A Explicit Root Cause

The API list was labelled illustrative even though release acceptance required complete executable route traceability.

### 47.20.B Authoritative Integration Location

Sections 27, 35, 38 and 42. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.20.C Users and Systems Affected

Product, Architecture, Engineering, QA, SDK/integration consumers, Support and release governance.

### 47.20.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.21 Generic delivery PATCH and status routes bypass state commands

**Severity:** Critical

### How the defect appears

`PATCH /deliveries/{delivery}` and a generic personnel `/status` command coexist with actor-specific state machines. They permit arbitrary state assignment, cross-domain mutation, and evidence or authorization bypass.

### Authoritative correction

Limit delivery PATCH to allowlisted non-state metadata that remains editable at the current lifecycle stage, such as customer-visible notes or instructions under policy. Reject fields including status, assignee, proof, payment, refund, dispute, and lifecycle values.

Replace generic status mutation with action commands:

```text
POST .../en-route-to-pickup
POST .../arrive-at-pickup
POST .../submit-pickup-verification
POST .../complete-pickup
POST .../start-transit
POST .../mark-arriving
POST .../arrive-at-destination
POST .../submit-delivery-proof
POST .../report-failed-attempt
POST .../start-return
POST .../complete-return
```

Each command binds to one registry action and validates actor, own-scope, assignment, state, evidence, location/timestamp policy, version, idempotency, and financial lock effects.

### Error handling and edge cases

A PATCH containing forbidden state fields returns `422 immutable_or_command_only_field`. Offline PWA commands queue with client command ID and original event time; server acceptance still validates current state and may return a conflict requiring user resolution. Duplicate proof submission is idempotent. GPS absence follows configured evidence policy and never silently fabricates coordinates. Commands received after reassignment return `403 assignment_no_longer_owned` or `409 assignment_changed` without exposing the new assignee.

### Acceptance requirements

Fuzz tests must prove generic PATCH cannot mutate any state domain. Every operational transition must have one command, one permission, one audit event, and negative tests for missing evidence, stale versions, wrong assignment, and invalid sequence.

---

### 47.21.A Explicit Root Cause

Generic resource mutation was retained after domain-specific commands and evidence rules were introduced.

### 47.21.B Authoritative Integration Location

Sections 5, 27 and 33. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.21.C Users and Systems Affected

Dispatch, Delivery Personnel, Sender, Support, Finance, Audit, API clients and offline PWA users.

### 47.21.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.22 Generic financial-period reopen bypasses approval workflow

**Severity:** High

### How the defect appears

A single `/financial-periods/reopen` route does not distinguish request, approval, execution, or cancellation, although the scope requires Finance execution and configured Merchant Administrator approval.

### Authoritative correction

Use a reopen request aggregate and commands:

```text
POST /api/v1/financial-period-reopen-requests
POST /api/v1/financial-period-reopen-requests/{request}/approve
POST /api/v1/financial-period-reopen-requests/{request}/reject
POST /api/v1/financial-period-reopen-requests/{request}/execute
POST /api/v1/financial-period-reopen-requests/{request}/cancel
POST /api/v1/financial-period-reopen-requests/{request}/close
```

The request records period, scope, reason, affected record types, requested mutations, maker, approvals, expiry, execution window, and audit evidence. Finance executes. Merchant Administrator approval applies only when policy or threshold requires it. High-risk requests can require a second Finance approver or platform oversight.

### Error handling and edge cases

Execution before required approval returns `409 approval_required`. Expired approval returns `409 reopen_authorization_expired`. A request cannot execute twice. Overlapping reopen windows are rejected or merged under policy. New locks or legal holds arising after approval block execution and require re-review. Cancellation after execution is forbidden; the period must be re-locked through the close command. Partial execution is transactionally prevented or explicitly recorded by item with compensating recovery.

### Acceptance requirements

The generic route is removed. Tests must prove actor separation, threshold rules, expiration, overlapping requests, stale approvals, re-locking, and complete audit.

---

### 47.22.A Explicit Root Cause

One reopen verb combined request, approval, execution, and cancellation despite maker/checker requirements.

### 47.22.B Authoritative Integration Location

Sections 18, 27 and 33. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.22.C Users and Systems Affected

Merchant Finance, Merchant Administrator approvers, Branch Managers, Audit, financial operations.

### 47.22.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.23 Launch optimization endpoint exposes a Post-MVP capability

**Severity:** High

### How the defect appears

`POST /route-groups/{group}/optimize` appears in the launch route list while full multi-vehicle route optimization is classified as Post-MVP.

### Authoritative correction

Remove the production optimization endpoint from launch OpenAPI and routing. Launch supports basic route calculation, ETA, and manual/single-route stop sequencing under clearly named commands such as:

```text
POST /api/v1/routes/{route}/calculate-distance-eta
POST /api/v1/routes/{route}/reorder-stops
```

Neither command may allocate jobs across multiple vehicles, solve fleet-wide constraints, or claim optimization.

Reserve the future endpoint in a non-normative architecture document only. When implemented, it requires its own entitlement, algorithm limits, explainability, manual override, capacity/time-window constraints, failure behavior, and performance SLOs.

### Error handling and edge cases

Basic route calculation returns `503 maps_dependency_unavailable` when provider failure prevents calculation, while preserving manual operation. Invalid coordinates return field errors. Stop reordering detects locked/completed stops. Large stop counts enforce configured limits. No hidden feature flag should make the Post-MVP optimizer callable on a sold launch plan.

### Acceptance requirements

Production route discovery and OpenAPI must contain no full optimization route. Tests must distinguish manual sequencing from multi-vehicle optimization.

---

### 47.23.A Explicit Root Cause

A deferred advanced optimizer remained exposed by a launch production endpoint.

### 47.23.B Authoritative Integration Location

Sections 12, 20, 27 and 35. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.23.C Users and Systems Affected

Dispatch, plan administrators, sales/marketing, maps/integration operators, Engineering and QA.

### 47.23.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.24 Refund maker/checker separation not enforced

**Severity:** High

### How the defect appears

Finance can request and approve refunds, and the routes do not prohibit the same user from performing both actions on the same refund.

### Authoritative correction

Every refund stores `requested_by`, `submitted_by`, `approved_by`, `processed_by`, and optional second approver. Policy defines thresholds by amount, method, risk, age, dispute status, and branch. At minimum:

```text
requested_by != approved_by
```

For high-risk refunds, `approved_by != second_approved_by` and the processor cannot be the sole approver. Automated low-value refunds may use an approved policy engine identity, with policy version and evidence recorded.

### Error handling and edge cases

Self-approval returns `403 separation_of_duty_violation`. An approval after amount or destination changes is invalidated and must be repeated. A request above the actor's threshold returns `403 approval_limit_exceeded`. Duplicate provider submissions use idempotency. Partial refunds concurrently requested against one payment are serialized and cumulative limits enforced. Failed processing does not create another liability; retry uses the same refund or a controlled retry attempt.

### Acceptance requirements

Tests must cover low/high thresholds, self-approval denial, changed-request invalidation, partial-refund races, provider retries, locked periods, and audit completeness.

---

### 47.24.A Explicit Root Cause

Finance ownership was described without separating refund preparation from approval and execution.

### 47.24.B Authoritative Integration Location

Sections 10.9, 27, 33 and 38. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.24.C Users and Systems Affected

Refund request makers in Dispatch/Support, Finance preparers and approvers, Merchant Administrator threshold approvers, Audit, Sender.

### 47.24.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.25 Route optimization simultaneously included in plans and deferred

**Severity:** Critical

### How the defect appears

Pro Branch and Multi-Branch plan text includes route optimization, while release classification defers full multi-vehicle optimization. This creates a sellability and delivery contradiction.

### Authoritative correction

Use exact capability names:

- **Basic route distance, ETA, map display, and manual multi-stop sequencing:** implemented at launch and plan-gated according to commercial policy.
- **Full multi-vehicle route optimization:** Post-MVP and not included in any launch plan promise.

Replace every launch plan matrix or description that says “Route optimization included” with the basic capability wording. A future plan version can add full optimization only after implementation and effective-dated commercial activation.

### Error handling and edge cases

Existing sales material or contracts promising optimization require manual commercial remediation rather than silently downgrading expectations. Entitlement checks use separate keys such as `routing.basic` and `routing.multi_vehicle_optimization`. The latter must be absent or disabled in launch production. UI copy must not label stop reordering as optimization.

### Acceptance requirements

Plan catalog, pricing pages, sales copy, entitlement seed data, UI, OpenAPI, and acceptance tests must use the same two capability names and release statuses.

---

### 47.25.A Explicit Root Cause

Plan marketing/entitlement wording and the release roadmap assigned full optimization to different releases.

### 47.25.B Authoritative Integration Location

Sections 12, 20 and 35. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.25.C Users and Systems Affected

Merchants on all plans, Dispatch, Platform Owner, sales/marketing, product, Engineering and QA.

### 47.25.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.26 API and webhook availability has incompatible release classifications

**Severity:** High

### How the defect appears

Plans describe API/webhooks as optional or included, another section says they may be launch entitlement or merely architecture-ready, and implementation phases defer them to advanced scale.

### Authoritative correction

Set one launch decision: core business-customer API clients and outbound webhooks are **Launch Entitlements**. They are implemented and production-ready at launch, offered as an add-on on Growth and included on Pro Branch and Multi-Branch. Starter has no business API entitlement. Architecture-ready applies only to future expanded endpoints and connectors, not the core client/webhook capability.

Move implementation of identity, scopes, credentials, idempotency, rate limits, webhook signatures, retries, dead letters, usage logs, and integration console into the launch implementation phases before release. Phase 7 may add advanced accounting connectors, higher quotas, event families, and partner integrations.

### Error handling and edge cases

Entitlement denial returns `403 entitlement_required`. Suspended organizations or merchants disable credentials. Webhook delivery uses signed retries, backoff, attempt caps, and dead letters. Endpoint validation prevents private-network SSRF according to deployment policy. Secret rotation supports bounded overlap. Delivery ordering is not guaranteed unless an event stream explicitly provides sequence numbers; consumers must handle duplicates.

### Acceptance requirements

Plan seed data, implementation roadmap, OpenAPI, integration console, and tests must all classify the same core capability as launch-implemented. No “when commercially launched” ambiguity remains.

---

### 47.26.A Explicit Root Cause

Entitlement gating was incorrectly used as a substitute for implementation status for API and webhook capabilities.

### 47.26.B Authoritative Integration Location

Sections 12, 21.7, 23 and 35. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.26.C Users and Systems Affected

Business-customer integration users, API clients, webhook consumers, merchants, sales, support, Engineering and QA.

### 47.26.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.27 Multi-Branch is sold as a launch plan but deferred to Phase 7

**Severity:** High

### How the defect appears

Multi-Branch is one of four core plans, yet central multi-branch command is placed in an advanced phase. A plan cannot be sold at launch without its defining controls.

### Authoritative correction

Classify the core Multi-Branch plan as a **Launch Entitlement**. Launch scope must include multiple branch creation, branch-scoped authorization, central branch switcher, consolidated dashboard, branch comparisons, centralized plan/limit enforcement, cross-branch reporting, controlled cross-branch role grants, inter-branch delivery support where specified, and extra-branch billing.

Phase 7 may contain advanced forecasting, optimization, accounting consolidation, and analytics, but not the controls necessary to operate the sold plan.

### Error handling and edge cases

Downgrade below current branch count creates a scheduled remediation workflow and never silently deletes branches. Cross-branch users require explicit grants. Branch transfer validates custody, finance, and service policies. Consolidated reports must preserve branch attribution. A branch outage must not expose another branch's data. Branch closure is blocked by active operational and financial obligations.

### Acceptance requirements

The plan is not marked purchasable until all core controls pass tenant/branch isolation and billing tests. Roadmap and plan copy must agree.

---

### 47.27.A Explicit Root Cause

Multi-Branch was sold as a launch plan while its core operating controls were scheduled as a later phase.

### 47.27.B Authoritative Integration Location

Sections 12, 23, 35 and 37. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.27.C Users and Systems Affected

Multi-Branch merchants, Merchant Administrator, Branch Managers, HR, Finance, Dispatch, Support, Audit, sales and implementation teams.

### 47.27.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.28 Core Launch Modules contain deferred capabilities

**Severity:** Medium

### How the defect appears

The “Core Launch Modules” list includes capabilities that are elsewhere plan-restricted, architecture-ready, or later-phase. The phrase is used inconsistently.

### Authoritative correction

Rename the section **Launch-Implemented Capability Modules** and attach one release-class key to every capability in machine-readable metadata:

```text
mandatory_mvp
launch_entitlement
launch_add_on
architecture_ready
post_mvp
```

Plan restriction does not make an implemented module non-launch. Conversely, architecture-ready means schema/extension points only and must not be presented as usable.

Compensation, core Multi-Branch, period locks, and core API/webhooks are launch-implemented but plan-gated under the decisions in this correction specification. Full optimization and advanced connectors are Post-MVP.

### Error handling and edge cases

A capability cannot have more than one release class for the same release version. CI rejects missing or duplicate classification. Marketing generation must exclude architecture-ready and Post-MVP capabilities from launch inclusions. Feature flags cannot silently convert an untested architecture-ready capability into production.

### Acceptance requirements

One release manifest drives scope text, plan catalog, feature flags, roadmap, OpenAPI exposure, test selection, and release notes.

---

### 47.28.A Explicit Root Cause

A “Core Launch” grouping mixed launch-complete, reserved, and deferred features without one release-class source of truth.

### 47.28.B Authoritative Integration Location

Sections 23, 35 and 37. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.28.C Users and Systems Affected

All launch users, Product, Engineering, QA, Sales, Support and Operations.

### 47.28.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.29 Starter plan role deadlock

**Severity:** Critical

### How the defect appears

Starter includes personnel, assignment, PWA, payment recording, and validation, but Merchant Administrator is barred from operational work, HR creates staff, Dispatch records payments, and Finance validates them. Starter does not clearly include the roles needed to operate.

### Authoritative correction

Define a viable Starter operating model without turning Merchant Administrator into an implicit superuser:

- One branch.
- One Merchant Administrator.
- Up to three staff users, assignable to the existing HR, Dispatch, and Finance role profiles.
- Up to three Delivery Personnel, within the plan's stated limit.
- **Core staff administration** is included: invitation, branch membership, essential eligibility, availability, suspension, and deactivation.
- Advanced HR features such as complex compensation rules, extensive document automation, and multi-branch HR remain Growth or higher.
- Integrated gateway payments may validate automatically. Offline or COD payment validation requires a Finance grant separate from the Dispatch maker for the same record.

The Merchant Administrator may appoint the initial HR user under the existing owner flow. HR then creates operational users. A single person may hold multiple compatible grants, but the same person cannot perform both sides of a maker/checker transaction where policy requires separation.

### Error handling and edge cases

A Starter tenant trying to exceed user limits receives `403 entitlement_limit_reached` with upgrade guidance. If no Finance checker exists, offline payments remain `pending_validation`; the platform must not falsely mark them cleared. A solo merchant can operate using integrated payments and one user holding Admin plus Dispatch, while financial checker actions that require separation remain unavailable until another user is added. Emergency access suspension remains available to the Merchant Administrator but does not replace HR lifecycle.

### Acceptance requirements

A clean Starter tenant must be able to register, appoint essential roles, create personnel, create and assign deliveries, complete PWA flow, collect integrated payment, issue receipts after validation, and handle basic operations without violating role exclusions.

---

### 47.29.A Explicit Root Cause

Starter limits assumed duties could be combined but did not provide a least-privilege way to perform essential HR/Dispatch/Finance controls.

### 47.29.B Authoritative Integration Location

Sections 12, 23, 33 and 38. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.29.C Users and Systems Affected

Starter merchants, Merchant Administrator, essential HR/Dispatch/Finance duty holders, Delivery Personnel, Sender and Support.

### 47.29.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.30 Overbroad platform-billing payment authority

**Severity:** Medium/High

### How the defect appears

Merchant Administrator, Branch Manager, Finance, and sometimes Dispatch are described as eligible platform-invoice payers without a consistent delegation, threshold, phone, approval, or opt-out policy.

### Authoritative correction

Default payment initiators are Merchant Administrator and Merchant Finance. Branch Manager and Dispatch require all of the following:

- explicit permission `platform_billing.payment.initiate`;
- merchant policy enabling the role;
- branch or merchant scope;
- per-transaction and cumulative limits;
- approved payer-phone or entered-phone policy;
- step-up authentication where risk requires;
- immediate audit and notification to Merchant Administrator/Finance.

Initiating an M-Pesa STK Push does not itself mark payment successful. Provider callback, reconciliation, and invoice allocation remain authoritative.

### Error handling and edge cases

Unauthorized roles receive `403 payment_initiation_not_delegated`. Above-limit attempts return `403 payment_limit_exceeded`. Duplicate STK requests use idempotency and show the existing attempt. Wrong or unverified phone numbers require confirmation and may be blocked by policy. Timeout leaves the attempt pending until callback/reconciliation; it must not create repeated pushes automatically without user awareness. Billing suspension must still allow authorized recovery payment.

### Acceptance requirements

Delegation is off by default for Branch Manager and Dispatch. Tests cover limits, phone policy, step-up, duplicate initiation, callback reconciliation, suspension recovery, and notification/audit.

---

### 47.30.A Explicit Root Cause

Convenient payment UI access was confused with the authority to initiate merchant-to-Citrus billing payments.

### 47.30.B Authoritative Integration Location

Sections 15.1, 27, 33 and 38. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.30.C Users and Systems Affected

Merchant Administrator, Merchant Finance, optionally authorized Branch Managers, Dispatch, Audit and platform billing services.

### 47.30.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.31 Insufficient payout-run segregation within Finance

**Severity:** Medium

### How the defect appears

HR prepares a payout run, but one Finance user can verify, standard-approve, and mark it paid. The statement that no one user completes all stages is too weak for settlement control.

### Authoritative correction

Use distinct action identities:

- HR preparer.
- Finance verifier.
- Finance approver.
- Merchant Administrator high-value approver where threshold applies.
- Finance settlement recorder or automated provider integration identity.

For runs above threshold or containing exceptions, verifier, approver, and settlement recorder must be different users. For low-risk standard runs, policy may combine verifier and approver, but the settlement recorder must still differ. Automated provider settlement records the system identity and requires human reconciliation.

### Error handling and edge cases

Self-prohibited actions return `403 separation_of_duty_violation`. Editing a run after verification invalidates downstream approvals. Adding an item after approval returns the run to draft. Partial payout success creates per-item results and does not mark the whole run paid. Retrying failed items uses idempotent payout attempt IDs. Deactivated approvers do not invalidate historical decisions but cannot act further.

### Acceptance requirements

Threshold policy is versioned and snapshotted on the run. Tests cover role combinations, change invalidation, partial settlement, provider retries, reversal, and audit.

---

### 47.31.A Explicit Root Cause

Finance was treated as one actor for payout preparation, approval, execution, and reconciliation, defeating segregation of duties.

### 47.31.B Authoritative Integration Location

Sections 17.8, 27, 33 and 38. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.31.C Users and Systems Affected

HR, Finance preparers/approvers/settlement operators, Merchant Administrator, Delivery Personnel, Audit.

### 47.31.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.32 Overlapping branch-day and financial reopen terminology

**Severity:** Medium

### How the defect appears

“Reopen branch day” can mean reopening operational intake, revising a submitted cash-up, or reopening a locked financial period. Roles and APIs use overlapping words.

### Authoritative correction

Use three distinct actions and permissions:

```text
reopen_operational_branch_day
reopen_cash_up_revision
reopen_financial_period
```

Operational branch-day reopen is a Branch Manager operation controlling new requests/dispatch. Cash-up revision reopens a submitted or correction-requested cash-up for the authorized maker. Financial-period reopen is the controlled Finance workflow described in correction 22.

Each has separate states, records, routes, UI wording, audit events, and lock effects.

### Error handling and edge cases

Operational reopening must not unlock financial records. Cash-up revision cannot mutate a locked period without an executed financial reopen. Financial reopen does not automatically reopen operational intake. Attempting the wrong action returns a domain-specific conflict. Reopening across a timezone boundary uses the original branch-day date and explicit effective window.

### Acceptance requirements

Search/lint must reject ambiguous commands named only `reopen`. Tests prove each action affects only its own domain.

---

### 47.32.A Explicit Root Cause

Branch operational reopening and locked financial-period reopening used overlapping language and commands.

### 47.32.B Authoritative Integration Location

Sections 18 and 27. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.32.C Users and Systems Affected

Branch Manager, Merchant Finance, Merchant Administrator approvers, Audit and reporting.

### 47.32.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.33 Goods Sender wording implies direct personnel assignment

**Severity:** Medium

### How the defect appears

The sender is told they may “choose next-available or preferred delivery personnel,” which implies assignment authority and contradicts Dispatch ownership.

### Authoritative correction

Use this exact rule:

> The sender may select a next-available assignment preference or submit a preferred-personnel request where offered. The preference does not create or guarantee an assignment. Dispatch or an authorized assignment service creates the actual assignment after final eligibility, availability, capacity, branch, service, vehicle, risk, and policy checks.

UI controls must be labeled “Request this person” or “Prefer this person,” never “Assign,” “Choose driver,” or “Lock driver.” Pricing disclosures must state whether the fee is charged only when fulfilled.

### Error handling and edge cases

Unavailable preferences show a safe alternative without exposing staff schedules or private status. A fulfilled preference can later change due to safety, breakdown, illness, or operational necessity; notification explains the change without exposing sensitive reasons. Fee treatment follows the snapshotted quote rule. No sender can enumerate all personnel unless the merchant explicitly offers a privacy-safe eligible list.

### Acceptance requirements

Copy tests and route policies must prove senders cannot create `delivery_assignments` and that preference records are distinct.

---

### 47.33.A Explicit Root Cause

Customer preference wording used selection/locking language that implied the sender controlled the actual assignment.

### 47.33.B Authoritative Integration Location

Sections 5.7 and 7.5. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.33.C Users and Systems Affected

Goods Sender, Dispatch, Delivery Personnel, Branch Manager, HR, Support and Audit.

### 47.33.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.34 Inconsistent ownership of product-specific referral campaign configuration

**Severity:** High

### How the defect appears

One passage says each product retains its own campaign configuration, while other passages correctly assign campaign versions, reward rules, qualification periods, calculation, reversal, and payout to the central Refer & Earn platform.

### Authoritative correction

Courier owns proposed commercial inputs and factual product data:

- eligible Courier plans and product facts;
- factual activity thresholds and event definitions;
- native marketing content and campaign entry points;
- requested campaign dates and commercial proposal;
- product event emission.

The central Refer & Earn platform owns the authoritative effective-dated campaign and reward configuration, activation, attribution policy, reward calculation, qualification evaluation, holds, reversals, payout, and campaign audit.

Courier may cache a minimal read-only campaign reference/version for display and event correlation. It must not calculate rewards from local campaign rules.

### Error handling and edge cases

A Courier proposal does not become active until the central platform returns an activated campaign version. Central unavailability must not block merchant registration; attribution remains pending. Events referencing an unknown campaign version are dead-lettered and reconciled. Campaign changes are prospective and do not rewrite snapshotted referrals unless a controlled migration is approved centrally.

### Acceptance requirements

All normative wording must use “Courier proposes/provides inputs” and “central platform owns/activates.” No Courier database contains an authoritative reward-rule engine.

---

### 47.34.A Explicit Root Cause

Courier and the central platform were both described as owners of product-specific campaign configuration without separating entry-point presentation from reward-rule authority.

### 47.34.B Authoritative Integration Location

Sections 21.9 and 46. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.34.C Users and Systems Affected

Central Campaign Administrator, Courier Platform Owner, Central Referrers, prospective and existing merchants, Integration and Audit.

### 47.34.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.35 Courier and central Super Administrator authority not separated

**Severity:** High

### How the defect appears

The document names a Courier Super Administrator, Platform Owner, and central Refer & Earn Super Administrator without defining whether one identity or grant implies another.

### Authoritative correction

Use product-bound effective-dated grants:

```text
platform_user_id
product_id
role_key
scope_type
scope_reference
status
effective_from
effective_to
granted_by
approved_by
```

One internal identity may hold several grants, but each product evaluates only its own grants. Courier `platform_owner` does not automatically grant central campaign, reward, payout, fraud, or payment-method permissions. Central roles do not grant Courier merchant-governance or operational access.

### Error handling and edge cases

A user navigating to another product without a grant receives `403 product_access_required`. Revoking one product grant leaves other grants intact. Shared SSO may authenticate the identity but authorization remains product-specific. Break-glass is product and scope bound. Audit records include both identity and effective product role.

### Acceptance requirements

Cross-product authorization tests must prove no transitive privilege. UI navigation must not display inaccessible product consoles merely because SSO succeeded.

---

### 47.35.A Explicit Root Cause

High-level roles in two products were treated as equivalent rather than product-scoped grants.

### 47.35.B Authoritative Integration Location

Sections 4.1A and 46.1. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.35.C Users and Systems Affected

Courier internal platform users, central referral staff, Security, Audit and identity administrators.

### 47.35.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.36 Referral event amounts violate the minor-unit convention

**Severity:** Medium

### How the defect appears

The referral event example uses `"amount": 5000` without defining whether it means KES 5,000 or 5,000 minor units.

### Authoritative correction

Use explicit integer minor-unit fields:

```json
{
  "amount_minor": 500000,
  "currency": "KES"
}
```

Every monetary event field ends in `_minor`. Percentage rates use a separately named fixed-precision representation such as basis points or decimal strings with an explicit scale. Currency exponent is governed by the project's currency metadata; no consumer infers units from examples.

### Error handling and edge cases

Events with `amount` but no `_minor` field fail schema validation. Negative amounts are allowed only for event types explicitly representing reversals or adjustments. Currency mismatch against the source invoice is rejected or quarantined. Values exceeding configured bounds are rejected. Integer overflow and floating-point conversion are prohibited.

### Acceptance requirements

JSON Schema, examples, producers, consumers, tests, and reconciliation all use the same minor-unit rule.

---

### 47.36.A Explicit Root Cause

An event example used major-unit-looking values despite the global integer minor-unit rule.

### 47.36.B Authoritative Integration Location

Sections 0.3, 25 and 46.4. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.36.C Users and Systems Affected

Courier event publishers, central event consumers, referral billing/payout operations, Finance, Integration and Audit.

### 47.36.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.37 merchant_activity_qualified implies Courier made the final reward decision

**Severity:** Medium

### How the defect appears

Courier emits `merchant_activity_qualified`, while the central platform is supposed to decide final reward qualification. The event name conflates factual product threshold with campaign qualification.

### Authoritative correction

Rename factual events to:

```text
merchant_activity_threshold_met
merchant_activity_threshold_not_met
```

The payload includes threshold definition/version, measurement period, measured value, required value, source facts, evaluation timestamp, and correction sequence. Courier states only that its product-specific factual threshold was met.

The central platform emits or records final outcomes such as:

```text
referral_month_qualified
referral_month_not_qualified
reward_created
reward_held
```

### Error handling and edge cases

Late-arriving Courier facts create corrected threshold events with a higher revision rather than deleting the original. The central platform re-evaluates according to policy. Duplicate revisions are idempotent. A threshold event without the corresponding subscription facts does not create a reward. A central campaign may require additional fraud, attribution, retention, or payment conditions.

### Acceptance requirements

No Courier event name may claim final referral/reward qualification. Contract tests verify producer and consumer vocabulary.

---

### 47.37.A Explicit Root Cause

A Courier factual event name encoded a central qualification decision.

### 47.37.B Authoritative Integration Location

Sections 46.4 and 46.8. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.37.C Users and Systems Affected

Central Qualification Reviewers, Campaign Administrators, Fraud/Risk, Referrers, Courier integration operators and Audit.

### 47.37.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.38 Product-event signature format is not canonical

**Severity:** Medium

### How the defect appears

The example embeds `signature` inside the JSON body without defining whether the signature covers itself, JSON field order, whitespace, timestamp tolerance, algorithm, or key identifier.

### Authoritative correction

Use a detached Ed25519 signature over a canonical string derived from raw body bytes:

```text
X-Citrus-Key-Id
X-Citrus-Timestamp
X-Citrus-Event-Id
X-Citrus-Signature-Alg: ed25519
X-Citrus-Signature
```

The signed message is:

```text
<timestamp>\n<event_id>\n<SHA-256-hex-of-raw-request-body>
```

The receiver validates key ID, active key window, event ID equality with body, timestamp tolerance, signature, product source, event schema version, and idempotency. The JSON body contains no signature field.

Key rotation supports overlapping verification windows and explicit revocation. Private keys remain in a secrets manager/HSM boundary; only public verification keys are distributed.

### Error handling and edge cases

Unknown key returns `401 unknown_signing_key`. Invalid signature returns `401 invalid_signature`. Stale timestamp returns `401 signature_timestamp_out_of_window`. A valid replay returns an idempotent success if the event body hash matches, or `409 event_id_payload_conflict` if it differs. Proxies must not alter body bytes after signing. Compression policy must define whether signing occurs before or after content encoding; launch should sign the transmitted raw body.

### Acceptance requirements

Interoperability tests use fixed vectors for body, hash, string-to-sign, signature, rotation, replay, and whitespace changes.

---

### 47.38.A Explicit Root Cause

The signature was described both as event-body data and as transport authentication, leaving canonical bytes and verification ambiguous.

### 47.38.B Authoritative Integration Location

Sections 46.5 through 46.8. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.38.C Users and Systems Affected

Platform Integration Operators, central consumers, Security, key custodians, Incident Response and Audit.

### 47.38.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.39 Historical correction specification embedded in the final normative scope

**Severity:** High

### How the defect appears

The scope reproduces a large prior correction register and states that the register prevails over older unchanged sentences. This explicitly admits contradictory normative prose remains and forces readers to resolve precedence manually.

### Authoritative correction

Remove the historical correction specification from the normative scope. Publish it as a separately versioned non-normative change log or decision record. The regenerated main scope contains only final rules.

The main scope header identifies its version, effective date, superseded versions, and normative appendices. Historical rationale links to decision records but cannot override current body text.

### Error handling and edge cases

Build tooling must detect duplicate major sections, copied correction headers, or precedence phrases such as “where this conflicts, this register prevails.” A release candidate containing unresolved conflict markers fails publication. References from tickets or old documents use stable decision IDs so moving history does not break traceability.

### Acceptance requirements

A reader can determine every rule from the main scope and normative machine-readable annexes without consulting a historical precedence register.

---

### 47.39.A Explicit Root Cause

Historical correction prose was copied into the normative scope instead of integrating the decisions and removing precedence text.

### 47.39.B Authoritative Integration Location

Sections 0.2, 42, 47 and 48. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.39.C Users and Systems Affected

Every scope reader and implementer, Product, Architecture, Engineering, QA, Security, Operations and external stakeholders.

### 47.39.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.40 Stray quote and numbering artifacts

**Severity:** Low

### How the defect appears

Standalone quote marks and an orphaned `3.` appear before a later section. They have no semantic meaning and indicate uncontrolled paste composition.

### Authoritative correction

Delete the artifacts and run Markdown structural lint. Enforce no orphan ordered-list items, unmatched quotation fences, malformed code fences, duplicate headings, or unexplained standalone punctuation between sections.

### Error handling and edge cases

Lint must ignore valid quoted examples and code blocks while flagging top-level orphan text. Generated documents should preserve line-ending and Unicode normalization. Manual suppression requires a documented lint directive, not silent ignoring.

### Acceptance requirements

The published Markdown parses without structural warnings and renders identically in supported documentation tooling.

---

### 47.40.A Explicit Root Cause

Large manual document assembly left unmatched quote/numbering artifacts without structural validation.

### 47.40.B Authoritative Integration Location

Sections 42 and 48. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.40.C Users and Systems Affected

Documentation owners, Product, Architecture, QA, Engineering and release reviewers.

### 47.40.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.41 Validation appendix checks presence rather than semantic consistency

**Severity:** High

### How the defect appears

The appendix checks UTF-8, heading order, code-fence balance, and keyword presence, but does not detect contradictory routes, enums, schemas, role definitions, or release classes. Presence is mistaken for correctness.

### Authoritative correction

Implement semantic lint and cross-reference validation:

- one canonical enum per domain;
- every transition source/target exists;
- every launch capability maps to one route or command;
- no Post-MVP route is deployed;
- no deprecated route appears in normative OpenAPI;
- every role maps to permissions, onboarding, landing page, and identity domain;
- every table is declared once;
- every schema field has one authoritative meaning;
- no generic state PATCH exists;
- money fields use `_minor` and currency;
- release manifest, plan entitlements, roadmap, and UI feature flags agree;
- all external events have schemas, signature rules, idempotency, and ownership.

Use machine-readable YAML/JSON registries as the source for generated documentation rather than parsing prose alone.

### Error handling and edge cases

Validation failures block publication and identify conflicting locations. Suppressions require an owner, reason, expiry, and approval. Generated sections include source registry version and checksum. A changed registry without regenerated docs fails CI.

### Acceptance requirements

The exact contradictions named in the supplied register must be represented as regression tests so they cannot reappear unnoticed.

---

### 47.41.A Explicit Root Cause

The validation appendix checked strings and section presence rather than whether independently generated artifacts expressed the same meaning.

### 47.41.B Authoritative Integration Location

Sections 42 and 48. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.41.C Users and Systems Affected

Product, Architecture, Engineering, QA, Security, Operations and release governance.

### 47.41.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.42 Claimed complete transition registries are not actually supplied

**Severity:** High

### How the defect appears

The upgrade summary declares complete registries authoritative, yet the document does not provide complete transition metadata for every domain and the validation appendix only checks section presence.

### Authoritative correction

Create versioned files such as:

```text
state-machines/request.v1.yaml
state-machines/quote.v1.yaml
state-machines/payment-requirement.v1.yaml
state-machines/assignment.v1.yaml
state-machines/custody-handover.v1.yaml
state-machines/operational-delivery.v1.yaml
state-machines/redelivery.v1.yaml
state-machines/dispute.v1.yaml
state-machines/refund.v1.yaml
state-machines/record-lifecycle.v1.yaml
```

Generate backend guards, OpenAPI action documentation, UI allowed-action maps, audit event names, and test cases from these registries where practical. Manual code must still verify permissions and evidence; generation does not weaken policy.

### Error handling and edge cases

Registry loading failure prevents deployment. Unknown actions fail closed. Version upgrades require migration rules for active records. A state removed from a new version remains interpretable for historical records. Long-running workflows retain the registry/configuration version under which they started unless an explicit migration applies.

### Acceptance requirements

CI proves completeness, reachability, terminal-state rules, action uniqueness, permission references, error-code references, and route linkage. The prose summary may claim completeness only after these checks pass.

---

### 47.42.A Explicit Root Cause

The scope asserted complete registries before supplying machine-readable transitions for every declared domain.

### 47.42.B Authoritative Integration Location

Sections 5, 33 and 42. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.42.C Users and Systems Affected

Every human and machine actor governed by a lifecycle registry, plus Engineering, QA and Audit.

### 47.42.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.


## 47.43 Duplicated and conflicting routes undermine API traceability

**Severity:** High

### How the defect appears

The scope claims every launch capability has a route or command, but contains duplicate staff deactivation paths, old and corrected sender authentication routes, generic and action-specific state mutation, launch optimization routes for deferred capability, and generic reopen routes alongside controlled workflows. Route presence alone therefore proves nothing.

### Authoritative correction

Create a canonical route registry with one row-equivalent object per route or command containing:

```text
operation_id
capability_id
method
path_or_command
release_class
actor_classes
permission
scope_rule
state_action
idempotency_required
audit_event
deprecation_status
replacement_operation_id
```

Although the registry is structured data, the normative prose must list only active routes by release group. Duplicate semantic operations require one canonical operation ID. Compatibility aliases are temporary, forward internally without changing semantics, emit deprecation telemetry, and have a removal version.

Specific removals and replacements are mandatory:

- Remove unscoped sender-auth routes.
- Restrict generic delivery PATCH and remove generic personnel status mutation.
- Remove the launch full-optimization route.
- Replace generic financial reopen with request/approve/execute commands.
- Consolidate staff deactivation into one operation and distinguish emergency security suspension from HR lifecycle deactivation.

### Error handling and edge cases

A deprecated alias must preserve idempotency and authorization of the canonical command; it cannot bypass new requirements. A route with the same method/path but different operation meaning fails registry validation. A capability with two active mutation routes fails CI unless one is explicitly a different action. SDK generation excludes deprecated routes after the compatibility window.

### Acceptance requirements

Traceability tests must prove one canonical mutation path per business action, no contradictory active routes, complete permission/state linkage, and release-class consistency. Route count is not accepted as evidence of coverage.

---


# Required integrated-scope regeneration procedure

The corrected scope must be regenerated rather than patched by appending more precedence text.

### Step 1: Freeze and identify sources

Assign a version to the current integrated scope and mark it superseded. Preserve it for history. Freeze new edits while the canonical registries and decisions are applied.

### Step 2: Build canonical machine-readable registries

Create registries for identity domains, role grants, permissions, state machines, route/command traceability, release classes, plan entitlements, money/event schemas, audit events, and errors. Each registry has an owner, version, effective date, and validation rules.

### Step 3: Rewrite normative prose from final decisions

Rewrite account taxonomy, onboarding, state machines, data model, APIs, plans, implementation phases, referral integration, validation, and acceptance criteria. Remove old alternatives, “or” release decisions, historical correction blocks, duplicate entities, and deprecated routes.

### Step 4: Publish schema and migration maps

Publish the final canonical schema and explicit migration/deprecation maps for identity, integration clients, delivery status, preferred personnel, payment/refund status, and billing ledger changes. Every legacy field receives one disposition: migrate, derive, retain as read-only compatibility view, or remove.

### Step 5: Generate and validate contracts

Generate or validate OpenAPI, event JSON Schemas, detached-signature test vectors, route registry, state-transition tests, permission matrix, entitlement manifest, and onboarding requirements. Documentation references their versions/checksums.

### Step 6: Execute controlled migration

Profile production or seed data, back up, migrate in deterministic batches, reconcile counts and financial totals, quarantine ambiguous records, revoke contradictory credentials/routes, and preserve immutable audit. Do not rely on hidden dual writes.

### Step 7: Run cross-domain acceptance suites

Mandatory suites include:

- platform-versus-merchant identity isolation;
- merchant/branch/organization/own-scope denial;
- role onboarding and MFA;
- state transition legality and concurrency;
- request/preference/assignment separation;
- invoice/payment/dispute/refund combinations;
- financial locks and reopen approval;
- refund and payout maker/checker;
- Starter operational viability;
- Multi-Branch launch controls;
- API/webhook entitlement and security;
- referral event units, names, signatures, retries, and ownership;
- deprecated route rejection;
- semantic document lint.

### Step 8: Publication gate

The scope may be called “integrated authoritative” only when:

- no unresolved contradiction is listed;
- all canonical registries pass validation;
- all Mandatory MVP and Launch Entitlement capabilities have implemented commands;
- no Post-MVP capability is exposed as launch production behavior;
- migrations and rollback plans are approved;
- acceptance tests pass;
- product, engineering, security, finance, support, and QA owners sign the version.

# Final risk position

Implementation from the uncorrected scope has a high probability of divergent team interpretations because contradictory definitions exist in identity, state, schema, route, plan, financial-control, and referral boundaries. Applying only wording fixes without machine-readable registries and migration control would reduce presentation defects but leave executable inconsistency. The correction is complete only when the obsolete behavior is impossible in schema, authorization, routes, services, UI, jobs, events, and tests.

### 47.43.A Explicit Root Cause

Routes accumulated across revisions without one canonical route registry, semantic duplicate detection, or removal policy.

### 47.43.B Authoritative Integration Location

Sections 27, 33 and 42. The normative rule in those sections supersedes quoted defective wording in this evidence annex.

### 47.43.C Users and Systems Affected

All API clients and human users invoking commands, Engineering, SDK owners, QA, Security, Support and release governance.

### 47.43.D Manifesto Test and Resolution Proof

Resolution requires the positive and negative tests named in Sections 33 and 38, plus migration or semantic-gate evidence applicable to this item. The former behaviour must be absent from active schemas, route registries, policies, user interfaces, workers, generated contracts, and production configuration. Evidence is accepted only when checksum-linked to the release candidate under Sections 42 and 48.

# 48. Final Publication, Migration, Test, and Demonstrated-Resolution Gate

This section is the binding completion procedure for making the regenerated scope operationally authoritative.

## 48.1 Required Artifacts

Before approval, the release owner must produce:

1. the final Markdown scope and checksum;
2. canonical identity, role, permission, and segregation-of-duty registries;
3. complete state-machine registries;
4. canonical schema catalog and forward/rollback migrations;
5. legacy table, field, route, enum, event, and screen deprecation map;
6. normative OpenAPI and internal-command registry;
7. release-class and plan-entitlement manifests;
8. error-code and audit-event registries;
9. product-event JSON Schemas, detached-signature profile, and test vectors;
10. capability-to-owner-to-route-to-screen-to-audit-to-test traceability;
11. data profiling, reconciliation, and migration reports;
12. security, privacy, accessibility, performance, backup, restore, and disaster-recovery reports;
13. user-communication and support runbooks for behaviour-changing migrations;
14. the forty-three-item resolution report generated from Section 47.

## 48.2 Migration Procedure

For every replaced authority:

1. freeze schema/route additions outside the canonical register;
2. profile legacy data and identify nulls, invalid enums, duplicates, orphan references, cross-tenant defects, and conflicting sources;
3. declare deterministic winner/backfill rules and obtain domain-owner approval;
4. create new canonical records and immutable migration links;
5. dual-read only for a bounded verification window where unavoidable;
6. prohibit indefinite dual write; writes move to the canonical source in one controlled cutover;
7. reconcile counts, sums, state distributions, ownership, and sample histories;
8. expose deprecated-use telemetry and reject new deprecated clients after the compatibility deadline;
9. remove or convert legacy structures to read-only compatibility views;
10. rehearse rollback without restoring contradictory write authority;
11. retain migration actor, batch, rule version, old/new identifiers, timestamp, checksum, and outcome in append-only audit.

Records that cannot be deterministically migrated enter a quarantined review queue. They are not guessed. Affected users receive a safe operational status and support path. Financial records remain locked against mutation until reconciled. Cross-tenant anomalies trigger Security and Audit review.

## 48.3 Test Execution and Failure Handling

All suites in Section 33 and all criteria in Section 38 must pass. Tests run against production-equivalent configuration, including every launch plan, role combination, branch/organization scope, billing state, provider degraded mode, and migration path.

A failed tenant-isolation, authorization, credential, money, ledger, signature, replay, or state-integrity test is release-blocking with no waiver. Other failures require a documented exception only where Section 42 permits one. Flaky tests are failures until root cause is proven and corrected; retries alone are not evidence.

Provider-dependent tests use certified sandboxes plus deterministic contract simulators for timeout, malformed callback, duplicate callback, out-of-order callback, partial success, quota exhaustion, and outage. Manual evidence is accepted only for controls that cannot be automated and must include reviewer, date, environment, inputs, outputs, and reproducible steps.

## 48.4 User-Impact Review

Every change is reviewed against:

- Platform Owner and each internal role;
- Merchant Administrator, Branch Manager, HR, Finance, Dispatch, Personnel, Support, and Audit;
- Goods Sender and Goods Recipient Access;
- every business-customer organization role;
- Business Customer Integration and System Integration identities;
- central referral roles and Central Referrers;
- support, security, finance, operations, sales, migration, and compliance teams.

The review identifies changed permissions, removed actions, new approvals, session/credential revocation, data migration, downtime/degraded behaviour, UI copy, training, notification, reporting, and support consequences. No affected-user statement may claim “no impact” without evidence.

## 48.5 Demonstration of Resolution

Resolution is demonstrated when:

1. all forty-three evidence items point to implemented normative rules;
2. semantic lint finds no conflicting authority in the normative body;
3. generated artifacts match the approved checksum;
4. deprecated writers are unreachable;
5. migration reconciliation is complete or explicitly quarantined;
6. positive, denial, concurrency, idempotency, edge, and recovery tests pass;
7. monitoring can detect regression into a removed behaviour;
8. support and incident runbooks are approved;
9. Product, Architecture, Engineering, QA, Security, Finance/Compliance, and Operations sign the same evidence bundle;
10. the released application behaves according to this scope under controlled acceptance demonstration.

## 48.6 Post-Release Verification

After deployment, automated canaries verify authentication scope, route registry version, transition registry version, billing restriction allowlists, outbox delivery, signature verification, financial allocation, and audit ingestion. Migration and deprecated-use telemetry are monitored through the defined observation window. Any evidence of a contradictory writer or authority triggers incident response, containment, root-cause analysis, corrective migration, and renewed release-gate review.

This scope may be marked **Authoritative and Released** only after the above evidence is retained under the approved version and checksum.

# 49. Generated Scope Validation Evidence

## 49.1 Validation Boundary

This section records document-level validation performed while generating this corrected edition. It proves structural integration and absence of the specifically identified contradictory executable wording in the normative body. It does not claim that application code, database migrations, provider sandboxes, production infrastructure, or runtime acceptance tests already exist. Those remain release-blocking obligations under Sections 33, 38, 42, and 48.

## 49.2 Inputs and Transformation Controls

The generator read the complete uploaded authoritative scope and the complete forty-three-item contradiction-correction specification. It parsed the scope by top-level numbered section, removed the embedded competing historical correction section, replaced contradiction-owning sections with canonical rules, patched retained sections at their owning subsections, added the complete non-normative evidence annex, and rebuilt the file in numeric order.

No web source or unrelated external document was used to invent project requirements. The original product capability sections were retained unless a correction required replacement, consolidation, or removal of duplicated authority.

## 49.3 Structural Validation Results

The generated file passed these checks:

- UTF-8 Markdown write completed successfully;
- top-level numbered sections are unique and sequential from 0 through 49;
- all Markdown fenced code blocks are balanced;
- Sections 0 through 46 contain the normative product scope;
- Section 47 contains exactly forty-three contradiction-resolution evidence items;
- every Section 47 item contains an explicit root-cause block, authoritative integration location, affected-user block, and manifesto test/resolution-proof block;
- Sections 48 and 49 contain publication controls and static generation evidence rather than a second competing scope;
- the canonical account, state, schema, API, billing, release, referral, migration, error, edge-case, test, and acceptance sections are present.

## 49.4 Semantic Contradiction-Signature Results

The normative body was checked and contains none of the following former executable definitions:

- detailed quote-approval actors stored as quote status values such as `pending_branch_manager_approval`;
- the invalid target `handover_pending → accepted by incoming assignee`;
- the physical transition `delivered → closed`;
- `refunded_externally` as a platform payment-attempt state;
- `transaction_fixed_fee_snapshot` in the percentage-fee ledger;
- the unscoped sender-authentication route `POST /api/v1/sender/auth/request`;
- the generic personnel delivery-status mutation route;
- the generic financial-period reopen route;
- the launch full-optimizer route;
- the decision-claiming event name `merchant_activity_qualified`;
- the prior embedded “Comprehensive Correction and Resolution Specification” as a normative section.

Former wording may appear inside Section 47 solely as quoted evidence of the proven defects. Such evidence is explicitly non-normative and cannot be generated into schemas, routes, policies, SDKs, or UI actions.

## 49.5 Canonical-Authority Results

Static inspection confirmed that the normative body declares:

- one highest Courier platform role key, `platform_owner`, with Super Administrator as a UI label;
- one canonical internal platform identity domain;
- organization-scoped business-customer human role grants;
- separate human, token-access, and machine-identity classifications;
- orthogonal lifecycle domains and command-specific state mutation;
- a separate administrative record lifecycle rather than physical `closed` status;
- invoice settlement, payment, dispute, and refund authority separation;
- one canonical identity-schema migration path and one client/credential model per identity domain;
- merchant-scoped sender authentication;
- normative launch routes and internal commands;
- separated financial reopen commands;
- basic launch routing and Post-MVP full optimization;
- launch-implemented core API/webhooks and Multi-Branch controls wherever sold;
- refund and payout segregation of duties;
- central campaign/reward authority separated from Courier entry-point/product-fact authority;
- integer minor-unit event amounts, neutral factual event names, detached signatures, replay protection, and transactional outbox delivery.

## 49.6 Remaining Proof Required Before Release

Document generation is not software verification. The following evidence is still mandatory before the scope may be marked Authoritative and Released:

- generated OpenAPI, schema migrations, state registries, permission registry, release manifest, event schemas, and detached-signature vectors;
- source-code and database-policy review;
- migration profiling, reconciliation, and rollback rehearsal;
- complete positive, negative, authorization, concurrency, idempotency, edge, degraded-mode, privacy, and recovery tests;
- end-to-end provider and central Refer & Earn integration tests;
- accessibility, performance, security, backup/restore, and disaster-recovery evidence;
- operational demonstration and cross-functional sign-off under Section 48.


