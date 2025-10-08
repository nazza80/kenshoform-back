# Docker Compose Setup for wger/kenshoform-back

This docker-compose.yml file provides a complete production-ready stack for running wger Workout Manager.

## Services

The stack includes the following services:

- **web**: Main Django application (using Gunicorn)
- **nginx**: Reverse proxy and static file server
- **db**: PostgreSQL 15 database
- **cache**: Redis cache for sessions and Celery
- **celery_worker**: Celery worker for background tasks
- **celery_beat**: Celery beat scheduler for periodic tasks

## Quick Start

1. Copy the environment file and configure it:
   ```bash
   cp .env.example .env
   # Edit .env with your settings
   ```

2. Build and start the services:
   ```bash
   docker-compose up -d
   ```

3. The application will be available at:
   - http://localhost (via nginx)
   - http://localhost:8000 (direct access to web service)

## First Run

On first run, you may want to populate the database with exercises and ingredients:

1. Update your `.env` file:
   ```bash
   SYNC_EXERCISES_ON_STARTUP=True
   DOWNLOAD_EXERCISE_IMAGES_ON_STARTUP=True
   LOAD_ONLINE_FIXTURES_ON_STARTUP=True
   ```

2. Restart the web service:
   ```bash
   docker-compose restart web
   ```

3. After the initial sync, set these back to `False` to avoid re-syncing on every restart.

## Environment Variables

Key environment variables in `.env`:

- `SECRET_KEY`: Django secret key (must be changed in production)
- `SITE_URL`: Your site's URL
- `DJANGO_DB_*`: Database credentials
- `ALLOW_REGISTRATION`: Enable/disable user registration
- `USE_CELERY`: Enable background task processing

See `.env.example` for all available options.

## Volumes

The following volumes are created for data persistence:

- `postgres-data`: PostgreSQL database files
- `redis-data`: Redis persistence
- `static`: Collected static files
- `media`: User uploaded media files
- `celery-beat`: Celery beat schedule

## Development vs Production

This configuration uses the production Dockerfile and is suitable for both development and production use.

For production:
- Change `SECRET_KEY` to a strong random value
- Set `DJANGO_DEBUG=False`
- Configure proper `SITE_URL` and `CSRF_TRUSTED_ORIGINS`
- Consider using HTTPS (update nginx configuration)
- Review security settings in `.env`

## Commands

```bash
# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down

# Rebuild services
docker-compose build

# Run Django management commands
docker-compose exec web python3 manage.py <command>

# Create superuser
docker-compose exec web python3 manage.py createsuperuser

# Access the database
docker-compose exec db psql -U wger -d wger
```

## Troubleshooting

If you encounter issues:

1. Check logs: `docker-compose logs -f web`
2. Ensure all environment variables are set in `.env`
3. Verify database connectivity: `docker-compose exec web python3 manage.py check`
4. Run migrations manually: `docker-compose exec web python3 manage.py migrate`

## More Information

For more detailed documentation, visit:
- [wger Documentation](https://wger.readthedocs.io)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
