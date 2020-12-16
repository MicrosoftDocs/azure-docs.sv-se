---
title: Flytta data från en HTTP-källa – Azure
description: Lär dig hur du flyttar data från en lokal eller moln-HTTP-källa med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ddf8236dbbc9714c705e442bb65eb2ac3d293cc7
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589584"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Flytta data från en HTTP-källa med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-http-connector.md)
> * [Version 2 (aktuell version)](../connector-http.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Azure Data Factory, se [http Connector i v2](../connector-http.md).


Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att flytta data från en lokal eller moln-HTTP-slutpunkt till ett mottagar data lager som stöds. Den här artikeln bygger på [Flytta data med hjälp av kopierings aktivitet](data-factory-data-movement-activities.md), som visar en allmän översikt över data flyttningen med hjälp av kopierings aktiviteten. Artikeln innehåller även de data lager som kopierings aktiviteten stöder som källor och mottagare.

Data Factory stöder för närvarande endast flytt av data från en HTTP-källa till andra data lager. Det går inte att flytta data från andra data lager till ett HTTP-mål.

## <a name="supported-scenarios-and-authentication-types"></a>Scenarier och autentiseringstyper som stöds

Du kan använda den här HTTP-anslutningen för att hämta data från *både ett moln och en lokal http/S-slutpunkt* med hjälp av metoderna http **Get** eller **post** . Följande autentiseringstyper stöds: **Anonym**, **Basic**, **Digest**, **Windows** och **ClientCertificate**. Observera skillnaden mellan den här kopplingen och [webb tabell anslutningen](data-factory-web-table-connector.md). Webb tabells kopplingen extraherar tabell innehåll från en HTML-webbsida.

När du kopierar data från en lokal HTTP-slutpunkt måste du installera Data Management Gateway i den lokala miljön eller i en virtuell Azure-dator. Mer information om Data Management Gateway och stegvisa anvisningar om hur du konfigurerar gatewayen finns i [Flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Kom igång

Du kan skapa en pipeline med en kopierings aktivitet för att flytta data från en HTTP-källa med hjälp av olika verktyg eller API: er:

- Det enklaste sättet att skapa en pipeline är att använda guiden Kopiera data. En snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data finns i [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).

- Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, en **Azure Resource Manager mall**, **.NET-API** eller **REST API**. Stegvisa instruktioner om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). För JSON-exempel som kopierar data från en HTTP-källa till Azure Blob Storage, se [JSON-exempel](#json-examples).

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

I följande tabell beskrivs JSON-element som är speciella för den länkade HTTP-tjänsten:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ | Egenskapen **Type** måste anges till **http**. | Yes |
| url | Bas-URL: en till webb servern. | Yes |
| authenticationType | Anger autentiseringstypen. Tillåtna värden är **Anonym**, **Basic**, **Digest**, **Windows** och **ClientCertificate**. <br><br> I senare avsnitt i den här artikeln hittar du fler egenskaper och JSON-exempel för de här typerna av autentisering. | Yes |
| enableServerCertificateValidation | Anger om du vill aktivera verifiering av Server-TLS/SSL-certifikat om källan är en HTTPS-webbserver. När HTTPS-servern använder ett självsignerat certifikat ställer du in på **false**. | No<br /> (Standardvärdet är **Sant**) |
| gatewayName | Namnet på Data Management Gateway-instansen som ska användas för att ansluta till en lokal HTTP-källa. | Ja, om du kopierar data från en lokal HTTP-källa |
| encryptedCredential | Krypterade autentiseringsuppgifter för åtkomst till HTTP-slutpunkten. Värdet genereras automatiskt när du konfigurerar autentiseringsinformationen i kopierings guiden eller med hjälp av dialog rutan **ClickOnce** . | No<br /> (gäller endast när du kopierar data från en lokal HTTP-server) |

Information om hur du anger autentiseringsuppgifter för en lokal HTTP-kopplings data källa finns i [Flytta data mellan lokala källor och molnet med hjälp av data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Använda Basic-, Digest-eller Windows-autentisering

Ange **authenticationType** som **Basic**, **Digest** eller **Windows**. Förutom de allmänna HTTP-kopplingens egenskaper som beskrivs i föregående avsnitt, anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| userName | Användar namnet som används för att få åtkomst till HTTP-slutpunkten. | Yes |
| password | Användarens lösen ord (**användar namn**). | Yes |

**Exempel: använda Basic-, Digest-eller Windows-autentisering**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Använda ClientCertificate-autentisering

Om du vill använda grundläggande autentisering anger du **authenticationType** till **ClientCertificate**. Förutom de allmänna HTTP-kopplingens egenskaper som beskrivs i föregående avsnitt, anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| embeddedCertData | Det Base64-kodade innehållet i binära data i PFX-filen. | Ange antingen **embeddedCertData** eller **certThumbprint** |
| certThumbprint | Tumavtrycket för det certifikat som har installerats på din gateway-dators certifikat arkiv. Gäller endast när du kopierar data från en lokal HTTP-källa. | Ange antingen **embeddedCertData** eller **certThumbprint** |
| password | Lösen ordet som är associerat med certifikatet. | No |

Om du använder **certThumbprint** för autentisering och certifikatet är installerat i det personliga arkivet på den lokala datorn ger du Läs behörighet till Gateway-tjänsten:

1. Öppna Microsoft Management Console (MMC). Lägg till snapin-modulen **certifikat** som är riktad mot den **lokala datorn**.
2. Expandera **certifikat**  >  **personliga** och välj sedan **certifikat**.
3. Högerklicka på certifikatet i det personliga arkivet och välj sedan **alla aktiviteter**  > **hantera privata nycklar**.
3. På fliken **säkerhet** lägger du till det användar konto som data Management Gateway värd tjänsten körs under, med Läs behörighet till certifikatet.  

**Exempel: använda ett klient certifikat**

Den här länkade tjänsten länkar din data fabrik till en lokal HTTP-webbserver. Det använder ett klient certifikat som är installerat på den dator som har Data Management Gateway installerat.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
        "certThumbprint": "thumbprint of certificate",
        "gatewayName": "gateway name"

        }
    }
}
```

**Exempel: använda ett klient certifikat i en fil**

Den här länkade tjänsten länkar din data fabrik till en lokal HTTP-webbserver. Den använder en klient certifikat fil på den dator som har Data Management Gateway installerat.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
        "embeddedCertData": "Base64-encoded cert data",
        "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

Vissa delar av en data uppsättnings JSON-fil, till exempel struktur, tillgänglighet och princip, liknar alla typer av data uppsättningar (Azure SQL Database, Azure Blob Storage, Azure Table Storage).

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [skapa data uppsättningar](data-factory-create-datasets.md).

Avsnittet **typeProperties** är olika för varje typ av data uppsättning. Avsnittet **typeProperties** innehåller information om platsen för data i data lagret. Avsnittet **typeProperties** för en data uppsättning av **http-** typen har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens **typ** måste anges till **http**. | Yes |
| relativeUrl | En relativ URL till den resurs som innehåller data. När sökvägen inte anges används bara den URL som anges i den länkade tjänst definitionen. <br><br> Om du vill skapa en dynamisk URL kan du använda [Data Factory funktioner och systemvariabler](data-factory-functions-variables.md). Exempel: **relativeUrl**: **$ $text. format ('/My/Report? month = {0: åååå}-{0: mm} &fmt = CSV ', SliceStart)**. | No |
| requestMethod | HTTP-metoden. Tillåtna värden är **Get** och **post**. | No <br />(standard är **Get**) |
| additionalHeaders | Ytterligare rubriker för HTTP-begäran. | No |
| requestBody | Bröd texten för HTTP-begäran. | No |
| format | Om du vill *Hämta data från en http-slutpunkt som-är* utan att parsa den, hoppar du över **format** inställningen. <br><br> Om du vill parsa innehållet i HTTP-svar under kopieringen, stöds följande format **typer: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat** och **ParquetFormat**. Mer information finns i [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), Orc- [format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet-format](data-factory-supported-file-and-compression-formats.md#parquet-format). |No |
| komprimering | Ange typ och nivå för komprimeringen för data. Typer som stöds: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**. Nivåer som stöds: **optimal** och **snabbast**. Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

**Exempel: använda metoden GET (standard)**

```json
{
  "name": "HttpSourceDataInput",
    "properties": {
    "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
      "relativeUrl": "XXX/test.xml",
        "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
    },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Exempel: använda POST-metoden**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
       "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera aktivitets egenskaper

Egenskaper som namn, beskrivning, indata och utdata-tabeller och policy är tillgängliga för alla typer av aktiviteter.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapa pipeliner](data-factory-create-pipelines.md). 

