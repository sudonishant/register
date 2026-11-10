<p align="center">
  <img height="350" alt="is-a.dev banner" src="https://raw.githubusercontent.com/is-a-dev/register/main/media/banner.png">
</p>

<h1 align="center">is-a.dev domain registry</h1>

<p align="center">A public registry of <code>.is-a.dev</code> subdomains for software developers' personal websites.</p>

<p align="center"><a href="https://docs.is-a.dev">Documentation</a> · <a href="https://is-a.dev/terms">Terms of Service</a> · <a href="https://discord.gg/is-a-dev-830872854677422150">Discord</a></p>

## What this repository does

This repository stores one JSON file per registered subdomain in [`domains/`](domains/). [`dnsconfig.js`](dnsconfig.js) reads those files and builds the `is-a.dev` DNS zone for DNSControl. GitHub Actions validate proposed changes, publish DNS changes after they reach `main`, and generate the raw registry data used by the service.

It is **not** a website hosting platform, registrar, or local DNS server. A merged record points a name at infrastructure you provide; it does not deploy or operate that infrastructure for you.

## Request a subdomain

The public request process and complete schema are maintained in the [is-a.dev documentation](https://docs.is-a.dev/). In brief:

1. Read the [Terms of Service](https://is-a.dev/terms) and [domain structure guide](https://docs.is-a.dev/domain-structure/).
2. Fork this repository and add your domain file under `domains/` using the documented filename and JSON structure.
3. Open a pull request using the repository template. Include a reachable, completed, software-development-related website and the requested ownership/contact details.
4. Respond to review feedback. A merge is required before the record is published; DNS and caches may take time to update afterward.

The automated checks validate filenames, ownership fields, record types and values, nested-domain ownership, proxy compatibility, and the required pull-request template. Passing checks does not replace maintainer review or the Terms of Service.

> **Important:** This project does not accept AI-generated domain-registration pull requests. Read [`AGENTS.md`](AGENTS.md) before contributing. Do not put passwords, API keys, or other secrets in a domain file; registry contents and owner contact fields are public.

## Work locally

The test suite is Node.js-based. From a fresh checkout:

```sh
git clone https://github.com/is-a-dev/register.git
cd register
npm ci
npm test
```

Useful focused checks are also available:

```sh
npm run check:reserved-order
npx prettier --check README.md dnsconfig.js tests util
```

`npm test` runs the AVA tests in `tests/*.test.js`. The test suite reads the complete `domains/` directory, so run it from the repository root with the registry data present. The DNSControl workflow uses DNSControl 4.14.0 to check `dnsconfig.js`; the local Node test commands do not publish DNS changes.

## Domain file shape

Use the documentation for the authoritative schema and allowed values. A minimal **illustrative** record looks like this (replace the placeholder values with your own, and do not submit this example unchanged):

```json
{
  "owner": {
    "username": "your-github-username"
  },
  "records": {
    "CNAME": "your-site.example.com"
  }
}
```

Supported record handling in `dnsconfig.js` includes `A`, `AAAA`, `CAA`, `CNAME`, `DS`, `MX`, `NS`, `SRV`, `TLSA`, `TXT`, and `URL`. The validators apply additional restrictions, including public-address checks, hostname validation, reserved/internal-name checks, and safeguards around proxying and self-references. Follow the [domain structure documentation](https://docs.is-a.dev/domain-structure/) rather than inferring a valid submission from this short example.

## Architecture and automation

```text
domains/*.json
      │
      ├── tests/*.test.js ──► AVA validation in CI
      │
      └── dnsconfig.js ─────► DNSControl check/push ──► Cloudflare DNS

 domains/*.json ────────────► util/raw-api.js ────────► raw registry workflow
```

- [`tests/`](tests/) contains data, record, proxy, ownership, and pull-request checks.
- [`util/`](util/) contains reserved-name management, raw API generation, and pull-request validation helpers.
- [`.github/workflows/ci.yml`](.github/workflows/ci.yml) runs `npm ci` and AVA; DNSControl checks run when relevant configuration changes.
- [`.github/workflows/publish.yml`](.github/workflows/publish.yml) pushes the zone after an eligible change on `main`, using a repository secret. Contributors should never need that credential locally.

## Abuse reports and service updates

Report suspected abuse through the [report-abuse issue template](https://github.com/is-a-dev/register/issues/new?assignees=&labels=report-abuse&projects=&template=report-abuse.md&title=Report+abuse) with relevant evidence. Service announcements and downtime notices are posted in the [Discord server](https://discord.gg/is-a-dev-830872854677422150). For policy details, see the [Terms of Service](https://is-a.dev/terms).
