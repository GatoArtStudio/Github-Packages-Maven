# Uso de paquetes Maven desde GitHub Packages

Crea un Personal Access Token (PAT) en GitHub:
- Ve a: `Settings` → `Developer settings` → `Personal access tokens` → `Tokens (classic)`
- Genera un token con el scope `read:packages`

Configura las credenciales en `settings.xml` de Maven:
`settings.xml`:
- `C:\Users\<TU_USUARIO>\.m2\settings.xml` (en Windows)
- `/home/<TU_USUARIO>/.m2/settings.xml` (en Linux/Mac)

`settings.xml`:
```xml
<server>
  <id>github</id> <!-- Debe ser idéntico -->
  <username>GatoArtStudio</username>
  <password>TOKEN</password> <!-- Token (classic) -->
</server>

```

Agregar la ruta completa del repositorio
`pom.xml`:
```xml
<repositories>
    <!--   Repository github package     -->
    <repository>
        <id>github</id> <!-- Este ID -->
        <url>https://maven.pkg.github.com/GatoArtStudio/DayNightSync</url>
    </repository>

    <repository>
        <id>github-foliaadapter</id>
        <url>https://maven.pkg.github.com/MinemuNetwork/foliaadapter</url>
    </repository>
    
<!--  Others...  -->
</repositories>


<dependencies>
    <!--  Custom package  -->
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

    <!--  Others...  -->
</dependencies>
```