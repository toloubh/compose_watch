Use watch to automatically update and preview your running Compose services as you edit and save your code.

*** Compose Watch is available in Docker Compose version 2.22 and later.

For many projects, this allows for a hands-off development workflow once Compose is running, as services automatically update themselves when you save your work.

How can run it:

1- sudo docker compose up --wait -d

$ docker ps
```
CONTAINER ID   IMAGE                      COMMAND                  CREATED              STATUS                        PORTS                    NAMES
094906aeca5b   python-docker-dev-server   "python3 app.py"         About a minute ago   Up About a minute             0.0.0.0:5000->5000/tcp   backend
4be29b9fb6e7   postgres:16.1-alpine       "docker-entrypoint.s…"   About a minute ago   Up About a minute (healthy)   5432/tcp                 postgreSql
```
------
$ curl localhost:5000
```
Hello, Docker!
```
------
# Automatically update services
2- sudo nohup docker compose watch <service name> > output.log 2>&1 & 
3- jobs
```
[1]+ Running sudo nohup docker compose watch <service name> > output.log 2>&1 &
```
-------
4- Edit app.py and check the URL again; you should see that the content has changed.
$ curl localhost:5000
```
Hello, Docker :)
```
--------
$ docker ps #the backend service has been restarted
```
CONTAINER ID   IMAGE                      COMMAND                  CREATED              STATUS                   PORTS                    NAMES
49c9cf4d8e88   python-docker-dev-server   "python3 app.py"         About a minute ago   Up 49 seconds            0.0.0.0:5000->5000/tcp   backend
4be29b9fb6e7   postgres:16.1-alpine       "docker-entrypoint.s…"   4 minutes ago        Up 4 minutes (healthy)   5432/tcp                 postgreSql
```

- Configuration

The watch attribute defines a list of rules that control automatic service updates based on local file changes.

Each rule requires, a path pattern and action to take when a modification is detected. There are two possible actions for watch and depending on the action, additional fields might be accepted or required.

Watch mode can be used with many different languages and frameworks. The specific paths and rules will vary project to project, but the concepts remain the same.

- Prerequisites

In order to work properly, watch relies on common executables. Make sure your service image contains the following binaries:
```
    stat
    mkdir
    rmdir
    tar
```
- action

**Sync

If action is set to sync, Compose makes sure any changes made to files on your host automatically match with the corresponding files within the service container.

sync is ideal for frameworks that support "Hot Reload" or equivalent functionality.

More generally, sync rules can be used in place of bind mounts for many development use cases.

** Rebuild

If action is set to rebuild, Compose automatically builds a new image with BuildKit and replaces the running service container.

The behavior is the same as running docker compose up --build <svc>.

Rebuild is ideal for compiled languages or as fallbacks for modifications to particular files that require a full image rebuild (e.g. package.json).

** Sync + Restart

If action is set to sync+restart, Compose synchronizes your changes with the service containers and restarts it.

sync+restart is ideal when config file changes, and you don't need to rebuild the image but just restart the main process of the service containers. It will work well when you update a database configuration or your nginx.conf file for example

* Use watch
```
    1. Add watch sections to one or more services in compose.yaml.
    2. Run docker compose watch to build and launch a Compose project and start the file watch mode.
    3. Edit service source files using your preferred IDE or editor.
```
