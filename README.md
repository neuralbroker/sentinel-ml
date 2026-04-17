# SentinelML

SentinelML is a fraud detection API. It takes transaction data as input and returns a fraud score between 0 and 1. It uses a Random Forest model trained on synthetic transaction data. The API is built with FastAPI and supports single predictions, batch predictions, and health checks.

## Tech Stack

- Python 3.11
- FastAPI (web framework)
- scikit-learn (machine learning)
- Redis (caching)
- PostgreSQL (database)
- MLflow (experiment tracking)
- Docker (containerization)

## Features

- Single and batch transaction fraud prediction
- REST API with JSON input/output
- Redis caching for repeated requests
- Async database logging
- Drift detection for model monitoring
- MLflow experiment tracking
- Web dashboard for testing predictions
- 22 unit tests

## Prerequisites

- Python 3.11 or higher
- Redis server (optional, for caching)
- PostgreSQL database (optional, for logging)
- MLflow server (optional, for tracking)

## Installation

Clone the repository:
```bash
git clone https://github.com/neuralbroker/sentinel-ml.git
cd sentinel-ml
```

Install dependencies:
```bash
pip install -r requirements.txt
```

Copy the environment file:
```bash
cp .env.example .env
```

## Usage

Start the API server:
```bash
python -m uvicorn sentinel_ml.api.main:app --host 127.0.0.1 --port 8000
```

Open the web dashboard at: http://localhost:8000

### API Endpoints

**Health Check**
```bash
curl http://localhost:8000/health
```

**Single Prediction**
```bash
curl -X POST http://localhost:8000/api/v1/predict -H 'Content-Type: application/json' -d '{
  \"user_id\": \"user_123\",
  \"amount\": 1500.00,
  \"merchant_id\": \"merchant_001\",
  \"merchant_category\": \"online_shopping\",
  \"transaction_type\": \"purchase\",
  \"device_type\": \"mobile\",
  \"location_country\": \"US\"
}'
```

**Response:**
```json
{
  \"transaction_id\": \"abc-123\",
  \"fraud_score\": 0.45,
  \"prediction\": false,
  \"confidence\": \"low\",
  \"model_version\": \"v20260410_092544\",
  \"inference_time_ms\": 65.0,
  \"cached\": false
}
```

**Batch Prediction**
```bash
curl -X POST http://localhost:8000/api/v1/predict/batch -H 'Content-Type: application/json' -d '{
  \"transactions\": [
    {\"user_id\": \"user_1\", \"amount\": 50, \"merchant_id\": \"m1\", \"merchant_category\": \"grocery\", \"transaction_type\": \"purchase\"},
    {\"user_id\": \"user_2\", \"amount\": 5000, \"merchant_id\": \"m2\", \"merchant_category\": \"electronics\", \"transaction_type\": \"purchase\"}
  ]
}'
```

### Training a New Model

```bash
python scripts/train_model.py
```

The trained model is saved in the artifacts/ directory.

### Running Tests

```bash
pytest sentinel_ml/tests/ -v
```

### Docker Deployment

Start the full stack:
```bash
docker-compose up -d
```

Services available at:
- API: http://localhost:8000
- MLflow UI: http://localhost:5000
- Grafana: http://localhost:3000

## Project Structure

```
sentinel_ml/
  api/            - FastAPI application and endpoints
  config/         - Configuration settings
  core/           - Database, cache, and logging utilities
  data/           - Synthetic data generation
  features/       - Feature engineering pipeline
  models/         - Model training and evaluation
  monitoring/     - Drift detection and alerting
  pipelines/      - Training and retraining pipelines
  tests/          - Unit tests
  dashboard/      - Web UI HTML
scripts/
  train_model.py  - Training script
  load_test.py    - Load testing script
artifacts/        - Saved models and feature engineers
```

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| DB_HOST | PostgreSQL host | localhost |
| DB_PORT | PostgreSQL port | 5432 |
| DB_NAME | Database name | sentinel_ml |
| DB_USER | Database user | postgres |
| DB_PASSWORD | Database password | postgres |
| REDIS_HOST | Redis host | localhost |
| REDIS_PORT | Redis port | 6379 |
| MLFLOW_TRACKING_URI | MLflow server URL | http://localhost:5000 |
| API_HOST | API bind host | 0.0.0.0 |
| API_PORT | API bind port | 8000 |
| API_WORKERS | Number of workers | 4 |
| LOG_LEVEL | Logging level | INFO |
| ENVIRONMENT | Environment name | development |

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Run tests: pytest sentinel_ml/tests/
5. Submit a pull request

## License

MIT License
