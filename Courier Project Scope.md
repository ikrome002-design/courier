# Project Scope: SaaS Web App for Point A to Point B Goods Delivery

## 1. Project Name

**Transport Service Provider Delivery Management SaaS**

A web-based SaaS platform that enables a transport service provider to manage goods delivery requests from pickup location **Point A** to destination **Point B**, including sender onboarding, recipient visibility, route planning, live delivery status, proof of delivery, billing, reporting, and administrator oversight.

The system should be built as a secure, scalable SaaS product using a modern stack such as **Laravel**, **Vue.js or React.js**, **TypeScript**, **Tailwind CSS or Bootstrap 5**, and **PostgreSQL or MySQL**, consistent with the earlier technical direction for a modern SaaS platform.  The frontend should use modern JavaScript or TypeScript and avoid jQuery because the app needs maintainable, component-based architecture. 

---

# 2. Primary Business Objective

The platform must allow a **transport service provider** to receive, price, assign, track, complete, and report on delivery jobs where goods are transported from a sender’s pickup location to a recipient’s destination location.

The transport service provider is the primary business user. The sender and recipient interact with the platform mainly to create, confirm, track, and acknowledge deliveries.

---

# 3. Core User Roles

## 3.1 Super Administrator

The **Super Administrator** controls the entire SaaS platform.

Responsibilities:

1. Manage all transport service provider accounts.
2. Approve, suspend, or deactivate transport providers.
3. Manage subscription plans.
4. View platform-wide analytics.
5. Configure global delivery settings.
6. Manage global pricing rules where applicable.
7. Monitor system health, failed jobs, disputes, and suspicious activity.
8. Access audit logs.
9. Manage integrations and API settings.
10. Control platform-level user permissions.

The Super Administrator is not the same as the transport provider. This role belongs to the SaaS owner/operator.

---

## 3.2 Transport Service Provider

The **Transport Service Provider** is the primary operational user.

Responsibilities:

1. Receive delivery requests.
2. Create manual delivery jobs.
3. Assign jobs to vehicles, drivers, or delivery agents.
4. Track deliveries on a map.
5. Communicate with senders and recipients.
6. Confirm pickup.
7. Confirm delivery.
8. Upload or verify proof of delivery.
9. Manage pricing, invoices, payments, and delivery records.
10. Manage internal staff and permissions.
11. View business reports.

Recommended internal provider roles:

| Internal Role  | Purpose                               |
| -------------- | ------------------------------------- |
| Provider Owner | Full control of provider account      |
| Dispatcher     | Assigns and monitors deliveries       |
| Driver / Rider | Handles pickup and delivery execution |
| Finance User   | Handles invoices, payments, refunds   |
| Support User   | Handles sender/recipient complaints   |
| Viewer         | Read-only access                      |

Likelihood this internal role split becomes necessary after MVP: **85%**. Delivery businesses usually need operational separation once order volume grows beyond one person.

---

## 3.3 Person 1 — Goods Sender

The **Goods Sender** is the person or business requesting goods to be transported.

Responsibilities:

1. Create a delivery request.
2. Enter pickup details.
3. Enter recipient details.
4. Describe the goods.
5. Select delivery service type.
6. View estimated price.
7. Pay or request invoice, depending on business model.
8. Track delivery status.
9. Receive pickup confirmation.
10. Communicate with provider support.
11. View delivery history.

---

## 3.4 Person B — Goods Recipient

The **Goods Recipient** is the person receiving the goods at Point B.

Responsibilities:

1. Receive delivery notification.
2. View delivery tracking page.
3. Confirm availability.
4. Receive ETA updates.
5. Confirm delivery.
6. Provide signature, OTP, photo confirmation, or other proof of receipt.
7. Report delivery issue.

Recipient accounts may be optional in MVP. A recipient can initially access delivery tracking through a secure magic link or OTP. Full recipient accounts become more useful later when recipients regularly receive deliveries.

Likelihood recipient full accounts are needed in MVP: **35%**. Likelihood recipient tracking links are needed in MVP: **95%**.

---

# 4. Delivery Lifecycle

## 4.1 Standard Delivery Flow

