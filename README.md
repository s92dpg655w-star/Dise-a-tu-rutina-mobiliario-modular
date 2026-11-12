# Validación de rutina de cierre de día

Herramienta web para validar con usuarios reales los 14 pasos supuestos del Service Blueprint y los 8 módulos del mueble.

Son archivos estáticos (HTML, CSS y JavaScript), sin framework ni proceso de build.

- **`index.html`**: el cuestionario para participantes. Tiene 7 pantallas y toma unos 5 minutos.
- **`resultados.html`**: el análisis para el equipo. Lee la Google Sheet.

## Estructura

```
validacion-rutina/
├── index.html                  Cuestionario
├── resultados.html             Análisis
├── css/estilos.css             Estilos (claro/oscuro, accesibles)
├── js/
│   ├── config.js               ← URL del Apps Script, clave, modo demo (lo único que editas para conectar)
│   ├── data.js                 ← Pasos, módulos, preguntas y textos (edita aquí el contenido)
│   ├── fila.js                 Convierte respuestas en una fila de la Sheet
│   ├── app.js                  Lógica del cuestionario
│   ├── ordenar.js              Arrastrar y soltar + teclado
│   ├── mueble.js               Dibujo del mueble (SVG y PNG)
│   ├── envio.js                Envío y respaldo en el dispositivo
│   └── resultados.js           Cálculos del análisis
├── datos-prueba/
│   └── respuestas-ficticias.js 5 respuestas FICTICIAS para probar resultados.html
├── apps-script/Codigo.gs       Código para pegar en Google Apps Script
├── PRUEBAS-CELULAR.md          Lista de pruebas antes de compartir
└── README.md
```

## Decisiones de diseño

- **Perfil.** "Espacio" y "¿Rentas o te mudas seguido?" son dos preguntas separadas. El espacio fija el límite de niveles del mueble: muy poco = 3, pared pequeña = 5, amplio = 8. La movilidad queda como segmento propio en el análisis.
- **Orden revuelto.** En "Ordena tu noche" los pasos aparecen en orden aleatorio, no en el del blueprint. Así no se sugiere la respuesta.
  - Si alguien no mueve nada, se le pregunta una vez si ese es su orden.
  - La columna `movimientos_orden` permite detectar a quien no ordenó.
- **Una sola lista.** Los pasos de la mañana (13 y 14) van en la misma lista libre que los de la noche.
- **Arrastre sin librerías.** Usa Pointer Events y se toma desde la agarradera de puntos, para que el resto de la tarjeta deje hacer scroll con el dedo. Cada tarjeta tiene botones ↑ ↓, y la agarradera responde a las flechas del teclado.
- **Nada se pierde.** Antes de enviar, la respuesta se guarda en el dispositivo (`localStorage`). Solo se borra de ahí cuando la Sheet confirma que la recibió. Si falla el envío, aparecen "Reintentar" y "Descargar JSON".
- **Varias personas en un dispositivo.** El botón "Responder de nuevo" sirve para encuestar a varias personas con un mismo celular o tableta.
- **Sin rastreo.** No hay cookies ni analíticas, y no se pide nombre ni contacto. Las únicas peticiones externas son a Google Fonts y al Apps Script.

---

## 1. Probarlo localmente

Necesitas un servidor local, porque algunos navegadores bloquean scripts abiertos con `file://`.

**Opción A: Python (ya viene en Mac y Linux):**
```bash
cd validacion-rutina
python3 -m http.server 8000
```
Luego abre `http://localhost:8000` en el navegador.

**Opción B: VS Code.** Instala la extensión *Live Server*, haz clic derecho en `index.html` y elige "Open with Live Server".

**Probar en tu celular en la misma red wifi:**
1. Averigua la IP de tu computadora (por ejemplo, `192.168.1.20`).
2. En el celular abre `http://192.168.1.20:8000`.

**Qué esperar sin el Apps Script configurado:**
- La pantalla final dirá "Modo de prueba: no hay hoja de respuestas conectada".
- La respuesta queda guardada en el dispositivo y se ofrece para descargar.
- Para ver el análisis con datos ficticios abre `http://localhost:8000/resultados.html?demo=1`.

