---
title: Kopiera data från OData-källor med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från OData-källor till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: jingwang
ms.openlocfilehash: 9dd86b4982edf5d206e64431a5e1458c4b848e9e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968543"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Kopiera data från en OData-källa med hjälp av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-odata-connector.md)
> * [Aktuell version](connector-odata.md)

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från en OData-källa. Artikeln bygger på [kopierings aktivitet i Azure Data Factory](copy-activity-overview.md), som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Denna OData-anslutning stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från en OData-källa till alla mottagar data lager som stöds. En lista över data lager som kopierings aktiviteten stöder som källor och mottagare finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Mer specifikt stöder denna OData-anslutning:

- OData version 3,0 och 4,0.
- Kopiera data med någon av följande autentiseringar: **Anonym**, **grundläggande**, **Windows** och **AAD-tjänstens huvud namn**.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory entiteter som är speciella för en OData-anslutning.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för en OData-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **OData**. |Ja |
| url | OData-tjänstens rot-URL. |Ja |
| authenticationType | Den typ av autentisering som används för att ansluta till OData-källan. Tillåtna värden är **Anonymous**, **Basic**, **Windows** och **AadServicePrincipal**. User-based OAuth stöds inte. Du kan också konfigurera autentiseringsscheman i `authHeader` egenskapen.| Ja |
| authHeaders | Ytterligare HTTP-begärandehuvuden för autentisering.<br/> Om du till exempel vill använda API-autentiseringsnyckel kan du välja autentiseringstyp som "Anonym" och ange API-nyckel i rubriken. | Inga |
| userName | Ange **användar namn** om du använder Basic-eller Windows-autentisering. | Inga |
| password | Ange **lösen ordet** för det användar konto som du har angett för **användar namn**. Markera det här fältet som en **SecureString** -typ för att lagra det på ett säkert sätt i Data Factory. Du kan också [referera till en hemlighet som lagrats i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| servicePrincipalId | Ange det Azure Active Directory programmets klient-ID. | Inga |
| aadServicePrincipalCredentialType | Ange vilken typ av autentiseringsuppgift som ska användas för autentisering av tjänstens huvud namn. Tillåtna värden är: `ServicePrincipalKey` eller `ServicePrincipalCert` . | Inga |
| servicePrincipalKey | Ange Azure Active Directory programmets nyckel. Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| servicePrincipalEmbeddedCert | Ange det Base64-kodade certifikatet för ditt program registrerat i Azure Active Directory. Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| servicePrincipalEmbeddedCertPassword | Ange lösen ordet för ditt certifikat om ditt certifikat är skyddat med ett lösen ord. Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md).  | Inga|
| tenant | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Inga |
| aadResourceId | Ange den AAD-resurs som du begär för auktorisering.| Inga |
| azureCloudType | För tjänstens huvud namns autentisering anger du vilken typ av Azure-moln miljö som ditt AAD-program är registrerat på. <br/> Tillåtna värden är **AzurePublic**, **AzureChina**, **azureusgovernment eller** och **AzureGermany**. Som standard används data fabrikens moln miljö. | Inga |
| connectVia | [Integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Inga |

**Exempel 1: använda anonym autentisering**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 2: använda grundläggande autentisering**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
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

**Exempel 3: använda Windows-autentisering**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

**Exempel 4: använda autentisering av tjänstens huvud namn**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Exempel 5: använda autentisering av tjänstens huvud certifikat**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Exempel 6: använda autentisering med API-nyckel**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "APIKey": {
                    "type": "SecureString",
                    "value": "<API key>"
                }
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

Det här avsnittet innehåller en lista över egenskaper som stöds av OData-datauppsättningen.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar och länkade tjänster](concepts-datasets-linked-services.md). 

Om du vill kopiera data från OData ställer du in egenskapen **Type** för data uppsättningen på **ODataResource**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens **typ** -egenskap måste anges till **ODataResource**. | Ja |
| path | Sökvägen till OData-resursen. | Ja |

**Exempel**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera aktivitets egenskaper

