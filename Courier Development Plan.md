# Courier by Citrus — Production-Grade SaaS Development Plan

This plan treats **Courier by Citrus** as a multi-tenant delivery management SaaS for transport service providers, senders, recipients, drivers, dispatchers, finance users, support users, and SaaS super administrators. The source scope defines the product as a Point A to Point B delivery operations platform covering request intake, route planning, live status, proof of delivery, billing, reporting, and administrator oversight.  The attached implementation brief requires a production-ready, secure, scalable, testable plan that an IDE-based coding agent can execute step by step, including proof, root-cause analysis, testing, and demonstrated resolution. 

The selected stack is **Laravel + PHP 8.2+ + Laravel Sanctum + React + TypeScript + Tailwind CSS + PostgreSQL + Redis + S3-compatible storage + Docker + CI/CD**. This matches the project’s required SaaS stack and tenant-isolation requirements.  Laravel’s current official documentation describes Sanctum as suitable for SPA/API authentication, while Passport is the OAuth2 server option and should be reserved for explicit OAuth2 needs. ([Laravel][1])

---

## 1. Executive Architecture Summary

Courier by Citrus should be built as a **shared-database, shared-schema, tenant-scoped SaaS**.

### Recommended architecture

| Layer              | Decision                                                                     |
| ------------------ | ---------------------------------------------------------------------------- |
| Backend            | Laravel API application                                                      |
| Frontend           | React + TypeScript SPA                                                       |
| Auth               | Laravel Sanctum for SPA session/API authentication                           |
| Database           | PostgreSQL                                                                   |
| Tenant model       | `transport_providers` as tenant/account root                                 |
| Tenant key         | `transport_provider_id` on every provider-owned table                        |
| Public identifiers | ULIDs exposed to client; internal IDs never exposed                          |
| Authorization      | Laravel Policies + Gates + Spatie Permission-compatible permission model     |
| Queue              | Redis-backed Laravel queues + Horizon                                        |
| Cache              | Redis                                                                        |
| Storage            | Private S3-compatible storage with signed URLs                               |
| Maps               | Google Maps JavaScript API, Places Autocomplete, Geocoding, Routes API       |
| Search             | PostgreSQL search first; Meilisearch/Typesense post-MVP                      |
| Deployment         | Dockerized app, queue workers, scheduler, CI/CD                              |
| Observability      | Structured logs, audit logs, error tracking, queue monitoring, uptime checks |

Laravel Horizon provides queue monitoring for Redis queues, including throughput, runtime, and job failure visibility, which fits the notification, report, export, and async processing needs. ([Laravel][2]) Laravel also includes a production health route suitable for uptime monitors, load balancers, and orchestration systems. ([Laravel][3])

### Core product flow

1. Sender creates a delivery request.
2. System validates pickup and destination.
3. System calculates distance, ETA, and quote.
4. Provider accepts or quotes.
5. Dispatcher assigns driver and vehicle.
6. Driver confirms pickup.
7. Delivery moves through tracked statuses.
8. Recipient confirms delivery via OTP/photo/name/GPS proof.
9. Invoice/receipt/reporting records are generated.
10. Audit log records sensitive actions.

### Most realistic projections

| Projection                                                                                     | Likelihood |
| ---------------------------------------------------------------------------------------------- | ---------: |
| Successful MVP delivery with disciplined scope                                                 |    **75%** |
| Delay if route optimization, native apps, wallet, accounting, and advanced analytics enter MVP |    **85%** |
| Driver portal failing operationally if desktop-only                                            |    **90%** |
| Full route optimization needed in MVP                                                          |    **40%** |
| Route optimization needed after scale                                                          |    **80%** |
| Google Maps cost overrun without quotas/restrictions                                           |    **65%** |
| Tenant leakage risk if rushed                                                                  |    **70%** |
| Tenant leakage risk with disciplined policy + tests                                            |    **35%** |

---

## 2. Assumptions and Constraints

### Assumptions

| Area               | Assumption                                            | Rationale                                                       |
| ------------------ | ----------------------------------------------------- | --------------------------------------------------------------- |
| Market             | Kenya/East Africa is likely initial market            | Scope mentions M-Pesa as likely essential for Kenya/East Africa |
| Payment            | MVP supports manual invoice + one gateway abstraction | Payment gateway varies by country                               |
| Recipient accounts | Not required for MVP                                  | Secure magic link/OTP is enough initially                       |
| Driver app         | Mobile-first PWA, not native app                      | Faster MVP; native apps create delay risk                       |
| Route optimization | Post-MVP                                              | Complex and cost-sensitive                                      |
| Live tracking      | Status tracking first, live GPS later                 | Avoid overpromising before real-time driver location is stable  |
| SaaS tenancy       | Provider = tenant/account                             | Scope requires provider isolation                               |
| Frontend           | React + TypeScript                                    | Type-safe SPA, scalable component structure                     |
| Admin              | Super admin isolated from tenant admin flows          | Scope separates SaaS owner/operator from provider               |

### Constraints

1. No jQuery.
2. No frontend-only authorization.
3. No public sequential IDs in APIs.
4. Every tenant-owned record requires `transport_provider_id`.
5. Every tenant-owned query must be scoped.
6. Every mutating endpoint must use Form Request validation.
7. Every protected endpoint must authenticate and authorize.
8. Every large collection must paginate.
9. Every slow operation must be queued.
10. Every sensitive operation must be audited.

---

## 3. Non-Negotiable Security Rules

1. **Backend authorization is mandatory.** Frontend permission checks only improve UX.
2. **Tenant isolation is mandatory.** A Provider A user must never access Provider B deliveries, senders, recipients, invoices, drivers, vehicles, proofs, files, reports, or webhooks.
3. **No unrestricted Maps API keys.** Google explicitly recommends API key restrictions with application and API restrictions, and production Maps keys must be restricted. ([Google for Developers][4])
4. **No secrets in frontend code.** Only restricted browser-safe public Maps keys may be exposed.
5. **No sensitive data in logs.** Never log passwords, tokens, OTPs, payment payload secrets, API keys, full card/mobile money secrets, or signed URLs.
6. **No unbounded queries.** Pagination is mandatory.
7. **No direct S3 public files.** Use private buckets and signed temporary URLs.
8. **No silent failures.** Errors must produce structured logs, user-safe responses, and observable failure records.
9. **No CSS-driven behavior.** CSS styles states; JavaScript/backend controls state changes.
10. **No JavaScript device detection for responsiveness.** Use CSS media queries only.
11. **No disabled zoom.** Mobile viewport must allow browser zoom.
12. **No production without automated test pass.**

---

## 4. System Architecture

### Runtime components

```text
Browser / PWA
  |
  | HTTPS
  v
Laravel API + React SPA host
  |
  +--> PostgreSQL
  +--> Redis cache
  +--> Redis queues
  +--> S3-compatible storage
  +--> Google Maps APIs
  +--> Payment gateway adapter
  +--> SMS / Email providers
  +--> Error tracking / logs / metrics
```

### Application boundaries

| Boundary                         | Responsibility                                               |
| -------------------------------- | ------------------------------------------------------------ |
| `app/Domain/Tenancy`             | Tenant resolution, current tenant context, tenant assertions |
| `app/Domain/Auth`                | Auth events, email verification, MFA hooks                   |
| `app/Domain/Providers`           | Transport provider account, subscription, settings           |
| `app/Domain/Deliveries`          | Delivery requests, statuses, items, route estimates          |
| `app/Domain/Dispatch`            | Driver/vehicle assignment, job board                         |
| `app/Domain/Proof`               | OTP/photo/signature/name/GPS delivery proof                  |
| `app/Domain/Billing`             | Quotes, invoices, payments, refunds                          |
| `app/Domain/Notifications`       | Email/SMS/in-app events                                      |
| `app/Domain/Files`               | Uploads, validation, private storage                         |
| `app/Domain/Audit`               | Immutable audit log records                                  |
| `app/Domain/Admin`               | Super admin workflows                                        |
| `resources/js` or `frontend/src` | React SPA                                                    |

### Recommended repository structure

```text
courier-by-citrus/
  app/
    Domain/
      Tenancy/
      Providers/
      Deliveries/
      Dispatch/
      Proof/
      Billing/
      Notifications/
      Files/
      Audit/
      Admin/
    Http/
      Controllers/Api/V1/
      Middleware/
      Requests/
      Resources/
    Models/
    Policies/
    Jobs/
    Events/
    Listeners/
  database/
    migrations/
    seeders/
    factories/
  routes/
    api.php
    web.php
    console.php
  resources/js/
    app.tsx
    api/
    components/
    features/
    layouts/
    routes/
    stores/
    styles/
    test/
  tests/
    Unit/
    Feature/
    Api/
    Security/
    Browser/
  docker/
  .github/workflows/
```

---

## 5. Backend Architecture

### Laravel backend standards

| Area           | Standard                            |
| -------------- | ----------------------------------- |
| Controllers    | Thin; delegate to services/actions  |
| Validation     | Form Request classes                |
| Output         | API Resources                       |
| Writes         | Database transactions               |
| Tenant context | Middleware + request-scoped service |
| Auth           | Sanctum                             |
| Authorization  | Policies/Gates                      |
| Jobs           | Queue tenant context explicitly     |
| Errors         | Structured JSON envelope            |
| IDs            | ULID route binding                  |
| Logs           | JSON structured logs                |

### Backend command baseline

```bash
composer create-project laravel/laravel courier-by-citrus
cd courier-by-citrus

composer require laravel/sanctum
php artisan install:api

composer require spatie/laravel-permission
composer require laravel/horizon
composer require laravel/scout
composer require predis/predis

npm create vite@latest resources/js -- --template react-ts
npm install
```

### Domain action pattern

Use single-purpose action classes for workflows:

```text
app/Domain/Deliveries/Actions/CreateDeliveryRequest.php
app/Domain/Deliveries/Actions/GenerateDeliveryReference.php
app/Domain/Deliveries/Actions/TransitionDeliveryStatus.php
app/Domain/Dispatch/Actions/AssignDriverToDelivery.php
app/Domain/Proof/Actions/RecordProofOfDelivery.php
app/Domain/Billing/Actions/CreateDeliveryQuote.php
app/Domain/Audit/Actions/WriteAuditLog.php
```

Each action must:

1. Accept validated DTO/input only.
2. Resolve tenant context.
3. Authorize the actor or receive pre-authorized command.
4. Use DB transaction for multi-table writes.
5. Emit domain events after successful commit.
6. Write audit log for sensitive action.
7. Return model/resource-safe data.

---

## 6. Frontend Architecture

### React + TypeScript SPA structure

```text
resources/js/
  api/
    client.ts
    errors.ts
    auth.ts
    deliveries.ts
    dispatch.ts
    billing.ts
    providers.ts
  components/
    ui/
      Button.tsx
      Input.tsx
      Select.tsx
      Modal.tsx
      Toast.tsx
      StatusChip.tsx
      DataTable.tsx
      EmptyState.tsx
    forms/
    maps/
    layout/
  features/
    auth/
    dashboard/
    deliveries/
    dispatch/
    driver/
    tracking/
    billing/
    settings/
    admin/
  layouts/
    AuthLayout.tsx
    ProviderAppLayout.tsx
    DriverLayout.tsx
    PublicTrackingLayout.tsx
    SuperAdminLayout.tsx
  routes/
    AppRouter.tsx
    protectedRoutes.tsx
  stores/
    authStore.ts
    tenantStore.ts
    themeStore.ts
    permissionStore.ts
  styles/
    tokens.css
    app.css
```

### Frontend rules

1. Centralize API calls in `api/client.ts`.
2. Store auth user, active provider, and permissions in memory/store.
3. Persist theme preference locally before auth and server-side after auth.
4. Never infer authorization from UI state.
5. Render permission-aware navigation, but expect server `403` as source of truth.
6. Use semantic HTML for forms, tables, nav, dialogs.
7. Lazy-load heavy modules: maps, reports, route planning, admin analytics.
8. Treat all server text as unsafe unless escaped.

### Route groups

```text
/public/track/:trackingToken
/login
/register
/forgot-password
/provider/select
/app/dashboard
/app/deliveries
/app/deliveries/:publicId
/app/dispatch
/app/drivers
/app/vehicles
/app/billing
/app/reports
/app/settings/team
/app/settings/profile
/driver/jobs
/driver/jobs/:publicId
/admin/providers
/admin/subscriptions
/admin/audit-logs
```

---

## 7. Database Architecture

### Identifier policy

| ID type                        | Usage                             |
| ------------------------------ | --------------------------------- |
| `id bigserial`                 | Internal relational PK only       |
| `public_id ulid unique`        | API/client routes                 |
| `transport_provider_id bigint` | Tenant ownership                  |
| `created_by_user_id`           | Actor trace                       |
| `deleted_at`                   | Soft delete where recovery needed |

### Core database tables

| Table                      | Purpose                         | Key columns                                                                                                                                                                                                                                                                                          | Constraints / indexes                                                 | Tenant-owned      | Retention                              |
| -------------------------- | ------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- | ----------------- | -------------------------------------- |
| `users`                    | Global identity                 | `id`, `public_id`, `name`, `email`, `password`, `email_verified_at`, `mfa_enabled`, `last_login_at`, `status`                                                                                                                                                                                        | unique `email`, unique `public_id`, index `status`                    | No                | Retain while account/legal need exists |
| `transport_providers`      | Tenant root                     | `id`, `public_id`, `legal_name`, `display_name`, `status`, `country`, `timezone`, `approved_at`, `suspended_at`                                                                                                                                                                                      | unique `public_id`, index `status`                                    | Root              | Soft delete; retain audit              |
| `provider_users`           | Memberships                     | `id`, `transport_provider_id`, `user_id`, `role_id`, `status`, `invited_by_user_id`, `joined_at`                                                                                                                                                                                                     | unique provider/user, index provider/status                           | Yes               | Soft delete                            |
| `roles`                    | Role definitions                | `id`, `public_id`, `name`, `scope`, `transport_provider_id nullable`                                                                                                                                                                                                                                 | unique `scope/name/provider`                                          | Nullable          | Retain                                 |
| `permissions`              | Permission catalog              | `id`, `name`, `description`                                                                                                                                                                                                                                                                          | unique `name`                                                         | No                | Retain                                 |
| `role_permission`          | Permission mapping              | `role_id`, `permission_id`                                                                                                                                                                                                                                                                           | composite PK                                                          | Nullable via role | Retain                                 |
| `invitations`              | Staff onboarding                | `id`, `public_id`, `transport_provider_id`, `email`, `role_id`, `token_hash`, `expires_at`, `accepted_at`, `revoked_at`                                                                                                                                                                              | unique active provider/email, index token hash                        | Yes               | Delete/anonymize expired after 90 days |
| `senders`                  | Sender profiles                 | `id`, `public_id`, `transport_provider_id`, `user_id nullable`, `name`, `phone`, `email`, `type`, `risk_flag`                                                                                                                                                                                        | provider/email index, provider/phone index                            | Yes               | Soft delete                            |
| `recipients`               | Recipient profiles              | `id`, `public_id`, `transport_provider_id`, `name`, `phone`, `email`, `risk_flag`                                                                                                                                                                                                                    | provider/phone index                                                  | Yes               | Soft delete                            |
| `addresses`                | Pickup/destination/address book | `id`, `public_id`, `transport_provider_id`, `owner_type`, `owner_id`, `formatted_address`, `place_id`, `lat`, `lng`, `landmark`, `validation_status`                                                                                                                                                 | provider owner index, geospatial index if needed                      | Yes               | Soft delete                            |
| `delivery_requests`        | Delivery job root               | `id`, `public_id`, `transport_provider_id`, `sender_id`, `recipient_id`, `pickup_address_id`, `destination_address_id`, `reference`, `tracking_token_hash`, `status`, `service_level`, `scheduled_pickup_at`, `delivery_window_start`, `delivery_window_end`, `quoted_amount`, `currency`, `version` | unique provider/reference, provider/status index, provider/date index | Yes               | Retain 7 years for billing/audit       |
| `delivery_items`           | Goods/package lines             | `id`, `public_id`, `transport_provider_id`, `delivery_request_id`, `category`, `description`, `weight_kg`, `length_cm`, `width_cm`, `height_cm`, `declared_value`, `fragile`, `hazardous`, `perishable`                                                                                              | delivery index, provider/category index                               | Yes               | Same as delivery                       |
| `delivery_route_estimates` | Snapshot of route/pricing basis | `id`, `transport_provider_id`, `delivery_request_id`, `distance_meters`, `duration_seconds`, `polyline`, `provider`, `raw_summary_json`, `calculated_at`                                                                                                                                             | delivery unique                                                       | Yes               | Same as delivery                       |
| `delivery_quotes`          | Quote records                   | `id`, `public_id`, `transport_provider_id`, `delivery_request_id`, `amount`, `currency`, `breakdown_json`, `status`, `expires_at`, `approved_by_user_id`                                                                                                                                             | provider/status index                                                 | Yes               | 7 years                                |
| `drivers`                  | Driver profile                  | `id`, `public_id`, `transport_provider_id`, `user_id nullable`, `name`, `phone`, `status`, `license_number encrypted nullable`                                                                                                                                                                       | provider/status index                                                 | Yes               | Soft delete                            |
| `vehicles`                 | Fleet records                   | `id`, `public_id`, `transport_provider_id`, `vehicle_type`, `plate_number`, `capacity_kg`, `status`, `service_area_json`                                                                                                                                                                             | unique provider/plate, provider/status                                | Yes               | Soft delete                            |
| `delivery_assignments`     | Driver/vehicle assignment       | `id`, `public_id`, `transport_provider_id`, `delivery_request_id`, `driver_id`, `vehicle_id`, `status`, `assigned_by_user_id`, `accepted_at`, `completed_at`                                                                                                                                         | delivery active unique, driver/status index                           | Yes               | Same as delivery                       |
| `driver_locations`         | Location samples                | `id`, `transport_provider_id`, `driver_id`, `delivery_request_id nullable`, `lat`, `lng`, `accuracy_m`, `recorded_at`                                                                                                                                                                                | driver/time index, delivery/time index                                | Yes               | 30–90 days unless tied to dispute      |
| `delivery_status_events`   | Status history                  | `id`, `public_id`, `transport_provider_id`, `delivery_request_id`, `from_status`, `to_status`, `actor_user_id`, `actor_type`, `notes`, `lat`, `lng`, `created_at`                                                                                                                                    | delivery/time index                                                   | Yes               | Same as delivery                       |
| `proofs_of_delivery`       | Delivery proof                  | `id`, `public_id`, `transport_provider_id`, `delivery_request_id`, `method`, `recipient_name`, `otp_hash nullable`, `photo_file_id nullable`, `signature_file_id nullable`, `lat`, `lng`, `captured_at`                                                                                              | delivery index                                                        | Yes               | Same as delivery                       |
| `uploaded_files`           | File metadata                   | `id`, `public_id`, `transport_provider_id`, `owner_type`, `owner_id`, `disk`, `path`, `mime_type`, `size_bytes`, `checksum`, `visibility`, `uploaded_by_user_id`, `scan_status`                                                                                                                      | owner index, provider index                                           | Yes               | Per file class                         |
| `payments`                 | Payment attempts                | `id`, `public_id`, `transport_provider_id`, `delivery_request_id`, `gateway`, `gateway_reference`, `amount`, `currency`, `status`, `paid_at`, `metadata_json`                                                                                                                                        | unique gateway/reference, provider/status                             | Yes               | 7 years                                |
| `invoices`                 | Invoice records                 | `id`, `public_id`, `transport_provider_id`, `delivery_request_id`, `invoice_number`, `amount`, `tax_amount`, `currency`, `status`, `issued_at`, `due_at`                                                                                                                                             | unique provider/invoice number                                        | Yes               | 7 years                                |
| `refunds`                  | Refunds                         | `id`, `public_id`, `transport_provider_id`, `payment_id`, `amount`, `reason`, `status`, `processed_at`                                                                                                                                                                                               | provider/status                                                       | Yes               | 7 years                                |
| `plans`                    | SaaS plans                      | `id`, `public_id`, `name`, `price`, `currency`, `limits_json`, `features_json`, `active`                                                                                                                                                                                                             | unique name                                                           | No                | Retain                                 |
| `provider_subscriptions`   | Tenant subscriptions            | `id`, `transport_provider_id`, `plan_id`, `status`, `starts_at`, `ends_at`, `trial_ends_at`, `gateway_reference`                                                                                                                                                                                     | provider/current index                                                | Yes               | 7 years                                |
| `pricing_rules`            | Provider pricing                | `id`, `public_id`, `transport_provider_id`, `rule_type`, `conditions_json`, `calculation_json`, `active`                                                                                                                                                                                             | provider/type index                                                   | Yes               | Soft delete                            |
| `service_zones`            | Delivery zones                  | `id`, `public_id`, `transport_provider_id`, `name`, `polygon_geojson`, `active`                                                                                                                                                                                                                      | provider/active index                                                 | Yes               | Soft delete                            |
| `notifications`            | In-app notification             | `id`, `public_id`, `transport_provider_id nullable`, `user_id nullable`, `recipient_type`, `channel`, `event_type`, `status`, `read_at`, `sent_at`                                                                                                                                                   | user/read index, provider/event index                                 | Nullable          | 12–24 months                           |
| `audit_logs`               | Immutable audit trail           | `id`, `public_id`, `transport_provider_id nullable`, `actor_user_id nullable`, `action`, `target_type`, `target_id`, `ip`, `user_agent`, `metadata_json`, `created_at`                                                                                                                               | provider/action/time, actor/time                                      | Nullable          | 7 years minimum                        |
| `support_tickets`          | Support/disputes                | `id`, `public_id`, `transport_provider_id`, `delivery_request_id nullable`, `opened_by_user_id`, `type`, `status`, `priority`                                                                                                                                                                        | provider/status index                                                 | Yes               | 3–7 years                              |
| `disputes`                 | Formal delivery dispute         | `id`, `public_id`, `transport_provider_id`, `delivery_request_id`, `reason`, `status`, `opened_at`, `resolved_at`                                                                                                                                                                                    | provider/status                                                       | Yes               | 7 years                                |
| `api_keys`                 | B2B API access                  | `id`, `public_id`, `transport_provider_id`, `name`, `key_hash`, `last_used_at`, `revoked_at`, `scopes_json`                                                                                                                                                                                          | provider index, key hash unique                                       | Yes               | Retain metadata                        |
| `webhook_endpoints`        | B2B webhook config              | `id`, `public_id`, `transport_provider_id`, `url`, `secret_encrypted`, `events_json`, `active`                                                                                                                                                                                                       | provider/active                                                       | Yes               | Soft delete                            |
| `webhook_deliveries`       | Webhook attempts                | `id`, `transport_provider_id`, `webhook_endpoint_id`, `event_type`, `payload_hash`, `status`, `attempts`, `next_retry_at`                                                                                                                                                                            | provider/status/retry                                                 | Yes               | 30–180 days                            |
| `account_settings`         | Tenant settings                 | `id`, `transport_provider_id`, `key`, `value_json`                                                                                                                                                                                                                                                   | unique provider/key                                                   | Yes               | Retain                                 |
| `personal_access_tokens`   | Sanctum tokens                  | Laravel default                                                                                                                                                                                                                                                                                      | token indexes                                                         | User-owned        | Per security policy                    |
| `failed_jobs`              | Failed queues                   | Laravel default                                                                                                                                                                                                                                                                                      | failed at index                                                       | System            | 30–90 days after resolution            |
| `job_batches`              | Batch jobs                      | Laravel default                                                                                                                                                                                                                                                                                      | id                                                                    | System            | 30–90 days                             |

