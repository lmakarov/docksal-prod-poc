# Docker System Service Stack 

A set of system (global) services and tools for Docker based project stacks.


## Features

- Ingress reverse-proxy using Traefik
- Docker web UI using Portainer
- Logs and monitoring using TBD


## Manage system services

To start the system stack:

```bash
make up
```

Other operations:

```bash
$ make
help                 Show this help
build                Build stack images
start                Start project stack
up                   Alias for "start"
stop                 Stop project stack
restart              Restart project start (stop + start)
remove               Remove project stack
reset                Reset project start (remove + start)
ps                   List stack services
logs                 Show stack logs. Examples: make logs; make logs web
show-config          Print effective stack docker-compose config
ctop                 Run ctop to view realtime container CPU/RAM/Disk/Network stats and logs
```


## Environment-specific variables

`.env` defines the current environment in use, e.g.:


```bash
HOSTING_ENVIRONMENT = prod
```

This file should NOT be stored in git and should only be present on the server.

Environment variables for a specific environment can be then set in `.<environment>.env`, which is included base on the
`HOSTING_ENVIRONMENT` value in the `.env` file.


## Basic HTTP Authentication for web UIs

Credentials: `admin`:`Ch@ngeMePle@se`

Generate a new credentials string with:

```bash
echo $(htpasswd -nb '<AUTH-USER>' '<AUTH-PASS>') | sed -e 's/\$/\$\$/g'
```

Set/update `ADMIN_BASIC_AUTH` variable in `.<environment>.env`, then run `make up`.
