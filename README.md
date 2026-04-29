# hermes-agent-but-docker

Docker Compose wrapper for running [Nous Research's Hermes Agent](https://hub.docker.com/r/nousresearch/hermes-agent) locally.

## Prerequisites

- Docker
- Docker Compose v2 (`docker compose`)
- At least one LLM provider API key (OpenRouter, Anthropic, OpenAI, or a local OpenAI-compatible endpoint)

## Setup

1. Copy the example environment file and fill it in:

   ```sh
   cp .env.example .env
   ```

2. Edit `.env`:
   - Set `DOCKER_ARCH` to `arm64` (Apple Silicon) or `amd64` (Intel/AMD).
   - Provide at least one LLM provider key (e.g. `OPENROUTER_API_KEY`).
   - Add any optional tool, messaging, or GitHub keys you need.

## Usage

```sh
make build   # pull/build the image
make up      # start the agent in detached mode
make logs    # tail logs
make down    # stop containers
make clean   # stop containers and remove volumes
```

To attach an interactive shell to the running agent:

```sh
docker attach hermes-agent
```

## Layout

| Path                 | Purpose                                                           |
| -------------------- | ----------------------------------------------------------------- |
| `docker-compose.yml` | Defines the `hermes-agent` service.                               |
| `.env.example`       | Template for required and optional environment variables.         |
| `.env`               | Local secrets (gitignored).                                       |
| `.hermes-agent/`     | Persistent agent data, mounted into the container at `/opt/data`. |
| `Makefile`           | Convenience wrappers around `docker compose`.                     |

## Environment Variables

See `.env.example` for the full list. Categories:

- **Docker** — `DOCKER_ARCH`
- **LLM providers** — `OPENROUTER_API_KEY`, `ANTHROPIC_API_KEY`, `ANTHROPIC_TOKEN`, `OPENAI_API_KEY`, `CUSTOM_LLM_BASE_URL`, `CUSTOM_LLM_API_KEY`
- **Tools** — `EXA_API_KEY`, `FIRECRAWL_API_KEY`, `FAL_KEY`, `PARALLEL_API_KEY`
- **Messaging gateways** — Telegram, Discord, Slack bot tokens and allowlists
- **GitHub** — `GITHUB_TOKEN`
- **Misc** — `HONCHO_API_KEY`, `BROWSERBASE_API_KEY`, `BROWSERBASE_PROJECT_ID`

## Notes

- The container runs the `gateway` entrypoint (`/opt/hermes/docker/entrypoint.sh gateway`).
- `TERMINAL_ENV=docker` is set inside the container so the agent knows it is running in Docker.
- `restart: unless-stopped` keeps the agent running across reboots until you `make down`.
