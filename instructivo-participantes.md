# Instructivo para participantes

Eres la dirección financiera de una empresa que sale a cotizar en la Bolsa Mexicana de Valores. En la **fase 1** preparas a tu empresa en el *Simulador de cálculos*; en la **fase 2** compites en el *Simulador BMV* contra las demás empresas del grupo. Quien lleva la sesión (el operador) captura en el tablero lo que tu equipo decide; tú decides qué hacer.

> Para entender a fondo cómo se calculan los precios y el puntaje, consulta el [Manual de valoración](manual-valoracion.md).

---

## Antes de la sesión: prepara tu empresa (fase 1)

En el [Simulador de cálculos](https://github.com/Mini-Buda-CH/Simulador-de-calculos), con su [manual de uso](manual-uso-simulador-calculos.md):

1. **Portada:** nombre de la empresa, mercado (*Capitales* = vendes acciones; *Deuda* = emites bonos), carrera, tipo de empresa y divisa (MXN o USD). Pulsa **Iniciar**.
2. **Valor de emisión:** cuánto dinero pides y en qué condiciones.
   - Capitales: monto, cantidad de acciones (es el total de acciones de tu empresa) y precio de emisión %.
   - Deuda: monto, plazo, cupón, periodicidad y precio %.
3. **Estados financieros:** estado de resultados (ventas, costo, gastos, depreciación) y balance general (bancos, clientes, inventarios, proveedores, acreedores). El capital debe ser positivo.
4. **Datos para Simulador BMV:** crecimiento anual de ventas, caja en la otra divisa, deuda bancaria inicial, objetivo de la emisión y, si eres de Capitales, tu política de dividendos.
5. **Exportar:** cuando todo esté en verde, descarga `tu_empresa_bmv.json` y entrégalo al operador.

**Piensa en estas decisiones desde ya, porque definen tu juego:**
- Un precio de emisión bajo (por ejemplo, 95 %) atrae demanda, pero recibes menos dinero.
- Mucha deuda inicial o pocos bancos empeoran tu **calificación** y, con ella, el crédito, el precio de tus bonos y el 30 % del puntaje.
- Un dividendo alto premia a tus accionistas, pero si no tienes caja en la fecha de pago tu acción baja 5 %.
- Un crecimiento de ventas exagerado no es gratis: los costos suben con la inflación de todos modos.

## Cómo se gana

Gana el puntaje más alto al terminar la partida:

| Peso | Qué mide | Cómo lo mejoras |
|---|---|---|
| **40 % Valor** | Cuánto creció el valor de tu empresa desde la colocación (Capitales: precio × acciones; Deuda: capital contable) | Utilidades constantes, inversiones que suben, evitar castigos |
| **30 % Utilidad** | Utilidad acumulada ÷ activos iniciales | Controlar intereses y riesgos; ganar con tus inversiones |
| **30 % Salud** | Tu calificación (AAA = 100, AA = 83, A = 67, BBB = 50, BB = 33, B = 17 o 0, C = 0) | Razón circulante ≥ 2 y pasivo ≤ 0.5 veces tu capital |

Valor y utilidad se comparan contra las demás empresas: el mejor obtiene 100 y el peor 0.

## Durante la sesión

### Ronda 0: colocación
Todas las empresas salen a bolsa al mismo tiempo. Tu equipo decide **cuánto comprar de cada emisión de las demás**:
- No puedes comprar tu propia emisión.
- Máximo el 30 % de cada emisión.
- Tu caja debe alcanzar para todas tus órdenes juntas. Si te falta una divisa, se convierte la otra automáticamente con un diferencial de 0.5 %.

Al cerrar la colocación:
- Si una emisión tiene más demanda que oferta, se reparte a prorrata y abre hasta 10 % más cara.
- Si le falta demanda, el público compra el resto con descuento según la calificación de la emisora, y abre más barata.

### Rondas
Cada ronda equivale a un mes o a un trimestre, según la configuración del operador.

**Orden de turnos (serpentina):**
- Lo fija el puntaje de registro: en las rondas impares va del mejor al peor y en las pares al revés.
- Si no te toca primero, en la siguiente ronda te toca antes.

**En tu turno puedes hacer hasta 3 operaciones** de entre estas:

| Operación | Qué es | Costo |
|---|---|---|
| **Comprar** al público | Compras acciones o bonos de otra empresa a la cotización + 0.5 % | Comisión 0.25 % |
| **Vender** al público | Vendes lo que tienes a la cotización − 0.5 % | Comisión 0.25 % + 10 % de impuesto sobre la ganancia |
| **Cruce** | Pactas con otra empresa una compra o venta a un precio dentro de ± 5 % de la cotización. La contraparte debe estar de acuerdo | Comisión 0.25 % para cada lado + impuesto al vendedor |
| **Crédito** | Pides un préstamo bancario o pagas uno por anticipado | Ver abajo |

También puedes **Pasar** (sin operar) o **Terminar turno** antes de usar las 3 operaciones.

**Reglas de operación:**
- Máximo el 5 % de una emisión por operación y el 30 % de tenencia total.
- No hay ventas en corto: solo vendes lo que tienes.
- No compras ni vendes tus propios valores.
- Al público solo le compras lo que tiene disponible.
- La ganancia de una venta se calcula contra tu costo promedio, que ya incluye comisiones. Entra a tus resultados del mes siguiente.

**Crédito bancario:**
- Tasa = TIIE + sobretasa según tu calificación:

  | Calificación | AAA | AA | A | BBB | BB o menor |
  |---|---|---|---|---|---|
  | Sobretasa | 2 % | 4 % | 6 % | 9 % | Negado |

- Plazo de 6 a 36 meses y comisión de apertura de 1 %, que se descuenta del monto.
- Tu deuda bancaria total no puede pasar del 40 % de tus activos.
- La tasa se ajusta cada mes con la TIIE y la mensualidad se recalcula.
- Puedes pagar por anticipado (cuenta como operación).

### Al cerrar cada periodo
El operador pulsa **Cerrar periodo** y el simulador:
1. Mueve la economía: inflación, TIIE y tipo de cambio.
2. Calcula tus resultados del mes. Las ventas crecen con tu tasa, los costos suben con la inflación y pagas intereses, ISR 30 % y PTU 10 %.
3. Paga y cobra cupones de bonos y dividendos en sus fechas.
4. Puede sortear **riesgos** (máximo 2 por ronda).
5. Revisa tu caja. Si queda negativa, entras en **impago**.
6. Recalcula calificaciones y precios.

**Qué mueve el precio de una acción cada mes:**
- se acerca 10 % a su valor justo (12 veces la utilidad anual por acción);
- la demanda neta de la ronda;
- el cambio de la TIIE;
- un componente al azar;
- los riesgos.

Todo queda explicado en la bitácora y en las noticias de la pantalla pública.

### Riesgos que te pueden tocar
- **Mercado:** caída general o de tu giro.
- **Clientes que no pagan:** es más probable si estás endeudado.
- **Fallas:** de personas, procesos o sistemas. La falla de sistemas te quita tu siguiente turno.
- **Economía:** subida de tasas o cambio en el dólar.
- **Multa:** cuesta el 1 % de tus activos y una ronda sin operar.
- **Regulación:** tus costos suben 3 % durante 6 meses.
- **Chisme sobre tu acción:** baja 10–20 % y tu demanda se reduce a la mitad durante 2 rondas. Pide al operador **desmentirlo** para recuperar la mitad de la caída.

### Impago y quiebra
- Si tu caja queda negativa al cierre, estás **en impago (1 de 2)**. Tienes 2 meses para regularizar: vende valores o pide crédito si tu calificación lo permite.
- Al tercer cierre seguido en negativo **quiebras** y sales del juego. Tus bienes se liquidan: primero cobran los bancos, luego los bonistas y al final los accionistas.

## Consejos
- **Vigila tu caja en las dos divisas.** Cada conversión cuesta 0.5 %.
- **Diversifica.** Un riesgo de giro golpea a todas las empresas del mismo tipo.
- **Cuida tu calificación.** Comprar valores aumenta tus activos, pero pedir crédito aumenta tu pasivo; mantén el endeudamiento bajo 1.
- **Los cruces sirven para negociar.** Puedes acordar un precio mejor que el del público y ahorrarte el diferencial de 0.5 %.
- **No te descapitalices antes de una fecha de dividendo o de cupón.**
- **Revisa el ranking** en la pantalla pública después de cada cierre.

## Glosario
- **Calificación:** nota de AAA a C según tu liquidez y endeudamiento.
- **Cotización:** precio de mercado actual de una acción o bono.
- **Cruce:** operación pactada entre dos empresas.
- **Cupón:** interés que paga un bono.
- **Diferencial:** lo que cobra el público (0.5 %) por comprarte o venderte, o el costo de convertir divisas.
- **Impago:** caja negativa al cierre de un mes.
- **Serpentina:** orden de turnos que se invierte cada ronda.
- **TIIE:** tasa de interés interbancaria; sube o baja según la inflación.
- **Valor justo:** 12 veces la utilidad anual por acción; el precio tiende hacia él.
