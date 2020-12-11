NETWORK_NAME=xpack-security

.PHONY: build
build:
	docker-compose build

.PHONY: up
up:
	@if [ -z "`docker network ls | grep $(NETWORK_NAME)`" ]; then docker network create $(NETWORK_NAME); fi
	docker-compose up -d

.PHONY: down
down:
	docker-compose down --rmi all --volumes
	@if [ -n "`docker network inspect $(NETWORK_NAME) | grep \"\\"Containers\\": {}\"`" ]; then docker network rm $(NETWORK_NAME); fi

.PHONY: clean
clean:
	@make down