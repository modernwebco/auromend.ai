# AuraMend AI — Production SaaS foundation

AuraMend is a multi-tenant AI-agent SaaS foundation: each business gets an isolated workspace, configurable agent, approved knowledge, conversations, leads, appointments, subscription state, and a website widget.

## Included

- Public marketing page
- Real account registration/login with signed sessions
- Per-business tenant isolation
- Password hashing with Node scrypt
- SQLite WAL database for local/single-node deployment
- Agent configuration
- Knowledge base CRUD
- Website import/crawl into approved knowledge
- Grounded AI chat with optional OpenAI Responses API
- Secure, scoped widget keys
- Embeddable `widget.js`
- Conversation persistence
- Lead capture API
- Appointment capture API
- Basic analytics-ready event table
- Subscription state and Stripe Checkout/webhook hooks
- HTTP security headers, CORS controls, JSON limits and rate limiting

## Run locally

Requires Node 22.5+.

```bash
cp .env.example .env
npm start
```

Open `http://localhost:3000`.

Demo account:
- `owner@auramend.local`
- `ChangeMe123!`

Change the demo password/seed before any public deployment.

## Real AI

Set:

```env
OPENAI_API_KEY=...
OPENAI_MODEL=gpt-5.6-mini
```

The same `/api/chat` endpoint then uses the configured model and only passes approved tenant knowledge into the agent context.

## Deployment

For a serious production deployment, put the app behind a TLS reverse proxy/load balancer, set a strong `JWT_SECRET`, configure `APP_URL`, use managed database/storage infrastructure, and run the app in multiple replicas only after moving persistence to managed Postgres. The SQL schema is intentionally normalized so the SQLite adapter can be replaced by Postgres without changing the product API.

Configure Stripe with:

```env
STRIPE_SECRET_KEY=...
STRIPE_WEBHOOK_SECRET=...
STRIPE_PRICE_STARTER=price_...
STRIPE_PRICE_GROWTH=price_...
```

Webhook endpoint: `/api/stripe/webhook`.

## Widget

Generate a scoped widget key from the dashboard. The production installation pattern is:

```html
<script src="https://YOUR-AURAMEND-DOMAIN/widget.js"
        data-agent="YOUR_AGENT_ID"
        data-key="YOUR_WIDGET_KEY"
        data-api="https://YOUR-AURAMEND-DOMAIN"></script>
```

Widget keys are intentionally scoped to one agent. Rotate them by generating a new key and revoking the old key at the database/admin layer.

## Next production hardening

Before onboarding paying customers: managed Postgres + migrations, Redis-backed rate limits/queues, object storage, email verification/password reset, CSRF strategy for cookie sessions if adopted, SSO/team invitations, full RBAC, encrypted integration credentials, audit logs, observability, automated backups, background website crawling, vector retrieval/pgvector, human handoff, calendar/CRM connectors, Stripe customer portal, and automated tests/CI/CD.
