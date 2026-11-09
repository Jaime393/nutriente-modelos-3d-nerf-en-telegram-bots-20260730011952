# Fuentes — Modelos 3D NeRF en Telegram bots

Consultado el 9 de noviembre de 2026. Cada fuente dice qué afirma; nada fuera de esta lista sostiene un número del README.

## Primarias (papers y documentación oficial)

1. **Mildenhall, Srinivasan, Tancik, Barron, Ramamoorthi y Ng, 2020 — "NeRF: Representing Scenes as Neural Radiance Fields for View Synthesis", ECCV 2020 (oral, Best Paper Honorable Mention), arXiv:2003.08934.**
   - README original del repo: https://raw.githubusercontent.com/bmild/nerf/master/README.md
   - Afirma: la escena es una función 5D (posición + dirección → color y densidad) representada por un MLP; el render es volumétrico y diferenciable; los pesos ocupan ~5 MB; ECCV 2020.
   - Resumen de la técnica: https://ai-scholar.tech/en/articles/nerf/nerf

2. **Müller, Evans, Schied y Keller, 2022 — "Instant Neural Graphics Primitives with a Multiresolution Hash Encoding", ACM Trans. Graph. 41(4), SIGGRAPH 2022, arXiv:2201.05989.**
   - https://arxiv.org/abs/2201.05989?context=cs
   - Afirma: codificación hash multirresolución; entrenamiento de primitivas de alta calidad en cuestión de segundos; render en decenas de milisegundos a 1920×1080; premio Best in Show de SIGGRAPH 2022.

3. **Kerbl, Kopanas, Leimkühler y Drettakis, 2023 — "3D Gaussian Splatting for Real-Time Radiance Field Rendering", ACM Trans. Graph. 42(4), SIGGRAPH 2023, arXiv:2308.04079.**
   - https://arxiv.org/abs/2308.04079
   - Afirma: la escena son gaussianas 3D con posición, forma, color y opacidad; render en tiempo real (≥30 fps) a 1080p en escenas completas; la figura 1 muestra entrenamientos de 6 y 51 minutos frente a 48 h de Mip-NeRF360.
   - Modelos preentrenados oficiales (14 GB en conjunto) y datos COLMAP (650 MB), listados en el archivo del repo: https://archive.org/details/github.com-graphdeco-inria-gaussian-splatting_-_2023-08-23_09-13-40

