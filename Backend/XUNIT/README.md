# xUnit basico

Vamos supor que estamos construindo um sistema de gerenciamento de pedidos com as seguintes camadas: Domain, Application, Infrastructure e Presentation.

**Passo 1: Configuração do Projeto:**
Certifique-se de que o projeto tenha referências às bibliotecas necessárias do [xUnit.net](http://xunit.net/) e outras dependências. Use o NuGet Package Manager para adicionar as referências.

**Passo 2: Escrevendo Testes para a Camada de Domínio:**
A camada de domínio é o coração da aplicação. Vamos criar testes para as entidades, agregados, objetos de valor e serviços do domínio.

Exemplo de teste para uma entidade `Order`:

```csharp
using Xunit;
using YourDomain.Entities;

namespace YourDomain.Tests
{
    public class OrderTests
    {
        [Fact]
        public void CanCreateOrder()
        {
            // Arrange
            var orderId = new OrderId();
            var order = new Order(orderId);

            // Assert
            Assert.Equal(orderId, order.Id);
        }
    }
}

```

**Passo 3: Testando Serviços de Aplicação:**
A camada de aplicação coordena as ações entre a camada de domínio e a camada de infraestrutura. Teste os casos de uso e os serviços de aplicação.

Exemplo de teste para um serviço de aplicação `OrderService`:

```csharp
using Xunit;
using YourApplication.Services;

namespace YourApplication.Tests
{
    public class OrderServiceTests
    {
        [Fact]
        public void CanPlaceOrder()
        {
            // Arrange
            var orderService = new OrderService();

            // Act
            var orderId = orderService.PlaceOrder();

            // Assert
            Assert.NotNull(orderId);
        }
    }
}

```

**Passo 4: Testando a Camada de Infraestrutura:**
A camada de infraestrutura lida com detalhes técnicos, como acesso a banco de dados, envio de e-mails, etc. Aqui, você pode usar técnicas como testes de integração ou mocks para isolar o código.

Exemplo de teste de integração para um repositório `OrderRepository` usando um banco de dados em memória:

```csharp
using Xunit;
using YourInfrastructure.Repositories;

namespace YourInfrastructure.Tests
{
    public class OrderRepositoryTests
    {
        [Fact]
        public void CanAddOrderToDatabase()
        {
            // Arrange
            var dbContext = InMemoryDbContext.Create(); // Crie um banco de dados em memória
            var orderRepository = new OrderRepository(dbContext);
            var order = new Order(new OrderId());

            // Act
            orderRepository.Add(order);
            dbContext.SaveChanges();

            // Assert
            var savedOrder = dbContext.Orders.Find(order.Id);
            Assert.NotNull(savedOrder);
        }
    }
}

```

**Passo 5: Testando a Camada de Apresentação:**
A camada de apresentação lida com a interação do usuário. Teste os controladores, view models, etc.

Exemplo de teste para um controlador `OrderController`:

```csharp
using Xunit;
using Microsoft.AspNetCore.Mvc;
using YourPresentation.Controllers;
using YourApplication.Services;

namespace YourPresentation.Tests
{
    public class OrderControllerTests
    {
        [Fact]
        public void CanPlaceOrder()
        {
            // Arrange
            var orderService = new OrderService();
            var controller = new OrderController(orderService);

            // Act
            var result = controller.PlaceOrder();

            // Assert
            var viewResult = Assert.IsType<ViewResult>(result);
            Assert.NotNull(viewResult.Model);
        }
    }
}

```

**Passo 6: Executando os Testes:**
Execute os testes usando ferramentas como `dotnet test` ou diretamente de sua IDE.