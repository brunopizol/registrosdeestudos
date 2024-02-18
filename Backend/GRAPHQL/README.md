# GraphQL em .NET

## estruturas de dados

As estruturas de dados no GraphQL em .NET são definidas através do uso de tipos GraphQL. Esses tipos são definidos usando a linguagem de schema do GraphQL, que é usada para descrever os tipos de dados que a API pode retornar.

Existem quatro tipos de dados básicos no GraphQL: scalars, enums, objetos e unions. Cada um desses tipos pode ser definido em uma classe C# correspondente, que implementa a interface `IGraphType`.

Por exemplo, para definir um objeto no GraphQL que representa um usuário, podemos criar uma classe C# que define as propriedades do usuário, como nome, idade e endereço:

```csharp
public class UserType : ObjectGraphType<User>
{
    public UserType()
    {
        Field(x => x.Name);
        Field(x => x.Age);
        Field(x => x.Address, type: typeof(AddressType));
    }
}

```

Nesse exemplo, a classe `UserType` estende a classe `ObjectGraphType<T>` e define três campos usando o método `Field`. O primeiro e o segundo campos são do tipo scalar, enquanto o terceiro campo é do tipo objeto. O tipo do objeto é definido em uma classe separada, `AddressType`.

Além disso, podemos definir enumerações no GraphQL, que são usadas para representar valores discretos, como status de pedido ou tipo de pagamento. Para isso, podemos criar uma classe C# que implementa a interface `IEnumerationGraphType`:

```csharp
public enum OrderStatus
{
    Pending,
    Processing,
    Shipped,
    Delivered,
    Cancelled
}

public class OrderStatusType : EnumerationGraphType<OrderStatus>
{
    public OrderStatusType()
    {
        Name = "OrderStatus";
        Description = "The status of an order";
    }
}

```

Nesse exemplo, a classe `OrderStatusType` implementa a interface `IEnumerationGraphType` e define os valores possíveis para a enumeração `OrderStatus`.

Esses são apenas alguns exemplos de como as estruturas de dados podem ser definidas no GraphQL em .NET, usando classes C# para representar tipos de dados. 

## Schemas

