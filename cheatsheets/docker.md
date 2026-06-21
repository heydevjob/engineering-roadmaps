# Docker Cheatsheet

The commands that get you from "it works on my machine" to a running container someone else can pull. Grouped by the task in front of you, beginner to advanced.

## Images: build, tag, pull, push

```bash
docker build -t myapp:1.0 .                        # build from the Dockerfile in this directory
docker build -t myapp:1.0 -f Dockerfile.prod .     # build from a named Dockerfile
docker build --no-cache -t myapp:1.0 .             # rebuild ignoring the layer cache
docker images                                      # list local images with size and tags
docker tag myapp:1.0 ghcr.io/me/myapp:1.0          # add a registry-qualified tag
docker pull nginx:1.27                             # download an image from a registry
docker push ghcr.io/me/myapp:1.0                   # upload a tagged image
docker history myapp:1.0                           # see each layer and what it cost in size
docker inspect myapp:1.0                           # full image metadata as JSON
```

## Containers: run, exec, logs, stop, rm

```bash
docker run nginx                                   # run in the foreground
docker run -d --name web nginx                     # run detached with a name
docker run -d -p 8080:80 nginx                     # map host 8080 to container 80
docker run -e ENV=prod -d myapp:1.0                # pass an environment variable
docker run --rm -it ubuntu bash                    # interactive shell, auto-remove on exit
docker ps                                          # running containers
docker ps -a                                       # all containers, including stopped
docker exec -it web bash                           # shell into a running container
docker logs web                                    # print the container's logs
docker logs -f --tail=100 web                      # stream the last 100 lines live
docker stop web                                    # graceful stop (SIGTERM, then SIGKILL)
docker restart web                                 # stop and start again
docker rm web                                      # remove a stopped container
docker rm -f web                                   # force-remove a running container
docker stats                                       # live CPU/memory/IO per container
```

## Volumes

```bash
docker volume create data                          # create a named volume
docker volume ls                                   # list volumes
docker run -v data:/var/lib/app myapp:1.0          # mount a named volume into a container
docker run -v "$PWD":/app myapp:1.0                # bind-mount the current dir (live code)
docker run -v "$PWD":/app:ro myapp:1.0             # bind-mount read-only
docker volume inspect data                         # where it lives on disk and its mountpoint
docker volume rm data                              # delete a volume (data is gone)
```

## Networks

```bash
docker network ls                                  # list networks
docker network create appnet                       # create a user-defined bridge network
docker run -d --network appnet --name db postgres  # join a container to a network
docker run -d --network appnet --name web myapp:1.0 # containers on appnet reach "db" by name
docker network inspect appnet                      # see which containers are attached
docker network connect appnet web                  # attach a running container to a network
```

## Dockerfile essentials

```dockerfile
FROM node:20-slim                                  # base image (slim variants cut size)
WORKDIR /app                                       # set the working dir for later commands
COPY package*.json ./                              # copy deps manifest first for cache reuse
RUN npm ci --omit=dev                              # install before copying source = cached layer
COPY . .                                           # copy the rest of the source
EXPOSE 3000                                        # document the port (does not publish it)
ENV NODE_ENV=production                            # set a build/runtime env var
USER node                                          # drop root for the running process
CMD ["node", "server.js"]                          # default command when the container starts
```

Order matters: put the lines that change least at the top. A `COPY . .` before `npm install` busts the cache on every source edit.

## Multi-stage builds

```dockerfile
# Build stage: full toolchain, never ships
FROM golang:1.22 AS build
WORKDIR /src
COPY . .
RUN CGO_ENABLED=0 go build -o /app .

# Final stage: tiny runtime image, just the binary
FROM gcr.io/distroless/static
COPY --from=build /app /app                         # pull only the artifact from the build stage
ENTRYPOINT ["/app"]
```

```bash
docker build --target build -t myapp:debug .       # build only up to a named stage
```

The point: compilers, dev headers, and node_modules stay in the build stage. The shipped image is just the runtime plus your artifact.

## Compose basics

```bash
docker compose up                                  # start all services in the foreground
docker compose up -d                               # start detached
docker compose up -d --build                       # rebuild images before starting
docker compose ps                                  # status of services in this project
docker compose logs -f web                          # stream one service's logs
docker compose exec web bash                        # shell into a running service
docker compose down                                # stop and remove containers + networks
docker compose down -v                             # also remove named volumes (wipes data)
```

A minimal `compose.yaml`:

```yaml
services:
  web:
    build: .
    ports:
      - "8080:80"
    depends_on:
      - db
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - dbdata:/var/lib/postgresql/data
volumes:
  dbdata:
```

## Prune & cleanup

```bash
docker system df                                   # how much disk images/containers/volumes use
docker container prune                             # remove all stopped containers
docker image prune                                 # remove dangling (untagged) images
docker image prune -a                              # remove every image not used by a container
docker volume prune                                # remove unused volumes (careful)
docker builder prune                               # clear the build cache
docker system prune -a --volumes                   # nuke everything unused, including volumes
```

## Debugging

```bash
docker inspect web                                 # full container state, mounts, network, env
docker inspect -f '{{.State.ExitCode}}' web        # just the exit code of a stopped container
docker logs --since 10m web                          # logs from the last 10 minutes
docker top web                                      # processes running inside the container
docker diff web                                     # files changed vs the image (what wrote where)
docker run --rm -it --entrypoint sh myapp:1.0       # override the entrypoint to poke around
docker exec web cat /etc/hosts                        # check resolution and network config inside
docker events                                       # live stream of daemon events (starts, dies, OOM)
```

When a container exits immediately, `docker logs <name>` plus the exit code from `docker inspect` is almost always the whole story. An exit 137 means it was OOM-killed; check `docker stats` and your memory limits.

## Practice it live

Reading `docker run` flags is one thing; shrinking a 1.2GB image to 80MB or tracking down why a container dies on boot is another. On [HeyDevJob](https://heydevjob.com) these commands run in real cloud workspaces with genuinely broken builds and containers - a bloated image, a failing multi-stage build, a service that exits on start - that you fix from your browser. Free to start, no card, no setup. [Practice on HeyDevJob →](https://heydevjob.com)
