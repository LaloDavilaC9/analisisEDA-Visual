# Flujo de análisis de marketing de afiliados (Orange Data Mining)

Prototipo en **Orange Data Mining** que junta dos fuentes habituales del marketing de afiliados: el **reporte de ventas** del programa de afiliados de una tienda en línea y las **analíticas de publicaciones** de la red social X. Con ellas limpia los datos, asocia cada publicación con el producto que menciona y responde cinco preguntas de análisis, que se listan más abajo.

El análisis es **descriptivo y exploratorio**: no hay modelos predictivos. K-Means y el clustering jerárquico agrupan productos parecidos, no predicen nada.

> Todos los datos incluidos son **sintéticos** (inventados). Los resultados sirven para comprobar que el flujo funciona, no son hallazgos reales.

![Los tres módulos y las tablas que se pasan entre ellos](docs/img/fig_modulos.png)

## Preguntas de análisis

1. ¿La cantidad de publicaciones dedicadas a un producto (saturación de contenido) corresponde a su desempeño real en ventas o existen productos que son publicados de manera desproporcionada a su rendimiento?
2. ¿Qué productos o categorías presentan la mejor relación entre interacción en redes sociales y conversión a venta? ¿Cuáles concentran gran cantidad de interacciones sin precisamente traducirse a un resultado económico?
3. ¿Existen patrones relacionados al tiempo en que se realizan las publicaciones asociadas a un mejor desempeño en ventas?
4. ¿Es posible clasificar los productos en niveles de prioridad con base en su rendimiento en publicaciones, ventas y conversión, saturación de contenido y temporalidad (rendimiento integral) en que se publica?
5. ¿Es posible agrupar los productos en conjuntos con un comportamiento similar en saturación de contenido, interacción en la red social y ventas?

El módulo 2 responde las preguntas 1 y 2; el módulo 3, las preguntas 3 a 5 (la 4 con K-Means y la 5 con clustering jerárquico).

## Qué hay aquí

```
flujo-afiliados-orange/
├── README.md
├── LICENSE                           licencia MIT
├── .gitignore                        deja fuera los datos reales y las tablas intermedias
├── 01_Limpieza.ows                   módulo 1: limpieza de ventas y de X, y asociación publicación-producto
├── 02_Preguntas_1_y_2.ows            módulo 2: saturación (P1) e interacción frente a comisión (P2)
├── 03_Preguntas_3_a_5.ows            módulo 3: momento de publicación (P3), K-Means (P4) y dendrograma (P5)
├── datos/
│   ├── ventas/                       reporte de ventas sintético (1 archivo); aquí también van las ventas reales
│   └── x/
│       └── sinteticos/               publicaciones sintéticas de X (5 archivos); las reales van en datos/x/
├── intermedios/                      (se crea sola al correr; no se sube a Git) tablas que un módulo deja para el siguiente
└── docs/
    ├── documentacion_tecnica.docx    documentación técnica completa
    └── img/                          figuras de los módulos
```

## Cómo probarlo

1. Instala [Orange Data Mining](https://orangedatamining.com/download/) (probado con la 3.38.1, que incluye Python 3.11).
2. Descarga o clona este repositorio. **No muevas archivos entre carpetas**: las rutas del flujo son relativas a la carpeta de los `.ows`.
3. Abre `01_Limpieza.ows` en Orange. En el widget **PUB-01 Unir archivos de X** (Python Script) pulsa **Run**.
4. Abre `02_Preguntas_1_y_2.ows` y después `03_Preguntas_3_a_5.ows`. Se calculan al abrir.

El orden siempre es 01 → 02 → 03. Los módulos se pasan tablas mediante los archivos de `intermedios/`, que no se incluyen en el repositorio: los crean al correr el módulo 1 (SAL-01, SAL-02 y SAL-03) y el módulo 2 (SAL-01), y la carpeta se crea sola. Si se abre el módulo 2 o el 3 antes de correr el anterior, sus widgets ENT avisarán que falta el archivo.

El funcionamiento de cada widget y las decisiones de diseño están en [`docs/documentacion_tecnica.docx`](docs/documentacion_tecnica.docx).

## Cómo se leen los nombres de los widgets

| Código | Significa |
|---|---|
| `VEN` | ventas del programa de afiliados |
| `PUB` | publicaciones de X |
| `INT` | integración de las dos fuentes |
| `Q1` … `Q5` | cada pregunta de análisis |
| `SAL` | guarda una tabla en `intermedios/` para el siguiente módulo |
| `ENT` | lee una tabla que dejó el módulo anterior |

El número indica el orden de cálculo dentro de su grupo.

## Usar tus propios datos

Los datos reales van en las mismas carpetas que los sintéticos, y el `.gitignore` los deja fuera de Git (ignora todo lo que está en `datos/` salvo los archivos que llevan `SINTETICO` en el nombre, y también `intermedios/*.pkl`).

| Datos | Dónde | Qué hacer en Orange |
|---|---|---|
| Ventas reales | `datos/ventas/` | En **VEN-01** (File), elegir el archivo. Debe traer las columnas del reporte de ventas del programa de afiliados (por ejemplo `ASIN`, `Product Title`, `Total Earnings`). |
| X reales | `datos/x/` (archivos sueltos, sin subcarpeta) | En el script de **PUB-01**, `RUTA_CARPETA = r"datos/x"`. |
| X sintéticos | `datos/x/sinteticos/` | En el script de **PUB-01**, `RUTA_CARPETA = r"datos/x/sinteticos"`. |

PUB-01 lee solo los archivos que están directamente en la carpeta indicada y no entra en subcarpetas; por eso los reales y los sintéticos no se mezclan. Después de cambiar la línea, pulsar **Run**.

Antes de hacer un commit conviene dejar PUB-01 en `datos/x/sinteticos` y VEN-01 en el archivo sintético, que es lo que tendrá quien clone el repositorio.

- **VEN-03** (resolver ASIN con una API propia) viene sin servidor configurado. Solo hace falta si tu reporte trae ASIN sin título.

## Licencia

MIT. Orange es software de terceros con su propia licencia (GPL) y no se redistribuye aquí.
