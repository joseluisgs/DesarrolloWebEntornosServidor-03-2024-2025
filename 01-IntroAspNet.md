![](./images/01-banner.jpg)

- [.NET y ASP.NET Core](#net-y-aspnet-core)
  - [Características Principales de .NET y ASP.NET Core](#características-principales-de-net-y-aspnet-core)
  - [ASP.NET Core](#aspnet-core)
  - [Módulos Principales de .NET y ASP.NET Core](#módulos-principales-de-net-y-aspnet-core)
  - [Servicios y Dependencias en ASP.NET Core](#servicios-y-dependencias-en-aspnet-core)
  - [Inversión de Control e Inyección de Dependencias](#inversión-de-control-e-inyección-de-dependencias)


# .NET y ASP.NET Core

[.NET](https://dotnet.microsoft.com/) es una plataforma de desarrollo de código abierto para construir una amplia gama de aplicaciones, desde aplicaciones web y móviles hasta aplicaciones de escritorio y servicios en la nube. ASP.NET Core es un marco de trabajo de código abierto para construir aplicaciones web modernas y de alto rendimiento en .NET.

## Características Principales de .NET y ASP.NET Core

1. **Inversión de Control (IoC)**: .NET Core utiliza un contenedor de inyección de dependencias que gestiona la creación y el ciclo de vida de los objetos, reduciendo la dependencia entre los componentes del software.

2. **Programación Orientada a Aspectos (AOP)**: Aunque .NET Core no tiene soporte nativo para AOP como Spring, se pueden utilizar bibliotecas como [AspectCore](https://github.com/dotnetcore/AspectCore-Framework) para implementar AOP.

3. **Integración de la base de datos**: .NET Core proporciona Entity Framework Core (EF Core) como una solución ORM para facilitar la integración con diferentes bases de datos.

4. **Soporte para transacciones**: EF Core y otros ORM en .NET Core proporcionan mecanismos de gestión de transacciones.

5. **Integración con tecnologías de middleware**: ASP.NET Core permite la integración con diversas tecnologías de middleware a través de su pipeline de middleware.

6. **Soporte para pruebas**: .NET Core proporciona un fuerte soporte para pruebas unitarias y de integración a través de bibliotecas como xUnit, NUnit y MSTest.

## ASP.NET Core

ASP.NET Core es un marco de trabajo que simplifica el desarrollo de aplicaciones web modernas. Sus características clave incluyen:

1. **Autoconfiguración**: ASP.NET Core puede configurar automáticamente una aplicación basada en las dependencias y servicios registrados en el contenedor de servicios.

2. **Standalone**: ASP.NET Core permite crear aplicaciones independientes con servidores web embebidos como Kestrel, eliminando la necesidad de un servidor web separado.

3. **Opinión predefinida**: ASP.NET Core tiene una opinión predefinida para configurar proyectos, aunque permite a los desarrolladores modificar la configuración según sus necesidades.

4. **Paquetes NuGet**: Proporciona paquetes NuGet que son un conjunto de dependencias convenientes que simplifican la configuración de la aplicación.

5. **Middleware**: Proporciona un pipeline de middleware para manejar solicitudes HTTP y agregar funcionalidades como autenticación, autorización, logging, etc.

6. **Pruebas**: ASP.NET Core proporciona soporte para pruebas con bibliotecas de pruebas como xUnit, NUnit y MSTest, facilitando la escritura de pruebas para las aplicaciones.

## Módulos Principales de .NET y ASP.NET Core

**.NET Core** está diseñado de manera modular, permitiéndote elegir y usar solo los módulos que necesitas para tu aplicación. Aquí te describo algunos de los módulos más comunes:

1. **.NET Core**: El núcleo del framework que incluye el runtime, la biblioteca de clases base y el compilador.

2. **Entity Framework Core (EF Core)**: Un ORM que facilita el acceso a la base de datos y el mapeo objeto-relacional.

3. **ASP.NET Core MVC**: Un marco para el desarrollo de aplicaciones web y APIs RESTful utilizando el patrón Modelo-Vista-Controlador.

4. **ASP.NET Core Razor Pages**: Una alternativa a MVC para construir aplicaciones web basadas en páginas.

5. **ASP.NET Core Blazor**: Un framework para construir aplicaciones web interactivas del lado del cliente usando C# en lugar de JavaScript.

6. **ASP.NET Core SignalR**: Una biblioteca para agregar funcionalidad en tiempo real a las aplicaciones web.

Además de estos módulos, .NET tiene varios proyectos que extienden su funcionalidad:

1. **ASP.NET Core Identity**: Un sistema de autenticación y autorización para ASP.NET Core.

2. **Azure SDK**: Proporciona herramientas para el desarrollo de aplicaciones en la nube con Azure.

3. **gRPC**: Un marco para construir servicios de alto rendimiento y de código abierto.

4. **Dapper**: Un micro ORM que facilita el acceso a la base de datos con un enfoque ligero.

5. **Serilog**: Una biblioteca de logging estructurado para .NET.

## Servicios y Dependencias en ASP.NET Core

En ASP.NET Core, los **Servicios** son los objetos fundamentales que forman la columna vertebral de tus aplicaciones. Son objetos que son instanciados, ensamblados y administrados por el contenedor de inyección de dependencias de ASP.NET Core.

La creación de servicios se puede configurar de varias maneras en ASP.NET Core:

1. **Anotaciones**: Puedes usar anotaciones como `AddSingleton`, `AddScoped`, y `AddTransient` para registrar servicios en el contenedor de servicios.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IMyService, MyService>();
    services.AddScoped<IOtherService, OtherService>();
    services.AddTransient<IAnotherService, AnotherService>();
}
```

2. **Inyección de Dependencias**: Una vez que los servicios están en el contenedor, puedes inyectarlos en otras partes de tu aplicación usando el constructor o la propiedad de inyección.

```csharp
public class MyController : Controller
{
    private readonly IMyService _myService;

    public MyController(IMyService myService)
    {
        _myService = myService;
    }

    //...
}
```

En este ejemplo, ASP.NET Core inyectará automáticamente el servicio `IMyService` en `MyController` cuando este último sea creado.

Los servicios son útiles porque te permiten abstraer la creación y gestión de objetos, haciendo que tu código sea más limpio, más fácil de probar y más modular. Además, los servicios en ASP.NET Core pueden tener diferentes ámbitos (singleton, scoped, transient) que te permiten controlar cuándo y cómo se crean y destruyen los servicios.

## Inversión de Control e Inyección de Dependencias

**Inversión de Control (IoC)** y **Inyección de Dependencias (DI)** son dos conceptos fundamentales en .NET y ASP.NET Core que facilitan la creación de aplicaciones modulares y mantenibles.