# SESION DE LABORATORIO N° 03: PRUEBAS UNITARIAS CON NUNIT

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
dotnet new sln -o Bank
```
3. Acceder a la solución creada y ejecutar el siguiente comando para crear una nueva libreria de clases y adicionarla a la solución actual.
```
cd Bank
dotnet new classlib -o Bank.Domain
dotnet sln add ./Bank.Domain/Bank.Domain.csproj
```
4. Ejecutar el siguiente comando para crear un nuevo proyecto de pruebas y adicionarla a la solución actual
```
dotnet new nunit -o Bank.Domain.Tests
dotnet sln add ./Bank.Domain.Tests/Bank.Domain.Tests.csproj
dotnet add ./Bank.Domain.Tests/Bank.Domain.Tests.csproj reference ./Bank.Domain/Bank.Domain.csproj
```
5. Iniciar Visual Studio Code (VS Code) abriendo el folder de la solución como proyecto. En el proyecto Primes.Lib, si existe un archivo Class1.cs proceder a eliminarlo. Asimismo en el proyecto Primes.Tests si existiese un archivo UnitTest1, tambièn proceder a aliminarlo.

6. En VS Code, en el proyecto Primes.Lib proceder a crear el archivo PrimeServices e introdudir el siguiente código, para generar un metodo que devolvera una excepciòn inicialmente:
```C#
namespace Primes.Lib
{
    public class PrimeService
    {
        public bool IsPrime(int candidate)
        {
            throw new NotImplementedException("Not implemented.");
        }
    }
}
```
7. Luego en el proyecto Primes.Tests añadir un nuevo archivo PrimeServiceTests.cs e introducir el siguiente código:
```C#
using Primes.Lib;
using Xunit;

namespace Primes.Tests
{
    public class PrimeServiceTests
    {
        private readonly PrimeService _primeService;
        public PrimeServiceTests()
        {
            _primeService = new PrimeService();
        }

        [Fact]
        public void IsPrime_InputIs1_ReturnFalse()
        {
            var result = _primeService.IsPrime(1);
            Assert.False(result, "1 should not be prime");
        }
    }
}
```
8. Abrir un terminal en VS Code (CTRL + Ñ) o vuelva al terminal anteriormente abierto, y ejecutar los comandos:
```Bash
dotnet test --collect:"XPlat Code Coverage"
```
9. El paso anterior debe producir un error por lo que sera necesario escribir el código mecesario para que el test funcione. 
```Bash
Failed!  - Failed:     1, Passed:     0, Skipped:     0, Total:     1, Duration: < 1 ms
```
10. En el proyecto Primes.Lib, modificar la clase PrimeService, con el siguiente contenido:
```C#
namespace Primes.Lib
{
    public class PrimeService
    {
        public bool IsPrime(int candidate)
        {
            if (candidate == 1) return false;
            throw new NotImplementedException("Not implemented.");
        }
    }
}
```
11. Ejecutar nuevamente el pase 8 y ahora deberia devolver algo similar a lo siguiente:
```Bash
Passed!  - Failed:     0, Passed:     1, Skipped:     0, Total:     1, Duration: < 1 ms
```
12. Con la finalidad de aumentar la confienza en la aplicación, se ampliará el rango de pruebas para lo cual editar la clase de prueba PrimeServiceTests y adicionar el método siguiente, que adiciona tres valores y escenarios de pruebas más:
```C#
        [Theory]
        [InlineData(-1)]
        [InlineData(0)]
        [InlineData(1)]
        public void IsPrime_ValuesLessThan2_ReturnFalse(int value)
        {
            var result = _primeService.IsPrime(value);
            Assert.False(result, $"{value} should not be prime");
        }
```
13. Ejecutar nuevamente el paso 8 para lo cual se obtendra un error similar al siguiente:
```
Failed!  - Failed:     2, Passed:     2, Skipped:     0, Total:     4, Duration: 46 ms
```
14. A fin de que las pruebas puedan ejecutarse correctamente, modificar la clase PrimeService de la siguiente manera:
```C#
namespace Primes.Lib
{
    public class PrimeService
    {
        public bool IsPrime(int candidate)
        {
            if (candidate < 2) return false;
            throw new NotImplementedException("Not implemented.");
        }
    }
}
```
15. Volver a ejecutar el paso 8 y verificar el resultado, debería ser similar a lo siguiente
```
Passed!  - Failed:     0, Passed:     4, Skipped:     0, Total:     4, Duration: 2 ms
```
16. Finalmente proceder a verificar la cobertura, dentro del proyecto Primes.Tests se dede haber generado una carpeta o directorio TestResults, en el cual posiblemente exista otra subpcarpeta o subdirectorio conteniendo un archivo con nombre `coverage.cobertura.xml`, si existe ese archivo proceder a ejecutar los siguientes comandos desde la linea de comandos abierta anteriomente, de los contrario revisar el paso 8:
```
dotnet tool install -g dotnet-reportgenerator-globaltool
ReportGenerator "-reports:./*/*/*/coverage.cobertura.xml" "-targetdir:Cobertura" -reporttypes:HTML
```
17. El comando anterior primero proceda instalar una herramienta llamada ReportGenerator (https://reportgenerator.io/) la cual mediante la segunda parte del comando permitira generar un reporte en formato HTML con la cobertura obtenida de la ejecución de las pruebas. Este reporte debe localizarse dentro de una carpeta llamada Cobertura y puede acceder a el abriendo con un navegador de internet el archivo index.htm.

---
## Actividades Encargadas
1. Adicionar los escenarios, casos de prueba, metodos de prueba y modificaciones para verificar los números del 2 al 20.
