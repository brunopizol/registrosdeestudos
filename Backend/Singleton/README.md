# Singleton Pattern C# #

O Singleton é um padrão de projeto de software que garante a existência de apenas uma única instância de uma determinada classe e fornece um ponto global de acesso a essa instância. Ele é útil quando você deseja controlar rigorosamente como uma única instância de uma classe é criada e compartilhada em todo o código.

O Singleton é implementado garantindo que a classe possua um construtor privado para evitar a criação de instâncias externamente. Além disso, a classe possui um método estático para acessar a única instância, que é criada na primeira chamada desse método e armazenada para futuras referências.

Aqui está um exemplo de implementação do padrão Singleton em C#:

```csharp
public class Singleton
{
    private static Singleton instance;

    // Construtor privado para evitar criação externa de instâncias
    private Singleton()
    {
        // Inicialização da instância
    }

    // Método estático para acessar a única instância
    public static Singleton Instance
    {
        get
        {
            // Se a instância ainda não foi criada, cria-a
            if (instance == null)
            {
                instance = new Singleton();
            }
            return instance;
        }
    }

    // Métodos e propriedades da classe
}

```

Agora, vamos ver como usar a classe Singleton:

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Acesso à instância única
        Singleton instance1 = Singleton.Instance;
        Singleton instance2 = Singleton.Instance;

        // Verificação de igualdade das instâncias
        Console.WriteLine(instance1 == instance2); // Saída: True
    }
}

```

Neste exemplo, a classe `Singleton` garante que apenas uma única instância seja criada. Quando `Singleton.Instance` é acessado pela primeira vez, a instância é criada e retornada. Nas chamadas subsequentes, a mesma instância é retornada, demonstrando assim o comportamento singleton.

É importante observar que essa implementação é simples e não é thread-safe. Se você estiver trabalhando em um ambiente multithread, poderá precisar adicionar sincronização para garantir que a criação da instância seja segura para threads. Além disso, considerar implementar uma versão thread-safe usando o padrão de bloqueio duplo para otimização, ou utilizar o tipo `Lazy<T>` do .NET para garantir a criação preguiçosa e segura da instância única.

em um cenário onde você deseja criar uma classe **`DatabaseConnection`** que represente uma conexão única com um banco de dados:

```csharp

using System;

public class DatabaseConnection
{
    private static DatabaseConnection instance;

    // Simulação de informações de conexão
    private string connectionString;

    // Construtor privado para evitar criação externa de instâncias
    private DatabaseConnection()
    {
        connectionString = "your_connection_string_here";
        Console.WriteLine("Database connection established.");
    }

    // Método estático para acessar a única instância
    public static DatabaseConnection Instance
    {
        get
        {
            // Se a instância ainda não foi criada, cria-a
            if (instance == null)
            {
                instance = new DatabaseConnection();
            }
            return instance;
        }
    }

    // Métodos e propriedades da classe
    public void ExecuteQuery(string query)
    {
        Console.WriteLine($"Executing query: {query}");
        // Simulação de execução de consulta no banco de dados
    }
}

```

Aqui está um exemplo de uso da classe **`DatabaseConnection`**:

```csharp

class Program
{
    static void Main(string[] args)
    {
        // Acesso à instância única da conexão com o banco de dados
        DatabaseConnection dbConnection1 = DatabaseConnection.Instance;
        DatabaseConnection dbConnection2 = DatabaseConnection.Instance;

        // Verificação de igualdade das instâncias
        Console.WriteLine(dbConnection1 == dbConnection2); // Saída: True

        // Executando uma consulta no banco de dados
        dbConnection1.ExecuteQuery("SELECT * FROM Customers");

        // A saída será semelhante a:
        // Database connection established.
        // Executing query: SELECT * FROM Customers
    }
}

```

Neste exemplo, a classe **`DatabaseConnection`** é implementada como um Singleton. Ela garante que apenas uma única conexão com o banco de dados seja criada e reutilizada em todo o código. Isso pode ser útil para evitar múltiplas conexões desnecessárias ao banco de dados e para centralizar a lógica de gerenciamento de conexões em um único ponto.
