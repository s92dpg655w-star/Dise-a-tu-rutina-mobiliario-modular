# Pruebas en celular antes de compartir

Hazlas en **un iPhone (Safari)** y **un Android (Chrome)**, con el enlace ya publicado y la Sheet conectada. Te toma unos 15 minutos.

## Flujo completo
- [ ] Sin marcar la casilla de consentimiento, "Empezar" muestra un aviso y no avanza.
- [ ] En Perfil, el teclado de la edad es numérico. Si dejas algo vacío, el error señala el campo.
- [ ] En los 14 pasos:
  - [ ] Al tocar una opción, avanza solo al siguiente paso.
  - [ ] La barra de progreso sube.
  - [ ] Los números de abajo permiten regresar a corregir un paso.
  - [ ] "Atrás" regresa paso por paso.
- [ ] El botón **Atrás del celular** (o deslizar para regresar en iPhone) regresa a la pantalla anterior y no saca de la encuesta.

## Ordenar (lo más importante)
- [ ] Solo aparecen los pasos que **no** marcaste como "Nunca", y aparecen revueltos.
- [ ] **Arrastrar con el pulgar** desde el ícono de puntos funciona en toda la lista, incluso llevando un paso del final hasta el inicio. La página debe desplazarse sola cerca de los bordes.
- [ ] Deslizar el dedo sobre el **texto** de una tarjeta hace scroll normal y no arrastra.
- [ ] Mantener presionado el ícono de puntos no abre menús del sistema ni selecciona texto.
- [ ] Los botones ↑ ↓ mueven el paso un lugar y los números #1, #2… se actualizan.
- [ ] Agregar un paso propio lo pone al final. Se puede mover y quitar.
- [ ] Si no mueves nada y tocas "Siguiente", aparece el aviso una sola vez.

## Mueble
- [ ] Al activar módulos, el mueble crece por niveles y los números cambian: pasos cubiertos y alto en cm.
- [ ] Con "Muy poco espacio" y 4 o más módulos, aparecen la línea **LÍMITE** y el aviso amarillo.
- [ ] El panel del mueble se queda visible arriba mientras bajas por los módulos, y no tapa demasiado en una pantalla chica.
- [ ] Por cada módulo activo aparece "¿Lo pondrías en tu cuarto?". Sin responderlo, no deja enviar.

## Envío y resultado
- [ ] Al enviar aparece "Respuesta recibida. ¡Listo!" y en la Sheet aparece **una** fila nueva con todas las columnas.
- [ ] **Prueba sin internet:** activa el modo avión justo antes de "Enviar respuestas". Debe salir "No pudimos enviar…". Al quitar el modo avión, "Reintentar" debe funcionar y la fila no debe duplicarse.
- [ ] "Descargar imagen (PNG)" guarda la imagen. En iPhone puede abrirse una vista previa: mantén presionado y elige "Guardar en Fotos".
- [ ] "Responder de nuevo" empieza una encuesta limpia.

## Accesibilidad y apariencia
- [ ] Con el **modo oscuro** del sistema activado, todo se lee bien.
- [ ] Con *Reducir movimiento* activado (iPhone: Accesibilidad → Movimiento; Android: Accesibilidad → Quitar animaciones), no hay animaciones de entrada.
- [ ] Con letra grande del sistema, los textos no se cortan ni se encima nada.
- [ ] En computadora, con **solo teclado** (Tab, Enter, Espacio y flechas), se puede contestar todo, incluido ordenar con flechas. El foco siempre se ve.

## Al final
- [ ] Borra de la Sheet las filas de prueba.
- [ ] `DEMO_PERMITIDO: false` en `js/config.js` y vuelve a publicar.
