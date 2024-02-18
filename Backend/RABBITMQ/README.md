# **Documentação do RabbitMQ com .NET**

## **Introdução**

O RabbitMQ é um sistema de mensagens de código aberto amplamente utilizado para a troca de mensagens em sistemas distribuídos. O RabbitMQ é conhecido por sua confiabilidade e flexibilidade, tornando-o ideal para cenários de comunicação assíncrona.

Neste guia, você aprenderá como:

1. Configurar um ambiente RabbitMQ.
2. Produzir mensagens para uma fila.
3. Consumir mensagens de uma fila, incluindo o uso de mensagens assíncronas e síncronas.
4. Executar Ack() e Nack() para confirmar ou rejeitar mensagens.
5. Lidar com dead letters.
6. Enviar mensagens em lotes.
7. Gerenciar o tempo de vida das mensagens.

## **Configuração do Ambiente**

### **Passo 1: Instale o RabbitMQ**

Primeiro, você precisa instalar o RabbitMQ no seu sistema. Siga as instruções de instalação no site oficial do RabbitMQ: **https://www.rabbitmq.com/download.html**.

### **Passo 2: Inicie o RabbitMQ**

Inicie o RabbitMQ após a instalação. Você pode usar o seguinte comando:

```bash
bashCopy code
rabbitmq-server

```

### **Passo 3: Crie uma Fila**

Você precisará criar uma fila para produzir e consumir mensagens. Use a interface web do RabbitMQ ou o RabbitMQ Management Plugin para criar uma fila chamada **`minha-fila`**.

## **Produzindo Mensagens**

### **Exemplo de Produção de Mensagens**

```csharp
csharpCopy code
using RabbitMQ.Client;
using System;
using System.Text;

class Program
{
    static void Main(string[] args)
    {
        var factory = new ConnectionFactory() { HostName = "localhost" };

        using (var connection = factory.CreateConnection())
        using (var channel = connection.CreateModel())
        {
            channel.QueueDeclare(queue: "minha-fila", durable: false, exclusive: false, autoDelete: false, arguments: null);

            string mensagem = "Mensagem de exemplo";
            var body = Encoding.UTF8.GetBytes(mensagem);

            channel.BasicPublish(exchange: "", routingKey: "minha-fila", basicProperties: null, body: body);
            Console.WriteLine($"Mensagem enviada: {mensagem}");
        }
    }
}

```

## **Consumindo Mensagens**

### **Exemplo de Consumo de Mensagens Síncrono**

```csharp
csharpCopy code
using RabbitMQ.Client;
using RabbitMQ.Client.Events;
using System;
using System.Text;

class Program
{
    static void Main(string[] args)
    {
        var factory = new ConnectionFactory() { HostName = "localhost" };

        using (var connection = factory.CreateConnection())
        using (var channel = connection.CreateModel())
        {
            channel.QueueDeclare(queue: "minha-fila", durable: false, exclusive: false, autoDelete: false, arguments: null);

            var consumer = new EventingBasicConsumer(channel);

            consumer.Received += (model, ea) =>
            {
                var body = ea.Body.ToArray();
                var mensagem = Encoding.UTF8.GetString(body);
                Console.WriteLine($"Mensagem recebida: {mensagem}");

                // Confirme a mensagem com Ack()
                channel.BasicAck(deliveryTag: ea.DeliveryTag, multiple: false);
            };

            channel.BasicConsume(queue: "minha-fila", autoAck: false, consumer: consumer);

            Console.WriteLine("Pressione qualquer tecla para sair.");
            Console.ReadLine();
        }
    }
}

```

### **Exemplo de Consumo de Mensagens Assíncrono**

```csharp
csharpCopy code
using RabbitMQ.Client;
using RabbitMQ.Client.Events;
using System;
using System.Text;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        var factory = new ConnectionFactory() { HostName = "localhost" };

        using (var connection = factory.CreateConnection())
        using (var channel = connection.CreateModel())
        {
            channel.QueueDeclare(queue: "minha-fila", durable: false, exclusive: false, autoDelete: false, arguments: null);

            var consumer = new AsyncEventingBasicConsumer(channel);

            consumer.Received += async (model, ea) =>
            {
                var body = ea.Body.ToArray();
                var mensagem = Encoding.UTF8.GetString(body);
                Console.WriteLine($"Mensagem recebida: {mensagem}");

                // Confirme a mensagem com Ack()
                channel.BasicAck(deliveryTag: ea.DeliveryTag, multiple: false);

                await Task.Yield();
            };

            channel.BasicConsume(queue: "minha-fila", autoAck: false, consumer: consumer);

            Console.WriteLine("Pressione qualquer tecla para sair.");
            Console.ReadLine();
        }
    }
}

```

## **Confirmação e Rejeição de Mensagens**

O RabbitMQ permite que você confirme ou rejeite mensagens após o consumo. Use **`Ack()`** para confirmar uma mensagem e **`Nack()`** para rejeitá-la.

### **Confirmação de Mensagens**

```csharp

channel.BasicAck(deliveryTag: ea.DeliveryTag, multiple: false); // Confirma a mensagem após o consumo bem-sucedido

```

### **Rejeição de Mensagens**

```csharp

channel.BasicNack(deliveryTag: ea.DeliveryTag, multiple: false, requeue: true); // Rejeita a mensagem e a coloca de volta na fila

```

