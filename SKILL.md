---
name: movistar-nativo-webos
description: "Guía para construir una interfaz nativa y rápida de Movistar Plus+ en una tele LG webOS rooteada (Rust + OpenGL ES, puente local en Node, reproductor web de la tele con Widevine L1, vista previa de canales bajo la interfaz). Usar cuando el usuario quiera hacerse su propia app nativa de Movistar Plus+ para LG/webOS, adaptarla a su tele o depurarla."
---

# Movistar Plus+ nativo en LG webOS

Objetivo: una interfaz propia de Movistar Plus+ que navegue mucho más rápido que la oficial, **con la misma calidad de
vídeo y audio** y sin depender de tener la app oficial en memoria. Probado en una LG con webOS 5.6 (Cortex-A55, 1,6 GB
de RAM, Node 8 en el sistema). Construye con el usuario, paso a paso, en su tele y con su cuenta.

## Reglas innegociables
1. **El DRM no se toca.** Se reproduce con el motor web de la propia tele (EME + Widevine L1), igual que la app oficial.
   Nada de Widevine L3, Kodi, descifrar ni capturar. Si un camino pierde calidad o exige saltarse algo, se descarta.
2. **Marcas de agua**: si un canal o contenido exige marca (visible o forense) y no puedes aplicarla igual que la
   oficial, no lo reproduzcas: abre la app oficial para ese caso.
3. **Solo la cuenta del usuario** y lo que ya tiene contratado. Nada de compartir sesiones ni automatizar a terceros.
4. **No copies recursos de la app oficial** (imágenes, fuentes, CSS, textos, ficheros de configuración): diseña los tuyos.
5. **No escribas en las particiones del sistema de LG**: todo en `/media/developer`, `/home/root` o `/var`, y el arranque
   con un script de `/var/lib/webosbrew/init.d/`. Quitar el script = tele como estaba.
6. **No actualices el firmware** ni instales la app "Developer Mode" de LG encima del root sin comprobarlo antes.
7. **Mide antes y después** de cada optimización (bancos repetibles); varias ideas "obvias" no mejoran nada.
8. Guarda el trabajo en git tras cada paso que funcione y lleva un cuaderno de bitácora (qué se probó, qué se midió).

## Orden de construcción recomendado
1. **Puente local** (Node, `127.0.0.1`) con la **sesión**: vincular la tele con el código de 6 cifras y renovar sola
   (`references/sesion-y-datos.md`).
2. **Datos**: canales, guía y portada/secciones desde la configuración pública del servicio; listas personales (Seguir
   viendo, Mi lista) con la sesión. Cachear en disco.
3. **Interfaz nativa** (Rust + SDL + GLES2): portada con filas, fichas, guía, buscador (`references/interfaz-nativa.md`).
4. **Reproductor**: página web propia (Shaka Player) con la licencia Widevine; primero como app aparte a pantalla
   completa, luego **integrado** debajo de la interfaz con grupos de ventanas (`references/reproductor-y-vista-previa.md`).
5. **Vista previa** de canales (quieto 1 s sobre un canal → se reproduce de fondo) y pantalla completa sin cambiar de app.
6. **Rendimiento**: imágenes, precargas, evitar bloqueos del puente (`references/lecciones-y-medidas.md`).
7. **Arranque**: el puente se lanza desde `init.d` de Homebrew; la app oficial NO se precarga.

## Herramientas útiles
- Compilación cruzada desde el PC (armv7, ABI gnueabi soft-float con NEON) y copia por `scp`.
- Scripts de pruebas en la tele (busybox: sin `date +%N`, sin `sleep` decimal; usar `/proc/uptime` y `usleep`).
- Inspector remoto de las páginas web de la tele (protocolo de DevTools) para medir el reproductor desde fuera.
- Capturas: el servicio de captura de webOS devuelve negro con vídeo DRM (normal); para ver la interfaz, que la propia
  app vuelque su framebuffer GL a PNG bajo petición (fichero disparador).

## Referencias
- `references/arquitectura.md`
- `references/sesion-y-datos.md`
- `references/interfaz-nativa.md`
- `references/reproductor-y-vista-previa.md`
- `references/lecciones-y-medidas.md`