## 2. Publicar el Apps Script (Google Sheet)

1. Crea una Google Sheet nueva en tu cuenta, por ejemplo "Validación rutina – respuestas".
2. Ve a **Extensiones → Apps Script**.
3. Borra todo lo que haya en `Código.gs` y pega el contenido de `apps-script/Codigo.gs`.
4. En la línea `var CLAVE_LECTURA = 'cambia-esta-clave';` pon una clave tuya, por ejemplo `rutina-equipo-7k2`. Guarda con el ícono de disco.
5. *(Opcional pero recomendado)* Prueba que el script puede escribir:
   - Selecciona la función `probarEscritura` y dale **Ejecutar**.
   - Google pedirá permisos: elige tu cuenta → "Configuración avanzada" → "Ir a … (no seguro)" → **Permitir**. Es tu propio script.
   - Verás una pestaña "Respuestas" con una fila `PRUEBA-…`. **Bórrala.**
6. Haz clic en **Implementar → Nueva implementación**.
   - En el engrane de "Tipo", elige **Aplicación web**.
   - Descripción: "v1".
   - *Ejecutar como:* **Yo**.
   - *Quién tiene acceso:* **Cualquier usuario**. Es necesario para que los participantes puedan enviar sin iniciar sesión.
7. Haz clic en **Implementar** y copia la **URL de la aplicación web**. Termina en `/exec`.
8. Revisa que funciona: pega la URL en el navegador agregando `?accion=ping` al final. Debe responder `{"ok":true,...}`.
9. Abre `js/config.js` y pega:
   ```js
   URL_APPS_SCRIPT: 'https://script.google.com/macros/s/XXXXXXXX/exec',
   CLAVE_LECTURA: 'rutina-equipo-7k2',   // la misma del paso 4
   ```

**Notas importantes:**
- **Si cambias el código del Apps Script después**, ve a *Implementar → Gestionar implementaciones → ✏️ → Versión: Nueva versión → Implementar*. La URL se mantiene. Si haces una *nueva implementación*, la URL cambia.
- Los encabezados se crean solos con el primer envío. Si en el futuro agregas un dato, la columna nueva se añade al final sin borrar nada.
- Los envíos repetidos de la misma persona (mismo `id_participante`) se ignoran.
- La columna `json_completo` guarda la respuesta íntegra como respaldo.
- **Privacidad:** cualquiera con la URL `/exec` podría enviar filas basura, y leer requiere la clave. No publiques la clave en redes. Como el código del sitio es público, la clave también lo es para quien lo inspeccione. Por eso se piden cero datos personales.

## 3. Publicar el sitio

No hay nada que compilar: esta carpeta ya es la página web. En cualquier opción, `index.html` debe quedar en la **raíz** (no dentro de otra carpeta).

**Vercel (desde GitHub, sin instalar nada):**
1. Entra a <https://github.com/new>, crea un repositorio (por ejemplo `validacion-rutina`) y dale **Create repository**.
2. Haz clic en **uploading an existing file**. Abre la carpeta `validacion-rutina` en tu computadora, selecciona **todo lo que hay adentro** (`index.html`, `resultados.html`, `vercel.json` y las carpetas `css`, `js`, `datos-prueba`, `apps-script`) y arrástralo. Luego **Commit changes**.
3. Entra a <https://vercel.com>, inicia sesión con GitHub y ve a **Add New… → Project**.
4. Elige tu repositorio y haz clic en **Import**.
5. Deja la configuración así:
   - Framework Preset: **Other**
   - Root Directory: `./`
   - Sin Build Command
6. Haz clic en **Deploy**. En unos segundos tendrás tu enlace, por ejemplo `https://validacion-rutina.vercel.app`.
   - Cuestionario: `https://validacion-rutina.vercel.app/`
   - Resultados: `https://validacion-rutina.vercel.app/resultados.html`
7. Cada vez que edites un archivo en GitHub (por ejemplo `js/config.js` con la URL del Apps Script), Vercel vuelve a publicar solo.

