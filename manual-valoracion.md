# Manual de valoración de las empresas

Cómo calcula el Simulador BMV el valor, la calificación, el precio de las acciones y de los bonos, y el puntaje de cada empresa. Todas las fórmulas salen del código del repositorio [simulador-bmv](https://github.com/Mini-Buda-CH/simulador-bmv) (`js/mercado.js`, `js/motor.js`, `js/riesgos.js`, `js/entorno.js`) con los valores aprobados de `js/parametros.js`. Los parámetros marcados como *configurable* se pueden cambiar en **Configurar partida**; los demás son fijos.

Contenido:
1. [De dónde salen los datos](#1-de-dónde-salen-los-datos)
2. [Balance a valor de mercado](#2-balance-a-valor-de-mercado)
3. [Calificación crediticia](#3-calificación-crediticia)
4. [Colocación (ronda 0)](#4-colocación-ronda-0)
5. [Resultados de cada mes](#5-resultados-de-cada-mes)
6. [Precio de las acciones](#6-precio-de-las-acciones)
7. [Precio de los bonos](#7-precio-de-los-bonos)
8. [Dividendos, impago y quiebra](#8-dividendos-impago-y-quiebra)
9. [Riesgos y su efecto en el valor](#9-riesgos-y-su-efecto-en-el-valor)
10. [Entorno económico](#10-entorno-económico)
11. [Puntaje y ranking](#11-puntaje-y-ranking)
12. [Orden de cálculo al cerrar un periodo](#12-orden-de-cálculo-al-cerrar-un-periodo)
13. [Tabla de parámetros](#13-tabla-de-parámetros)

---

## 1. De dónde salen los datos

Cada empresa llega en un archivo `nombre_bmv.json` exportado desde el **Simulador de cálculos**:

| Dato | Origen en el Simulador de cálculos | Uso en el Simulador BMV |
|---|---|---|
| Nombre, mercado (Capitales/Deuda), divisa, carrera, tipo de empresa | Portada | Identidad; el tipo de empresa fija la volatilidad de la acción y los riesgos por giro |
| Monto, acciones o plazo/cupón/periodicidad, precio de emisión % | Valor de emisión | Lo que se coloca en la ronda 0 |
| Ventas, costo de ventas, gastos, depreciación | Estado de resultados | Base de los resultados mensuales (las ventas son el año 1) |
| Bancos, clientes, inventarios, proveedores, acreedores | Balance general | Balance inicial |
| Crecimiento anual de ventas, caja en la otra divisa, deuda bancaria inicial, dividendos | Datos para Simulador BMV | Proyección de ventas, caja, deuda y política de dividendos |

Clientes, inventarios, proveedores y acreedores se mantienen fijos durante la partida; lo que cambia es la caja, la cartera de inversiones, la deuda y los bonos emitidos.

## 2. Balance a valor de mercado

En cada momento se arma el balance de cada empresa **en su divisa** (`Motor.balance`):

- **Caja** = caja en su divisa + caja en la otra divisa × tipo de cambio (sin diferencial).
- **Inversiones** = valor de su cartera a precio de mercado: cantidad × cotización de cada acción o bono que posee, convertido a su divisa.
- **Activo total** = caja + clientes + inventarios + inversiones. Todo el activo se considera circulante.
- **Pasivo total** = proveedores + acreedores + deuda bancaria (saldo de la deuda inicial + saldo de los créditos) + valor nominal de los bonos emitidos en circulación.
- **Pasivo circulante** = proveedores + acreedores + pagos de deuda de los próximos 12 meses + cupones de un año de los bonos emitidos.
- **Capital** = activo total − pasivo total.

## 3. Calificación crediticia

`Mercado.calificar` suma dos puntajes:

| Razón circulante (activo circulante ÷ pasivo circulante) | Puntos |
|---|---|
| 2.0 o más | 3 |
| de 1.5 a menos de 2.0 | 2 |
| de 1.0 a menos de 1.5 | 1 |
| menos de 1.0 | 0 |

| Endeudamiento (pasivo total ÷ capital) | Puntos |
|---|---|
| 0.5 o menos | 3 |
| más de 0.5 hasta 1.0 | 2 |
| más de 1.0 hasta 2.0 | 1 |
| más de 2.0 | 0 |

| Puntos | 6 | 5 | 4 | 3 | 2 | 0–1 | capital ≤ 0 |
|---|---|---|---|---|---|---|---|
| Calificación | AAA | AA | A | BBB | BB | B | C |

**Ejemplo:**
- Activo circulante: 5,000,000; pasivo circulante: 2,000,000. La razón es 2.5, que da 3 puntos.
- Pasivo total: 3,000,000; capital: 2,000,000. El endeudamiento es 1.5, que da 1 punto.
- Total: 4 puntos, calificación **A**.

La calificación se recalcula:
- al crear la partida;
- al cerrar la colocación;
- cada mes que se cierra.

Sus efectos:
- la tasa y el acceso al crédito bancario;
- la sobretasa que el mercado exige a los bonos;
- el precio al que el público toma una colocación incompleta;
- el 30 % del puntaje.

## 4. Colocación (ronda 0)

### Precio de colocación
- **Acciones:** valor nominal = monto ÷ cantidad de acciones. Precio de colocación = nominal × precio de emisión %.
- **Bonos:** títulos de $100 nominales. Número de títulos = monto ÷ 100 (entero hacia abajo). Precio de colocación = 100 × precio de emisión %.

### Órdenes
Cada empresa anota cuántos títulos quiere de cada emisión ajena:
- Máximo el 30 % de cada emisión (*configurable*: tenencia máxima).
- La caja debe alcanzar para todas sus órdenes juntas. Si falta una divisa, se cubre con la otra, con un diferencial cambiario de 0.5 %.

### Al cerrar la colocación
**Sobredemanda (demanda ≥ oferta):**
- Se asigna a prorrata con enteros. Las unidades sobrantes van a las fracciones más grandes y, si hay empate, por orden de carga.
- La emisora recibe oferta × precio de colocación.
- Precio de apertura = precio de colocación × (1 + mín(10 %, 0.10 × (demanda ÷ oferta − 1))).

**Demanda incompleta:**
- Cada orden se asigna completa y el público compra el resto con un descuento según la calificación de la emisora:

| Calificación | AAA | AA | A | BBB | BB | B | C |
|---|---|---|---|---|---|---|---|
| Factor sobre el precio de colocación | 1.00 | 0.98 | 0.95 | 0.90 | 0.85 | 0.80 | 0.70 |

- Precio de apertura = promedio ponderado: lo recibido ÷ total de títulos.

En la colocación no se cobra comisión. La emisora recibe el dinero en su caja. Después se recalculan las calificaciones y se fija el **valor inicial** de cada empresa para el puntaje.

**Ejemplo, Capitales:** monto 1,000,000, 100,000 acciones, precio de emisión 98.5 %. El nominal es 10 y el precio de colocación 9.85.
- **Demanda de 150,000:** precio de apertura = 9.85 × (1 + 0.10 × 0.5) = **10.34**.
- **Demanda de 60,000 con calificación A:**
  - el público compra 40,000 a 9.85 × 0.95 = 9.3575;
  - la emisora recibe 591,000 + 374,300 = 965,300;
  - precio de apertura = **9.653**.

## 5. Resultados de cada mes

`resultadosMes` calcula el estado de resultados de cada empresa en el mes *t* (t = 1 es el primer mes jugado).

| Concepto | Fórmula |
|---|---|
| Ventas | ventas del año 1 ÷ 12 × (1 + crecimiento anual)^((t − 1) ÷ 12) |
| Costo de ventas | costo anual ÷ 12 × (INPC del mes ÷ INPC inicial) × factor regulatorio |
| Gastos | gastos anuales ÷ 12 × (INPC del mes ÷ INPC inicial) × factor regulatorio |
| Depreciación | depreciación anual ÷ 12 (no sale de caja) |
| Intereses | deuda inicial: saldo × tasa ÷ 12 (pago francés fijo); créditos: saldo × (TIIE del mes + sobretasa) ÷ 12 |
| Cupones devengados | nominal de bonos emitidos × cupón ÷ 12 |
| Ingresos financieros | cupones y dividendos cobrados en el mes |
| Extraordinarios | costos por riesgos (multas, fallas, clientes que no pagan) |
| **UAI** | ventas − costo − gastos − depreciación − intereses − cupones devengados + ingresos financieros − extraordinarios |
| ISR y PTU | 30 % y 10 % de la UAI si es positiva; 0 si no |
| Otros | ganancias o pérdidas realizadas del mes anterior (ventas de valores ya con impuesto, vencimientos, liquidaciones) |
| **Utilidad neta** | UAI − ISR − PTU + otros |

**Caja del mes:** + ventas − costo − gastos − intereses − abono a capital − ISR − PTU. Los cupones de los bonos emitidos se pagan en sus fechas: cada 12 ÷ periodicidad meses, y el nominal al vencimiento.

Se guarda la utilidad neta de los últimos 12 meses, la utilidad acumulada desde el inicio y la utilidad desde el último dividendo.

## 6. Precio de las acciones

### Valor justo
`Mercado.valorJustoAccion`:

- **Utilidad de 12 meses** (`Motor.utilidad12m`):
  - con 12 meses o más: la suma de los últimos 12;
  - con menos: el promedio anualizado (suma × 12 ÷ meses);
  - sin meses cerrados: la utilidad base anual = (ventas − costo − gastos − depreciación) × 0.6 si es positiva.
- Si la utilidad de 12 meses es positiva: **valor justo = múltiplo × utilidad de 12 meses ÷ acciones**. El múltiplo es 12 (*configurable*).
- Si no: **valor justo = 0.8 × capital contable ÷ acciones**, con un mínimo de 0.01.

### Movimiento mensual
`Mercado.nuevoPrecioAccion`. Al cerrar cada mes:

```
total = 0.10 · ln(VJ / P)          ← tendencia al valor justo
      + 2 · (compras netas ÷ acciones totales)   ← demanda
      − 3 · ΔTIIE                  ← economía
      + σ · ε                      ← azar
P nuevo = P · exp(total), con un tope de ± 25 % por mes
```

- **Tendencia al valor justo:** cada mes se cierra el 10 % de la distancia (en logaritmos) entre el precio y el valor justo.
- **Demanda:**
  - compras netas = compras al público − ventas al público. Un cruce suma o resta según su precio pactado: arriba de la cotización empuja hacia arriba y abajo empuja hacia abajo.
  - Solo cuenta en el **primer mes** de la ronda; en rondas trimestrales, los meses 2 y 3 no llevan demanda.
  - Con un chisme vigente, la demanda positiva se reduce a la mitad.
- **Economía:** ΔTIIE es el cambio de la TIIE en el mes, en decimal. Si sube 25 pb, el precio baja ≈ 0.75 %.
- **Azar:** ε es normal estándar, generada con la semilla (misma semilla, misma secuencia). σ según el tipo de empresa:

| Tipo de empresa | σ mensual |
|---|---|
| Grupos financieros y banca comercial | 3.5 % |
| Fideicomisos de bienes raíces | 3.0 % |
| Casas de bolsa y operadoras de fondos | 4.5 % |
| Conglomerados industriales y de materiales | 4.0 % |
| Grandes cadenas de retail | 3.5 % |
| Empresas de infraestructura y logística | 3.5 % |
| Operadores de aeropuertos | 4.5 % |
| Cadenas hoteleras e inmobiliarias turísticas | 4.5 % |
| Servicios de entretenimiento y restaurantes | 5.0 % |
| Otro o sin tipo | 4.0 % |

**Ejemplo:**
- Datos: precio 10.00; utilidad de 12 meses 1,000,000; 1,000,000 de acciones. El valor justo es 12.
- Las empresas compraron en neto el 2 % de las acciones, la TIIE subió 25 pb y el azar dio ε = 0.5 con σ = 4 %.
- Valor justo: 0.10 · ln(12/10) = +0.0182
- Demanda: 2 · 0.02 = +0.0400
- Economía: −3 · 0.0025 = −0.0075
- Azar: 0.04 · 0.5 = +0.0200
- Total: 0.0707; el precio nuevo es 10 · e^0.0707 = **10.73** (+7.3 %).

Además del movimiento mensual, el precio cambia por:
- riesgos (sección 9);
- el castigo por omitir un dividendo (−5 %);
- la liquidación en una quiebra (sección 8).

La bitácora de cada cierre muestra el cambio de la ronda desglosado en *valor justo, demanda, economía, azar* y *riesgos*.

## 7. Precio de los bonos

### Precio de un título
`Mercado.precioBono`: valor presente, al rendimiento *y*, de los cupones (100 × cupón ÷ n) y del nominal que faltan:

```
precio = Σ  (100·c/n + [100 si es el último]) · (1 + y/n)^(−meses·n/12)
```

### Rendimiento de referencia (y0)
Al cerrar la colocación se calcula el rendimiento que reproduce exactamente el precio de apertura (bisección, `rendimientoImplicito`). Se guardan también la TIIE y la sobretasa de la calificación de ese momento.

### Cada mes
`Mercado.nuevoPrecioBono`:

```
y = y0 + (TIIE actual − TIIE inicial) + (sobretasa actual − sobretasa inicial)
premio = mín(±10 %, premio anterior × 0.5 + 0.5 × compras netas ÷ títulos)
precio = precioBono(y) × (1 + premio)
```

| Calificación | AAA | AA | A | BBB | BB | B | C |
|---|---|---|---|---|---|---|---|
| Sobretasa sobre TIIE | 1 % | 1.5 % | 2.5 % | 4 % | 6 % | 9 % | 20 % |

**Ejemplo:**
- Datos:
  - bono con cupón 10 %, semestral, 36 meses;
  - abre en 98.00, lo que da y0 = 10.80 %;
  - al mes siguiente (35 meses restantes) la TIIE sube 50 pb y la emisora baja de A a BBB (+1.5 %);
  - compras netas: 1 % de los títulos.
- y = 10.80 % + 0.50 % + 1.50 % = 12.80 %, con un precio sin premio de 94.17.
- El premio es 0.5 %, así que el precio final es **94.65**.

**Al vencimiento:**
- La emisora paga el último cupón y el nominal (100 por título).
- Los tenedores registran la diferencia contra su costo como ganancia o pérdida del mes siguiente.

## 8. Dividendos, impago y quiebra

### Dividendos
Solo empresas de Capitales con un porcentaje mayor que 0.

- **Fechas:** se pagan cuando el mes de partida es múltiplo de 12 ÷ periodicidad (trimestral: meses 3, 6, 9…).
- **Monto:** porcentaje × utilidad neta acumulada desde el último pago. Si esa utilidad no es positiva, no hay dividendo.
- **Retención:** cada tenedor recibe su parte menos 10 % (*configurable*). Lo cobrado entra como ingreso financiero del mes.
- **Sin caja suficiente** (aun convirtiendo la otra divisa): el dividendo se omite y la acción baja **5 %** (*configurable*).

### Impago
Al final de cada mes, si una divisa queda negativa se convierte la otra (con el diferencial). Si aun así queda caja negativa:
- la empresa entra **en impago (1 de 2)** y luego (2 de 2);
- si regulariza su caja, el contador vuelve a 0.

### Quiebra
Al tercer cierre seguido con caja negativa, la empresa quiebra. Son 2 meses de gracia (*configurable*).

**Fondo de liquidación** = caja + cartera vendida al público (−0.5 %) + 80 % de clientes + 50 % de inventarios − proveedores − acreedores.

**Prelación:**
1. **Bancos:** cobran hasta el saldo de la deuda bancaria.
2. **Bonos:** a prorrata, hasta 100 por título.
3. **Accionistas:** reciben el residuo por acción.

El precio del valor queda en lo que se pagó por título. Los tenedores registran la pérdida o ganancia contra su costo y la empresa sale del juego y del ranking.

## 9. Riesgos y su efecto en el valor

Cada mes se sortean eventos, con un máximo de 2 por ronda (*configurable*). Las probabilidades son por mes (*configurables*):

| Riesgo | Prob. | Efecto en el valor |
|---|---|---|
| Caída general del mercado | 6 % | Todas las acciones −5 a −20 % |
| Caída por giro comercial | 8 % | Acciones de un tipo de empresa −8 a −25 % |
| Incumplimiento de pago | 1 a 5 % por empresa* | Pierde 15–40 % de su saldo de clientes en caja; puede llevar a impago |
| Falla de personas | 4 % | Costo del 10 % de su utilidad operativa mensual** |
| Falla de procesos | 4 % | Costo del 15 % |
| Falla de sistemas | 3 % | Costo del 10 % y pierde su siguiente turno |
| Cambio de tasas | 5 % | TIIE +50, 75 o 100 pb desde el mes siguiente, que regresa 25 pb por mes |
| Tipo de cambio | 5 % | El dólar sube o baja 3–10 % de forma permanente |
| Incumplimiento legal | 3 % | Multa del 10 % de sus activos y una ronda sin operar |
| Cambio regulatorio | 3 % | Costos y gastos de un tipo de empresa +3 % durante 6 meses |
| Chisme | 5 % | Su acción −10 a −35 % y su demanda a la mitad durante 2 rondas |

\* Probabilidad = 1 % + 4 % × mín(1, endeudamiento ÷ 2). Más deuda, más riesgo.
\** Utilidad operativa del último mes; si no es positiva, 1 % de sus ventas mensuales.

**Notas:**
- Los costos de los riesgos salen de la caja y entran como gastos extraordinarios en la UAI del mes.
- El operador puede activar cualquier riesgo a mano; esos no cuentan para el máximo.
- Un chisme **desmentido** termina el castigo a la demanda y recupera la mitad de la caída al siguiente cierre.

## 10. Entorno económico

`Entorno.simular` parte del mes inicial real y simula hacia adelante con la semilla. Los datos reales cubren de enero 2025 a septiembre 2026: INPC, TIIE 28 días y tipo de cambio.

- **Inflación mensual:**
  - fórmula: meta mensual + 0.5 × (inflación anterior − meta mensual) + choque;
  - el choque se calibra para que la inflación anual simulada tenga la misma dispersión que la real;
  - meta: 3 % (*configurable*).
- **TIIE:**
  - cada mes se mueve un paso de 25 pb (*configurable*) hacia la tasa objetivo = meta + tasa real neutral (3 %) + reacción × (inflación anual − meta);
  - la reacción es 1.5, o 2 si la inflación sale de la banda de ±1 %;
  - piso 3 %, techo 15 %.
- **Tipo de cambio:**
  - fórmula: TC × exp(ln((1 + inflación MX) ÷ (1 + inflación EUA)) + σ × ε);
  - σ se calibra con la historia y la inflación de EUA es 2.5 % (*configurable*).

**Cómo llega a las empresas:**
- La inflación sube costos y gastos.
- La TIIE mueve el precio de las acciones (−3 × ΔTIIE), el rendimiento de los bonos y la tasa de los créditos.
- El tipo de cambio cambia el valor en la divisa de la empresa de la caja y las inversiones en la otra divisa, y el valor en MXN de las empresas en USD.

## 11. Puntaje y ranking

`Mercado.puntajes` combina tres componentes, cada uno de 0 a 100:

| Componente | Peso | Cálculo |
|---|---|---|
| Valor | 40 % | Rendimiento = valor actual ÷ valor inicial − 1, normalizado entre empresas |
| Utilidad | 30 % | Utilidad acumulada ÷ activos iniciales, normalizado entre empresas |
| Salud | 30 % | Puntos de calificación ÷ 6 × 100 (sin normalizar) |

- **Valor de la empresa** (en MXN):
  - Capitales: precio × acciones;
  - Deuda: capital contable;
  - el valor inicial es el del cierre de la colocación.
- **Normalización mín–máx:** la mejor empresa obtiene 100, la peor 0 y el resto en proporción. Si todas son iguales, todas obtienen 50.
- **Empates:** se ordenan por orden de carga.
- **Puntaje = 0.40 · valor + 0.30 · utilidad + 0.30 · salud.** Las ponderaciones son *configurables* y deben sumar 100 %.

**Ejemplo con tres empresas:**

| Empresa | Rend. | Utilidad ÷ activos | Calif. | Valor | Utilidad | Salud | **Puntaje** |
|---|---|---|---|---|---|---|---|
| A | +20 % | 6 % | A (4) | 100 | 70 | 66.7 | **81.0** |
| B | −5 % | 10 % | AAA (6) | 0 | 100 | 100 | **60.0** |
| C | +5 % | −3.3 % | BB (2) | 40 | 0 | 33.3 | **26.0** |

**Orden de turnos (serpentina):**
- Al crear la partida se calcula un **puntaje de registro**, con el mismo método, sin rendimiento y con la utilidad base.
- Las rondas impares van del mejor al peor y las pares al revés.
- Las empresas quebradas salen del orden.

## 12. Orden de cálculo al cerrar un periodo

En cada mes de la ronda:
1. Avanza el entorno (inflación, TIIE, tipo de cambio).
2. Solo en el primer mes: los chismes desmentidos recuperan la mitad de su caída.
3. Cupones y vencimientos de bonos.
4. Resultados de cada empresa (sección 5).
5. Dividendos en sus fechas.
6. Sorteo de riesgos y sus efectos.
7. Revisión de liquidez: impago o quiebra y liquidación.
8. Calificaciones.
9. Precios de acciones y bonos. La demanda de la ronda solo cuenta en el primer mes.

Al final de la ronda:
- se escribe el cierre en la bitácora (entorno y desglose de precios);
- se guarda el ranking;
- se crea un respaldo automático.

## 13. Tabla de parámetros

| Grupo | Parámetro | Valor aprobado |
|---|---|---|
| Partida | Periodo por ronda · mes inicial · semilla | Mensual · enero 2025 · 50928 |
| Entorno | Meta de inflación · banda · paso TIIE · tasa real neutral · piso TIIE · inflación EUA | 3 % · ±1 % · 0.25 % · 3 % · 3 % · 2.5 % |
| Crédito | Sobretasas AAA · AA · A · BBB | 2 % · 4 % · 6 % · 9 % (negado debajo de BBB) |
| Crédito | Plazo · comisión de apertura · máximo sobre activos · meses para regularizar | 6–36 meses · 1 % · 40 % · 2 |
| Operación | Comisión · diferencial del público · diferencial cambiario | 0.25 % · 0.5 % · 0.5 % |
| Operación | Impuesto a ganancias · retención de dividendos · operaciones por turno | 10 % · 10 % · 3 |
| Límites | Máximo por operación · tenencia máxima · banda de cruces | 5 % · 30 % · ±5 % |
| Precio | Múltiplo de utilidad · castigo por omitir dividendo | 12 · 5 % |
| Riesgos | Máximo por periodo | 2 |
| Puntaje | Valor · utilidad · salud | 40 % · 30 % · 30 % |

Fijos en el código:
- tendencia 0.10;
- peso de la demanda 2 (bonos 0.5);
- sensibilidad a la TIIE 3;
- tope de ±25 % mensual y premio de bonos ±10 %;
- sobretasas de bonos y factores de colocación por calificación;
- ISR 30 % y PTU 10 %.
