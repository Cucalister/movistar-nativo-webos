# Movistar Plus+ nativo para teles LG webOS — guía para construir la tuya con una IA

Esto **no es una app** ni trae código de nadie: es un *skill* (una guía detallada pensada para que la lea una IA como
Claude) con todo lo aprendido al hacerme una interfaz propia de Movistar Plus+ para una tele LG con webOS 5. La app
oficial me resultaba muy lenta navegando; la mía, hecha con Rust + OpenGL, va muchísimo más rápida y reproduce con la
misma calidad que la oficial (Widevine L1 de la propia tele, 1080p en directo y 4K en VOD, sin tocar el DRM).

Inspirado por **PlxNative** y su maravillosa app para Plex: la idea de "interfaz nativa rápida + reproductor de la tele"
viene de ahí.

## Cómo usarlo
1. Necesitas una LG con webOS **rooteada** (Homebrew Channel + SSH) y tu propia cuenta de Movistar Plus+.
2. Dale esta carpeta a tu IA (en Claude: añádela como skill o pégale `SKILL.md` y las referencias) y pídele algo como:

   > "Quiero hacerme una interfaz nativa de Movistar Plus+ para mi tele LG siguiendo este skill. Mi tele es <modelo>,
   > webOS <versión>, ya está rooteada y entro por SSH. Vamos paso a paso: primero el puente y la sesión, luego la
   > portada, luego el reproductor."

3. La IA escribirá el código contigo, adaptado a tu tele y a tu cuenta. Ve probando cada paso en la tele.

## Avisos (léelos)
- **Proyecto personal, no oficial**, sin relación con Telefónica/Movistar ni con LG. **No doy soporte.**
- Úsalo solo con **tu cuenta** y para ver lo que ya pagas. **No se salta ningún DRM** ni marca de agua: la reproducción
  la hace el motor web de la tele con su Widevine, igual que la app oficial. Si algo exige una protección que no puedes
  respetar, la respuesta correcta es abrir la app oficial, no saltársela.
- No copies imágenes, fuentes, textos ni ficheros de la app oficial en tu proyecto; diseña los tuyos.
- Movistar puede cambiar sus servicios cuando quiera y tu versión dejará de funcionar hasta que la adaptes. Ten siempre
  instalada la app oficial como respaldo.
- Rootear la tele y usar clientes no oficiales es bajo tu responsabilidad (garantía, condiciones de uso del servicio).

## Qué hay dentro
- `SKILL.md` — punto de entrada para la IA: objetivos, reglas y el orden de construcción.
- `references/arquitectura.md` — las piezas (app nativa, puente local, reproductor) y cómo se hablan.
- `references/sesion-y-datos.md` — vincular el dispositivo con el código de 6 cifras, renovación, de dónde salen los datos.
- `references/interfaz-nativa.md` — Rust + SDL + OpenGL ES en webOS: compilación, imágenes, rendimiento.
- `references/reproductor-y-vista-previa.md` — reproductor web con Widevine, vídeo debajo de la interfaz, teclas y foco.
- `references/lecciones-y-medidas.md` — comportamientos de webOS descubiertos, cifras medidas y método de trabajo.

Licencia de este texto: CC BY 4.0 (https://creativecommons.org/licenses/by/4.0/deed.es).
