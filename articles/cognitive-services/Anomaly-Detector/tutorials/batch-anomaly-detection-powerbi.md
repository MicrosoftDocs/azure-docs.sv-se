---
title: 'Självstudie: visualisera avvikelser med batch-identifiering och Power BI'
titleSuffix: Azure Cognitive Services
description: 'Lär dig hur du använder API: t för avvikelse detektor och Power BI för att visualisera avvikelser i dina tids serie data.'
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: fc2667dfddf4442acaf39f6783190e8640b22a89
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561737"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Självstudie: visualisera avvikelser med batch-identifiering och Power BI

Använd den här självstudien för att hitta avvikelser inom en tids serie data uppsättning som en batch. Med hjälp av Power BI Skriv bordet tar du en Excel-fil, förbereder data för API: t för avvikelse detektor och visualiserar statistisk avvikelse i hela den.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Använd Power BI Desktop för att importera och transformera en tids serie data uppsättning
> * Integrera Power BI Desktop med API: t för avvikelse detektor vid batch-avvikelse identifiering
> * Visualisera avvikelser som finns i dina data, inklusive förväntade och visade värden och gränser för avvikelse identifiering.

## <a name="prerequisites"></a>Förutsättningar
* En [Azure-prenumeration](https://azure.microsoft.com/free/cognitive-services)
* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), tillgängligt kostnads fritt.
* En Excel-fil (. xlsx) som innehåller tids serie data punkter. Exempel data för den här snabb starten finns på [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)
* När du har en Azure-prenumeration <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" skapar du en avvikelse detektor resurs "  target="_blank"> skapa en avvikelse </a> i den Azure Portal för att hämta nyckel och slut punkt.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API: t för avvikelse identifiering. Du kommer att göra detta senare i snabb starten.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Läsa in och formatera tids serie data

Kom igång genom att öppna Power BI Desktop och läsa in tids serie data som du laddade ned från kraven. Den här Excel-filen innehåller en serie tidsstämpel och värdepar för UTC (Coordinated Universal Time).  

> [!NOTE]
> Power BI kan använda data från en mängd olika källor, till exempel CSV-filer, SQL-databaser, Azure Blob Storage med mera.  

Klicka på menyfliksområdet **Start** i huvud Power BI Desktops fönstret. I den **externa data** gruppen i menyfliksområdet öppnar du den nedrullningsbara menyn **Hämta data** och klickar på **Excel**.

![En bild av knappen Hämta data i Power BI](../media/tutorials/power-bi-get-data-button.png)

När dialog rutan visas går du till den mapp där du laddade ned filen example. xlsx och markerar den. När **navigerings** dialog rutan visas klickar du på **Blad1** och sedan på **Redigera**.

