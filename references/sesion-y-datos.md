# Sesión y datos

## Sesión: vincular la tele con el código de 6 cifras
- Usa el mismo método que la app oficial para teles: **vinculación de dispositivo por código** (flujo estándar de
  autorización de dispositivo). La tele pide un código, el usuario lo aprueba desde el móvil o el PC en la web de
  Movistar Plus+ y la tele recibe la sesión. El dispositivo aparece en la lista de dispositivos de la cuenta, donde se
  puede quitar cuando se quiera.
- Ventajas frente a un login con usuario y contraseña: **no se guarda la contraseña en la tele**, el dispositivo es
  visible y revocable, y se comporta como un dispositivo más de la cuenta.
- La sesión trae varios tokens con vidas distintas (aprox.: uno principal de 24 h, uno de cabecera para servicios
  personales de 12 h, uno para la licencia del vídeo de 24 h) y, además, un token "clásico" que es el que aceptan los
  servicios antiguos de datos personales. Guarda la sesión en un fichero con permisos 600 y **nunca en git**.
- **Renovación**: la app oficial, al arrancar, pide una sesión nueva presentando la guardada aunque esté caducada, y
  funciona mientras el dispositivo siga vinculado. Haz lo mismo: renueva sola cuando a cualquiera de los tokens le
  quede menos de ~1 h y también al arrancar tras días apagada. No hace falta volver a meter el código.
- Una sola renovación a la vez (portada, reproductor y temporizadores la pedirán a la vez). Registra en el log si la
  renovación fue rechazada: solo entonces hay que volver a vincular.
- Para los servicios clásicos (listas personales, token del CDN, sesión de visionado, diferido) existe documentación
  pública de la comunidad: el addon de Kodi **plugin.video.movistarplus** de Paco8 (GitHub) es una buena referencia de
  qué servicios hay y cómo se llaman.

## Datos de la interfaz
- **Portada y secciones**: la app oficial las construye a partir de una **configuración pública** (menú → submenús →
  módulos, cada uno con su consulta y sus parámetros) y de un directorio de servicios. Replicar eso da las mismas filas,
  en el mismo orden, sin necesidad de la app oficial. Renueva la configuración cada pocas horas y guarda la última.
- Catálogo, canales, guía y fichas: públicos (sin sesión) en su mayoría.
- **Personales** (Seguir viendo, Mi lista, grabaciones, buscador): servicios clásicos con la sesión.
- **Recomendaciones personalizadas y banners** de la portada: van por servicios que exigen una firma propia de la app
  oficial. **No se documenta aquí.** Sin ellas, oculta esas filas (la app oficial también oculta las vacías).
- Banners: además del contenido, cada carrusel indica su tipo (tarjetas altas o anchas) y su título; los de tipo
  "Sección" abren un submenú, los de "Ficha" abren una ficha y los de publicidad no abren nada útil.
- Páginas "Ver todo": un submenú con un solo módulo de colección es un **mosaico** paginado (con orden y filtros); con
  varios módulos es una **página de filas**. Algunos módulos contienen varios carruseles dentro (pestañas): mostrar cada
  carrusel como una fila es una aproximación que funciona bien.
- Imágenes: algunas fichas solo traen variantes "con estado" (p. ej. portada 2x3). Si no hay la variante normal, usa
  esa; si no, la tarjeta sale vacía.
- **Programa en emisión** de las filas de canales: la oficial lo refresca al cambiar de minuto y cada 2 min. Pide solo
  esas filas (no toda la portada) y parchea la copia guardada.

## Caché y refrescos
- Portada en disco (la interfaz arranca con ella al instante) y refresco en segundo plano cada ~15 min.
- Secciones con una caducidad corta (~10 min) y respuesta inmediata desde disco si la red tarda.
- Cuidado con el coste de CPU del puente: un trabajo síncrono de 1 s bloquea también las imágenes y las teclas que pasan
  por él. Filtra antes de hacer trabajo caro y deja respirar al bucle de eventos (`setImmediate`) entre tareas pesadas.