Det här avsnittet innehåller en lista över egenskaper som OData-källan stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData as-källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** för att kopiera data från OData:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **ODataSource**. | Ja |
| DocumentDB | OData-frågealternativ för att filtrera data. Exempel: `"$select=Name,Description&$top=5"`.<br/><br/>**Obs!** OData-kopplingen kopierar data från den kombinerade URL: en: `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]` . Mer information finns i [OData URL-komponenter](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Inga |
| httpRequestTimeout | Timeout ( **TimeSpan** -värdet) för http-begäran för att få ett svar. Det här värdet är tids gränsen för att få ett svar, inte tids gränsen för att läsa svars data. Om inget värde anges är standardvärdet **00:30:00** (30 minuter). | Inga |

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du använder `RelationalSource` typ av källa, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya vägen framåt.

## <a name="data-type-mapping-for-odata"></a>Data typs mappning för OData

När du kopierar data från OData används följande mappningar mellan OData-datatyper och Azure Data Factory interimistiska data typer. Information om hur kopierings aktiviteten mappar käll schema och data typ till mottagaren finns i [schema-och data typs mappningar](copy-activity-schema-and-type-mapping.md).

| OData-datatyp | Data Factory data typen Interim |
|:--- |:--- |
| EDM. Binary | Byte [] |
| Edm.Boolean | Bool |
| EDM. byte | Byte [] |
| EDM. DateTime | DateTime |
| EDM. decimal | Decimal |
| Edm.Double | Double |
| EDM. Single | Enkel |
| EDM. GUID | GUID |
| EDM. Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| EDM. SByte | Int16 |
| Edm.String | Sträng |
| EDM. Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> OData komplexa data typer (t. ex. **objekt**) stöds inte.

## <a name="copy-data-from-project-online"></a>Kopiera data från Project Online

Om du vill kopiera data från Project Online kan du använda OData-anslutningen och en åtkomsttoken som hämtats från verktyg som Postman.

> [!CAUTION]
> Åtkomsttoken upphör att gälla om 1 timme som standard. du måste skaffa en ny åtkomsttoken när den upphör att gälla.

1. Använd **Postman** för att hämta åtkomsttoken:

   1. Gå till fliken **auktorisering** på Postman-webbplatsen.
   1. I rutan **typ** väljer du **OAuth 2,0** och i rutan **Lägg till auktoriseringsdata till väljer du** **begärandehuvuden**.
   1. Fyll i följande information på sidan **Konfigurera ny token** för att hämta en ny åtkomsttoken: 
      - **Typ av beviljande**: Välj **auktoriseringskod**.
      - **Återanrops-URL**: ange `https://www.localhost.com/` . 
      - **URL för autentisering**: ange `https://login.microsoftonline.com/common/oauth2/authorize?resource=https://<your tenant name>.sharepoint.com` . Ersätt `<your tenant name>` med ditt eget klient namn. 
      - **URL till** åtkomsttoken: ange `https://login.microsoftonline.com/common/oauth2/token` .
      - **Klient-ID**: Ange ditt huvud namn för AAD-tjänsten.
      - **Klient hemlighet**: Ange din hemlighet för tjänstens huvud namn.
      - **Klientautentisering**: Välj **Skicka som grundläggande auth-huvud**.
     
   1. Du uppmanas att logga in med ditt användar namn och lösen ord.
   1. När du har skaffat din åtkomsttoken kan du kopiera och spara den för nästa steg.
   
    [![Använd Postman för att hämta åtkomsttoken](./media/connector-odata/odata-project-online-postman-access-token-inline.png)](./media/connector-odata/odata-project-online-postman-access-token-expanded.png#lightbox)

1. Skapa den länkade OData-tjänsten:
    - **Tjänst-URL**: ange `https://<your tenant name>.sharepoint.com/sites/pwa/_api/Projectdata` . Ersätt `<your tenant name>` med ditt eget klient namn. 
    - **Autentiseringstyp**: Välj **Anonym**.
    - **Auth-rubriker**:
        - **Egenskaps namn**: Välj **auktorisering**.
        - **Värde**: Ange den **åtkomsttoken** som kopierades från steg 1.
    - Testa den länkade tjänsten.

    ![Skapa OData-länkad tjänst](./media/connector-odata/odata-project-online-linked-service.png)

1. Skapa OData-datauppsättningen:
    1. Skapa data uppsättningen med den länkade OData-tjänsten som skapades i steg 2.
    1. Förhandsgranska data.
 
    ![Förhandsgranska data](./media/connector-odata/odata-project-online-preview-data.png)
 


## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg

En lista över data lager som kopierings aktiviteten stöder som källor och handfat i Azure Data Factory finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).