1. Sender creates delivery request.
2. System validates pickup and destination addresses.
3. System calculates route, estimated distance, estimated duration, and estimated price.
4. Sender confirms request.
5. Transport provider reviews or automatically accepts the request.
6. Dispatcher assigns a vehicle/driver.
7. Driver proceeds to pickup location.
8. Pickup is confirmed.
9. Delivery is marked as in transit.
10. Recipient receives tracking updates.
11. Driver arrives at destination.
12. Recipient confirms delivery.
13. Proof of delivery is recorded.
14. Delivery is completed.
15. Invoice, receipt, and reporting records are generated.

---

## 4.2 Delivery Statuses

Recommended statuses:

1. Draft
2. Pending Quote
3. Quoted
4. Awaiting Payment
5. Confirmed
6. Assigned
7. Driver En Route to Pickup
8. Arrived at Pickup
9. Picked Up
10. In Transit
11. Arrived at Destination
12. Delivered
13. Failed Delivery
14. Cancelled
15. Returned
16. Disputed
17. Refunded

Do not oversimplify this to only “pending / in progress / completed.” That will become weak once failed deliveries, returns, cancellations, and disputes appear.

---

# 5. Core Modules

## 5.1 Account & Tenant Management

The platform must support multiple transport service provider accounts.

Features:

1. Provider registration.
2. Provider approval workflow.
3. Provider business profile.
4. Provider documents.
5. Provider service areas.
6. Provider operating hours.
7. Provider pricing settings.
8. Provider staff users.
9. Provider role-based permissions.
10. Provider subscription status.
11. Provider suspension/deactivation.

Every transport provider must be treated as a separate tenant/account. Provider A must never access Provider B’s jobs, customers, routes, invoices, vehicles, drivers, or reports.

---

## 5.2 User Management

Features:

1. User registration.
2. Login/logout.
3. Email verification.
4. Password reset.
5. Optional MFA.
6. Role assignment.
7. Permission assignment.
8. User invitation.
9. User suspension.
10. User activity logs.
11. Last login tracking.

Access must be role-based and tenant-scoped. Frontend permission checks improve usability but must not be treated as security controls.

---

## 5.3 Delivery Request Management

Features:

1. Create delivery request.
2. Edit delivery request before confirmation.
3. Cancel request based on rules.
4. Add pickup location.
5. Add destination location.
6. Add sender contact details.
7. Add recipient contact details.
8. Add goods description.
9. Add package size, weight, value, and handling requirements.
10. Add delivery notes.
11. Add preferred pickup date/time.
12. Add delivery time window.
13. Upload goods photos or documents.
14. Generate delivery reference number.
15. Generate tracking link.

Goods fields should include:

| Field                  | Required            |
| ---------------------- | ------------------- |
| Goods category         | Yes                 |
| Description            | Yes                 |
| Weight                 | Recommended         |
| Dimensions             | Recommended         |
| Declared value         | Optional but useful |
| Fragile flag           | Optional            |
| Hazardous flag         | Optional            |
| Perishable flag        | Optional            |
| Special handling notes | Optional            |
| Photo upload           | Recommended         |

For security and liability, the platform should block or flag restricted goods categories depending on the provider’s policy.

---

## 5.4 Quotation & Pricing Module

Pricing options:

1. Fixed base fee.
2. Distance-based pricing.
3. Weight-based pricing.
4. Vehicle-type pricing.
5. Urgency surcharge.
6. Time-window surcharge.
7. Zone-based pricing.
8. Minimum delivery fee.
9. Waiting-time fee.
10. Failed delivery fee.
11. Return fee.
12. Manual override quote.

Formula example:

`Total Price = Base Fee + Distance Fee + Weight Fee + Service Level Fee + Surcharges - Discounts + Taxes`

MVP should support manual quotes plus basic distance-based pricing. Fully dynamic pricing can come later.

Likelihood manual quote is needed early: **80%**.
Likelihood fully automated pricing works perfectly at launch: **30%**, unless the service model is very standardized.

---

## 5.5 Dispatch & Assignment Module

Features:

