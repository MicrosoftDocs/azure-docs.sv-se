---
title: Visualiseringar
description: Visualisera dina data med hjälp av Azure Synapse-anteckningsböcker
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: 7e57cdca1d212e6077d685d95a8f869c12e546a8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627956"
---
# <a name="visualize-data"></a>Visualisera data
Azure Synapse är en integrerad analys tjänst som påskyndar tiden för insikter, över informations lager och Big data analys system. Data visualisering är en viktig komponent där du kan få inblick i dina data. Det hjälper dig att göra stora och små data enklare för människor att förstå. Det gör det också lättare att identifiera mönster, trender och avvikande värden i grupper med data. 

När du använder Apache Spark i Azure Synapse Analytics finns det olika inbyggda alternativ som hjälper dig att visualisera dina data, inklusive Synapse-diagram alternativ för antecknings böcker, åtkomst till populära bibliotek med öppen källkod och integrering med Synapse SQL och Power BI.

## <a name="notebook-chart-options"></a>Alternativ för notebook-diagram
När du använder en Azure Synapse-anteckningsbok kan du göra en vy i tabell resultatet till ett anpassat diagram med hjälp av diagram alternativ. Här kan du visualisera dina data utan att behöva skriva någon kod. 

### <a name="displaydf-function"></a>Visa (DF)-funktion
```display```Funktionen gör att du kan aktivera SQL-frågor och Apache Spark dataframes och RDD i omfattande data visualiseringar. ```display```Funktionen kan användas på dataframes eller RDD som skapats i PySpark, Scala, Java och .net.

För att få åtkomst till diagram alternativ:
1. Utdata från ```%%sql``` Magic-kommandon visas som standard i vyn renderad tabell. Du kan också anropa ```display(df)``` Spark-DataFrames eller RDD-funktionen (elastiskt distribuerade data uppsättningar) för att skapa den åter givnings tabellen. 
   
