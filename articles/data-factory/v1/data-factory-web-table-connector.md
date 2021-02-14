---
title: Flytta data från en webb tabell med Azure Data Factory
description: Lär dig mer om hur du flyttar data från en tabell på en webb sida med hjälp av Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7663848bd06244de7efb169a576e11a5c78204c1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392909"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Flytta data från en webb tabell källa med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-web-table-connector.md)
> * [Version 2 (aktuell version)](../connector-web-table.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [webb tabells koppling i v2](../connector-web-table.md).

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data från en tabell på en webb sida till ett mottagar data lager som stöds. Den här artikeln bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) som visar en allmän översikt över data förflyttning med kopierings aktivitet och listan över data lager som stöds som källor/mottagare.

Data Factory har för närvarande endast stöd för att flytta data från en webb tabell till andra data lager, men inte flytta data från andra data lager till ett webb tabell mål.

> [!IMPORTANT]
> Denna webb anslutning stöder för närvarande endast extrahering av tabell innehåll från en HTML-sida. Om du vill hämta data från en HTTP/s-slutpunkt använder du [http-anslutning](data-factory-http-connector.md) i stället.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här webb tabell anslutningen måste du konfigurera en egen värd Integration Runtime (aka Data Management Gateway) och konfigurera `gatewayName` egenskapen i den länkade mottagar tjänsten. Om du till exempel vill kopiera från webb tabellen till Azure Blob Storage konfigurerar du Azure Storage länkade tjänsten som följer:

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data från ett lokalt Cassandra data lager med hjälp av olika verktyg/API: er. 

- Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data. 
- Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden. 
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata. 

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet.  Ett exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data från en webb tabell finns i [JSON-exempel: kopiera data från webb tabellen till Azure Blob](#json-example-copy-data-from-web-table-to-azure-blob) i den här artikeln. 

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för en webb tabell:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
Följande tabell innehåller en beskrivning av JSON-element som är speciella för länkad webb tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **webb** |Ja |
| URL |URL till webb adressen |Ja |
| authenticationType |Anonym. |Ja |

### <a name="using-anonymous-authentication"></a>Använda anonym autentisering

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar alla typer av data uppsättningar (Azure SQL, Azure Blob, Azure Table osv.).

Avsnittet **typeProperties** är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet typeProperties för data uppsättning av typen **webtable** har följande egenskaper

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |typ av data uppsättning. måste vara inställd på **Webtable** |Ja |
| path |En relativ URL till den resurs som innehåller tabellen. |Nej. Om ingen sökväg anges används endast den URL som angavs i den länkade tjänst definitionen. |
| index |Indexet för tabellen i resursen. Se avsnittet [Hämta index för en tabell i en HTML-sida](#get-index-of-a-table-in-an-html-page) för steg för att hämta index för en tabell på en HTML-sida. |Ja |

**Exempel:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utdata-tabeller och policy är tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

För närvarande stöds inte ytterligare egenskaper när källan i kopierings aktiviteten är av typen **Websource**.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON-exempel: kopiera data från webb tabell till Azure-Blob
Följande exempel visar:

1. En länkad tjänst av typen [Web](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [webtable](#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [Websource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en webb tabell till en Azure-Blob varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

Följande exempel visar hur du kopierar data från en webb tabell till en Azure-blob. Data kan dock kopieras direkt till någon av de handfat som anges i artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) med hjälp av kopierings aktiviteten i Azure Data Factory.

**Länkad webb tjänst** I det här exemplet används den länkade webb tjänsten med anonym autentisering. Se avsnittet [webb länkad tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Azure Storage länkad tjänst**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

Data **uppsättning för Webtable-indata** Om du anger **external** till **True** informerar Data Factory tjänsten att data uppsättningen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

> [!NOTE]
> Se avsnittet [Hämta index för en tabell i en HTML-sida](#get-index-of-a-table-in-an-html-page) för steg för att hämta index för en tabell på en HTML-sida.  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Utdatauppsättning för Azure-blob**

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**Pipeline med kopierings aktivitet**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen har **käll** typen angetts till **Websource** och **mottagar** typ är inställd på **BlobSink**.

Se egenskaper för Websource-typ för listan över egenskaper som stöds av Websource.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Hämta index för en tabell i en HTML-sida
1. Starta **Excel 2016** och växla till fliken **data** .  
2. Klicka på **ny fråga** i verktygsfältet, peka på **från andra källor** och klicka på **från webben**.

    ![Power Query-menyn](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. I dialog rutan **från webben** anger du den **URL** som du skulle använda i länkad tjänst-JSON (till exempel: https://en.wikipedia.org/wiki/) tillsammans med sökvägen som du anger för data UPPSÄTTNINGEN (till exempel: AFI% 27s_100_Years... 100_Movies) och klicka på **OK**.

    ![Från webben-dialog](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    URL som används i det här exemplet: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Om dialog rutan **Öppna webb innehåll** visas väljer du rätt **URL**, **autentisering** och klickar på **Anslut**.

   ![Dialog rutan för att komma åt webb innehåll](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Klicka på ett **tabell** objekt i trädvyn om du vill visa innehåll från tabellen och klicka sedan på knappen **Redigera** längst ned.  

   ![Navigeringsdialogruta](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. I fönstret **Frågeredigeraren** klickar du på **avancerad redigerare** knapp i verktygsfältet.

    ![Avancerad redigerare knapp](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. I dialog rutan Avancerad redigerare är talet intill "källa" indexet.

    ![Avancerad redigerare-index](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Om du använder Excel 2013 använder du [Microsoft Power Query för Excel](https://www.microsoft.com/download/details.aspx?id=39379) för att hämta indexet. Mer information finns i artikeln om [att ansluta till en webb sida](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) . Stegen är liknande om du använder [Microsoft Power BI för skriv bord](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Information om hur du mappar kolumner från käll data uppsättning till kolumner från mottagar data uppsättningen finns [i mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.
