setup: install db-migrate

install:
	npm install

db-migrate:
	npm run migrate

build:
	npm run build

prepare-env:
	cp -n .env.example .env

start:
	NODE_ENV=production npm run start

dev:
	npx concurrently "make start-frontend" "make start-backend"

start-backend:
	npm start -- --watch --verbose-watch --ignore-watch='node_modules .git .sqlite'

start-frontend:
	npx webpack --watch --progress

lint:
	npx eslint .

lint-fix:
	npx eslint --fix .

test:
	NODE_ENV=test npm test -s

compose:
	docker compose up -d

compose-production:
	docker compose --file docker-compose.yml run production

compose-build:
	docker compose build

compose-logs:
	docker compose logs -f

compose-down:
	docker compose down --remove-orphans || true

compose-clear:
	docker compose down -v --remove-orphans || true

compose-stop:
	docker compose stop || true

compose-restart:
	docker compose restart

compose-setup: compose-down compose-build app-setup

compose-ci-build:
	docker build -f Dockerfile.production -t hexletbasics/services-app .
	docker build -f services/web-nginx/Dockerfile -t hexletbasics/services-web .
	docker compose -f docker-compose.yml build

compose-ci: compose-ci-build
	docker compose --file docker-compose.yml up --abort-on-container-exit

compose-app:
	docker compose -f docker-compose.yml up --abort-on-container-exit --exit-code-from app

ci:
	docker compose -f docker-compose.yml up --abort-on-container-exit --exit-code-from app

compose-dev:
	docker compose -f docker-compose.yml -f docker-compose.override.yml up app

compose-test:
	docker compose -f docker-compose.yml up --abort-on-container-exit --exit-code-from app