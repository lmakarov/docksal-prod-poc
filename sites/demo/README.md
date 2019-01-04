# Demo Site

## Local development

This repo provides a local development environment using [Docksal](https://github.com/docksal/docksal).  
Please report issues related to Docksal in the [issues queue](https://github.com/docksal/docksal/issues) on GitHub.

### Docksal installation

If this is the first time you are going to use Docksal on any project, 
please following the instructions for [Installing Docksal](https://docs.docksal.io/getting-started/setup/)

If you already have a working Docksal environment for any project, then 
proceed with the project setup steps below.

### Project initialization

1. Clone this repo
2. Initialize the stack and site

    ```
    fin init
    ```

## Hosting

This project can be hosted with Docker anywhere.

Deploy the codebase to `/var/www/` on the server(s).

Important paths:

- `/var/www/files/demo` - file uploads (mounted as `/mnt/files` inside the project stack)
- `/var/www/sites/demo` - site codebase
- `/var/www/system` - system stack definition (Traefik ingress, etc.) 

### Manage system services

System services are defined in `/var/www/system` on the server(s).

Traefik is used as the ingress reverse-proxy.

To start system services:

```bash
make up
```

Note: run `make help` for a list of available commands.

### Manage project stack

Switch into `/var/www/sites/demo` on the server(s).

To start the project stack:

```bash
make up
```

Other operations:

```
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
bash                 Open a bash console for cli service
drush                Run drush. Examples: "make drush status", "make drush -- --version"
db-backup            Create a timestamped database backup
logs                 Show stack logs. Examples: "make logs", "make logs web"
show-config          Print effective docker-compose configuration for the stack
```
