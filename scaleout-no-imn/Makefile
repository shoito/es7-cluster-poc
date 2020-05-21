NETWORK_NAME=elastic
SCALE_35_YAML=docker-compose.35.yml
SCALE_6_YAML=docker-compose.6.yml

.PHONY: build
build:
	docker-compose build
	docker-compose -f $(SCALE_35_YAML) build

.PHONY: up
up:
	@if [ -z "`docker network ls | grep $(NETWORK_NAME)`" ]; then docker network create $(NETWORK_NAME); fi
	docker-compose up -d

.PHONY: down
down:
	docker-compose down --rmi all --volumes
	@if [ -n "`docker network inspect $(NETWORK_NAME) | grep \"\\"Containers\\": {}\"`" ]; then docker network rm $(NETWORK_NAME); fi

.PHONY: scale-out
scale-out:
	docker-compose -f $(SCALE_35_YAML) up -d

.PHONY: scale-in
scale-in:
	docker-compose -f $(SCALE_35_YAML) down --rmi all --volumes

.PHONY: clean
clean:
	docker-compose -f $(SCALE_6_YAML) down --rmi all --volumes
	@make scale-in
	@make down