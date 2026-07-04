# Tekmetric (tekmetric)

Tekmetric is a cloud-based auto repair shop management platform (digital vehicle inspections, estimates, repair orders, inventory, job/technician tracking, and customer communication). Tekmetric operates a partner-gated REST API - access requires requesting credentials at api.tekmetric.com and waiting for Tekmetric's approval (no self-serve signup or public API reference); approved partners authenticate with OAuth 2.0 client credentials against a sandbox or production base URL and read shop, customer, vehicle, repair order, job, employee, appointment, canned job, and inventory data.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/tekmetric/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/tekmetric/refs/heads/main/apis.yml)

## A note on access and sourcing

Tekmetric does not publish a self-serve developer portal or a public API reference. `api.tekmetric.com` is a request-access landing page: prospective integrators submit a request and, per third-party accounts, wait roughly 2-3 weeks for Tekmetric to review and issue OAuth Client ID/Secret credentials. `developer.tekmetric.com` does not resolve.

Because the API reference itself is gated, the endpoint paths, request parameters, and response envelope documented in this repository are **not** transcribed from an official Tekmetric document. They are sourced from [beetlebugorg/tekmetric-mcp](https://github.com/beetlebugorg/tekmetric-mcp), an independent, actively maintained, open-source Go client and MCP server that holds approved Tekmetric API credentials and calls the live production/sandbox API. Its source code (`pkg/tekmetric/*.go`) shows the concrete request paths, query parameters, OAuth flow, and pagination envelope used against Tekmetric's real API. That project explicitly states it is "not an official Tekmetric product" and is not affiliated with, endorsed by, or supported by Tekmetric. Treat the endpoints below as **confirmed-via-third-party-client**, not as officially published by Tekmetric - `endpointsModeled: true` in `review.yml` reflects that distinction.

Only read (`GET`) endpoints are documented, because the reference client itself is read-only; write endpoints (creating or updating repair orders, jobs, customers, etc.) may exist in the full partner API but are not evidenced anywhere in public sources.

## Tags

- Automotive
- Auto Repair
- Shop Management
- Fleet
- Vertical SaaS

## Timestamps

- **Created:** 2026-07-04
- **Modified:** 2026-07-04

## APIs

### Tekmetric Shops API

Lists the shop (location) records an approved OAuth client is scoped to, and retrieves a single shop's hours, labor rates, and settings by ID.

- **Base URL:** `https://shop.tekmetric.com/api/v1`
- **Confirmed endpoints:** `GET /shops`, `GET /shops/{id}`

### Tekmetric Customers API

Lists and searches customers by name, email, or phone within a shop, and retrieves a single customer's contact info, addresses, and marketing/accounts-receivable flags by ID.

- **Base URL:** `https://shop.tekmetric.com/api/v1`
- **Confirmed endpoints:** `GET /customers`, `GET /customers/{id}` (with `search`, `eligibleForAccountsReceivable`, `okForMarketing`, `customerTypeId` query params)

### Tekmetric Vehicles API

Lists and searches a shop's vehicles by VIN, license plate, make/model, or owning customer, and retrieves a single vehicle by ID.

- **Base URL:** `https://shop.tekmetric.com/api/v1`
- **Confirmed endpoints:** `GET /vehicles`, `GET /vehicles/{id}` (with `search`, `customerId` query params)

### Tekmetric Repair Orders API

Lists and filters repair orders by status, date, repair order number, customer, or vehicle, and retrieves a single repair order by ID, including totals and workflow status.

- **Base URL:** `https://shop.tekmetric.com/api/v1`
- **Confirmed endpoints:** `GET /repair-orders`, `GET /repair-orders/{id}` (with `repairOrderNumber`, `repairOrderStatusId`, `customerId`, `vehicleId` query params)
- **Known quirk:** combining a date-range filter with a deleted-date filter on `/repair-orders` returns only deleted records; active/updated and deleted records need separate calls, merged client-side.

### Tekmetric Jobs API

Lists and filters the individual jobs (services) within repair orders by vehicle, customer, repair order, authorization state, or status, and retrieves a single job with its labor lines, parts, and technician assignments by ID.

- **Base URL:** `https://shop.tekmetric.com/api/v1`
- **Confirmed endpoints:** `GET /jobs`, `GET /jobs/{id}` (with `vehicleId`, `repairOrderId`, `customerId`, `authorized`, `repairOrderStatusId` query params)

### Tekmetric Employees API

Lists a shop's employees (technicians and staff) and retrieves a single employee by ID.

- **Base URL:** `https://shop.tekmetric.com/api/v1`
- **Confirmed endpoints:** `GET /employees`, `GET /employees/{id}`

### Tekmetric Appointments API

Lists and filters a shop's scheduled appointments by date, customer, or vehicle, and retrieves a single appointment by ID.

- **Base URL:** `https://shop.tekmetric.com/api/v1`
- **Confirmed endpoints:** `GET /appointments`, `GET /appointments/{id}` (with `customerId`, `vehicleId`, `includeDeleted` query params)

### Tekmetric Inventory API

Lists a shop's parts inventory, filterable by part type and tire attributes (ratio, diameter), including stock levels.

- **Base URL:** `https://shop.tekmetric.com/api/v1`
- **Confirmed endpoints:** `GET /inventory` (with `partTypeId`, `ratio`, `diameter` query params)

### Tekmetric Canned Jobs API

Lists a shop's canned jobs - pre-built menu services bundling standard labor and parts (for example, a smart or regular canned job for an oil change) - available to apply to estimates.

- **Base URL:** `https://shop.tekmetric.com/api/v1`
- **Confirmed endpoints:** `GET /canned-jobs`

## Authentication

OAuth 2.0 client credentials grant. Approved partners `POST` `client_id`/`client_secret` (as an HTTP Basic `Authorization` header) plus `grant_type=client_credentials` to the token endpoint, and use the resulting bearer token on all subsequent calls.

- **Sandbox token endpoint:** `https://sandbox.tekmetric.com/api/v1/oauth/token`
- **Production token endpoint:** `https://shop.tekmetric.com/api/v1/oauth/token`
- The token's `scope` field returns the space-separated shop IDs the client is authorized for.
- Per third-party reporting, the token endpoint is idempotent (repeated calls return the same token) and tokens do not expire unless explicitly revoked by Tekmetric, despite an `expires_in` field being present in the response.

## Pagination

All list endpoints return a Spring Data `Page` envelope: `content` (the array of records), `totalPages`, `totalElements`, `first`, `last`, `size`, `number`, `numberOfElements`.

## Common Properties

- [GitHub Organization](https://github.com/Tekmetric)
- [LinkedIn](https://www.linkedin.com/company/tekmetric)
- [Website](https://www.tekmetric.com/)
- [Documentation](https://api.tekmetric.com)
- [Plans](plans/tekmetric-plans-pricing.yml)
- [Rate Limits](rate-limits/tekmetric-rate-limits.yml)
- [Fin Ops](finops/tekmetric-finops.yml)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
