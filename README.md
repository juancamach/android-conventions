# android-conventions

Convenciones centralizadas para los proyectos Android del equipo SXA: formateo (Spotless + ktlint), `.editorconfig`, y a futuro lint, detekt, etc.

Un solo lugar para mantener las reglas. Los repos consumidores las traen vía `apply from:` y un comando de sincronización.

---

## Para repos consumidores

### Setup inicial

En tu `build.gradle` raíz:

```groovy
buildscript {
    ext {
        conventionsVersion = 'v1.0'   // ← versión de las convenciones
    }
    dependencies {
        // ... tus dependencias existentes
        classpath 'com.diffplug.spotless:spotless-plugin-gradle:6.25.0'
    }
}

apply from: "https://raw.githubusercontent.com/juancamach/android-conventions/${conventionsVersion}/spotless.gradle"
apply from: "https://raw.githubusercontent.com/juancamach/android-conventions/${conventionsVersion}/sync-conventions.gradle"
```

Luego corre una vez:

```bash
./gradlew syncConventions
git add .editorconfig
git commit -m "chore: sync android-conventions ${conventionsVersion}"
```

### Actualizar a una nueva versión

1. Cambia `conventionsVersion = 'v1.1'` en tu `build.gradle`.
2. Corre `./gradlew syncConventions`.
3. Commitea los archivos actualizados.

Eso es todo. La config de Spotless se actualiza automáticamente; los archivos auxiliares (`.editorconfig`, etc.) se sincronizan con el comando.

---

## Para mantenedores de este repo

### Lanzar una nueva versión

1. Edita los archivos (`spotless.gradle`, `.editorconfig`, etc.).
2. Commitea en `main`.
3. Crea y pushea un tag:
   ```bash
   git tag v1.1
   git push origin v1.1
   ```
4. Anuncia al equipo qué cambió para que actualicen sus repos.

### Política de versionado

- **Versiones mayor** (`v2.0`): cambios incompatibles que requieren acción manual en consumidores.
- **Versiones menor** (`v1.1`, `v1.2`): nuevas reglas, ajustes que solo requieren `./gradlew syncConventions`.

**Nunca muevas un tag ya publicado.** Los consumidores cachean por URL — un tag movido genera builds inconsistentes según la caché de Gradle de cada dev.

### Agregar un nuevo archivo de convención (ej: `lint.xml`)

1. Crea el archivo en este repo.
2. Agrégalo al map `conventionsFiles` en `sync-conventions.gradle`.
3. Si requiere configuración Gradle adicional (ej: para que Android Lint use ese archivo), créala en un script aparte (`lint.gradle`) que los consumidores apliquen con `apply from:`.
4. Tag nueva versión.

---

## Reglas actuales

### Spotless + ktlint
- Sin wildcard imports
- Trailing commas habilitados (declaración y llamada)
- `max-line-length`, `function-naming`, `filename`, `package-name` deshabilitados (justificación: Compose + convención `sxa_*`)
- Orden de imports estilo IntelliJ: `*, java.**, javax.**, kotlin.**, ^`
