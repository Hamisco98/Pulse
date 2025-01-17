## Swagger Integration in ASP.NET Core

### Installation
To add Swagger support to your ASP.NET Core project, use the following command:

```bash
Install-Package Swashbuckle.AspNetCore
```

### Configuration
Add Swagger services in `Program.cs`:

```csharp
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

// Configure Swagger options
builder.Services.AddSwaggerGen(options =>
{
    options.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "An ASP.NET Core Web API for managing ToDo items",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Example Contact",
            Url = new Uri("https://example.com/contact")
        },
        License = new OpenApiLicense
        {
            Name = "Example License",
            Url = new Uri("https://example.com/license")
        }
    });
});
```

Enable Swagger in development environments:

```csharp
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}
```

### Enhancing Swagger UI with Documentation

#### Triple-Slash Comments for Actions
Add `<summary>` elements to provide descriptions in the Swagger UI.

```csharp
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
[HttpDelete("{id}")]
public IActionResult Delete(int id)
{
    // Implementation here
}
```

#### Detailed Remarks for Actions
Include `<remarks>` elements to supplement information and improve clarity.

```csharp
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <param name="item">The TodoItem to create.</param>
/// <returns>A newly created TodoItem.</returns>
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item #1",
///        "isComplete": true
///     }
///
/// </remarks>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public async Task<IActionResult> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    await _context.SaveChangesAsync();

    return CreatedAtAction(nameof(Get), new { id = item.Id }, item);
}
```