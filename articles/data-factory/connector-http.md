---
title: Kopiera data från en HTTP-källa med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från en moln-eller lokal HTTP-källa till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: f3184602bad8aabf654c8fa94d33372d08c11a66
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573208"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Kopiera data från en HTTP-slutpunkt med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-http-connector.md)
> * [Aktuell version](connector-http.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från en HTTP-slutpunkt. Artikeln bygger på [kopierings aktivitet i Azure Data Factory](copy-activity-overview.md), som visar en översikt över kopierings aktiviteten.

Skillnaden mellan den här HTTP-kopplingen, [rest-kopplingen](connector-rest.md) och [webb tabell anslutningen](connector-web-table.md) är:

- **Rest Connector** har stöd för att kopiera data från RESTful-API: er; 
- **Http-anslutningen** är generisk för att hämta data från alla http-slutpunkter, t. ex. för att hämta filen. Innan REST Connector blir tillgängligt kan det hända att du använder HTTP-anslutningen för att kopiera data från RESTful-API, som stöds men mindre funktions jämförelse med REST Connector.
- **Webb tabells koppling** extraherar tabell innehåll från en HTML-webbsida.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här HTTP-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från en HTTP-källa till alla mottagar data lager som stöds. En lista över data lager som kopierings aktiviteten stöder som källor och mottagare finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Du kan använda den här HTTP-anslutningen för att:

- Hämta data från en HTTP/S-slutpunkt med hjälp av metoderna HTTP **Get** eller **post** .
- Hämta data genom att använda någon av följande autentiseringar: **Anonym**, **grundläggande**, **Sammanfattad**, **Windows** eller **ClientCertificate**.
- Kopiera HTTP-svaret i befintligt skick eller parsa det genom att använda [fil format och komprimerings-codec som stöds](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Om du vill testa en HTTP-begäran för data hämtning innan du konfigurerar HTTP-anslutningen i Data Factory kan du läsa om API-specifikationen för sidhuvuds-och text krav. Du kan använda verktyg som Postman eller webbläsare för att validera.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory entiteter som är speciella för HTTP-anslutningen.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade HTTP-tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **HttpServer**. | Yes |
| url | Bas-URL: en till webb servern. | Yes |
| enableServerCertificateValidation | Ange om du vill aktivera verifiering av Server-TLS/SSL-certifikat när du ansluter till en HTTP-slutpunkt. Om HTTPS-servern använder ett självsignerat certifikat ställer du in den här egenskapen på **falskt**. | No<br /> (Standardvärdet är **Sant**) |
| authenticationType | Anger autentiseringstypen. Tillåtna värden är **Anonym**, **Basic**, **Digest**, **Windows** och **ClientCertificate**. <br><br> Se de avsnitt som följer den här tabellen för fler egenskaper och JSON-exempel för de här typerna av autentisering. | Yes |
| connectVia | [Integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |No |

### <a name="using-basic-digest-or-windows-authentication"></a>Använda Basic-, Digest-eller Windows-autentisering

Ange egenskapen **authenticationType** som **Basic**, **Digest** eller **Windows**. Förutom de allmänna egenskaper som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| userName | Användar namnet som används för att få åtkomst till HTTP-slutpunkten. | Yes |
| password | Användarens lösen ord (värdet **username** ). Markera det här fältet som en **SecureString** -typ för att lagra det på ett säkert sätt i Data Factory. Du kan också [referera till en hemlighet som lagrats i Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

**Exempel**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Använda ClientCertificate-autentisering

Om du vill använda ClientCertificate-autentisering ställer du in egenskapen **authenticationType** på **ClientCertificate**. Förutom de allmänna egenskaper som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| embeddedCertData | Base64-kodade certifikat data. | Ange antingen **embeddedCertData** eller **certThumbprint**. |
| certThumbprint | Tumavtrycket för det certifikat som är installerat på din egen värd Integration Runtime datorns certifikat arkiv. Gäller endast när den egen värdbaserade typen av Integration Runtime anges i egenskapen **connectVia** . | Ange antingen **embeddedCertData** eller **certThumbprint**. |
| password | Lösen ordet som är associerat med certifikatet. Markera det här fältet som en **SecureString** -typ för att lagra det på ett säkert sätt i Data Factory. Du kan också [referera till en hemlighet som lagrats i Azure Key Vault](store-credentials-in-key-vault.md). | No |

Om du använder **certThumbprint** för autentisering och certifikatet är installerat i det personliga arkivet på den lokala datorn ger du Läs behörighet till den lokala datorn integration Runtime:

1. Öppna Microsoft Management Console (MMC). Lägg till snapin-modulen **certifikat** som är riktad mot den **lokala datorn**.
2. Expandera **certifikat**  >  **personliga** och välj sedan **certifikat**.
3. Högerklicka på certifikatet i det personliga arkivet och välj sedan **alla aktiviteter**  >  **hantera privata nycklar**.
3. På fliken **säkerhet** lägger du till det användar konto som integration runtime värd tjänsten (dia Host service) körs under, med Läs behörighet till certifikatet.

**Exempel 1: använda certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 2: använda embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för HTTP under `location` Inställningar i format-baserad data mängd:

| Egenskap    | Beskrivning                                                  | Krävs |
| ----------- | ------------------------------------------------------------ | -------- |
| typ        | Typ egenskapen under `location` i data mängden måste anges till **HttpServerLocation**. | Yes      |
| relativeUrl | En relativ URL till den resurs som innehåller data. HTTP-anslutningen kopierar data från den kombinerade URL: en: `[URL specified in linked service][relative URL specified in dataset]` .   | No       |

> [!NOTE]
> Den begärda nytto Last storleken för HTTP-begäran är cirka 500 KB. Om den nytto Last storlek som du vill skicka till webb slut punkten är större än 500 KB bör du överväga att lägga till nytto lasten i mindre segment.

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera aktivitets egenskaper

Det här avsnittet innehåller en lista över egenskaper som HTTP-källan stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP som källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för HTTP under `storeSettings` Inställningar i format-baserad kopierings Källa:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Typ egenskapen under `storeSettings` måste anges till **HttpReadSettings**. | Yes      |
| requestMethod            | HTTP-metoden. <br>Tillåtna värden är **Get** (standard) och **post**. | No       |
| additionalHeaders         | Ytterligare rubriker för HTTP-begäran.                             | No       |
| requestBody              | Bröd texten för HTTP-begäran.                               | No       |
| httpRequestTimeout           | Timeout ( **TimeSpan** -värdet) för http-begäran för att få ett svar. Det här värdet är tids gränsen för att få ett svar, inte tids gränsen för att läsa svars data. Standardvärdet är **00:01:40**. | No       |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | No       |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSettings",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Äldre modeller

>[!NOTE]
>Följande modeller stöds fortfarande för bakåtkompatibilitet. Du rekommenderar att du använder den nya modellen som anges ovan och fortsätter, och redigerings gränssnittet för ADF har växlat till att generera den nya modellen.

### <a name="legacy-dataset-model"></a>Äldre data uppsättnings modell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens **typ** -egenskap måste anges till **HttpFile**. | Yes |
| relativeUrl | En relativ URL till den resurs som innehåller data. När den här egenskapen inte anges används endast den URL som anges i den länkade tjänst definitionen. | No |
| requestMethod | HTTP-metoden. Tillåtna värden är **Get** (standard) och **post**. | No |
| additionalHeaders | Ytterligare rubriker för HTTP-begäran. | No |
| requestBody | Bröd texten för HTTP-begäran. | No |
| format | Om du vill hämta data från HTTP-slutpunkten i befintligt skick utan att parsa den, och sedan kopiera data till en filbaserad lagring, hoppar du över avsnittet **format** i både indata och utdata-datauppsättnings definitioner.<br/><br/>Om du vill parsa innehållet i HTTP-svar under kopieringen stöds följande fil format **typer: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat** och **ParquetFormat**. Under **format** anger du egenskapen **Type** till något av dessa värden. Mer information finns i [JSON-format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [text format](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format)-format, Orc- [format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parquet-format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |No |
| komprimering | Ange typ och nivå för komprimeringen för data. Mer information finns i [fil format och komprimerings-codecar som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/><br/>Typer som stöds: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**.<br/>Nivåer som stöds:  **optimal** och **snabbast**. |No |

> [!NOTE]
> Den begärda nytto Last storleken för HTTP-begäran är cirka 500 KB. Om den nytto Last storlek som du vill skicka till webb slut punkten är större än 500 KB bör du överväga att lägga till nytto lasten i mindre segment.

**Exempel 1: använda Get-metoden (standard)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Exempel 2: använda post-metoden**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Käll modell för äldre kopierings aktiviteter

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **HttpSource**. | Yes |
| httpRequestTimeout | Timeout ( **TimeSpan** -värdet) för http-begäran för att få ett svar. Det här värdet är tids gränsen för att få ett svar, inte tids gränsen för att läsa svars data. Standardvärdet är **00:01:40**.  | No |

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg

En lista över data lager som kopierings aktiviteten stöder som källor och handfat i Azure Data Factory finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
