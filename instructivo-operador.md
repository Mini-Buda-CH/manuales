# Instructivo para quien lleva la sesión

Eres el operador: recibes los archivos de los equipos, configuras la partida, capturas en el tablero lo que cada equipo decide, cierras los periodos y comentas los resultados. Los equipos deciden; tú registras y das ritmo.

> Detalle de cada pantalla: [Manual de uso del Simulador BMV](manual-uso-simulador-bmv.md). Fórmulas: [Manual de valoración](manual-valoracion.md). Lo que ven los equipos: [Instructivo para participantes](instructivo-participantes.md).

---

## 1. Antes de la sesión

### Material
- **Una computadora** con un navegador actualizado (Chrome, Edge o Firefox) y el simulador abierto desde GitHub Pages o con `python3 -m http.server`. No necesita internet una vez cargado.
- **Un proyector o segunda pantalla** para la **Pantalla pública**. Debe abrirse **en el mismo navegador y la misma computadora** que el tablero: se sincronizan por el almacenamiento del navegador.
- **Los archivos `*_bmv.json`** de cada equipo, exportados desde el Simulador de cálculos (entre 2 y 40 empresas).
- **Opcional:** una hoja impresa del [Instructivo para participantes](instructivo-participantes.md) por equipo.

### Revisa los archivos (un día antes)
1. Abre `index.html` y suelta todos los JSON a la vez.
2. Revisa la vista previa de cada uno:
   - **Lista para agregar**: correcto.
   - **Ya está cargada**: es un duplicado (mismo identificador, o mismo nombre, mercado y divisa). Puedes **Reemplazar**; conserva su lugar en la lista.
   - **Con errores**: el archivo se descarta. Pide al equipo que corrija lo indicado en el Simulador de cálculos y lo vuelva a exportar. Errores comunes: capital contable cero o negativo, precio de emisión mayor a 100 %, falta de dividendos en Capitales.
3. **Agregar todas las válidas.** El orden de la lista es el orden de carga y desempata el ranking.

### Configura la partida
En **Configurar partida**:

| Decisión | Recomendación |
|---|---|
| Periodo por ronda | **Mensual** para sesiones con muchas rondas; **Trimestral** para avanzar más rápido en el tiempo (la demanda solo cuenta en el primer mes de cada ronda) |
| Mes y año inicial | Cualquier mes de enero 2025 a septiembre 2026. El entorno parte de los datos reales de ese mes |
| Semilla | 50928 (aprobada). La misma semilla reproduce el mismo entorno y el mismo azar; cámbiala con **Nueva semilla** para otra sesión |
| Parámetros | Deja los aprobados salvo que quieras un escenario especial. **Restablecer** los devuelve |

- Revisa las gráficas y la tabla del entorno: inflación, TIIE y tipo de cambio de las primeras 12 rondas.
- Pulsa **Guardar configuración** y luego **Iniciar partida**.

> Si ya hay una partida en curso, el botón dice **Continuar partida**. **Empezar una partida nueva** borra la actual; pide un segundo clic para confirmar.

## 2. Guion sugerido de la sesión

| Momento | Tiempo | Qué hacer |
|---|---|---|
| Apertura | 10 min | Proyecta la pantalla pública; explica cómo se gana (40/30/30), el orden en serpentina y las reglas básicas |
| Ronda 0 · colocación | 15–20 min | Cada equipo dicta sus órdenes; cierra la colocación y comenta la sobredemanda y los precios de apertura |
| Cada ronda | 5–10 min | Turnos de 1–2 min por equipo; cierra el periodo y lee las noticias |
| Pausa de análisis | cada 3–4 rondas | Pide a los equipos que revisen su calificación, caja y posición en el ranking |
| Cierre | 10 min | **Terminar partida**, ranking final, exporta el CSV y comenta las estrategias ganadoras |

Para una sesión de 2 horas, de 8 a 12 rondas mensuales suele ser suficiente.

## 3. Conducir la ronda 0 (colocación)

1. En el tablero, elige la **Empresa inversionista**, con la lista o con un clic en la columna **Orden de turnos**.
2. Captura la cantidad que pide en la columna **Orden** de cada emisión. Con **Enter** pasas a la siguiente fila.
   - El tablero muestra la caja, lo comprometido, la demanda acumulada y el máximo (30 %).
   - Un error aparece en la fila, por ejemplo cuando la caja no alcanza o se pasa del máximo.
3. Repite con cada empresa. Una orden se corrige escribiendo otra cantidad, o 0 para quitarla.
4. Pulsa **Cerrar colocación** y confirma con un segundo clic. Comenta:
   - qué emisiones tuvieron sobredemanda y abrieron más caras;
   - cuáles completó el público con descuento.

## 4. Conducir cada ronda

