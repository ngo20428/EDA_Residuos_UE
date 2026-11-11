# Generación y gestión de residuos en la Unión Europea

### Memoria del proyecto de análisis exploratorio de datos (EDA)

**Autoría:** Naroa García · **Fecha:** noviembre de 2026
**Documento complementario a la presentación.** Recoge el trabajo realizado, las técnicas empleadas, las fuentes de datos y las dificultades encontradas.

---

## 1. Introducción y objetivos

El proyecto explora cómo generan y gestionan sus residuos los países de la UE entre 2004 y 2024. La presentación recoge los resultados principales; esta memoria detalla el proceso completo.

Partimos de seis preguntas, que luego se contrastaron con los datos:

| #  | Pregunta / hipótesis                                                                                                             | Bloque                  |
| -- | --------------------------------------------------------------------------------------------------------------------------------- | ----------------------- |
| H1 | ¿Ha aumentado la generación de residuos en la UE?                                                                               | Generación             |
| H2 | ¿Ha disminuido la proporción de residuos peligrosos?                                                                            | Generación             |
| H3 | ¿Qué países y qué actividades económicas generan más residuos?                                                              | Generación             |
| H4 | ¿Está relacionada la generación de residuos con el crecimiento económico (PIB per cápita)? ¿y con el peso de la industria? | Generación + economía |
| H5 | ¿Reciclan más los países nórdicos? ¿Y los países con mayor PIB per cápita?                                                 | Tratamiento             |
| H6 | ¿Reciclan más los países que más residuos generan?                                                                           | Tratamiento             |
| H7 | ¿Qué tipos de residuo se reciclan más y cuáles acaban eliminándose?                                                          | Tratamiento             |

---

## 2. Fuentes de datos

Todos los datos proceden de **Eurostat**, la oficina estadística de la UE, descargados en formato `csv.gz` ("linear", un registro por observación). Los datos de Eurostat se pueden reutilizar citando la fuente, según su política de reutilización de datos (licencia CC BY 4.0).

