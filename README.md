# sci-colombia
Este indicador mide la estructura de los bosques midiendo 3 variables principales: densidad de dosel, altura de dosel y 
tiempo de intervención, generando una categoría que varía entre 1 (Bosques bajos, abiertos y recientemente disturbados) y
18 (Bosques altos, densos y poco disturbados). 

## Pre-requisitos
Solo se requiere una cuenta activa de Google Earth Engine y definir el año y área de estudio (aoi). En el script se realiza una prueba con un aoi que cubre toda la parte continental de Colombia. Todos los insumos del script se encuentran disponibles en Assets de Google Earth Engine.

```
var aoi = ee.FeatureCollection("USDOS/LSIB_SIMPLE/2017").filter(ee.Filter.eq('country_na', 'Colombia'));
var image = ee.Image("UMD/hansen/global_forest_change_2024_v1_12").clip(aoi);
var image2 = ee.Image('users/andrewjhansen31/treeCover_SA');
var image4 = ee.Image('users/andrewjhansen31/20N_080W_treecover2010_v3');
var merged_img = ee.ImageCollection([image2, image4]).mosaic().clip(aoi);
var image3 = ee.Image('users/andrewjhansen31/treeheight_SA').clip(aoi);
var image5 = ee.Image('projects/ee-lromero/assets/Tree_height_2019').clip(aoi);
var image6 = ee.Image('projects/ee-lromero/assets/Bosques_etter');
```

## Cómo ejecutar
Primero se define el año de trabajo definiendo en una variable los dos últimos dígitos. En el momento en que se publica esta rutina, el set de datos más actualizado es del año 2024, y en caso de que se requiera un año previo, se recomienda que se defina como mínimo el año 2017. Con los años antes de 2017 el script no funcionará por la manera en como están definidas las categorías. En el script se usó como ejemplo el año 2024

```
var maxYear = ee.Number(24);
```
**Aspectos a tener en cuenta:** De las tres variables que componen el indicador, la única que varía anualmente es el tiempo de intervención. La densidad y altura de dosel permanecen constantes independientemente del año de estudio. La densidad de dosel corresponde a la cobertura de árboles global para el año 2010, construida a partir de datos Landsat y con una resolución de 30 m. Por otro lado, para la altura de dosel se proponen dos sets de datos: La altura de dosel para el año 2012 construida por el Global Land Analysis & Discovery Lab (GLAD) de la Universidad de Maryland, y la altura de dosel calculada para Colombia por Fagua et al. (2025). En el script proporcionado se sugiere utilizar la versión de 2025, pero queda a discresión del usuario. Si el usuario posee otras capas relacionadas con densidad y altura de dosel, pueden cargarse localmente mientras se mantengan con los mismos nombres y la variable se mida en el mismo rango (0 a 100 para densidad de dosel y altura en metros). Como paso adicional, se aplica una máscara de bosques potenciales desarrollada por Andrés Etter para que los valores se asocien solo a las áreas boscosas. 

Al final del script se muestra un ejemplo para aplicar nuevas máscaras que pueden ser interés (como el caso de la capa de Bosque no Bosque de IDEAM) y lo necesario para calcular un indicador sencillo como el promedio del área evaluada.

```
var promedio = sci_bnb.reduceRegion({
  reducer: ee.Reducer.mean(),
  geometry: aoi,   
  scale: 30,                     
  maxPixels: 1e13                
});

print('Promedio SCI 20'+maxYear.getInfo()+':', promedio);
```

## Autores(as) y contacto
Esta metodología fue diseñada por el equipo de Andrew Hansen y puede consultarse en el enlace: [Hansen et al. (2019)](https://www.nature.com/articles/s41597-019-0214-3#Tab2)
Las modificaciones para evaluar diferentes años y el ajuste al escenario de Colombia fue por parte de Luis Hernando Romero Jiménez (lromero@humboldt.org.co)

## Licencia

Este proyecto está bajo la licencia MIT, 
mira la [LICENCIA](LICENSE.md) 
para obtener más detalles.
