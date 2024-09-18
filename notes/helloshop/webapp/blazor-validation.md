# Blazor 表单验证

## 使用数据注解 DataAnnotations 验证

```csharp
public class Employee
{
    [Required(ErrorMessage = "Name is required")]
    public string Name { get; set; }
}
```

```csharp
@page "/employee"
@using System.ComponentModel.DataAnnotations
@using Microsoft.AspNetCore.Components.Forms

<h3>Employee Form</h3>

<EditForm Model="@employee" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <div class="form-group">
        <label for="Name">Name</label>
        <InputText id="Name" @bind-Value="employee.Name" class="form-control" />
        <ValidationMessage For="@(() => employee.Name)" />
    </div>

    <button type="submit" class="btn btn-primary">Submit</button>
</EditForm>

@code {
    private Employee employee = new Employee();
}
```

## 使用 FluentValidation 验证

```shell
dotnet add package Blazored.FluentValidation
```

```csharp
public class EmployeeValidator : AbstractValidator<Employee>
{
    public EmployeeValidator()
    {
        RuleFor(x => x.Name).NotEmpty().WithMessage("Name is required");
    }
}
```

```csharp
@page "/employee"
@using FluentValidation
@using FluentValidation.Results

<h3>Employee Form</h3>

<EditForm Model="@employee" OnValidSubmit="@HandleValidSubmit">
    <FluentValidationValidator DisableAssemblyScanning="@true" />
    <ValidationSummary />

    <div class="form-group">
        <label for="Name">Name</label>
        <InputText id="Name" @bind-Value="employee.Name" class="form-control" />
        <ValidationMessage For="@(() => employee.Name)" />
    </div>

    <button type="submit" class="btn btn-primary">Submit</button>
</EditForm>
```
## 自定义验证


