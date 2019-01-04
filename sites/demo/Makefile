SHELL = /bin/bash

STACK_PATH = .stack

# Set project environment here. This file is git ignored and SHOULD NOT be committed to git
include $(STACK_PATH)/.env
include $(STACK_PATH)/.common.env
# Include additional invironment overrides if present
-include $(STACK_PATH)/.$(PROJECT_ENVIRONMENT).env

# Make it possible to pass arguments to Makefile from command line
# https://stackoverflow.com/a/6273809/1826109
ARGS = $(filter-out $@,$(MAKECMDGOALS))

DB_BACKUP_FILE = $(DB_BACKUP_PATH)/$(PROJECT_NAME)-$(PROJECT_ENVIRONMENT)-$(shell date +\%F-\%H\%M\%S).sql.gz

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
	@mkdir -p ../../files/$(PROJECT_NAME)
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

.PHONY: bash
bash:  ## Open a bash console for cli service
	@$(DOCKER) exec -it -u $(CLI_USER) -e COLUMNS=$(shell tput cols) -e LINES=$(shell tput lines) $(shell $(DOCKER) ps --filter name='$(PROJECT_NAME)_cli' --format "{{ .ID }}") bash -il

.PHONY: drush
drush:  ## Run drush. Examples: "make drush status", "make drush -- --version"
	@$(DOCKER) exec -u $(CLI_USER) $(shell $(DOCKER) ps --filter name='$(PROJECT_NAME)_cli' --format "{{ .ID }}") bash -lc 'drush -r /var/www/docroot $(ARGS)'

.PHONY: db-backup
db-backup:  ## Create a timestamped database backup
	@echo "Creating DB backup in $(DB_BACKUP_FILE)"
	@make -s drush -- sql-dump | gzip -f > $(DB_BACKUP_FILE)

.PHONY: logs
logs:  ## Show stack logs. Examples: "make logs", "make logs web"
	@$(DOCKER_COMPOSE) logs -f $(ARGS)

.PHONY: network-disconnect
network-disconnect:
	@for network in $(shell $(DOCKER) network inspect -f '{{range .Containers}}{{.Name}} {{end}}' $(PROJECT_NETWORK_NAME)); do \
		$(DOCKER) network disconnect -f $(PROJECT_NETWORK_NAME) $$network; \
	done

.PHONY: show-config
show-config:  ## Print effective docker-compose configuration for the stack
	@$(DOCKER_COMPOSE) config

# https://stackoverflow.com/a/6273809/1826109
%:
	@:
