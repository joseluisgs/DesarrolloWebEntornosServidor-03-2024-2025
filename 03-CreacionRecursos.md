![](./images/03-banner.webp)
- [ASP.NET Core Web](#aspnet-core-web)
  - [Creando un proyecto](#creando-un-proyecto)
    - [Dependencias](#dependencias)
    - [Punto de Entrada](#punto-de-entrada)
    - [Creando Controladores y Rutas](#creando-controladores-y-rutas)
    - [Responses](#responses)
    - [Requests](#requests)
      - [Parámetros de ruta](#parámetros-de-ruta)
      - [Parámetros de consulta](#parámetros-de-consulta)
      - [Peticiones con datos serializados](#peticiones-con-datos-serializados)
    - [Versionado de la API](#versionado-de-la-api)
  - [Postman](#postman)
  - [Ejemplo](#ejemplo)
  - [Práctica de clase, mi primera API REST](#práctica-de-clase-mi-primera-api-rest)
# ASP.NET Core Web

**ASP.NET Core MVC** es un framework para construir aplicaciones web y APIs RESTful en .NET. Se basa en el patrón de diseño Modelo-Vista-Controlador (MVC), que es comúnmente utilizado para separar las preocupaciones en el desarrollo de aplicaciones.

Aquí está cómo funciona ASP.NET Core MVC en términos del patrón MVC:

1. **Modelo**: Representa los datos y la lógica de negocio de la aplicación. En ASP.NET Core, los modelos son típicamente clases que contienen propiedades y métodos que representan y manipulan los datos.

2. **Vista**: Es responsable de presentar los datos al usuario. ASP.NET Core soporta varias tecnologías de vistas, incluyendo Razor, que es un motor de vistas basado en HTML y C#.

3. **Controlador**: Maneja las solicitudes del usuario, actualiza el modelo y selecciona la vista adecuada para renderizar. En ASP.NET Core, los controladores son clases que heredan de `Controller` o `ControllerBase`.

El framework ASP.NET Core MVC proporciona muchas funcionalidades para el desarrollo de aplicaciones web, incluyendo:

- Enlace de datos: ASP.NET Core puede enlazar automáticamente los parámetros de la solicitud a los parámetros del método del controlador.
- Validación: Soporta la validación de datos utilizando atributos de validación.
- Formateo de datos: Puede convertir automáticamente entre strings y tipos de datos más complejos.
- Manejo de excepciones: Proporciona un mecanismo robusto para manejar excepciones.
- Soporte para la generación de respuestas en varios formatos, como JSON y XML.

## Creando un proyecto

Podemos crear un proyecto ASP.NET Core utilizando Visual Studio o la CLI de .NET. Aquí se muestra cómo crear un proyecto usando la CLI:

```bash
dotnet new webapi -n TiendaApi
cd TiendaApi
```

Esto crea un nuevo proyecto de API web en .NET Core, con una estructura básica y archivos de configuración necesarios.

### Dependencias

En ASP.NET Core, las dependencias se gestionan a través del archivo `csproj`. Aquí podemos añadir paquetes NuGet según sea necesario. Para nuestro proyecto, utilizaremos ASP.NET Core para crear aplicaciones web y APIs no reactivas.

Si necesitamos añadir más dependencias, podemos hacerlo usando el comando `dotnet add package` o editando el archivo `.csproj` directamente.

### Punto de Entrada

El punto de entrada para una aplicación ASP.NET Core es la clase `Program`, que configura y lanza el servidor web. La configuración de la aplicación se realiza en la clase `Startup`.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

La clase `Startup` define cómo se configura la aplicación y los servicios que se utilizarán:

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
}
```

Todo esto se puede hacer usando la sintaxis de C# o utilizando la api minima de .NET Core, que es más sencilla y ligera. Esta es la que usaremos en los ejemplos de la clase.

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", () => "Hello World!");
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
});
app.Run();
```


### Creando Controladores y Rutas

En ASP.NET Core, los controladores manejan las solicitudes y definen las rutas. Un controlador típico se ve así:

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductosController : ControllerBase
{
    [HttpGet]
    public IActionResult Get()
    {
        return Ok(new { mensaje = "Hola, mundo!" });
    }
}
```

### Responses

Para enviar respuestas desde los controladores, podemos usar métodos como `Ok()`, `BadRequest()`, `NotFound()`, etc., que son proporcionados por la clase `ControllerBase`.

### Requests

ASP.NET Core puede enlazar automáticamente los parámetros de la solicitud a los parámetros del método del controlador. Aquí hay algunos ejemplos:

#### Parámetros de ruta

```csharp
[HttpGet("{id}")]
public IActionResult GetProducto(int id)
{
    // Lógica para obtener el producto por id
    return Ok(producto);
}
```

#### Parámetros de consulta

```csharp
[HttpGet]
public IActionResult GetProductos([FromQuery] string categoria)
{
    // Lógica para obtener productos por categoría
    return Ok(productos);
}
```

#### Peticiones con datos serializados

```csharp
[HttpPost]
public IActionResult CrearProducto([FromBody] Producto producto)
{
    // Lógica para crear un nuevo producto
    return CreatedAtAction(nameof(GetProducto), new { id = producto.Id }, producto);
}
```

### Versionado de la API

Para versionar una API en ASP.NET Core, podemos usar el paquete `Microsoft.AspNetCore.Mvc.Versioning`. Aquí hay un ejemplo de cómo configurarlo:

1. Instala el paquete:

```bash
dotnet add package Microsoft.AspNetCore.Mvc.Versioning
```

2. Configura el versionado en `Startup.cs`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();
    services.AddApiVersioning(config =>
    {
        config.DefaultApiVersion = new ApiVersion(1, 0);
        config.AssumeDefaultVersionWhenUnspecified = true;
        config.ReportApiVersions = true;
    });
}
```

3. Anota los controladores con la versión de la API:

```csharp
[ApiVersion("1.0")]
[Route("api/v{version:apiVersion}/[controller]")]
public class ProductosController : ControllerBase
{
    // Métodos del controlador
}
```

## Postman

Postman es una herramienta excelente para probar APIs. Puedes crear colecciones de solicitudes, configurar variables de entorno y automatizar pruebas para asegurarte de que tu API funciona como se espera.

## Ejemplo

Vamos a crear una API REST sencilla para gestionar productos. Aquí hay un ejemplo básico de cómo podríamos estructurar nuestra API en ASP.NET Core:

1. Crea el modelo `Producto`:

```csharp
public class Producto
{
    public int Id { get; set; }
    public string Nombre { get; set; }
    public decimal Precio { get; set; }
}
```

2. Crea el controlador `ProductosController`:

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductosController : ControllerBase
{
    private static List<Producto> productos = new List<Producto>
    {
        new Producto { Id = 1, Nombre = "Producto 1", Precio = 100 },
        new Producto { Id = 2, Nombre = "Producto 2", Precio = 200 }
    };

    [HttpGet]
    public IActionResult Get()
    {
        return Ok(productos);
    }

    [HttpGet("{id}")]
    public IActionResult Get(int id)
    {
        var producto = productos.FirstOrDefault(p => p.Id == id);
        if (producto == null)
        {
            return NotFound();
        }
        return Ok(producto);
    }

    [HttpPost]
    public IActionResult Post([FromBody] Producto producto)
    {
        producto.Id = productos.Max(p => p.Id) + 1;
        productos.Add(producto);
        return CreatedAtAction(nameof(Get), new { id = producto.Id }, producto);
    }

    [HttpPut("{id}")]
    public IActionResult Put(int id, [FromBody] Producto producto)
    {
        var existingProducto = productos.FirstOrDefault(p => p.Id == id);
        if (existingProducto == null)
        {
            return NotFound();
        }
        existingProducto.Nombre = producto.Nombre;
        existingProducto.Precio = producto.Precio;
        return NoContent();
    }

    [HttpDelete("{id}")]
    public IActionResult Delete(int id)
    {
        var producto = productos.FirstOrDefault(p => p.Id == id);
        if (producto == null)
        {
            return NotFound();
        }
        productos.Remove(producto);
        return NoContent();
    }
}
```

## Práctica de clase, mi primera API REST

Crea un proyecto .NET con las dependencias necesarias para crear una API REST.

Crea el controlador de FunkosRestController con las operaciones CRUD para productos (GET, POST, PUT, PATCH, DELETE) que devuelvan un mensaje de texto con cada operación.

Crea el modelo Funko con los siguientes atributos: id, nombre, precio, cantidad, imagen, categoría, fecha de creación y fecha 
de actualización.

Crea el repositorio de Funkos en base a la colección que quieras. Puedes importarla desde un fichero csv que se lea de properties o desde un fichero json, como en ejercicios anteriores.

Inyecta el repositorio el controlador de Funkos con las siguientes rutas:
- GET /funkos: Devuelve todos los funkos, si tiene el query categoría, los filtra por categoría, por ejemplo /funkos?categoria=disney (cuidado con los letras en mayúscula o minúscula)
- GET /funkos/{id}: Devuelve el funko con el id indicado, si no existe devuelve un error 404
- POST /funkos: Crea un nuevo funko y lo devuelva
- PUT /funkos/{id}: Actualiza el funko con el id indicado y lo devuelve, si no existe devuelve un error 404
- PATCH /funkos/{id}: Actualiza el funko con el id indicado y lo devuelve, si no existe devuelve un error 404
- DELETE /funkos/{id}: Borra el funko con el id indicado y devuelve un mensaje de éxito, si no existe devuelve un error 404
- Prueba las rutas con Postman
