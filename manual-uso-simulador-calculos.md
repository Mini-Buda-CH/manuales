# Manual de uso del Simulador de cálculos

Fase 1 de *Del aula a la bolsa*. Cada equipo prepara aquí los números de su empresa antes de salir a bolsa y exporta el archivo que usará en la fase 2, el [Simulador BMV](https://github.com/Mini-Buda-CH/simulador-bmv).

Documentos de la fase 2:
- [Manual de uso del Simulador BMV](manual-uso-simulador-bmv.md)
- [Instructivo para participantes](instructivo-participantes.md)
- [Instructivo para quien lleva la sesión](instructivo-operador.md)
- [Manual de valoración](manual-valoracion.md)

---

## Antes de empezar
- **Cómo abrirlo:** `index.html` en el navegador, desde GitHub Pages o con `python3 -m http.server`. No requiere instalación ni internet.
- **Persistencia:** lo capturado se guarda en el navegador. Al volver a una opción, los campos y resultados siguen ahí. Usa siempre la misma computadora y el mismo navegador hasta exportar.
- **Captura de números:**
  - el dinero se escribe con comas automáticas y hasta 2 decimales;
  - los enteros, sin decimales;
  - los porcentajes, como porcentaje (9.5 = 9.5 %).
- **Errores:** si un campo tiene un error, se marca en rojo con su mensaje debajo.
- **Calcular:** se muestra una animación breve de carga y luego los resultados.

## Ruta recomendada

1. Portada.
2. Valor de emisión.
3. Estado de resultados.
4. Balance general.
5. Datos para Simulador BMV.
6. Exportar.

SPEI, Ruta de una operación, Rendimiento simple y Costo de crédito son herramientas de aprendizaje: no se exportan, pero ayudan a tomar decisiones.

---

## 1. Portada (`index.html`)

| Campo | Qué capturar |
|---|---|
| Empresa | Nombre de la empresa |
| Tipo de mercado | **Capitales** (emite acciones) o **Deuda** (emite bonos) |
| Carrera | Finanzas y Contaduría · Administración y Gestión Empresarial · Administración de Empresas Turísticas |
| Tipo de empresa | Depende de la carrera (ver tabla) |
| Divisa | MXN o USD: la divisa principal de la empresa |

| Carrera | Tipos de empresa |
|---|---|
| Finanzas y Contaduría | Grupos financieros y banca comercial · Fideicomisos de bienes raíces · Casas de bolsa y operadoras de fondos |
| Administración y Gestión Empresarial | Conglomerados industriales y de materiales · Grandes cadenas de retail · Empresas de infraestructura y logística |
| Administración de Empresas Turísticas | Operadores de aeropuertos · Cadenas hoteleras e inmobiliarias turísticas · Servicios de entretenimiento y restaurantes |

El tipo de empresa importa en la fase 2: define la volatilidad de la acción y qué riesgos por giro le afectan.

**Botones:**
- **Iniciar →:** guarda los datos y abre el tablero.
  - Si cambias de empresa (otro nombre, mercado o divisa), se borran los cálculos de la anterior.
  - La misma empresa conserva sus cálculos.
- **Limpiar:** borra la portada y todos los cálculos guardados.

## 2. Tablero (`simulador.html`)

- **Menú lateral**, en este orden:
  - Valor de emisión
  - SPEI
  - Ruta de una operación
  - Rendimiento simple
  - Estados financieros: Estado de resultados y Balance general
  - Costo de crédito
  - Datos para Simulador BMV
  - **Exportar**
- **Barra superior:** el título de la opción elegida.
- **En celular:** el menú se abre con el botón de menú.
- **Navegación:** cada opción tiene su dirección (`simulador.html#valor-emision`, etc.), así que el botón Atrás del navegador funciona.
- **Tipo de cambio:** empieza en 18.50 MXN por USD y se comparte entre opciones durante la sesión.

---

## 3. Valor de emisión

Calcula cuánto vale y cuánto cuesta la emisión. Los campos dependen del mercado de la portada; si no hay portada, aparece un selector de mercado.

### Capitales
| Campo | Nota |
|---|---|
| Monto solicitado | Dinero que se quiere obtener |
| Divisa | Tomada de los datos de la empresa |
| Cantidad de acciones a emitir | Es también el total de acciones de la empresa |
| Precio de emisión (sobre valor nominal) | Por ejemplo, 98.50 % |
| Tipo de cambio (MXN por USD) | Para la sección convertida |

### Deuda
Monto solicitado, divisa, **Plazo de pago (años)**, **Cupón anual %**, **Periodicidad de pagos** (Mensual, Bimestral, Trimestral, Cuatrimestral, Semestral, Anual), precio de emisión % y tipo de cambio.

### Resultados (M = monto, P = precio %, C = cupón, n = pagos al año)
| Resultado | Fórmula |
|---|---|
| Valor nominal | M |
| Costo por acción (Capitales) | M ÷ acciones |
| Precio de emisión | M × P |
| Costo de emisión | M × (1 − P) |
| Rendimiento por periodo | M × C ÷ n |
| Rendimiento anual | M × C |
| Rendimiento total al final del plazo | M × C × plazo |
| Total a pagar | M + rendimiento total |

Se muestran en la divisa principal y convertidos a la otra.

**Gráficas:**
- Capitales: "Composición de la colocación".
- Deuda: capital e intereses acumulados por año.

**Ejemplo, Deuda:** M = 10,000,000, C = 9.5 %, semestral, plazo 3 años, P = 98.5 %.
- Precio de emisión: 9,850,000
- Costo de emisión: 150,000
- Rendimiento por periodo: 475,000
- Rendimiento total: 2,850,000
- Total a pagar: 12,850,000

## 4. SPEI

Ruta de una transferencia en 6 pasos y 4 participantes: cliente, institución emisora, Banco de México y receptora.
1. Instrucción del cliente
2. Validación por la emisora
3. Envío al SPEI
4. Procesamiento y liquidación
5. Acreditación en cuenta destino
6. Generación del comprobante (CEP)

**Cómo se usa:**
- Haz clic en un paso de la lista o del mapa, o usa los botones ← →.
- Con el teclado: flechas, Inicio y Fin.
- El paso activo muestra su descripción y a quién pasa la operación; el mapa traza la ruta hasta ese paso.

## 5. Ruta de una operación

Misma mecánica que SPEI, con 8 pasos y 5 participantes: cliente, intermediario, bolsa, compensación y custodia.
1. Apertura de cuenta
2. Orden de compra/venta
3. Validación y controles
4. Ingreso al sistema
5. Cruce de posturas
6. Obligaciones efectivo/valores
7. Compensación y liquidación
8. Posiciones y custodia

## 6. Rendimiento simple

Compara una inversión con la tasa esperada contra la tasa realizada.

| Campo | Nota |
|---|---|
| Valor inicial | Capital invertido |
| Periodos de inversión | Capitalización: mensual, bimestral, trimestral, cuatrimestral, semestral, anual |
| Años de inversión | Plazo |
| Tasa anual esperada | La planeada |
| Tasa anual realizada | La que realmente se obtuvo |

**Fórmula:** Monto = V × (1 + r ÷ n)^(n × años), con capitalización por periodo.

**Resultados:**
- monto y rendimiento esperados y realizados;
- **diferencia** en dinero (realizado − esperado) y en puntos porcentuales;
- **cumplimiento** = rendimiento realizado ÷ esperado × 100;
- gráfica de dos líneas y tabla por periodo.

## 7. Estados financieros

### Estado de resultados
| Campo | Nota |
|---|---|
| Ventas totales anuales | Año 1 para el Simulador BMV |
| Costo de ventas | |
| Gastos de administración y ventas | |
| Depreciación y amortización | |
| Tipo de cambio | Para la conversión |

**Resultados:**
- Utilidad bruta = ventas − costo.
- Utilidad de operación = UB − gastos − depreciación.
- Utilidad antes de impuestos = UO. Se omiten los resultados cambiario y financiero y otros conceptos.
- **ISR 30 %** y **PTU 10 %** sobre la UAI, o 0 si la UAI ≤ 0.
- Utilidad neta.
- Margen bruto y margen neto, con la frase "Por cada $100 vendidos, la empresa retiene/pierde…".
- Ambas divisas y gráfica de cascada.

### Balance general
Solo en la divisa de la portada. Todos los rubros se consideran de corto plazo.

| Activo | Pasivo |
|---|---|
| Bancos, Clientes, Inventarios | Proveedores, Acreedores |

**Resultados:**
- **Capital contable** = activo − pasivo. Debe ser positivo para exportar al BMV.
- **Razón circulante** = activo ÷ pasivo, con su interpretación:
  - menor a 1: problemas de liquidez;
  - de 1 a 1.5: adecuada;
  - de 1.5 a 2: óptima;
  - de 2 a 3: holgada;
  - mayor a 3: posible ineficiencia.
- **Capital de trabajo** = activo circulante − pasivo circulante.
- **Endeudamiento** = pasivo ÷ capital, con su nivel:
  - < 0.40: bajo;
  - 0.40–0.60: moderado;
  - 0.60–1.00: alto;
  - > 1.00: muy alto.

> En el Simulador BMV la calificación usa estas mismas razones (con la deuda bancaria y los bonos incluidos). Una razón circulante ≥ 2 y un endeudamiento ≤ 0.5 dan la calificación más alta.

## 8. Costo de crédito

Calcula el costo de un préstamo bancario.

| Campo | Nota |
|---|---|
| Monto del crédito | |
| Forma de pago | Mensual, Bimestral, Trimestral, Semestral o Único al vencimiento |
| Plazo (años) | |
| Comisiones | % que se cobra al inicio sobre el monto (sin IVA) |
| Interés anual | Tasa nominal anual |

**Cálculo:**
- **Pago fijo (sistema francés):** i = tasa ÷ pagos al año; pago = M × i ÷ (1 − (1 + i)^−N). El último pago absorbe el redondeo.
- **Único al vencimiento:** capital + interés simple (M × tasa × años).
- **Resultados:**
  - pago programado y número de pagos;
  - comisión y monto por recibir (M − comisión);
  - total de capital, total de intereses y monto total por pagar;
  - tabla de amortización y gráfica de pago a capital contra intereses.

## 9. Datos para Simulador BMV

Datos que la fase 2 necesita y no salen de los demás módulos. Primero hay que capturar la portada, porque el mercado define qué se pide.

| Campo | Nota |
|---|---|
| Crecimiento anual esperado de ventas | Incluye la inflación esperada. Las ventas del estado de resultados son el año 1 |
| Objetivo de la emisión | Proyectos de crecimiento · Expandir operaciones · Desarrollo de nuevos productos · Saldar deudas existentes |
| Caja en la otra divisa | La caja en la divisa principal es "Bancos" del balance |
| Deuda bancaria inicial | 0 si no tiene deuda |
| Tasa anual de la deuda y plazo restante (meses) | Obligatorios si hay deuda |
| Dividendos: porcentaje de la utilidad neta y periodicidad (Trimestral, Semestral, Anual) | Solo Capitales; 0 si no reparte |

Pulsa **Guardar datos**. El mensaje "Datos guardados." confirma.

## 10. Exportar

Botón al final del menú.

1. **Lista de verificación:** datos de la empresa, Valor de emisión, Estado de resultados, Balance general y Datos para Simulador BMV. Cada pendiente trae su enlace: Capturar en el inicio, Calcular o Completar.
2. **Resumen:** empresa, carrera y tipo, emisión, caja en ambas divisas, deuda, objetivo y dividendos.
3. **Descargar:** cuando dice "Todo listo para descargar.", baja `nombre-de-la-empresa_bmv.json` (formato `simulador-bmv/empresa` v1).

Entrega ese archivo a quien lleva la sesión. Si después corriges algo, vuelve a exportar: en el Simulador BMV se reemplaza la versión anterior.

> El valor de emisión y los datos BMV deben corresponder al mismo mercado de la portada; si cambiaste el mercado, recalcúlalos.

---

## Preguntas frecuentes
- **¿Se pierden mis datos al cerrar el navegador?** No, quedan guardados en ese navegador. Se borran con **Limpiar** o al iniciar otra empresa.
- **¿Puedo cambiar el tipo de cambio?** Sí, en Valor de emisión y Estado de resultados; se comparte en la sesión.
- **¿Por qué no puedo exportar?** Revisa la lista de verificación. Un capital contable negativo, o un mercado distinto en Valor de emisión, impiden exportar o hacen que el BMV rechace el archivo.
