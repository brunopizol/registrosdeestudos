# SOLID em C#

**Princípio da Responsabilidade Única (Single Responsibility Principle - SRP):**

Cada classe deve ter apenas uma única razão para mudar. Isso significa que uma classe deve ter uma única responsabilidade ou tarefa. Isso ajuda a manter o código coeso e facilita a manutenção.

Exemplo de código:

```csharp
// Sem SRP
class Customer {
    void AddToDatabase() { /* Adiciona o cliente ao banco de dados */ }
    void CalculateDiscount() { /* Calcula o desconto do cliente */ }
}

// Com SRP
class CustomerDatabase {
    void AddToDatabase() { /* Adiciona o cliente ao banco de dados */ }
}

class DiscountCalculator {
    void CalculateDiscount() { /* Calcula o desconto do cliente */ }
}

```

**Princípio Aberto/Fechado (Open/Closed Principle - OCP):**

As entidades de software (classes, módulos, funções etc.) devem ser abertas para extensão, mas fechadas para modificação. Isso significa que você pode adicionar novos comportamentos ou funcionalidades sem alterar o código existente.

Exemplo de código:

```csharp
// Sem OCP
class Rectangle {
    int Width { get; set; }
    int Height { get; set; }
}

class AreaCalculator {
    double CalculateArea(Rectangle rectangle) {
        return rectangle.Width * rectangle.Height;
    }
}

// Com OCP
interface IShape {
    double CalculateArea();
}

class Rectangle : IShape {
    int Width { get; set; }
    int Height { get; set; }
    double CalculateArea() {
        return Width * Height;
    }
}

class Circle : IShape {
    int Radius { get; set; }
    double CalculateArea() {
        return Math.PI * Radius * Radius;
    }
}

```

**Princípio da Substituição de Liskov (Liskov Substitution Principle - LSP):**

Os objetos de uma classe derivada devem ser substituíveis pelos objetos da classe base sem afetar a corretude do programa. Isso garante que a herança seja usada de forma adequada.

Exemplo de código:

```csharp
// Violando o LSP
class Bird {
    virtual void Fly() { /* Lógica de voo para pássaros */ }
}

class Ostrich : Bird {
    override void Fly() { /* Não voa, mas ainda precisa implementar */ }
}

// Respeitando o LSP
interface IFlyable {
    void Fly();
}

class Bird : IFlyable {
    void Fly() { /* Lógica de voo para pássaros */ }
}

class Ostrich : IFlyable {
    void Fly() { /* Não faz nada, já que não voa */ }
}

```

**Princípio da Segregação da Interface (Interface Segregation Principle - ISP):**

Nenhum cliente deve ser forçado a depender de métodos que ele não utiliza. Isso significa que as interfaces devem ser pequenas e específicas, atendendo às necessidades individuais dos clientes.

Exemplo de código:

```csharp
// Violando o ISP
interface IWorker {
    void Work();
    void Eat();
}

class Programmer : IWorker {
    void Work() { /* Implementação do trabalho de um programador */ }
    void Eat() { /* Implementação do comer de um programador */ }
}

// Respeitando o ISP
interface IWorkable {
    void Work();
}

interface IEatable {
    void Eat();
}

class Programmer : IWorkable, IEatable {
    void Work() { /* Implementação do trabalho de um programador */ }
    void Eat() { /* Implementação do comer de um programador */ }
}

```

**Princípio da Inversão de Dependência (Dependency Inversion Principle - DIP):**

Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações. Além disso, abstrações não devem depender de detalhes; detalhes devem depender de abstrações.

Exemplo de código:

```csharp
// Violando o DIP
class LightBulb {
    void TurnOn() { /* Lógica para ligar a lâmpada */ }
    void TurnOff() { /* Lógica para desligar a lâmpada */ }
}

class Switch {
    LightBulb bulb = new LightBulb();
    void Flip() {
        if (bulb.IsOn) {
            bulb.TurnOff();
        } else {
            bulb.TurnOn();
        }
    }
}

// Respeitando o DIP
interface ISwitchable {
    void TurnOn();
    void TurnOff();
}

class LightBulb : ISwitchable {
    void TurnOn() { /* Lógica para ligar a lâmpada */ }
    void TurnOff() { /* Lógica para desligar a lâmpada */ }
}

class Switch {
    ISwitchable device;
    Switch(ISwitchable device) {
        this.device = device;
    }
    void Flip() {
        if (device.IsOn) {
            device.TurnOff();
        } else {
            device.TurnOn();
        }
    }
}

```
