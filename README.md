## Uso de paquetes Maven desde GitHub Packages

Este documento explica, paso a paso, cómo consumir paquetes Maven publicados en GitHub Packages desde tu proyecto Maven.

Requisitos previos
- Tener una cuenta de GitHub con acceso al repositorio que contiene el paquete.
- Maven instalado en tu máquina.

1) Generar un Personal Access Token (PAT)
- Ve a: `Settings` → `Developer settings` → `Personal access tokens` → `Tokens (classic)`.
- Genera un token con el scope mínimo necesario: `read:packages`.
- Copia el token (se mostrará sólo una vez).

2) Añadir credenciales a `settings.xml` de Maven

Localizaciones típicas de `settings.xml`:
- Windows: `C:\Users\<TU_USUARIO>\.m2\settings.xml`
- Linux / macOS: `/home/<TU_USUARIO>/.m2/settings.xml`

Ejemplo de entrada dentro de `<servers>` (reemplaza placeholders):

```xml
<servers>
    <server>
        <id>github</id> <!-- Este id debe coincidir con el <id> del repository en el pom.xml -->
        <username>GatoArtStudio</username> <!-- tu usuario de GitHub o el owner del repo -->
        <password>YOUR_PERSONAL_ACCESS_TOKEN</password> <!-- pega aquí tu PAT (no lo subas a VCS) -->
    </server>
</servers>
```

Notas de seguridad
- No pongas el token directamente en `pom.xml` ni lo subas al repositorio.
- Usa variables de entorno, cifrado o herramientas de CI para manejar credenciales en pipelines.

Importante — `settings.xml` es un archivo local (no de proyecto)

`settings.xml` se encuentra en el directorio del usuario dentro de la carpeta `.m2` (ej.: `C:\Users\<TU_USUARIO>\.m2\settings.xml` en Windows o `~/.m2/settings.xml` en Linux/macOS). Este archivo **no** forma parte del código fuente ni de la configuración del repositorio. No debes añadirlo ni commitearlo en tu repositorio.

Por qué esto causa confusión
- Muchas guías muestran cómo configurar `settings.xml`, y algunos usuarios piensan que debe ir en el repositorio del proyecto. Eso es incorrecto: `settings.xml` es específico de la máquina/usuario y contiene credenciales sensibles.

Alternativas correctas para compartir/configurar en equipos o CI
- Para CI (GitHub Actions, GitLab CI, etc.) guarda el token como secreto en la plataforma y genera un `settings.xml` temporal durante la ejecución del pipeline (no lo comites).
- Si necesitas compartir configuración no sensible, crea un ejemplo `settings.example.xml` sin credenciales o documenta los pasos en el README.

Ejemplo rápido (GitHub Actions): usa un secreto llamado `MAVEN_TOKEN` y crea `settings.xml` en el pipeline cargando el token desde el secreto. Nunca almacenes el token en el repositorio.

3) Declarar el repositorio en `pom.xml`

Añade el repositorio de GitHub Packages en la sección `<repositories>` de tu `pom.xml`. Asegúrate de que el `<id>` aquí coincide con el `<id>` definido en `settings.xml` (ej. `github`).

Ejemplo:

```xml
<repositories>
    <repository>
        <id>github</id>
        <url>https://maven.pkg.github.com/GatoArtStudio/DayNightSync</url>
    </repository>

    <!-- Otro ejemplo -->
    <repository>
        <id>github-foliaadapter</id>
        <url>https://maven.pkg.github.com/MinemuNetwork/foliaadapter</url>
    </repository>
</repositories>
```

4) Añadir la dependencia en `<dependencies>`

Ejemplo:

```xml
<dependencies>
    <dependency>
        <groupId>art.gatoartstudio</groupId>
        <artifactId>day-night-sync</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>

    <dependency>
        <groupId>com.molean</groupId>
        <artifactId>foliaadapter</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>
```

5) Comprobar que funciona

- Desde la raíz de tu proyecto, ejecuta (si usas un `settings.xml` personalizado, pásalo con `-s`):

```powershell
mvn -s %USERPROFILE%\.m2\settings.xml dependency:resolve
```

Este comando intentará resolver las dependencias. Si hay un error de autenticación, revisa el `id` en `pom.xml` vs `settings.xml` y que el token tenga `read:packages`.

Solución de problemas (FAQ)
- Error 401 / 403 al resolver dependencias:
    - Verifica que el PAT es válido y tiene `read:packages`.
    - Asegúrate de que el `<id>` del `<repository>` en el `pom.xml` coincide exactamente con el `<id>` del `<server>` en `settings.xml`.
- El paquete no se encuentra:
    - Confirma la URL del repositorio GitHub Packages: `https://maven.pkg.github.com/<OWNER>/<REPOSITORY>`.
    - Comprueba que el paquete/version realmente existen en el repo de GitHub.
- Evitar exponer el token:
    - Nunca comites `settings.xml` con tokens en repositorios públicos.
    - Para CI, usa los secretos del proveedor (GitHub Actions, GitLab CI, etc.) y configura `settings.xml` durante el pipeline.

Notas adicionales
- GitHub Packages puede requerir que el repositorio sea público o que la cuenta/token tenga acceso al repositorio privado donde esté publicado el paquete.
- Si publicas artefactos a GitHub Packages desde tu proyecto, revisa la documentación oficial de GitHub para `maven` y la configuración de `distributionManagement`.

Si quieres, puedo:
- añadir un ejemplo de `settings.xml` que cargue el token desde una variable de entorno en CI,
- o generar un pequeño script PowerShell para comprobar la resolución de dependencias en Windows.

---