1. View all active delivery jobs.
2. Assign delivery to driver/vehicle.
3. Reassign delivery.
4. Batch assign deliveries.
5. View driver availability.
6. View vehicle availability.
7. View current job load.
8. Filter by pickup area, delivery area, urgency, status, and time window.
9. Map-based job visualization.
10. Manual route planning.
11. Optimized route planning for multiple stops.

For multi-stop logistics, Google’s Route Optimization API is relevant because it can generate optimized plans for one or multiple vehicles and stops, using constraints such as time windows, vehicle capacity, driver hours, shipment size, and shipment weight. ([Google for Developers][1])

---

## 5.6 Driver / Delivery Agent Module

Even though the app is web-based, drivers need a mobile-friendly web portal or PWA.

Features:

1. View assigned jobs.
2. Accept or reject assignment, depending on business rules.
3. Navigate to pickup.
4. Mark arrival at pickup.
5. Confirm goods pickup.
6. Upload pickup photo.
7. Capture sender signature or OTP.
8. Navigate to destination.
9. Update in-transit status.
10. Mark arrival at destination.
11. Capture recipient signature, OTP, photo, or name.
12. Mark delivery completed.
13. Report failed delivery.
14. Report incident.
15. View job history.

A native mobile app is not mandatory for MVP, but a mobile-first driver PWA is strongly recommended.

Likelihood a web-only desktop experience fails drivers in real operations: **90%**.

---

## 5.7 Live Tracking Module

Features:

1. Public tracking page.
2. Secure tracking link.
3. Delivery status timeline.
4. Pickup and delivery addresses.
5. Estimated delivery time.
6. Driver/vehicle details, where allowed.
7. Map route display.
8. Live driver position, where allowed.
9. Sender notifications.
10. Recipient notifications.
11. Delivery proof after completion.

Privacy rule: recipient should not see unnecessary sender data, and sender should not see unnecessary recipient data beyond delivery needs.

---

## 5.8 Proof of Delivery Module

Proof options:

1. Recipient OTP.
2. Digital signature.
3. Delivery photo.
4. Recipient name.
5. Timestamp.
6. GPS coordinates.
7. Driver notes.
8. Failed delivery reason.
9. Return confirmation.
10. Uploaded document.

Recommended MVP proof methods:

1. OTP confirmation.
2. Delivery photo.
3. Timestamp.
4. GPS coordinates.
5. Recipient name.

Signature capture can be added later unless legally required.

---

## 5.9 Notifications Module

Notification channels:

1. Email.
2. SMS.
3. WhatsApp.
4. In-app notifications.
5. Push notifications, later phase.
6. Webhook notifications for business customers, later phase.

Notification events:

1. Delivery request created.
2. Quote generated.
3. Payment received.
4. Delivery confirmed.
5. Driver assigned.
6. Driver en route.
7. Pickup completed.
8. Delivery in transit.
9. Delivery arriving soon.
10. Delivery completed.
11. Delivery failed.
12. Delivery cancelled.
13. Refund issued.
14. Dispute opened.

Recommended integrations:

| Need     | Integration Option                                  |
| -------- | --------------------------------------------------- |
| Email    | SendGrid, Mailgun, Amazon SES                       |
| SMS      | Twilio, Africa’s Talking, Termii, local SMS gateway |
| WhatsApp | WhatsApp Business API provider                      |
| Push     | Firebase Cloud Messaging                            |
| In-app   | Native database notification system                 |

---

## 5.10 Payments & Billing Module

Payment features:

1. Online payment.
2. Cash-on-delivery marker, if allowed.
3. Invoice generation.
4. Receipt generation.
5. Refunds.
6. Payment status tracking.
7. Provider payout reports.
8. Tax/VAT handling.
9. Promo codes or discounts.
10. Failed payment handling.

Payment gateways should depend on operating country. Common options include Stripe, PayPal, Flutterwave, Paystack, M-Pesa, or local bank/card processors.

For Kenya or East Africa, M-Pesa integration is likely essential. Probability: **85%**, assuming the product operates in Kenya or nearby markets.

---

## 5.11 Vehicle & Fleet Management

Features:

1. Add vehicle.
2. Vehicle type.
3. Plate number.
4. Capacity.
5. Driver assignment.
6. Availability status.
7. Insurance/inspection document upload.
8. Vehicle service area.
9. Vehicle maintenance status.
10. Active/inactive status.

Vehicle types:

1. Motorcycle.
2. Small van.
3. Pickup.
4. Box truck.
5. Lorry.
6. Refrigerated vehicle.
7. Custom vehicle type.

---

## 5.12 Customer Management

Customer records should support both senders and recipients.

Features:

1. Sender profiles.
2. Recipient profiles.
3. Business customer profiles.
4. Contact persons.
5. Address book.
6. Frequent routes.
7. Delivery history.
8. Payment history.
9. Notes and risk flags.
10. KYC documents for business clients, if needed.

---

## 5.13 Reports & Analytics

Provider reports:

1. Total deliveries.
2. Completed deliveries.
3. Failed deliveries.
4. Cancelled deliveries.
5. Average delivery time.
6. Revenue.
7. Outstanding invoices.
8. Driver performance.
9. Vehicle utilization.
10. Most common pickup zones.
11. Most common delivery zones.
12. Customer activity.
13. Delivery delay reasons.
14. Failed delivery reasons.

Super Admin reports:

1. Total providers.
2. Active providers.
3. Total platform deliveries.
4. Gross merchandise value.
5. Platform revenue.
6. Subscription revenue.
7. API usage.
8. Failed jobs.
9. Suspicious activity.
10. System performance.

---

# 6. Google Maps Integration Scope

Google Maps Platform should be used for the mapping, routing, address, and location intelligence layer. Google’s Maps JavaScript API supports web map display and map features, while the Google Maps Platform includes routing, geocoding, places, and route optimization products relevant to this delivery use case. ([Google for Developers][2])

## 6.1 Required Google Maps Services

| Requirement                             | Recommended Google Service      |
| --------------------------------------- | ------------------------------- |
| Display map in web app                  | Maps JavaScript API             |
| Pickup/destination autocomplete         | Places API / Place Autocomplete |
| Convert address to coordinates          | Geocoding API                   |
| Convert coordinates to address          | Reverse Geocoding               |
| Calculate route from Point A to Point B | Routes API                      |
| Calculate ETA and distance              | Routes API                      |
| Optimize multi-stop deliveries          | Route Optimization API          |
| Validate delivery addresses             | Address Validation API          |
| Show markers and routes                 | Maps JavaScript API             |
| Restrict map search by country/region   | Places API settings             |

The Routes API can return routes between locations with real-time traffic support and can handle multiple locations and waypoint optimization. ([Google for Developers][3]) The Geocoding API converts addresses, coordinates, and Place IDs between human-readable and geographic formats, which is directly useful for pickup and delivery workflows. ([Google for Developers][4])

---

## 6.2 Address Search & Autocomplete

The system must provide autocomplete for pickup and delivery addresses.

Requirements:

1. Suggest addresses as the user types.
2. Bias results to the provider’s operating country or city.
3. Restrict results to supported delivery regions.
4. Store selected Place ID.
5. Store latitude and longitude.
6. Store formatted address.
7. Allow manual address notes such as apartment, floor, gate, landmark.
8. Validate incomplete or ambiguous addresses before job confirmation.

Google’s Autocomplete service returns place and query predictions based on user input and can use geographic bounds to influence the search area. ([Google for Developers][5])

---

## 6.3 Address Validation

The platform should validate addresses before confirming a delivery. Google’s Address Validation API validates, standardizes, and geocodes addresses; it can improve delivery predictability by catching bad addresses and reducing delivery failures. ([Google for Developers][6])

Validation behavior:

1. Accept entered address.
2. Validate address.
3. Identify missing components.
4. Suggest corrected address.
5. Ask sender to confirm corrected address.
6. Store validation result.
7. Flag low-confidence addresses for manual review.

This is more important for structured-address countries. In markets where many deliveries use landmarks instead of formal addresses, the system must support manual landmarks and pinned map locations.

---

## 6.4 Route Calculation

The platform must calculate:

