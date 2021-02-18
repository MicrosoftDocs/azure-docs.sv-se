---
title: Använd API för data insamling för att skapa en datapipeline
description: 'Du kan använda API: et Azure Monitor HTTP-datainsamling för att lägga till POST-JSON-data till arbets ytan Log Analytics från vilken klient som helst som kan anropa REST API. Den här artikeln beskriver hur du överför data som lagras i filer på ett automatiserat sätt.'
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: ac2b79046718fe45ad0dad0396b6f7653efbb779
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624257"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Skapa en datapipeline med data insamlings-API: et

Med [API: et för Azure Monitor data insamling](data-collector-api.md) kan du importera alla anpassade loggdata till en Log Analytics arbets yta i Azure Monitor. De enda kraven är att data är JSON-formaterade och delas upp i 30 MB eller färre segment. Det här är en helt flexibel mekanism som kan anslutas till på många sätt: från data som skickas direkt från ditt program till en-av adhoc-uppladdningar. Den här artikeln beskriver några start punkter för ett vanligt scenario: behovet att ladda upp data som lagras i filer regelbundet. Även om den pipeline som visas här inte är den mest utförda eller på annat sätt optimerat, är den avsedd att fungera som en utgångs punkt för att skapa en produktions pipeline på egen hand.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Exempel problem
I resten av den här artikeln kommer vi att undersöka sid visnings data i Application Insights. I vårt hypotetiska scenario vill vi korrelera geografisk information som samlas in som standard av Application Insights SDK för att anpassa data som innehåller populationen för varje land/region i världen, med målet att identifiera var vi bör spendera de flesta marknads dollar. 

