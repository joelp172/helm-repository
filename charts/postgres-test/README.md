# PostgreSQL Connectivity Test Helm Chart

A Helm chart for testing connectivity to an external PostgreSQL database. This chart deploys a container that periodically tests the ability to create tables, write data, and read data from a PostgreSQL database.

## Features

- Validates PostgreSQL connectivity
- Tests complete write and read operations
- Supports custom test frequency
- Flexible credential management (direct values or existing secrets)
- Comprehensive logging

## Installation

### Prerequisites

- Kubernetes 1.16+
- Helm 3.0+
- Access to a PostgreSQL database

### Using Helm

# Install the chart with release name "postgres-test"
```bash
# Add the repository
helm repo add joelp172 https://joelp172.github.io/helm-repository/
helm repo update

# Install the chart with release name "postgres-test"
helm install postgres-test joelp172/postgres-test

# Install with custom PostgreSQL credentials
helm install postgres-test ./postgres-test \
  --set postgres.credentials.host=mydb.example.com \
  --set postgres.credentials.port=5432 \
  --set postgres.credentials.username=myuser \
  --set postgres.credentials.password=mypassword \
  --set postgres.credentials.database=mydb
```

### Using an Existing Secret

You can use an existing Kubernetes secret instead of providing credentials directly:

1. Create a secret with your PostgreSQL credentials:

```bash
kubectl create secret generic my-postgres-creds \
  --from-literal=DB_HOST=mydb.example.com \
  --from-literal=DB_PORT=5432 \
  --from-literal=DB_NAME=mydb \
  --from-literal=DB_USER=myuser \
  --from-literal=DB_PASSWORD=mypassword
```

2. Use this secret in your Helm installation:

```bash
helm install postgres-test ./postgres-test \
  --set postgres.existingSecret.name=my-postgres-creds
```

If your secret uses different key names, you can specify those:

```bash
helm install postgres-test ./postgres-test \
  --set postgres.existingSecret.name=my-postgres-creds \
  --set postgres.existingSecret.keys.host=POSTGRES_HOST \
  --set postgres.existingSecret.keys.username=POSTGRES_USER
```

## Configuration

The following table lists the configurable parameters of the chart and their default values:

| Parameter                                  | Description                                              | Default                 |
|-------------------------------------------|----------------------------------------------------------|-------------------------|
| `replicaCount`                            | Number of replicas to run                                | `1`                     |
| `image.repository`                        | Container image repository                               | `python`                |
| `image.tag`                               | Container image tag                                      | `3.9-slim`              |
| `image.pullPolicy`                        | Container image pull policy                              | `IfNotPresent`          |
| `service.type`                            | Service type                                             | `ClusterIP`             |
| `service.port`                            | Service port                                             | `80`                    |
| `resources.requests.cpu`                  | CPU resource request                                     | `100m`                  |
| `resources.requests.memory`               | Memory resource request                                  | `128Mi`                 |
| `resources.limits.cpu`                    | CPU resource limit                                       | `200m`                  |
| `resources.limits.memory`                 | Memory resource limit                                    | `256Mi`                 |
| `postgres.testFrequencySeconds`           | How often to run the connectivity test (in seconds)      | `60`                    |
| `postgres.credentials.host`               | PostgreSQL server hostname                               | `your-postgres-host`    |
| `postgres.credentials.port`               | PostgreSQL server port                                   | `5432`                  |
| `postgres.credentials.database`           | PostgreSQL database name                                 | `postgres`              |
| `postgres.credentials.username`           | PostgreSQL username                                      | `postgres`              |
| `postgres.credentials.password`           | PostgreSQL password                                      | `your-password`         |
| `postgres.existingSecret.name`            | Name of existing secret with PostgreSQL credentials      | `""`                    |
| `postgres.existingSecret.keys.host`       | Key in the existing secret for the host                  | `DB_HOST`               |
| `postgres.existingSecret.keys.port`       | Key in the existing secret for the port                  | `DB_PORT`               |
| `postgres.existingSecret.keys.database`   | Key in the existing secret for the database              | `DB_NAME`               |
| `postgres.existingSecret.keys.username`   | Key in the existing secret for the username              | `DB_USER`               |
| `postgres.existingSecret.keys.password`   | Key in the existing secret for the password              | `DB_PASSWORD`           |

## Monitoring

Check the logs to see the connectivity test results:

```bash
kubectl logs -f deployment/postgres-test-postgres-test
```

Successful logs will show:
```
2023-03-11 10:15:30,123 - postgres-test - INFO - PostgreSQL connectivity test completed successfully!
2023-03-11 10:15:30,124 - postgres-test - INFO - Test cycle completed with status: SUCCESS
```

## Validation

Query the `connectivity_test` table to see the test results:

```
postgres=> SELECT * FROM connectivity_test;

 id |               test_id                |                 message                  |         created_at         
----+--------------------------------------+------------------------------------------+----------------------------
  1 | 6b6b9011-505e-4cef-b34c-650d7615658d | Connectivity test at 2025-03-12 11:19:55 | 2025-03-12 11:19:55.88113
  2 | 76a0c1b4-d49d-4f52-97ff-82f0c8307d4c | Connectivity test at 2025-03-12 11:20:55 | 2025-03-12 11:20:55.970949
  3 | 2c05f38f-7cda-4a9d-818f-2fbb9dbfcd89 | Connectivity test at 2025-03-12 11:21:56 | 2025-03-12 11:21:56.058724
  4 | 0abeead9-d3d5-4fff-81ef-e593b01f3fba | Connectivity test at 2025-03-12 11:27:53 | 2025-03-12 11:27:53.529038
  5 | 142d823a-e4ac-4ef5-8a50-539c8e3d1cc2 | Connectivity test at 2025-03-12 11:28:53 | 2025-03-12 11:28:53.604718
(5 rows)
```

## Uninstalling the Chart

```bash
helm uninstall postgres-test
```