1. Pickup-to-destination distance.
2. Estimated travel duration.
3. Estimated arrival time.
4. Route polyline for map display.
5. Alternative routes, where useful.
6. Toll information, where relevant.
7. Traffic-aware ETA, where available.

Route result must be stored as a delivery estimate snapshot. Do not recalculate historical delivery pricing without preserving the original quoted estimate.

---

## 6.5 Route Optimization

For multi-delivery dispatching, route optimization should be added after the basic MVP.

Features:

1. Optimize stop sequence.
2. Assign shipments to vehicles.
3. Consider vehicle capacity.
4. Consider pickup/delivery time windows.
5. Consider driver working hours.
6. Balance delivery load.
7. Re-optimize after cancellations or failed pickups.
8. Display optimized route to dispatcher and driver.

Google’s Route Optimization API can optimize routes and task assignments based on business objectives and physical constraints such as driver hours, vehicle capacity, shipment size, weight, and time windows. ([Google for Developers][1])

MVP likelihood of needing full route optimization: **40%**.
Post-MVP likelihood once there are multiple vehicles and many same-day deliveries: **80%**.

---

## 6.6 Google Maps API Security

Google Maps API keys must be secured. Google explicitly recommends restricting API keys, using separate keys per app, deleting unused keys, checking usage, splitting client-side and server-side usage, and disabling unused services. ([Google for Developers][7]) Google also states that unrestricted API keys can create financial exposure because the account owner is responsible for charges caused by abuse. ([Google for Developers][7])

Required security controls:

1. Separate browser API key and server API key.
2. Restrict browser key by domain.
3. Restrict server key by IP or server environment where supported.
4. Restrict each key to only required APIs.
5. Monitor Maps API usage.
6. Set billing alerts.
7. Set quota limits.
8. Rotate keys carefully.
9. Never expose unrestricted server-side keys to frontend code.
10. Disable unused Google Maps services.

---

# 7. Other Required Integrations

## 7.1 Payment Gateway

Needed for:

1. Sender payments.
2. Business customer invoicing.
3. Refunds.
4. Provider subscription billing.
5. Provider payout reporting.

Recommended options depend on country:

| Region / Use Case                 | Likely Gateway                        |
| --------------------------------- | ------------------------------------- |
| Global cards                      | Stripe                                |
| Africa-focused cards/mobile money | Flutterwave or Paystack               |
| Kenya mobile money                | M-Pesa Daraja                         |
| Enterprise invoicing              | Bank transfer + manual reconciliation |

---

## 7.2 SMS / WhatsApp Gateway

Needed for:

1. OTP delivery confirmation.
2. Sender updates.
3. Recipient updates.
4. Driver assignment alerts.
5. Failed delivery notifications.

Recommended providers:

1. Twilio.
2. Africa’s Talking.
3. WhatsApp Business API provider.
4. Local SMS aggregator.

---

## 7.3 Email Provider

Needed for:

1. Account verification.
2. Password reset.
3. Delivery receipts.
4. Invoices.
5. Admin alerts.
6. Provider onboarding.
7. Dispute notifications.

Recommended providers:

1. Amazon SES.
2. Mailgun.
3. SendGrid.
4. Postmark.

---

## 7.4 File Storage

Needed for:

1. Goods photos.
2. Proof of delivery photos.
3. Provider documents.
4. Vehicle documents.
5. Invoices.
6. Receipts.
7. Dispute attachments.

Recommended:

1. AWS S3.
2. Cloudflare R2.
3. Google Cloud Storage.
4. DigitalOcean Spaces.

Files must not be stored publicly by default. Use private storage and signed temporary URLs.

---

## 7.5 Accounting / ERP Integration

Optional post-MVP.

Possible integrations:

1. QuickBooks.
2. Xero.
3. Zoho Books.
4. Sage.
5. Custom export CSV.

Needed when providers need formal accounting reconciliation.

Likelihood needed in MVP: **20%**.
Likelihood needed for business clients later: **65%**.

---

## 7.6 Webhook / API Integration

Post-MVP but important for B2B.

Features:

1. Business customers create deliveries via API.
2. External systems receive delivery status updates.
3. Webhook retry mechanism.
4. Webhook signature verification.
5. API key management.
6. API usage logs.
7. Rate limiting.