### Migration considerations

1. Use `foreignId()->constrained()->cascadeOnDelete()` only where deletion is safe.
2. Use `restrictOnDelete()` for billing, payments, proofs, and audit logs.
3. Add `ulid('public_id')->unique()` to client-exposed models.
4. Add composite indexes like `(transport_provider_id, status, created_at)`.
5. Use `jsonb` for PostgreSQL structured metadata.
6. Use `decimal(12,2)` for money, never float.
7. Use encrypted casts for sensitive identifiers/secrets.
8. Use optimistic locking via `version` on `delivery_requests`.

---

## 8. Multi-Tenancy and Data Isolation Model

### Tenant resolution

Tenant is resolved in this order:

1. Authenticated user’s selected provider from header: `X-Provider-Id: <provider_public_id>`.
2. Session-stored active provider for SPA.
3. Route prefix for provider-specific public pages only where safe.
4. Super admin routes bypass normal tenant context only inside `/api/v1/admin/*`.

### Middleware

```php
// app/Http/Middleware/ResolveTenant.php
public function handle($request, Closure $next)
{
    $providerPublicId = $request->header('X-Provider-Id')
        ?? $request->session()->get('active_provider_public_id');

    abort_unless($providerPublicId, 400, 'Provider context required.');

    $provider = TransportProvider::where('public_id', $providerPublicId)->firstOrFail();

    abort_unless(
        $request->user()->belongsToProvider($provider) || $request->user()->isSuperAdminInAdminRoute($request),
        403
    );

    app(CurrentTenant::class)->set($provider);

    return $next($request);
}
```

### Tenant context rules

| Context        | Rule                                                                     |
| -------------- | ------------------------------------------------------------------------ |
| HTTP request   | `CurrentTenant` service stores provider                                  |
| Eloquent query | Global tenant scopes for tenant-owned models where safe                  |
| Policy         | Always checks ownership + permission                                     |
| Job            | Constructor receives `transport_provider_id`; `handle()` restores tenant |
| Export         | Export query must start with `where('transport_provider_id', $tenantId)` |
| Notification   | Notification payload includes `transport_provider_id`                    |
| Webhook        | Endpoint selected only by provider scope                                 |
| Super admin    | Separate route group and separate policies                               |

### Tenant-owned model trait

```php
trait BelongsToTransportProvider
{
    protected static function bootBelongsToTransportProvider(): void
    {
        static::creating(function ($model) {
            if (!$model->transport_provider_id && app(CurrentTenant::class)->has()) {
                $model->transport_provider_id = app(CurrentTenant::class)->id();
            }
        });
    }

    public function scopeForTenant($query, TransportProvider|int $provider)
    {
        $id = $provider instanceof TransportProvider ? $provider->id : $provider;
        return $query->where($this->getTable().'.transport_provider_id', $id);
    }
}
```

### Denied cases to test

| Case                                                       | Expected result                                   |
| ---------------------------------------------------------- | ------------------------------------------------- |
| Provider A user requests Provider B delivery public ID     | `404` or `403`; prefer `404` to avoid enumeration |
| Provider member without `deliveries.assign` assigns driver | `403`                                             |
| Background job runs without tenant context                 | Job fails safely and records structured error     |
| Export endpoint omits tenant scope                         | Test fails; endpoint rejected before merge        |
| Valid public ID belongs to another provider                | `404`                                             |
| Super admin accesses tenant route without admin mode       | `403`                                             |
| Suspended provider user logs in                            | Auth succeeds but provider access blocked         |

---

## 9. Authentication Model

### Auth features

| Feature                  | Implementation                          |
| ------------------------ | --------------------------------------- |
| Login                    | Laravel Sanctum SPA auth                |
| Password hashing         | Laravel default hasher                  |
| Email verification       | Laravel email verification              |
| Password reset           | Laravel password broker                 |
| MFA                      | Post-MVP but schema-ready               |
| Session timeout          | Configurable idle/absolute timeout      |
| CSRF                     | Sanctum cookie-based SPA CSRF           |
| Rate limiting            | Laravel rate limiters                   |
| Invitation acceptance    | Signed token hash + expiry + rate limit |
| Suspicious login logging | `security_events` or `audit_logs`       |

### Auth route examples

```text
POST /api/v1/auth/register
POST /api/v1/auth/login
POST /api/v1/auth/logout
GET  /api/v1/auth/me
POST /api/v1/auth/email/verification-notification
POST /api/v1/auth/forgot-password
POST /api/v1/auth/reset-password
POST /api/v1/invitations/{token}/accept
```

### Rate limits

| Endpoint             | Limit                       |
| -------------------- | --------------------------- |
| Login                | 5/minute per email + IP     |
| Password reset       | 3/hour per email + IP       |
| Registration         | 5/hour per IP               |
| Invitation accept    | 10/hour per token/IP        |
| Public tracking      | 60/minute per token/IP      |
| OTP verification     | 5 attempts per delivery     |
| Maps proxy endpoints | 60/minute per user/provider |
| File upload          | Size + count + rate limit   |

