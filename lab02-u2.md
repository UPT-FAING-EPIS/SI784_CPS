# SESION DE LABORATORIO N° 02: PRUEBAS UNITARIAS CON XUNIT

## OBJETIVOS
  * Comprender el funcionamiento de las pruebas unitarias dentro de una aplicación utilizando el Framework de pruebas XUnit.

## REQUERIMIENTOS
  * Conocimientos: 
    - Conocimientos básicos de Bash (powershell).
    - Conocimientos básicos de Contenedores (Docker).
  * Hardware:
    - Virtualization activada en el BIOS..
    - CPU SLAT-capable feature.
    - Al menos 4GB de RAM.
  * Software:
    - Windows 10 64bit: Pro, Enterprise o Education (1607 Anniversary Update, Build 14393 o Superior)
    - Docker Desktop 
    - Powershell versión 7.x
    - Net 6
    - Visual Studio Code

## CONSIDERACIONES INICIALES
  * Clonar el repositorio mediante git para tener los recursos necesarios

## DESARROLLO
1. Iniciar la aplicación Powershell o Windows Terminal en modo administrador 
2. Ejecutar el siguiente comando para crear una nueva solución
```
dotnet new sln -o Primes
```
3. Acceder a la solución creada y ejecutar el siguiente comando para crear una nueva libreria de clases y adicionarla a la solución actual.
```
cd Primes
dotnet new classlib -o Primes.Lib
dotnet sln add .\Primes.Lib\Primes.Lib.csproj
```
4. Ejecutar el siguiente comando para crear un nuevo proyecto de pruebas y adicionarla a la solución actual
```
dotnet new xunit -o Primes.Tests
dotnet sln add .\Primes.Tests\Primes.Tests.csproj
dotnet add .\Primes.Tests\Primes.Tests.csproj reference .\Primes.Lib\Primes.Lib.csproj
```
5. Iniciar Visual Studio Code abriendo el folder de la solución como proyecto. Luego en el proyecto Math.Tests añadir un nuevo archivo RooterTests.cs e introducir el siguiente código:
```C#
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace Math.Tests
{
    [TestClass]
    public class RooterTests
    {
        [TestMethod]
        public void BasicRooterTest()
        {
            Rooter rooter = new Rooter();
            double expectedResult = 2.0;
            double input = expectedResult * expectedResult;
            double actualResult = rooter.SquareRoot(input);
            Assert.AreEqual(expectedResult, actualResult, delta: expectedResult / 100);
        }
    }
}
```
6. Abrir un terminal en VS Code (CTRL + Ñ) o vuelva al terminal anteriormente abierto, y ejecutar los comandos:
```
dotnet restore
dotnet test
```
7. El paso anterior debe producir un error por lo que sera necesario escribir el código mecesario para que el test funcione. 
8. En el proyecto Math.Lib, añadir un nuevo archivo Rooter.cs, con el siguiente contenido:
```C#
namespace Math.Lib
{
    public class Rooter
    {
        public double SquareRoot(double input)
        {
            return input / 2;
        }
    }
}
```
9. Seguidamente modificar el archivo RooterTests.cs y adicionar al inicio del mismo el siguiente contenido:
```C#
using Math.Lib;
```
10. Ejecutar nuevamente el pase 6 y ahora deberia devolver algo similar a lo siguiente:
```
Correctas! - Con error:     0, Superado:     2, Omitido:     0, Total:     2, Duración: 12 ms - Math.Tests.dll
```
11. Con la finalidad de aumentar la confienza en la aplicación, se ampliará el rango de pruebas para lo cual editar la clase de prueba RooterTests y adicionar los métodos siguientes:
```C#
        [TestMethod]
        public void RooterValueRange()
        {
            Rooter rooter = new Rooter();
            for (double expected = 1e-8; expected < 1e+8; expected *= 3.2)
                RooterOneValue(rooter, expected);
        }
        private void RooterOneValue(Rooter rooter, double expectedResult)
        {
            double input = expectedResult * expectedResult;
            double actualResult = rooter.SquareRoot(input);
            Assert.AreEqual(expectedResult, actualResult, delta: expectedResult / 1000);
        }
```
12. Ejecutar nuevamente el paso 6 para lo cual se obtendra un error similar al siguiente:
```
Con error! - Con error:     1, Superado:     2, Omitido:     0, Total:     3, Duracin: 30 ms - Math.Tests.dll
```
13. A fin de que las pruebas puedan ejecutarse correctamente, modificar la clase Rooter de la siguiente manera:
```C#
namespace Math.Lib
{
    public class Rooter
    {
        public double SquareRoot(double input)
        {
            double result = input;
            double previousResult = -input;
            while (System.Math.Abs(previousResult - result)
                > result / 1000)
            {
            previousResult = result;
            result = result - (result * result - input) / (2 * result);
            }
            return result;
        }
    }
}
```
14. Volver a ejecutar el paso 6 y verificar el resultado, debería ser similar a lo siguiente
```
Correctas! - Con error:     0, Superado:     3, Omitido:     0, Total:     3, Duracin: 14 ms - Math.Tests.dll
```
15. Adicionar un nuevo caso de prueba con excepción en la clase RooterTests:
```C#
        [TestMethod]
        public void RooterTestNegativeInputx()
        {
            Rooter rooter = new Rooter();
            try
            {
                rooter.SquareRoot(-10);
            }
            catch (System.ArgumentOutOfRangeException)
            {
                return;
            }
            Assert.Fail();
        }
```
16. Modificar la clase Rooter adicionando una nueva condición:
```C#
namespace Math.Lib
{
    public class Rooter
    {
        public double SquareRoot(double input)
        {
            if (input <= 0.0) throw new ArgumentOutOfRangeException();
            double result = input;
            double previousResult = -input;
            while (System.Math.Abs(previousResult - result)
                > result / 1000)
            {
            previousResult = result;
            result = result - (result * result - input) / (2 * result);
            }
            return result;
        }
    }
}
```
17. Al ejecutar las pruebas (paso 6) se obtendrá el siguiente resultado:
```
Correctas! - Con error:     0, Superado:     4, Omitido:     0, Total:     4, Duración: 13 ms - Math.Tests.dll
```

---
## Actividades Encargadas
1. Adicionar un nuevo escenario de prueba donde se maneje una excepción con un mensaje que diga "El valor ingresado es invalido, solo se puede ingresar números positivos".
