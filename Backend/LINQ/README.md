# LINQ

A Linguagem Integrada de Consulta (Language Integrated Query - LINQ) é uma tecnologia do .NET que permite escrever consultas em estilo SQL diretamente em código C# ou VB.NET. O LINQ permite que você realize operações de consulta, filtro e transformação em coleções de objetos, bancos de dados, XML e muito mais. Neste guia, abordaremos todos os principais aspectos do LINQ e forneceremos exemplos de código para diferentes casos de uso.


## **1. Introdução ao LINQ**

O LINQ é uma extensão do .NET que permite escrever consultas de banco de dados usando sintaxe semelhante ao SQL diretamente no código. Isso oferece maior legibilidade e manutenção do código, além de permitir consultas em diferentes fontes de dados.

## **2. Consultas Básicas**

Você pode usar o LINQ para consultar coleções de objetos. Aqui está um exemplo de consulta simples em uma lista de inteiros:

```csharp

var numbers = new List<int> { 1, 2, 3, 4, 5 };

var evenNumbers = from num in numbers
                  where num % 2 == 0
                  select num;

foreach (var num in evenNumbers)
{
    Console.WriteLine(num);
}

```

versão inline:

```csharp

var numbers = new List<int> { 1, 2, 3, 4, 5 };

var evenNumbers = numbers.Where(num => num % 2 == 0);

foreach (var num in evenNumbers)
{
    Console.WriteLine(num);
}

```

## **3. Operações de Filtro**

Use a cláusula **`where`** para filtrar resultados com base em uma condição:

```csharp

var products = GetProducts(); // Suponha que haja uma lista de produtos

var expensiveProducts = from product in products
                        where product.Price > 100
                        select product;

```

Versão inline

```csharp

var expensiveProducts = products.Where(product => product.Price > 100);

```

### Exemplos:

exemplos de operações de filtro em LINQ envolvendo quatro entidades diferentes. Vamos considerar as seguintes entidades: **`Customers`**, **`Orders`**, **`Products`** e **`Categories`**.

```csharp

public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public DateTime OrderDate { get; set; }
}

public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
    public int CategoryId { get; set; }
}

public class Category
{
    public int Id { get; set; }
    public string Name { get; set; }
}

```

Aqui estão exemplos de operações de filtro em LINQ envolvendo essas quatro entidades:

### 3.**1 Filtrar Pedidos de um Cliente Específico:**

```csharp

int customerId = 1;

var ordersForCustomer = orders.Where(order => order.CustomerId == customerId);

```

### 3**.2 Filtrar Produtos de uma Categoria Específica:**

```csharp

int categoryId = 2;

var productsInCategory = products.Where(product => product.CategoryId == categoryId);

```

### 3.**3 Filtrar Clientes com Pedidos em uma Data Específica:**

```csharp

DateTime targetDate = new DateTime(2023, 8, 1);

var customersWithOrdersOnDate = customers.Where(customer =>
    orders.Any(order => order.CustomerId == customer.Id && order.OrderDate.Date == targetDate.Date));

```

### 3.**4 Filtrar Produtos com Preço Superior a um Valor Específico e de uma Categoria Específica:**

```csharp

int categoryId = 3;
decimal minPrice = 50.00M;

var expensiveProductsInCategory = products.Where(product =>
    product.CategoryId == categoryId && product.Price > minPrice);

```

## **4. Operações de Ordenação**

Você pode ordenar os resultados usando as cláusulas **`orderby`** e **`descending`**:

```csharp

var students = GetStudents(); // Suponha que haja uma lista de estudantes

var sortedStudents = from student in students
                     orderby student.Name ascending
                     select student;

```

Versão inline:

```jsx
var sortedStudents = students.OrderBy(student => student.Name);
```

### Exemplos:

exemplos de consultas LINQ de operações de ordenação envolvendo as quatro entidades: **`Customers`**, **`Orders`**, **`Products`** e **`Categories`**. Vamos continuar usando as mesmas entidades definidas anteriormente.

