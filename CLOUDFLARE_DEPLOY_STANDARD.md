# ZestfulPulse Cloudflare Deployment Standard v1.1

## Goal
Every new ZestfulPulse web service must fail early and clearly before Wrangler deploy.

## Organization baseline
- GitHub is source of truth.
- Cloudflare deployment uses `CLOUDFLARE_API_TOKEN` and `CLOUDFLARE_ACCOUNT_ID`.
- Node 22 is the baseline for Wrangler 4.x.
- Secrets never live in source.
- Production URL uses `<project>.zestfulpulse.com` unless explicitly overridden.

## Required onboarding for every new repository
1. Confirm the repository can read the shared `CLOUDFLARE_API_TOKEN`.
2. Confirm `CLOUDFLARE_ACCOUNT_ID` resolves to the ZestfulPulse Cloudflare account.
3. Preflight the token against the Workers API before npm install/deploy.
4. Run tests.
5. Deploy.
6. Smoke-test the production URL when configured.
7. Only then mark deployment complete.

## Token policy
The shared deployment token must include the intended ZestfulPulse account in Account Resources and allow Workers Scripts:Edit. If custom-domain automation is performed, the token also needs the minimum Zone/DNS permissions required for that operation. Do not broaden permissions beyond the deployment workflow.

## Failure semantics
- Missing token/account: CONFIG FAIL
- Token present but Workers API denied: AUTHZ FAIL
- Build/test failure: BUILD FAIL
- Wrangler failure after preflight: DEPLOY FAIL
- Production URL not responding after deploy: SMOKE FAIL
- PASS requires deploy + smoke evidence.

## New repository rule
Creating a repository is not considered deployment-ready until the shared secret/variable access check passes. This is part of project bootstrap, not a later troubleshooting step.