---

## 10. Authorization, Roles, and Permissions Model

### Role hierarchy

| Role           | Scope    | Description                         |
| -------------- | -------- | ----------------------------------- |
| Super Admin    | Platform | SaaS owner/operator                 |
| Provider Owner | Tenant   | Full provider control               |
| Admin          | Tenant   | Manage most provider operations     |
| Dispatcher     | Tenant   | Assign and monitor deliveries       |
| Driver/Rider   | Tenant   | Execute assigned jobs               |
| Finance User   | Tenant   | Quotes, invoices, payments, refunds |
| Support User   | Tenant   | Customer support, disputes          |
| Viewer         | Tenant   | Read-only                           |

The generic minimum roles required by the technical brief are Owner, Admin, Manager, Member, and Viewer.  For Courier, map them as:

| Generic role | Courier role                                   |
| ------------ | ---------------------------------------------- |
| Owner        | Provider Owner                                 |
| Admin        | Provider Admin                                 |
| Manager      | Dispatcher / Finance Manager / Support Manager |
| Member       | Driver / Support User                          |
| Viewer       | Viewer                                         |

### Permission matrix

| Permission          | Owner | Admin | Dispatcher |               Driver | Finance | Support | Viewer |
| ------------------- | ----: | ----: | ---------: | -------------------: | ------: | ------: | -----: |
| `providers.manage`  |     ✅ |     ✅ |          ❌ |                    ❌ |       ❌ |       ❌ |      ❌ |
| `team.invite`       |     ✅ |     ✅ |          ❌ |                    ❌ |       ❌ |       ❌ |      ❌ |
| `team.remove`       |     ✅ |     ✅ |          ❌ |                    ❌ |       ❌ |       ❌ |      ❌ |
| `deliveries.view`   |     ✅ |     ✅ |          ✅ |        Assigned only |       ✅ |       ✅ |      ✅ |
| `deliveries.create` |     ✅ |     ✅ |          ✅ |                    ❌ |       ❌ |       ✅ |      ❌ |
| `deliveries.update` |     ✅ |     ✅ |          ✅ | Assigned status only |       ❌ |       ✅ |      ❌ |
| `deliveries.cancel` |     ✅ |     ✅ |          ✅ |                    ❌ |       ❌ |       ✅ |      ❌ |
| `dispatch.assign`   |     ✅ |     ✅ |          ✅ |                    ❌ |       ❌ |       ❌ |      ❌ |
| `drivers.manage`    |     ✅ |     ✅ |          ✅ |                    ❌ |       ❌ |       ❌ |      ❌ |
| `vehicles.manage`   |     ✅ |     ✅ |          ✅ |                    ❌ |       ❌ |       ❌ |      ❌ |
| `proof.create`      |     ✅ |     ✅ |          ✅ |        Assigned only |       ❌ |       ❌ |      ❌ |
| `quotes.manage`     |     ✅ |     ✅ |          ✅ |                    ❌ |       ✅ |       ❌ |      ❌ |
| `payments.manage`   |     ✅ |     ✅ |          ❌ |                    ❌ |       ✅ |       ❌ |      ❌ |
| `invoices.view`     |     ✅ |     ✅ |          ❌ |                    ❌ |       ✅ |       ✅ |      ✅ |
| `reports.view`      |     ✅ |     ✅ |          ✅ |                    ❌ |       ✅ |       ✅ |      ✅ |
| `audit.view`        |     ✅ |     ✅ |          ❌ |                    ❌ |       ❌ |       ❌ |      ❌ |
| `settings.manage`   |     ✅ |     ✅ |          ❌ |                    ❌ |       ❌ |       ❌ |      ❌ |

### Policy example

```php
public function view(User $user, DeliveryRequest $delivery): bool
{
    $tenant = app(CurrentTenant::class)->get();

    return $delivery->transport_provider_id === $tenant->id
        && $user->hasProviderPermission($tenant, 'deliveries.view');
}

public function assign(User $user, DeliveryRequest $delivery): bool
{
    $tenant = app(CurrentTenant::class)->get();

    return $delivery->transport_provider_id === $tenant->id
        && $user->hasProviderPermission($tenant, 'dispatch.assign')
        && in_array($delivery->status, [
            DeliveryStatus::CONFIRMED,
            DeliveryStatus::ASSIGNED,
        ], true);
}
```

### Ownership transfer

1. Only current Provider Owner can initiate.
2. Target user must be active member.
3. Require password confirmation and optional MFA.
4. Create audit log before and after transfer.
5. Transfer in DB transaction.
6. Ensure at least one owner remains.

---

## 11. API Design

### API standards

| Standard         | Rule                         |
| ---------------- | ---------------------------- |
| Versioning       | `/api/v1/...`                |
| Auth             | Sanctum middleware           |
| Tenant           | `tenant.resolved` middleware |
| Input            | Form Requests                |
| Output           | API Resources                |
| Errors           | Consistent JSON              |
| IDs              | Public ULIDs                 |
| Collections      | Paginated                    |
| Filtering        | Whitelisted filters          |
| Sorting          | Whitelisted fields           |
| Sensitive errors | Never exposed                |

### JSON envelope

```json
{
  "data": {},
  "meta": {
    "request_id": "req_01HZ...",
    "timestamp": "2026-06-08T12:00:00+03:00"
  }
}
```

Validation error:

```json
{
  "message": "The given data was invalid.",
  "errors": {
    "pickup_address_id": ["Pickup address is required."]
  },
  "meta": {
    "request_id": "req_01HZ..."
  }
}
```

### Route groups

```php
Route::prefix('v1')->group(function () {
    Route::prefix('auth')->group(...);

    Route::middleware(['auth:sanctum'])->group(function () {
        Route::get('/me', MeController::class);
        Route::get('/providers', ProviderSelectionController::class);

        Route::middleware(['tenant.resolved', 'provider.active'])->group(function () {
            Route::apiResource('deliveries', DeliveryController::class);
            Route::post('deliveries/{delivery}/quote', CreateQuoteController::class);
            Route::post('deliveries/{delivery}/assign', AssignDeliveryController::class);
            Route::post('deliveries/{delivery}/status', TransitionDeliveryStatusController::class);
            Route::post('deliveries/{delivery}/proof', RecordProofController::class);

            Route::apiResource('drivers', DriverController::class);
            Route::apiResource('vehicles', VehicleController::class);
            Route::apiResource('senders', SenderController::class);
            Route::apiResource('recipients', RecipientController::class);
            Route::apiResource('invoices', InvoiceController::class)->only(['index','show']);
            Route::apiResource('team/invitations', InvitationController::class);
            Route::get('reports/summary', ProviderReportController::class);
        });

        Route::prefix('admin')
            ->middleware(['super.admin'])
            ->group(...);
    });

    Route::get('/track/{trackingToken}', PublicTrackingController::class);
    Route::post('/track/{trackingToken}/confirm', PublicDeliveryConfirmationController::class);
});
```

### API route examples

| Method | Route                                   |   Auth |       Tenant | Permission                        |
| ------ | --------------------------------------- | -----: | -----------: | --------------------------------- |
| `GET`  | `/api/v1/deliveries`                    |      ✅ |            ✅ | `deliveries.view`                 |
| `POST` | `/api/v1/deliveries`                    |      ✅ |            ✅ | `deliveries.create`               |
| `GET`  | `/api/v1/deliveries/{public_id}`        |      ✅ |            ✅ | `deliveries.view` + ownership     |
| `POST` | `/api/v1/deliveries/{public_id}/assign` |      ✅ |            ✅ | `dispatch.assign`                 |
| `POST` | `/api/v1/deliveries/{public_id}/proof`  |      ✅ |            ✅ | driver assigned or `proof.create` |
| `GET`  | `/api/v1/track/{token}`                 | Public | Token-scoped | Signed/hashed token               |
| `GET`  | `/api/v1/admin/providers`               |      ✅ |   Admin only | `platform.providers.view`         |

---

## 12. UI/UX Design System

The brand identity defines Courier by Citrus as warm, clear, trustworthy, professional, modern, calm, capable, and inviting.  The UI must therefore be operationally dense where needed, but not cold or cluttered.

### Design tokens

| Token               | Value                    |
| ------------------- | ------------------------ |
| Primary dark        | `#4A0637` Courier Plum   |
| Deep accent         | `#2B0425` Mulberry Night |
| CTA                 | `#FF5A45` Handoff Coral  |
| Confirmation accent | `#FFB84D` Apricot Proof  |
| Background          | `#FFF4E8` Citrus Cream   |
| Surface             | `#FFFFFF` Cloud White    |
| Body text           | `#24111F` Ink Plum       |
| Muted text          | `#7A6A73` Warm Slate     |
| Border              | `#EADDD7` Soft Clay      |
| Info                | `#3B82F6` Route Blue     |
| Success             | `#22A06B` Verified Green |
| Warning             | `#F59E0B` Amber Notice   |
| Error               | `#D92D20` Delivery Red   |

### Component standards

| Component      | Standard                                                   |
| -------------- | ---------------------------------------------------------- |
| Buttons        | 10–12px radius, 44px minimum height on touch surfaces      |
| Cards          | 14–18px radius, subtle border, minimal shadow              |
| Forms          | Always visible labels, helper text, validation text        |
| Status chips   | Icon + text + color; never color only                      |
| Tables         | Sticky header on desktop, card-list transform on mobile    |
| Modals         | Focus trap, ESC close where non-destructive, labeled title |
| Toasts         | Announce via ARIA live region                              |
| Sidebar        | Collapsible at tablet, drawer on mobile                    |
| Profile menu   | Avatar/name/provider context as cohesive identity unit     |
| Empty states   | Warm copy + one primary action                             |
| Loading states | Skeleton for cards/tables; spinner only for short actions  |