![En bild av skärmen för data källan "Navigator" i Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI kommer att konvertera tidsstämplar i den första kolumnen till en `Date/Time` datatyp. Dessa tidsstämplar måste konverteras till text för att kunna skickas till API: t för avvikelse identifiering. Om Power Query redigeraren inte öppnas automatiskt, klickar du på **Redigera frågor** på fliken Start.

Klicka på menyfliksområdet **transformera** i Power Query redigeraren. I **valfri kolumn** grupp öppnar du menyn **datatyp:** och väljer **text**.

![En bild av den nedrullningsbara data typen](../media/tutorials/data-type-drop-down.png)

När du får ett meddelande om att ändra kolumn typ klickar du på **Ersätt aktuella**. Klicka sedan på **stäng & Verkställ** eller **Använd** i menyfliksområdet **Start** .

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Skapa en funktion för att skicka data och formatera svaret

Om du vill formatera och skicka data filen till API: t för avvikelse detektor kan du anropa en fråga i tabellen som har skapats ovan. I Power Query redigeraren, från **Start** -menyfliksområdet, öppnar du List rutan **ny källa** och klickar på **Tom fråga**.

Se till att din nya fråga är markerad och klicka sedan på **avancerad redigerare**.

![En bild av skärmen "Avancerad redigerare"](../media/tutorials/advanced-editor-screen.png)

I Avancerad redigerare använder du följande Power Query M-kodfragment för att extrahera kolumnerna från tabellen och skicka dem till API: et. Därefter skapar frågan en tabell från JSON-svaret och returnerar den. Ersätt `apiKey` variabeln med din giltiga API-nyckel för avvikelse detektor och `endpoint` med slut punkten. När du har angett frågan i Avancerad redigerare klickar du på **klar**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody, ManualStatusHandling={400}]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({

                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),

    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Anropa frågan på ditt data blad genom att välja `Sheet1` under **ange parameter** och klicka på **anropa**.

![En bild av Invoke-funktionen](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Sekretess och autentisering för data Källa

> [!NOTE]
> Tänk på organisationens principer för data sekretess och åtkomst. Mer information finns i [Power BI Desktop sekretess nivåer](/power-bi/desktop-privacy-levels) .

Du kan få ett varnings meddelande när du försöker köra frågan eftersom den använder en extern data källa.

![En bild som visar en varning som skapats av Power BI](../media/tutorials/blocked-function.png)

Åtgärda detta genom att klicka på **Arkiv**, **alternativ och inställningar**. Klicka sedan på **alternativ**. Under den **aktuella filen** väljer du **Sekretess** och **ignorerar sekretess nivåerna och kan förbättra prestandan**.

Dessutom kan du få ett meddelande där du uppmanas att ange hur du vill ansluta till API: et.

![En bild som visar en begäran om att ange autentiseringsuppgifter](../media/tutorials/edit-credentials-message.png)

Åtgärda detta genom att klicka på **Redigera autentiseringsuppgifter** i meddelandet. När dialog rutan visas väljer du **Anonym** för att ansluta till API: et anonymt. Klicka sedan på **Anslut**.

Klicka sedan på **stäng & tillämpa** i menyfliksområdet **Start** för att tillämpa ändringarna.

## <a name="visualize-the-anomaly-detector-api-response"></a>Visualisera API-svar för avvikelse detektor

Börja använda frågorna som skapats ovan för att visualisera data på skärmen main Power BI. Välj först **linje diagram** i **visualiseringar**. Lägg sedan till tidsstämpeln från den anropade funktionen till linje diagrammets **axel**. Högerklicka på den och välj **tidsstämpel**.

![Högerklicka på värdet för tidsstämpeln](../media/tutorials/timestamp-right-click.png)

Lägg till följande fält från den **anropade funktionen** i diagrammets fält **värden** . Använd skärm bilden nedan för att bygga ditt diagram.

* Värde
* UpperMargins
* LowerMargins
* ExpectedValues

![En bild av diagrammets inställningar](../media/tutorials/chart-settings.png)

När du har lagt till fälten klickar du på diagrammet och ändrar storlek på det för att visa alla data punkter. Diagrammet kommer att se ut ungefär som på skärm bilden nedan:

![En bild av diagrammets visualisering](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Visa avvikelse data punkter

Till höger i Power BIs fönstret, under fönstret **fält** , högerklicka på **värde** under **frågan anropad funktion** och klicka på **nytt snabb mått**.

![En bild av den nya snabb mått skärmen](../media/tutorials/new-quick-measure.png)

På skärmen som visas väljer du **filtrerat värde** som beräkning. Ange **bas värde** till `Sum of Value` . Dra sedan `IsAnomaly` från de **anropade funktions** fälten för att **filtrera**. Välj `True` i den nedrullningsbara menyn **filter** .

![En andra bild av den nya snabb måtts skärmen](../media/tutorials/new-quick-measure-2.png)

När du har klickat på **OK** har du ett `Value for True` fält längst ned i listan över dina fält. Högerklicka på den och Byt namn på den till **avvikelse**. Lägg till den i diagrammets **värden**. Välj sedan **format** verktyget och ange X-axelns typ till **kategoriska**.

![En bild av formatet x-axel](../media/tutorials/format-x-axis.png)

Använd färger i diagrammet genom att klicka på **format** verktyget och **data färger**. Diagrammet bör se ut ungefär så här:

![En bild av det slutliga diagrammet](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Strömma avvikelse identifiering med Azure Databricks](../overview.md)