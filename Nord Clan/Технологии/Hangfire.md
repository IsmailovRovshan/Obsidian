**Hangfire** — это библиотека для .NET, которая позволяет запускать фоновые задачи (background jobs) прямо из кода C# без необходимости писать сложную инфраструктуру (например, вручную работать с очередями или планировщиками).
# Program.cs
```csharp
var hangfireConnectionString = builder.Configuration.GetConnectionString("HangfireConnectionString");
```
```csharp
builder.Services.AddHangfire(config => config
    .SetDataCompatibilityLevel(CompatibilityLevel.Version_180)
    .UseSimpleAssemblyNameTypeSerializer()
    .UseRecommendedSerializerSettings()
    .UsePostgreSqlStorage(options => options.UseNpgsqlConnection(hangfireConnectionString)));
```

```csharp
builder.Services.AddHangfireServer();
```

```csharp
app.UseHangfireDashboard("/hangfire");
```

```csharp
using (var scope = app.Services.CreateScope())
{
    var dbContext = scope.ServiceProvider.GetRequiredService<DatabaseDbContext>();
    await dbContext.Database.MigrateAsync();

    var recurringJobManager = scope.ServiceProvider.GetRequiredService<IRecurringJobManager>();

    recurringJobManager.AddOrUpdate<ISchedulerService>(
        "myJob",
        service => service.FetchAllCurrencyDataAsync(CancellationToken.None),
        Cron.Daily);
}
```

---
# ChatGPT
Конечно! Вот красиво оформленный **markdown** вариант всех этих примеров и возможностей Hangfire:

---

# 🚀 Возможности Hangfire с примерами (C#)

Hangfire — мощная библиотека для фоновых задач в .NET. Она позволяет запускать задачи немедленно, откладывать их, планировать на расписание и строить сложные цепочки.

---

## 1️⃣ `BackgroundJob.Enqueue`

Запускает **одноразовую задачу**, которая выполняется **как можно скорее**.

```csharp
BackgroundJob.Enqueue(() => Console.WriteLine("Hello from Hangfire!"));
```

Можно использовать для:

- немедленной отправки email
- записи в лог
- старта генерации отчёта

---

## 2️⃣ `BackgroundJob.Schedule`

Позволяет **отложить выполнение задачи на заданный промежуток времени**.

```csharp
BackgroundJob.Schedule(
    () => Console.WriteLine("Будет выполнено через 1 день"),
    TimeSpan.FromDays(1)
);
```

---

## 3️⃣ `RecurringJob.AddOrUpdate`

Используется для **периодических задач по расписанию (cron)**.

```csharp
RecurringJob.AddOrUpdate(
    "daily-check",                  // идентификатор задачи
    () => Console.WriteLine("Каждый день!"),
    Cron.Daily                      // встроенный cron для daily
);
```

Можно задать произвольный cron:

```csharp
RecurringJob.AddOrUpdate(
    "custom-cron",
    () => Console.WriteLine("Каждый понедельник в 12:30"),
    "30 12 * * 1"                   // cron expression
);
```

---

## 4️⃣ `BackgroundJob.ContinueWith`

Позволяет **запустить задачу после успешного завершения другой задачи** (chain jobs).

```csharp
var jobId = BackgroundJob.Enqueue(() => Console.WriteLine("Первая задача"));
BackgroundJob.ContinueWith(jobId, () => Console.WriteLine("Вторая задача после первой"));
```

---

## ⚡ Полезные cron-выражения
- `Cron.Minutely` — каждую минуту
- `Cron.Hourly` — каждый час
- `Cron.Daily` — каждый день в полночь
- `Cron.Weekly` — каждую неделю
- `Cron.Monthly` — каждый месяц
- `Cron.Yearly` — каждый год
