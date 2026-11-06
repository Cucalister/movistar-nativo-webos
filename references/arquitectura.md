# Arquitectura

Tres piezas, todas en la tele, más la app oficial instalada solo como respaldo:

| Pieza | Tecnología | Qué hace |
|---|---|---|
| **App nativa** (interfaz) | Rust + SDL2 + OpenGL ES 2 (las librerías de la propia tele) | Portada, secciones, fichas, guía, buscador, perfiles. No reproduce: pinta y pide datos al puente |
| **Puente** | Node.js del sistema (Node 8 en webOS 5), HTTP solo en `127.0.0.1` | Sesión, peticiones a Movistar, caché en disco de datos e imágenes, sesiones de visionado, control del reproductor |
| **Reproductor integrado** | App web (HTML + Shaka Player) con EME/Widevine L1 de la tele | Vídeo a pantalla completa y vista previa **debajo** de la interfaz (grupo de ventanas) |

## Por qué así
- La interfaz web oficial es lenta en la CPU de la tele; una interfaz nativa con GL pinta en milisegundos.
- El vídeo con DRM solo se puede reproducir con la calidad de la oficial a través del motor web de la tele (Widevine L1,
  ruta de vídeo segura). Por eso el reproductor es una página web y no parte de la app nativa.
- Separar el puente permite cachear, precargar y renovar la sesión aunque la interfaz esté cerrada, y mantener la app
  nativa sencilla (solo HTTP local + JSON).

## API local del puente (ejemplo de diseño)
- Datos: `/home` (portada), `/section?i=` (secciones del menú), `/submenu?id=` ("Ver todo": filas o mosaico),
  `/browse?id=&cp=&start=` (páginas de mosaico con orden y filtros), `/details?id=` (ficha), `/related?id=`,
  `/search?q=`, `/epg?from=` (guía), `/live?sec=` (programa en emisión de las filas de canales), `/img?u=` (imágenes
  cacheadas).
- Reproducción: `/play/channel?code=`, `/play/content?id=`, `/preview/start?code=`, `/preview/stop`, `/preview/full`,
  `/preview/status`; el reproductor avisa al puente de su estado (`/preview/state?...`).
- Teclas: la interfaz y el reproductor se reenvían teclas por el puente (ver `reproductor-y-vista-previa.md`).
- Diagnóstico: `/status`, y un registro con hora en `/tmp/<puente>.log`.

## Disco y arranque
- Código y datos en `/home/root/<proyecto>/` (puente, datos, caché de imágenes con límite de tamaño) y la app nativa
  instalada como app de desarrollador en `/media/developer/apps/usr/palm/applications/<tu.app.id>/`.
- El puente arranca desde un script en `/var/lib/webosbrew/init.d/` (lo ejecuta Homebrew Channel al arrancar). Ojo:
  "apagar" la tele suele ser suspender; los init.d solo corren tras un arranque real.
- La app oficial NO se precarga (en la tele va corta de RAM y ocupa cientos de MB). Queda instalada como respaldo.

## Modos y vuelta atrás
- Conviene un "interruptor" por fichero para cada cambio grande (p. ej. modo directo sí/no, sesión nueva/antigua):
  crear o borrar un fichero y reiniciar el puente, sin recompilar.
- Antes de sustituir un fichero en la tele, copia de seguridad con sufijo (`*.bak_<cambio>`).