### Product language

Use “delivery,” “package,” “driver,” “recipient,” and “proof.” Avoid “consignment,” “recipient entity,” and “POD event” in sender/recipient UI.

---

## 13. Responsive Layout Strategy

Required breakpoints:

| Mode    | Width            |
| ------- | ---------------- |
| Desktop | `>= 1025px`      |
| Tablet  | `768px – 1024px` |
| Mobile  | `<= 767px`       |

### CSS strategy

```css
:root {
  --sidebar-width: 280px;
}

@media (min-width: 1025px) {
  .app-shell {
    display: grid;
    grid-template-columns: var(--sidebar-width) minmax(0, 1fr);
  }
}

@media (min-width: 768px) and (max-width: 1024px) {
  .app-shell {
    display: grid;
    grid-template-columns: 88px minmax(0, 1fr);
  }
}

@media (max-width: 767px) {
  .app-shell {
    display: block;
  }

  .desktop-table {
    display: none;
  }

  .mobile-card-list {
    display: grid;
    gap: 0.75rem;
  }
}
```

### Layout behavior

| Area           | Desktop                       | Tablet                          | Mobile                      |
| -------------- | ----------------------------- | ------------------------------- | --------------------------- |
| Dashboard      | KPI cards + table + map split | Collapsed sidebar + stacked map | Single-column cards         |
| Sidebar        | Persistent full               | Icon rail                       | Drawer                      |
| Header         | Search + actions + profile    | Compact actions                 | Sticky mobile top bar       |
| Data tables    | Full table                    | Reduced columns                 | Card list                   |
| Forms          | Two-column where safe         | One/two-column hybrid           | Single-column               |
| Settings       | Sidebar tabs                  | Top tabs                        | Accordion/stacked sections  |
| Profile menu   | Dropdown anchored right       | Dropdown                        | Bottom sheet                |
| Modals         | Centered                      | Centered                        | Full-screen or bottom sheet |
| Dispatch board | Multi-column + map            | Stacked board/map               | List-first, map collapsible |
| Driver portal  | Not primary                   | Usable                          | Primary large-button flow   |

---

## 14. Dark Mode Strategy

### Token strategy

Use CSS variables and Tailwind theme extension.

```css
:root {
  --color-bg: #FFF4E8;
  --color-surface: #FFFFFF;
  --color-text: #24111F;
  --color-muted: #7A6A73;
  --color-border: #EADDD7;
}

[data-theme="dark"] {
  --color-bg: #160112;
  --color-surface: #2B0425;
  --color-text: #FFF4E8;
  --color-muted: #D7C8CF;
  --color-border: #4A0637;
}
```

### Persistence

| User state            | Storage                                             |
| --------------------- | --------------------------------------------------- |
| Guest/public tracking | `localStorage`                                      |
| Authenticated user    | `users.theme_preference`                            |
| Initial page load     | Inline script sets `data-theme` before React mounts |

### Test requirements

1. Light mode screenshots for core pages.
2. Dark mode screenshots for core pages.
3. WCAG contrast checks.
4. Keyboard focus visible in both themes.
5. Validation errors visible in both themes.
6. Status chips readable without relying only on color.

---

## 15. Accessibility Strategy

Minimum standard: practical WCAG AA alignment.

### Requirements

| Area          | Requirement                                          |
| ------------- | ---------------------------------------------------- |
| Keyboard      | Full navigation without mouse                        |
| Focus         | Visible focus rings on all interactive controls      |
| Forms         | Labels required; placeholders cannot replace labels  |
| Errors        | Use `aria-describedby` and field-level error text    |
| Buttons       | Accessible names                                     |
| Touch targets | Minimum 44x44 points                                 |
| Zoom          | Browser zoom supported; no viewport scaling disabled |
| Motion        | Respect `prefers-reduced-motion`                     |
| Modals        | Focus trap, return focus to trigger                  |
| Menus         | Proper ARIA roles where needed                       |
| Maps          | Provide textual route/status equivalents             |
| Status        | Icon + text + color, never color alone               |

### Verification

```bash
npm run test:components
npm run test:e2e:a11y
```

Manual checks:

1. Tab through login, delivery creation, dispatch, driver proof, tracking.
2. Use screen reader on public tracking page.
3. Zoom to 200%.
4. Test reduced motion.
5. Test mobile tap targets.

---

## 16. Forms and Input Behavior Strategy

### Form standards

| State             | Behavior                                  |
| ----------------- | ----------------------------------------- |
| Empty             | Label visible, helper text optional       |
| Focused           | Clear focus ring and border               |
| Populated         | Label remains visible                     |
| Disabled          | Reduced emphasis but readable             |
| Error             | Field border + message + ARIA association |
| Success           | Confirmation where useful                 |
| Submitting        | Disable submit button, show pending state |
| Server validation | Map backend `errors` object to fields     |
| Sensitive         | Mask values; never echo secrets           |

### Delivery creation form sections

1. Pickup details.
2. Recipient details.
3. Goods details.
4. Schedule/time window.
5. Photos/documents.
6. Quote review.
7. Confirmation.

### Duplicate submit prevention

1. Frontend disables submit after click.
2. Backend supports idempotency key for delivery creation/payment.
3. Button re-enabled only after success/error.
4. API rejects duplicate idempotency key replay safely.

---

## 17. User Profile and Account UI Strategy

### Required profile identity unit

Display as one cohesive control:

```text
[Avatar] Jane Doe
         Courier Console · ABC Logistics
```

### Components

```text
components/layout/ProfileMenu.tsx
components/layout/ProviderSwitcher.tsx
components/account/ProfilePreviewCard.tsx
components/account/AccountSettingsNav.tsx
```

### Behavior

1. Click opens menu.
2. Keyboard Enter/Space opens menu.
3. ESC closes menu.
4. Focus returns to trigger.
5. Menu does not clip under header.
6. Account switcher validates membership from API.
7. Changing account updates `X-Provider-Id` context and reloads scoped data.
8. Switching to suspended provider shows blocked state.

---

## 18. Billing and Plan Enforcement Strategy

### Billing levels

| Billing type                   | MVP status                       |
| ------------------------------ | -------------------------------- |
| Provider SaaS subscription     | MVP schema + manual/admin status |
| Sender delivery payment        | MVP gateway abstraction          |
| Invoice/receipt generation     | MVP                              |
| Refunds                        | MVP basic                        |
| Promo codes                    | Post-MVP                         |
| Automated subscription billing | Post-MVP                         |

### Plan enforcement

Create `plans.features_json` and `plans.limits_json`.

Example:

```json
{
  "features": {
    "driver_portal": true,
    "public_tracking": true,
    "route_optimization": false,
    "webhooks": false
  },
  "limits": {
    "monthly_deliveries": 1000,
    "active_drivers": 25,
    "storage_gb": 20
  }
}
```

### Enforcement locations

1. Backend middleware: `EnsureFeatureEnabled`.
2. Service/action checks before expensive operations.
3. Frontend hides unavailable features but server remains source of truth.
4. Audit when provider hits plan limit.
5. Return `402 Payment Required` or `403 Feature Disabled` with structured response.

---

## 19. File Upload and Storage Strategy

### Accepted file classes

| Class              | Types                            | Max size |
| ------------------ | -------------------------------- | -------: |
| Goods photo        | jpg, png, webp                   |     8 MB |
| Proof photo        | jpg, png, webp                   |     8 MB |
| Signature image    | png, svg sanitized or rasterized |     2 MB |
| Provider documents | pdf, jpg, png                    |    15 MB |
| Vehicle documents  | pdf, jpg, png                    |    15 MB |
| Invoice/receipt    | pdf                              |    10 MB |
| Dispute attachment | pdf, jpg, png, webp              |    15 MB |

### Security controls

1. Validate extension and MIME.
2. Store in private S3 bucket.
3. Generate path: `providers/{provider_public_id}/{class}/{ulid}.{ext}`.
4. Never trust original filename.
5. Calculate checksum.
6. Queue malware scan.
7. Mark `scan_status=pending|clean|infected|failed`.
8. Block download unless clean or explicitly permitted by admin.
9. Authorize upload and download.
10. Use signed temporary URLs with short TTL.
11. Audit sensitive file access.
12. Cleanup orphaned uploads daily.

---

## 20. Queue, Jobs, Notifications, and Scheduled Task Strategy

### Queued jobs

| Job                                  | Trigger                     | Queue           |
| ------------------------------------ | --------------------------- | --------------- |
| `SendEmailVerificationJob`           | Register                    | `mail`          |
| `SendDeliveryCreatedNotificationJob` | Delivery created            | `notifications` |
| `CalculateRouteEstimateJob`          | Address selected            | `maps`          |
| `GenerateInvoicePdfJob`              | Payment/delivery complete   | `documents`     |
| `SendOtpToRecipientJob`              | Delivery arriving/delivered | `notifications` |
| `ScanUploadedFileJob`                | File uploaded               | `security`      |
| `ExportDeliveriesJob`                | Report export requested     | `exports`       |
| `DispatchWebhookJob`                 | Status change               | `webhooks`      |
| `RetryWebhookDeliveryJob`            | Failed webhook              | `webhooks`      |
| `PruneDriverLocationsJob`            | Schedule                    | `maintenance`   |

### Tenant-aware job rule

Every tenant job constructor must include:

```php
public function __construct(
    public int $transportProviderId,
    public string $targetPublicId
) {}
```

In `handle()`:

```php
$provider = TransportProvider::findOrFail($this->transportProviderId);
app(CurrentTenant::class)->set($provider);
```

### Scheduled tasks

```php
Schedule::command('sanctum:prune-expired --hours=24')->daily();
Schedule::job(new PruneExpiredInvitationsJob)->hourly();
Schedule::job(new RetryFailedWebhooksJob)->everyFiveMinutes();
Schedule::job(new PruneOldDriverLocationsJob)->daily();
Schedule::job(new GenerateDailyProviderMetricsJob)->dailyAt('01:00');
Schedule::command('queue:prune-failed --hours=720')->daily();
```

---

## 21. Search Strategy

### MVP

Use PostgreSQL indexed search:

1. Delivery reference.
2. Sender name/phone/email.
3. Recipient name/phone/email.
4. Driver name.
5. Vehicle plate.
6. Status.
7. Date range.

### Post-MVP

Use Laravel Scout + Meilisearch or Typesense.

Laravel Scout’s documentation recommends queueing non-database search index sync operations to improve web response times. ([Laravel][5])

### Tenant-safe search indexing

Every indexed document must include:

```json
{
  "transport_provider_id": 123,
  "public_id": "01HZ...",
  "type": "delivery_request"
}
```

Search queries must include provider filter:

```text
filter: transport_provider_id = currentTenant.id
```

---

## 22. Observability and Audit Logging Strategy

### Logs

Use structured JSON logs:

```json
{
  "level": "warning",
  "message": "Cross-tenant access denied",
  "request_id": "req_01HZ...",
  "actor_user_id": 17,
  "transport_provider_id": 4,
  "target_type": "delivery_request",
  "target_public_id": "01HZ...",
  "ip": "197.x.x.x"
}
```

### Audit events

| Event                                  | Audit |
| -------------------------------------- | ----- |
| Provider approved/suspended            | ✅     |
| User invited/removed                   | ✅     |
| Role/permission changed                | ✅     |
| Delivery created/updated/cancelled     | ✅     |
| Driver assigned/reassigned             | ✅     |
| Status changed                         | ✅     |
| Proof recorded                         | ✅     |
| File downloaded if sensitive           | ✅     |
| Quote overridden                       | ✅     |
| Payment/refund changed                 | ✅     |
| Invoice generated                      | ✅     |
| Webhook/API key created/revoked        | ✅     |
| Failed login threshold reached         | ✅     |
| Super admin impersonation/admin access | ✅     |

### Monitoring

| Area                | Tooling                                  |
| ------------------- | ---------------------------------------- |
| App errors          | Sentry/Bugsnag/Rollbar                   |
| Laravel performance | Pulse/Nightwatch/New Relic/OpenTelemetry |
| Queue               | Horizon                                  |
| Uptime              | Health route + external monitor          |
| Logs                | Centralized log aggregation              |
| DB                  | Slow query logs, index analysis          |
| Maps cost           | Google billing alerts + quotas           |
| Storage             | Bucket metrics and lifecycle policies    |

---

## 23. Performance and Scalability Plan

### Likely bottlenecks

| Bottleneck               |              Likelihood | Mitigation                                           |
| ------------------------ | ----------------------: | ---------------------------------------------------- |
| Google Maps request cost |                     65% | Debounce autocomplete, cache route snapshots, quotas |
| Delivery list queries    |        70% after growth | Composite indexes, pagination, filters               |
| Driver location writes   | 60% after live tracking | Throttle writes, retention, batch insert             |
| Report generation        |        75% after growth | Queue exports, materialized daily metrics            |
| File storage growth      |                     55% | Compression, lifecycle rules                         |
| Notifications            |                     65% | Queue, retry, provider failover                      |
| Dispatch board polling   |                     50% | Polling first, WebSockets later                      |
| Tenant isolation defects |                  35–70% | Policies, scopes, tests                              |

### Concrete rules

1. Paginate every list with max page size 100.
2. Use cursor pagination for high-volume status/location feeds.
3. Add indexes before feature is considered complete.
4. Cache expensive provider settings for 5–15 minutes.
5. Invalidate cache on settings update.
6. Lazy-load maps only when visible.
7. Use image compression for uploaded photos.
8. Generate reports asynchronously.
9. Snapshot route estimates and quotes.
10. Avoid recalculating old pricing.

---

## 24. Security Threat Model

| Threat                | Attack                                   | Impact    | Mitigation                                         | Test                   |
| --------------------- | ---------------------------------------- | --------- | -------------------------------------------------- | ---------------------- |
| Broken access control | Provider A accesses Provider B public ID | Critical  | Tenant middleware + policies + scoped bindings     | Cross-tenant API tests |
| IDOR                  | Sequential ID guessing                   | Critical  | ULIDs only in API                                  | API route tests        |
| SQL injection         | Filter/search injection                  | High      | Eloquent binding + whitelist filters               | Validation tests       |
| XSS                   | Sender notes contain script              | High      | Escape output, sanitize rich text                  | Component tests        |
| CSRF                  | Browser mutating request                 | High      | Sanctum CSRF                                       | Feature tests          |
| File upload abuse     | PHP/script upload                        | High      | MIME/ext validation, private storage, malware scan | Upload security tests  |
| Maps key abuse        | Browser/server key stolen                | High cost | Key restrictions, quotas, separate keys            | Config review          |
| Mass assignment       | User sets provider ID manually           | Critical  | Guarded/fillable, server tenant assignment         | Feature tests          |
| Session fixation      | Session reused after login               | High      | Regenerate session                                 | Auth test              |
| Brute force           | Login/OTP attacks                        | High      | Rate limits, lockouts                              | Rate limit tests       |
| Unsafe redirect       | Redirect to attacker URL                 | Medium    | Allowlist redirects                                | Validation tests       |
| Sensitive logs        | Token/payment logged                     | High      | Log scrubber                                       | Log tests              |
| Webhook spoofing      | Fake payment/webhook event               | High      | Signature verification                             | Webhook tests          |

---

## 25. Testing Strategy

### Test layers

| Layer                  | Tool                                          |
| ---------------------- | --------------------------------------------- |
| Backend unit           | PHPUnit/Pest                                  |
| Feature/API            | Laravel HTTP tests                            |
| Authorization/security | PHPUnit/Pest                                  |
| Frontend component     | Vitest + React Testing Library                |
| E2E/browser            | Playwright                                    |
| Accessibility          | axe integration                               |
| Static analysis        | PHPStan/Larastan, ESLint, TypeScript          |
| Security scan          | Composer audit, npm audit, dependency scanner |

### Required test files

```text
tests/Feature/Auth/LoginTest.php
tests/Feature/Auth/EmailVerificationTest.php
tests/Feature/Auth/PasswordResetTest.php
tests/Feature/Tenancy/TenantResolutionTest.php
tests/Feature/Tenancy/CrossTenantAccessDeniedTest.php
tests/Feature/Authorization/PermissionMatrixTest.php
tests/Feature/Deliveries/CreateDeliveryRequestTest.php
tests/Feature/Deliveries/DeliveryStatusTransitionTest.php
tests/Feature/Dispatch/AssignDeliveryTest.php
tests/Feature/Driver/DriverPortalTest.php
tests/Feature/Proof/RecordProofOfDeliveryTest.php
tests/Feature/Billing/CreateQuoteTest.php
tests/Feature/Billing/PaymentWebhookTest.php
tests/Feature/Files/FileUploadSecurityTest.php
tests/Feature/Reports/ExportTenantScopeTest.php
tests/Feature/Admin/SuperAdminProviderManagementTest.php
tests/Unit/Pricing/DistancePricingCalculatorTest.php
tests/Unit/DeliveryStatus/AllowedTransitionTest.php
tests/Security/NoSequentialIdsExposedTest.php
tests/Security/LogScrubbingTest.php
```

Frontend:

```text
resources/js/test/components/Button.test.tsx
resources/js/test/components/FormField.test.tsx
resources/js/test/features/deliveries/CreateDeliveryForm.test.tsx
resources/js/test/features/dispatch/DispatchBoard.test.tsx
resources/js/test/features/driver/DriverJobFlow.test.tsx
resources/js/test/features/tracking/PublicTrackingPage.test.tsx
resources/js/test/a11y/accessibility.test.tsx
```

E2E:

```text
tests/Browser/sender-create-delivery.spec.ts
tests/Browser/provider-assign-driver.spec.ts
tests/Browser/driver-complete-proof.spec.ts
tests/Browser/recipient-track-confirm.spec.ts
tests/Browser/cross-tenant-denial.spec.ts
```

### Commands

```bash
composer test
php artisan test
vendor/bin/phpstan analyse
npm run typecheck
npm run lint
npm run test
npm run test:e2e
composer audit
npm audit
```

---

## 26. Deployment and CI/CD Strategy

### Docker services

```text
app          PHP-FPM Laravel
nginx        Web server
postgres     Database
redis        Cache/queue
queue        Laravel queue worker
scheduler    Laravel scheduler
horizon      Queue dashboard
meilisearch  Post-MVP search
```

### Production environment

Required env variables:

```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://courier.citruslabs.example

SESSION_SECURE_COOKIE=true
SESSION_SAME_SITE=lax
SANCTUM_STATEFUL_DOMAINS=app.example.com

DB_CONNECTION=pgsql
CACHE_STORE=redis
QUEUE_CONNECTION=redis

FILESYSTEM_DISK=s3
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=
AWS_BUCKET=
AWS_ENDPOINT=

GOOGLE_MAPS_BROWSER_KEY=
GOOGLE_MAPS_SERVER_KEY=

MAIL_MAILER=
SMS_PROVIDER=
PAYMENT_PROVIDER=
```

### CI pipeline

