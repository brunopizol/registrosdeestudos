# Guia Completo de Uso do Entity Framework em .NET

O Entity Framework (EF) é um framework de mapeamento objeto-relacional (ORM) que simplifica o acesso e manipulação de dados em bancos de dados relacionais usando objetos .NET. Neste guia, você aprenderá desde o básico até as funcionalidades avançadas do Entity Framework.

## Índice

1. [Introdução ao Entity Framework](https://www.notion.so/Entity-Framework-fd7693645cd54071b52de0abd07e1087?pvs=21)
2. [Configuração do Projeto](https://www.notion.so/Entity-Framework-fd7693645cd54071b52de0abd07e1087?pvs=21)
3. [Definindo Modelos de Dados](https://www.notion.so/Entity-Framework-fd7693645cd54071b52de0abd07e1087?pvs=21)
4. [Consultas e Operações CRUD](https://www.notion.so/Entity-Framework-fd7693645cd54071b52de0abd07e1087?pvs=21)
5. [Relacionamentos](https://www.notion.so/Entity-Framework-fd7693645cd54071b52de0abd07e1087?pvs=21)
6. [Migrações](https://www.notion.so/Entity-Framework-fd7693645cd54071b52de0abd07e1087?pvs=21)
7. [Consultas Avançadas](https://www.notion.so/Entity-Framework-fd7693645cd54071b52de0abd07e1087?pvs=21)
8. [Armazenamento em Cache](https://www.notion.so/Entity-Framework-fd7693645cd54071b52de0abd07e1087?pvs=21)

## 1. Introdução ao Entity Framework

O Entity Framework é uma ferramenta poderosa que permite mapear classes .NET para tabelas em um banco de dados relacional. Ele automatiza muitas tarefas comuns de acesso a dados, como consultas, inserções, atualizações e exclusões.

## 2. Configuração do Projeto

Antes de começar, instale o pacote NuGet do Entity Framework no seu projeto:

```bash
Install-Package Microsoft.EntityFrameworkCore

```

Aqui está um exemplo de configuração do contexto de banco de dados:

```csharp
using Microsoft.EntityFrameworkCore;

public class ApplicationDbContext : DbContext
{
    public DbSet<Product> Products { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer("YourConnectionString");
    }
}

```

## 3. Definindo Modelos de Dados

Crie classes que representem suas tabelas no banco de dados:

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
}

```

## 4. Consultas e Operações CRUD

### Consulta Básica:

```csharp
using var context = new ApplicationDbContext();
var products = context.Products.ToList();

```

### Inserção:

```csharp
var newProduct = new Product { Name = "New Product", Price = 9.99 };
context.Products.Add(newProduct);
context.SaveChanges();

```

### Atualização:

```csharp
var productToUpdate = context.Products.Find(productId);
productToUpdate.Name = "Updated Name";
context.SaveChanges();

```

### Exclusão:

```csharp
var productToDelete = context.Products.Find(productId);
context.Products.Remove(productToDelete);
context.SaveChanges();

```

## 5. Relacionamentos

### Um para Muitos:

```csharp
public class Category
{
    public int Id { get; set; }
    public string Name { get; set; }
    public List<Product> Products { get; set; }
}

```

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
    public int CategoryId { get; set; }
    public Category Category { get; set; }
}

```

### Muitos para Muitos:

```csharp
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }
    public List<Course> Courses { get; set; }
}

```

```csharp
public class Course
{
    public int Id { get; set; }
    public string Title { get; set; }
    public List<Student> Students { get; set; }
}

```

## 6. Migrações

O recurso de migrações permite que você evolua o esquema do banco de dados ao longo do tempo:

```bash
Add-Migration InitialCreate
Update-Database

```

## 7. Consultas Avançadas

### Filtragem:

```csharp
var cheapProducts = context.Products.Where(p => p.Price < 10).ToList();

```

### Ordenação:

```csharp
var sortedProducts = context.Products.OrderBy(p => p.Name).ToList();

```

### Junção:

```csharp
var productsWithCategories = context.Products.Include(p => p.Category).ToList();

```

## 8. Armazenamento em Cache

O Entity Framework suporta armazenamento em cache de consultas:

```csharp
var products = context.Products
    .Where(p => p.Price < 10)
    .Cacheable()
    .ToList();

```