| Fichero original                               | Código Eurostat                                                                                   | Contenido                                                                                                                           | Uso en el proyecto                                                                                            |
| ---------------------------------------------- | -------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `env_wasgen_linear.csv.gz`                   | [`env_wasgen`](https://ec.europa.eu/eurostat/databrowser/view/env_wasgen/default/table?lang=en)   | Generación de residuos por categoría de residuo, peligrosidad y actividad económica (NACE Rev. 2), en toneladas y kg per cápita | Dataset principal de generación                                                                              |
| `env_wastrt_linear.csv.gz`                   | [`env_wastrt`](https://ec.europa.eu/eurostat/databrowser/view/env_wastrt/default/table?lang=en)   | Tratamiento de residuos por categoría, peligrosidad y operación de gestión (reciclado, vertedero, incineración…)               | Dataset principal de tratamiento                                                                              |
| `sdg_08_10_page_linear.csv.gz`               | [`sdg_08_10`](https://ec.europa.eu/eurostat/databrowser/view/sdg_08_10/default/table?lang=en)     | PIB real per cápita (indicador ODS 8.10)                                                                                           | Relación PIB–residuos y PIB–reciclaje                                                                      |
| `nama_10_a10_page_linear.csv.gz`             | [`nama_10_a10`](https://ec.europa.eu/eurostat/databrowser/view/nama_10_a10/default/table?lang=en) | Cuentas nacionales por rama de actividad (desglose A*10)                                                                            | Peso de la industria en la economía                                                                          |
| `nama_10_gdp__custom_23890097_linear.csv.gz` | [`nama_10_gdp`](https://ec.europa.eu/eurostat/databrowser/view/nama_10_gdp/default/table?lang=en) | PIB y componentes principales                                                                                                       | Se cargó y limpió, pero **no se usó** en el análisis final (se optó por `sdg_08_10`)             |
| Geometrías de países (GeoJSON)               | `countries.geojson`                                                                              | Fronteras para dibujar los mapas                                                                                                    | Dibujo de los mapas coropléticos (junto con cartografía base de [OpenStreetMap](https://openstreetmap.org))  |

**Referencias:**

- Eurostat, base de datos en línea, `ec.europa.eu/eurostat`
- Eurostat. *Generation of waste* (`env_wasgen`). [Data Browser](https://ec.europa.eu/eurostat/databrowser/view/env_wasgen/default/table?lang=en).
- Eurostat. *Treatment of waste* (`env_wastrt`). [Data Browser](https://ec.europa.eu/eurostat/databrowser/view/env_wastrt/default/table?lang=en).
- Eurostat. *GDP per capita* (`sdg_08_10`). [Data  Browser](https://ec.europa.eu/eurostat/databrowser/view/sdg_08_10/default/table?lang=en).
- Eurostat. *National accounts aggregates by industry*
  (`nama_10_a10`). [Data  Browser](https://ec.europa.eu/eurostat/databrowser/view/nama_10_a10/default/table?lang=en).
- Eurostat. *GDP and main components* (`nama_10_gdp`). [Data Browser](https://ec.europa.eu/eurostat/databrowser/view/nama_10_gdp/default/table?lang=en).
- OpenStreetMap. [openstreetmap.org](https://openstreetmap.org)

**Cobertura.** 27 Estados miembros más el agregado "Unión Europea (UE-27, desde 2020)". Los datos de generación son **bienales** (2004, 2006, …, 2024). El dataset de tratamiento tiene 227.136 filas y el de generación 688.573, ambos tras la limpieza.

---

## 3. Estructura del proyecto

```
Data/
	gen_residuos.csv · trt_residuos.csv · gdp_pc.csv
  	gdp_pc_2024.csv · gdp_industria.csv · gen_2024.csv
  Original/     <- ficheros descargados de Eurostat (sin tocar)
  	env_wasgen_linear.csv.gz
    env_wastrt_linear.csv.gz
    nama_10_a10_page_linear.csv.gz
    nama_10_gdp__custom_23890097_linear.csv.gz
    sdg_08_10_page_linear.csv.gz
  
  
Limpieza de datos/
  Limpieza_generacion.ipynb     -> gen_residuos.csv
  Limpieza_tratamiento.ipynb    -> trt_residuos.csv
  gdp.ipynb                     -> gdp_pc.csv, gdp_pc_2024.csv, gdp_industria.csv

EDA_generacion_residuos_UE.ipynb
EDA_reciclaje_residuos_UE.ipynb
Mapas.ipynb                   -> mapas interactivos (folium)
```

Se separó la **limpieza** del **análisis** para que cada paso sea reproducible: los ficheros originales no se modifican, y cada notebook de EDA lee solo datos ya limpios. Los dos EDA comparten paleta de colores y estilo gráfico (matplotlib, seaborn y un tema equivalente en Plotly).

---

## 4. Preparación y limpieza de los datos

### 4.1 Pasos comunes

1. **Eliminación de columnas sin información relevante** (`DATAFLOW`, `LAST UPDATE`, `freq`, y en los datos económicos también `unit`, `na_item`) y **renombrado** a nombres cortos (`TIME_PERIOD` → `year`, `OBS_VALUE` → `value`…).
2. **Pivotado de la unidad de medida.** Eurostat entrega toneladas y kg per cápita como filas distintas (`unit`). Con `pivot` se convirtieron en dos columnas: `toneladas` y `kg_per_capita`.
3. **Filtrado geográfico.** Se dejaron solo los 27 países de la UE y el agregado UE-27, eliminando países candidatos, EFTA y otros agregados.
4. **Eliminación de categorías obsoletas.** Se descartaron los tipos de residuo etiquetados *"valid up to 2008"*, que pertenecen a una clasificación antigua que no cubre toda la serie.
5. **Traducción al castellano** mediante diccionarios: 28 países, 3 niveles de peligrosidad, unos 45 tipos de residuo, 22 actividades NACE y 9 operaciones de tratamiento. Se conservaron las columnas originales durante la limpieza y se exportó solo la versión en castellano.
6. **Exportación** a CSV limpios con las columnas `pais, year, peligrosidad, actividad|tratamiento, residuo, kg_per_capita, toneladas, obs_flag, conf_status`.

### 4.2 Datos económicos (`gdp.ipynb`)

Se limpiaron el PIB real per cápita (serie 2000–2024) y el peso de la industria, y se unificó el nombre de los países con el mismo diccionario de traducción, de modo que `pais` + `year` sirve de **clave de unión** entre los tres bloques de datos. Se generó además un corte de 2024 para los análisis transversales.

### 4.3 Calidad de los datos

Tras la limpieza se revisó la calidad de ambos datasets:

|                                                       | Generación | Tratamiento |
| ----------------------------------------------------- | ----------- | ----------- |
| Filas                                                 | 688.573     | 227.136     |
| Valores ausentes en `toneladas`/`kg_per_capita`   | 12,4 %      | 15,2 %      |
| Filas con marca de calidad (`obs_flag`)             | 14 %        | 15 %        |
| Filas con marca de confidencialidad (`conf_status`) | 1,7 %       | 1,9 %       |

Las **marcas de calidad** de Eurostat (`obs_flag`) se conservaron y se consultaron en cada análisis. Las que afectaron a resultados fueron: la cifra de 2024 del agregado UE (marcada `i`, que interpretamos como valor imputado por Eurostat), el dato de Grecia en 2024 (`p`, provisional), y varios valores estimados (`e`) o con ruptura de serie (`b`) en años anteriores.

---

## 5. Análisis de la generación de residuos

**Técnicas:** series temporales, comparaciones entre países, índices base 100, correlaciones de Pearson y Spearman y una clasificación de "desacoplamiento" entre economía y residuos.

### 5.1 Evolución en la UE (H1)

La UE generó unos **2.196 millones de toneladas en 2024** (4.872 kg por habitante), frente a 2.249 millones en 2004: una variación del −2,4 %, con oscilaciones entre 2.144 y 2.336 millones en el periodo. **No puede afirmarse que la generación haya aumentado.** Como los residuos minerales dominan el total, se repitió el análisis excluyéndolos para ver el comportamiento del resto.

![1794420604543](image/memoria_residuos_UE/1794420604543.png)

### 5.2 Residuos peligrosos (H2)

Se calculó el porcentaje de toneladas peligrosas sobre el total. **La proporción ha subido** de 3,6 % (2004) a 5,9 % (2024), y en términos absolutos de 80,8 a 128,9 millones de toneladas. Esto contradice la hipótesis de partida. El punto de 2024 se dibujó con marcador hueco por ser un dato imputado.

![1794420628813](image/memoria_residuos_UE/1794420628813.png)

### 5.3 Diferencias entre países (H3)

En 2024 lideran el ranking per cápita **Finlandia (21.774 kg), Suecia (17.174 kg) y Bulgaria (14.324 kg)**, frente a Letonia (1.670 kg), Portugal (1.891 kg) o Croacia (2.015 kg). Es una diferencia de más de diez veces. Una explicación coherente con los datos es el peso de las industrias extractivas y los residuos minerales (ver 5.4), aunque no la hemos verificado país a país. Al excluir los residuos minerales, el mapa de generación muestra un patrón distinto, en el que destaca Estonia, atribuido a la explotación de esquisto bituminoso (*oil shale*) [[Eurostat](https://ec.europa.eu/eurostat/web/products-eurostat-news/w/ddn-20261029-2)].

![1794420715617](image/memoria_residuos_UE/1794420715617.png)

### 5.4 Actividades económicas y tipos de residuo (H3)

Las actividades que más residuos generan en la UE en 2024 son la **construcción (≈840 millones de toneladas, ≈38 % del total)** y las **industrias extractivas (≈524 millones, ≈24 %)**. Les siguen manufactura (217), gestión de residuos y agua (210–238) y hogares (196). Entre 2004 y 2024 la construcción creció (de 667 a 840 millones) y la minería y la manufactura bajaron (de 718 a 524 y de 334 a 217 millones). Por tipo de material, dominan los residuos minerales de construcción y demolición, los suelos y los residuos domésticos. Se usaron *treemaps* de Plotly para ver el peso relativo de cada categoría.

### 5.5 Crecimiento económico (H4)

Se enlazó la generación con el PIB real per cápita y se calcularon dos cosas:

- **Índices base 100** (año inicial = 100) de PIB y residuos por país, con una gráfica para cada uno de los 28 casos.
- **Correlación PIB–residuos por país**, que va de −0,89 (Bulgaria) a +0,85 (Eslovenia), con la UE en −0,42. Con solo 11 observaciones por país, estos valores son frágiles, y la heterogeneidad ya indica que no hay una relación general.

Se propuso además una **clasificación de desacoplamiento** según el crecimiento acumulado 2004–2024:

| Categoría (criterio)                                                 | Países                                                                                                 |
| --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| **Desacoplamiento absoluto** (PIB sube, residuos bajan)         | UE-27 y 9 países: Alemania, Bulgaria, España, Estonia, Irlanda, Luxemburgo, Malta, Portugal, Rumanía |
| **Desacoplamiento relativo** (residuos crecen menos que el PIB) | 11 países, entre ellos Francia, Polonia, Países Bajos, Lituania, Austria, Bélgica                    |
| **Acoplamiento expansivo** (residuos crecen más que el PIB)    | Dinamarca, Eslovenia, Finlandia, Italia, Letonia, Suecia                                                |
| **Contracción conjunta**                                       | Grecia                                                                                                  |

### 5.6 Peso de la industria (H4)

Con datos de 2024, la correlación entre el peso de la industria en el PIB y los residuos per cápita es prácticamente nula (Pearson −0,14, p = 0,50; Spearman −0,10, p = 0,62). **No hay evidencia de relación.**

---

## 6. Análisis del tratamiento y reciclaje

**Técnicas:** cálculo de tasas con denominador explícito, tablas dinámicas (`pivot_table`), normalización a porcentajes, agrupación de categorías, contraste no paramétrico (Mann–Whitney) y correlaciones.

**Definición de la tasa de reciclaje:** toneladas en "*Valorización – reciclado"* ÷ toneladas en "*Tratamiento de residuos"* (total). Se eligió este cociente, con denominador explícito, porque muchas categorías del dataset son subtotales de otras y sumarlas habría contado dos veces lo mismo.

### 6.1 Evolución (UE)

La tasa de reciclaje de la UE **sube de 36,9 % (2010) a 42,0 % (2024)**, con un mínimo del 35,5 % en 2012–2014. La serie de reciclado solo está disponible desde 2010.

![1794420796414](image/memoria_residuos_UE/1794420796414.png)

### 6.2 Comparación entre países (2024)

Reciclan más **Italia (87,3 %), Hungría (77,1 %), Eslovaquia (69,8 %) y Bélgica (69,0 %)**. Reciclan menos **Bulgaria (7,9 %), Finlandia (10,8 %), Suecia (16,1 %) e Irlanda (16,9 %)**. El gráfico de barras apiladas muestra que en los países de menor tasa el destino mayoritario es el vertedero (Finlandia 63,7 %, Bulgaria 53,0 %).

![1794420853009](image/memoria_residuos_UE/1794420853009.png)

### 6.3 Contrastes de hipótesis (H5)

| Pregunta                                                                              | Método                  | Resultado                                          | Conclusión                                                                                         |
| ------------------------------------------------------------------------------------- | ------------------------ | -------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| ¿Reciclan más los nórdicos? (Dinamarca, Finlandia, Suecia vs. resto, n = 3 vs. 23) | Mann–Whitney unilateral | U = 15, p = 0,94. Media nórdica 26,7 % vs. 49,2 % | **No se confirma.** Si acaso, los datos van en sentido contrario                              |
| ¿Reciclan más los países con mayor PIB per cápita?                                | Pearson / Spearman       | r = −0,18 (p = 0,37) / ρ = −0,15 (p = 0,47)     | **Sin relación significativa**                                                               |
| ¿Reciclan más los que más residuos generan?                                        | Pearson / Spearman       | r = −0,62 (p < 0,001) / ρ = −0,53 (p = 0,004)   | **Relación negativa significativa:** a más residuos per cápita, menor porcentaje reciclado |

El último resultado es el más llamativo, aunque está muy influido por Finlandia, Suecia y Bulgaria, que combinan generación muy alta y reciclaje bajo (ver Fig. 5). Una lectura plausible es que buena parte de sus residuos son minerales que se depositan o se usan como relleno en lugar de reciclarse, pero esto es una hipótesis, no un resultado demostrado por el análisis. Hay que recordar también que la correlación no implica causalidad.

![1794420903916](image/memoria_residuos_UE/1794420903916.png)

### 6.4 Tipos de residuo (H6)

Los 22 tipos de residuo disponibles se agruparon en cinco bloques para poder leerlos (*Mineral y construcción*, *Reciclables clásicos*, *Urbano y mixto*, *Orgánico y biomasa*, *Químico e industrial*). Aproximadamente, a partir del gráfico:

- **Orgánico y biomasa** (≈90 % reciclado) y **reciclables clásicos** (papel, vidrio, plástico, metales…; ≈83 %) son los que mejor se recuperan.
- **Urbano y mixto**: ≈32 % reciclado, un ≈41 % va a valorización energética y un ≈24 % a vertedero.
- **Químico e industrial**: ≈49 % reciclado y una parte relevante a vertedero e incineración.
- **Mineral y construcción**: el grupo con mayor peso en vertedero (≈36 %), con un ≈35 % reciclado y un ≈18 % en relleno.

![1794420924098](image/memoria_residuos_UE/1794420924098.png)

---

## 7. Mapas interactivos

El notebook `Mapas` incluye dos mapas interactivos creados con **folium** que muestran, por país, los **residuos generados** y los **residuos reciclados** (`mapa_residuos_UE_2024.html` y `mapa_trt_residuos_UE_2024.html`). Sirven para ver la geografía del problema de un vistazo: la comparación entre países queda más clara sobre un mapa que en un gráfico de barras con 27 países. En ambos casos, presentan los residuos totales excluyendo los residuos minerales.

---

## 8. Dificultades encontradas y cómo se resolvieron

1. **Formato largo y varias unidades.** Cada observación aparecía duplicada (toneladas y kg per cápita). Se resolvió pivotando por `unit`, incluyendo en el índice las columnas de calidad para no perder las marcas de Eurostat.
2. **Categorías solapadas (riesgo de doble conteo).** Residuos, actividades NACE y operaciones de tratamiento incluyen totales y subtotales que contienen a otras categorías. Hubo que revisar manualmente cada jerarquía, definir la tasa de reciclaje con denominador explícito, elegir un subconjunto sin solapamientos para los *treemaps* y agrupar los tipos de residuo en cinco bloques.
3. **Peso de los residuos minerales.** Distorsionan la comparación entre países y la evolución temporal. Se optó por mostrar siempre dos versiones: con y sin residuos minerales.
4. **Valores confidenciales en Austria (2024).** Eurostat ocultaba dos operaciones (incineración D10 y valorización energética R1). Como afectaban al reparto de tratamiento de un país, se consultó el gráfico de Eurostat y se **imputaron a mano** unos 147.551 t y 3.434.070 t. Es una estimación leída de un gráfico y debe tratarse como tal.
5. **Cobertura desigual.** Los datos son bienales, el reciclaje total empieza en 2010 y **Rumanía no tiene datos de 2024** ni en generación ni en tratamiento, por lo que los análisis de 2024 trabajan con 26 países más el agregado UE.
6. **Datos imputados o provisionales en el último año.** El agregado UE (`i`) y Grecia (`p`) en 2024. Se mantuvieron, se señalaron en los gráficos y se tuvieron en cuenta al interpretar.
7. **Muestras pequeñas.** Solo tres países nórdicos y 11 puntos temporales por país. Por eso se eligió un test no paramétrico, se usaron Pearson y Spearman a la vez y se evitó sobreinterpretar.
8. **Unificación de países entre fuentes.** Los nombres en inglés de Eurostat se tradujeron con un único diccionario para poder unir generación, tratamiento y PIB por `pais` y `year`.
9. **Escala per cápita.** Las cifras per cápita incluyen todos los residuos (minería, construcción…) y son mucho mayores que las de residuos urbanos que suelen aparecer en prensa; hay que aclararlo al presentarlas.
10. **Coherencia visual.** Para que los gráficos de los dos EDA fueran homogéneos se definió una paleta única y una configuración común de matplotlib/seaborn, y un tema equivalente en Plotly.

---

## 9. Limitaciones y mejoras pendientes

- **Solapamiento en el gráfico de actividades.** El gráfico de barras y el *treemap* de actividades NACE incluyen a la vez agregados (industria manufacturera, suministro de agua y gestión de residuos) y sus subcategorías, por lo que no son perfectamente aditivos. No afecta a la conclusión principal (construcción y minería dominan), pero sería mejor excluir los agregados.
- **El agregado UE-27 entra en las correlaciones de 2024** (27 filas en lugar de 26 países). Conviene excluirlo y recalcular.
- **El PIB real (`nama_10_gdp`)** se limpió pero no se usó; podría incorporarse o eliminarse del repositorio.
- **Las correlaciones no prueban causalidad** y con muestras tan pequeñas son sensibles a países atípicos. Una mejora sería repetirlas excluyendo Finlandia, Suecia y Bulgaria.
- **Datos bienales:** no permiten estudiar cambios anuales.
- Un análisis posterior podría profundizar más en los tipos de residuos o en las actividades.

---

## 10. Conclusiones

| Hipótesis                      | Resultado                                                                                                                                                                                               |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| H1 · Aumento de la generación | **No confirmado:** −2,4 % entre 2004 y 2024, sin tendencia clara                                                                                                                                 |
| H2 · Menos residuos peligrosos | **Refutado:** la proporción sube de 3,6 % a 5,9 %                                                                                                                                                |
| H3 · Países y actividades     | Finlandia, Suecia y Bulgaria lideran el ranking per cápita; construcción (≈38 %) y minería (≈24 %) dominan por actividad                                                                           |
| H4 · Economía y residuos      | Sin relación clara con el PIB ni con el peso de la industria; 10 de 28 casos (UE incluida) muestran desacoplamiento absoluto                                                                           |
| H5 · Reciclaje                 | Los nórdicos **no** reciclan más; el PIB **no** se asocia al reciclaje;                                                                                                                   |
| H6 · Reciclaje y generación   | Los países que más residuos generan no son los que más reciclan.                                                                                                                                     |
| H7 · Tipos de residuo          | Hay diferencias entre los tipos de residuos. Parece que se recuperan mejor los residuos orgánicos y los reciclables clásicos; los minerales y los mixtos acaban más a menudo en vertedero o relleno |

La tasa de reciclaje de la UE mejora de forma sostenida (36,9 % → 42,0 %) y la generación total se mantiene estable, pero la proporción de residuos peligrosos crece y las diferencias entre países son enormes. Además, las cifras dependen mucho de qué se cuente (por ejemplo, incluir o no los residuos minerales).

---

## Anexo · Herramientas

Python 3 · `pandas`, `numpy` (manipulación) · `matplotlib`, `seaborn`, `plotly.express` (visualización) · `scipy.stats` (Pearson, Spearman, Mann–Whitney) · `folium` (mapas) · Jupyter Notebook.
