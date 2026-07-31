# Vitrin

Generates AI product marketing posters for small businesses. Add a product, the system
generates a poster, you approve it and download it.

Generation takes 20–60 seconds, so it runs asynchronously through a job queue: the API accepts
the request and returns immediately, a separate worker process does the work.

## Requirements

- .NET 10 SDK — the exact version is pinned in `global.json`
- Docker and Docker Compose — for Postgres and the other dependencies

## Running locally

Dependencies run in Docker. .NET runs on the host.

```bash
cp .env.example .env          # then fill in the secrets
docker compose up -d          # Postgres, etc.

dotnet run --project src/Vitrin.Api       # HTTP API
dotnet run --project src/Vitrin.Worker    # queue consumer — separate process
```

Both processes are needed. The API alone will accept requests and never complete them.

## Tests

```bash
dotnet test
```

Unit tests need nothing. Integration tests start their own Postgres container.

## Architecture

Four layers as five projects, wired by hand. Dependencies point inward; `Vitrin.Domain` has none.

```
src/Vitrin.Domain           entities, no dependencies of any kind
src/Vitrin.Application      use cases, declares the interfaces
src/Vitrin.Infrastructure   implements them — Postgres, storage, image generation
src/Vitrin.Api              HTTP delivery
src/Vitrin.Worker           queue consumer
```

- `docs/architecture.html` — the layer map and request flow
- `docs/rings.html` — the dependency rules and why the graph looks like this
- `docs/setup.html` — how this repository was built, step by step
- `CLAUDE.md` — project conventions and the deliberate non-choices
