# Mapa base de código abierto en WGS84 para navegador

## Objetivo

Levantar un mapa base de código abierto, accesible desde un navegador (escritorio y móvil), cuyas coordenadas se expresen en WGS84, y que muestre al usuario las coordenadas WGS84 (latitud, longitud) del cursor.

## Contexto

- El repositorio está vacío: el proyecto parte de cero.
- WGS84 (EPSG:4326) es el sistema de referencia de las coordenadas geográficas (longitud/latitud). Los mapas web con tiles suelen dibujarse en proyección Web Mercator (EPSG:3857), pero las posiciones que el usuario ve son latitud/longitud en WGS84. MapLibre GL JS trabaja internamente con el orden `[longitud, latitud]`, distinto del orden de presentación pedido (latitud, longitud).
- Las decisiones de abajo salen de las respuestas dadas en la conversación.

## Decisiones fijadas

| Tema | Decisión |
|---|---|
| Fuente de datos | Tiles públicos de terceros; sin servidor de tiles propio |
| Proveedor y estilo | OpenFreeMap, estilo Liberty |
| Cobertura | Mundial, con zoom completo |
| Librería de renderizado | MapLibre GL JS (mapa vectorial) |
| Stack y entrega | HTML estándar + JavaScript estático, sin paso de build |
| Origen de MapLibre | cdnjs, versión 6.11.2 (prioriza carga rápida y estabilidad corporativa) |
| CSS de MapLibre | `https://cdnjs.cloudflare.com/ajax/libs/maplibre-gl/6.11.2/maplibre-gl.css` |
| JS de MapLibre | `https://cdnjs.cloudflare.com/ajax/libs/maplibre-gl/6.11.2/maplibre-gl.js` |
| Vista inicial | Centrada en la intersección de Moneda con Ahumada, Santiago Centro, Chile; el zoom debe mostrar la intersección y hasta 6 cuadras alrededor |
| Fallo de carga de tiles o estilo | Se muestra el mensaje "Fuera de Alcance" |
| Verificación de la versión 6.11.2 en cdnjs | Se comprobará más adelante |
| Formato de coordenadas | Latitud, longitud; dos decimales |
| Navegadores | Chrome y Safari, desde la penúltima versión estable de cada uno |
| Dispositivos | Uso en móvil requerido (además de escritorio) |
| Indicador con el cursor fuera del mapa | Muestra un guion |
| Indicador en dispositivos sin cursor (táctiles) | Se muestra un cuadro blanco |
| Navegación | Controles de zoom en pantalla y rueda del mouse |
| Función adicional | Mostrar las coordenadas WGS84 del cursor |

## Lo que sí y lo que no

**Sí:**
- Un mapa base vectorial con MapLibre GL JS y estilo Liberty de OpenFreeMap, con cobertura mundial.
- Una página HTML estándar que se sirve como archivo estático, sin build.
- Un indicador visible con las coordenadas WGS84 (latitud, longitud) con dos decimales.
- Controles de zoom en pantalla y zoom con la rueda del mouse.
- Funcionamiento en Chrome y Safari, también en móvil.

**No (en esta tarea):**
- Servidor de tiles propio, autoalojamiento de tiles (p. ej. PMTiles) ni modo offline.
- Mapa raster con Leaflet u OpenLayers.
- Proyecto con npm/Vite/TypeScript o contenedor Docker.
- Brújula, barra de escala y carga de capas GeoJSON propias: no fueron seleccionadas como funciones requeridas.
- Limitación de la navegación a un país, región o ciudad (la vista inicial es Santiago Centro, pero el mapa cubre el mundo).

## Criterios de aceptación (EARS)

