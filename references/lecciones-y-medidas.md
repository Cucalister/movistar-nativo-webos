# Lecciones de webOS y medidas

## Comportamientos de webOS 5 descubiertos
- Las apps nativas (SDL) no se pueden precargar ocultas: con `preload` se ponen delante a los pocos segundos.
- Instalar una app web que está abierta falla: ciérrala (y a su dueña de grupo) antes.
- El servicio de captura devuelve negro (o una imagen fija) con vídeo DRM: para verificar vídeo usa el estado de la
  salida de vídeo del sistema y el estado que te dé tu reproductor.
- Reiniciar el compositor deja la pantalla en negro varios segundos: evítalo.
- "Apagar" suele ser suspender: el puente se queda congelado y continúa al encender (no lo tomes por un bloqueo).
- `/var/log/messages` es diminuto y rota enseguida: grábalo con `tail -F` durante una prueba.
- Busybox: sin `date +%N` ni `sleep` decimal; para cronometrar usa `/proc/uptime` y `usleep`.
- Node del sistema antiguo (8 en webOS 5): sin `?.`, `??` ni `Promise.finally`.
- La RAM es justa (1,6 GB con el sistema usando más de 1 GB): la app oficial en memoria ocupa cientos de MB, así que
  el objetivo es no necesitarla. Vigila el consumo del puente y de la página del reproductor.

## Cifras de referencia (medidas en la tele)
- Miniaturas (navegación con caché caliente): media ~60-110 ms, peor caso ~200-400 ms, ~24 fps. La app oficial: media
  ~144 ms, peor ~600 ms y decenas de fotogramas de más de 80 ms en el mismo recorrido.
- Canal: ~3,5-4 s a imagen con la página caliente; vista previa ~4-5 s contando el segundo de espera.
- Calentamiento del reproductor integrado: ~19 s una vez por arranque de la app.
- Calidad: igual que la oficial (misma representación de vídeo, mismos KB por segmento, 0 fotogramas perdidos).

## Método
- Bancos repetibles: mismo recorrido de teclas (p. ej. 6 filas × 5 tarjetas) con caché vacía y caliente; la misma
  medida en la app oficial mediante el inspector remoto para comparar.
- Medir el reproductor desde fuera: engancharse por el inspector a la capa de red de Shaka y contar bytes por
  segmento, resolución decodificada y fotogramas perdidos.
- Un detector de bloqueos en el puente (si el bucle de eventos se para más de ~0,5 s, registrarlo) descubre trabajos
  síncronos caros.
- Varias hipótesis "obvias" no mejoraron nada; no des una optimización por buena sin medirla.
- Paso a paso, con commit tras cada avance y un cuaderno de bitácora: el proyecto es largo y se retoma muchas veces.
