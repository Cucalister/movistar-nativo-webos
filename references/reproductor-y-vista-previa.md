# Reproductor y vista previa

## Reproductor (página web propia)
- App web de la tele con **Shaka Player** y EME: la licencia Widevine la pide el reproductor al servidor de licencias
  de Movistar con la autorización que corresponde (token de licencia en directo; token de contenido en VOD). La ruta de
  vídeo segura de la tele garantiza la misma calidad que la oficial (medido: 1080p y la representación más alta en
  directo, 3840x2160 en VOD 4K, 0 fotogramas perdidos).
- Antes de reproducir, el puente abre la **sesión de visionado** (y la mantiene viva con el intervalo que indique el
  servicio; la cierra al parar). Si el servicio la rechaza, muestra el error y no reproduzcas.
- Respeta las **marcas de agua** que exija el canal o contenido; si no puedes aplicarlas, abre ese contenido en la app
  oficial.
- Diseña tu propia capa de controles (título, barra, avance); no copies los recursos de la oficial.

## Vídeo debajo de la interfaz (grupo de ventanas de webOS)
- webOS deja que una app sea **dueña de un grupo de ventanas** y otra **cliente** en una capa inferior (es lo que usa la
  propia TV en directo con HbbTV). La app nativa crea el grupo y una capa con z negativo (p. ej. z=-1; el hint de SDL de
  LG siempre pone z=+1, así que hay que crearla a mano con el protocolo Wayland de webOS); el reproductor web se declara
  cliente de ese grupo en su `appinfo.json` (además de `noSplashOnLaunch` para que no salga la pantalla de carga).
- La superficie GL de la interfaz con **alfa** (8 bits): donde se limpia transparente se ve el vídeo de debajo.
- **El primer vídeo de una página cliente recién abierta tarda ~15 s** (el navegador espera a que el compositor le dé su
  ventana de vídeo); los siguientes, ~3 s. Solución: **calentar** la página al abrir la app (un canal en silencio que no
  se ve) y **no cerrarla nunca** (~30-50 MB). Durante el calentamiento no toques la página; encola lo que se pida.
  No pongas un vigilante de "sin imagen a los N s" que reinicie el canal durante el calentamiento (lo alarga).
- Si la app dueña se cierra, el sistema cierra también la cliente → Atrás en la portada vuelve a la app anterior.

## Teclas y foco (lo más traicionero)
- **Nunca muevas el foco del teclado entre la interfaz y la página cliente**: una página cliente que gana y pierde el
  foco se descarga. En pantalla completa, la interfaz no se pinta (transparente) y **reenvía las teclas** al puente, que
  las inyecta en la página por el inspector remoto.
- Con el **mando físico**, el compositor a veces entrega Atrás (y el foco) a la página cliente. Fuera de pantalla
  completa, la página debe reenviar Atrás/flechas/OK a la interfaz (vía el puente).
- No intentes "recuperar" el foco desde la app al perderlo: el seguimiento de la app delantera va con retraso y la
  ventana puede quedarse atascada.

## Robustez de red
- Shaka por defecto espera ~30 s por intento: con una conexión colgada el canal no vuelve. Reintentos cortos
  (conexión ~4 s, petición ~10 s, sin progreso ~5 s, varios intentos) y un vigilante propio a ~6 s que reintenta con
  sesión nueva (marcando qué canal es el que ya tiene imagen). Pruébalo cortando la red de verdad (una ruta a una
  puerta de enlace inexistente), no con simulaciones.
- Tiempos medidos de canal: ~3,5-4 s de petición a imagen, de los que ~2,5 s son la tubería de vídeo de la tele
  (igual en la app oficial). Ajustes de Shaka probados sin ganancia apreciable: `rebufferingGoal`,
  `startAtSegmentBoundary`, `autoCorrectDrift`, `defaultPresentationDelay`.

## Vista previa de canal
- Quieto ~1 s sobre un canal de una fila de canales → se reproduce a pantalla completa **debajo** de la interfaz (que
  difumina el fondo); al pasar a otro canal, el anterior sigue hasta que se cumple el segundo del nuevo; al salir de la
  fila o abrir algo, se para. OK sobre el canal: pasa a pantalla completa en el mismo reproductor, sin cambiar de app.
- Mientras se pide la pantalla completa, que la lógica de vista previa no toque nada (si no, la para al instante).