1. Cuando el usuario abra la página en Chrome o Safari, el sistema deberá mostrar un mapa base vectorial con el estilo Liberty de OpenFreeMap, renderizado con MapLibre GL JS.
2. Cuando la página termine de cargar, el sistema deberá mostrar el mapa centrado en la intersección de Moneda con Ahumada, en Santiago Centro, Chile, con un zoom que muestre la intersección y hasta 6 cuadras alrededor.
3. El sistema deberá permitir navegar el mapa por todo el mundo y a través de todos los niveles de zoom que ofrezca el estilo elegido.
4. El sistema deberá ofrecer controles de zoom en pantalla y deberá permitir acercar y alejar con la rueda del mouse.
5. Cuando el usuario mueva el cursor sobre el mapa, el sistema deberá mostrar la latitud y la longitud del punto bajo el cursor, en ese orden, en grados decimales WGS84 con dos decimales.
6. Mientras el cursor esté fuera del área del mapa, el sistema deberá mostrar un guion en el indicador de coordenadas.
7. Cuando el usuario cambie el zoom o desplace el mapa con el cursor quieto sobre él, el sistema deberá actualizar el indicador con la coordenada correcta de la posición actual del cursor.
8. El sistema deberá funcionar en dispositivos móviles con Chrome y Safari, permitiendo navegar y hacer zoom con gestos táctiles y con los controles en pantalla.
9. Mientras el dispositivo no tenga cursor (pantalla táctil), el sistema deberá mostrar el indicador de coordenadas como un cuadro blanco.
10. El sistema deberá funcionar en la penúltima versión estable de Chrome y de Safari, o en una posterior.
11. El sistema deberá funcionar servido como archivos estáticos, con MapLibre GL JS 6.11.2 cargado desde cdnjs y sin paso de compilación ni dependencias instaladas con un gestor de paquetes.
12. Si las tiles o el estilo no están disponibles o falla su carga, entonces el sistema deberá mostrar el mensaje "Fuera de Alcance" [ubicación del mensaje: ver pregunta abierta 2].
13. El sistema deberá mostrar la atribución que exigen OpenFreeMap y los datos que usa su estilo.

## Cómo se verifica

- **Servir y abrir:** servir el directorio con cualquier servidor de archivos estáticos y abrir la página en Chrome y en Safari (criterios 1, 10 y 11).
- **Vista inicial:** al cargar, confirmar que el mapa está centrado en la intersección de Moneda con Ahumada, que se ven la intersección y hasta 6 cuadras alrededor (criterio 2). Las coordenadas exactas de esa intersección se toman de una fuente verificable al implementar.
- **Navegación:** recorrer distintas regiones del mundo y acercar/alejar hasta el zoom máximo disponible (criterio 3).
- **Controles de zoom:** probar los botones en pantalla y la rueda del mouse (criterio 4).
- **Coordenadas:** colocar el cursor sobre puntos de referencia conocidos (p. ej. la intersección de ecuador y meridiano de Greenwich, y ciudades con coordenadas publicadas) y comparar con los valores esperados en WGS84, con dos decimales y en orden latitud, longitud (criterios 5 y 7).
- **Cursor fuera del mapa:** sacar el cursor del área del mapa y comprobar que el indicador muestra un guion (criterio 6).
- **Móvil:** probar en un teléfono o en el modo de dispositivo de Chrome y, para Safari, en un iPhone o iPad real o en Safari en macOS con simulación de dispositivo; comprobar gestos y controles (criterio 8) y que el indicador muestre el cuadro blanco al no haber cursor (criterio 9).
- **Versiones de navegador:** comprobar en la penúltima versión estable de Chrome y de Safari, y en la última (criterio 10).
- **Sin build y CDN:** confirmar que el repositorio no requiere `npm install` ni compilación y que las URL de cdnjs de MapLibre 6.11.2 (CSS y JS) responden y cargan la librería. Esa comprobación no se pudo hacer al redactar la spec (la red del entorno bloqueó el acceso a cdnjs); se acordó hacerla más adelante (criterio 11).
- **Fallo de carga:** bloquear la red hacia OpenFreeMap con las herramientas del navegador y comprobar que aparece "Fuera de Alcance" (criterio 12).
- **Atribución:** revisar que la atribución visible coincida con lo que exige OpenFreeMap y con la de sus datos (criterio 13).

## Preguntas abiertas

1. **Cuadro blanco en dispositivos sin cursor:** ¿el cuadro blanco es el indicador vacío (sin texto) o debe mostrar algo dentro (p. ej. la coordenada del centro del mapa)?
2. **Mensaje "Fuera de Alcance":** ¿dónde se muestra (sobre el mapa, en el indicador de coordenadas u otro lugar) y desaparece si las tiles vuelven a cargar?
3. **Integridad de la CDN (SRI):** ¿se agrega verificación de integridad (atributos `integrity` y `crossorigin`) a los enlaces de cdnjs?
4. **Hosting y licencia del proyecto:** ¿dónde se publicará la página y bajo qué licencia se distribuirá el código?
