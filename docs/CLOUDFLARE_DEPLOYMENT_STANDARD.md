# ZestfulPulse Cloudflare Deployment Standard

This is the organization-wide default for Cloudflare Worker deployments.

## Shared credentials

- Secret: `CLOUDFLARE_API_TOKEN`
  - Organization-level GitHub Actions secret
  - Uses the shared Cloudflare token named **ZestfulPulse GitHub Deploy**
- Variable: `CLOUDFLARE_ACCOUNT_ID`
  - Organization-level GitHub Actions variable
  - Non-secret

Do not create repository-specific Cloudflare tokens unless a project has an explicit isolation requirement.

## Required release sequence

```text
Credential / Workers permission preflight
→ npm ci
→ Build / Test
→ Cloudflare Deploy
→ Production URL Smoke
→ Actual rendered wide + narrow screenshots
→ DPL Visual Release Review
```

Build PASS is not DPL PASS.

Deploy PASS is not DPL PASS.

DPL PASS requires production deployment, smoke PASS, and actual rendered evidence review. If rendered evidence has not been inspected, the verdict remains `NOT_VERIFIED`.

## Reusable workflow

Call:

```yaml
jobs:
  deploy:
    uses: ZestfulPulse/.github/.github/workflows/cloudflare-worker-deploy.yml@main
    with:
      production_url: https://example.zestfulpulse.com
      cloudflare_account_id: ${{ vars.CLOUDFLARE_ACCOUNT_ID }}
    secrets: inherit
```

The repository must be allowed to access the organization secret and variable.

## Project rules

- Keep Cloudflare credentials out of source files.
- Keep deployment-specific project bindings in the project repository.
- Reuse this workflow instead of duplicating credential handling.
- Production screenshot artifacts are release evidence, not decoration.
- A failed preflight is an organization deployment configuration issue first, not a reason to mint another token.