This becomes important when e-commerce stores, warehouses, pharmacies, retailers, or enterprise clients need automated delivery creation.

Likelihood needed for serious B2B growth: **75%**.

---

# 8. Functional Requirements by User Role

## 8.1 Super Administrator Features

1. Login to admin panel.
2. View platform dashboard.
3. Manage transport providers.
4. Approve provider registration.
5. Suspend provider.
6. View all deliveries across platform.
7. View provider-specific reports.
8. Manage subscription plans.
9. Manage platform pricing rules.
10. Manage system integrations.
11. View API usage.
12. View error logs.
13. View audit logs.
14. Manage global notification templates.
15. Manage prohibited goods categories.
16. Manage dispute escalation.
17. Export reports.

---

## 8.2 Transport Provider Features

1. Provider dashboard.
2. Delivery request inbox.
3. Create delivery manually.
4. Accept/reject delivery request.
5. Generate quote.
6. Assign driver/vehicle.
7. View live delivery map.
8. Manage drivers.
9. Manage vehicles.
10. Manage senders/customers.
11. Manage recipients.
12. Manage proof of delivery.
13. Manage failed deliveries.
14. Manage returns.
15. Manage invoices.
16. Manage payments.
17. Manage provider staff.
18. Configure delivery zones.
19. Configure pricing.
20. View performance reports.

---

## 8.3 Goods Sender Features

1. Register/login.
2. Create delivery request.
3. Save pickup addresses.
4. Save recipients.
5. Enter goods details.
6. Upload goods photo.
7. View quote.
8. Pay for delivery.
9. Track delivery.
10. Cancel delivery before cutoff.
11. View delivery history.
12. Download receipts.
13. Open support ticket.
14. Rate delivery experience.

---

## 8.4 Goods Recipient Features

1. Access delivery tracking link.
2. View expected delivery.
3. Confirm contact information.
4. View ETA.
5. Receive OTP.
6. Confirm delivery.
7. Sign digitally, where required.
8. Report issue.
9. Rate delivery experience.

---

# 9. Non-Functional Requirements

## 9.1 Security

Requirements:

1. Secure authentication.
2. Email verification.
3. Password reset.
4. Optional MFA.
5. Role-based access control.
6. Tenant isolation.
7. CSRF protection.
8. API rate limiting.
9. Input validation.
10. File validation.
11. Private file storage.
12. Secure audit logging.
13. Encrypted sensitive fields where needed.
14. Secure API key storage.
15. No secrets in frontend code except restricted public browser keys.
16. HTTPS in production.
17. Strict CORS configuration.
18. Protection against IDOR/cross-account access.

Most likely severe SaaS failure mode: **broken access control / tenant data leakage**, probability **70%** in poorly designed SaaS systems unless tenant scoping and authorization policies are enforced from the beginning.

---

## 9.2 Performance

Requirements:

1. Paginated delivery lists.
2. Indexed database fields.
3. Queue-based notifications.
4. Queue-based report generation.
5. Cached route estimates where legally and contractually allowed.
6. Lazy-loaded maps.
7. Optimized image upload.
8. CDN for static files.
9. Database query monitoring.
10. Background jobs for heavy work.

Google Maps usage must be controlled because map loads, autocomplete requests, route calculations, and optimization requests can become a major operating cost.

---

## 9.3 Responsiveness

The app must support desktop, tablet, and mobile layouts. The earlier requirement correctly states that responsive behavior should be based on browser viewport width, not JavaScript device detection.  The required breakpoint structure can follow desktop at `>=1025px`, tablet at `768px–1024px`, and mobile at `<=767px`. 

Recommended usage:

| User                     | Primary Device   |
| ------------------------ | ---------------- |
| Super Admin              | Desktop          |
| Transport Provider Owner | Desktop / tablet |
| Dispatcher               | Desktop          |
| Driver                   | Mobile           |
| Sender                   | Mobile / desktop |
| Recipient                | Mobile           |

---

## 9.4 Accessibility

Requirements:

