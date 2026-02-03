# Docker Setup Guide

This guide explains how to run the Open Paper project locally using Docker. This setup simplifies the installation process by containerizing all services (Server, Client, Jobs Service, Database, Redis, RabbitMQ).

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running.

## Configuration

1. Create a `.env` file in the root directory of the project. You can copy the example below:

```bash
# AI Service
GEMINI_API_KEY=your_gemini_api_key

# Object Storage (AWS S3 or Cloudflare R2)
AWS_ACCESS_KEY_ID=your_access_key
AWS_SECRET_ACCESS_KEY=your_secret_key
S3_BUCKET_NAME=your_bucket_name
CLOUDFLARE_PUBLIC_DOMAIN=your_public_bucket_domain # e.g. assets.example.com
AWS_REGION=us-east-1

# Optional Analytics
NEXT_PUBLIC_POSTHOG_KEY=
NEXT_PUBLIC_POSTHOG_HOST=

# Optional Payments
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=
STRIPE_API_KEY=
STRIPE_MONTHLY_PRICE_ID=
STRIPE_YEARLY_PRICE_ID=
```

**Note:**
*   `DATABASE_URL`, `CELERY_BROKER_URL`, and `CELERY_RESULT_BACKEND` are automatically configured in `docker-compose.yml` to point to the internal Docker services.
*   `CLOUDFLARE_BUCKET_NAME` is used to generate public URLs for uploaded files. If you are using AWS S3, this might be your bucket URL domain.

## Running the Application

1. Build and start the services:

```bash
docker-compose up --build
```

2. Wait for the services to start.
    *   The `server` will run database migrations automatically.
    *   The `client` might take a few minutes to build the Next.js application.

3. Access the application:
    *   **Frontend**: [http://localhost:3000](http://localhost:3000)
    *   **Backend API**: [http://localhost:8000/docs](http://localhost:8000/docs)
    *   **RabbitMQ Management**: [http://localhost:15672](http://localhost:15672) (User: `guest`, Pass: `guest`)

## Stopping the Application

Press `Ctrl+C` in the terminal, or run:

```bash
docker-compose down
```

To remove the database volume (reset all data):

```bash
docker-compose down -v
```

## Troubleshooting

*   **S3/Upload Errors**: Check the `jobs-worker` logs. Ensure your AWS/Cloudflare credentials and bucket name are correct in the `.env` file.
*   **Database Connection**: If the server fails to connect to the DB initially, it will retry. Ensure the `db` service is healthy.
*   **Client API Errors**: If the client cannot reach the server, ensure `server` is running on port 8000. The setup assumes you are accessing `localhost:3000` from your browser.
