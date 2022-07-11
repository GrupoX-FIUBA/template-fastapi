# Template repository for FastAPI microservices

This is a template to use as a starting point for FastAPI microservice repositories. Once configured, it will run the tests on every pull request to `main`, and will automatically deploy to corresponding the Kubernetes cluster when a push to `main` occurs.

## To Do after fork/create-from-template

1. Go to the repository settings, _Actions secrets_ and create the following _repository secrets_:
	- `APP_NAME`: the name of the service (preferably `service-X`).
	- `DB_USER`: the name of the database user.
	- `DB_PASS`: the password for the database user.
2. Create the following _Organization secrets_, replacing `XYZ` with your service name:
	- `XYZ_SERVICE_URL`: the URL you will publicly access to your service (Ask the Kubernetes manager).
	- `XYX_SERVICE_API_KEY`: a random API Key to limit access to your service.
3. In `.github/workflows/deploy.yml`, replace `X_SERVICE_API_KEY` with the name of the secret you just create, and `xdb` with the name of the database (preferably something like `musicdb`).
4. Replace all `SERVICE-` with your service name in `docker-compose.yml`.
4. If needed, create the user and database for postgres:
	- Connect to the postgres instance (via Kubernetes).
	- `CREATE USER user-name WITH PASSWORD 'user-password';`.
	- `CREATE DATABASE db-name WITH OWNER 'user-name';`.

In the `app` directory there's a very simple example of a FastAPI app with tests.

## Local development

- First, create a `.env` file to reduce commands length, setting up the COMPOSE_PROFILES variable to "dev" (`echo COMPOSE_PROFILES=dev >> .env`)
- It's also recommended to build the container after a pull: `docker-compose build`.
- Then you can run the app: `docker-compose up`. It will also run a postgres container for development.

The app will start at port 8000 as default. You can use a specific port by setting the PORT env variable either at the `.env` file or within each command.

### Changes to database models

To create database migrations for changes done in _models_ files, run `docker-compose exec development alembic revision --autogenerate -m "Title of migration"`.

To apply changes to existing-running container, you can either restart it (`docker-compose restart`) or run the migrations (`docker-compose exec development alembic upgrade head`).

## Tests

To run the tests, simply execute `docker-compose --profile test up`. You can add `--exit-code-from test` to pass the exit code of the test script to the shell session.

Again, it's recommended to build the container after a pull. In this case, you must build with the _test_ profile: `docker-compose --profile test build`.

## Docs

The documentation is generated automatically by FastAPI. It's available in the server at `/docs` (Swagger) and `/redoc` (ReDoc)
