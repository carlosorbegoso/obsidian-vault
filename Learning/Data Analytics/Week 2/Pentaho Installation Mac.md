# Instalación de Pentaho Data Integration (Spoon) en Mac Apple Silicon

## Requisitos previos

- macOS con chip Apple Silicon (M1/M2/M3)
- Homebrew instalado
- Conexión a internet

## Paso 1: Instalar Homebrew (si no lo tienes)

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## Paso 2: Instalar herramienta de descarga

```bash
brew install gdown
```

## Paso 3: Crear carpeta y descargar Pentaho

```bash
mkdir -p ~/Applications/pentaho
cd ~/Applications/pentaho

# Descargar desde Google Drive (archivo del curso)
gdown "1kCdZNYQZP-qyrQj3_Be7kX2ut7RtJXUx" -O pentaho.zip

# Descomprimir
unzip pentaho.zip

# Dar permisos de ejecución
chmod +x data-integration/*.sh
```

## Paso 4: Instalar Java 11 x86_64 (requerido para Apple Silicon)

Pentaho necesita Java x86_64, no la versión ARM nativa.

```bash
cd ~/Applications

# Descargar Zulu JDK 11 x86_64
curl -L -o zulu11.tar.gz "https://cdn.azul.com/zulu/bin/zulu11.66.15-ca-jdk11.0.20-macosx_x64.tar.gz"

# Extraer
tar -xzf zulu11.tar.gz
mv zulu11.66.15-ca-jdk11.0.20-macosx_x64 zulu11-x64
rm zulu11.tar.gz
```

## Paso 5: Crear script de ejecución

Crear archivo `run-spoon.sh`:

```bash
#!/bin/bash
# Script para ejecutar Pentaho Spoon con Java 11 en Mac (Apple Silicon)

# Eliminar caché SWT (causa conflictos de arquitectura arm64 vs x86_64)
rm -rf ~/.swt 2>/dev/null

# Ejecutar todo bajo Rosetta x86_64
arch -x86_64 /bin/bash << 'EOF'
export JAVA_HOME="$HOME/Applications/zulu11-x64/zulu-11.jdk/Contents/Home"
export PENTAHO_JAVA_HOME="$JAVA_HOME"
export PATH="$JAVA_HOME/bin:$PATH"

cd ~/Applications/pentaho/data-integration

java --add-opens java.base/java.net=ALL-UNNAMED \
     --add-opens java.base/java.lang=ALL-UNNAMED \
     --add-opens java.base/sun.net.www.protocol.jar=ALL-UNNAMED \
     -XstartOnFirstThread \
     -Xms1024m -Xmx2048m \
     -Djava.library.path=../libswt/osx64/ \
     -Djava.locale.providers=COMPAT,SPI \
     -jar launcher/launcher.jar -lib ../libswt/osx64/
EOF
```

Dar permisos:

```bash
chmod +x run-spoon.sh
```

## Paso 6: Ejecutar Spoon

```bash
./run-spoon.sh
```

## Solución de problemas

### Problema: Texto invisible (letras en blanco)

Pentaho tiene un bug con el modo oscuro de macOS. Solución:

**Opción 1:** Cambiar a modo claro antes de usar Spoon:
- Ir a **Preferencias del Sistema → Apariencia → Claro**

**Opción 2:** Ejecutar este comando:
```bash
osascript -e 'tell application "System Events" to tell appearance preferences to set dark mode to false'
```

Para volver al modo oscuro después:
```bash
osascript -e 'tell application "System Events" to tell appearance preferences to set dark mode to true'
```

### Problema: Error "platform [arm64] is not supported"

Asegúrate de:
1. Eliminar la caché SWT: `rm -rf ~/.swt`
2. Usar el script que ejecuta bajo Rosetta (`arch -x86_64`)

### Problema: Error de SWT library

Si ves errores relacionados con `libswt-cocoa`:
1. Eliminar caché: `rm -rf ~/.swt`
2. Reiniciar Spoon

## Estructura de archivos final

```
~/Applications/
├── pentaho/
│   ├── pentaho.zip (puedes eliminarlo)
│   └── data-integration/
│       ├── spoon.sh
│       ├── kitchen.sh
│       ├── pan.sh
│       └── ...
└── zulu11-x64/
    └── zulu-11.jdk/
        └── Contents/Home/
```

## Comandos útiles

| Comando | Descripción |
|---------|-------------|
| `./run-spoon.sh` | Iniciar Spoon (interfaz gráfica) |
| `kitchen.sh` | Ejecutar Jobs desde línea de comandos |
| `pan.sh` | Ejecutar Transformaciones desde línea de comandos |
