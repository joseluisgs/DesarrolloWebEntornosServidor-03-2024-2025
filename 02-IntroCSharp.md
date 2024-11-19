![](images/02-banner.jpg)

- [C#](#c)
  - [Sistema de tipos](#sistema-de-tipos)
  - [Nulabilidad y Opcionales](#nulabilidad-y-opcionales)
    - [Encadenamiento opcional](#encadenamiento-opcional)
  - [Programación Orientada a Objetos](#programación-orientada-a-objetos)
  - [Objetos en C#](#objetos-en-c)
    - [Clases en C#](#clases-en-c)
      - [Herencia](#herencia)
      - [Clases abstractas](#clases-abstractas)
      - [Constructores múltiples](#constructores-múltiples)
    - [Interfaces en C#](#interfaces-en-c)
    - [Bucles](#bucles)
  - [Funciones](#funciones)
  - [Operadores params y params](#operadores-params-y-params)
    - [Operador params](#operador-params)
    - [Operador spread](#operador-spread)
  - [Asincronía en C#](#asincronía-en-c)
  - [Operaciones con colecciones](#operaciones-con-colecciones)
  - [LINQ](#linq)



# C#
C# es un lenguaje de programación desarrollado por Microsoft que se basa en el lenguaje C. Es un lenguaje de propósito general, orientado a objetos y con tipado estático que se utiliza principalmente para el desarrollo en la plataforma .NET. C# es conocido por su simplicidad, modernidad y flexibilidad, lo que permite desarrollar aplicaciones de escritorio, web, móviles y juegos.

C# se compila a un lenguaje intermedio (IL) que se ejecuta en el Common Language Runtime (CLR) de .NET, lo que permite una alta portabilidad y rendimiento.

C# trata de resolver muchos de los problemas de los lenguajes anteriores centrándose en la productividad de los desarrolladores y la robustez del código. Proporciona características avanzadas como el manejo de excepciones, la recolección de basura automática y el soporte para la programación asincrónica, lo que facilita la creación de aplicaciones escalables y mantenibles.

## Sistema de tipos
Además de los tipos `string` y `int`, C# también admite los siguientes tipos básicos:

- `bool`: tipo de dato lógico que representa verdadero o falso.
- `array`: tipo de dato estructurado que permite almacenar una colección de elementos.
- `enum`: representa al tipo enumeración. Una enumeración es una forma de dar nombres descriptivos a los conjuntos de valores numéricos.
- `object`: el tipo base de todos los tipos en C#.
- `dynamic`: indica que la variable puede ser de cualquier tipo y se resuelve en tiempo de ejecución.
- `void`: indica que un método no devolverá ningún valor.
- `nullable`: este tipo permite que los tipos de valor (como `int`, `bool`, etc.) puedan ser nulos.

C# usa inferencia de tipos con la palabra clave `var` para inferir los tipos de las variables cuando no se especifican. Por ejemplo, si asignamos un valor a una variable, C# infiere el tipo de la variable en función del tipo del valor asignado.

Además, C# permite que una variable almacene distintos tipos gracias a las interfaces y tipos base, aunque no utiliza un operador de unión de tipos como `|`.

```csharp
int age = 25;
string name = "Juan";
bool isStudent = true;
int[] numbers = { 1, 2, 3, 4, 5 };
object person = new { Name = "Juan", Age = 25 };
string? nullableValue = null;
dynamic anyValue = "Hola";
var tupleValue = Tuple.Create("Juan", 25);
enum Color { Red, Green, Blue };
Color color = Color.Red;
void MyFunction(string param) { Console.WriteLine(param); }
object unionType = "Hola";
```

## Nulabilidad y Opcionales
C# permite especificar si una variable puede ser nula o no utilizando el operador `?` para los tipos de valor y el tipo base `Nullable<T>`. Puedes utilizar el operador `??` para proporcionar un valor predeterminado en caso de que la variable sea nula.

```csharp
string? nullableValue = null;
int? nullableInt = null;
nullableInt = 5;
```

### Encadenamiento opcional
El encadenamiento opcional es una característica introducida en C# 8.0 que te permite acceder a propiedades de un objeto de forma segura, incluso si alguna de las propiedades en la cadena de acceso es nula.

Imaginemos que tienes un objeto `persona` con la siguiente estructura:

```csharp
var persona = new 
{
    Nombre = "Juan",
    Direccion = new 
    {
        Calle = "Calle Principal",
        Ciudad = "Ciudad de Ejemplo",
        CodigoPostal = "12345"
    }
};
```

Si quieres acceder al código postal de la dirección de la persona, normalmente lo harías así:

```csharp
var codigoPostal = persona.Direccion.CodigoPostal;
```

Sin embargo, si por alguna razón el objeto `Direccion` no está definido, obtendrías una excepción. Aquí es donde entra en juego el encadenamiento opcional.

Usando el encadenamiento opcional, puedes acceder de forma segura a la propiedad `CodigoPostal` incluso si `Direccion` es nulo. Para hacer esto, simplemente añade un signo de interrogación `?` después de la propiedad o el objeto que podría ser nulo:

```csharp
var codigoPostal = persona.Direccion?.CodigoPostal;
```

En este caso, si `Direccion` es nulo, la expresión `persona.Direccion?.CodigoPostal` evaluará a `null` en lugar de lanzar una excepción. Esto te permite evitar errores y manejar de manera más segura las propiedades anidadas.

Si deseas realizar una comprobación adicional, puedes encadenar múltiples propiedades opcionales:

```csharp
var codigoPostal = persona?.Direccion?.CodigoPostal;
```

En este ejemplo, si `persona` es nulo o `Direccion` es nulo, la expresión `persona?.Direccion?.CodigoPostal` evaluará a `null`.

El encadenamiento opcional también se puede utilizar con métodos y funciones:

```csharp
var longitudNombre = persona?.Nombre?.Length;
```

Aquí, si `persona` es nulo o `Nombre` es nulo, `persona?.Nombre?.Length` evaluará a `null`.

## Programación Orientada a Objetos
C# es un lenguaje orientado a objetos que permite la creación de clases y objetos para modelar el mundo real. Las clases en C# pueden tener propiedades, métodos y eventos, y pueden heredar de otras clases.

## Objetos en C#
### Clases en C#
Las clases en C# se definen utilizando la palabra clave `class`. Aquí tienes un ejemplo de una clase simple:

```csharp
public class Persona
{
    public string Nombre { get; set; }
    public int Edad { get; set; }

    public void Saludar()
    {
        Console.WriteLine($"Hola, mi nombre es {Nombre} y tengo {Edad} años.");
    }
}
```

#### Herencia
C# soporta la herencia simple, lo que significa que una clase puede heredar de una sola clase base. Se utiliza la palabra clave `:`

```csharp
public class Estudiante : Persona
{
    public string Escuela { get; set; }

    public void Estudiar()
    {
        Console.WriteLine($"Estoy estudiando en {Escuela}.");
    }
}
```

#### Clases abstractas
Las clases abstractas no pueden ser instanciadas y pueden contener métodos abstractos que deben ser implementados por las clases derivadas.

```csharp
public abstract class Animal
{
    public abstract void HacerSonido();
}

public class Perro : Animal
{
    public override void HacerSonido()
    {
        Console.WriteLine("Guau");
    }
}
```

#### Constructores múltiples
C# permite definir múltiples constructores utilizando la sobrecarga de métodos.

```csharp
public class Persona
{
    public string Nombre { get; set; }
    public int Edad { get; set; }

    public Persona() { }

    public Persona(string nombre, int edad)
    {
        Nombre = nombre;
        Edad = edad;
    }
}
```

### Interfaces en C#
Las interfaces en C# se definen utilizando la palabra clave `interface` y pueden contener declaraciones de métodos, propiedades, eventos e indexadores.

```csharp
public interface ISaludable
{
    void Saludar();
}

public class Persona : ISaludable
{
    public string Nombre { get; set; }
    public void Saludar()
    {
        Console.WriteLine($"Hola, mi nombre es {Nombre}.");
    }
}
```

### Bucles
C# soporta varios tipos de bucles, incluyendo `for`, `foreach`, `while` y `do-while`.

```csharp
for (int i = 0; i < 10; i++)
{
    Console.WriteLine(i);
}

foreach (var numero in new int[] { 1, 2, 3, 4, 5 })
{
    Console.WriteLine(numero);
}

int j = 0;
while (j < 10)
{
    Console.WriteLine(j);
    j++;
}

int k = 0;
do
{
    Console.WriteLine(k);
    k++;
} while (k < 10);
```

## Funciones
Las funciones en C# se definen dentro de clases y pueden tener parámetros y valores de retorno.

```csharp
public class Calculadora
{
    public int Sumar(int a, int b)
    {
        return a + b;
    }
}
```

## Operadores params y params
### Operador params
El operador `params` permite pasar un número variable de argumentos a un método.

```csharp
public void ImprimirNumeros(params int[] numeros)
{
    foreach (var numero in numeros)
    {
        Console.WriteLine(numero);
    }
}
```

### Operador spread
C# no tiene un operador spread como tal, pero puedes lograr efectos similares utilizando colecciones y métodos que aceptan parámetros variables.

## Asincronía en C#
C# soporta la programación asincrónica utilizando las palabras clave `async` y `await`. Esto permite escribir código no bloqueante que puede realizar operaciones de I/O de manera eficiente.

```csharp
public async Task<string> ObtenerDatosAsync()
{
    using (HttpClient client = new HttpClient())
    {
        var respuesta = await client.GetStringAsync("https://api.example.com/datos");
        return respuesta;
    }
}
```

## Operaciones con colecciones
C# proporciona varias clases de colecciones en el espacio de nombres `System.Collections` y `System.Collections.Generic`, incluyendo `List<T>`, `Dictionary<TKey, TValue>`, y `Queue<T>`.

```csharp
var lista = new List<int> { 1, 2, 3, 4, 5 };
lista.Add(6);
lista.Remove(1);

var diccionario = new Dictionary<string, int>
{
    { "uno", 1 },
    { "dos", 2 }
};
diccionario["tres"] = 3;
```

## LINQ
LINQ (Language Integrated Query) es una característica poderosa de C# que permite realizar consultas sobre colecciones de datos de manera declarativa.

```csharp
var numeros = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

var numerosPares = from numero in numeros
                   where numero % 2 == 0
                   select numero;

foreach (var numero in numerosPares)
{
    Console.WriteLine(numero);
}

// Con métodos de extensión
var numerosImpares = numeros.Where(n => n % 2 != 0).Select(n => n);

foreach (var numero in numerosImpares)
{
    Console.WriteLine(numero);
}
```
