# Make Options

## Docker vs Podman

By default, all `make` targets working with containers or container images will use **podman** if it is installed - and fall back to **Docker** if not.

You can force the usage of Docker with `make CONTAINER_ENGINE=docker ...`.

## Podman VM memory

On macOS, Podman runs containers inside a Linux VM. The default VM memory (2 GB) is **not sufficient** to compile the Go services in this repo — the compiler will be killed by the OOM killer (`signal: killed` errors during `go build`).

Check your current allocation:

```bash
podman machine inspect | grep -i memory
```

Set at least 4 GB (8 GB recommended for parallel builds):

```bash
podman machine stop
podman machine set --memory 8192
podman machine start
```

## Parallel builds

By default, `make build-services` (and all `make` targets depending on it) will trigger up to 7 jobs in parallel to build the subcomponents.

You can change this behavior by overriding `BUILD_SERVICES_OPTS` and providing an alternative `-j` parameter for make, e.g.

* `make BUILD_SERVICES_OPTS="-j1" ...` will limit number of parallel jobs to 1
* `make BUILD_SERVICES_OPTS="-j" ...` will not limit number of parallel jobs

If builds fail with `signal: killed` even with sufficient VM memory, reduce parallelism to lower peak memory usage:

```bash
make BUILD_SERVICES_OPTS="-j1" personal-dev-env
```