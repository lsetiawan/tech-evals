# tech-evals

Don's repository for evaluating various technologies and software before using them in projects.

## Overview

This repository contains Docker Compose files and local configuration for evaluating different technologies. Each technology evaluation is organized in its own directory with Pixi for environment management.

## Structure

```
tech-evals/
├── pixi.toml              # Root-level Pixi configuration
├── .gitignore             # Git ignore patterns
├── README.md              # This file
└── <tech-name>/           # Individual technology evaluation directories
    ├── pixi.toml          # Tech-specific Pixi configuration
    ├── docker-compose.yml # Docker Compose setup
    ├── .env.example       # Example environment variables
    └── README.md          # Tech-specific documentation
```

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) and Docker Compose
- [Pixi](https://pixi.sh/) - for environment management (optional but recommended)

## Getting Started

### Installing Pixi (Optional)

```bash
curl -fsSL https://pixi.sh/install.sh | bash
```

### Using This Repository

1. Navigate to the technology directory you want to evaluate:
   ```bash
   cd <tech-name>
   ```

2. Copy the example environment file and configure it:
   ```bash
   cp .env.example .env
   # Edit .env with your configuration
   ```

3. Start the services using Docker Compose:
   ```bash
   docker-compose up -d
   ```

4. Follow the technology-specific README for additional instructions.

## Available Evaluations

- [LiteLLM](./litellm/README.md) - Unified API for 100+ LLMs with load balancing, fallbacks, and cost tracking

## Adding New Evaluations

To add a new technology evaluation:

1. Create a new directory with the technology name
2. Add a `pixi.toml` file for environment management
3. Add a `docker-compose.yml` file with the necessary services
4. Create a `.env.example` file with required environment variables
5. Write a `README.md` with evaluation-specific instructions
6. Update this main README to list the new evaluation

## License

This repository is for personal evaluation purposes.
