# ULMA FlowBoard — Add-in de PowerPoint

Editor de diagramas de Gantt operativo para ULMA Handling Systems.  
Se instala como complemento (add-in) en el panel lateral de PowerPoint y permite insertar el diagrama como imagen PNG de alta resolución en la diapositiva activa.

---

## Estructura del proyecto

```
ulma-flowboard/
├── index.html           ← Aplicación principal (task pane del add-in)
├── manifest.xml         ← Manifiesto del complemento Office
├── README.md
└── assets/
    ├── icon-16.svg      ← Iconos del add-in (ULMA mark en rojo/negro)
    ├── icon-32.svg
    ├── icon-64.svg
    ├── icon-80.svg
    └── html2canvas.min.js  ← DESCARGAR MANUALMENTE (ver paso 0)
```

---

## Paso 0 — Descargar html2canvas (obligatorio)

La exportación PNG depende de html2canvas. El fichero no se incluye en el repositorio por tamaño.

**Descárgalo desde:**  
`https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js`

Guárdalo en `assets/html2canvas.min.js`.

> Si no lo descargas, la app intentará cargarlo desde CDN como respaldo.  
> En entornos corporativos sin acceso a internet externo, la descarga local es imprescindible.

---

## Paso 1 — Publicar en GitHub Pages

1. Crea un repositorio público en GitHub llamado `ulma-flowboard`.

2. Sube todos los ficheros del proyecto:
   ```bash
   git init
   git add .
   git commit -m "ULMA FlowBoard v1.0"
   git remote add origin https://github.com/TU-USUARIO/ulma-flowboard.git
   git push -u origin main
   ```

3. En GitHub → Settings → Pages → Source: **Deploy from branch → main → / (root)**.

4. Tu add-in quedará en `https://TU-USUARIO.github.io/ulma-flowboard/`.

5. **Sustituye `TU-USUARIO`** por tu usuario real en `manifest.xml` (hay 7 ocurrencias).  
   Puedes hacerlo con buscar y reemplazar en cualquier editor.

> **Nota sobre HTTPS:** Office Add-ins requieren HTTPS. GitHub Pages lo proporciona gratuitamente.  
> Para servidores internos (IIS, Apache, nginx), asegúrate de que el certificado SSL es válido.

---

## Paso 2 — Generar un GUID único para el manifiesto

El `<Id>` del manifiesto debe ser único por add-in. El placeholder del fichero es válido para pruebas, pero para producción genera uno nuevo:

- Web: [https://guidgenerator.com](https://guidgenerator.com)
- PowerShell: `[System.Guid]::NewGuid().ToString()`
- Bash: `uuidgen`

Reemplaza el valor en `manifest.xml`:
```xml
<Id>TU-GUID-AQUI</Id>
```

---

## Paso 3 — Instalar el add-in en PowerPoint

### PowerPoint Windows (sideloading mediante carpeta de red)

1. Crea una carpeta compartida en red (o usa una local).  
   Ejemplo: `\\servidor\AddIns\ULMA` o `C:\AddIns\ULMA`.

2. Copia `manifest.xml` en esa carpeta.

3. En PowerPoint: **Archivo → Opciones → Centro de confianza → Configuración del Centro de confianza → Catálogos de complementos de confianza**.

4. Añade la ruta de la carpeta, marca **Mostrar en menú** y haz clic en **Aceptar**.

5. Cierra y vuelve a abrir PowerPoint.

6. **Insertar → Obtener complementos → MI ORGANIZACIÓN** → selecciona **ULMA FlowBoard**.

> El add-in cargará la URL de GitHub Pages definida en el manifiesto.  
> El ordenador necesita acceso a internet (o al servidor interno donde está alojado el HTML).

### PowerPoint Windows (sideloading directo para desarrollo)

1. Copia `manifest.xml` a la carpeta de sideloading de Office:
   ```
   %USERPROFILE%\AppData\Local\Microsoft\Office\16.0\Wef\
   ```
   (Crea la carpeta `Wef` si no existe.)

2. Abre PowerPoint → **Insertar → Mis complementos → CARPETA COMPARTIDA** → selecciona ULMA FlowBoard.

### PowerPoint Mac

1. Copia `manifest.xml` a:
   ```
   ~/Library/Containers/com.microsoft.Powerpoint/Data/Documents/wef/
   ```
   (Crea los directorios si no existen.)

2. Abre PowerPoint → **Insertar → Mis complementos** → el add-in debería aparecer en la pestaña **Carpeta compartida**.

> En Mac con versiones recientes de Office, es posible que necesites reiniciar PowerPoint tras copiar el manifiesto.

### PowerPoint Online / Microsoft 365 (administrador de organización)

1. En el **Centro de administración de Microsoft 365** ([admin.microsoft.com](https://admin.microsoft.com)):  
   **Configuración → Aplicaciones integradas → Cargar aplicaciones personalizadas**.

2. Sube el fichero `manifest.xml`.

3. Asigna el add-in a los usuarios o grupos deseados.

4. Los usuarios verán el add-in en **Insertar → Mis complementos → Administrado por administrador**.

### PowerPoint Online (sideloading personal, sin admin)

1. Abre una presentación en PowerPoint Online.
2. **Insertar → Complementos de Office → MI ORGANIZACIÓN → Cargar mi complemento**.
3. Selecciona el fichero `manifest.xml` de tu equipo local.

> El sideloading personal en Office Online puede no estar disponible en tenants con restricciones de seguridad.

---

## Uso del add-in

1. Abre PowerPoint y, desde el ribbon (pestaña **Inicio**, grupo **ULMA**), haz clic en **Gantt Operativo**.

2. El panel lateral muestra el editor FlowBoard. Diseña tu diagrama:
   - Arrastra elementos desde el panel izquierdo al canvas.
   - Haz doble clic en una celda para crear un bloque rápido.
   - Selecciona un bloque para editar sus propiedades en el panel derecho.
   - Usa los botones JSON para guardar/cargar diagramas.

3. Cuando el diagrama esté listo, pulsa **Insertar en slide** (botón rojo en la barra superior).  
   El diagrama se inserta como imagen PNG de alta resolución en la diapositiva activa, centrado horizontalmente.

> **En modo ventana completa:** el panel izquierdo siempre está visible.  
> **En modo task pane (350px):** usa el botón ☰ (hamburguesa) de la barra superior para desplegar el panel de elementos.

---

## Actualizar el add-in

El add-in carga siempre desde la URL de GitHub Pages definida en el manifiesto.  
Para actualizar la herramienta:

1. Modifica `index.html` (o cualquier otro fichero).
2. Haz commit y push a GitHub:
   ```bash
   git add .
   git commit -m "Actualización: descripción del cambio"
   git push
   ```
3. GitHub Pages despliega en ~1 minuto. La próxima vez que el usuario abra el add-in, cargará la versión actualizada automáticamente.

**No es necesario reinstalar ni tocar el manifest.xml** salvo que cambies la URL base, el GUID o los metadatos del complemento.

> Para forzar la recarga inmediata en PowerPoint Desktop, cierra y vuelve a abrir el panel del add-in (o usa F5 dentro del task pane).

---

## Iconos — conversión a PNG (opcional)

Los iconos se proporcionan en formato SVG, que funciona correctamente en Office Online y versiones modernas de Office Desktop.

Si necesitas PNG para compatibilidad con versiones antiguas de Office, convierte los SVG:

**Opción 1 — Inkscape (línea de comandos):**
```bash
for size in 16 32 64 80; do
  inkscape assets/icon-${size}.svg --export-type=png --export-filename=assets/icon-${size}.png
done
```

**Opción 2 — ImageMagick:**
```bash
for size in 16 32 64 80; do
  convert -background none assets/icon-${size}.svg assets/icon-${size}.png
done
```

**Opción 3 — Online:** [svgtopng.com](https://svgtopng.com) o similar.

Después actualiza las URLs en `manifest.xml` cambiando `.svg` por `.png`.

---

## Solución de problemas

| Síntoma | Solución |
|---------|----------|
| El botón "Insertar en slide" no aparece | Office.js no se cargó. Verifica que el add-in está instalado desde PowerPoint, no abriendo `index.html` directamente en el navegador. |
| Error "No se pudo cargar html2canvas" | Descarga el fichero y colócalo en `assets/html2canvas.min.js` (ver Paso 0). |
| El add-in no carga (pantalla en blanco) | Revisa que la URL en `manifest.xml` apunta correctamente a GitHub Pages con HTTPS. Abre la URL en el navegador para confirmar. |
| "No se puede cargar el complemento" en PowerPoint Desktop | Comprueba que el manifiesto es válido XML y que el GUID es único. Valida en [Office Add-in Validator](https://github.com/OfficeDev/office-addin-manifest). |
| CORS al insertar imagen | Asegúrate de que `html2canvas` se carga correctamente y que el dominio del add-in está en la lista `<AppDomains>` del manifiesto. |
| El diagrama se ve recortado al insertar | El canvas se genera a resolución completa. Si el Gantt tiene muchas columnas, reduce el zoom antes de insertar o usa escala de celda menor. |

---

## Requisitos

- Microsoft 365 / Office 2019+ (Desktop o Online)
- PowerPoint con soporte para complementos de Office.js
- Conexión a internet (para cargar el add-in desde GitHub Pages)
- Navegador moderno si se usa PowerPoint Online (Chrome, Edge, Firefox)

---

*ULMA Handling Systems — Grupo ULMA — Oñati, Gipuzkoa*
