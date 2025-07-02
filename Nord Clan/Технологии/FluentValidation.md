FluentValidation — **это библиотека для .NET, которая позволяет описывать правила валидации в виде текучего синтаксиса и применять их**.
# Di

builder.Services.AddScoped, CreateUserValidator >();
# NuGet
PackageReference  PackageReference Include="FluentValidation" Version="12.0.0"

# Пример
```csharp
public record PersonDto(string Name, int Age);
```

```csharp
public class CreateUserValidator : AbstractValidator<PersonDto>
{
    public CreateUserValidator()
    {
        RuleFor(x => x.Name)
            .NotEmpty()
            .MaximumLength(3);

        RuleFor(x => x.Age)
            .NotEmpty()
            .InclusiveBetween(10, 12)
            .WithMessage("Между 10 и 12");
    }
}
```

```csharp
[ApiController]
[Route("[controller]")]
public class MyController : ControllerBase
{
    private readonly IValidator<PersonDto> _validator;
    public MyController(IValidator<PersonDto> validator)
    {
        _validator = validator;
    }
    [HttpGet]
    public async Task<IActionResult> CreateEntity([FromQuery] PersonDto dto)
    {
        _validator.ValidateAndThrow(dto);

        var person = new PersonDto(dto.Name, dto.Age);
        Console.WriteLine(person.Age);
        return Ok(person);
    }
}
```

---
# Дополнение
Если ты хочешь **автоматическую валидацию в ASP.NET Core**, то:
1. Добавь также `FluentValidation.AspNetCore`:
PackageReference Include="FluentValidation.AspNetCore" Version="12.0.0" 
2. Зарегистрируй в `Program.cs`:
```csharp
builder.Services.AddControllers()
    .AddFluentValidation(fv =>
    {
        fv.RegisterValidatorsFromAssemblyContaining<CreateUserValidator>();
    });

```