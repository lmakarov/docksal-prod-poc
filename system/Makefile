SHELL = /bin/bash

# Set project environment here. This file is git ignored and SHOULD NOT be committed to git
include .env
include .common.env
# Include additional invironment overrides if present
-include .$(HOSTING_ENVIRONMENT).env

# Make it possible to pass arguments to Makefile from command line
# https://stackoverflow.com/a/6273809/1826109
ARGS = $(filter-out $@,$(MAKECMDGOALS))

.EXPORT_ALL_VARIABLES:

# Prints help based on annotations for Makefile commands
.PHONY: help
help: ## Show this help
	@awk 'BEGIN {FS = ":.*?## "} /^[a-zA-Z_-]+:.*?## / {sub("\\\\n",sprintf("\n%22c"," "), $$2);printf "\033[36m%-20s\033[0m %s\n", $$1, $$2}' $(MAKEFILE_LIST)

.PHONY: build
build:  ## Build stack images
	@echo "Building stack images for $(PROJECT_NAME)..."
	@$(DOCKER_COMPOSE) build

.PHONY: start
start: ## Start project stack
	@echo "Starting up containers for $(PROJECT_NAME)..."
	#@$(DOCKER_COMPOSE) pull
	@$(DOCKER_COMPOSE) up -d --remove-orphans --build

.PHONY: up
up: start  ## Alias for "start"

.PHONY: stop
stop: ## Stop project stack
	@echo "Stopping containers for $(PROJECT_NAME)..."
	@$(DOCKER_COMPOSE) stop

.PHONY: restart
restart: ## Restart project start (stop + start)
	@make stop
	@make start

.PHONY: remove
remove: ## Remove project stack
	@echo "Removing resources for $(PROJECT_NAME)..."
	@make network-disconnect
	@$(DOCKER_COMPOSE) down -v

.PHONY: reset
reset: ## Reset project start (remove + start)
	@make remove
	@make start

.PHONY: ps
ps:  ## List stack services
	@$(DOCKER) ps --filter "label=com.docker.compose.project=$(PROJECT_NAME)"

.PHONY: logs
logs:  ## Show stack logs. Examples: make logs; make logs web
	@$(DOCKER_COMPOSE) logs -f $(ARGS)

.PHONY: network-disconnect
network-disconnect:
	@for network in $(shell $(DOCKER) network inspect -f '{{range .Containers}}{{.Name}} {{end}}' $(PROJECT_NETWORK_NAME)); do \
		$(DOCKER) network disconnect -f $(PROJECT_NETWORK_NAME) $$network; \
	done

.PHONY: show-config
show-config:  ## Print effective stack docker-compose config
	@$(DOCKER_COMPOSE) config

.PHONY: ctop
ctop:  ## Run ctop to view realtime container CPU/RAM/Disk/Network stats and logs
	@docker rm -vf ctop &>/dev/null || true
	@docker run --rm -it --name=ctop \
		-v /var/run/docker.sock:/var/run/docker.sock \
		quay.io/vektorlab/ctop:latest

# https://stackoverflow.com/a/6273809/1826109
%:
	@:
