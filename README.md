# 🧪 Nutriente Exótico: Modelos 3D NeRF en Telegram bots

**Región de origen:** Rusia  
**Cazado el:** 20260730011952  

---

## 📦 Tesoros encontrados en GitHub:
- No se encontraron repositorios relacionados.

---

## 🌍 ¿Por qué es valioso?
Este tema es escaso porque proviene de ecosistemas de desarrollo no occidentales. Contiene aproximaciones únicas a la IA que no están en los papers mainstream de EEUU/Europa.

---

> **Corrección del encabezado original (9 de noviembre de 2026).** Tres afirmaciones de la plantilla del bot que creó el repo no se sostienen:
> 1. **"No se encontraron repositorios relacionados" — falso.** Existen, entre otros: `bmild/nerf` (la implementación original), `nerfstudio-project/nerfstudio`, `graphdeco-inria/gaussian-splatting` y `playcanvas/supersplat`, además del proyecto instant-ngp de NVIDIA.
> 2. **"Región de origen: Rusia" — sin evidencia.** El trabajo fundacional es de Berkeley (NeRF, Nerfstudio), NVIDIA (instant-ngp) e Inria (3D Gaussian Splatting). No se encontró ningún ecosistema ruso detrás del tema.
> 3. **"No están en los papers mainstream de EEUU/Europa" — falso.** NeRF es ECCV 2020, instant-ngp es SIGGRAPH 2022 y 3D Gaussian Splatting es SIGGRAPH 2023. El tema nació en los venues más mainstream que existen.

*Consultado el 9 de noviembre de 2026.*

## 1. Qué es un NeRF, en una frase

Un NeRF (Neural Radiance Field) es una red neuronal que memoriza una escena como una función: le das una posición en el espacio (x, y, z) y una dirección de mirada (θ, φ), y te devuelve el color (r, g, b) y la densidad en ese punto. Con eso se renderiza cualquier vista nueva por integración volumétrica, el mismo principio del render clásico, pero diferenciable, así que la red se entrena comparando sus renders con las fotos reales. Es el artículo de Mildenhall y otros, ECCV 2020.

Dos consecuencias que deciden todo lo demás:

- **Un NeRF no es un archivo 3D.** No hay malla, ni puntos, ni voxels: hay unos pesos de red que, según el README original, ocupan unos 5 MB. Para ver la escena hay que ejecutar la red y renderizar; no se puede abrir como se abre un .obj.
- **Para entrenarlo hay que saber dónde estaba la cámara en cada foto.** Las poses de cámara se calculan antes con structure-from-motion, y la herramienta habitual para eso es COLMAP: sigue siendo la fuente usual de poses para entrenar NeRF y 3D Gaussian Splatting, y sus errores se heredan en todo lo que viene después.

## 2. La cadena: de las fotos a algo que se puede mirar

La cadena completa tiene cuatro eslabones, y cada uno vive en un sitio distinto:

1. **Captura.** Fotos o vídeo dando la vuelta al objeto o a la escena. En el móvil lo hacen Luma AI (iPhone 11 o posterior para la captura AR; ver y subir funcionan desde iPhone X/XR, sin LiDAR) y Polycam (fotogrametría con cualquier cámara, LiDAR en los iPhone Pro para habitaciones y objetos). Ambas procesan en la nube y comparten la escena con un enlace web.
2. **Poses.** COLMAP reconstruye dónde estaba la cámara en cada foto y entrega las poses y una nube de puntos dispersa.
3. **Entrenamiento.** Aquí el campo se partió en dos. El NeRF original era costoso de entrenar y de renderizar. instant-ngp (Müller y otros, NVIDIA, SIGGRAPH 2022) lo bajó a segundos de entrenamiento y decenas de milisegundos de render a 1920×1080 con una codificación hash multirresolución. Y 3D Gaussian Splatting (Kerbl y otros, Inria, SIGGRAPH 2023) cambió la representación: en vez de una red, millones de gaussianas 3D con posición, forma, color y opacidad, con render en tiempo real de al menos 30 fps a 1080p. Nerfstudio (Tancik y otros, Berkeley, SIGGRAPH 2023) es el marco que junta todo: entrena varios métodos, tiene visor web en tiempo real (viewer.nerf.studio) y exporta a vídeo, nube de puntos y malla.
4. **Salida.** O se queda el modelo entrenado (pesos NeRF, ~5 MB, que solo sabe renderizar su escena) o se hornea a algo explícito: nube de puntos (`ns-export pointcloud`), malla por TSDF o por reconstrucción de Poisson (`ns-export tsdf`, `ns-export poisson`), o splats gaussianos en .ply (`ns-export gaussian-splat`).

La salida que elijas decide si lo que llega al chat es un vídeo o una escena que se puede girar.

## 3. Las dos salidas que importan en un chat

- **Vídeo renderizado.** Se define una trayectoria de cámara en el visor y se exporta un MP4. Es lo más compatible que existe: Telegram lo reproduce en línea, no necesita visor 3D ni GPU en el teléfono del que mira. Pierdes la interactividad: el que mira no elige el ángulo.
- **Escena interactiva (splats).** El .ply de gaussianas se abre en un visor web. SuperSplat, de PlayCanvas, es un editor gratuito y de código abierto (MIT) que corre en el navegador: se arrastra el .ply, se limpia, se publica y cualquiera lo explora. Su visor existe como paquete npm (`@playcanvas/supersplat-viewer`) y se puede autoalojar, o se exporta un HTML de visor de un solo archivo. Matiz que importa: SuperSplat 3.0 (8 de septiembre de 2026) es una reescritura solo-WebGPU — Chrome o Edge actuales, Safari 26 o posterior, o Firefox con WebGPU activado — y la rama 2.x (última 2.32.5) queda como respaldo WebGL2.