2. När du har en renderad tabellvy växlar du till diagramvyn.
   ![inbyggda diagram](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. Nu kan du anpassa visualiseringen genom att ange följande värden:

   | Konfiguration | Beskrivning |
   |--|--| 
   | Diagramtyp | ```display```Funktionen har stöd för en mängd olika diagram typer, inklusive stapeldiagram, punkt diagram, linje diagram med mera |
   | Nyckel | Ange värde intervallet för x-axeln|
   | Värde | Ange värde intervallet för y-axelns värden |
   | Serie grupp | Används för att fastställa grupper för sammanställningen | 
   | Aggregering | Metod för att aggregera data i visualiseringen| 
   
   
    > [!NOTE]
    > Som standard ```display(df)``` tar funktionen bara de första 1000 raderna i data för att återge diagram. Kontrol lera **agg regeringen över alla resultat** och klicka på knappen **tillämpa** . du kommer att använda diagrammets generering från hela data uppsättningen. Ett Spark-jobb aktive ras när diagram inställningen ändras. Observera att det kan ta flera minuter att slutföra beräkningen och återge diagrammet.
   
4. När du är klar kan du Visa och interagera med din slutliga visualisering!

### <a name="displaydf-statistic-details"></a>information om Visa (DF) statistik
Du kan använda <code>display(df, summary = true)</code> för att kontrol lera statistik sammanfattningen för en specifik Apache Spark DataFrame som innehåller kolumn namn, kolumn typ, unika värden och saknade värden för varje kolumn. Du kan också välja en speciell kolumn om du vill visa dess lägsta värde, högsta värde, medelvärde och standard avvikelse.
    ![inbyggda diagram – Sammanfattning](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtmldf-option"></a>alternativet displayHTML (DF)
Azure Synapse Analytics-anteckningsböcker har stöd för HTML-grafik med ```displayHTML``` funktionen.

Följande bild är ett exempel på hur du skapar visualiseringar med hjälp av [D3.js](https://d3js.org/).

   ![D3-JS-exempel](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

Kör följande kod för att skapa visualiseringen ovan.

```python
displayHTML("&quot;&quot;<!DOCTYPE html>
<meta charset=&quot;utf-8&quot;>

<!-- Load d3.js -->
<script src=&quot;https://d3js.org/d3.v4.js&quot;></script>

<!-- Create a div where the graph will take place -->
<div id=&quot;my_dataviz&quot;></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select(&quot;#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>Populära bibliotek
När den kommer till data visualisering erbjuder python flera diagram bibliotek som är förpackade med många olika funktioner. Som standard innehåller varje Apache Spark pool i Azure Synapse Analytics en uppsättning granskade och populära bibliotek med öppen källkod. Du kan också lägga till eller hantera ytterligare bibliotek & versioner med hjälp av funktionerna för hantering av Azure Synapse Analytics-bibliotek. 

### <a name="matplotlib"></a>Matplotlib
Du kan rendera standard ritnings bibliotek, som matplotlib, med hjälp av de inbyggda åter givnings funktionerna för varje bibliotek.

Följande bild är ett exempel på hur du skapar ett stapeldiagram med **matplotlib**.
   ![Linje diagram exempel.](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

Kör följande exempel kod för att rita bilden ovan.

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```


### <a name="bokeh"></a>Bokeh
Du kan återge HTML-eller interaktiva bibliotek, som **bokeh**, med hjälp av ```displayHTML(df)``` . 

Följande bild är ett exempel på hur du ritar glyfer över en karta med **bokeh**.

   ![bokeh – exempel](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

Kör följande exempel kod för att rita bilden ovan.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator&quot;, y_axis_type=&quot;mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```


### <a name="plotly"></a>Plotly
Du kan återge HTML-eller interaktiva bibliotek som att **Rita** med hjälp av **displayHTML ()**.

Kör följande exempel kod för att rita bilden nedan.

   ![Rita ett exempel](./media/apache-spark-development-using-notebooks/synapse-plotly-image.png#lightbox)


```python
from urllib.request import urlopen
import json
with urlopen('https://raw.githubusercontent.com/plotly/datasets/master/geojson-counties-fips.json') as response:
    counties = json.load(response)

import pandas as pd
df = pd.read_csv("https://raw.githubusercontent.com/plotly/datasets/master/fips-unemp-16.csv",
                   dtype={"fips": str})

import plotly.express as px

fig = px.choropleth(df, geojson=counties, locations='fips', color='unemp',
                           color_continuous_scale="Viridis",
                           range_color=(0, 12),
                           scope="usa",
                           labels={'unemp':'unemployment rate'}
                          )
fig.update_layout(margin={"r":0,"t":0,"l":0,"b":0})

# create an html document that embeds the Plotly plot
h = plotly.offline.plot(fig, output_type='div')

# display this html
displayHTML(h)
```


### <a name="additional-libraries"></a>Ytterligare bibliotek 
Utöver de här biblioteken innehåller Azure Synapse Analytics-körningen även följande uppsättning bibliotek som ofta används för data visualisering:
- [Matplotlib](https://matplotlib.org/)
- [Bokeh](https://bokeh.org/)
- [Seaborn](https://seaborn.pydata.org/) 
- [Plotly](https://plotly.com/)

Du kan gå till [dokumentationen](./spark/../apache-spark-version-support.md) för Azure Synapse Analytics-körningen om du vill ha den senaste informationen om tillgängliga bibliotek och versioner.

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>Ansluta till Power BI med Apache Spark & SQL på begäran
Azure Synapse Analytics integreras djupt med Power BI som gör det möjligt för data tekniker att bygga analys lösningar.

Med Azure Synapse Analytics kan olika beräknings motorer för arbets ytan dela databaser och tabeller mellan dess Spark-pooler och Server lös SQL-poolen. Med hjälp av den [delade metadata modellen](../metadata/overview.md)kan du köra frågor mot dina Apache Spark tabeller med SQL på begäran. När du är färdig kan du ansluta din SQL-slutpunkt på begäran till Power BI så att du enkelt kan fråga dina synkroniserade Spark-tabeller.


## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar Spark SQL DW Connector: [SYNAPSE SQL Connector](./spark/../synapse-spark-sql-pool-import-export.md)
- Visa standard biblioteken: [Azure Synapse Analytics runtime](../spark/apache-spark-version-support.md)