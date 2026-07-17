# E-Commerce API QA Automation

[![API Tests with Newman](https://github.com/RadovanovicSasha/ecommerce-api-qa-automation/actions/workflows/api-tests.yml/badge.svg)](https://github.com/RadovanovicSasha/ecommerce-api-qa-automation/actions/workflows/api-tests.yml)

A Postman/Newman API test automation suite that validates the product management endpoints of the [DummyJSON](https://dummyjson.com/docs/products) e-commerce API. The suite runs locally via Newman and automatically on every push, pull request, and manual trigger through GitHub Actions.

## Scope

- Create, read, update, delete, and list products
- A chained, end-to-end product lifecycle flow
- Negative scenarios: not-found resources, empty body, malformed JSON, invalid endpoint
- Response status, body, header, schema, and response-time assertions
- Search and pagination utilities

## Tech stack

- [Postman](https://www.postman.com/) — collection authoring
- [Newman](https://github.com/postmanlabs/newman) — CLI test execution
- [newman-reporter-htmlextra](https://github.com/DannyDainton/newman-reporter-htmlextra) — HTML test report
- [GitHub Actions](https://github.com/features/actions) — CI pipeline

## Project structure

```
.
├── E-Commerce API QA Automation.postman_collection.json   # test collection
├── E-Commerce QA Environment.postman_environment.json     # environment (baseUrl, variables)
├── package.json                                            # npm scripts and Newman dependencies
├── .github/workflows/api-tests.yml                         # CI pipeline
└── reports/                                                 # generated HTML report (git-ignored)
```

## Collection structure

| Folder | Purpose |
|---|---|
| `01 - Product CRUD` | Independent create, read, update, delete, and list requests, each verifying status code, response body, and relevant headers/schema |
| `02 - Flow - Full Product Lifecycle` | A single chained scenario: create → read → update → delete, sharing state via environment variables |
| `03 - Negative Tests` | Not-found resources, an empty request body, a malformed JSON body, and an invalid endpoint |
| `04 - Utilities` | Health check, search, and pagination requests |

## Getting started

### Prerequisites

- [Node.js](https://nodejs.org/) 18+ and npm
- Optionally, the [Postman](https://www.postman.com/downloads/) desktop app for manual exploration

### Install dependencies

```bash
npm install
```

### Run the tests

```bash
npm test
```

### Run the tests and generate an HTML report

```bash
npm run test:report
```

The report is written to `reports/report.html` (git-ignored; also published as a downloadable artifact on every CI run).

### Run with Postman

Import both `E-Commerce API QA Automation.postman_collection.json` and `E-Commerce QA Environment.postman_environment.json` into Postman, select the environment, and run the collection with the Collection Runner.

## Continuous integration

Every push to `main`, every pull request targeting `main`, and manual runs (`workflow_dispatch`) trigger [`.github/workflows/api-tests.yml`](.github/workflows/api-tests.yml), which installs dependencies, runs the full collection with Newman, and uploads the HTML report as a build artifact.

## Known limitations

The suite runs against [dummyjson.com](https://dummyjson.com), a public mock API:

- Created, updated, and deleted resources are **not persisted** — the underlying dataset never changes. For this reason, requests that read back a resource target a known, stable product ID (`1`) rather than an ID generated in a prior step.
- Where a scenario chains a dynamically generated ID (`productId`, `flowProductId`), a dedicated assertion verifies that the value was captured correctly, since using it directly in a follow-up read would return `404` against this API.
- The API does not validate request payloads (e.g. an empty body still returns `201 Created`); this is documented and asserted as real, verified API behavior rather than treated as a defect.
- No authentication is required for the endpoints under test, so the suite does not exercise auth flows.

## License

Distributed under the [MIT License](LICENSE).