```yaml
name: CI

on:
  pull_request:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16
      redis:
        image: redis:7
    steps:
      - uses: actions/checkout@v4
      - run: composer install --no-interaction --prefer-dist
      - run: npm ci
      - run: cp .env.ci .env
      - run: php artisan key:generate
      - run: php artisan migrate --force
      - run: php artisan test
      - run: vendor/bin/phpstan analyse
      - run: npm run typecheck
      - run: npm run lint
      - run: npm run test
      - run: composer audit
      - run: npm audit --audit-level=high
```

### Deployment sequence

1. Build immutable Docker image.
2. Run tests in CI.
3. Push image to registry.
4. Backup DB before migrations.
5. Put app into maintenance mode only when migration is not online-safe.
6. Run `php artisan migrate --force`.
7. Clear and warm caches:

```bash
php artisan config:cache
php artisan route:cache
php artisan view:cache
php artisan event:cache
```

8. Restart queue workers:

```bash
php artisan queue:restart
```

9. Verify health route.
10. Verify login, tenant switch, delivery list, queue processing.
11. Monitor errors for at least one deployment window.

---

## 27. Step-by-Step Development Roadmap

Each phase must follow: inspect existing files, prove gap, implement smallest correct change, add tests, run tests, demonstrate proof.

### Phase 1 — Project initialization

| Item       | Details                                                       |
| ---------- | ------------------------------------------------------------- |
| Objective  | Create Laravel + React + TS baseline                          |
| Files      | `composer.json`, `package.json`, `resources/js/*`, `routes/*` |
| Backend    | Install Laravel, Sanctum, base API route                      |
| Frontend   | Install React TS, Vite, Tailwind                              |
| DB         | Configure PostgreSQL                                          |
| Security   | `.env.example` only; no secrets                               |
| Tests      | Smoke tests for `/up`, `/api/v1/health`                       |
| Commands   | `php artisan test`, `npm run typecheck`                       |
| Acceptance | App boots; health endpoint works                              |

### Phase 2 — Docker and environment setup

| Item       | Details                                                 |
| ---------- | ------------------------------------------------------- |
| Objective  | Local/prod parity                                       |
| Files      | `Dockerfile`, `docker-compose.yml`, `docker/nginx.conf` |
| Backend    | PHP extensions: pgsql, redis, intl, gd                  |
| Frontend   | Node build stage                                        |
| DB         | Postgres service                                        |
| Security   | Non-root containers                                     |
| Tests      | CI container boot                                       |
| Acceptance | `docker compose up` boots app                           |

### Phase 3 — Laravel backend setup

| Item       | Details                                         |
| ---------- | ----------------------------------------------- |
| Objective  | API foundation                                  |
| Files      | `app/Http/Controllers/Api/V1`, `routes/api.php` |
| Backend    | JSON responses, exception handler, request IDs  |
| Tests      | API error shape tests                           |
| Acceptance | Consistent API envelope                         |

### Phase 4 — Frontend setup

| Item       | Details                                              |
| ---------- | ---------------------------------------------------- |
| Objective  | React SPA shell                                      |
| Files      | `resources/js/App.tsx`, `api/client.ts`, `layouts/*` |
| Frontend   | Router, layouts, API client                          |
| Tests      | Component render tests                               |
| Acceptance | Login shell renders; API client handles errors       |

### Phase 5 — Authentication

| Item       | Details                                               |
| ---------- | ----------------------------------------------------- |
| Objective  | Secure auth                                           |
| Backend    | Sanctum, login, logout, me, email verification, reset |
| Frontend   | Login/register/reset pages                            |
| Security   | Rate limits, session regeneration                     |
| Tests      | Login success/failure/rate-limit                      |
| Acceptance | Auth works; bad attempts throttled                    |

### Phase 6 — Provider tenant model

| Item       | Details                                                    |
| ---------- | ---------------------------------------------------------- |
| Objective  | Provider tenancy                                           |
| Backend    | `transport_providers`, `provider_users`, tenant middleware |
| Frontend   | Provider selector                                          |
| Tests      | Tenant resolution, inactive provider block                 |
| Acceptance | User can select provider; provider context required        |

### Phase 7 — Memberships and invitations

| Item       | Details                               |
| ---------- | ------------------------------------- |
| Objective  | Team onboarding                       |
| Backend    | Invitations, accept flow, expiration  |
| Frontend   | Team settings UI                      |
| Security   | Token hash, expiry, audit             |
| Tests      | Accept, expired, revoked, wrong email |
| Acceptance | Invite flow works and is audited      |

### Phase 8 — Roles and permissions

| Item       | Details                         |
| ---------- | ------------------------------- |
| Objective  | Permission enforcement          |
| Backend    | Roles, permissions, policies    |
| Frontend   | Permission-aware nav            |
| Tests      | Permission matrix               |
| Acceptance | Unauthorized action returns 403 |

### Phase 9 — Tenant-scoped data access

| Item       | Details                                  |
| ---------- | ---------------------------------------- |
| Objective  | Hard tenant isolation                    |
| Backend    | Tenant trait, scoped route binding       |
| Tests      | Cross-tenant denial on every resource    |
| Acceptance | Provider A cannot access Provider B data |

### Phase 10 — API foundation

| Item       | Details                                 |
| ---------- | --------------------------------------- |
| Objective  | Versioned REST                          |
| Backend    | Resources, pagination, filters, sorting |
| Tests      | Pagination, invalid filters             |
| Acceptance | Every collection paginated              |

### Phase 11 — UI layout foundation

| Item       | Details                                   |
| ---------- | ----------------------------------------- |
| Objective  | SaaS shell                                |
| Frontend   | Sidebar, header, profile menu, app layout |
| Tests      | Layout snapshots/components               |
| Acceptance | Desktop layout stable                     |

### Phase 12 — Responsive design

| Item       | Details                   |
| ---------- | ------------------------- |
| Objective  | Desktop/tablet/mobile     |
| Frontend   | CSS media queries only    |
| Tests      | Playwright viewport tests |
| Acceptance | No horizontal overflow    |

### Phase 13 — Dark mode

| Item       | Details                          |
| ---------- | -------------------------------- |
| Objective  | Light/dark theme                 |
| Frontend   | Tokens, toggle, persistence      |
| Backend    | `users.theme_preference`         |
| Tests      | Theme persistence and visibility |
| Acceptance | No flash; accessible contrast    |

### Phase 14 — Accessibility foundation

| Item       | Details                        |
| ---------- | ------------------------------ |
| Objective  | WCAG-aligned baseline          |
| Frontend   | Labels, focus, modals, menus   |
| Tests      | axe + keyboard manual          |
| Acceptance | Critical flows keyboard usable |

### Phase 15 — User profile/account UI

| Item       | Details                                |
| ---------- | -------------------------------------- |
| Objective  | Profile + provider identity            |
| Frontend   | Profile dropdown, provider switcher    |
| Tests      | Keyboard/focus tests                   |
| Acceptance | Switch provider reloads scoped context |

### Phase 16 — Delivery core

| Item       | Details                                         |
| ---------- | ----------------------------------------------- |
| Objective  | Create/manage deliveries                        |
| Backend    | Delivery, items, senders, recipients, addresses |
| Frontend   | Create delivery form                            |
| Tests      | Create/edit/cancel/cross-tenant                 |
| Acceptance | Sender/provider can create delivery             |

### Phase 17 — Maps and pricing

| Item       | Details                                       |
| ---------- | --------------------------------------------- |
| Objective  | Address, route, ETA, quote                    |
| Backend    | Maps service adapter, route estimate snapshot |
| Frontend   | Address autocomplete, map preview             |
| Security   | Separate restricted Maps keys                 |
| Tests      | Mocked Maps responses                         |
| Acceptance | Distance/ETA stored as snapshot               |

Google’s official guidance recommends restricting Maps API keys and using application/API restrictions; do not ship unrestricted browser or server keys. ([Google for Developers][4])

### Phase 18 — Dispatch

| Item       | Details                                  |
| ---------- | ---------------------------------------- |
| Objective  | Assign driver/vehicle                    |
| Backend    | Drivers, vehicles, assignments           |
| Frontend   | Dispatch board                           |
| Tests      | Assignment permissions, reassignment     |
| Acceptance | Dispatcher assigns active driver/vehicle |

### Phase 19 — Driver portal

| Item       | Details                                  |
| ---------- | ---------------------------------------- |
| Objective  | Mobile-first execution                   |
| Frontend   | Driver job list, status buttons          |
| Backend    | Assigned-only job visibility             |
| Tests      | Driver cannot see unassigned jobs        |
| Acceptance | Driver completes pickup/in-transit steps |

### Phase 20 — Proof, payments, notifications

| Item       | Details                                             |
| ---------- | --------------------------------------------------- |
| Objective  | Complete delivery lifecycle                         |
| Backend    | Proof, OTP, photo, payments, invoice, notifications |
| Frontend   | Proof capture, payment status                       |
| Tests      | OTP attempts, file upload, invoice generation       |
| Acceptance | Delivery completes with proof                       |

### Phase 21 — File storage

| Item       | Details                                    |
| ---------- | ------------------------------------------ |
| Objective  | Private uploads                            |
| Backend    | Upload API, signed downloads, scan job     |
| Tests      | Invalid MIME, cross-tenant download denied |
| Acceptance | Files private and authorized               |

### Phase 22 — Audit logging

| Item       | Details                                   |
| ---------- | ----------------------------------------- |
| Objective  | Trace sensitive actions                   |
| Backend    | Audit service, listeners                  |
| Tests      | Audit created for role, delivery, payment |
| Acceptance | Audit logs immutable from tenant UI       |

