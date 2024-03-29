# FastAPI-TDD-Docker
![Continuous Integration and Delivery](https://github.com/yysu/fastapi-tdd-docker/workflows/Continuous%20Integration%20and%20Delivery/badge.svg?branch=main)

Our objective is to create a real-time text summarization service used for creating article summaries from a given URL.

For example, we would like to summarize https://news.google.com/
It would show "COVID-19 news: See the latest coverage of the coronavirus"

## API spec
The API itself will follow RESTful design principles, using the basic HTTP verbs: GET, POST, PUT, and DELETE.

| Endpoint | HTTP Method | CRUD Method | Result |
| ----- | ----- | ----- | ---- |
| /summaries | GET | READ | get all summaries |
| /summaries/:id | GET | READ | get a single summary |
| /summaries | POST | CREATE | add a summary | 
| /summaries/:id | PUT | UPDATE | update a summary | 
| /summaries/:id | DELETE | DELETE | delete a summary | 

## Tech stack
- FastAPI
- Tortoise ORM
- Postgres DB
- Pytest
- Docker
- GitHub Action
- Heroku

## Play around with this service
```
Domain=https://fastapi-tdd-docker-jimsu.herokuapp.com

# Make sure the website is alive
$ curl ${Domain}/ping

# Pick a website which you would like to get summary
# ex. https://www.nytimes.com/
$ ID=$(curl -s -X POST ${Domain}/summaries/ \
   -H 'Content-Type: application/json' \
   -d '{"url":"https://news.google.com/"}'  | jq '.id')

# Get the result
$ curl -s ${Domain}/summaries/${ID}/ | jq '.summary' 
```


## docker-compose Commands
```
# Start
$ docker-compose up -d --build

# tear down
$ docker-compose down -v
```


## pytest Commands
```
# normal run
$ docker-compose exec web python -m pytest

# disable warnings
$ docker-compose exec web python -m pytest -p no:warnings

# run only the last failed tests
$ docker-compose exec web python -m pytest --lf

# run only the tests with names that match the string expression
$ docker-compose exec web python -m pytest -k "summary and not test_read_summary"

# stop the test session after the first failure
$ docker-compose exec web python -m pytest -x

# enter PDB after first failure then end the test session
$ docker-compose exec web python -m pytest -x --pdb

# stop the test run after two failures
$ docker-compose exec web python -m pytest --maxfail=2

# show local variables in tracebacks
$ docker-compose exec web python -m pytest -l

# list the 2 slowest tests
$ docker-compose exec web python -m pytest --durations=2

# Run the tests with coverage
$ docker-compose exec web python -m pytest --cov="."

# Run the tests with coverage (html)
$ docker-compose exec web python -m pytest --cov="." --cov-report html

# Linting with flake8, exclude env folder
$ docker-compose exec web flake8 --exclude=env

# Format code check with Black, exclude env folder
$ docker-compose exec web black . --check

# Format with diff in Black, exclude env folder
$ docker-compose exec web black . --check --exclude=env --diff

# Format code with Black, exclude env folder --dif
$ docker-compose exec web black .

# Sort all our imports alphabetically
$ docker-compose exec web isort . --check-only -s env
$ docker-compose exec web isort . --diff -s env
$ docker-compose exec web isort . -s env
```

## heroku commands
```
# Create a new app
$ heroku create

# Login in to the Heroku container registry
$ heroku container:login

# Provision a new Postgres database with the hobby-dev plan
$ heroku addons:create heroku-postgresql:hobby-dev --app <app-name>

# Release the image
$ heroku container:release web --app <app-name>

# Apply the migrations
$ heroku run aerich upgrade --app <app-name>
```

## Docker
```

# Build docker using amd64
$ docker buildx build --load --platform linux/amd64 -f project/Dockerfile.prod -t registry.heroku.com/<app-name>/web ./project

# push to heroku
$ docker push registry.heroku.com/<app-name>/web:latest
```