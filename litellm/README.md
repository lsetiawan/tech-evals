# LiteLLM Evaluation

LiteLLM is a unified interface for 100+ LLMs with features including load balancing, fallbacks, cost tracking, and observability.

## Overview

This evaluation sets up LiteLLM with:
- **LiteLLM Proxy**: Main service providing unified API access
- **PostgreSQL**: Database for storing model configurations and metrics
- **Prometheus**: Metrics collection and monitoring

## Quick Start

1. **Set up environment variables**:
   ```bash
   cp .env.example .env
   ```
   Edit `.env` and add your API keys for the LLM providers you want to use.

2. **Start the services**:
   ```bash
   # Using docker-compose directly
   docker-compose up -d
   
   # Or using pixi (if installed)
   pixi run up
   ```

3. **Verify services are running**:
   ```bash
   docker-compose ps
   # or
   pixi run status
   ```

4. **Access the services**:
   - LiteLLM UI: http://localhost:4000
   - LiteLLM API: http://localhost:4000/docs (Swagger documentation)
   - Prometheus: http://localhost:9090

## Configuration

### Adding API Keys

Edit the `.env` file to add your API keys. Example:

```bash
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
```

### Using config.yaml (Optional)

For advanced configuration, you can create a `config.yaml` file and uncomment the volume mount and command in `docker-compose.yml`:

```yaml
model_list:
  - model_name: gpt-4
    litellm_params:
      model: openai/gpt-4
      api_key: os.environ/OPENAI_API_KEY
  - model_name: claude-3
    litellm_params:
      model: anthropic/claude-3-opus-20240229
      api_key: os.environ/ANTHROPIC_API_KEY
```

## Usage Examples

### Basic API Call

```bash
curl http://localhost:4000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-3.5-turbo",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

### Using with OpenAI Python SDK

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:4000",
    api_key="any-string"  # Can be any string if no master key is set
)

response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[{"role": "user", "content": "Hello!"}]
)
print(response.choices[0].message.content)
```

## Available Pixi Tasks

If you have Pixi installed, you can use these convenience commands:

- `pixi run up` - Start all services
- `pixi run down` - Stop all services
- `pixi run logs` - View logs
- `pixi run status` - Check service status
- `pixi run restart` - Restart services
- `pixi run clean` - Stop services and remove volumes (⚠️ deletes data)

## Monitoring

### Prometheus Metrics

Access Prometheus at http://localhost:9090 to view metrics including:
- Request rates
- Error rates
- Latency percentiles
- Cost tracking

### Health Checks

Check LiteLLM health:
```bash
curl http://localhost:4000/health/liveliness
```

## Troubleshooting

### Services not starting

1. Check if ports are already in use:
   ```bash
   lsof -i :4000
   lsof -i :5432
   lsof -i :9090
   ```

2. View logs:
   ```bash
   docker-compose logs
   ```

### Database connection issues

Ensure the database is healthy:
```bash
docker-compose exec db pg_isready -U llmproxy -d litellm
```

### Reset everything

To completely reset the evaluation:
```bash
docker-compose down -v
docker-compose up -d
```

## Cleanup

To stop and remove all services:
```bash
docker-compose down

# To also remove volumes and data
docker-compose down -v
```

## Resources

- [LiteLLM Documentation](https://docs.litellm.ai/)
- [LiteLLM GitHub](https://github.com/BerriAI/litellm)
- [API Reference](https://docs.litellm.ai/docs/proxy/endpoints)
- [Supported Models](https://docs.litellm.ai/docs/providers)
