# Totales por producto

Aplicación web de un solo archivo que lee un listado de pedidos en Excel (`.xlsx`) y calcula la **cantidad total de cada producto**, separada por tipo (litros, cajas, bidones) y filtrable por **uno o varios clientes**.

No necesita instalación, servidor ni conexión a internet: se abre `totales-productos.html` con doble clic en cualquier navegador moderno.

## Qué hace

- Suma las cantidades de cada producto (código + descripción) en todo el listado.
- Separa los totales por **tipo de producto**, según el prefijo del código. Nunca suma unidades distintas entre sí (litros con cajas, por ejemplo).
- Permite **filtrar por cliente**, eligiendo uno o varios a la vez.
- Muestra un resumen por tipo, una tabla ordenable y un buscador de productos.
- Acepta **varios archivos a la vez** (se suman) y varias hojas por archivo.
- Avisa de las filas que no pudo sumar (por ejemplo, con cantidad pero sin código) en lugar de ignorarlas en silencio.
- Exporta el resultado a **Excel**, **CSV** o lo copia para pegarlo en una planilla.

## Cómo usarla

1. Abrí `totales-productos.html` en Chrome, Edge o Firefox.
2. Arrastrá el archivo Excel a la zona punteada, o hacé clic para elegirlo.
3. Revisá los totales por tipo y la tabla de productos.
4. Si querés, filtrá por cliente (ver más abajo).
5. Descargá el resultado con **Descargar Excel**, **Descargar CSV** o **Copiar tabla**.

Para procesar otro listado, cargá el nuevo archivo o tocá **Limpiar**.

## Formato esperado del Excel

La aplicación lee estas columnas (se pueden cambiar en **Columnas y tipos de producto**):

| Dato | Columna por defecto | Obligatoria |
|---|---|---|
| Código del producto | `E` | Sí |
| Descripción | `F` | Sí |
| Cantidad | `M` | Sí |
| Pendiente | `N` | No (si la dejás vacía, no se calcula) |
| Nombre del cliente | `C` | Solo para filtrar por cliente |

Cómo interpreta las filas:

- **Fila de producto:** tiene código, descripción y una cantidad numérica. Se aceptan números, `1,5` y `1.200` (separador de miles).
- **Fila de cliente:** su primer texto empieza con la palabra **"Cliente"** y trae el nombre en la columna del cliente. Todos los productos que siguen pertenecen a ese cliente hasta la próxima fila de cliente.
- **Se ignoran** los encabezados que se repiten en cada página, los subtotales y los números de página.
- **Se avisan** las filas con cantidad numérica pero sin código o sin descripción, y las filas con código y descripción cuya cantidad no es un número.

Si el diseño del listado cambia, ajustá las letras de columna y el resultado se recalcula solo.

## Tipos de producto

El tipo se determina por el **prefijo del código**. Los valores por defecto son:

| Prefijo | Unidad |
|---|---|
| `GR` | Litros |
| `MI` | Cajas |
| `XMI` | Bidones |

- Se editan en **Columnas y tipos de producto**, una línea por tipo con el formato `PREFIJO = Unidad`.
- Si un código coincide con más de un prefijo, gana el **más largo** (por eso `XMI…` no se confunde con `MI…`).
- Los códigos que no coinciden con ningún prefijo aparecen como **"Sin clasificar"**, con un aviso.
- **Restablecer tipos** vuelve a los valores por defecto.

## Filtro por cliente

- Al hacer clic en **Filtrar por cliente** se abre la lista de clientes con su cantidad de líneas.
- **Tildá** los que quieras: los totales, la tabla y las descargas se actualizan al instante.
- Cada cliente elegido aparece como una ficha con **×** para quitarlo.
- Escribí parte de un nombre para buscar dentro de la lista, y usá **Tildar los N que coinciden** (o la tecla Enter) para elegir todos los que lo contienen. La búsqueda ignora mayúsculas y acentos.
- Sin ningún cliente tildado se muestran **todos**. **Todos los clientes** borra la selección.
- Se maneja también con el teclado: flechas para moverse, Enter para tildar o destildar, Esc para cerrar.

## Descargas

| Opción | Contenido |
|---|---|
| **Excel (.xlsx)** | Una fila por producto con unidad, cantidad total, pendiente (si corresponde) y líneas, más un bloque de **totales por unidad** calculado con fórmulas. Si hay clientes filtrados, agrega una nota con sus nombres y lo indica en el nombre del archivo. |
| **CSV** | Mismas columnas, separadas por punto y coma, en UTF-8 y con coma decimal, listo para abrir en Excel en español. |
| **Copiar tabla** | Copia la tabla al portapapeles para pegarla directamente en una planilla. |

Todas las descargas respetan el filtro de cliente activo.

## Privacidad

Los archivos se leen **dentro del navegador**. No se suben a ningún servidor y la aplicación no hace pedidos de red. Lo único que se guarda, en el propio navegador (`localStorage`), son las letras de columna y la lista de tipos que configuraste.

## Requisitos

- Un navegador reciente (Chrome, Edge o Firefox). Necesita `DecompressionStream` para leer el Excel.
- Archivos `.xlsx` o `.xlsm`. Si tenés un `.xls` antiguo, abrilo en Excel y guardalo como `.xlsx`. Los archivos protegidos con contraseña no se pueden leer.

## Publicarla en la web (opcional)

Con **GitHub Pages** la aplicación queda disponible desde un link, sin copiar el archivo. En el repositorio: *Settings → Pages* y elegí la rama principal. La página queda en `https://<usuario>.github.io/<repositorio>/totales-productos.html`. Los Excel se siguen procesando en el navegador de quien los usa.

## Detalles técnicos

- Un único archivo HTML con CSS y JavaScript incluidos, **sin dependencias** ni paso de compilación.
- Lee `.xlsx` con un lector propio de ZIP y XML (`DecompressionStream` y `DOMParser`), y genera el Excel de salida con un escritor propio, también sin librerías.
- El código está en el bloque `<script>` de [`totales-productos.html`](totales-productos.html), dividido en secciones comentadas: lectura de Excel, extracción de líneas, tipos, filtros, pantalla, selector de clientes y exportación.

## Antes de subir cambios al repositorio

Los listados de pedidos y los Excel de resultados contienen **nombres de clientes y cantidades reales**. No los subas: dejá en el repositorio solo el HTML y este README, o agregá `*.xlsx` a un archivo `.gitignore`.