## **Dead Letters**

Dead letters são mensagens que não puderam ser processadas com sucesso. Você pode configurar uma fila de dead letter para lidar com essas mensagens.

```csharp

var args = new Dictionary<string, object>
{
    { "x-dead-letter-exchange", "" },
    { "x-dead-letter-routing-key", "minha-fila-dead-letter" }
};

channel.QueueDeclare(queue: "minha-fila", durable: false, exclusive: false, autoDelete: false, arguments: args);
channel.QueueDeclare(queue: "minha-fila-dead-letter", durable: false, exclusive: false, autoDelete: false, arguments: null);

// Configure o tempo de vida da mensagem e a política de dead letter
var properties = channel.CreateBasicProperties();
properties.Expiration = "3600000"; // 1 hora

```

## **Envio de Mensagens em Lotes**

Você pode enviar mensagens em lotes para melhorar o desempenho.

```csharp

for (int i = 0; i < 1000; i++)
{
    var mensagem = $"Mensagem {i}";
    var body = Encoding.UTF8.GetBytes(mensagem);
    channel.BasicPublish(exchange: "", routingKey: "minha-fila", basicProperties: null, body: body);
}

// Certifique-se de aguardar a conclusão dos envios de lotes
channel.WaitForConfirmsOrDie(TimeSpan.FromSeconds(10));

```

## **Gerenciamento do Tempo de Vida das Mensagens**

O RabbitMQ permite configurar o tempo de vida das mensagens usando a propriedade **`expiration`**.

```csharp

var properties = channel.CreateBasicProperties();
properties.Expiration = "3600000"; // Define o tempo de vida para 1 hora (em milissegundos)

```

## **Produzindo Mensagens com MassTransit**

### **Configuração do MassTransit**

Antes de produzir mensagens, adicione o MassTransit ao seu projeto. Utilize o NuGet Package Manager Console com o comando:

```bash
Install-Package MassTransit

```

### **Exemplo de Produção de Mensagens com MassTransit**

```csharp
using System;
using System.Threading.Tasks;
using MassTransit;

class Program
{
    static async Task Main(string[] args)
    {
        var busControl = Bus.Factory.CreateUsingRabbitMq(cfg =>
        {
            var host = cfg.Host(new Uri("rabbitmq://localhost"), h => { });

            cfg.Send<Message>(x => x.UsePublishAcknowledge());
        });

        await busControl.StartAsync();

        try
        {
            var endpoint = await busControl.GetSendEndpoint(new Uri("rabbitmq://localhost/minha-fila"));

            await endpoint.Send(new Message { Text = "Mensagem de exemplo" });

            Console.WriteLine("Mensagem enviada com MassTransit");
        }
        finally
        {
            await busControl.StopAsync();
        }
    }
}

public class Message
{
    public string Text { get; set; }
}

```

## **Consumindo Mensagens com MassTransit**

### **Exemplo de Consumo de Mensagens Assíncrono com MassTransit**

```csharp
using System;
using System.Threading.Tasks;
using MassTransit;

class Program
{
    static async Task Main(string[] args)
    {
        var busControl = Bus.Factory.CreateUsingRabbitMq(cfg =>
        {
            var host = cfg.Host(new Uri("rabbitmq://localhost"), h => { });

            cfg.ReceiveEndpoint(host, "minha-fila", ep =>
            {
                ep.Handler<Message>(context =>
                {
                    var mensagem = context.Message.Text;
                    Console.WriteLine($"Mensagem recebida: {mensagem}");
                    return Task.CompletedTask;
                });
            });
        });

        await busControl.StartAsync();

        try
        {
            Console.WriteLine("Aguardando mensagens. Pressione qualquer tecla para sair.");
            Console.ReadLine();
        }
        finally
        {
            await busControl.StopAsync();
        }
    }
}

public class Message
{
    public string Text { get; set; }
}

```

## **Confirmação e Rejeição de Mensagens com MassTransit**

Ao utilizar MassTransit, a confirmação e rejeição de mensagens são gerenciadas automaticamente.

```csharp
ep.Handler<Message>(context =>
{
    var mensagem = context.Message.Text;
    Console.WriteLine($"Mensagem recebida: {mensagem}");
    return Task.CompletedTask; // Acknowledgement automático
});

```

## **Dead Letters e MassTransit**

Configure dead letters em MassTransit para lidar com mensagens não processadas.

```csharp
ep.DeadLetterExchange = "minha-fila-dead-letter";
ep.DeadLetterQueueName = "minha-fila-dead-letter";

```

## **Envio de Mensagens em Lotes com MassTransit**

MassTransit facilita o envio de mensagens em lotes.

```csharp
await endpoint.Send<Message>(new List<Message>
{
    new Message { Text = "Mensagem 1" },
    new Message { Text = "Mensagem 2" },
    // ... adicione mais mensagens conforme necessário
});

```

## **Gerenciamento do Tempo de Vida das Mensagens com MassTransit**

O tempo de vida das mensagens é configurado automaticamente pelo MassTransit, utilizando a expiração configurada na fila do RabbitMQ.

```csharp
cfg.ReceiveEndpoint(host, "minha-fila", ep =>
{
    ep.Message<Message>(m => m.TimeToLive = TimeSpan.FromHours(1));
});

```