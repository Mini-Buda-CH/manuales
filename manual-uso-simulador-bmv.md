# Manual de uso del Simulador BMV

Guía pantalla por pantalla de la fase 2 de *Del aula a la bolsa*. La fase 1 (preparar cada empresa) está en el [Manual de uso del Simulador de cálculos](manual-uso-simulador-calculos.md).

Documentos relacionados:
- [Instructivo para quien lleva la sesión](instructivo-operador.md)
- [Instructivo para participantes](instructivo-participantes.md)
- [Manual de valoración](manual-valoracion.md)

---

## Recorrido general

```
index.html          →  configurar.html       →  tablero.html        +  publico.html
Empresas               Partida                   Ronda 0 y rondas       Proyector
(cargar los JSON)      (parámetros y entorno)    (operar y cerrar)      (solo lectura)
```

**Cómo abrirlo:** `index.html` en el navegador, GitHub Pages o `python3 -m http.server` en la carpeta del repositorio [simulador-bmv](https://github.com/Mini-Buda-CH/simulador-bmv). No requiere instalación.

**Dónde se guarda:** todo queda en el navegador (`localStorage`). Al recargar se conserva:
- las empresas cargadas: `simuladorBMV.empresas`;
- la configuración: `simuladorBMV.partida`;
- la partida en curso: `simuladorBMV.juego`;
- los últimos 6 respaldos: `simuladorBMV.respaldos`.

Otra computadora u otro navegador no ven estos datos; para moverlos, usa **Exportar/Importar partida**.

**Accesibilidad:**
- Todas las pantallas se usan con teclado (Tab, Enter, flechas) y tienen un enlace "Saltar al contenido".
- Se adaptan a celular, tableta y escritorio.
- Respetan la preferencia de movimiento reducido del sistema.

---

## 1. Empresas (`index.html`)

### Cargar archivos
- **Suelta aquí los archivos JSON** o haz clic para elegirlos (se pueden cargar varios a la vez).
- Solo acepta archivos `simulador-bmv/empresa` versión 1, generados con **Exportar** en el Simulador de cálculos.

### Vista previa (uno por uno)
Para cada archivo se muestra "Archivo 1 de n" con:
- empresa, mercado, divisa, carrera y tipo;
- la emisión;
- los estados financieros;
- los datos para el BMV.

Cada archivo lleva un sello con su estado:

| Sello | Significado | Botón |
|---|---|---|
| Lista para agregar | Datos completos y válidos | **Agregar al simulador** |
| Ya está cargada | Mismo identificador (o mismo nombre, mercado y divisa) que una empresa de la lista | **Reemplazar**; conserva su lugar |
| Con errores | Se listan los errores (capital ≤ 0, precio de emisión > 100 %, faltan dividendos…) | Solo **Descartar** |
| Partida completa | Ya hay 40 empresas | Solo **Descartar** |

- Los **avisos** (por ejemplo, un archivo sin identificador) se muestran debajo, pero no impiden agregar el archivo.
- **Agregar todas las válidas (n)** agrega de un golpe las que no tienen errores.
- **Descartar** quita el archivo de la cola.

### Lista "En la partida"
- Muestra las empresas cargadas, en orden de carga. Ese orden desempata el ranking.
- Cada una tiene un botón para eliminarla (pide "¿Eliminar?"). **Vaciar lista** las quita todas, también con confirmación.
- **Configurar partida** se habilita con 2 a 40 empresas.

## 2. Configurar partida (`configurar.html`)

A la izquierda está el formulario **Configuración** en secciones plegables (al abrir solo se ve desplegada **Partida**); a la derecha, el **Entorno simulado** y las empresas.

### Partida
- **Periodo por ronda:** Mensual o Trimestral. Cada ronda avanza 1 o 3 meses.
- **Mes inicial** y **Año inicial:** listas independientes. Hay datos reales de enero 2025 a septiembre 2026; los meses sin datos aparecen deshabilitados y, si eliges un año donde tu mes no existe, se ajusta al último disponible.
- **Semilla:** número que fija todo el azar (entorno, precios, riesgos). **Nueva semilla** propone otra al azar.

### Las demás secciones
Cada campo valida su rango y muestra el error junto al campo.

| Sección | Campos |
|---|---|
| **Entorno económico** | Meta de inflación, banda, paso de la TIIE, tasa real neutral, piso de la TIIE, inflación de EUA |
| **Crédito bancario** | Sobretasas AAA–BBB (deben crecer de AAA a BBB), plazo mínimo y máximo, comisión de apertura, máximo sobre activos, meses para regularizar un impago |
| **Operación** | Comisión, diferencial del público, diferencial cambiario, impuesto a ganancias, retención de dividendos, operaciones por turno |
| **Límites** | Máximo por operación, tenencia máxima, banda de precio en cruces |
| **Riesgos** | Máximo por periodo y probabilidad mensual de cada riesgo (incumplimiento con mínimo y máximo) |
| **Puntaje y precio** | Pesos de valor, utilidad y salud (deben sumar 100 %), múltiplo de utilidad, castigo por omitir dividendo |

Los porcentajes se capturan como porcentaje (8.5 = 8.5 %).

### Entorno simulado
Se actualiza al cambiar cualquier parámetro:
- indicadores iniciales;
- tres gráficas (inflación anual, TIIE y tipo de cambio), con la historia real, lo simulado y la meta;
- la tabla de las **primeras 12 rondas**.

### Botones
- **Guardar:** valida y guarda. El estado en la cabecera dice "Guardada · fecha" o "Cambios sin guardar". Si hay errores, abre la sección y pone el foco en el primer campo con error.
- **Restablecer:** vuelve a los valores aprobados (pide confirmación).
- **Iniciar partida:** guarda, crea la partida y abre el tablero.
  - Si ya hay una partida en curso, dice **Continuar partida** y aparece **Empezar una partida nueva**, que borra la actual tras confirmar.
- **← Volver a empresas.**

## 3. Tablero (`tablero.html`)

### Barra superior
- **Datos de la ronda:** ronda, periodo en juego (por ejemplo, "Febrero 2025" o "Abr – Jun 2025"), inflación anual, TIIE y tipo de cambio.
- **Riesgos:** catálogo y efectos vigentes (ver abajo).
- **Pantalla pública:** abre `publico.html` en otra ventana.
- **Partida:** respaldos, exportar, importar, configuración y terminar.
- **Cerrar colocación / Cerrar periodo:** botón principal. Pide un segundo clic para confirmar cuando hace falta (el aviso dura 4 segundos).

### Columna "Orden de turnos"
- Lista de empresas en el orden de la ronda (serpentina), con su estado: Pendiente, En turno, Terminó, Pasó, Sin turno.
- **Buscar empresa:** filtra la lista; con **Enter** elige la primera.
- Un clic en una empresa:
  - en la ronda 0, la vuelve la inversionista;
  - en las rondas, le da el turno.

### Centro: ronda 0 · colocación
- **Empresa inversionista:** su caja en MXN y USD y lo **comprometido** en órdenes.
- **Tabla de emisiones:** precio, oferta, demanda acumulada (resaltada si hay sobredemanda) y **Orden**.
  - La cantidad se escribe en **Orden** y se guarda al salir del campo o con **Enter**, que pasa a la siguiente fila.
  - El máximo por empresa se muestra debajo y los errores, en la fila.

### Centro: turno
**Encabezado:**
- "En turno · n de m" y los puntos de operaciones usadas (por ejemplo, 1/3);
- nombre, mercado, divisa, calificación, dividendos y alertas;
- caja MXN, caja USD, cartera y deuda bancaria;
- **Ver cartera**, que despliega las posiciones.

**Tipos de operación:**
- **Comprar / Vender** (con el público): **Valor** (solo los colocados y vigentes; al vender, solo los que tiene) y **Cantidad**.
- **Cruce:**
  - elige si la empresa en turno **Compra** o **Vende**;
  - **Contraparte**, **Valor**, **Cantidad** y **Precio** pactado (propone la cotización; debe quedar dentro de ± 5 %).
- **Crédito:**
  - **Solicitar:** **Monto** y **Plazo (meses)**, con la tasa, la comisión, el monto a recibir, la mensualidad estimada, los intereses estimados y el disponible;
  - **Pagar anticipado:** tabla de créditos vigentes (saldo, tasa, mensualidad, meses que restan), **Monto a pagar** y **Todo** para liquidarlo.

**Disponibles:** el campo de cantidad se titula con la unidad (acciones o títulos) y debajo se muestra cuántos hay y cuántos se pueden operar; el máximo ya considera el público disponible, el máximo por operación, la tenencia máxima, la caja y los títulos de cada parte. Por ejemplo:
- "Público: 12,000 disponibles · Puedes comprar hasta 3,500 acciones";
- "Tienes 2,000 · Puedes vender hasta 2,000 acciones";
- en un cruce, lo que tiene la contraparte y el máximo al precio pactado.

**Vista previa y registro:**
- La **vista previa** muestra el importe, la comisión, la conversión de divisas, la ganancia y el impuesto (al vender), el neto y la tenencia final. Si la operación no procede, explica por qué.
- **Registrar operación** (o Enter) la aplica y cuenta una operación.
- **Operaciones del turno** lista lo registrado, con **Deshacer última operación**.
- **Pasar** (sin operaciones) o **Terminar turno**.

### Columna derecha: pestañas de información
| Pestaña | Contenido |
|---|---|
| **Cotizaciones** | Emisora, precio, variación de la ronda, calificación y títulos en manos del público. Clic en una fila la elige como valor de la operación |
| **Empresas** | Caja, deuda, utilidad y puntaje de cada una, con alertas (impago, quebrada) |
| **Bitácora** | Las últimas 200 notas, de la más reciente a la más antigua, con ronda, mes y tipo: inicio, colocación, operación, turno, crédito, cierre, precio (con desglose), bonos, dividendo, riesgo, impago, quiebra y fin |

### Diálogo Riesgos
- **Efectos vigentes:** chismes (con **Desmentir**), regulación por giro con los meses que quedan y empresas sin turno.
- **Catálogo:** los 11 riesgos con su categoría, probabilidad mensual y efecto. Se elige a quién afecta y se pulsa **Activar**. Los manuales no cuentan para el máximo por ronda y se pueden deshacer.

### Diálogo Partida
- **Respaldos automáticos:** se crean al cerrar la colocación, en cada cierre, al importar y al terminar. **Restaurar** pide confirmación.
- **Exportar partida** (`partida-bmv-rondaN.json`) e **Importar partida**.
- **Configuración:** vuelve a la configuración. Los cambios aplican solo a una partida nueva.
- **Terminar partida** (confirma): muestra el ranking final.

### Fin de partida
El centro muestra:
- el **podio**;
- la tabla con puntaje, valor, utilidad, salud, rendimiento y utilidad acumulada;
- las quebradas.

Desde ahí se puede **Exportar ranking (CSV)** (`ranking-bmv.csv`, compatible con Excel) o **Exportar partida**.

## 4. Pantalla pública (`publico.html`)

Para el proyector. Se actualiza sola con cada cambio del tablero, en el mismo navegador. **No muestra órdenes ni operaciones**; de las finanzas solo enseña las de la empresa en turno.

- **Encabezado:** ronda, periodo, inflación anual, TIIE y tipo de cambio.
- **Cotizaciones:** precio y variación de cada valor; marca los vencidos y liquidados.
- **Turno:** empresa en turno y las siguientes 3.
- **Empresa en turno** (junto al turno, encima del ranking):
  - caja MXN y caja USD;
  - cartera MXN y cartera USD (valor de mercado de los valores que cotizan en cada divisa);
  - deuda bancaria;
  - **total a pagar en la ronda**: mensualidades de la deuda y los créditos, más cupones y principal de sus bonos que vencen en los meses de la ronda y, si toca, el dividendo estimado. Se muestra solo el total.

  Fuera de los turnos dice "Sin empresa en turno".
- **Noticias:** todo el historial de colocación, cierres, bonos, dividendos, riesgos, impagos, quiebras y fin, de la más reciente a la más antigua, con scroll.
  - **Filtros:** Todas · Riesgos · Cierre · Bonos · Colocación · Dividendos · Impago · Quiebra. El fin de partida aparece en Cierre.
- **Ranking:** todas las empresas, con scroll; **Ranking final** al terminar.
- Noticias y ranking conservan la posición del scroll cuando la pantalla se actualiza; también se recorren con el teclado (Tab y flechas).

## 5. Formatos de archivo

| Archivo | Formato | Quién lo genera |
|---|---|---|
| `nombre_bmv.json` | `simulador-bmv/empresa` v1 (porcentajes en decimal: 0.08 = 8 %) | Simulador de cálculos → Exportar |
| `partida-bmv-rondaN.json` | `simulador-bmv/juego` v1 (partida completa) | Tablero → Partida → Exportar partida |
| `ranking-bmv.csv` | Posición, empresa, mercado, divisa, puntaje, valor, utilidad, salud, rendimiento %, utilidad acumulada, calificación | Tablero → Exportar ranking |

Ejemplos de empresas en [`tests/ejemplos/`](https://github.com/Mini-Buda-CH/simulador-bmv/tree/main/tests/ejemplos) del repositorio simulador-bmv.