### 4.**1. Ordenar Clientes pelo Nome em Ordem Crescente:**

```csharp

var sortedCustomers = customers.OrderBy(customer => customer.Name);

```

### 4.**2. Ordenar Produtos por Preço em Ordem Decrescente:**

```csharp

var sortedProductsByPriceDesc = products.OrderByDescending(product => product.Price);

```

### 4.**3. Ordenar Pedidos por Data em Ordem Crescente e, em caso de empate, pelo ID do Pedido em Ordem Decrescente:**

```csharp

var sortedOrders = orders.OrderBy(order => order.OrderDate)
                          .ThenByDescending(order => order.Id);

```

### 4.**4. Ordenar Produtos por Categoria e, dentro de cada Categoria, por Nome:**

```csharp

var sortedProductsByCategoryThenName = products.OrderBy(product => product.CategoryId)
                                                .ThenBy(product => product.Name);

```

## **5. Operações de Projeção**

Use a cláusula **`select`** para projetar dados específicos:

```csharp

var employees = GetEmployees(); // Suponha que haja uma lista de funcionários

var employeeNames = from employee in employees
                    select employee.Name;

```

Versão inline:

```jsx
var employeeNames = employees.Select(employee => employee.Name);
```

### Exemplo:

exemplos de consultas LINQ de operações de projeção envolvendo as quatro entidades: **`Customers`**, **`Orders`**, **`Products`** e **`Categories`**. Continuaremos usando as mesmas entidades definidas anteriormente.

### 5.**1. Projetar uma Lista de Nomes de Clientes:**

```csharp

var customerNames = customers.Select(customer => customer.Name).ToList();

```

### 5.**2. Projetar uma Lista de Nomes e Preços de Produtos:**

```csharp

var productNamesAndPrices = products.Select(product => new { product.Name, product.Price }).ToList();

```

### 5.**3. Projetar uma Lista de Pedidos com Nomes de Clientes e Datas de Pedido:**

```csharp

var ordersWithCustomerInfo = orders.Select(order => new
{
    OrderId = order.Id,
    CustomerName = customers.FirstOrDefault(customer => customer.Id == order.CustomerId)?.Name,
    OrderDate = order.OrderDate
}).ToList();

```

### 5.**4. Projetar uma Lista de Categorias com Quantidade de Produtos em Cada Categoria:**

```csharp

var categoriesWithProductCount = categories.Select(category => new
{
    CategoryName = category.Name,
    ProductCount = products.Count(product => product.CategoryId == category.Id)
}).ToList();

```

## **6. Joins**

Você pode realizar junções semelhantes ao SQL usando as cláusulas **`join`** e **`on`**:

```csharp

var orders = GetOrders(); // Suponha que haja uma lista de pedidos
var customers = GetCustomers(); // Suponha que haja uma lista de clientes

var orderCustomerInfo = from order in orders
                        join customer in customers on order.CustomerId equals customer.Id
                        select new { OrderId = order.Id, CustomerName = customer.Name };

```

versão inline: 

```jsx
var orderCustomerInfo = orders.Join(customers, 
order => order.CustomerId, customer => customer.Id, 
(order, customer) => new { 
OrderId = order.Id, CustomerName = customer.Name 
});
```

### Exemplos:

exemplos de consultas LINQ de operações de junção (join) envolvendo as quatro entidades utilizando o estilo de consulta inline:

### 6.**1. Inner Join para Obter os Nomes de Clientes e Nomes de Produtos por Pedido:**

```csharp

var innerJoinQuery = orders
    .Join(customers, order => order.CustomerId, customer => customer.Id, (order, customer) => new { order, customer })
    .Join(products, oc => oc.order.ProductId, product => product.Id, (oc, product) => new
    {
        CustomerName = oc.customer.Name,
        ProductName = product.Name
    });

```

### 6.**2. Left Join para Obter Clientes e seus Pedidos (Inclusive Clientes sem Pedidos):**

