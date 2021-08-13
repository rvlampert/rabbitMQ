# rabbitMQ
A sender and a receiver tha post and read from a RabbitMQ queue

Link : https://www.rabbitmq.com/tutorials/tutorial-one-dotnet.html

# Objective
The objective of this project is to create a sender that post a message in a rabbitMQ queue and  to create a receiver that get this message and show on console

# How to run
Raise rabbitMQ via docker with ```make run-rabitmq``` command,

Run the receiver with the ```make run``` command in the receiver project

Run the sender with the ```make run``` command in the sender project 

The sender must send the message "hello world" to the receiver and the receiver must print it on the screen

*It is also possible to follow the entries and exits of the queues through the url http://localhost:15672/#/queues