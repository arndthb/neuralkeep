# Deployment outline

NeuralKeep documents an existing Karakeep deployment rather than distributing an independently verified Compose stack.

1. Install Docker Desktop (or a compatible Docker Engine with Compose).
2. Follow the [official Karakeep installation documentation](https://docs.karakeep.app/).
3. Keep secrets in a local `.env` file that is excluded from Git.
4. Choose a free host port for Karakeep. In the reference homelab the host port is **3002**, forwarded to container port **3000**.
5. Start the upstream application stack and confirm the web UI, crawler and search service are healthy.
6. Import bookmarks in batches and monitor the crawler and import queue. Some sites block crawlers or time out; do not weaken SSRF safeguards to force imports.

> The reference deployment uses Docker Compose services for Karakeep, Chromium and Meilisearch. Configuration may change with upstream releases.

## Operations

Use `docker compose ps` to inspect service health and `docker compose logs --tail=100 web` to inspect recent application activity.

Avoid restarting the application during a large import unless you understand the consequences for in-flight work.

## Backups

Plan a consistent backup of the application's persistent data and configuration before upgrades. A tested backup-and-restore procedure is still on the roadmap.
