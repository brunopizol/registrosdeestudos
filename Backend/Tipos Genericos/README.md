# Tipos genericos em .NET

### 1. **Entendendo os Conceitos Básicos**

### 1.1 List<T>

`List<T>` é uma coleção genérica no .NET que permite armazenar uma lista de elementos do tipo `T`.

### 1.2 Tipos Genéricos (<T>)

Os tipos genéricos, representados por `<T>`, permitem que você escreva código flexível e reutilizável, adaptando-o a diferentes tipos de dados.

### 2. **Usando List<T>**

### 2.1 Criando uma Lista

Para criar uma lista, primeiro você precisa importar o namespace `System.Collections.Generic` e, em seguida, pode declarar uma lista assim:

```csharp
using System.Collections.Generic;

List<int> numeros = new List<int>();

```

### 2.2 Adicionando e Removendo Elementos

Para adicionar elementos à lista, você pode usar o método `Add()`:

```csharp
numeros.Add(1);
numeros.Add(2);
numeros.Add(3);

```

Para remover elementos, você pode usar o método `Remove()`:

```csharp
numeros.Remove(2); // Remove o número 2 da lista

```

### 3. **Classes e Interfaces Genéricas**

### 3.1 Classes Genéricas

Você pode criar classes genéricas para reutilização de código. Um exemplo seria uma classe genérica para representar uma entidade:

```csharp
public class Entidade<T>
{
    public T Id { get; set; }
    public string Nome { get; set; }
}

```

### 3.2 Interfaces Genéricas

Interfaces também podem ser genéricas. Por exemplo, uma interface genérica para um repositório:

```csharp
public interface IRepository<T>
{
    void Inserir(T item);
    T BuscarPorId(int id);
    void Atualizar(T item);
    void Deletar(T item);
}

```

### 4. **Classes de Serviço Genéricas**

Você pode criar classes de serviço genéricas que operam em entidades genéricas. Suponha que você tenha uma classe de serviço `EntityService<T>`:

```csharp
public class EntityService<T>
{
    private readonly IRepository<T> _repository;

    public EntityService(IRepository<T> repository)
    {
        _repository = repository;
    }

    public void Criar(T item)
    {
        _repository.Inserir(item);
    }

    public T BuscarPorId(int id)
    {
        return _repository.BuscarPorId(id);
    }

    // Outros métodos de serviço...
}

```

### 5. **Queries Genéricas em Repositórios**

Você pode criar métodos genéricos em seus repositórios para executar consultas em entidades genéricas. Aqui está um exemplo simples:

```csharp
public class EntityRepository<T> : IRepository<T>
{
    private readonly List<T> _dados = new List<T>();

    public void Inserir(T item)
    {
        _dados.Add(item);
    }

    public T BuscarPorId(int id)
    {
        // Lógica para buscar por ID
    }

    public void Atualizar(T item)
    {
        // Lógica para atualizar
    }

    public void Deletar(T item)
    {
        // Lógica para deletar
    }

    // Método genérico para executar uma consulta
    public IEnumerable<T> Consultar(Func<T, bool> condicao)
    {
        return _dados.Where(condicao);
    }
}

```

### 6. **Benefícios da Abordagem Genérica**

- **Reutilização de Código:** A abordagem genérica permite que você escreva um código mais reutilizável, pois se aplica a diferentes tipos de entidades.
- **Type Safety:** Os tipos genéricos fornecem segurança de tipos, evitando erros de compilação relacionados a tipos incorretos.
- **Flexibilidade:** Você pode criar serviços e repositórios genéricos para operar em várias entidades sem duplicação de código.