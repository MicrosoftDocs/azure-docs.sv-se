---
title: 'Självstudie: skapa en antecknings bok i Azure Cosmos DB för att analysera och visualisera data'
description: 'Självstudie: Lär dig hur du använder inbyggda Jupyter-anteckningsböcker för att importera data till Azure Cosmos DB, analysera data och visualisera utdata.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: beb58922172a045242f4f9bcaf647b3cfc8b5551
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380805"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Självstudie: skapa en antecknings bok i Azure Cosmos DB för att analysera och visualisera data
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln beskriver hur du använder inbyggda Jupyter-anteckningsböcker för att importera exempel på detalj handels data till Azure Cosmos DB. Du kommer att se hur du använder SQL-och Azure Cosmos DB Magic-kommandon för att köra frågor, analysera data och visualisera resultaten.

## <a name="prerequisites"></a>Förutsättningar

* [Aktivera antecknings böcker på ett Azure Cosmos-konto](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>Skapa resurser och importera data
 
I det här avsnittet ska du skapa Azure Cosmos-databasen, containern och importera detalj handels data till behållaren.

1. Gå till ditt Azure Cosmos-konto och öppna **datautforskaren.**

1. Gå till fliken **antecknings böcker** , Välj `…` bredvid **Mina antecknings böcker** och skapa en **ny antecknings bok**. Välj **python 3** som standard kernel.

   :::image type="content" source="./media/create-notebook-visualize-data/create-new-notebook.png" alt-text="Skapa en ny antecknings bok":::

1. När en ny antecknings bok har skapats kan du byta namn på den till något som liknar **VisualizeRetailData. ipynb.**

1. Härnäst ska du skapa en databas med namnet "RetailDemo" och en behållare med namnet "WebsiteData" för att lagra detalj handels data. Du kan använda/CartID som partitionsnyckel. Kopiera och klistra in följande kod i en ny cell i din bärbara dator och kör den:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Om du vill köra en cell markerar `Shift + Enter` du eller markerar cellen och väljer alternativet **Kör aktiv cell** i navigerings fältet i data Utforskaren.

   :::image type="content" source="./media/create-notebook-visualize-data/run-active-cell.png" alt-text="Kör den aktiva cellen":::

   Databasen och behållaren skapas i ditt nuvarande Azure Cosmos-konto. Behållaren har tillhandahållits med 400 RU/s. Följande utdata visas när databasen och behållaren har skapats. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   Du kan också uppdatera fliken **data** och se de nyligen skapade resurserna:

   :::image type="content" source="media/create-notebook-visualize-data/refresh-data-tab.png" alt-text="Uppdatera fliken data om du vill se den nya behållaren":::

1. Härnäst ska du importera exempel detalj handels data till Azure Cosmos-behållaren. Här är formatet för ett objekt från detalj handels data:

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   I själv studie kursen lagras exempel detalj handels data i Azure Blob Storage. Du kan importera den till Azure Cosmos-behållaren genom att klistra in följande kod i en ny cell. Du kan bekräfta att data har importer ATS genom att köra en fråga för att välja antalet objekt.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   När du kör den föregående frågan returneras följande utdata:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Hämta dina data till en DataFrame

Innan du kör frågor för att analysera data kan du läsa data från container till en [Pandas-DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) för analys. Använd följande SQL Magic-kommando för att läsa data till en DataFrame:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Mer information finns i de [inbyggda antecknings kommandon och funktioner i Azure Cosmos DB](use-python-notebook-features-and-commands.md) artikel. Du kommer att köra frågan- `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c` . Resultaten sparas i en Pandas-DataFrame med namnet df_cosmos. Klistra in följande kommando i en ny Notebook-cell och kör den:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

I en ny Notebook-cell kör du följande kod för att läsa de första 10 objekten från utdata:

```python
# See a sample of the result
df_cosmos.head(10)
```

:::image type="content" source="./media/create-notebook-visualize-data/run-query-get-top10-items.png" alt-text="Kör fråga för att hämta de 10 främsta objekten":::

## <a name="run-queries-and-analyze-your-data"></a>Köra frågor och analysera dina data

I det här avsnittet ska du köra några frågor om hämtade data.

* **Fråga1:** Kör en grupp efter fråga på DataFrame för att få summan av total försäljnings intäkt för varje land/region och Visa 5 objekt från resultatet. Kör följande kod i en ny Notebook-cell:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/total-sales-revenue-output.png" alt-text="Total försäljnings intäkt, utdata":::

* **Query2:** Öppna en ny Notebook-cell och kör följande kod för att få en lista över de fem främsta inköpta objekten:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/top5-purchased-items.png" alt-text="De fem främsta inköpta artiklarna":::

## <a name="visualize-your-data"></a>Visualisera dina data  

1. Nu när vi har våra data på intäkter från Azure Cosmos-behållaren kan du visualisera dina data med ett visualiserings bibliotek som du själv väljer. I den här självstudien kommer vi att använda bokeh-bibliotek. Öppna en ny Notebook-cell och kör följande kod för att installera bokeh-biblioteket. När alla krav är uppfyllda kommer biblioteket att installeras.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Förbered sedan för att rita data på en karta. Koppla data i Azure Cosmos DB med information om land/region som finns i Azure Blob Storage och konvertera resultatet till formatet interjson. Kopiera följande kod till en ny Notebook-cell och kör den.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country/region information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries/regions dataframe with our data in Azure Cosmos DB, joining on country/region code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. Visualisera försäljnings intäkterna för olika länder/regioner på en världs karta genom att köra följande kod i en ny Notebook-cell:

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   I utdata visas världs kartan med olika färger. Färgerna som är mörkare till ljusare motsvarar de länder/regioner som har högst intäkter till lägsta intäkter.

   :::image type="content" source="./media/create-notebook-visualize-data/countries-revenue-map-visualization.png" alt-text="Visualisering av intäkts karta för länder/regioner":::

1. Nu ska vi se ett annat fall av data visualisering. Behållaren WebsiteData innehåller en lista över användare som har visat ett objekt, lagt till i sin varukorg och köpt objektet. Vi ska Rita in konverterings takten för de köpta objekten. Kör följande kod i en ny cell för att visualisera konverterings takten för varje objekt:

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png" alt-text="Visualisera inköps konverterings takt":::

## <a name="next-steps"></a>Nästa steg

* Läs mer om python-kommandon för bärbara datorer [i så här använder du inbyggda Notebook-kommandon och-funktioner i Azure Cosmos DB](use-python-notebook-features-and-commands.md) artikel.
