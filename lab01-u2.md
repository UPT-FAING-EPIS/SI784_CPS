# SESION DE LABORATORIO N° 01: PRUEBAS UNITARIAS

## OBJETIVOS
  * Comprender el funcionamiento de las tecnicas de Analisis Estatico mediante su aplicación en una herramienta y una aplicación.

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
  * Clonar el repositorio mediante git para tener los recursos necesaarios

## DESARROLLO
1. Iniciar la aplicación Powershell o Windows Terminal en modo administrador 
2. Ejecutar el siguiente comando para crear una nueva solución
```
dotnet new sln -o MyMath
```
3. Acceder a la solución creada y ejecutar el siguiente comando para crear una nueva libreria de clases y adicionarla a la solución actual.
```
cd MyMath
dotnet new classlib -o Math.Lib
dotnet sln add .\Math.Lib\Math.Lib.csproj
```
4. Ejecutar el siguiente comando para crear un nuevo proyecto de pruebas y adicionarla a la solución actual
```
dotnet new mstest -o Math.Tests
dotnet sln add .\Math.Tests\Math.Tests.csproj
dotnet add .\Math.Tests\Math.Tests.csproj reference .\Math.Lib\Math.Lib.csproj
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
```
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
12. Ejecutar el siguiente comando en Powershell, iniciar la sesión de revisión de sonarqube.
```
dotnet sonarscanner begin /d:sonar.host.url="http://localhost:9000" /d:sonar.login=admin /d:sonar.password=admin /k:”aplicacionNetCore”
```
13. Ejecutar el siguiente comando en Powershell para construir la aplicación.
```
dotnet build
```
14. Ejecutar el siguiente comando en Powershell finalizar la revisión de sonarqube.
```
dotnet sonarscanner end /d:sonar.login=admin /d:sonar.password=admin
```
15. Ejecutar el siguiente comando en Powershell para eliminar el contenedor generado.
```
docker rm -f sonarqube
```
16. Verificar la instancia de contenedor ya no se encuentra activa
```
docker ps
```
---
## Actividades Encargadas
1. Realizar la misma actividad para su proyecto, teniendo en cuenta que la forma de invocar Sonnar Scaner puede se distinta, para mayor información https://docs.sonarqube.org/latest/analyzing-source-code/scanners/sonarscanner/
