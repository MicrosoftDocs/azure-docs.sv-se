---
title: Webb aktivitet i Azure Data Factory
description: Lär dig hur du kan använda webb aktivitet, en av de kontroll flödes aktiviteter som stöds av Data Factory, för att anropa en REST-slutpunkt från en pipeline.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: e4578b41e5cbb62c8a1bfa0c48d4fd60d042a506
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361527"
---
# <a name="web-activity-in-azure-data-factory"></a>Webb aktivitet i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Webbaktiviteten kan används till att anropa en anpassad REST-slutpunkt från en Data Factory-pipeline. Du kan överföra datauppsättningar och länkade tjänster så att de förbrukas och används av aktiviteten.

> [!NOTE]
> Webbaktivitet har stöd för anrop av URL:er som finns i ett privat virtuellt nätverk och användning av lokalt installerad integrationskörning. Integrationskörningen bör fri sikt till URL-slutpunkten. 

> [!NOTE]
> Den högsta nytto Last storleken för utmatnings svar är 4 MB.  

## <a name="syntax"></a>Syntax

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "connectVia": {
          "referenceName": "<integrationRuntimeName>",
          "type": "IntegrationRuntimeReference"
      }
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Tillåtna värden | Obligatorisk
-------- | ----------- | -------------- | --------
name | Namn på webb aktiviteten | Sträng | Ja
typ | Måste vara inställt på **webactivity**. | Sträng | Ja
metod | REST API-metod för mål slut punkten. | Sträng. <br/><br/>Typer som stöds: "GET", "POST", "placera" | Ja
url | Mål slut punkt och sökväg | Sträng (eller uttryck med resultType för sträng). Aktiviteten avbryts efter 1 minut med ett fel om den inte får något svar från slutpunkten. | Ja
sidhuvud | Huvuden som skickas till begäran. Om du till exempel vill ange språk och typ på en begäran: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` . | Sträng (eller uttryck med resultType för sträng) | Ja, innehålls typ rubrik krävs. `"headers":{ "Content-Type":"application/json"}`
body | Representerar den nytto last som skickas till slut punkten.  | Sträng (eller uttryck med resultType för sträng). <br/><br/>Se schemat för nytto lasten för begäran i [nytto Last schema](#request-payload-schema) avsnittet. | Krävs för metoderna POST/infört.
autentisering | Autentiseringsmetod som används för att anropa slut punkten. Typer som stöds är Basic eller ClientCertificate. Mer information finns i avsnittet [Authentication](#authentication) . Om autentisering inte krävs utelämnar du den här egenskapen. | Sträng (eller uttryck med resultType för sträng) | Inga
datauppsättningar | Lista över data uppsättningar som skickats till slut punkten. | Matris med data uppsättnings referenser. Kan vara en tom matris. | Ja
linkedServices | Lista över länkade tjänster som skickats till slut punkten. | Matris med länkade tjänst referenser. Kan vara en tom matris. | Ja
connectVia | [Integrerings körningen](./concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure integration runtime eller den lokala integrerings körningen (om ditt data lager finns i ett privat nätverk). Om den här egenskapen inte anges använder tjänsten standard Azure integration Runtime. | Referens för integration Runtime. | Inga 

> [!NOTE]
> REST-slutpunkter som webbaktiviteten anropar måste returnera ett svar av typen JSON. Aktiviteten avbryts efter 1 minut med ett fel om den inte får något svar från slutpunkten.

I följande tabell visas kraven för JSON-innehåll:

| Värdetyp | Begärandetext | Själva svaret |
|---|---|---|
|JSON-objekt | Stöds | Stöds |
|JSON-matris | Stöds <br/>(För närvarande fungerar JSON-matriser inte som ett resultat av ett fel. En korrigering pågår.) | Stöd saknas |
| JSON-värde | Stöds | Stöd saknas |
| Icke-JSON-typ | Stöd saknas | Stöd saknas |
||||

## <a name="authentication"></a>Autentisering

Nedan visas autentiseringstyper som stöds i webb aktiviteten.

### <a name="none"></a>Inget

Om autentisering inte krävs inkluderar du inte egenskapen "Authentication".

### <a name="basic"></a>Grundläggande

Ange användar namn och lösen ord som ska användas med grundläggande autentisering.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Klient certifikat

Ange Base64-kodat innehåll för en PFX-fil och lösen ordet.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Hanterad identitet

Ange resurs-URI för vilken åtkomsttoken ska begäras med hjälp av den hanterade identiteten för data fabriken. Använd för att anropa Azure Resource Management-API: et `https://management.azure.com/` . Mer information om hur hanterade identiteter fungerar finns på [översikts sidan hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Om din data fabrik har kon figurer ATS med en git-lagringsplats måste du lagra dina autentiseringsuppgifter i Azure Key Vault för att kunna använda Basic eller autentisering med klient certifikat. Azure Data Factory lagrar inte lösen ord i git.

## <a name="request-payload-schema"></a>Schema för begäran om nytto Last
När du använder metoden POST/placering representerar egenskapen Body den nytto last som skickas till slut punkten. Du kan skicka länkade tjänster och data uppsättningar som en del av nytto lasten. Här är schemat för nytto lasten:

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>Exempel
I det här exemplet anropar webb aktiviteten i pipelinen en REST-slutpunkt. Den skickar en länkad Azure SQL-tjänst och en Azure SQL-datauppsättning till slut punkten. RESTEN av slut punkten använder Azure SQL-anslutningssträngen för att ansluta till den logiska SQL-servern och returnerar namnet på SQL Server-instansen.

### <a name="pipeline-definition"></a>Pipeline-definition

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Parameter värden för pipeline

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Slut punkts kod för webb tjänst

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Nästa steg
Se andra kontroll flödes aktiviteter som stöds av Data Factory:

- [Köra pipelineaktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
