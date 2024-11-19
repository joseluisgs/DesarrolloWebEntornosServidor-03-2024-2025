
![](images/13-banner.png)

- [Perfiles en ASP.NET Core](#perfiles-en-aspnet-core)
  - [Configuración de Entornos](#configuración-de-entornos)
  - [Ejemplo de Archivos de Configuración](#ejemplo-de-archivos-de-configuración)
    - [appsettings.json](#appsettingsjson)
    - [appsettings.Development.json](#appsettingsdevelopmentjson)
    - [appsettings.Production.json](#appsettingsproductionjson)
    - [Cambiar de Entorno](#cambiar-de-entorno)
    - [Práctica de Clase: Perfiles y Configuración de Entorno](#práctica-de-clase-perfiles-y-configuración-de-entorno)
- [Práctica de clase: perfiles de desarrollo y producción](#práctica-de-clase-perfiles-de-desarrollo-y-producción)



# Perfiles en ASP.NET Core

ASP.NET Core utiliza un sistema de configuración que permite cargar diferentes configuraciones según el entorno en el que se ejecuta la aplicación. Los entornos comunes son `Development`, `Staging` y `Production`. Puedes definir archivos de configuración específicos para cada entorno, como `appsettings.Development.json` y `appsettings.Production.json`.

## Configuración de Entornos

En ASP.NET Core, el entorno se especifica mediante la variable de entorno `ASPNETCORE_ENVIRONMENT`. Según su valor, la aplicación cargará el archivo de configuración correspondiente.

## Ejemplo de Archivos de Configuración

### appsettings.json

Este es el archivo de configuración global que se aplica a todos los entornos:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### appsettings.Development.json

Este archivo contiene configuraciones específicas para el entorno de desarrollo:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=DevelopmentDb;Trusted_Connection=True;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "Microsoft.AspNetCore": "Information"
    }
  }
}
```

### appsettings.Production.json

Este archivo contiene configuraciones específicas para el entorno de producción:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=production_server;Database=ProductionDb;User Id=myUsername;Password=myPassword;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Error",
      "Microsoft.AspNetCore": "Warning"
    }
  }
}
```

### Cambiar de Entorno

Para cambiar de entorno, puedes establecer la variable de entorno `ASPNETCORE_ENVIRONMENT` antes de ejecutar la aplicación. Por ejemplo, en un terminal de comandos en Windows, puedes ejecutar:

```bash
set ASPNETCORE_ENVIRONMENT=Production
dotnet run
```

En Linux o macOS, el comando sería:

```bash
export ASPNETCORE_ENVIRONMENT=Production
dotnet run
```

### Práctica de Clase: Perfiles y Configuración de Entorno

1. **Configura los Perfiles de Desarrollo y Producción:**
   - Crea los archivos `appsettings.Development.json` y `appsettings.Production.json` con las configuraciones necesarias para tus entornos.
   - Usa la variable de entorno `ASPNETCORE_ENVIRONMENT` para alternar entre los entornos.

2. **Configura Swagger para Ejecutarse en el Perfil de Desarrollo:**
   - En `Startup.cs` o el archivo de configuración del middleware de tu aplicación, añade Swagger solo si el entorno es `Development`.

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseSwagger();
        app.UseSwaggerUI(c => c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1"));
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseRouting();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

Con esta configuración, Swagger solo estará disponible cuando la aplicación se ejecute en el entorno de desarrollo. Esto es útil para mantener la documentación de la API accesible durante el desarrollo, pero no en producción.

# Práctica de clase: perfiles de desarrollo y producción
1. Configura los perfiles de desarrollo y producción de tu servicio
2. Muestra el perfil activo al iniciar el servicio
3. Configura Swagger para que se ejecute en el perfil de desarrollo