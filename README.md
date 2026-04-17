# SentinelML

Fraud detection API. Random Forest on synthetic transaction data.

## Why

I needed a working example of ML model serving with FastAPI, caching, and basic monitoring. This is it.

## Install

```bash
pip install -r requirements.txt
```

## Run

```bash
python -m uvicorn sentinel_ml.api.main:app --host 127.0.0.1 --port 8000
```

## Use

Single prediction:
```bash
curl -X POST http://localhost:8000/api/v1/predict -H ''Content-Type: application/json'' -d ''{\"user_id\": \"user_123\",\"amount\": 1500.00,\"merchant_id\": \"merchant_001\",\"merchant_category\": \"online_shopping\",\"transaction_type\": \"purchase\",\"device_type\": \"mobile\",\"location_country\": \"US\"}''
```

Batch prediction:
```bash
curl -X POST http://localhost:8000/api/v1/predict/batch -H ''Content-Type: application/json'' -d ''{\"transactions\": [{\"user_id\": \"user_1\", \"amount\": 50, \"merchant_id\": \"m1\", \"merchant_category\": \"grocery\", \"transaction_type\": \"purchase\"},{\"user_id\": \"user_2\", \"amount\": 5000, \"merchant_id\": \"m2\", \"merchant_category\": \"electronics\", \"transaction_type\": \"purchase\"}]}''
```

Health check:
```bash
curl http://localhost:8000/health
```

## Internals

Model: scikit-learn RandomForest, class_weight=''balanced''. Trained on synthetic data with known fraud labels.

Features: 25 features. Temporal (hour, day, weekend). User behavior (txn count, amount avg, velocity). Risk scores (merchant, country). Change detection (device, location).

Caching: Redis, TTL 3600s. Key = transaction_id.

API: FastAPI with async endpoints. Pydantic validation. Background task logging to PostgreSQL.

Training: `python scripts/train_model.py`. Generates synthetic data, trains model, saves to artifacts/.

Tests: `pytest sentinel_ml/tests/ -v`. 22 tests.

## License

MIT
