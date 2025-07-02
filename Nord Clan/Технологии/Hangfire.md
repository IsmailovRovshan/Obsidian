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