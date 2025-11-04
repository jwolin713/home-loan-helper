# Development Plan

- Objective: Deliver an MVP web app that connects to users’ real financial data (via Plaid) and live mortgage rates to compute and track personalized home affordability, with scenario comparisons for first-time homebuyers.
- Scope basis: Aligned to the provided PRD/Feature Specs templates and the Business Context for “Home Loan Navigator.”

## MVP Scope (V1)

- Authentication & onboarding: Email/password + OAuth option; guided first-time setup.
- Data connections: Plaid Link integration to access accounts, income, debts (read-only).
- Rate data: Integrate a live mortgage rates provider; daily refresh + on-demand fetch.
- Affordability engine: Compute monthly payment ranges, DTI, max purchase price, based on connected financials and live rates.
- Scenario modeling: Compare down payment, term, rate scenarios with immediate recalculation.
- Transparency: Show calculation breakdowns and key assumptions.
- Basic alerts: Daily rate change impact on affordability (in-app notifications).
- Privacy & consent: Clear data permissions; minimal PII storage; secure tokens.

## Out-of-Scope (Post-MVP Candidates)

- Premium: Rate change push alerts, neighborhood affordability maps, pre-approval readiness scores, lender referrals and monetization flows.
- Payments: Subscription billing and entitlements.
- Mobile apps: Mobile-first web; native mobile later.

## Assumptions

- Web app stack and hosting align with team standards (e.g., React/Next.js + Node/TypeScript + Postgres). Finalize during Sprint 0.
- Rate API provider selected during Sprint 0; abstraction to swap if needed.
- No storage of raw bank credentials; Plaid access tokens stored securely.

## Milestones & Timeline (Indicative 8–10 Weeks)

- Sprint 0 (1 week): Discovery, architecture, rate API selection, data model, security plan, UX wireframes.
- Sprint 1 (2 weeks): Auth + user profile, Plaid sandbox integration, base data ingestion, schemas, affordability engine v1.
- Sprint 2 (2 weeks): Rate API integration, daily refresh job, scenario modeling UI, calculation transparency views.
- Sprint 3 (2 weeks): Alerts (in-app), UX polish, error handling, accessibility pass, analytics events.
- Sprint 4 (1–2 weeks): Hardening, perf baseline, E2E/UAT, launch prep, docs and runbooks.

## Architecture & Technical Blueprint

- Frontend: SPA/SSR web app; state management for financial profile, scenarios, and rate data; responsive design; accessibility WCAG AA.
- Backend: REST/GraphQL for auth, Plaid token exchange/webhooks, rate fetcher, affordability calculations, scenarios, alerts; background workers for refreshes.
- Data: Postgres (users, financial profile snapshots, scenarios, rate cache, audit logs). Encrypt sensitive fields at rest; rotate secrets.
- Integrations:
  - Plaid: Link flow, token exchange, Accounts, Liabilities/Transactions/Income (as available), webhooks for updates.
  - Rates: Provider abstraction; cache rates with timestamp, source, and parameters (term, points, LTV buckets).
- Security: OAuth/OpenID ready; least-privilege access; secure secret storage; audit trails; comprehensive logging; PII minimization.

## Epics & Key Stories

- Epic: Authentication & Onboarding
  - As a user, I can sign up/sign in and complete a guided setup to connect financial accounts.
  - Acceptance: Email verification, password reset, session handling, onboarding progress saved.
- Epic: Financial Data Integration (Plaid)
  - As a user, I can securely connect accounts and view imported balances, income, and debts.
  - Acceptance: Plaid Link success/error flows; tokenization; data refresh and webhook handling.
- Epic: Mortgage Rate Integration
  - As a user, I see current rates that match my profile and location assumptions.
  - Acceptance: Live fetch, daily refresh, fallback to recent cached rate if provider down.
- Epic: Affordability Engine
  - As a user, I see my estimated monthly payment and max home price with a clear breakdown.
  - Acceptance: Validated formulas; configurable assumptions; unit-tested calculation paths.
- Epic: Scenario Modeling
  - As a user, I can create, edit, and compare scenarios (down payment, term, rate).
  - Acceptance: Instant recalculation; side-by-side comparison; saved scenarios persisted.
- Epic: Alerts & Transparency
  - As a user, I get notified when rate changes affect my affordability.
  - Acceptance: In-app alerts; readable explanations; dismiss and snooze controls.

## Functional Requirements (MVP Highlights)

- Inputs: Connected balances, income, monthly debts; user-entered location, credit-band, down payment.
- Processing: DTI calculations, PITI estimate, max purchase price, scenario deltas, simple tax/insurance defaults by region (configurable).
- Outputs: Payment breakdown, max price, rate-applied scenarios, audit of assumptions and calculation steps.
- Business rules: Never store bank creds; only store tokens; require explicit consent; default assumptions documented and adjustable.

## Non-Functional Requirements

- Performance: P95 page load < 2.5s; P95 calc recompute < 250ms; background refreshes non-blocking.
- Security: TLS everywhere; encrypted secrets; secure token storage; role-based access; audit logging.
- Reliability: 99.5% uptime target for MVP; graceful rate/Plaid degradation with cached data.
- Scalability: Design for 10k MAU initially; stateless services; horizontal scaling path.

## Testing Strategy

- Unit: Calculation engine, data mappers, rate selection logic, Plaid webhook handlers.
- Integration: Plaid sandbox flows, rate provider mock, DB migrations, background jobs.
- E2E (happy/edge): Onboarding, connect account, see affordability, create scenarios, alert triggered.
- Performance: Load test calc paths; cache hit/miss behavior for rate fetch; DB index verification.
- Security: Token handling tests; authZ checks; dependency vulnerability scanning.

## Analytics & Metrics

- Activation: Completed onboarding, connected Plaid, first affordability shown, first scenario created.
- Engagement: Scenario comparisons per user, session frequency, alert interactions.
- Reliability: Rate fetch success %, Plaid webhook success %, calc error rate.
- Business: Conversion proxy metrics (pre-referral): trial→active, retention d30.

## Deliverables per Sprint

- Sprint 0: Architecture doc, data model ERD, wireframes, API/provider decision record, security plan.
- Sprint 1: Auth service, user model, Plaid Link in sandbox, initial schemas, calc engine v1, unit tests.
- Sprint 2: Rate integration + cache, scenario UI v1, transparency views, integration tests.
- Sprint 3: Alerts module, accessibility pass, error states, analytics events, E2E tests.
- Sprint 4: Perf tuning, monitoring dashboards, runbooks, UAT sign-off, release notes.

## Risks & Mitigations

- Rate provider instability: Use cache and provider abstraction; circuit breaker; retries with backoff.
- Data quality gaps (income/liabilities): Allow manual overrides; flag low-confidence inputs.
- User trust: Transparent calculations; clear permissions; no surprise data use; content education.
- Compliance/security: Strict PII minimization; secure storage; regular reviews; vendor DPAs.

## Dependencies

- Plaid account and keys; rate API contract and keys.
- Design system or baseline styles; analytics tooling; monitoring/logging stack.
- Hosting and CI/CD pipelines; secret management.