```csharp

var leftJoinQuery = customers
    .GroupJoin(orders, customer => customer.Id, order => order.CustomerId, (customer, orders) => new { customer, orders })
    .SelectMany(co => co.orders.DefaultIfEmpty(), (co, order) => new
    {
        CustomerName = co.customer.Name,
        OrderId = order?.Id
    });

```

### 6.**3. Inner Join com Filtro para Obter Produtos e suas Categorias (Apenas Produtos com Categoria):**

```csharp

var innerJoinWithFilter = products
    .Join(categories.Where(category => category.Name == "Electronics"), product => product.CategoryId, category => category.Id, (product, category) => new
    {
        ProductName = product.Name,
        CategoryName = category.Name
    });

```

### 6.**4. Cross Join para Obter Todas as Combinações Possíveis de Clientes e Produtos:**

```csharp

var crossJoinQuery = customers
    .SelectMany(customer => products, (customer, product) => new
    {
        CustomerName = customer.Name,
        ProductName = product.Name
    });

```

## **7. GroupBy e Agregação**

A cláusula **`group by`** permite agrupar dados e realizar operações de agregação:

```csharp

var orders = GetOrders(); // Suponha que haja uma lista de pedidos

var orderTotalsByCustomer = from order in orders
                            group order by order.CustomerId into customerGroup
                            select new { CustomerId = customerGroup.Key, TotalAmount = customerGroup.Sum(o => o.Amount) };

```

versão inline:

```csharp
var orderTotalsByCustomer = orders.GroupBy(order => order.CustomerId, (key, group)
 => new { 
CustomerId = key, TotalAmount = group.Sum(o => o.Amount) 
});

```

## **8. Consultas Aninhadas**

É possível aninhar consultas LINQ dentro de outras consultas:

```csharp

var departments = GetDepartments(); // Suponha que haja uma lista de departamentos

var employeesPerDepartment = from department in departments
                            select new
                            {
                                DepartmentName = department.Name,
                                Employees = from employee in department.Employees
                                            select employee.Name
                            };

```

Versão inline: 

```csharp
var employeesPerDepartment = departments.Select(department => new
{
    DepartmentName = department.Name,
    Employees = department.Employees.Select(employee => employee.Name)
});
```

### Exemplos:

exemplos de consultas LINQ de consultas aninhadas envolvendo as quatro entidades: **`Customers`**, **`Orders`**, **`Products`** e **`Categories`**. Continuaremos utilizando as mesmas entidades definidas anteriormente.

### **1. Consulta Aninhada para Obter os Pedidos de Cada Cliente:**

```csharp

var ordersPerCustomer = customers.Select(customer => new
{
    CustomerName = customer.Name,
    Orders = orders.Where(order => order.CustomerId == customer.Id)
});

```

### **2. Consulta Aninhada para Obter os Produtos de Cada Categoria:**

```csharp

var productsPerCategory = categories.Select(category => new
{
    CategoryName = category.Name,
    Products = products.Where(product => product.CategoryId == category.Id)
});

```

### **3. Consulta Aninhada para Obter os Nomes de Clientes que Fizeram Pedidos em uma Data Específica:**

```csharp

DateTime targetDate = new DateTime(2023, 8, 1);

var customersWithOrdersOnDate = customers.Where(customer =>
    orders.Any(order => order.CustomerId == customer.Id && order.OrderDate.Date == targetDate.Date))
    .Select(customer => customer.Name);

```

### **4. Consulta Aninhada para Obter o Nome do Cliente que Fez o Pedido mais Caro:**

```csharp

var customerWithHighestOrderAmount = orders.OrderByDescending(order => order.Amount)
    .Select(order => customers.FirstOrDefault(customer => customer.Id == order.CustomerId)?.Name)
    .FirstOrDefault();

```

## **9. Inserções com LINQ**

Apesar de o LINQ ser principalmente voltado para consultas, você pode realizar inserções em coleções usando o método **`Add`**:

```csharp

var students = new List<Student>(); // Suponha que haja uma lista de estudantes vazia

students.Add(new Student { Name = "Alice", Age = 20 });
students.Add(new Student { Name = "Bob", Age = 22 });

```
