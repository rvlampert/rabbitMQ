# Creating .NET Send and Receiver

We'll start by creating two .NET projects, one to work as a ```sender``` and one as a ```receiver```.

Creation of ```Sender```
```
dotnet new console -n Send
mv Send/Program.cs Send/Send.cs
```
Creation of ```receiver```
```
dotnet new console -n Receive
mv Receive/Program.cs Receive/Receive.cs
```
Once this is done, enter the respective Sender and Receiver directories

# Creating a makefile
Create a ```Makefile``` file for each of the projects, with the following commands to run the project
```
run:
    dotnet run
```
For each of the projects, add the ```RabbitMQ.Client``` dependency
```
dotnet add package RabbitMQ.Client
dotnet restore
```
Add the following lines to the Makefile to start a RabbitMQ instance via docker or end this instance

To start the instance:
```
run-rabbitmq:
    docker container run -d --rm \
        --name rabbitmq_location \
        -p 5672:5672 -p 15672:15672 rabbitmq:3.9-management
```
To end:
```
stop-rabbitmq:
    docker container stop local_rabbitmq
```
# Creating the Sender

The sender will be responsible for posting a ```hello world``` message to the rabbit queue.

We started by creating a connection with the server, in this example we will use _localhost_, but if the interest is to use another machine, just put the ip in place of localhost.

That done, we created a channel in a queue that we named ```hello```

In the ```Send.cs``` file, we start by adding the dependencies
```
using System;
using RabbitMQ.Client;
using System.Text;
```
we created a connection with the server and a channel, in this example we will use ```localhost"```, but if the interest is to use another machine, just put the ip in place of localhost.
```
var factory = new ConnectionFactory() { HostName = "localhost" };
using(var connection = factory.CreateConnection())
using(var channel = connection.CreateModel())
{
}
```
After that, within this channel, we created a queue that we named ```hello```
```
channel.QueueDeclare(queue: "hello",
                           durable: false,
                           exclusive: false,
                           autoDelete: false,
                           arguments: null);
Finally, we publish a "hello world" message in this queue
string message = ;
var body = Encoding.UTF8.GetBytes("Hello World!");
channel.BasicPublish(exchange: "",
                   routingKey: "hello",
                   basicProperties: null,
                   body: body);
Console.WriteLine(" [x] Sent {0}", "Hello World!");
```
The complete ```Send``` looks like this:
```
using System;
using RabbitMQ.Client;
using System.Text;
class send
{
    public static void Main()
    {
        var factory = new ConnectionFactory() { HostName = "localhost" };
        using(var connection = factory.CreateConnection())
        using(var channel = connection.CreateModel())
        {
            channel.QueueDeclare(queue: "hello",
                                durable: false,
                                exclusive: false,
                                autoDelete: false,
                                arguments: null);
            var body = Encoding.UTF8.GetBytes( "Hello World!");
            channel.BasicPublish(exchange: "",
                                routingKey: "hello",
                                basicProperties: null,
                                body: body);
            Console.WriteLine(" [x] Sent {0}", "Hello World!");
        }
        Console.WriteLine(" Press [enter] to exit.");
        Console.ReadLine();
    }
}
```
# Creating the Receiver
The receiver is answerable for opening a channel and listening to it until it is closed, it will keep checking the queue if there was any input and when it listens it will return the content. We start again by opening a connection and a channel and connecting to a queue, however now instead of publish it will consume

In the ```Receive.cs``` file, we start by adding the necessary dependencies
```
using RabbitMQ.Client;
using RabbitMQ.Client.Events;
using System;
using System.Text;
```
Then we open a connection and a channel, in this example we will use ```localhost```, but if the interest is to use another machine, just put the ip in place of localhost.
```
public static void Main()
    {
        var factory = new ConnectionFactory() { HostName = "localhost" };
        using(var connection = factory.CreateConnection())
        using(var channel = connection.CreateModel())
        {
        }
    }
```
After that, within this channel, we connect in the same ```hello``` queue
```
channel.QueueDeclare(queue: "hello", durable: false, exclusive: false, autoDelete: false, arguments: null);
Console.WriteLine(" [*] Waiting for messages.");
```
Finally, we created the receiver, which will be connected to the queue listening and printing what arrives.
```
var consumer = new EventingBasicConsumer(channel);
consumer.Received += (model, ea) =>
{
  var body = ea.Body.ToArray();
  var message = Encoding.UTF8.GetString(body);
  Console.WriteLine(" [x] Received {0}", message);
};
channel.BasicConsume(queue: "hello", autoAck: true, consumer: consumer);
Console.WriteLine(" Press [enter] to exit.");
Console.ReadLine();
```
The ```receiver.py``` should lock like this
```
using RabbitMQ.Client;
using RabbitMQ.Client.Events;
using System;
using System.Text;
class Receive
{
    public static void Main()
    {
        var factory = new ConnectionFactory() { HostName = "localhost" };
        using(var connection = factory.CreateConnection())
        using(var channel = connection.CreateModel())
        {
            channel.QueueDeclare(queue: "hello",
                                  durable: false,
                                  exclusive: false,
                                  autoDelete: false,
                                  arguments: null);
            Console.WriteLine(" [*] Waiting for messages.");
            var consumer = new EventingBasicConsumer(channel);
            consumer.Received += (model, ea) =>
            {
                var body = ea.Body.ToArray();
                var message = Encoding.UTF8.GetString(body);
                Console.WriteLine(" [x] Received {0}", message);
            };
            channel.BasicConsume(queue: "hello", autoAck: true, consumer: consumer);
            Console.WriteLine(" Press [enter] to exit.");
            Console.ReadLine();
        }
    }
}
```
