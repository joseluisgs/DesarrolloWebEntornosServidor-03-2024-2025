
![](images/07-banner.png)

- [Almacenamiento de archivos en ASP.NET Core](#almacenamiento-de-archivos-en-aspnet-core)
  - [Añadiendo subida de archivos a nuestros endpoints](#añadiendo-subida-de-archivos-a-nuestros-endpoints)
    - [Configurando el sistema de almacenamiento](#configurando-el-sistema-de-almacenamiento)
    - [Creando endpoints para gestionar archivos](#creando-endpoints-para-gestionar-archivos)
    - [Añadiendo subida de archivos a otros endpoints](#añadiendo-subida-de-archivos-a-otros-endpoints)
    - [Configuración en `appsettings.json`](#configuración-en-appsettingsjson)
    - [Configuración adicional](#configuración-adicional)
- [Práctica de clase: Almacenamiento de Ficheros](#práctica-de-clase-almacenamiento-de-ficheros)


# Almacenamiento de archivos en ASP.NET Core

En este tutorial, vamos a ver cómo subir archivos a nuestra aplicación ASP.NET Core utilizando una API mínima.

## Añadiendo subida de archivos a nuestros endpoints

Primero, necesitamos configurar nuestro proyecto para permitir la subida de archivos. Para ello, vamos a crear un servicio de almacenamiento que nos permita guardar los archivos en el sistema de archivos.

### Configurando el sistema de almacenamiento

1. **Crear el servicio de almacenamiento de archivos:**

   Crea una interfaz `IStorageService` y su implementación `FileSystemStorageService`.

   ```csharp
   public interface IStorageService
   {
       Task<string> StoreFileAsync(IFormFile file);
       Task<FileStream> LoadFileAsStreamAsync(string filename);
       string GetFileUrl(string filename);
   }

   public class FileSystemStorageService : IStorageService
   {
       private readonly string _rootPath;

       public FileSystemStorageService(IConfiguration configuration)
       {
           _rootPath = configuration.GetValue<string>("UploadRootPath");
       }

       public async Task<string> StoreFileAsync(IFormFile file)
       {
           if (file == null || file.Length == 0)
               throw new ArgumentException("File is empty");

           var filePath = Path.Combine(_rootPath, file.FileName);

           using (var stream = new FileStream(filePath, FileMode.Create))
           {
               await file.CopyToAsync(stream);
           }

           return file.FileName;
       }

       public async Task<FileStream> LoadFileAsStreamAsync(string filename)
       {
           var filePath = Path.Combine(_rootPath, filename);

           if (!File.Exists(filePath))
               throw new FileNotFoundException("File not found", filename);

           return new FileStream(filePath, FileMode.Open, FileAccess.Read);
       }

       public string GetFileUrl(string filename)
       {
           return $"/api/files/{filename}";
       }
   }
   ```

2. **Configurar la dependencia en `Program.cs`:**

   ```csharp
   var builder = WebApplication.CreateBuilder(args);

   // Add services to the container.
   builder.Services.AddSingleton<IStorageService, FileSystemStorageService>();

   var app = builder.Build();

   // Configure the HTTP request pipeline.
   if (app.Environment.IsDevelopment())
   {
       app.UseDeveloperExceptionPage();
   }

   app.UseStaticFiles();
   app.UseRouting();

   app.MapGet("/", () => "Hello World!");

   app.Run();
   ```

### Creando endpoints para gestionar archivos

Ahora vamos a crear los endpoints para subir y descargar archivos.

1. **Endpoint para subir archivos:**

   ```csharp
   app.MapPost("/api/files", async (IStorageService storageService, IFormFile file) =>
   {
       if (file == null || file.Length == 0)
       {
           return Results.BadRequest("No file uploaded");
       }

       var filename = await storageService.StoreFileAsync(file);
       var fileUrl = storageService.GetFileUrl(filename);

       return Results.Created(fileUrl, new { Url = fileUrl });
   }).Accepts<IFormFile>("multipart/form-data");
   ```

2. **Endpoint para descargar archivos:**

   ```csharp
   app.MapGet("/api/files/{filename}", async (IStorageService storageService, string filename) =>
   {
       try
       {
           var fileStream = await storageService.LoadFileAsStreamAsync(filename);
           return Results.File(fileStream, "application/octet-stream", enableRangeProcessing: true);
       }
       catch (FileNotFoundException)
       {
           return Results.NotFound();
       }
   });
   ```

### Añadiendo subida de archivos a otros endpoints

Si deseas agregar la funcionalidad de subir archivos a otros endpoints, puedes hacerlo de manera similar a cómo lo hicimos anteriormente. Por ejemplo, para un modelo de `Raqueta`:

1. **Modelo `Raqueta`:**

   ```csharp
   public class Raqueta
   {
       public long Id { get; set; }
       public string Nombre { get; set; }
       public string ImagenUrl { get; set; }
   }
   ```

2. **Endpoint para actualizar la imagen de una `Raqueta`:**

   ```csharp
   app.MapPatch("/api/raquetas/imagen/{id}", async (IStorageService storageService, long id, IFormFile file) =>
   {
       // Aquí deberías buscar la raqueta en tu base de datos
       // var raqueta = await _context.Raquetas.FindAsync(id);

       if (file == null || file.Length == 0)
       {
           return Results.BadRequest("No file uploaded");
       }

       var filename = await storageService.StoreFileAsync(file);
       var fileUrl = storageService.GetFileUrl(filename);

       // Actualiza la URL de la imagen en la raqueta
       // raqueta.ImagenUrl = fileUrl;

       // Guarda los cambios en la base de datos
       // await _context.SaveChangesAsync();

       return Results.Ok(new { Url = fileUrl });
   }).Accepts<IFormFile>("multipart/form-data");
   ```

### Configuración en `appsettings.json`

Asegúrate de tener configurado el directorio de almacenamiento en tu archivo `appsettings.json`.

```json
{
  "UploadRootPath": "uploads"
}
```

### Configuración adicional

No olvides crear el directorio de almacenamiento si no existe y configurar los permisos necesarios para que la aplicación pueda escribir en él.

```csharp
var uploadRootPath = builder.Configuration.GetValue<string>("UploadRootPath");
if (!Directory.Exists(uploadRootPath))
{
    Directory.CreateDirectory(uploadRootPath);
}
```

Con esto, deberías tener una configuración básica para el almacenamiento de archivos en una aplicación ASP.NET Core utilizando una API mínima. Puedes extender este ejemplo para agregar más funcionalidad según tus necesidades.
  
# Práctica de clase: Almacenamiento de Ficheros
1. Crea el sistema de almacenamiento para subir imágenes a los Funkos.
2. Permite consultar y devolver la imagen de un Funko.
3. Testea los nuevos elementos tanto a nivel unitario como e2e.