**Vercel (con la terminal):**
```bash
npm i -g vercel
cd validacion-rutina
vercel          # primera vez: responde las preguntas, acepta los valores sugeridos
vercel --prod   # publica la versión final
```

**GitHub Pages:**
1. Crea un repositorio y sube el contenido de la carpeta `validacion-rutina/`. `index.html` debe quedar en la raíz.
2. Ve a *Settings → Pages → Build and deployment → Source: Deploy from a branch*, elige `main` y la carpeta `/ (root)`. Guarda.
3. En 1–2 minutos el sitio queda en `https://TU-USUARIO.github.io/NOMBRE-REPO/`.

**Netlify (más rápido):**
1. Entra a <https://app.netlify.com/drop>.
2. Arrastra la carpeta `validacion-rutina` completa. Te da una URL al instante.
3. Para cambiar el nombre: *Site configuration → Change site name*.

**Antes del trabajo de campo real:**
- [ ] `URL_APPS_SCRIPT` y `CLAVE_LECTURA` configurados, y un envío de prueba visto en la Sheet (luego borra esa fila).
- [ ] `DEMO_PERMITIDO: false` en `js/config.js`. Así `?demo=1` deja de cargar datos ficticios.
- [ ] Hiciste la lista de `PRUEBAS-CELULAR.md`.

## 4. Compartir con participantes

- Comparte solo el enlace del **cuestionario** (`…/index.html` o la raíz del sitio). **No compartas `resultados.html`.**
- Mensaje sugerido:
  > Hola 👋 Estoy haciendo un proyecto de Diseño de Servicios sobre cómo cerramos el día antes de dormir. ¿Me ayudas con una encuesta anónima de 5 minutos? No pide tu nombre ni datos de contacto. [enlace]
- Para encuestar en persona con un solo dispositivo, al terminar toca **"Responder de nuevo (otra persona en este dispositivo)"**.
- Si alguien ve el aviso de "No pudimos enviar", pídele que toque **Reintentar**, o que te mande el JSON descargado. Puedes cargarlo en `resultados.html` con **Cargar respaldos JSON**.

## 5. Ver los resultados

- Abre `resultados.html` (en tu sitio o en local). Lee la Sheet con la clave de `config.js`.
- **Actualizar** vuelve a leer la Sheet.
- **Cargar respaldos JSON** suma respuestas descargadas de dispositivos que no pudieron enviar. Los duplicados se ignoran.
- Botones para descargar:
  - CSV · resumen por paso
  - CSV · módulos
  - CSV · respuestas completas

  Abren bien en Excel y Google Sheets.
- **Modo demo:** `resultados.html?demo=1` carga 5 respuestas **FICTICIAS** (ids `FICTICIA-01…05`) con una banda rayada de aviso.
  - Los CSV del demo llevan el prefijo `DEMO-FICTICIO-`.
  - En modo real, cualquier fila con id `FICTICIA-…` se excluye automáticamente.

**Cómo leer el análisis:**
- **Semáforo por paso**, según el % que lo hace (siempre + a veces):
  - Verde: 70 % o más
  - Amarillo: 40–69 %
  - Rojo: menos de 40 %

  Los umbrales se cambian en `config.js`.
- **Posición relativa:** 0 % es el inicio de la noche y 100 % el final. Hace falta porque cada persona ordena una cantidad distinta de pasos, así que el lugar absoluto no se puede comparar directamente.
- **Coincidencia con el blueprint (Spearman):** 1 significa el mismo orden que suponían; cerca de 0, sin relación.
- **Aviso de pocas respuestas:** aparece si hay menos de 10 respuestas. Con n pequeño, reporta los resultados como *señales* y no como *validación*.

## Editar contenido

Todo el texto, los pasos, los módulos y las opciones están en `js/data.js`.

- **Sí puedes** cambiar textos libremente, aunque ya hayas empezado el trabajo de campo.
- **No cambies** `id` ni `valor` después de empezar. El análisis y las columnas dependen de ellos.
- **Si agregas un módulo:** dale un `id` nuevo, su lista de `pasos` y un `corto` breve. El `corto` es el texto que se ve dentro del mueble.
