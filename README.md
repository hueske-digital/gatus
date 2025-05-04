# Gatus Monitoring Setup

This repository contains the configuration and setup for a [Gatus](https://github.com/TwiN/gatus) monitoring system. It includes service monitoring, alerting, and a PostgreSQL backend for storage.

## Features

- **Service Monitoring**: Monitor HTTP(S) endpoints, TCP connections, and more.
- **Alerting**: Configured with Pushover for notifications.
- **PostgreSQL Storage**: Persistent storage for monitoring data.
- **Dynamic Configuration**: Uses templates and environment variables for flexible setup.
- **Dockerized Deployment**: Fully containerized using `docker-compose`.

## Prerequisites

- Docker and Docker Compose installed on your system.
- A Git repository with deploy key support for configuration synchronization.

## Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/hueske-digital/gatus.git
cd gatus
```

### 2. Generate SSH Key for Deploy Key

Run the following command to generate an SSH key for accessing the Git repository:

```bash
docker compose run --rm ssh-key-generator
```

Add the generated key as a deploy key in your Git repository (the one with config files for the checks) with **read-only** access.

### 4. Start the Services

Start the monitoring stack using Docker Compose:

```bash
docker compose up -d
```

### 5. Access the Dashboard

The Gatus dashboard will be available at `http://<your-server-ip>:8080`. Use the credentials configured in the `.env` file or the default (`admin`/`admin`).

## Configuration

### Regenerate Global Configuration

Run the following command to generate the `global.yaml` configuration file:

```bash
docker compose up -d --force-recreate
```

OR

```bash
docker compose run --rm gatus-globalconfig
```

### Environment Variables

The `.env` file contains the following configurable variables:

- `PUSHOVER_APP_TOKEN`: Pushover application token for alerting.
- `PUSHOVER_USER_KEY`: Pushover user key for alerting.
- `BASIC_AUTH_USERNAME`: Username for the Gatus dashboard.
- `BASIC_AUTH_PASSWORD`: Password for the Gatus dashboard.
- `GIT_REPO_URL`: URL of the Git repository for configuration synchronization.
- `POSTGRES_USER`: PostgreSQL username.
- `POSTGRES_PASSWORD`: PostgreSQL password.
- `POSTGRES_DB`: PostgreSQL database name.
- `SYNC_INTERVAL`: Interval for syncing configuration from the Git repository.

### Endpoint Configuration

Endpoint configurations are stored in the `config/` directory. Each YAML file defines monitoring rules for specific services. Example:

```yaml
endpoints:
  - name: "Example Service"
    url: "https://example.com"
    group: public
    interval: 60s
    conditions:
      - "[STATUS] == 200"
      - "[RESPONSE_TIME] < 1000"
```

### Templates

The `templates/` directory contains the `global.tpl.yaml` file, which dynamically generates the `global.yaml` configuration based on environment variables.

## Backup and Restore

The PostgreSQL database is automatically backed up using Ofelia. Backups are stored in the `db_data` volume.

## Troubleshooting

- **Database Connection Issues**: Ensure the PostgreSQL service is healthy and the credentials in `.env` are correct.
- **Configuration Sync Issues**: Verify the deploy key and Git repository URL.
- **Alerting Issues**: Check the Pushover configuration in `.env`.