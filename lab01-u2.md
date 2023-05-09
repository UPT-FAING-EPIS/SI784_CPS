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
```
5. Iniciar Visual Studio Code abriendo el folder de la solución como proyecto. Luego modificar el archivo UnitTest1.cs
```
<script src="https://gist.github.com/p-cuadros/7a146fe1754e2b6fc04dd9b58f8c6350.js"></script>
```
7. Verificar la instancia de contenedor este en ejecución
```
docker ps
```
8. Esperar unos segundos e iniciar un navegador con la direccion: http://localhost:9000/
> user: admin  
> pass: admin  

9. Crear una nueva aplicación con el nombre aplicacionNetCore:

10. En un terminal de Powershell en modo Administrador, ejecutar e instalar sonar-scanner
```
dotnet tool install --global dotnet-sonarscanner
```
11. Ejecutar el siguiente comando el Powershell para crear una aplicación de consola.
```
dotnet new sln -o aplicacionNetCore
cd aplicacionNetCore
dotnet new console
dotnet sln aplicacionNetCore.sln add aplicacionNetCore.csproj
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