### Phase 23 — Observability

| Item       | Details                              |
| ---------- | ------------------------------------ |
| Objective  | Production visibility                |
| Backend    | Request ID, structured logs, Horizon |
| Infra      | Error tracker, uptime                |
| Tests      | Log scrubbing                        |
| Acceptance | Failures visible without secrets     |

### Phase 24 — Testing suite hardening

| Item       | Details                           |
| ---------- | --------------------------------- |
| Objective  | High-confidence regression        |
| Tests      | Unit, feature, API, security, E2E |
| Acceptance | CI blocks failures                |

### Phase 25 — Security hardening

| Item       | Details                                   |
| ---------- | ----------------------------------------- |
| Objective  | Pre-release hardening                     |
| Backend    | Rate limits, CORS, headers, log scrubbers |
| Frontend   | XSS-safe rendering                        |
| Tests      | Security tests                            |
| Acceptance | Threat model mitigations verified         |

### Phase 26 — Performance optimization

| Item       | Details                        |
| ---------- | ------------------------------ |
| Objective  | Scale readiness                |
| Backend    | Indexes, eager loading, cache  |
| Frontend   | Lazy-load maps/reports         |
| Tests      | Query count/performance checks |
| Acceptance | No N+1 on major lists          |

### Phase 27 — Deployment pipeline

| Item       | Details                                  |
| ---------- | ---------------------------------------- |
| Objective  | Repeatable release                       |
| Infra      | Docker, CI/CD, migrations, queue workers |
| Tests      | Pipeline validation                      |
| Acceptance | Staging deploy succeeds                  |

### Phase 28 — Final production readiness

| Item       | Details                                    |
| ---------- | ------------------------------------------ |
| Objective  | Go-live gate                               |
| Tests      | Full regression + smoke + security         |
| Acceptance | Checklist complete; no critical risks open |

---

## 28. IDE Agent Execution Instructions

For every task, the IDE agent must produce this before coding:

```text
Requirement:
Evidence:
Current files inspected:
Gap:
Planned smallest correct change:
Tests to add/update:
Risk:
```

After coding, the agent must produce:

```text
Files changed:
Tests run:
Test result:
Manual verification:
API response examples:
Database verification:
Remaining risk:
```

### Bug Fix Protocol

```text
Observed problem:
Evidence:
Affected files:
Root cause:
Why this is the root cause:
Correct fix:
Files changed:
Tests added or updated:
Test command:
Test result:
Proof of resolution:
Remaining risk:
```

### Forbidden IDE agent behavior

1. Do not rewrite modules broadly without evidence.
2. Do not patch frontend when backend authorization is wrong.
3. Do not hide errors silently.
4. Do not remove tests to pass CI.
5. Do not bypass tenant middleware.
6. Do not introduce unscoped queries.
7. Do not use internal IDs in API output.
8. Do not add dependencies without justification.
9. Do not commit secrets.
10. Do not mark complete without proof.

---

## 29. Acceptance Criteria

The application is acceptable only when:

1. Multiple transport providers can exist safely.
2. Multiple users per provider can be invited, assigned roles, suspended, and removed.
3. Provider data is isolated at DB query, API, policy, route-binding, export, file, notification, and job levels.
4. Super admin workflows are separated from tenant workflows.
5. Sender can create a delivery request.
6. System stores pickup, destination, goods, sender, recipient, and schedule.
7. System calculates/stores distance and ETA snapshot.
8. Provider can quote, accept, assign, and monitor delivery.
9. Driver can complete pickup and delivery via mobile-friendly PWA.
10. Sender and recipient can track status through safe tracking pages.
11. Proof of delivery is captured with OTP/photo/timestamp/GPS/name.
12. Payments and invoices are recorded.
13. UI works on desktop, tablet, and mobile.
14. Light and dark modes work and remain readable.
15. Accessibility requirements pass practical verification.
16. APIs are authenticated, validated, authorized, rate-limited, and paginated.
17. Background jobs process slow operations.
18. Logs, monitoring, audit trails, health checks, and backups exist.
19. CI/CD can deploy repeatably.
20. Production config contains no development defaults.
21. Critical tests pass.
22. Critical security threats have mitigations and tests.

---

## 30. Risk Register with Mitigation Steps

| Risk                                 |       Likelihood |   Impact | Mitigation                                                                |
| ------------------------------------ | ---------------: | -------: | ------------------------------------------------------------------------- |
| Scope creep                          |              80% |     High | Lock MVP to request → quote → assign → pickup → deliver → proof → invoice |
| Google Maps cost overrun             |              65% |     High | Debounce, cache, quotas, alerts, restricted keys                          |
| Poor address quality                 |              75% |     High | Map pin, landmarks, address validation, recipient confirmation            |
| Tenant data leakage if rushed        |              70% | Critical | Tenant middleware, policies, scoped route binding, cross-tenant tests     |
| Tenant data leakage if disciplined   |              35% | Critical | Continue security regression testing                                      |
| Driver adoption problems             |              60% |     High | Mobile-first PWA, large buttons, minimal workflow                         |
| Payment reconciliation issues        |              55% |     High | Gateway abstraction, transaction logs, webhook signatures                 |
| Recipient ignores links              |              45% |   Medium | SMS/WhatsApp OTP fallback                                                 |
| Manual dispatch does not scale       | 70% after growth |     High | Batch assignment, route optimization post-MVP                             |
| Sender fraud/false claims            |              40% |   Medium | Photos, OTP, timestamps, GPS, audit trail                                 |
| Failed deliveries from bad addresses |              65% |     High | Address validation, map pin, manual notes                                 |
| Marketing overpromises live tracking |              50% |     High | Say “status tracking” until live GPS is implemented                       |
| Warm brand feels too consumer-facing |              35% |   Medium | Balance warm palette with operational UI density                          |
| “Courier” name feels generic         |              75% |   Medium | Use “Courier by Citrus” externally                                        |

---

## 31. Final Verification Checklist

### Security

* [ ] All protected routes use `auth:sanctum`.
* [ ] All provider routes use tenant middleware.
* [ ] All tenant-owned models include `transport_provider_id`.
* [ ] All tenant-owned policies check ownership and permission.
* [ ] Cross-tenant tests exist for every tenant-owned resource.
* [ ] No API exposes internal sequential IDs.
* [ ] Rate limits exist for auth, OTP, tracking, uploads, Maps proxy.
* [ ] CORS is production-restricted.
* [ ] Cookies are secure in production.
* [ ] Secrets are absent from repository.
* [ ] Logs scrub tokens, OTPs, keys, payment secrets.
* [ ] Google Maps browser/server keys are separated and restricted.

### Backend

* [ ] Migrations include FKs, indexes, constraints.
* [ ] Form Requests validate mutating endpoints.
* [ ] API Resources control response shape.
* [ ] Collections paginate.
* [ ] Slow work uses queues.
* [ ] Jobs restore tenant context.
* [ ] Exports are tenant-scoped.
* [ ] Notifications are tenant-scoped.
* [ ] Audit logs are written for sensitive actions.

### Frontend

* [ ] React + TypeScript compiles cleanly.
* [ ] No jQuery.
* [ ] API client centralizes errors.
* [ ] Permission-aware UI does not replace backend auth.
* [ ] Responsive CSS uses viewport media queries.
* [ ] No horizontal overflow on normal content.
* [ ] Light/dark mode works.
* [ ] Forms have labels and accessible errors.
* [ ] Profile menu is keyboard accessible.
* [ ] Driver portal is mobile-first.

### Product workflows

* [ ] Provider registration/approval works.
* [ ] Staff invitation works.
* [ ] Sender creates delivery.
* [ ] Route estimate stored.
* [ ] Quote created.
* [ ] Dispatcher assigns driver/vehicle.
* [ ] Driver confirms pickup.
* [ ] Driver marks in transit.
* [ ] Recipient tracking page works.
* [ ] Proof of delivery recorded.
* [ ] Delivery completed.
* [ ] Invoice/receipt generated.
* [ ] Reports show tenant-scoped data only.

### Testing and deployment

* [ ] `php artisan test` passes.
* [ ] PHPStan/Larastan passes.
* [ ] TypeScript check passes.
* [ ] ESLint passes.
* [ ] Component tests pass.
* [ ] E2E critical workflows pass.
* [ ] Dependency audits run.
* [ ] Docker image builds.
* [ ] Migrations run on staging.
* [ ] Queue workers process jobs.
* [ ] Scheduler runs.
* [ ] Health route monitored.
* [ ] Backups configured.
* [ ] Rollback process tested.

**Strategic verdict:** build this as a disciplined MVP, not a logistics mega-platform. The highest-probability winning path is: secure tenant foundation first, delivery lifecycle second, maps/pricing third, dispatch/driver proof fourth, billing/notifications fifth. Route optimization, native apps, webhooks, wallet, accounting integrations, and advanced analytics belong after the web/PWA workflow proves real usage.

[1]: https://laravel.com/docs/13.x/sanctum?utm_source=chatgpt.com "Laravel Sanctum - API token authentication"
[2]: https://laravel.com/docs/13.x/horizon?utm_source=chatgpt.com "Laravel Horizon | Laravel 13.x - The clean stack for ..."
[3]: https://laravel.com/docs/13.x/deployment?utm_source=chatgpt.com "Deployment | Laravel 13.x - The clean stack for Artisans ..."
[4]: https://developers.google.com/maps/api-security-best-practices?utm_source=chatgpt.com "Google Maps Platform security guidance"
[5]: https://laravel.com/docs/13.x/scout?utm_source=chatgpt.com "Laravel Scout | Laravel 13.x - The clean stack for Artisans ..."