1. Keyboard navigation.
2. Visible focus states.
3. High-contrast text.
4. Proper form labels.
5. Accessible validation messages.
6. Touch-friendly buttons.
7. Support browser zoom.
8. Avoid tiny map controls on mobile.
9. Avoid relying on color alone for delivery status.
10. Provide text equivalents for status and route information.

---

# 10. Recommended Technical Architecture

## 10.1 Backend

Recommended:

1. Laravel.
2. PHP 8.2+.
3. REST API.
4. Laravel Sanctum for SPA authentication.
5. PostgreSQL preferred.
6. Redis for cache and queues.
7. Laravel Horizon for queue monitoring.
8. Laravel Scheduler for scheduled jobs.
9. S3-compatible storage.
10. Spatie Permission or Laravel Policies/Gates.
11. Meilisearch or Typesense for search, later phase.

---

## 10.2 Frontend

Recommended:

1. Vue.js or React.js.
2. TypeScript.
3. Vite.
4. Tailwind CSS.
5. Component-based architecture.
6. Responsive layouts.
7. Dark mode support.
8. Map components.
9. Form components.
10. Shared API client.
11. Role-aware navigation.
12. Real-time status updates.

---

## 10.3 Realtime Layer

Options:

1. Laravel WebSockets.
2. Pusher.
3. Ably.
4. Socket.io.
5. Firebase, if broader realtime infrastructure is preferred.

Realtime should be used for:

1. Delivery status updates.
2. Driver location updates.
3. Dispatcher dashboard updates.
4. Notifications.
5. Assignment changes.

MVP can use polling every 15–30 seconds for tracking. Realtime WebSockets become more important at higher volume.

Probability polling is enough for MVP: **70%**.
Probability WebSockets are needed after scaling: **75%**.

---

# 11. Suggested Database Entities

Core tables:

1. `users`
2. `roles`
3. `permissions`
4. `transport_providers`
5. `provider_users`
6. `senders`
7. `recipients`
8. `addresses`
9. `delivery_requests`
10. `delivery_items`
11. `delivery_quotes`
12. `delivery_assignments`
13. `drivers`
14. `vehicles`
15. `driver_locations`
16. `delivery_status_events`
17. `proofs_of_delivery`
18. `payments`
19. `invoices`
20. `refunds`
21. `notifications`
22. `support_tickets`
23. `disputes`
24. `audit_logs`
25. `api_keys`
26. `webhook_endpoints`
27. `webhook_deliveries`
28. `provider_subscriptions`
29. `pricing_rules`
30. `service_zones`

Every tenant-owned table should include `transport_provider_id` or equivalent tenant ownership key.

---

# 12. MVP Scope

## 12.1 MVP Must Include

1. Super Admin login.
2. Transport provider account management.
3. Provider dashboard.
4. Sender registration/login.
5. Create delivery request.
6. Pickup and destination address entry.
7. Google Maps address autocomplete.
8. Distance and ETA calculation.
9. Manual or basic automated quote.
10. Delivery request confirmation.
11. Dispatcher assignment.
12. Driver mobile web portal.
13. Status updates.
14. Sender tracking page.
15. Recipient tracking page.
16. OTP or photo proof of delivery.
17. Email/SMS notifications.
18. Basic payment integration.
19. Delivery history.
20. Basic reports.
21. Audit logs.
22. Role-based access control.
23. Secure tenant isolation.

---

## 12.2 Post-MVP Features

1. Route optimization.
2. Multi-stop delivery planning.
3. Advanced pricing engine.
4. WhatsApp integration.
5. Webhook/API access for business customers.
6. Mobile apps.
7. Driver wallet or payout module.
8. Customer ratings.
9. Advanced dispute management.
10. Insurance module.
11. Accounting integrations.
12. AI-based ETA prediction.
13. Fraud/risk scoring.
14. Subscription billing automation.
15. Warehouse pickup scheduling.
16. Barcode/QR scanning.
17. Fleet maintenance.
18. Advanced analytics.

---

# 13. Key Risks & Realistic Likelihoods

