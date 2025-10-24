# LiteLLM Evaluation

LiteLLM is a unified interface for 100+ LLMs with features including load balancing, fallbacks, cost tracking, and observability.

## Overview

This evaluation sets up LiteLLM with:
- **LiteLLM Proxy**: Main service providing unified API access with Azure OpenAI endpoints
- **PostgreSQL**: Database for storing model configurations and metrics
- **Prometheus**: Metrics collection and monitoring
- **S3 Logging**: Automatic logging of all requests to S3 for auditing and analysis

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

### Environment Variables

This setup uses a `config.yaml` file for configuration. Copy the `.env.example` to `.env` and configure your credentials:

```bash
cp .env.example .env
# Edit .env with your values
```

Required environment variables:
- **Azure OpenAI**: `AZURE_API_KEY`, `AZURE_API_BASE`, `AZURE_API_VERSION`
- **S3 Logging**: `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `S3_BUCKET_NAME`, `S3_REGION_NAME`, `S3_PATH_PREFIX`

### config.yaml

The included `config.yaml` provides:

**Azure OpenAI Models:**
- `azure-gpt-4` - GPT-4 via Azure OpenAI
- `azure-gpt-35-turbo` - GPT-3.5 Turbo via Azure OpenAI

**S3 Logging:**
- Automatically logs all successful and failed requests to S3
- Logs are stored in the configured S3 bucket with optional path prefix

**Example config.yaml structure:**
```yaml
model_list:
  - model_name: azure-gpt-4
    litellm_params:
      model: azure/gpt-4
      api_base: os.environ/AZURE_API_BASE
      api_key: os.environ/AZURE_API_KEY
      api_version: os.environ/AZURE_API_VERSION

litellm_settings:
  success_callback: ["s3"]
  failure_callback: ["s3"]
  s3_callback_params:
    s3_bucket_name: os.environ/S3_BUCKET_NAME
    s3_region_name: os.environ/S3_REGION_NAME
    s3_aws_access_key_id: os.environ/AWS_ACCESS_KEY_ID
    s3_aws_secret_access_key: os.environ/AWS_SECRET_ACCESS_KEY
    s3_path: os.environ/S3_PATH_PREFIX

general_settings:
  master_key: os.environ/LITELLM_MASTER_KEY
  database_url: os.environ/DATABASE_URL
```

You can modify `config.yaml` to add more models or change logging behavior.

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
