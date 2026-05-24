# Secure Notes API

A production-grade Notes API in Go, built to demonstrate DevSecOps practices from first commit to Kubernetes deployment. Every layer is secured, every decision is explained, and every tool is real.

[![CI](https://github.com/philaturo/secure-notes-api/actions/workflows/pipeline.yml/badge.svg)](https://github.com/philaturo/secure-notes-api/actions/workflows/pipeline.yml)
[![Go Report Card](https://goreportcard.com/badge/github.com/your-username/secure-notes-api)](https://goreportcard.com/report/github.com/your-username/secure-notes-api)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## What This Is

This is a reference implementation, not a tutorial. It demonstrates:

- **Secure development practices:** commit signing, pre-commit hooks, secure code review
- **Authentication:** JWT-based auth with bcrypt password hashing
- **Authorization:** IDOR protection, ownership enforcement at the database layer
- **Infrastructure:** hardened Docker images, Kubernetes deployment with Vault secrets injection
- **Observability:** structured logging, Prometheus metrics, security alerting
- **Testing:** unit tests, security regression tests, race detection
- **CI/CD:** SAST, SCA, container scanning, artifact signing

Built as part of the DevSecOps specialization track at [Zone01 Kisumu](https://zone01kisumu.ke).

---

## Architecture

```bash
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Ingress   │────▶│  Auth svc   │────▶│  PostgreSQL │
│  (nginx)    │     │   (:8081)   │     │             │
└─────────────┘     └─────────────┘     └─────────────┘
│
└────────────▶┌─────────────┐
│  Notes API  │────▶│  PostgreSQL │
│   (:8080)   │     │             │
└─────────────┘     └─────────────┘
│
▼
┌─────────────┐
│ HashiCorp   │
│    Vault    │
└─────────────┘

```

---

## Quick Start

### Prerequisites

- Go 1.22+
- Docker & Docker Compose
- kubectl & a local Kubernetes cluster (k3d, minikube, or kind)
- HashiCorp Vault CLI

### Local Development

```bash
# Clone and enter
git clone https://github.com/your-username/secure-notes-api.git
cd secure-notes-api

# Start dependencies (Postgres, Vault)
docker-compose up -d

# Run tests
go test -race ./...

# Start services
go run ./cmd/auth
go run ./cmd/api

```

## API Endpoints

| Method | Endpoint      | Auth | Description               |
| ------ | ------------- | ---- | ------------------------- |
| POST   | `/register`   | No   | Create account            |
| POST   | `/login`      | No   | Authenticate, receive JWT |
| GET    | `/notes`      | Yes  | List your notes           |
| POST   | `/notes`      | Yes  | Create a note             |
| GET    | `/notes/{id}` | Yes  | Get a specific note       |
| PUT    | `/notes/{id}` | Yes  | Update a note             |
| DELETE | `/notes/{id}` | Yes  | Delete a note             |

## Security Features

| Layer       | Control                  | Implementation                    |
| ----------- | ------------------------ | --------------------------------- |
| Development | Secret scanning          | GitLeaks pre-commit hooks         |
| Development | Commit verification      | GPG signing required              |
| Auth        | Password storage         | bcrypt cost 12                    |
| Auth        | Token expiry             | 15-minute JWT lifetime            |
| Auth        | Algorithm verification   | HS256 only, blocks `alg: none`    |
| API         | IDOR prevention          | `AND user_id = $N` on every query |
| API         | SQL injection prevention | Parameterized queries only        |
| API         | Input validation         | Length limits, type checking      |
| Container   | Minimal attack surface   | `scratch` base image, no shell    |
| Container   | Non-root execution       | UID 65532                         |
| Container   | Immutable filesystem     | `readOnlyRootFilesystem: true`    |
| Kubernetes  | Secret management        | Vault agent sidecar injection     |
| Kubernetes  | Network isolation        | Default-deny network policies     |
| Kubernetes  | Least privilege          | RBAC with minimal permissions     |
| CI/CD       | Static analysis          | Semgrep, go vet, staticcheck      |
| CI/CD       | Dependency scanning      | govulncheck, Nancy                |
| CI/CD       | Container scanning       | Trivy (CRITICAL/HIGH fail build)  |
| CI/CD       | Artifact integrity       | Cosign keyless signing            |

## Project Structure

```bash
secure-notes-api/
├── .github/
│   └── workflows/
│       └── pipeline.yml          # CI/CD with security gates
├── cmd/
│   ├── auth/
│   │   └── main.go               # Auth service entrypoint
│   └── api/
│       └── main.go               # Notes API entrypoint
├── docker/
│   ├── Dockerfile.auth            # Hardened auth container
│   └── Dockerfile.api             # Hardened API container
├── internal/
│   ├── auth/
│   │   ├── handler.go             # Login/register endpoints
│   │   ├── jwt.go                 # Token generation/validation
│   │   └── handler_test.go        # Auth tests
│   ├── middleware/
│   │   └── auth.go                # JWT validation middleware
│   ├── notes/
│   │   ├── handler.go             # CRUD endpoints
│   │   ├── repository.go          # Database queries
│   │   └── repository_test.go     # IDOR/security tests
│   ├── telemetry/
│   │   ├── logger.go              # Structured JSON logging
│   │   └── metrics.go             # Prometheus metrics
│   └── vault/
│       └── client.go              # HashiCorp Vault integration
├── k8s/                           # Kubernetes manifests
│   ├── namespace.yaml
│   ├── rbac.yaml
│   ├── network-policy.yaml
│   ├── auth-deployment.yaml
│   ├── api-deployment.yaml
│   └── ...
├── migrations/
│   └── 001_init.sql               # Database schema
├── security-tests/                # DAST configs
│   ├── zap/
│   └── nuclei/
├── docker-compose.yml             # Local dev environment
├── go.mod
├── go.sum
├── .pre-commit-config.yaml
├── .gitignore
└── LICENSE

```

## Dev.to Series

This repository accompanies a Dev.to series on the Modern DevSecOps Engineering Stack:

- Part 1: Development Foundations — commit signing, pre-commit hooks, secure code review
- Part 2: CI/CD Pipeline Security — least privilege, artifact signing, environment isolation
- Part 3: Static Analysis (SAST) — Semgrep, CodeQL, custom rule writing
- Part 4: Dynamic Analysis (DAST) — OWASP ZAP, Nuclei, API fuzzing
- Part 5: Container & Kubernetes Security — hardened images, Vault injection, network policies
- Part 6: Observability & Incident Response — metrics, alerting, runbooks

## Contributing

This is a learning project. Issues and PRs welcome, especially:

- Security improvements
- Additional test coverage
- Documentation clarifications
- Alternative implementations
- Please sign your commits (git commit -S) and ensure pre-commit hooks pass.

## License

MIT License — see LICENSE for details.

## Connect

Built with intent.Secured by design.

---

## LICENSE File (MIT)

```text
MIT License

Copyright (c) 2026 Phil Aturo

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

```
