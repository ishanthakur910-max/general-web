# Stockroom — Full-Stack Inventory Practice

This project intentionally excludes `docker-compose.yml` and `Jenkinsfile`; write both yourself.

## Architecture
- `frontend/`: static HTML/CSS/JS served by Nginx
- `backend/`: Node.js + Express API
- PostgreSQL database

Use Compose service names: `frontend`, `backend`, `db`.

## Compose requirements
- Frontend build context `./frontend`, publish `80:80`.
- Backend build context `./backend`; environment:
  - `PORT=3000`
  - `DB_HOST=db`
  - `DB_PORT=5432`
  - `DB_NAME=inventorydb`
  - `DB_USER=inventoryuser`
  - `DB_PASSWORD=inventorypass123`
- Database image `postgres:16-alpine`; environment:
  - `POSTGRES_DB=inventorydb`
  - `POSTGRES_USER=inventoryuser`
  - `POSTGRES_PASSWORD=inventorypass123`
- Named volume at `/var/lib/postgresql/data`.
- Add a `pg_isready` health check and wait for the database to be healthy before starting backend.
- Do not publish port 5432 to the public host.
- Nginx already proxies `/api/` to `http://backend:3000`.

## API
- `GET /api/health` — checks database connectivity
- `GET /api/products` — lists products
- `POST /api/products` — adds a product
- `DELETE /api/products/:id` — deletes a product

Example JSON:
`{"name":"Keyboard","quantity":12,"price":24.99}`

## Suggested Jenkins stages
1. Checkout (only if the Jenkins job is configured with SCM)
2. Validate Compose configuration
3. Build images
4. Deploy
5. Verify containers
6. Health check

Useful commands:
```bash
docker compose config -q
docker compose build
docker compose up -d
docker compose ps
curl -fsS http://localhost/api/health
```

`localhost` only works if the application is reachable from the machine/container running the command. If Jenkins runs on a different host from the deployment target, configure remote deployment rather than assuming local commands deploy to EC2.

## Manual tests
```bash
curl -i http://localhost/api/health
curl -i http://localhost/api/products
curl -i -X POST http://localhost/api/products -H 'Content-Type: application/json' -d '{"name":"Keyboard","quantity":12,"price":24.99}'
```

The database schema is created automatically. The named volume preserves data unless you explicitly remove it. The sample password is for practice only.
