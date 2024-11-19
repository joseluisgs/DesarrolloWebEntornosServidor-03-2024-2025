![](./images/09-banner.png)

- [Negociación de contenido](#negociación-de-contenido)
- [Paginación y ordenación](#paginación-y-ordenación)
  - [Ajustando el repositorio](#ajustando-el-repositorio)
  - [Obteniendo las páginas desde el controlador](#obteniendo-las-páginas-desde-el-controlador)
  - [El resultado como página](#el-resultado-como-página)
    - [Simplificando el resultado](#simplificando-el-resultado)
- [Práctica de clase, Paginación, Criterios de selección y Ordenación](#práctica-de-clase-paginación-criterios-de-selección-y-ordenación)


# Negociación de contenido

La negociación de contenido en ASP.NET Core permite a un cliente especificar el formato de los datos que desea recibir del servidor. Puedes configurar tu aplicación para que devuelva datos en diferentes formatos, como JSON o XML.

Para habilitar XML en ASP.NET Core, primero necesitas agregar el paquete necesario:

```bash
dotnet add package Microsoft.AspNetCore.Mvc.Formatters.Xml
```

Luego, configura los servicios en `Program.cs` para incluir soporte de formato XML:

```csharp
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllers()
    .AddXmlSerializerFormatters(); // Agregar soporte para XML

var app = builder.Build();

// Configure the HTTP request pipeline.
app.UseHttpsRedirection();

app.MapControllers();

app.Run();
```

Ahora puedes obtener contenido en JSON y XML de varias maneras:

- **Usando la URL:** Aunque en ASP.NET Core no es común usar parámetros de consulta para la negociación de contenido, puedes implementar lógica personalizada para leer un parámetro de consulta y ajustar el `Accept` header en consecuencia.
- **Usando los headers en la petición:** Utiliza el header `Accept` con valores como `application/xml` para XML o `application/json` para JSON.

# Paginación y ordenación

La paginación y la ordenación son esenciales para manejar grandes cantidades de datos en aplicaciones web. ASP.NET Core junto con Entity Framework Core ofrece soporte para implementar estas características.

## Ajustando el repositorio

Primero, asegúrate de que tu repositorio esté configurado para manejar la paginación y la ordenación. Usaremos `DbContext` y `IQueryable` para esto.

```csharp
public class MyEntity
{
    public long Id { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
}

public class MyDbContext : DbContext
{
    public DbSet<MyEntity> MyEntities { get; set; }
}
```

## Obteniendo las páginas desde el controlador

En tu controlador, acepta parámetros para la página, el tamaño de la página y la ordenación. Usa estos parámetros para obtener los datos paginados y ordenados.

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddDbContext<MyDbContext>(options => 
    options.UseInMemoryDatabase("MyDatabase"));

var app = builder.Build();

app.MapGet("/api/entities", async (
    [FromServices] MyDbContext dbContext,
    [FromQuery] int page = 0,
    [FromQuery] int size = 10,
    [FromQuery] string sortBy = "Id",
    [FromQuery] string order = "asc") =>
{
    var query = dbContext.MyEntities.AsQueryable();

    // Apply sorting
    query = order.ToLower() == "asc" 
        ? query.OrderBy(e => EF.Property<object>(e, sortBy))
        : query.OrderByDescending(e => EF.Property<object>(e, sortBy));

    // Apply pagination
    var totalItems = await query.CountAsync();
    var items = await query.Skip(page * size).Take(size).ToListAsync();

    var result = new
    {
        Content = items,
        Pageable = new
        {
            PageNumber = page,
            PageSize = size,
            TotalElements = totalItems,
            TotalPages = (int)Math.Ceiling(totalItems / (double)size)
        }
    };

    return Results.Ok(result);
});

app.Run();
```

En este ejemplo, si un cliente hace una petición GET a `/api/entities?page=1&size=20&sortBy=name&order=asc`, el controlador recuperará la segunda página de entidades, con 20 entidades por página, ordenadas por el campo `Name`.

## El resultado como página

El objeto devuelto contiene los elementos de la página solicitada y metadatos sobre la paginación, similar al objeto `Page` en Spring Data. Aquí tienes un ejemplo de cómo podría verse un resultado en formato JSON:

```json
{
    "content": [
        {
            "id": 1,
            "name": "Entity 1",
            "description": "Description 1"
        },
        {
            "id": 2,
            "name": "Entity 2",
            "description": "Description 2"
        }
        // ... más entidades ...
    ],
    "pageable": {
        "pageNumber": 0,
        "pageSize": 10,
        "totalElements": 50,
        "totalPages": 5
    }
}
```

### Simplificando el resultado

Puedes ajustar el formato de la respuesta para que se ajuste a tus necesidades específicas, incluyendo o excluyendo campos según sea necesario.

Esta traducción debería darte una buena base para implementar una API RESTful en ASP.NET Core con características de negociación de contenido, paginación y ordenación, similar a lo que harías en Spring Boot.

# Práctica de clase, Paginación, Criterios de selección y Ordenación

1. Ajusta repositorios, servicios y controladores para que permitan trabajar con paginación y criterios de ordenación.
2. Ajusta repositorios, servicios y controladores para que se pueda consultar paginadamente en base a criterios de selección y poder ordenar además los resultados.
3. Configura la negociación de contenido para que se pueda obtener los datos en formato XML o JSON.
4. Testea los repositorios, servicios y controladores con la nueva funcionalidad.

