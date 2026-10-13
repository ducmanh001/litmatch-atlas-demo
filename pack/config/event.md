# Environment variables EVENT_*

id: config:EVENT
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| EVENT_BUS_KAFKA_REQUEST_TIMEOUT_MS | core-api | tuning | number | 5000 |  | .env.example:100 |  |
| EVENT_BUS_KAFKA_RETRIES | core-api | other | number | 1 |  | .env.example:101 |  |
| EVENT_BUS_CONSUMER_MAX_ATTEMPTS | core-api | tuning | number | 3 |  | .env.example:102 |  |
| EVENT_BUS_CONSUMER_RETRY_DELAY_MS | core-api | tuning | number | 250 |  | .env.example:103 |  |