1. **Turno:** el tablero muestra la empresa en turno ("En turno · 2 de 8") con su caja, cartera y deuda. Si un equipo no está listo, elige otra empresa pendiente en la lista; no pierde su turno.
2. **Operación:**
   - Elige **Comprar**, **Vender**, **Cruce** o **Crédito**.
   - Elige el valor y la cantidad. Un clic en una fila de **Cotizaciones** la selecciona. Debajo de la cantidad verás cuántos títulos hay disponibles y el máximo que la empresa puede comprar, vender o cruzar.
   - Revisa la **vista previa**: precio, comisión, impuesto, conversión de divisas y tenencia final.
   - Pulsa **Registrar operación**.
   - **Cruce:** confirma en voz alta con ambas empresas el lado, la cantidad y el precio pactado (± 5 % de la cotización).
   - **Crédito:** muestra la tasa, la mensualidad y el disponible antes de solicitarlo.
3. **¿Te equivocaste?** **Deshacer última operación** revierte la última captura.
   - Funciona mientras la ronda siga abierta.
   - Se pierde al cerrar el periodo o al recargar la página.
4. **Fin del turno:** con 3 operaciones el turno termina solo; si no, **Terminar turno**, o **Pasar** si no operó.
5. **Cerrar periodo:** cuando todos terminaron. Si faltan turnos, el botón pide confirmar ("Quedan n turnos. ¿Cerrar?").
6. **Usa la pantalla pública.**
   - Mientras cada equipo opera, la ventana **Empresa en turno** muestra sus cajas, cartera, deuda y lo que debe pagar en la ronda.
   - En **Noticias** puedes filtrar por riesgos, cierre, bonos, colocación u otras, y recorrer el historial y el ranking completo con scroll.
7. **Lee las noticias.** Usa la bitácora (pestaña **Bitácora**) para explicar:
   - el cambio de cada acción, desglosado en valor justo, demanda, economía, azar y riesgos;
   - los riesgos que ocurrieron;
   - dividendos, impagos y quiebras.

### Avisos que debes comunicar a los equipos
- **En impago (n de 2):** la empresa tiene caja negativa. Debe vender valores o pedir crédito, o quebrará al tercer cierre.
- **Sin turno esta ronda:** por falla de sistemas o multa legal.
- **Dividendo omitido:** la empresa no tuvo caja y su acción bajó 5 %.
- **Quebrada:** sale del juego y del ranking; sus valores se liquidaron.

## 5. Riesgos manuales y chismes

Con **Riesgos** (barra superior):
- **Efectos vigentes:** chismes, regulación y sanciones en curso.
- **Catálogo:** cualquier riesgo, con su probabilidad mensual. Elige a quién afecta y pulsa **Activar**.
  - Los riesgos manuales no cuentan para el máximo de 2 por ronda.
  - Se pueden deshacer como una operación.
- **Desmentir:** si un equipo responde a un chisme (por ejemplo, con un comunicado breve), pulsa **Desmentir**. Termina el castigo a su demanda y recupera la mitad de la caída al siguiente cierre.

Úsalos con criterio pedagógico: para ilustrar un tema (alza de tasas, devaluación) o para equilibrar una partida, avisando al grupo.

## 6. Respaldos y problemas comunes

Con **Partida** (barra superior):
- **Respaldos automáticos:** se guardan al cerrar la colocación, en cada cierre de periodo, al importar y al terminar; se conservan los últimos 6. **Restaurar** pide confirmación.
- **Exportar partida:** descarga `partida-bmv-rondaN.json`. Hazlo al final de cada sesión o antes de cambiar de computadora.
- **Importar partida:** carga un archivo exportado y continúa donde quedó.
- **Configuración:** vuelve a la pantalla de parámetros. Los cambios aplican solo a una partida nueva.

| Problema | Solución |
|---|---|
| La pantalla pública no se actualiza | Ábrela desde el botón **Pantalla pública** del tablero, en el mismo navegador. Recárgala |
| Se capturó mal una operación | **Deshacer última operación** antes de cerrar el periodo |
| Se cerró un periodo por error | **Partida → Restaurar** el respaldo anterior |
| Cambio de computadora | **Exportar partida** en una e **Importar partida** en la otra |
| "No se pudo guardar la partida en este navegador" | El almacenamiento está lleno o bloqueado (modo privado). Exporta la partida y usa una ventana normal |
| Se alcanzó el horizonte | El entorno cubre 360 meses; termina la partida |

## 7. Terminar la partida

1. **Partida → Terminar partida** (confirma con un segundo clic).
2. El tablero y la pantalla pública muestran el **Ranking final**: podio y tabla con puntaje, componentes, rendimiento y utilidad acumulada.
3. **Exportar ranking (CSV)** para calificar, y **Exportar partida** para archivar.
4. Cierre sugerido. Pregunta a los primeros lugares:
   - ¿qué decisión les dio más valor?
   - ¿cómo cuidaron su calificación?
   - ¿qué riesgo les dolió más?
