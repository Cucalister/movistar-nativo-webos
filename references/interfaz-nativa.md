# Interfaz nativa (Rust + SDL + OpenGL ES 2)

## Compilación
- Compilación cruzada desde el PC para `armv7-unknown-linux-gnueabi` contra las librerías de la tele (SDL2, EGL/GLES2,
  libjpeg-turbo): copia esas `.so` de la tele a una carpeta del toolchain para enlazar.
- CPU de una webOS 5 típica: Cortex-A55. `target-cpu=cortex-a55` y `+neon` manteniendo el ABI soft-float del sistema:
  usa la FPU/NEON sin romper el enlace con las librerías de la tele.
- Instala como app de desarrollador (ipk una vez; después basta con copiar el binario y relanzar).

## Estructura que funcionó
- Módulos: portada/filas (`ui`), datos (`data`: parseo del JSON del puente), mosaico "Ver todo" (`grid`), ficha
  (`details`), guía (`epg`), buscador (`search`), perfiles, red (`net`), imágenes (`images`), texto (atlas de glifos),
  grupo de ventanas (`wgroup`).
- Trabajos de red en hilos con resultado sondeado desde el bucle principal (nunca bloquear el bucle de pintado).
- **No repintar si nada cambia**: sin animaciones ni imágenes nuevas, dormir 16 ms. En reposo la app debe estar al 0 %
  de CPU (un elemento oculto que se "animaba" sin parar provocaba repintados continuos).
- Teclas para depurar: que la app lea también un fichero (p. ej. `/tmp/<app>.keys`) con nombres de teclas y que vuelque
  su framebuffer a PNG al tocar otro fichero. Ojo: el mando físico no siempre se comporta igual que las teclas simuladas.

## Imágenes (lo que más se nota)
- **JPEG con la libjpeg-turbo de la tele** (NEON): decodificar directo a RGBA y con **escalado DCT N/8** al tamaño
  final. Un fondo de 1920 px pasó de ~4,5 s a ~0,1 s; una miniatura de ~10 ms a ~3,5 ms. PNG con un decodificador normal.
- **Dos colas**: fondos (un solo hilo que solo atiende el último pedido) y miniaturas (varios hilos).
- **Precarga en memoria** alrededor del foco (fila actual +6 tarjetas, fila de arriba y 3 de abajo) con pocos hilos de
  baja prioridad, y **precarga a disco** en el puente tras cada refresco (primeras tarjetas de cada fila, logos, algunos
  fondos), cediendo el paso a lo que la interfaz pide en ese momento.
- En el puente: conexiones HTTPS reutilizables (keep-alive) y reintento si el CDN cierra una conexión reutilizada.
- **Subir texturas grandes a la GPU por franjas** (~1 MB): el driver Mali paraba ~80 ms un fotograma por cada fondo.
- Mide la latencia "pedir → lista para pintar" y regístrala cada pocos segundos (media y peor caso).

## Navegación
- Animaciones cortas de posición y opacidad; mantener el foco y la columna al refrescar datos en vivo.
- Vista previa: quieto ~1 s sobre un canal → reproducir de fondo (ver `reproductor-y-vista-previa.md`).
- Atrás en la portada: **no cierres la app**, vuelve a la app anterior (si la app se cierra, el sistema cierra también
  el reproductor integrado y el siguiente vídeo vuelve a tardar mucho).
