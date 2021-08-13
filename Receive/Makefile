run:
	dotnet run

run-rabbitmq:
	@echo "--> Launching RabbitMQ container"
	docker container run -d --rm \
		--name local_rabbitmq \
		-p 5672:5672 -p 15672:15672 rabbitmq:3.9-management

stop-rabbitmq:
	@echo "--> Removing RabbitMQ container"
	docker container stop local_rabbitmq
