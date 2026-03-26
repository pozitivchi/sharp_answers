Сделаю строго по заданию:
**теория 7–12**, затем **практика 7–12**.

---

# 📘 Теоретические вопросы (7–12)

---

### **7. Абстрактный класс**

Абстрактный класс — это класс, который:

* нельзя создать через `new`
* используется как базовый для других классов

**Ограничения:**

* нельзя создать объект
* может содержать как реализованные, так и абстрактные методы

**Абстрактный метод:**

* объявлен без реализации (`abstract`)
* должен быть реализован в дочернем классе

✔ Да, дочерний класс **обязан реализовать все абстрактные методы**, если сам не является абстрактным.

---

### **8. Абстрактный класс vs Интерфейс**

| Критерий             | Абстрактный класс | Интерфейс                   |
| -------------------- | ----------------- | --------------------------- |
| Реализация методов   | Да                | Да (с C# 8, но ограниченно) |
| Поля                 | Да                | Нет                         |
| Конструктор          | Да                | Нет                         |
| Модификаторы доступа | Есть              | Ограничены                  |
| Наследование         | Только одно       | Множественное               |

**Когда использовать:**

* Абстрактный класс → когда есть общая логика и состояние
* Интерфейс → когда нужен контракт (поведение)

---

### **9. Делегаты**

Делегат — это тип, который хранит ссылку на метод.

```csharp
delegate int MyDelegate(int x);
```

**Многоадресный делегат** — хранит несколько методов.

**Встроенные делегаты:**

* `Action` → ничего не возвращает
* `Func` → возвращает значение
* `Predicate` → возвращает `bool`

---

### **10. События**

Событие — это механизм уведомления.

Основано на делегатах.

```csharp
event EventHandler MyEvent;
```

**Подписка / отписка:**

```csharp
obj.Event += Handler;
obj.Event -= Handler;
```

**Почему `event`, а не делегат:**

* защищает от внешнего вызова
* только добавление/удаление обработчиков

---

### **11. Наследование**

Наследование — это получение свойств и методов от базового класса.

```csharp
class Dog : Animal
```

**Наследуется:**

* методы
* свойства

**Не наследуется:**

* конструкторы
* private поля

**Вызов конструктора:**

```csharp
class Dog : Animal
{
    public Dog() : base("Dog") {}
}
```

---

### **12. virtual / override / new / sealed**

* `virtual` — метод можно переопределить
* `override` — переопределение
* `new` — скрытие метода
* `sealed` — запрет наследования

**Разница override vs new:**

* `override` → полиморфизм
* `new` → просто скрытие

```csharp
class A
{
    public virtual void Show() {}
}

class B : A
{
    public override void Show() {}
}
```

---

# 💻 Практические задачи (7–12)

---

## **7. Product + IComparable**

```csharp
class Product : IComparable<Product>
{
    public string Name { get; set; }
    public decimal Price { get; set; }

    public int CompareTo(Product other)
    {
        return Price.CompareTo(other.Price);
    }

    public override string ToString()
    {
        return $"Name: {Name}, Price: {Price:C}";
    }
}

class Program
{
    static void Main()
    {
        var products = new List<Product>
        {
            new Product { Name="A", Price=10 },
            new Product { Name="B", Price=5 },
            new Product { Name="C", Price=20 },
            new Product { Name="D", Price=15 },
            new Product { Name="E", Price=8 }
        };

        products.Sort();

        foreach (var p in products)
            Console.WriteLine(p);
    }
}
```

---

## **8. Employee + IPayable**

```csharp
interface IPayable
{
    decimal CalculatePayment();
}

abstract class Employee : IPayable
{
    public string Name;
    public int HoursWorked;

    public abstract decimal CalculatePayment();
}

class FullTimeEmployee : Employee
{
    public decimal Salary;

    public override decimal CalculatePayment() => Salary;
}

class PartTimeEmployee : Employee
{
    public decimal HourRate;

    public override decimal CalculatePayment() => HourRate * HoursWorked;
}

class Program
{
    static void Main()
    {
        var list = new List<Employee>
        {
            new FullTimeEmployee { Name="A", Salary=1000 },
            new PartTimeEmployee { Name="B", HourRate=10, HoursWorked=50 }
        };

        foreach (var e in list)
            Console.WriteLine($"{e.Name}: {e.CalculatePayment()}");
    }
}
```

---

## **9. Делегаты**

```csharp
delegate double MathOperation(double a, double b);

class Program
{
    static double Add(double a, double b) => a + b;
    static double Sub(double a, double b) => a - b;
    static double Mul(double a, double b) => a * b;
    static double Div(double a, double b) => a / b;

    static void Execute(MathOperation op, double a, double b)
    {
        Console.WriteLine(op(a, b));
    }

    static void Main()
    {
        MathOperation[] ops = { Add, Sub, Mul, Div };

        foreach (var op in ops)
            Execute(op, 10, 2);
    }
}
```

---

## **10. Func / Filter / Transform**

```csharp
static List<T> Filter<T>(List<T> list, Func<T, bool> predicate)
{
    return list.Where(predicate).ToList();
}

static List<TResult> Transform<T, TResult>(List<T> list, Func<T, TResult> selector)
{
    return list.Select(selector).ToList();
}

class Program
{
    static void Main()
    {
        var numbers = new List<int> {1,2,3,4,5,6};

        var even = Filter(numbers, x => x % 2 == 0);
        var result = Transform(even, x => $"Число: {x}");

        result.ForEach(Console.WriteLine);
    }
}
```

---

## **11. События (Thermostat)**

```csharp
class Thermostat
{
    private double temp;

    public event Action<double> TemperatureChanged;
    public event Action OverheatWarning;

    public double Temperature
    {
        get => temp;
        set
        {
            temp = value;
            TemperatureChanged?.Invoke(temp);

            if (temp > 100)
                OverheatWarning?.Invoke();
        }
    }
}

class Monitor
{
    public void Subscribe(Thermostat t)
    {
        t.TemperatureChanged += t1 => Console.WriteLine($"Temp: {t1}");
        t.OverheatWarning += () => Console.WriteLine("OVERHEAT!");
    }
}

class Program
{
    static void Main()
    {
        var t = new Thermostat();
        var m = new Monitor();

        m.Subscribe(t);

        t.Temperature = 50;
        t.Temperature = 120;
    }
}
```

---

## **12. Vector2D**

```csharp
class Vector2D
{
    public double X, Y;

    public Vector2D(double x, double y)
    {
        X = x; Y = y;
    }

    public static Vector2D operator +(Vector2D a, Vector2D b)
        => new Vector2D(a.X + b.X, a.Y + b.Y);

    public static Vector2D operator -(Vector2D a, Vector2D b)
        => new Vector2D(a.X - b.X, a.Y - b.Y);

    public static Vector2D operator *(Vector2D v, double k)
        => new Vector2D(v.X * k, v.Y * k);

    public static bool operator ==(Vector2D a, Vector2D b)
        => a.X == b.X && a.Y == b.Y;

    public static bool operator !=(Vector2D a, Vector2D b)
        => !(a == b);

    public override string ToString()
        => $"({X}; {Y})";
}

class Program
{
    static void Main()
    {
        var v1 = new Vector2D(1,2);
        var v2 = new Vector2D(3,4);

        Console.WriteLine(v1 + v2);
        Console.WriteLine(v1 - v2);
        Console.WriteLine(v1 * 2);
        Console.WriteLine(v1 == v2);
    }
}
```

---

Если хочешь, могу:

* объяснить любой из пунктов проще
* или разобрать код как на экзамене (с комментариями)
# sharp_answers

