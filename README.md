# Tablero de asistencia por grupo

Tablero web para analizar el reporte de **Gestión de Asistencia** de GeoVictoria y responder, por grupo, cuántos usuarios están planificados y cuántos están marcando.

Todo el procesamiento ocurre en el navegador de quien lo usa. El archivo Excel nunca se sube a un servidor ni queda guardado en este repositorio.

**Publicado en:** https://fdurancontreras4-ui.github.io/tablero-asistencia/

---

## Cómo se usa

1. Abre la URL del tablero.
2. Arrastra el archivo `.xlsx` exportado desde GeoVictoria (Gestión de Asistencia, una fila por usuario y día).
3. El tablero detecta las columnas solo y muestra los resultados.
4. Descarga el Excel o el PDF con el botón del encabezado.

No hay instalación, login ni configuración.

---

## Qué muestra

| Sección | Contenido |
|---|---|
| Marcaje y planificación | % de usuarios que marcaron y % de usuarios con turno asignado, más el desglose de sin planificar, planificados sin marcas, marcan sin planificar y cumplimiento de días |
| Marcaje diario | Todos los días del archivo con dos líneas: usuarios planificados y usuarios que marcaron. El hover de cada día muestra los tres números |
| Espacios de mejora | Hallazgos priorizados por cantidad de usuarios o días afectados, con dónde ocurre y qué acción tomar |
| Horas registradas | Horas trabajadas, tiempo extra, días con atraso y días con permiso |
| Detalle por grupo | Tabla ordenable con todas las métricas. Al hacer clic en un grupo se abre la lista de sus usuarios uno por uno |

Si además subes el reporte de enrolamiento (ver abajo), se agrega el **% de usuarios enrolados** como métrica general y como columna en el detalle por grupo, en el Excel y en el PDF.

---

## Enrolamiento (opcional)

Debajo de los filtros hay una sección para subir, de forma opcional, el reporte de **Enrolamiento** de GeoVictoria (una fila por usuario con sus métodos de enrolamiento). Si lo subes:

- Se calcula el % de usuarios con **al menos un método de enrolamiento activo** (huella, rostro, voz, tarjeta, clave, etc.), cruzando por `Identificador` con el libro de asistencia.
- Ese % aparece como métrica general en el tablero y en el resumen del PDF, y como columna adicional en el detalle por grupo (pantalla, Excel y PDF).

**Columnas del archivo de enrolamiento:** `Identificador` es obligatoria; el resto de columnas (Voz, Huella USB, Huella Box, Rostro Box, Biorostro Box, Tarjeta, etc.) se detectan automáticamente y cada una cuenta como método de enrolamiento si su valor no es `No Enrolado` / `No` / vacío.

Si no subes este archivo, el tablero, el Excel y el PDF se generan igual, simplemente sin esa métrica.

---

## Definiciones

Estas son las reglas con las que se calcula todo. Aparecen también al pie del tablero y en la hoja `Parámetros` del Excel.

- **Planificado**: el día tiene un turno asignado. `No Planificado` nunca cuenta. `Descanso` cuenta solo si activas la opción en *Personalizar*.
- **Marcando**: el usuario registró al menos una marca (Entró o Salió) en el rango analizado.
- **% de marcaje**: usuarios con al menos una marca ÷ usuarios del rango.
- **% de planificación**: usuarios con al menos un día con turno asignado ÷ usuarios del rango.
- **Cumplimiento de días**: días planificados con al menos una marca ÷ días planificados. Por defecto se descuentan los días con permiso (vacaciones, incapacidad, viaje); se puede desactivar en *Personalizar*.

### Ajuste automático del rango

Si el archivo incluye días posteriores a la última marca registrada (por ejemplo, un reporte del mes completo descargado a mitad de mes), el rango se ajusta solo a la última fecha con marcas. Esos días futuros solo traen turnos planificados y hundirían el cumplimiento sin motivo. El tablero avisa cuando lo hace y el rango se puede ampliar manualmente.

---

## Formato de archivo esperado

Exportación de Gestión de Asistencia con una fila por usuario y día. Las columnas se detectan por nombre, sin importar su posición:

**Obligatorias:** `Grupo`, `Identificador`, `Fecha`, `Turno`, y al menos una columna `Entró` o `Salió`.

**Opcionales, si están se usan:** `Nombre`, `Apellidos`, `Cargo`, `Permiso`, `Atraso`, `HT` (horas trabajadas), `HEA` y `HEC` (tiempo extra).

La fila de encabezados se busca en las primeras 20 filas de cada hoja, así que no importa si el archivo trae filas en blanco o un título arriba.

---

## Archivos que genera

**Excel** (respeta los filtros activos):

- `Resumen por grupo` — todas las métricas por grupo (incluye usuarios enrolados y % de enrolados si cargaste ese archivo)
- `Espacios de mejora` — hallazgos con prioridad, dónde y acción sugerida
- `Detalle usuarios` — un renglón por usuario con su estado
- `Por día` — planificados, marcaron y cumplimiento diario
- `Parámetros` — archivo origen, rango, criterios usados y definiciones

**PDF** horizontal con el logo GeoVictoria: resumen con indicadores grandes (seis, más el % de enrolados si lo cargaste) y la tabla por grupo con semáforo de color (rojo bajo 70%, ámbar 70–89%, verde 90% o más). La tabla del PDF solo incluye los grupos con planificación o marcaje bajo 100%; el Excel y el tablero en pantalla siempre muestran todos los grupos. El PDF no incluye "Espacios de mejora" (esa sección sigue disponible en pantalla y en el Excel).

---

## Detalles técnicos

Un solo archivo `index.html` autocontenido: HTML, CSS y JavaScript en el mismo documento, con el logo embebido en base64. No hay build, ni dependencias que instalar, ni backend.

Carga tres recursos externos por HTTPS:

| Recurso | Para qué |
|---|---|
| [SheetJS](https://sheetjs.com) 0.18.5 (cdnjs) | Leer y escribir archivos `.xlsx` |
| [jsPDF](https://github.com/parallax/jsPDF) 2.5.1 (cdnjs) | Generar el reporte PDF |
| Nunito (Google Fonts) | Tipografía |

Si la red bloquea cdnjs, el tablero avisa y quedan como respaldo el Excel o `Ctrl+P`. Para eliminar la dependencia se pueden descargar los dos `.js` al repositorio y apuntar las etiquetas `<script>` a rutas locales.

---

## Publicar y actualizar

Publicado con GitHub Pages desde la rama `main`, carpeta raíz (*Settings → Pages*).

Para actualizar, reemplaza `index.html` y haz commit. Los cambios tardan uno o dos minutos en verse; si la página sigue igual, recarga con `Ctrl+Shift+R` para saltarte la caché del navegador.

---

## Privacidad

El repositorio es público para que GitHub Pages funcione, pero no contiene datos de personas: los nombres, identificadores y marcas se procesan en memoria en el navegador de quien carga el archivo y desaparecen al cerrar la pestaña.

**No subas archivos `.xlsx` a este repositorio.** El `.gitignore` los excluye, pero conviene revisarlo antes de cada commit.
