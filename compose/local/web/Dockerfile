# define an alias for the specfic python version used in this file.
FROM python:3.10.10-slim-bullseye as python

# Python build stage
FROM python as python-build-stage

ENV PYTHONUNBUFFERED 1
ENV PYTHONDONTWRITEBYTECODE 1

# Install system packages required by Wagtail and Django.
# https://github.com/wagtail/wagtail/blob/v4.0.2/wagtail/project_template/Dockerfile
RUN apt-get update --yes --quiet && apt-get install --yes --quiet --no-install-recommends \
    build-essential \
    libpq-dev \
    libjpeg62-turbo-dev \
    zlib1g-dev \
    libwebp-dev \
 && rm -rf /var/lib/apt/lists/*

# Copy the requirements listed in the Pipfiles
COPY ./Pipfile* .venv .
RUN pip install pipenv && pipenv install --dev

# Python 'run' stage
FROM python as python-run-stage

WORKDIR /app

# RUN apt-get update && apt-get install --no-install-recommends -y \
#   # psycopg2 dependencies
#   libpq-dev \
#   # Translations dependencies
#   gettext \
#   # cleaning up unused files
#   && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
#   && rm -rf /var/lib/apt/lists/*

COPY --from=python-build-stage /venv  /venv

RUN pip install --no-cache-dir --no-index pipenv
COPY . /app
ENV PATH="/venv/bin:$PATH"

COPY ./compose/local/web/entrypoint /entrypoint
RUN sed -i 's/\r$//g' /entrypoint
RUN chmod +x /entrypoint

COPY ./compose/local/web/start /start
RUN sed -i 's/\r$//g' /start
RUN chmod +x /start

ENTRYPOINT ["/entrypoint"]
