# ImgTOsvg Versión CLI
Genera imágenes de pintura por números (vectorizadas con SVG) a partir de cualquier imagen de entrada.

## Demostración

Pruébalo [aquí](https://acrohs.github.io/DavidP_imgTOsvg/ )

### Versión CLI

La versión CLI es una aplicación de nodo autónoma que realiza la conversión a partir de argumentos, por ejemplo:
```
paint-by-numbers-generator-win.exe -i input.png -o output.svg
```
Puedes cambiar la configuración en settings.json u opcionalmente especificar un settings.json específico con el argumento `-c path_to_settings.json`.

La configuración contiene principalmente las mismas configuraciones en la versión web:
- randomSeed: la semilla aleatoria para elegir los puntos de partida iniciales del algoritmo de agrupamiento de k-medias. Esto garantiza que se generen los mismos resultados cada vez.
- kMeansNrOfClusters: la cantidad de colores a los que se cuantificará la imagen
- kMeansMinDeltaDifference: la distancia delta del umbral que se debe alcanzar antes de detenerse. Tener un valor mayor acelerará la agrupación, pero puede generar agrupaciones subóptimas. Valor predeterminado: 1
- kMeansClusteringColorSpace: el espacio de color en el que se aplicará la agrupación
- kMeansColorRestrictions: especifique qué colores se deben usar. Una matriz de valores rgb (como matriz de números) o nombres de colores (referencia a alias de colores). Si no se especifican colores, no se aplican restricciones. Útil si solo tiene algunos colores de pintura a mano.
- colorAliases: mapa de claves/valores donde las claves son los nombres de los colores y los valores son los colores rgb (como matriz de números). Puede usar los nombres de los colores en las restricciones de color anteriores. Los nombres también se mencionan en el json de salida que le indica qué porcentaje del área es de ese color específico.
```
"colorAliases": {
"A1": [ 0, 0, 0 ],
"A2": [ 255, 0, 0 ],
"A3": [ 0, 255, 0 ],
}
```
- removeFacetsSmallerThanNrOfPoints: elimina cualquier faceta que sea más pequeña que la cantidad de píxeles indicada. Si se reduce el valor, se crearán resultados más detallados, pero puede resultar mucho más difícil pintarlos debido a su tamaño.
- removeFacetsFromLargeToSmall (true/false): de mayor a menor evitará que los límites deformen las formas porque las facetas más pequeñas actúan como puntos de anclaje de borde, pero pueden ser considerablemente más lentas
- maximumNumberOfFacets: si hay más facetas que el número máximo dado, sigue eliminando las facetas más pequeñas hasta que se alcance el límite

- nrOfTimesToHalveBorderSegments: reducir la cantidad de puntos en un segmento de borde (usando la reducción de wavelets de Haar) suavizará más la curva cuadrática, pero con una pérdida de detalle. Un segmento (borde compartido con una faceta) siempre conservará su punto de inicio y final.

- narrowPixelStripCleanupRuns: la limpieza de píxeles angostos elimina franjas de filas de un solo píxel, lo que haría que algunas facetas tengan algunos segmentos de borde que son demasiado angostos para ser útiles. La eliminación de facetas pequeñas puede introducir nuevas franjas de píxeles angostos, por lo que esto se repite en algunas ejecuciones iterativas.

- resizeImageIfTooLarge (verdadero/falso): si es verdadero y la imagen de entrada es más grande que las dimensiones dadas, se redimensionará para ajustarse, pero mantendrá su proporción. - resizeImageWidth: restricción de ancho
- resizeImageHeight: restricción de alto

También hay perfiles de salida que puedes definir para generar el resultado en svg, png, jpg con configuraciones específicas, por ejemplo:
```
"outputProfiles": [
{
"name": "full",
"svgShowLabels": true,
"svgFillFacets": true,
"svgShowBorders": true,
"svgSizeMultiplier": 3,
"svgFontSize": 50,
"svgFontColor": "#333",
"filetype": "png"
},
{
"name": "bordersLabels",
"svgShowLabels": true,
"svgFillFacets": false,
"svgShowBorders": true,
"svgSizeMultiplier": 3,
"svgFontSize": 50,
"svgFontColor": "#333",
"filetype": "svg"
},
{
"name": "jpgtest",
"svgShowLabels": false,
"svgFillFacets": true,
"svgShowBorders": false,
"svgSizeMultiplier": 3,
"svgFontSize": 50,
"svgFontColor": "#333",
"filetype": "jpg",
"filetypeQuality": 80
}
]
```
Esto define 3 perfiles de salida. El perfil "completo" muestra las etiquetas, rellena las facetas y muestra los bordes con un multiplicador de tamaño de 3x, un peso de tamaño de fuente de 50, un color de #333 y una salida a una imagen png. El perfil bordersLabels genera un archivo svg sin rellenar facetas y jpgtest genera un archivo jpg con una configuración de calidad jpg de 80.

La versión CLI también genera un archivo json que brinda más información sobre la paleta, qué colores se usan y en qué cantidad, por ejemplo:
```
...
{
"areaPercentage": 0.20327615489130435,
"color": [ 59, 36, 27 ],
"frequency": 119689,
"index": 0
