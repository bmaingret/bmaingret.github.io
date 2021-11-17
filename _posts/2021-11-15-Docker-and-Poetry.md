---
category: [programming, python, devops]
tags: [docker, poetry, pyenv, gcp]
title: "Python and Poetry on Docker"
---

Build a multi-stage Docker image from official Python images with support for Poetry projects.

[Source code on Github](https://github.com/bmaingret/coach-planner)

<!--more-->
Sources:
 * [Python=>Speed](https://pythonspeed.com/articles/base-image-python-docker-images/)
 * [Python images on docker.com](https://hub.docker.com/_/python)
 * [Dockerfile on github.com/michaeloliverx](https://github.com/michaeloliverx/python-poetry-docker-example/blob/master/docker/Dockerfile)
 * [Dockerfiles on mktr.ai](https://www.mktr.ai/the-data-scientists-quick-guide-to-dockerfiles-with-examples)
 * [Discussions on Github Poetry](https://github.com/python-poetry/poetry/discussions/1879)


## Summary
- [Multi-stage build](#multi-stage-build)
- [Choosing a base version](#choosing-a-base-version)
- [Stage: Staging](#stage-staging)
  - [ARG and environment variables](#arg-and-environment-variables)
  - [Install Poetry](#install-poetry)
  - [Source file and dependencies](#source-file-and-dependencies)
- [Stage: Development](#stage-development)
  - [Install our project](#install-our-project)
  - [Flask webserver and entrypoint](#flask-webserver-and-entrypoint)
- [Stage: Build](#stage-build)
- [Stage: Production](#stage-production)
  - [Environment variables](#environment-variables)
  - [Installating our application](#installating-our-application)
  - [Entrypoint](#entrypoint)
- [Build our image and use it!](#build-our-image-and-use-it)
  - [Production image](#production-image)
  - [Development image](#development-image)




## Multi-stage build

A multi-stage build allows:
* to stop at a specific step of a build
* start from different base thus beginning a new stage of the bulid
* pass artifacts from one build to another

I started this while looking for the best way to use both Docker and Poetry, and stumble upon an quite complete dockerfile at [github.com/michaeloliverx/python-poetry-docker-example](https://github.com/michaeloliverx/python-poetry-docker-example/blob/master/docker/Dockerfile). The author use a multi-stage build to offer several images for development, testing, linting and production. Stopping at a specific images allows to minimize the image size and time to run for each step. However I was not completly happy with this example which was a bit too complex for me and I wanted to dig in anyway.

In our case we will have the following stages:
 * `staging`: Installs poetry and copy relevant source files
 * `development`: Install our project in editable mode
 * `build`: Build our project into a wheel file
 * `production`: Clean Python image that installs our built wheel

## Choosing a base version

To start of our image we need to chose the base image, with two obvious options:

 1. Official Linux images (Ubuntu, Debian, RHEL)
 2. Official Python images

I discarded the first one as you don't always have the most recent Python versions, and I am not so worried about performance differences pointed out by [Python=>Speed](https://pythonspeed.com/articles/base-image-python-docker-images/).

Regarding [official Python images on docker.com](https://hub.docker.com/_/python), we still have to chose the tag (i.e. flavor) we want:
 
 1. python:<version>(-<debian-codename>): based of a specific or not Debian version with common packages installed
 2. python:<versoins>-slim: based of a specific or not Debian version with only the strict requirements for working Python environment
 3. python:<version>-alpine: discarded because although much smaller, it brings complexity specific to the Alpine distribution

I chose 1 by default, the recommended version and including building tools that are needed anyway.

```dockerfile
FROM python:3.10.0 as python-base
```

## Stage: Staging

### ARG and environment variables

We set up a few environment variables for Python, Pip and Poetry configurations.

A few things to keep in mind:

* Dockerfile doesn't support reference to previously defined environment variables in the same `ENV` instruction.
* `ARG` defined before a stage can be used by referencing them again in the stage.
* When using a different base image than the previous stages, `ENV` variables won't be defined anymore. (seems obvious once said...)

```dockerfile
ARG APP_NAME=coach_planner
ARG APP_PATH=/opt/$APP_NAME
ARG PYTHON_VERSION=3.10.0
ARG POETRY_VERSION=1.1.11
```

 1. Python process will be ran only once in the container so we don't need to write the compiled Python files (*.pyc) to disk
 2. Make sure Python outputs are sent straight to terminal
 3. Make sure Python traceback are dumped  (even on segfaults for instance)
 4. No need for the cache in the Docker image

```dockerfile
ENV \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONFAULTHANDLER=1 
ENV \
    POETRY_VERSION=$POETRY_VERSION \
    POETRY_HOME="/opt/poetry" \
    POETRY_VIRTUALENVS_IN_PROJECT=true \
    POETRY_NO_INTERACTION=1
```

 5. We won't update the pip version in any case
 6. Default timeout is only 15 seconds
 7. Fixing Poetry version
 8. Instead of /root
 9. Make sure the `.venv` directory will be in the build directory
 10. No prompt from Poetry
 11. Path for building stages
 12. Add the virtual environment to path in a separate `ENV`line to use previously defined environment variables.
 13. Update path with Poetry and virtual env path.

### Install Poetry

Installation follow Poetry's official documentation and make use of the new install script supporting the upcoming Poetry version. We need to update our `PATH` to be able to use poetry afterwards.

```dockerfile
# Install Poetry - respects $POETRY_VERSION & $POETRY_HOME
RUN curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/install-poetry.py | python
ENV PATH="$POETRY_HOME/bin:$PATH"
```

### Source file and dependencies

To copy our source files, we make the assumption that the directory structure follows the one poetry would create:

```
poetry-demo
├── pyproject.toml
├── poetry_demo
│   └── __init__.py
```
We also obviously import the `poetry.lock` file.

```dockerfile
WORKDIR $APP_PATH
COPY ./poetry.lock ./pyproject.toml ./
COPY ./$APP_NAME ./$APP_NAME
```

## Stage: Development

Make sure to specify the `ARG` we need in this stage.

```dockerfile
FROM staging as development
ARG APP_NAME
ARG APP_PATH
```

### Install our project

Nothing fancy here, we make use of poetry command.

```dockerfile
WORKDIR $APP_PATH
RUN poetry install
```

### Flask webserver and entrypoint

In development mode we use the default flask webserver. We first define a few flask related environment variable, and then define an entrypooint as the `flask run` command. This run the following command in the activated virtual environment of the project. This has several advantages:
 
 * no direct `path` manipulation
 * express the intented use of this stage
 * document how to use this stage
 * easy command override while not bothering with the virtual environment. e.g. `docker run -it poetry flask shell`.

```dockerfile
ENV FLASK_APP=$APP_NAME \
    FLASK_ENV=development \
    FLASK_RUN_HOST=0.0.0.0 \
    FLASK_RUN_PORT=8888

ENTRYPOINT ["poetry", "run"]
CMD ["flask", "run"]
```

We can still access a shell by overriding the entrypoint, but that shouldn't be the most common use case imho. Note that in this cas one should activate the environement.

## Stage: Build

Nothing fancy here, we use the `poetry build` command, and add the `--flag wheel` parameter to only build the wheel.

```dockerfile
FROM staging as build

WORKDIR $APP_PATH
RUN poetry build --format wheel
```

## Stage: Production

For our production, we will start from a clean python image, and install our freshly built application.

### Environment variables

We redefined some Python related environemnt variable (required since we start from a fresh image), and we add some directly related to PIP. Note that again we reference our `ARG` to be able to use them in this stage.

```dockerfile
FROM python:$PYTHON_VERSION as production
ARG APP_NAME
ARG APP_PATH

ENV \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONFAULTHANDLER=1

ENV \
    PIP_NO_CACHE_DIR=off \
    PIP_DISABLE_PIP_VERSION_CHECK=on \
    PIP_DEFAULT_TIMEOUT=100 
```

### Installating our application

We first retrieve the packaged application from the `build` stage using the `--from` flag of the `CP` command. Then we proceed with the installation using PIP.

Note that we make use of wildcards, but we could also add the application version similarly to Python and Poetry versions, to get a more determinist install.

```dockerfile
WORKDIR $APP_PATH
COPY --from=build $APP_PATH/dist/*.whl ./
RUN pip install  ./$APP_NAME*.whl
```


### Entrypoint

We will use `gunicorn` to serve our application in production.

We define two environment variables that will be used in the `gunicorn` command allowing to be overriden (mostly for the `PORT`). This can be used when deplyoing on GCP Cloud Run for instance.

```dockerfile
# gunicorn port. Naming is consistent with GCP Cloud Run
ENV PORT=8888 
# export APP_NAME as environment variable for the CMD
ENV APP_NAME=$APP_NAME
```

The difference between `ENTRYPOINT` and `CMD` can be confusing especially when used together, and I would recommend reading [Docker documentation on the topic](https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact). In our case we need shell variable substitution for the environment variable so it limits our choice. Other alternative would be to use a `config.py` file.

```dockerfile
COPY ./docker/docker-entrypoint.sh /docker-entrypoint.sh # 1
RUN chmod +x /docker-entrypoint.sh # 1
ENTRYPOINT ["/docker-entrypoint.sh"] # 2
CMD ["gunicorn", "--bind :$PORT", "--workers 1", "--threads 1", "--timeout 0", "\"$APP_NAME:create_app()\""] # 3
```

 1. Get the entrypoint script (see below) and make it executable
 2. With this syntax, this is equivalent to `exec /docker-entrypoint.sh`
 3. These arguments will be passed to the entrypoint scripts and can be overriden by the arguments passed to `docker run`

And the script:

```bash
#!/bin/sh
set -e # 1 
eval "exec $@" # 2
```

 1. Will exit the script if any error occurs
 2. Expand arguments passed to the entrypoint script in the shell before passing them to `exec`, so that it can support passing environment variable.

## Build our image and use it!


### Production image

Let's build our image and try using it.

```console
$ docker build --tag poetry --file docker/Dockerfile .

[+] Building 33.3s (17/17) FINISHED
 => [internal] load build definition from Dockerfile                                  0.0s
 => => transferring dockerfile: 2.13kB                                                0.0s
 => [internal] load .dockerignore                                                     0.0s
 => => transferring context: 2B                                                       0.0s
 => [internal] load metadata for docker.io/library/python:3.10.0                      1.2s
 => [internal] load build context                                                     0.0s
 => => transferring context: 332B                                                     0.0s
 => CACHED [staging 1/5] FROM docker.io/library/python:3.10.0@sha256:bb797f045026352  0.0s
 => => resolve docker.io/library/python:3.10.0@sha256:bb797f045026352ece65ab376c5666  0.0s
 => CACHED [production 2/6] WORKDIR /opt/coach_planner                                0.0s
 => [staging 2/5] RUN curl -sSL https://raw.githubusercontent.com/python-poetry/poe  22.1s
 => [staging 3/5] WORKDIR /opt/coach_planner                                          0.1s
 => [staging 4/5] COPY ./poetry.lock ./pyproject.toml ./                              0.1s
 => [staging 5/5] COPY ./coach_planner ./coach_planner                                0.1s
 => [build 1/2] WORKDIR /opt/coach_planner                                            0.1s
 => [build 2/2] RUN poetry build --format wheel                                       2.2s
 => [production 3/6] COPY --from=build /opt/coach_planner/dist/*.whl ./               0.1s
 => [production 4/6] RUN pip install  ./coach_planner*.whl                            3.5s
 => [production 5/6] COPY ./docker/docker-entrypoint.sh /docker-entrypoint.sh         0.1s
 => [production 6/6] RUN chmod +x /docker-entrypoint.sh                               0.6s
 => exporting to image                                                                0.2s
 => => exporting layers                                                               0.2s
 => => writing image sha256:a612b9cdb91bacb1cefd1393318a5d15238034183fd48dbbeafffdd7  0.0s
 => => naming to docker.io/library/poetry                                             0.0s
```

Note that there is only the `staging`, `build`, and `production` stages here. The `development` stage is not required for the final (i.e. default) stage, it is not even built.


```console
$ docker run -p 8888:8888 -it poetry

[2021-11-15 18:17:59 +0000] [1] [INFO] Starting gunicorn 20.1.0
[2021-11-15 18:17:59 +0000] [1] [INFO] Listening at: http://0.0.0.0:8888 (1)
[2021-11-15 18:17:59 +0000] [1] [INFO] Using worker: sync
[2021-11-15 18:17:59 +0000] [11] [INFO] Booting worker with pid: 11
```
Let's change our gunicorn binding port:

```console
$ docker run -p 8888:8888 --env PORT=5555 -it poetry

[2021-11-17 18:21:28 +0000] [1] [INFO] Starting gunicorn 20.1.0
[2021-11-17 18:21:28 +0000] [1] [INFO] Listening at: http://0.0.0.0:5555 (1)
[2021-11-17 18:21:28 +0000] [1] [INFO] Using worker: sync
[2021-11-17 18:21:28 +0000] [7] [INFO] Booting worker with pid: 7
```

### Development image

Now let's use our development stage:

```console
$ docker build --target development -t po
etry --file docker/Dockerfile .

[+] Building 1.4s (12/12) FINISHED
 => [internal] load build definition from Dockerfile                                  0.0s
 => => transferring dockerfile: 38B                                                   0.0s
 => [internal] load .dockerignore                                                     0.0s
 => => transferring context: 2B                                                       0.0s
 => [internal] load metadata for docker.io/library/python:3.10.0                      1.2s
 => [internal] load build context                                                     0.0s
 => => transferring context: 260B                                                     0.0s
 => [staging 1/5] FROM docker.io/library/python:3.10.0@sha256:bb797f045026352ece65ab  0.0s
 => => resolve docker.io/library/python:3.10.0@sha256:bb797f045026352ece65ab376c5666  0.0s
 => CACHED [staging 2/5] RUN curl -sSL https://raw.githubusercontent.com/python-poet  0.0s
 => CACHED [staging 3/5] WORKDIR /opt/coach_planner                                   0.0s
 => CACHED [staging 4/5] COPY ./poetry.lock ./pyproject.toml ./                       0.0s
 => CACHED [staging 5/5] COPY ./coach_planner ./coach_planner                         0.0s
 => CACHED [development 1/2] WORKDIR /opt/coach_planner                               0.0s
 => CACHED [development 2/2] RUN poetry install                                       0.0s
 => exporting to image                                                                0.1s
 => => exporting layers                                                               0.0s
 => => writing image sha256:cae979092d3b1e2c833e96f2e9acdfbd6980609f36d907a6547f05f1  0.0s
 => => naming to docker.io/library/poetry                                             0.0s
 ```
Here only the `staging` and `developement` stages are run!

And using it:

```console
$ docker run -p 8888:8888 -it poetry

 * Serving Flask app 'coach_planner' (lazy loading)
 * Environment: development
 * Debug mode: on
 * Running on all addresses.
   WARNING: This is a development server. Do not use it in a production deployment.
 * Running on http://172.17.0.2:8888/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 774-185-805
```

Or overriding the default `CMD` and getting a Python shell:

```console
$ docker run -p 8888:8888 -it poetry python

Python 3.10.0 (default, Oct 26 2021, 22:20:53) [GCC 10.2.1 20210110] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

Or even overriding the default `ENTRYPOINT` and then starting a poetry shell:

```console
$ docker run -p 8888:8888 --entrypoint /bin/bash -it poetry

root@4dd4bb68f510:/opt/coach_planner# poetry shell
Spawning shell within /opt/coach_planner/.venv
root@4dd4bb68f510:/opt/coach_planner# . /opt/coach_planner/.venv/bin/activate
(.venv) root@4dd4bb68f510:/opt/coach_planner#
```