4. **Tancik, Weber, Ng, Li, Yi, Kerr, Wang, Kristoffersen, Austin, Salahi, Ahuja, McAllister y Kanazawa, 2023 — "Nerfstudio: A Modular Framework for Neural Radiance Field Development", SIGGRAPH '23, arXiv:2302.04264.**
   - https://ar5iv.labs.arxiv.org/html/2302.04264
   - Afirma: marco modular con visor web en tiempo real (https://viewer.nerf.studio/) y exportaciones a vídeo, nube de puntos y malla (TSDF y Poisson con textura).
   - Comandos de exportación (`ns-export pointcloud/poisson/tsdf/gaussian-splat`): https://deepwiki.com/nerfstudio-project/nerfstudio/9.1-export-formats
   - Lanzado en octubre de 2022 por estudiantes del laboratorio KAIR de Berkeley; README: https://raw.githubusercontent.com/robo-alex/nerfstudio/main/README.md (copia espejo del README oficial).

5. **Telegram, documentación oficial — Mini Apps (Web Apps).**
   - https://core.telegram.org/api/bots/webapps
   - Afirma: una Mini App es una página web (HTML, CSS, JavaScript) servida por HTTPS que Telegram abre en un WebView en todas las plataformas; el puente es `telegram-web-app.js` (`window.Telegram.WebApp`); se lanza desde el menú del bot, botón inline o enlace directo.

6. **Telegram Bot API — límites de archivos (referencia oficial, verificada por un índice independiente el 20 de agosto de 2026).**
   - Índice con la cita exacta de cada límite y su enlace a la documentación oficial: https://www.conferbot.com/limits/telegram
   - Afirma: subida multipart 50 MB (10 MB fotos); envío por URL 5 MB fotos y 20 MB resto; descarga con `getFile` 20 MB, enlace válido al menos 1 hora; servidor Bot API local 2.000 MB de subida y descarga sin límite; reutilizar un `file_id` no tiene límite de tamaño.
   - La redacción oficial ("Bots can currently send files of any type of up to 50 MB in size") reproducida en: https://docs.rs/telegram-bot-api/latest/telegram_bot_api/methods/struct.SendDocument.html

## Secundarias (verificadas, marcadas como tales)

7. **Luma AI — captura 3D con NeRF y Gaussian Splatting (revisión 2026).**
   - https://fast.io/resources/luma-ai-review-2026/
   - Afirma: captura con iPhone 11 o posterior, sin LiDAR; procesado en la nube; escenas compartibles por enlace web; exportaciones USDZ, glTF, OBJ y formatos nativos NeRF/splat; Genie (texto a 3D) dado de baja el 1 de enero de 2026.
   - Guía de uso (2023): https://www.arshopia.com/p/nerf-from-2d-photos-to-a-drone-footage

8. **Polycam — escáner 3D para móvil (revisión con datos comprobados el 29 de agosto de 2026).**
   - https://3dtoolwiki.com/tools/polycam
   - Afirma: LiDAR en el dispositivo (iPhone Pro/iPad Pro), fotogrametría y splats en la nube; el plan gratuito exporta solo GLTF, con tope de 150 imágenes o 3 minutos de vídeo; compartir por enlace con visor web.
   - Reseña 2026 (visor web sin app para el receptor; modo foto sin LiDAR): https://aisotools.com/blog/polycam-review-2026

9. **SuperSplat — editor de Gaussian Splats en el navegador (documentación oficial de PlayCanvas).**
   - https://developer.playcanvas.com/user-manual/supersplat/
   - Afirma: editor gratuito en el navegador; el visor es el paquete npm `@playcanvas/supersplat-viewer`; se puede exportar un HTML de visor de un solo archivo y autoalojar; la versión 3.0 exige navegador con WebGPU (Chrome o Edge actuales, Safari 26 o posterior, Firefox con WebGPU activado).
   - Historial de versiones (3.0.0 el 8 de septiembre de 2026, reescritura solo-WebGPU; rama 2.x preservada en 2.32.5 como respaldo WebGL2): https://radiancefields.com/platforms/supersplat

10. **COLMAP como fuente de poses para NeRF y 3DGS.**
    - https://blog.shyandram.dev/en/learn/deep-learning/3d-reconstruction/
    - Afirma: NeRF y 3DGS se entrenan con poses calculadas por structure-from-motion como COLMAP; el artículo NeRF toma las poses de sus escenas reales de COLMAP; 3DGS inicializa sus gaussianas a partir de los puntos SfM; COLMAP sigue siendo la fuente usual de poses y sus errores se heredan.

11. **Límites de archivos de Telegram para usuarios (contraste con los bots).**
    - https://fast.io/resources/telegram-file-size-limit/
    - Afirma: un usuario puede enviar archivos de hasta 2 GB, 4 GB con Premium; los bots están limitados a 50 MB de subida y 20 MB de descarga en el servidor público.

## Visto y no usado

12. **"Neuro Radiance Field" ($NERF), proyecto de token — NO es fuente técnica.**
    - https://www.coinbase.com/en-br/converter/nerf/xof
    - Es lo único que devuelve la búsqueda de "Telegram bot + NeRF": un token de criptomoneda que promete un bot de Telegram para generar escenas 3D. Sus afirmaciones y cifras no se verificaron ni se usaron. Se cita solo para documentar la confusión de nombres: el token no es la técnica.
