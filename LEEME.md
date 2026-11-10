# Entregas PT — cómo generar el APK

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