El NeRF puro, sin hornear, no se sirve en un chat: no hay visor de pesos NeRF en el navegador del usuario. O se renderiza a vídeo o se convierte a splats o a malla.

## 4. Tres vías para servirlo en Telegram

Telegram no tiene visor 3D nativo documentado. Lo que hay son tres vías, con límites medidos:

| Vía | Cómo | Límite verificado |
|---|---|---|
| Vídeo como mensaje | `sendVideo` con el MP4 renderizado | El bot sube hasta 50 MB por archivo (multipart); un humano puede enviar 2 GB, 4 GB con Premium |
| Archivo como documento | `sendDocument` con el .ply, la malla o el modelo | El bot sube hasta 50 MB; por URL, 20 MB (5 MB para fotos); descargar con `getFile`, 20 MB, enlace válido al menos 1 hora |
| Mini App con visor web | Botón que abre una página HTTPS en el WebView de Telegram con el visor de SuperSplat o el enlace de Luma/Polycam | La página debe servirse por HTTPS; el puente es `telegram-web-app.js` (`window.Telegram.WebApp`); se lanza desde el menú del bot, un botón inline o un enlace `t.me/<bot>?startapp` |

Tres notas sobre la tabla:

- **Reutilizar un `file_id` no tiene límite de tamaño.** Una vez que el archivo está en los servidores de Telegram, el bot lo puede reenviar sin volver a subirlo. La primera subida es el cuello de botella, no las siguientes.
- **Servidor Bot API local.** Telegram publica un servidor Bot API propio que se puede autoalojar: sube hasta 2.000 MB y descarga sin límite. Es la única vía documentada para mover archivos grandes con un bot sin pasar por la nube pública.
- **El tamaño manda.** Los modelos preentrenados oficiales del repositorio de 3D Gaussian Splatting pesan 14 GB en conjunto: una escena real entrenada es grande. Un .ply de splats de una escena real no cabe en los 50 MB del bot como documento, así que la vía interactiva real es la Mini App o el enlace web, no el archivo adjunto. El tamaño típico exacto de un .ply por escena es NO SÉ (ver §6).

## 5. Arquitectura mínima de un bot que lo haga

Si alguien quiere construirlo, la pieza mínima es esta, con herramientas reales:

1. El usuario envía fotos o un vídeo al bot. Ojo: lo que llega como foto se comprime; como documento llega intacto, pero el bot solo puede descargar 20 MB por `getFile` salvo servidor local.
2. Un servidor con GPU corre COLMAP para las poses y Nerfstudio (`splatfacto`) para entrenar los splats.
3. Se exporta el .ply con `ns-export gaussian-splat`, o se renderiza el vídeo con la trayectoria de cámara.
4. El bot responde con el vídeo por `sendVideo`, o con un botón que abre la Mini App donde corre el visor de SuperSplat con el .ply servido por HTTPS.
5. Atajo sin entrenar nada propio: el usuario captura con Luma AI o Polycam y el bot solo comparte el enlace web de la escena. La captura y el procesado los hace la app; el bot es solo el mensajero.

El entrenamiento necesita GPU y no ocurre en Telegram: ocurre en un servidor. Lo que corre en el teléfono del que mira es solo el visor.

## 6. Límites y NO SÉ

- **NO SÉ si el WebView de Telegram expone WebGPU en el móvil.** SuperSplat 3.0 lo exige; la rama 2.x usa WebGL2. Si el WebView de Telegram en iOS o Android no expone ni uno ni otro, la Mini App con visor no funciona y solo queda el vídeo o el enlace externo. No se verificó en esta sesión.
- **NO SÉ el tamaño típico de un .ply de splats por escena.** No se midió ninguna escena concreta en esta sesión. Lo único medido es el orden de magnitud: los modelos preentrenados oficiales de 3DGS suman 14 GB.
- **NO SÉ si existe un bot de Telegram en producción que sirva NeRF de forma interactiva.** Buscado el 9 de noviembre de 2026: no se encontró ninguno. Lo único con ese nombre es "Neuro Radiance Field", un proyecto de token ($NERF) cuyas afirmaciones y cifras no se verificaron ni se usaron. No confundir el token con la técnica.
- **NO SÉ si iOS abre un .usdz enviado por un bot en AR Quick Look.** No se verificó en esta sesión.
- **NO SÉ cuánto tarda una ronda completa real** (fotos → poses → entrenar → renderizar → enviar) en un caso concreto. Los números de entrenamiento son del paper en su hardware, no de un despliegue.
- **NO SÉ si la etiqueta "Rusia" del stub corresponde a algún ecosistema real.** No se encontró nada: el trabajo fundacional es de Berkeley, NVIDIA e Inria.
- **Límite honesto del tema.** El NeRF original es una técnica de 2020; lo que se usa hoy para servir escenas en tiempo real es 3D Gaussian Splatting, que ya no es una red neuronal. El nombre del repo dice NeRF, pero la pieza que se puede poner en un chat es un splat.