Egenskaper som är tillgängliga i **typeProperties** -avsnittet av aktiviteten varierar beroende på varje aktivitets typ. För en kopierings aktivitet varierar egenskaperna beroende på typerna av källor och mottagare.

För närvarande stöds följande egenskaper när källan i kopierings aktiviteten är av typen **HttpSource** :

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| httpRequestTimeout | Timeout ( **TimeSpan** -värdet) för http-begäran för att få ett svar. Det är tids gränsen för att få ett svar, inte tids gränsen för att läsa svars data. | No<br />(standardvärde: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimeringsformat som stöds

Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md) .

## <a name="json-examples"></a>JSON-exempel

I följande exempel finns exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). I exemplen visas hur du kopierar data från en HTTP-källa till Azure Blob Storage. Data kan dock kopieras *direkt* från någon av källorna till någon av de handfat [som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopierings aktiviteten i Azure Data Factory.

**Exempel: kopiera data från en HTTP-källa till Azure Blob Storage**

Data Factory-lösningen för det här exemplet innehåller följande Data Factory entiteter:

*   En länkad tjänst av typen [http](#linked-service-properties).
*   En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   En indata- [datauppsättning](data-factory-create-datasets.md) av typen [http](#dataset-properties).
*   En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   En [pipeline](data-factory-create-pipelines.md) som har en kopierings aktivitet som använder [HttpSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en HTTP-källa till en Azure-Blob varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitt som följer exemplen.

### <a name="http-linked-service"></a>Länkad HTTP-tjänst

I det här exemplet används den länkade HTTP-tjänsten med anonym autentisering. Se [http-länkad tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>Data uppsättning för HTTP-indata

Om du anger **external** till **True** informerar Data Factory tjänsten att data uppsättningen är extern i data fabriken och inte skapas av en aktivitet i data fabriken.

```json
{
  "name": "HttpSourceDataInput",
    "properties": {
    "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
        "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
    },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Utdatauppsättning för Azure-blob

Data skrivs till en ny BLOB varje timme (**frekvens**: **timme**, **intervall**: **1**).

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

### <a name="pipeline-that-uses-a-copy-activity"></a>Pipeline som använder en kopierings aktivitet

Pipelinen innehåller en kopierings aktivitet som är konfigurerad att använda data uppsättningar för indata och utdata. Kopierings aktiviteten är schemalagd att köras varje timma. I JSON-definitionen för pipelinen är **käll** typen inställt på **HttpSource** och **mottagar** typen är inställd på **BlobSink**.

En lista över egenskaper som stöds av **HttpSource** finns i [HttpSource](#copy-activity-properties).

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
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

> [!NOTE]
> Information om hur du mappar kolumner från en käll data uppsättning till kolumner från en Sink-datauppsättning finns [i mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering

Information om viktiga faktorer som påverkar prestandan för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den finns i [guiden Kopiera aktivitets prestanda och justering](data-factory-copy-activity-performance.md).
