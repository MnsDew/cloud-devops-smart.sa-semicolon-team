# Cloud DevOps Backend API — Smart.sa Internship

A containerized Task Management REST API developed as part of a software engineering internship at **Smart.sa**, working with the **Semicolon team**. The project demonstrates backend engineering practices and the deployment of a Dockerized application to Oracle Cloud Infrastructure (OCI), with automated CI/CD, PostgreSQL persistence, Nginx reverse proxying, and HTTPS.

**Author:** Mansoor Gabali   
**Team:** Semicolon  
**Company:** Smart.sa  
**Project period:** 10/2026  
**Repository:** [cloud-devops-smart.sa-semicolon-team](https://github.com/MnsDew/cloud-devops-smart.sa-semicolon-team)  
**GitHub:** [MnsDew](https://github.com/MnsDew)  
**Live health endpoint:** [https://smartsa-staj.mns70.com/health](https://smartsa-staj.mns70.com/health)

## Project Overview

The project provides a RESTful task management API and a practical environment for learning and applying backend deployment and operations practices.

The application is containerized with Docker and Docker Compose. PostgreSQL provides persistent data storage, Prisma manages database access and migrations, and Nginx acts as the public-facing reverse proxy. The application is hosted on OCI, with Cloudflare handling DNS and the public HTTPS connection.

The repository also includes automated checks through GitHub Actions and a deployment workflow intended to update the OCI application after successful validation.

## Technology Stack

| Area | Technology |
|---|---|
| Runtime | Node.js 20 LTS |
| Language | TypeScript with strict checking |
| Backend | Express |
| Database | PostgreSQL |
| ORM and migrations | Prisma |
| Validation | Zod |
| Logging | Pino |
| Testing | Vitest and Supertest |
| Containerization | Docker and Docker Compose |
| CI/CD | GitHub Actions |
| Cloud infrastructure | Oracle Cloud Infrastructure (OCI) |
| Reverse proxy | Nginx |
| DNS and HTTPS | Cloudflare |

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/health` | Liveness check |
| `GET` | `/health/ready` | Readiness check, including database connectivity |
| `GET` | `/api/v1/tasks` | List tasks with pagination |
| `GET` | `/api/v1/tasks/:id` | Retrieve a task |
| `POST` | `/api/v1/tasks` | Create a task |
| `PATCH` | `/api/v1/tasks/:id` | Update a task |
| `DELETE` | `/api/v1/tasks/:id` | Delete a task |

## Production Deployment

**Hosting:** Oracle Cloud Infrastructure (OCI)  
**Operating system:** Oracle Linux 9  
**Application runtime:** Docker containers managed with Docker Compose  
**Database:** PostgreSQL with persistent Docker volume storage  
**Reverse proxy:** Nginx  
**Public DNS and HTTPS:** Cloudflare  
**Application domain:** `smartsa-staj.mns70.com`

### Request flow

```text
Client
  |
  v
Cloudflare DNS / HTTPS proxy
  |
  v
OCI Virtual Machine
  |
  v
Nginx (ports 80 and 443)
  |
  v
Backend API (127.0.0.1:3000)
  |
  v
PostgreSQL (Docker network)
```

Nginx redirects HTTP requests to HTTPS and forwards application requests to the backend. The backend is bound to the VM's loopback interface, and PostgreSQL is not exposed through a public host port. Persistent volume storage keeps database data separate from the container lifecycle.

### Live verification

The public liveness endpoint is:

[https://smartsa-staj.mns70.com/health](https://smartsa-staj.mns70.com/health)

The endpoint has been verified to return HTTP `200 OK` with a JSON response indicating that the service is running. This confirms public HTTPS reachability and application liveness; database readiness is checked separately through `/health/ready`.

## Continuous Integration and Deployment

The repository uses GitHub Actions to automate application validation. The CI workflow includes:

1. Checking out the repository.
2. Installing dependencies with `npm ci`.
3. Running linting and TypeScript checks.
4. Applying Prisma migrations against a test PostgreSQL service.
5. Running automated tests.
6. Building the application.

The deployment job is configured to run after successful CI on the `master` branch. It connects to the OCI virtual machine over SSH, updates the repository, rebuilds and restarts the API through Docker Compose, and verifies the local health endpoint.

Production SSH credentials are stored in GitHub Actions secrets rather than committed to the repository. The deployment pipeline should be considered fully verified only after a successful workflow run confirms both CI and deployment.

## Local Development

### Prerequisites

- Node.js 20
- npm
- Docker Engine or Docker Desktop with Docker Compose
- Git

### Setup on Windows CMD

Clone the repository and enter its directory:

```cmd
git clone https://github.com/MnsDew/cloud-devops-smart.sa-semicolon-team.git
cd cloud-devops-smart.sa-semicolon-team
```

Create a local environment file and install dependencies:

```cmd
copy .env.example .env
npm ci
```

Start the PostgreSQL service:

```cmd
docker compose up -d db
```

Apply database migrations and start the development server:

```cmd
npm run prisma:migrate:deploy
npm run dev
```

The local API is available at:

`http://localhost:3000`

The local Compose setup maps PostgreSQL to host port `55432` by default to avoid conflicts with a locally installed PostgreSQL server. The database container itself uses port `5432`.

For the full containerized setup, use:

```cmd
docker compose up --build
```

Configure `.env` using `.env.example`. Do not commit the actual `.env` file or production secrets.

## Common Development Commands

```cmd
npm run lint
npm run typecheck
npm test
npm run build
```

To inspect running containers:

```cmd
docker compose ps
```

To inspect application logs:

```cmd
docker compose logs -f api
```

To stop the local services:

```cmd
docker compose down
```

Stopping the services this way does not remove the named database volume. Avoid using volume-removal options unless you intentionally want to delete local database data.

## Engineering and Security Practices

- **Type safety:** TypeScript strict mode helps catch errors before runtime.
- **Input validation:** Zod validates environment configuration and request payloads.
- **Database migrations:** Prisma migrations provide a controlled schema evolution process.
- **Structured logging:** Pino supports machine-readable application logs.
- **HTTP security:** Helmet applies security-related HTTP headers.
- **CORS configuration:** Allowed origins are configured through environment variables.
- **Request limits:** Request body size is restricted.
- **Error handling:** Production error responses avoid exposing stack traces.
- **Secret management:** Environment files and SSH private keys are kept out of source control.
- **Network isolation:** The backend listens on loopback behind Nginx, and PostgreSQL is not publicly exposed through a host port.
- **Persistent storage:** PostgreSQL data is stored in a named Docker volume.

## Documentation

- [Project plan](docs/PROJECT_PLAN.md)
- [Architecture](docs/ARCHITECTURE.md)
- [Development log](docs/DEVELOPMENT_LOG.md)
- [Testing](docs/TESTING.md)
- [Deployment guide](docs/DEPLOYMENT.md)
- [Learning notes](docs/LEARNING_NOTES.md)
- [Contributing guidelines](CONTRIBUTING.md)

## Scope and Future Improvements

Potential future improvements include:

- Automated database backups and a documented restore test.
- Monitoring and alerting for service availability and resource usage.
- SSH host-key pinning for the deployment workflow.
- A documented rollback procedure for failed releases.
- Deployment notifications and clearer release history.
- Additional integration tests for database failures and readiness checks.

These are follow-up improvements, not claims that the features are already implemented.

## License

Copyright 2026 Mansoor Gabali (GitHub: [MnsDew](https://github.com/MnsDew)).

Licensed under the [Apache License, Version 2.0](LICENSE).

You may use, copy, modify, and redistribute this project under the terms of Apache-2.0. Redistributions must preserve the required copyright and attribution notices and include the license and applicable `NOTICE` file. See [LICENSE](LICENSE) and [NOTICE](NOTICE) for details.