| Risk                                     |                        Likelihood |   Impact | Mitigation                                                            |
| ---------------------------------------- | --------------------------------: | -------: | --------------------------------------------------------------------- |
| Google Maps cost overruns                |                               65% |     High | Quotas, billing alerts, API restrictions, cache strategy              |
| Poor address quality                     |                               75% |     High | Map pin, landmarks, address validation, manual notes                  |
| Tenant data leakage                      | 35% if well-built / 70% if rushed | Critical | Tenant scoping, policies, automated tests                             |
| Driver adoption problems                 |                               60% |     High | Mobile-first PWA, simple workflow                                     |
| Recipients ignore confirmation links     |                               45% |   Medium | SMS/WhatsApp OTP fallback                                             |
| Payment reconciliation issues            |                               55% |     High | Clear payment states, transaction logs, gateway webhooks              |
| Manual dispatch does not scale           |                  70% after growth |     High | Route optimization and batch assignment                               |
| Sender fraud or false claims             |                               40% |   Medium | Photos, OTP, signatures, audit trail                                  |
| Failed deliveries due to wrong addresses |                               65% |     High | Address validation, map pin, recipient confirmation                   |
| Scope creep                              |                               80% |     High | Lock MVP around request → assign → pickup → deliver → proof → invoice |

---

# 14. Success Criteria

The project is successful when:

1. A sender can create a delivery request from Point A to Point B.
2. The system can calculate distance, route, and ETA.
3. The transport provider can quote, accept, assign, and monitor the delivery.
4. A driver can complete pickup and delivery through a mobile-friendly interface.
5. The sender and recipient can track status.
6. Proof of delivery is captured.
7. Payments and invoices are recorded.
8. Provider data is isolated from other providers.
9. Super Admin can manage providers and platform operations.
10. The app works cleanly across desktop, tablet, and mobile.
11. Google Maps API usage is secure, restricted, and monitored.
12. The system can scale beyond manual delivery tracking into fleet dispatch and route optimization.

---

# 15. Recommended Build Priority

## Phase 1 — Foundation

1. Authentication.
2. Roles and permissions.
3. Provider tenancy.
4. User management.
5. Basic dashboard.
6. Audit logs.

## Phase 2 — Delivery Core

1. Delivery request creation.
2. Sender and recipient records.
3. Address entry.
4. Goods details.
5. Delivery status lifecycle.
6. Tracking reference.

## Phase 3 — Maps & Pricing

1. Google Maps display.
2. Places autocomplete.
3. Geocoding.
4. Routes API.
5. ETA and distance.
6. Basic price calculation.

## Phase 4 — Dispatch

1. Driver records.
2. Vehicle records.
3. Assignment.
4. Driver portal.
5. Pickup confirmation.
6. Delivery confirmation.

## Phase 5 — Proof, Payments & Notifications

1. OTP proof.
2. Photo proof.
3. SMS/email notifications.
4. Payment integration.
5. Invoice/receipt generation.

## Phase 6 — Scale Features

1. Route optimization.
2. Webhooks/API.
3. Advanced analytics.
4. Accounting integrations.
5. Native mobile apps, only after the web/PWA workflow proves demand.

**Most realistic MVP complexity:** medium-high.
**Estimated probability of successful MVP delivery with disciplined scope:** **75%**.
**Estimated probability of delays if route optimization, native apps, wallet, accounting, and advanced analytics are included in MVP:** **85%**.

[1]: https://developers.google.com/maps/documentation/route-optimization/overview "What is the Route Optimization API  |  Google for Developers"
[2]: https://developers.google.com/maps/documentation/javascript/overview "Overview  |  Maps JavaScript API  |  Google for Developers"
[3]: https://developers.google.com/maps/documentation/routes/overview "Compute Routes Overview  |  Routes API  |  Google for Developers"
[4]: https://developers.google.com/maps/documentation/geocoding/overview "Geocoding API overview  |  Google for Developers"
[5]: https://developers.google.com/maps/documentation/places/web-service/place-autocomplete "Autocomplete (New)  |  Places API  |  Google for Developers"
[6]: https://developers.google.com/maps/documentation/address-validation/overview "Address Validation API overview  |  Google for Developers"
[7]: https://developers.google.com/maps/api-security-best-practices "Google Maps Platform security guidance  |  Google for Developers"
