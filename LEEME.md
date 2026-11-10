# Entregas PT

## Novedades v2.4 — entregas parciales y plazo automático
- **Varios envíos por OT**: en cada envío eliges qué ítems y **qué cantidad** salen; lo demás queda pendiente. Cada envío tiene chofer, vehículo, prueba de entrega y comprobante propios (el comprobante lista lo entregado y lo pendiente).
- La OT queda como **"Parcial"** hasta que sale todo; la pantalla Hoy muestra las **notas con entregas parciales**.
- **Plazo automático**: lee el "Tiempo de entrega" de la nota (ej. "10 días hábiles") y calcula la fecha compromiso sumando días de **lunes a viernes** desde la fecha que elijas (por defecto, hoy).

## Novedades v2.3 — planificación de producción
- Pestaña **🏭 Producción**: OT en fabricación (avance por etapas, fin estimado, riesgo) y **aprobadas en cola** ordenadas por fecha límite para iniciar.
- Tipos de tablero con **días de fabricación** (NLAB 3, NAB 8, Celda 15, editables) y **días hábiles** del galpón.
- La app calcula **"iniciar a más tardar"** = fecha compromiso − días de fabricación, y avisa en **Hoy** lo que debe iniciar.
- Al terminar todas las etapas de una OT, ofrece pasarla a PT y **sugiere la siguiente** de la cola.
- Trello: lista **"aprobadas"** como cola; la fecha de vencimiento de la tarjeta se toma como compromiso. Si la tarjeta pasa a "en proceso", la OT inicia sola en la app.
- **Tiempos reales vs estimados** por tipo, con botón para ajustar la estimación.

## Novedades v2.2 — viajes, QR y avisos
- **🚛 Armar viaje**: varias OT verificadas salen juntas con chofer y vehículo; se registra salida y regreso al galpón.
- **🏷 Etiqueta QR** por OT para pegar en el tablero y **📷 Escanear QR** para abrir la OT al instante.
- **Avisos**: salidas, entregas, despachos parciales y OT agendadas para hoy o mañana (llegan mientras la app está abierta o en segundo plano).
- **Trello automático**: revisa las listas al abrir y cada 10 minutos; mueve a PT lo que pasó a "Producto Terminado" y avisa de tarjetas nuevas.
- **Mejora continua**: tiempo promedio por etapa y ranking de causas de retraso (con causas rápidas al reprogramar o hacer parciales).

## Novedades v2.1 — trazabilidad hasta el cliente
- Pantalla **Hoy**: vencidas, en riesgo, para despachar hoy, en ruta, entregadas y mañana.
- Estados **🛣 En ruta** y **✅ Entregada**. Al entregar: quién recibe, cédula, hasta 3 fotos y firma en pantalla.
- **Comprobante** de entrega en imagen para enviar por WhatsApp.
- **Historial** por OT con quién hizo cada cosa y cuánto duró cada etapa (fabricación, en PT, ruta, total).
- Indicadores nuevos: tiempo promedio en ruta y % de entregas con prueba.

## Novedades v2.0 — varios teléfonos conectados
- Datos compartidos en tiempo real con Firebase (proyecto `entregas-pt`). Funciona sin señal y sincroniza al volver.
- Usuarios con nombre y PIN. Roles: **Supervisor** (todo), **Despacho** (tilda ítems y despacha), **Vendedor** (solo ve sus notas y comenta).
- Cada tilde guarda quién la hizo y a qué hora. Las notas PDF se guardan en la nube y se ven desde cualquier teléfono.
- Estado nuevo 🔧 **En fabricación** y botón **Llegó a PT**.
- Importación desde Trello (tablero PLANIFICACION INSELARA): "en proceso" entra como En fabricación y "Producto Terminado" como En PT. Si la tarjeta se mueve en Trello, la app la pasa sola a PT.
- Reglas de seguridad de Firestore en `firestore.rules`.

# Cómo generar el APK

## Paso a paso (igual que con tu app de finanzas)

1. En GitHub crea un repositorio nuevo, por ejemplo `entregas-pt`.
2. Descomprime el ZIP y sube TODO su contenido al repositorio (incluida la carpeta oculta `.github`).
   - Si subes desde la web de GitHub y no ves `.github`, crea a mano el archivo
     `.github/workflows/build-apk.yml` y pega su contenido.
3. Ve a la pestaña **Actions**. El flujo "Generar APK" arranca solo (tarda ~5 minutos).
4. Cuando termine en verde, entra al flujo y descarga **EntregasPT-apk** (sección Artifacts).
5. Descomprime, pasa `app-debug.apk` al teléfono e instálalo (permite "orígenes desconocidos").

Cada vez que cambies `www/index.html` y hagas push, se genera un APK nuevo.
Al instalar la nueva versión encima de la anterior, los datos se conservan.

## Estructura
- `www/index.html` → toda la app (pantallas, lógica y diseño).
- `www/lib/` → lector de PDF (pdf.js), incluido para que funcione sin internet.
- `capacitor.config.json` → nombre e ID de la app.
- `.github/workflows/build-apk.yml` → la "fábrica" que convierte la web en APK.

## Cómo funciona por dentro
- Los datos se guardan en el almacenamiento interno del teléfono (funciona sin internet).
- Estado de cada orden: **Sin fecha** (llegó a PT) → **Agendada** → **Despachada**.
  Si la fecha pasa y no se despachó, queda **Vencida** (en rojo).
- Disponibilidad: defines los días de despacho y el máximo de entregas por día;
  el botón ✨ Sugerir busca el primer día con cupo.
- Cada cambio de fecha queda registrado con su motivo, para el indicador de mejora continua.

## Novedades v1.2
- Lector ajustado al formato real de las notas (ITEM · DESCRIPCIÓN · CANT · UND · PRECIO), probado con NE-2026-10-115.
- Toma de la nota: N° de nota, cliente y vendedor. Si el vendedor no está en tu lista, aparece un botón para agregarlo.

## Novedades v1.1 — nota adjunta e ítems
1. Al crear la OT toca **Subir** y elige el PDF de la nota (o una foto).
2. La app lee la tabla de la nota: busca la fila de títulos (Cant. / Descripción) y saca cada ítem
   con su cantidad. Las líneas extra (por ejemplo medidas de chasis o aleta) se unen al ítem de arriba.
   También llena solo el N° de nota y el cliente si los encuentra.
3. Revisa la lista y **desmarca** lo que no va en esta OT. Lo que dejes fuera te lo ofrece
   para crear otra OT con la misma nota adjunta.
4. Al despachar, abre la OT y **tilda** cada ítem que va saliendo.
   - Todo tildado → se despacha completa.
   - Solo una parte → despacho parcial: pide el motivo y te lleva a reagendar lo pendiente.
5. Si la nota es una foto o un escaneo, queda adjunta pero los ítems se agregan a mano.

Nota: el respaldo copia órdenes, ítems y notas; los PDF adjuntos se quedan guardados en el teléfono.