Em GraphQL, os schemas descrevem toda a funcionalidade disponível para a API, incluindo quais são os tipos de dados disponíveis, quais são as operações que podem ser realizadas e como os dados podem ser consultados. No .NET, os schemas são definidos usando a biblioteca [GraphQL.NET](http://graphql.net/).

Um exemplo de definição de schema em GraphQL em .NET é:

```csharp
var schema = Schema.For(@"
  type Query {
    hello: String
  }
");

```

Nesse exemplo, estamos criando um schema que define um tipo de consulta chamado `Query` com um campo `hello` que retorna uma string. O método `Schema.For` é usado para criar o schema com base na linguagem de schema do GraphQL.

Outro exemplo de schema em GraphQL em .NET é:

```csharp
var schema = new Schema
{
    Query = new ObjectGraphType
    {
        Name = "Query",
        Fields = new QueryFields()
        {
            {
                "hello",
                new FieldType
                {
                    Type = typeof(StringGraphType),
                    Resolve = context => "world"
                }
            }
        }
    }
};

```

Nesse exemplo, estamos criando um schema com um objeto de consulta `Query` que tem um campo `hello` que retorna uma string. Diferente do exemplo anterior, esse schema é criado programaticamente usando a biblioteca [GraphQL.NET](http://graphql.net/).

Em resumo, no GraphQL em .NET, os schemas são definidos usando a linguagem de schema do GraphQL ou programaticamente usando a biblioteca [GraphQL.NET](http://graphql.net/). Eles descrevem toda a funcionalidade disponível para a API, incluindo tipos de dados, operações e consultas possíveis.Em GraphQL, os schemas descrevem toda a funcionalidade disponível para a API, incluindo quais são os tipos de dados disponíveis, quais são as operações que podem ser realizadas e como os dados podem ser consultados. No .NET, os schemas são definidos usando a biblioteca [GraphQL.NET](http://graphql.net/).

Para adicionar novos tipos ao schema, podemos definir novas classes que estendem a classe `ObjectGraphType<T>`, que representa um tipo de objeto no GraphQL. Por exemplo, para adicionar um tipo de objeto `User` ao schema, podemos criar a classe `UserType`:

```csharp
public class UserType : ObjectGraphType<User>
{
    public UserType()
    {
        Name = "User";
        Field(x => x.Id, type: typeof(IdGraphType)).Description("The ID of the user.");
        Field(x => x.Name, type: typeof(StringGraphType)).Description("The name of the user.");
        Field(x => x.Age, type: typeof(IntGraphType)).Description("The age of the user.");
    }
}

```

Nesse exemplo, a classe `UserType` representa um tipo de objeto `User` no GraphQL. Ela define três campos usando o método `Field`: `Id`, `Name` e `Age`. Cada campo tem um tipo correspondente do GraphQL, como `IdGraphType`, `StringGraphType` e `IntGraphType`.

Além de tipos de objeto, podemos definir outros tipos de dados no schema, como enums e unions. Para adicionar um tipo de enumeração ao schema, podemos criar uma classe que implementa a interface `IEnumerationGraphType`. Por exemplo, para adicionar um tipo de enumeração `Status` ao schema, podemos criar a classe `StatusType`:

```csharp
public enum Status
{
    Pending,
    Approved,
    Rejected
}

public class StatusType : EnumerationGraphType<Status>
{
    public StatusType()
    {
        Name = "Status";
        Description = "The status of something.";
    }
}

```

Nesse exemplo, a classe `StatusType` representa um tipo de enumeração `Status` no GraphQL. Ela define três valores possíveis para a enumeração: `Pending`, `Approved` e `Rejected`.

## Resolvers

No GraphQL, os resolvers são responsáveis por buscar dados para um campo específico em um esquema. No .NET, os resolvedores são implementados como métodos em uma classe que herda de `ObjectGraphType<T>`.

Por exemplo, digamos que tenhamos um esquema que define um tipo `Usuário` com um campo `nome`:

```csharp
public class UserType : ObjectGraphType<User>
{
    public UserType()
    {
        Field(x => x.Name);
    }
}

```

Para buscar os dados do campo `nome`, precisamos definir um método resolver na classe `UserType`:

```csharp
public class UserType : ObjectGraphType<User>
{
    public UserType()
    {
        Field(x => x.Name, resolve: context => context.Source.GetName());
    }
}

```

Neste exemplo, estamos definindo um método resolver que recupera o nome do usuário usando o método `GetName()` no objeto `User`. O parâmetro `context` fornece acesso ao contexto de execução atual, incluindo o objeto de origem (`context.Source`).

Os resolvers também podem ser assíncronos. Por exemplo, digamos que tenhamos um esquema que define um tipo `Post` com um campo `comentários`:

```csharp
public class PostType : ObjectGraphType<Post>
{
    public PostType()
    {
        Field(x => x.Title);
        Field<ListGraphType<CommentType>>(
            "comments",
            resolve: async context =>
            {
                var comments = await myCommentService.GetCommentsAsync(context.Source.Id);
                return comments;
            });
    }
}

```

Neste exemplo, estamos definindo um método resolver que busca os comentários de um post usando um método assíncrono (`myCommentService.GetCommentsAsync()`). O parâmetro `context` ainda está disponível e pode ser usado para acessar o objeto de origem (`context.Source`).

Em resumo, os resolvers em GraphQL são responsáveis por buscar dados para campos específicos em um esquema. No .NET, os resolvedores são implementados como métodos em uma classe que herda de `ObjectGraphType<T>`. Os resolvedores podem ser síncronos ou assíncronos e podem acessar o contexto de execução atual.

## Queries e Mutations no GraphQL em .NET

O GraphQL em .NET suporta dois tipos de operações: queries e mutations. As queries são usadas para recuperar dados da API, enquanto as mutations são usadas para alterar dados na API.

### Queries

As queries no GraphQL em .NET são definidas como métodos em uma classe que herda de `ObjectGraphType<T>`. Cada método representa um campo na query e retorna o tipo de dados correspondente. Por exemplo, para definir uma query que retorna uma lista de usuários, podemos criar uma classe `QueryType` com um método `Users`:

```csharp
public class QueryType : ObjectGraphType
{
    public QueryType()
    {
        Field<ListGraphType<UserType>>(
            "users",
            resolve: context => myUserService.GetUsers());
    }
}

```

Nesse exemplo, estamos definindo um campo `users` na query que retorna uma lista de usuários (`ListGraphType<UserType>`). O método `resolve` é usado para especificar como os dados devem ser recuperados, usando um serviço fictício `myUserService`.

Podemos também passar argumentos para as queries. Por exemplo, para definir uma query que retorna um usuário específico com base no ID, podemos adicionar um argumento `id` ao método `Users`:

```csharp
public class QueryType : ObjectGraphType
{
    public QueryType()
    {
        Field<UserType>(
            "user",
            arguments: new QueryArguments(
                new QueryArgument<NonNullGraphType<IdGraphType>> { Name = "id" }),
            resolve: context =>
            {
                var id = context.GetArgument<int>("id");
                return myUserService.GetUser(id);
            });
    }
}

```

Nesse exemplo, estamos adicionando um argumento `id` à query, que é usado para recuperar um usuário específico usando o serviço fictício `myUserService`.

### Mutations

As mutations no GraphQL em .NET são definidas da mesma forma que as queries, como métodos em uma classe que herda de `ObjectGraphType<T>`. Cada método representa um campo na mutation e retorna o tipo de dados correspondente. Por exemplo, para definir uma mutation que cria um novo usuário, podemos criar uma classe `MutationType` com um método `CreateUser`:

```csharp
public class MutationType : ObjectGraphType
{
    public MutationType()
    {
        Field<UserType>(
            "createUser",
            arguments: new QueryArguments(
                new QueryArgument<NonNullGraphType<StringGraphType>> { Name = "name" },
                new QueryArgument<NonNullGraphType<IntGraphType>> { Name = "age" }),
            resolve: context =>
            {
                var name = context.GetArgument<string>("name");
                var age = context.GetArgument<int>("age");
                return myUserService.CreateUser(name, age);
            });
    }
}

```

Nesse exemplo, estamos definindo um campo `createUser` na mutation que cria um novo usuário com base no nome e na idade fornecidos como argumentos. O método `resolve` é usado para especificar como os dados devem ser criados, usando um serviço fictício `myUserService`.

Assim como nas queries, podemos passar argumentos para as mutations. Por exemplo, para definir uma mutation que atualiza um usuário existente com base no ID, podemos adicionar um argumento `id` ao método `UpdateUser`:

```csharp
public class MutationType : ObjectGraphType
{
    public MutationType()
    {
        Field<UserType>(
            "updateUser",
            arguments: new QueryArguments(
                new QueryArgument<NonNullGraphType<IdGraphType>> { Name = "id" },
                new QueryArgument<NonNullGraphType<StringGraphType>> { Name = "name" },
                new QueryArgument<NonNullGraphType<IntGraphType>> { Name = "age" }),
            resolve: context =>
            {
                var id = context.GetArgument<int>("id");
                var name = context.GetArgument<string>("name");
                var age = context.GetArgument<int>("age");
                return myUserService.UpdateUser(id, name, age);
            });
    }
}

```

Nesse exemplo, estamos adicionando um argumento `id` à mutation, que é usado para atualizar um usuário existente usando o serviço fictício `myUserService`.

Em resumo, o GraphQL em .NET suporta dois tipos de operações: queries e mutations. As queries são usadas para recuperar dados da API, enquanto as mutations são usadas para alterar dados na API. As queries e mutations são definidas como métodos em classes que herdam de `ObjectGraphType<T>`. As queries podem receber argumentos e retornar tipos de dados personalizados, enquanto as mutations podem receber argumentos e retornar o tipo de dados que foi alterado.

## validators e sanitizers

Em GraphQL, a validação e sanitização são importantes para garantir que os dados que são enviados e recebidos pela API estejam de acordo com as expectativas. No .NET, podemos usar a biblioteca [GraphQL.Validation](https://github.com/graphql-dotnet/validation) para realizar a validação e sanitização dos dados.

Para validar e sanitizar os dados em uma query ou mutation, podemos criar uma classe que implementa a interface `IDocumentValidator`. Essa classe deve ter um método `Validate` que recebe um documento GraphQL e retorna uma lista de erros de validação.

Por exemplo, vamos supor que queremos validar que uma mutation `CreateUser` receba um nome e uma idade válidos. Podemos criar a seguinte classe para validação:

```csharp
public class CreateUserValidator : IDocumentValidator
{
    public Task<ValidationResult> ValidateAsync(ValidationContext context)
    {
        var errors = new List<ValidationError>();

        var operation = context.Document.Operations.FirstOrDefault();

        if (operation != null && operation.OperationType == OperationType.Mutation)
        {
            var createUserField = operation.SelectionSet.Selections
                .OfType<Field>()
                .FirstOrDefault(f => f.Name.Value == "createUser");

            if (createUserField != null)
            {
                var nameArg = createUserField.Arguments.FirstOrDefault(a => a.Name.Value == "name");
                var ageArg = createUserField.Arguments.FirstOrDefault(a => a.Name.Value == "age");

                if (nameArg == null || string.IsNullOrWhiteSpace(nameArg.Value.ToString()))
                {
                    errors.Add(new ValidationError(
                        "createUser",
                        "Name is required.",
                        createUserField));
                }

                if (ageArg == null || !int.TryParse(ageArg.Value.ToString(), out int age))
                {
                    errors.Add(new ValidationError(
                        "createUser",
                        "Age is required and must be a valid integer.",
                        createUserField));
                }
            }
        }

        return Task.FromResult(new ValidationResult(errors));
    }
}

```

Nesse exemplo, estamos criando uma classe `CreateUserValidator` que implementa a interface `IDocumentValidator`. No método `ValidateAsync`, estamos verificando se a operação é uma mutation e se ela contém um campo `createUser`. Em seguida, estamos validando se o argumento `name` é obrigatório e se o argumento `age` é obrigatório e um inteiro válido.

Para usar essa classe de validação, podemos adicioná-la ao pipeline de validação do [GraphQL.NET](http://graphql.net/) da seguinte forma:

```csharp
var schema = Schema.For(@"
  type Query {
    hello: String
  }

  type Mutation {
    createUser(name: String!, age: Int!): User
  }

  type User {
    id: ID!
    name: String!
    age: Int!
  }
");

var validationRules = new List<IValidationRule>
{
    new CreateUserValidator()
};

var options = new ExecutionOptions
{
    Schema = schema,
    ValidationRules = validationRules,
    FieldMiddleware = new[] { new AuthorizationMiddleware() }
};

var result = await new DocumentExecuter().ExecuteAsync(options);

```

Nesse exemplo, estamos criando um schema com uma mutation `createUser` que recebe um nome e uma idade obrigatórios. Em seguida, estamos criando uma lista de regras de validação que contém a classe `CreateUserValidator`. Por fim, estamos configurando as opções de execução da query/mutation com o schema, as regras de validação e o middleware de autorização (que não está relacionado com a validação/sanitização).

Em resumo, podemos usar a biblioteca [GraphQL.Validation](https://github.com/graphql-dotnet/validation) para realizar a validação e sanitização dos dados em uma query ou mutation GraphQL em .NET. Para isso, podemos criar classes que implementam a interface `IDocumentValidator` e adicioná-las ao pipeline de validação do [GraphQL.NET](http://graphql.net/)

## Otimização de queries com fragmentos, batching e paginação

Otimização de queries é um aspecto importante em qualquer API, e o GraphQL em .NET oferece várias técnicas para melhorar o desempenho e a eficiência das queries.

Uma das técnicas mais úteis é o uso de fragmentos (fragments) para reutilizar partes comuns de queries. Por exemplo, digamos que temos uma query que retorna informações sobre um usuário, incluindo seu nome, idade e endereço. Podemos definir um fragmento `UserFragment` para representar esses campos:

```graphql
fragment UserFragment on User {
  name
  age
  address {
    street
    city
    state
    country
  }
}

```

Em seguida, podemos reutilizar esse fragmento em várias queries diferentes para recuperar informações sobre o usuário:

```graphql
query GetUserById($id: ID!) {
  user(id: $id) {
    ...UserFragment
  }
}

query GetUsers {
  users {
    ...UserFragment
  }
}

```

O uso de fragmentos pode reduzir significativamente o tamanho das queries e melhorar o desempenho da API.

Outra técnica útil é a busca de lotes (batching) para reduzir o número de chamadas de rede necessárias para recuperar várias entidades relacionadas. Por exemplo, digamos que temos uma query que retorna informações sobre um usuário e seus pedidos:

```graphql
query GetUserAndOrders($userId: ID!) {
  user(id: $userId) {
    name
    orders {
      id
      date
      status
    }
  }
}

```

Se tivermos vários usuários para recuperar, podemos usar a busca de lotes para enviar uma única chamada de rede que retorna informações sobre todos os usuários e seus pedidos em uma única resposta:

```graphql
query GetUsersAndOrders($userIds: [ID!]!) {
  users(ids: $userIds) {
    name
    orders {
      id
      date
      status
    }
  }
}

```

O uso de busca de lotes pode reduzir significativamente o tempo de resposta da API e melhorar a experiência do usuário.

Finalmente, a paginização é uma técnica comum para lidar com grandes conjuntos de dados. No GraphQL em .NET, podemos usar a diretiva `@skip` e `@first` para implementar a paginização em nossas queries. Por exemplo, digamos que temos uma query que retorna uma lista de usuários:

```graphql
query GetUsers($first: Int!, $skip: Int!) {
  users(first: $first, skip: $skip) {
    id
    name
    email
  }
}

```

Podemos usar as variáveis `first` e `skip` para controlar o número de resultados retornados e o número de resultados ignorados. Isso nos permite implementar a paginização em nossa API e melhorar o desempenho ao lidar com grandes conjuntos de dados.

Em resumo, o GraphQL em .NET oferece várias técnicas para otimizar e melhorar o desempenho de nossas queries, incluindo o uso de fragmentos, busca de lotes e paginização. Ao aplicar essas técnicas, podemos criar APIs mais eficientes e escaláveis que atendam às necessidades de nossos usuários.

## Documentação graphql

A documentação em GraphQL é uma parte crucial do desenvolvimento de APIs, pois permite que os consumidores entendam os tipos de dados disponíveis, os campos que podem ser consultados, os argumentos que podem ser passados e outras informações essenciais para interagir com o esquema GraphQL. No contexto do desenvolvimento em .NET, você pode criar documentação clara e abrangente para suas APIs GraphQL usando ferramentas e práticas recomendadas. Vou explicar como fazer isso com exemplos usando o ecossistema GraphQL no .NET.

1. **Usando o GraphQL Playground ou GraphiQL:**

O GraphQL Playground e o GraphiQL são interfaces interativas que permitem explorar e testar suas APIs GraphQL enquanto fornecem documentação automática sobre os tipos e campos disponíveis. Ao criar suas classes de tipo e definir campos com resolvers, a documentação gerada automaticamente ajuda os usuários a entender como usar sua API. Aqui está um exemplo simples:

```csharp
public class UserType : ObjectGraphType<User>
{
    public UserType()
    {
        Field(x => x.Id);
        Field(x => x.Name);
        Field(x => x.Email);
    }
}

```

Quando os usuários acessam o GraphQL Playground ou o GraphiQL, eles verão automaticamente os campos `Id`, `Name` e `Email` do tipo `User`, juntamente com seus tipos de dados e descrições.

1. **Adicionando descrições aos campos:**

Você pode adicionar descrições aos campos dos seus tipos para fornecer informações adicionais aos usuários. Isso torna a documentação mais rica e compreensível. Veja um exemplo:

```csharp
public class UserType : ObjectGraphType<User>
{
    public UserType()
    {
        Field(x => x.Id, nullable: false, description: "ID do usuário");
        Field(x => x.Name, nullable: true, description: "Nome do usuário");
        Field(x => x.Email, nullable: true, description: "Endereço de e-mail do usuário");
    }
}

```

1. **Usando o pacote GraphQL.Types.Federation:**

Se você estiver trabalhando com um esquema GraphQL federado, pode usar o pacote `GraphQL.Types.Federation` para adicionar informações de federação à sua documentação. Isso permite que outros serviços descubram e interajam com sua API federada. Consulte a documentação do pacote para obter mais informações e exemplos.

1. **Integrando com Swagger ou outras ferramentas de documentação:**

Você também pode usar ferramentas de documentação de API, como o Swagger, para gerar documentação adicional e interativa para suas APIs GraphQL no .NET. Embora o Swagger seja mais associado a APIs REST, existem pacotes e soluções para integração com GraphQL também.

Lembre-se de que a documentação é uma parte essencial de qualquer API, incluindo GraphQL. Ao criar e manter uma documentação clara e precisa, você ajuda os consumidores a entender e utilizar efetivamente sua API GraphQL no ecossistema .NET.

## Testes Unitarios em GraphQL

Testes unitários são uma prática fundamental no desenvolvimento de APIs GraphQL em .NET, pois garantem que suas operações, resolvers e tipos estejam funcionando conforme o esperado. Vou abordar como realizar testes unitários abrangendo queries, input types, schemas e mutations usando algumas das principais bibliotecas de testes.

Vamos considerar o uso das seguintes bibliotecas:

- [xUnit.NET](http://xunit.net/): Uma popular estrutura de testes unitários.
- Moq: Uma biblioteca de simulação para criar objetos simulados.

Vamos começar com exemplos de testes unitários para cada uma das áreas mencionadas.

1. **Queries:**

Considere uma operação de consulta (query) para recuperar informações de um usuário por ID:

```csharp
public class UserQuery : ObjectGraphType
{
    public UserQuery(IUserService userService)
    {
        Field<UserType>(
            "user",
            arguments: new QueryArguments(new QueryArgument<NonNullGraphType<IntGraphType>> { Name = "id", Description = "ID do usuário" }),
            resolve: context =>
            {
                var userId = context.GetArgument<int>("id");
                return userService.GetUserById(userId);
            }
        );
    }
}

```

Aqui estão os testes unitários usando [xUnit.NET](http://xunit.net/) e Moq:

```csharp
public class UserQueryTests
{
    [Fact]
    public void GetUserById_Should_Return_User()
    {
        // Arrange
        var userServiceMock = new Mock<IUserService>();
        userServiceMock.Setup(service => service.GetUserById(It.IsAny<int>()))
                      .Returns(new User { Id = 1, Name = "John" });

        var query = new UserQuery(userServiceMock.Object);
        var schema = new Schema { Query = query };

        // Act
        var result = schema.ExecuteAsync("{ user(id: 1) { id name } }").Result;

        // Assert
        var expectedResult = "{ 'user': { 'id': 1, 'name': 'John' } }".Replace("'", "\\"");
        result.Should().BeEquivalentTo(expectedResult);
    }
}

```

1. **Input Types:**

Vamos supor que você tenha uma mutation que cria um novo usuário usando um Input Type:

```csharp
public class UserMutation : ObjectGraphType
{
    public UserMutation(IUserService userService)
    {
        Field<UserType>(
            "createUser",
            arguments: new QueryArguments(new QueryArgument<NonNullGraphType<UserInputType>> { Name = "user" }),
            resolve: context =>
            {
                var userInput = context.GetArgument<UserInput>("user");
                return userService.CreateUser(userInput);
            }
        );
    }
}

```

Aqui estão os testes unitários correspondentes:

```csharp
public class UserMutationTests
{
    [Fact]
    public void CreateUser_Should_Return_New_User()
    {
        // Arrange
        var userServiceMock = new Mock<IUserService>();
        userServiceMock.Setup(service => service.CreateUser(It.IsAny<UserInput>()))
                      .Returns(new User { Id = 1, Name = "Jane" });

        var mutation = new UserMutation(userServiceMock.Object);
        var schema = new Schema { Mutation = mutation };

        // Act
        var result = schema.ExecuteAsync("mutation { createUser(user: { name: \\"Jane\\" }) { id name } }").Result;

        // Assert
        var expectedResult = "{ 'createUser': { 'id': 1, 'name': 'Jane' } }".Replace("'", "\\"");
        result.Should().BeEquivalentTo(expectedResult);
    }
}

```

1. **Schemas:**

Para testar schemas, você pode usar o `SchemaPrinter` para comparar as representações do esquema:

```csharp
public class SchemaTests
{
    [Fact]
    public void Schema_Should_Have_Expected_Representation()
    {
        // Arrange
        var expectedSchema = @"
            type Query {
              user(id: Int!): UserType
            }

            type UserType {
              id: Int!
              name: String
            }
        ".Replace("\\r\\n", "\\n");

        // Act
        var actualSchema = new Schema { Query = new UserQuery(null) };
        var printedSchema = SchemaPrinter.Print(actualSchema).Replace("\\r\\n", "\\n");

        // Assert
        printedSchema.Should().Be(expectedSchema);
    }
}

```

1. **Mutations:**

Para testar mutations, você pode seguir um padrão semelhante aos exemplos anteriores, garantindo que a mutação retorne os resultados esperados.