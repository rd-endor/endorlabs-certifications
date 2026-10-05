# Endor Labs Certifications

Become an AppSec expert with Endor Labs.

This repository helps attendees prepare for **Endor Labs certification** sessions and guided labs. Use it as a pre-work checklist before any instructor-led or self-paced certification lab that uses Endor Labs with intentionally vulnerable sample applications.

Exact session details (tenant URL, namespace, GitHub organization or assigned repository, and whether the GitHub App is organizer-managed) come from your instructors. Follow those instructions when they differ from the examples below.

---

## Lab model (typical)

Many certification labs ask attendees to work with GitHub repositories that contain intentionally vulnerable applications and connect those repositories to an Endor Labs GitHub App installation managed by the organizers.

Common reference applications used in prep and labs (these are **GitHub repositories**, not GitHub Apps):

- [OWASP VulnerableApp](https://github.com/SasanLabs/VulnerableApp)
- [DVWS Node](https://github.com/snoopysecurity/dvws-node)

Use only the repository your instructors assign (or a personal fork of a lab repo)—never a production repository.

---

## Required attendee preparation

### 1. Confirm account and access

Before the session:

- Have an active [GitHub.com](https://github.com) account with MFA enabled.
- Sign in to GitHub and confirm you can access repositories, branches, and pull requests.
- Accept the invitation to the session GitHub organization or assigned repository (when provided).
- Confirm you can create a branch and open a pull request in your assigned lab repository.
- Be prepared to authorize the Endor Labs GitHub App if prompted by the session instructions.
- Confirm you can sign in to the Endor Labs tenant and namespace provided by the instructors.

The Endor Labs GitHub App can selectively scan repositories for SCA, secrets, RSPM, and GitHub Actions. Organizers typically install and authorize the App centrally; attendees should not independently install it into unrelated personal or company organizations. See the [Endor Labs GitHub App documentation](https://docs.endorlabs.com/setup-deployment/scm-integrations/github-app/).

### 2. Install local prerequisites

Required on your laptop:

| Requirement | Notes |
| --- | --- |
| Git | Clone and push lab repositories |
| Docker Desktop with Docker Compose v2 | Preferred way to run lab apps |
| Modern browser | Endor Labs UI and local apps |
| Terminal access | Run clone / compose commands |
| At least 8 GB available memory | Lab apps and supporting services |
| **endorctl** (**optional**) | Only if instructors include CLI exercises — see [Optional: endorctl CLI prep](#optional-endorctl-cli-prep) |

**Node.js and npm** are **not required** if you use the DVWS Node Docker Compose path. They are only needed for a manual DVWS Node setup; that repository documents Node 20.x/22.x and npm 10.x as tested versions. See [DVWS Node setup](https://github.com/snoopysecurity/dvws-node).

### 3. Preflight OWASP VulnerableApp

Run this before the session:

```bash
git clone https://github.com/SasanLabs/VulnerableApp.git
cd VulnerableApp
docker compose pull
docker compose up
```

Confirm that the application loads at:

- `http://localhost`
- `http://localhost/mailpit/` for the local Mailpit interface

The repository documents Docker Compose as the simplest way to run the full application. It includes intentionally vulnerable scenarios such as SQL injection, XSS, SSRF, IDOR, command injection, path traversal, XXE, and authentication weaknesses. See the [VulnerableApp README](https://github.com/SasanLabs/VulnerableApp).

Stop the application after validation:

```bash
docker compose down
```

### 4. Preflight DVWS Node

Use DVWS Node as a web-service/API lab application when your session includes it:

```bash
git clone https://github.com/snoopysecurity/dvws-node.git
cd dvws-node
docker compose up
```

Confirm that the application/API starts successfully. DVWS Node uses Docker Compose to start the vulnerable service plus MySQL and MongoDB. It includes API and web-service vulnerabilities such as IDOR, access-control issues, SSRF, JWT weaknesses, SQL injection, XXE, command injection, GraphQL issues, and path traversal. See the [DVWS Node README](https://github.com/snoopysecurity/dvws-node).

The two lab applications may attempt to use overlapping ports. Validate them one at a time unless organizers provide port mappings or a hosted lab environment.

### 5. Verify Endor Labs access

Before the session, you should be able to:

- Log in to the Endor Labs tenant provided for the certification.
- Open the assigned namespace.
- See the lab repository as an Endor Labs project after the organizer’s GitHub App installation (when that model is used).
- View at least one completed scan and its findings.
- Open a finding and review its dependency, code, secret, or workflow context.

Organizers typically create a dedicated child namespace for the lab GitHub integration, install the current GitHub Cloud App Pro, select only the lab repositories, enable the required scanners, and manually trigger a scan before the session. Endor Labs’ scheduled scans run periodically, so organizers should not rely on the scheduled cycle immediately before the session. See [GitHub Cloud App Pro setup](https://docs.endorlabs.com/setup-deployment/scm-integrations/github-app).

### 6. Complete one PR trigger test

Be ready to:

1. Create a branch in the assigned repository.
2. Make a harmless documentation or comment change.
3. Open a pull request.
4. Confirm that the Endor Labs PR scan or result becomes visible.
5. Locate the finding or policy result in the pull request or Endor Labs UI.

If the session includes CI scanning, organizers should provide the preconfigured GitHub Actions workflow. Endor Labs supports dependency and secrets scans, PR comments, SARIF output, and container scanning through its GitHub Action. See [GitHub Actions integration](https://docs.endorlabs.com/setup-deployment/ci-cd/scan-with-github-actions/).

---

## Optional: endorctl CLI prep

The steps above cover UI- and GitHub App–based lab prep. Installing and authenticating the **endorctl** CLI is **optional** unless your instructors explicitly include CLI scans or exercises. Skip this section if your session is UI-only.

When a session does use the CLI, complete the following before the lab:

### Install endorctl

Install with one of the supported methods, then confirm the binary is available:

```bash
# macOS / Linux (Homebrew)
brew install endorlabs/tap/endorctl

# macOS / Linux / Windows (npm)
npm install -g endorctl

# Then verify
endorctl --version
```

You can also download the platform binary directly (Linux, macOS, Windows) from the Endor Labs API download endpoints. For EU tenants, use `https://api.eu.endorlabs.com` instead of `https://api.endorlabs.com`. Full install options are in the [endorctl CLI documentation](https://docs.endorlabs.com/setup-deployment/cli).

### Authenticate

Authenticate with the same identity provider or credentials your instructors provide for the Endor Labs tenant. Interactive login via `endorctl init` is the usual workstation path:

```bash
# Examples — use the auth mode your session uses
endorctl init --auth-mode=github
# or: google | gitlab | sso (SSO also needs --auth-tenant=<tenant>)
```

Alternatively, if instructors provide API credentials, set:

```bash
export ENDOR_API_CREDENTIALS_KEY=<api-key>
export ENDOR_API_CREDENTIALS_SECRET=<api-key-secret>
export ENDOR_NAMESPACE=<tenant-namespace>
```

Do not use personal or production API keys for certification labs. Prefer credentials or scopes provided for the session namespace.

### Configure and verify

Confirm you are targeting the assigned namespace and that authentication works:

```bash
# Optional: persist namespace (and other CLI settings) for later commands
echo "ENDOR_NAMESPACE: <tenant-namespace>" >> ~/.endorctl/config.yaml

# Verify auth / access (empty JSON is OK if the namespace has no projects yet)
endorctl api list -r Project --page-size=1
```

If your instructors ask you to run a local CLI scan during the session, wait for their exact `endorctl scan` flags and repository path—do not invent scan targets against production or unrelated repositories.

---

## Safety requirements

- Run lab applications locally or in the isolated session environment only.
- Never expose either application to the public internet.
- Do not use real credentials, API keys, customer data, or production repositories.
- Do not copy vulnerable code or intentionally exposed secrets into personal or company projects outside the lab.
- Keep Docker services running only for the duration of the exercise, then run `docker compose down`.
- Treat all findings and exploit paths as training content, not as permission to attack any external system.

DVWS Node is intentionally vulnerable training software and must remain isolated from production and the public internet. See the [DVWS Node README](https://github.com/snoopysecurity/dvws-node).

---

## Definition of ready

You are ready for the certification lab when you can:

- Start at least one vulnerable application locally (or use a hosted lab provided by instructors).
- Access the assigned GitHub repository and open a pull request.
- Sign in to the Endor Labs namespace.
- See a completed Endor Labs scan for the lab repository.
- Navigate from a finding to its evidence and recommended next step.

---

## Organizer readiness checklist

For instructors and partner engineering preparing a certification or workshop session, complete the following before attendee invitations go out:

- Pin tested commits or release versions for lab repositories.
- When using DVWS, use `dvws-node` as the DVWS implementation.
- Create a dedicated GitHub organization or workshop repository space.
- Install the Endor Labs GitHub Cloud App Pro centrally and scope it only to lab repositories.
- Create a dedicated Endor Labs child namespace for the session.
- Run baseline scans for lab repositories and confirm findings are populated.
- Configure PR scans and test at least one pull request per repository.
- Confirm the namespace, App installation, and repository permissions with a non-admin test account.
- Provide a fallback hosted lab or pre-scanned screenshots for attendees who cannot run Docker locally.
- Prepare a reset procedure and a support contact for GitHub, Docker, and Endor Labs access issues.

---

## Source documentation

- [OWASP VulnerableApp](https://github.com/SasanLabs/VulnerableApp)
- [DVWS Node](https://github.com/snoopysecurity/dvws-node)
- [Endor Labs GitHub App](https://docs.endorlabs.com/setup-deployment/scm-integrations/github-app/)
- [Endor Labs GitHub Cloud App Pro](https://docs.endorlabs.com/setup-deployment/scm-integrations/github-app)
- [Endor Labs GitHub Actions integration](https://docs.endorlabs.com/setup-deployment/ci-cd/scan-with-github-actions/)
- [endorctl CLI](https://docs.endorlabs.com/setup-deployment/cli)