Vi använder en offentlig data källa, till exempel de kunder som inte är [världs befolkning](https://esa.un.org/unpd/wpp/) för detta ändamål. Data kommer att ha följande enkla schema:

![Exempel på enkelt schema](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

I vårt exempel antar vi att vi kommer att ladda upp en ny fil med de senaste årets data så snart den blir tillgänglig.

## <a name="general-design"></a>Allmän design
Vi använder en klassisk ETL-typ logik för att utforma vår pipeline. Arkitekturen kommer att se ut så här:

![Pipeline-arkitektur för data insamling](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Den här artikeln beskriver inte hur du skapar data eller [laddar upp det till ett Azure Blob Storage-konto](../../storage/blobs/storage-upload-process-images.md). I stället väljer vi flödet upp så fort en ny fil överförs till blobben. Härifrån:

1. En process identifierar att nya data har laddats upp.  I vårt exempel används en [Azure Logic-app](../../logic-apps/logic-apps-overview.md)som har en utlösare för att identifiera nya data som överförs till en blob.

2. En processor läser in nya data och konverterar den till JSON, det format som krävs av Azure Monitor i det här exemplet använder vi en [Azure-funktion](../../azure-functions/functions-overview.md) som ett lätt och kostnads effektivt sätt att köra vår bearbetnings kod. Funktionen startas av samma Logic-app som vi använde för att identifiera nya data.

3. Slutligen när JSON-objektet är tillgängligt skickas det till Azure Monitor. Samma Logic-App skickar data till Azure Monitor med hjälp av den inbyggda aktiviteten Log Analytics data insamlare.

Även om den detaljerade konfigurationen av blob-lagringen, Logic app eller Azure-funktionen inte beskrivs i den här artikeln finns detaljerade instruktioner på sidan specifika produkter.

För att övervaka den här pipelinen använder vi Application Insights för att övervaka vår Azure Function- [information](../../azure-functions/functions-monitoring.md)och Azure Monitor för att övervaka vår [information om](../../logic-apps/monitor-logic-apps-log-analytics.md)Logic Apps här. 

## <a name="setting-up-the-pipeline"></a>Konfigurera pipelinen
Om du vill ställa in pipelinen ska du först kontrol lera att din BLOB-behållare har skapats och kon figurer ATS. Kontrol lera också att Log Analytics arbets ytan där du vill skicka data till skapas.

## <a name="ingesting-json-data"></a>Inmatning av JSON-data
Inmatning av JSON-data är trivial med Logic Apps och eftersom ingen omvandling behöver utföras, kan vi ställa hela pipelinen i en enda Logic-app. När både BLOB-behållaren och Log Analytics arbets ytan har kon figurer ATS, skapar du en ny Logic app och konfigurerar den på följande sätt:

![Exempel på Logic Apps-arbetsflöde](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Spara din Logic app och fortsätt att testa den.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Mata in XML, CSV eller andra data format
Logic Apps idag har inte inbyggda funktioner för att enkelt transformera XML, CSV eller andra typer till JSON-format. Därför måste vi använda ett annat sätt för att slutföra den här omvandlingen. I den här artikeln använder vi Server lös beräknings funktioner i Azure Functions som ett mycket lätt och kostnads effektivt sätt att göra det. 

I det här exemplet parsar vi en CSV-fil, men alla andra filtyper kan bearbetas på samma sätt. Ändra bara avserialiserings delen av Azure-funktionen för att visa rätt logik för din särskilda datatyp.

1.  Skapa en ny Azure-funktion med funktionen runtime v1 och förbruknings baserad när du uppmanas till det.  Välj den **http-utlösare** som är riktad mot C# som en start punkt som konfigurerar dina bindningar som vi behöver. 
2.  Från fliken **Visa filer** i den högra rutan skapar du en ny fil med namnet **project.jspå** och klistrar in följande kod från NuGet-paket som vi använder:

    ![Azure Functions exempel projekt](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Växla till **Kör. CSX** från den högra rutan och ersätt standard koden med följande. 

    >[!NOTE]
    >För ditt projekt måste du ersätta post modellen (klassen "PopulationRecord") med ditt eget data schema.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. Spara din funktion.
5. Testa funktionen för att kontrol lera att koden fungerar som den ska. Växla till fliken **test** i den högra rutan och konfigurera testet enligt följande. Placera en länk till en blob med exempel data i text rutan för text i **begäran** . När du har klickat på **Kör** bör du se JSON-utdata i rutan **utdata** :

    ![Test kod för Function Apps](./media/create-pipeline-datacollector-api/functions-test-01.png)

Nu måste vi gå tillbaka och ändra den Logic-app som vi började skapa tidigare för att inkludera data som matas in och konverteras till JSON-format.  Använd View Designer, konfigurera enligt följande och spara sedan din Logic app:

![Logic Apps arbets flöde slutfört exempel](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Testa pipelinen
Nu kan du ladda upp en ny fil till den konfigurerade bloben tidigare och låta den övervakas av din Logic app. Snart bör du se att en ny instans av logi Kap par är inaktive rad, anropar din Azure-funktion och sedan skicka data till Azure Monitor. 

>[!NOTE]
>Det kan ta upp till 30 minuter innan data visas i Azure Monitor första gången du skickar en ny datatyp.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Korrelera med andra data i Log Analytics och Application Insights
För att slutföra vårt mål att korrelera Application Insights sid visning av data med populations data som vi har matat in från vår anpassade data källa, kör du följande fråga från antingen Application Insights Analytics-fönstret eller Log Analytics arbets ytan:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

Utdata ska visa de två data källorna som nu är anslutna.  

![Korrelera kopplade data i ett Sök Resultat exempel](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Föreslagna förbättringar för en produktions pipeline
Den här artikeln presenterade en fungerande prototyp, logiken bakom som kan tillämpas mot en verklig lösning för produktions kvalitet. För en sådan lösning för produktions kvalitet rekommenderas följande förbättringar:

* Lägg till fel hantering och försök att använda logik i din Logic app och funktion.
* Lägg till logik för att säkerställa att 30MB/Single Log Analytics-inmatnings-API-anropet inte överskrids. Dela upp data i mindre segment om det behövs.
* Konfigurera en rensnings princip för Blob Storage. När du har skickat till Log Analytics arbets ytan, såvida du inte vill behålla rå data tillgängliga för arkivering, finns det ingen anledning att fortsätta att lagra den. 
* Kontrol lera att övervakning är aktiverat i hela pipeline och Lägg till spårnings punkter och aviseringar efter behov.
* Utnyttjar käll kontroll för att hantera koden för din funktion och din Logic app.
* Se till att en korrekt ändrings hanterings princip följs, så att om schemat ändras ändras funktionen och Logic Apps i enlighet med detta.
* Om du överför flera olika data typer, åtskiljer du dem i enskilda mappar i BLOB-behållaren och skapar logik för att utnyttja logiken utifrån data typen. 


## <a name="next-steps"></a>Nästa steg
Läs mer om  [API: et för data insamling](data-collector-api.md) för att skriva Data till Log Analytics arbets ytan från valfri REST API-klient.
