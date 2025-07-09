Вот подборка шаблонов и приёмов, схожих по духу с вашим «опциональным» фильтром, которые часто встречаются при работе с LINQ/EF и в чистом C#:

---

## 1. Несколько опциональных фильтров в одном запросе

```csharp
var query = _dbContext.Products.AsQueryable();

if (!string.IsNullOrWhiteSpace(name))
    query = query.Where(p => p.Name.Contains(name));

if (minPrice.HasValue)
    query = query.Where(p => p.Price >= minPrice.Value);

if (maxPrice.HasValue)
    query = query.Where(p => p.Price <= maxPrice.Value);

var result = await query.ToListAsync();
```

**Зачем?**

- Удобно масштабировать, когда фильтров много.
    
- Читаемость лучше, чем один гигантский `Where`.
    

---

## 2. Пейджинг (Skip/Take)

```csharp
var pageSize = 20;
var pageNumber = 3; // нумерация с нуля

var paged = await _dbContext.Orders
    .OrderBy(o => o.Date)
    .Skip(pageNumber * pageSize)
    .Take(pageSize)
    .ToListAsync();
```

**Зачем?**

- Подгружать данные порциями для UI или API.
    

---

## 3. Динамическая сортировка

```csharp
IQueryable<User> q = _dbContext.Users;

q = sortBy switch
{
    "name" => q.OrderBy(u => u.LastName),
    "age"  => q.OrderBy(u => u.Age),
    _      => q.OrderBy(u => u.Id)
};

var users = await q.ToListAsync();
```

**Зачем?**

- Параметризовать `OrderBy` без отражения (reflection).
    

---

## 4. «IN-фильтр» через `Contains`

```csharp
List<int> allowedIds = GetAllowedIds();

var items = await _dbContext.Items
    .Where(i => allowedIds.Contains(i.CategoryId))
    .ToListAsync();
```

**Зачем?**

- Отобрать записи, чей ключ есть в заданном списке.
    

---

## 5. Объединение и разность множеств

```csharp
var a = new[] {1,2,3,4};
var b = new[] {3,4,5};

// Объединение: {1,2,3,4,5}
var union = a.Union(b);

// Пересечение: {3,4}
var intersect = a.Intersect(b);

// Разность a \ b: {1,2}
var except = a.Except(b);
```

**Зачем?**

- Быстрые операции над наборами в памяти.
    

---

## 6. Группировка и агрегация

```csharp
var stats = await _dbContext.Orders
    .GroupBy(o => o.CustomerId)
    .Select(g => new {
        CustomerId = g.Key,
        Total     = g.Sum(o => o.Amount),
        Count     = g.Count()
    })
    .ToListAsync();
```

**Зачем?**

- Подсчитать итоги по категориям, клиентам и т.п.
    

---

## 7. `Any` и `All` для вложенных коллекций

```csharp
// Есть ли заказы на сумму >1000
bool hasBig = await _dbContext.Customers
    .AnyAsync(c => c.Orders.Any(o => o.Amount > 1000));

// У всех ли заказов статус «Completed»?
bool allDone = await _dbContext.Customers
    .AllAsync(c => c.Orders.All(o => o.Status == "Completed"));
```

**Зачем?**

- Проверить существование/полное соответствие вложенных элементов.
    

---

## 8. Безопасная навигация и значение по умолчанию

```csharp
// Если product или его свойство Version вдруг null, вернётся 0
int version = product?.Version ?? 0;
```

**Операторы**

- `?.` — только если не `null`.
    
- `??` — даёт запасное значение, если слева `null`.
    

---

## 9. Выражения-switch и pattern matching

```csharp
string Describe(object obj) => obj switch
{
    int n when n > 0  => "Положительное число",
    int _             => "Нул или отрицательное",
    string s          => $"Строка длиной {s.Length}",
    null              => "Ничего нет",
    _                 => "Какой-то другой объект"
};
```

**Зачем?**

- Удобная замена сложных `if..else` и классического `switch`.
    

---

## 10. LINQ Query Syntax с `let`

```csharp
var q = from c in _dbContext.Customers
        let bigOrders = c.Orders.Where(o => o.Amount > 500)
        where bigOrders.Any()
        select new {
            c.Name,
            BigOrdersCount = bigOrders.Count()
        };
```

**`let`** позволяет вычислить подзапрос один раз и использовать несколько раз.

---

Эти приёмы помогут писать гибкие, читаемые и производительные запросы и логику на C#.


этот пример сделай 11 пунктом public async Task<List<ProductDto>> GetProductsAsync(string category = null) { return await _dbContext.Products .Where(p => category == null || p.Category == category) .Select(p => new ProductDto { Id = p.Id, Name = p.Name, Category = p.Category }) .ToListAsync(); }