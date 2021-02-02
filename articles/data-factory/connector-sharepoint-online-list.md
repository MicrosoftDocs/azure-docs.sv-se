---
title: Kopiera data från SharePoint Online-listan med Azure Data Factory
description: Lär dig hur du kopierar data från SharePoint Online-listan till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: 440dd561beddc9696ec703142fe82655b69fbb48
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99474955"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory"></a>Kopiera data från SharePoint Online-listan med Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från SharePoint Online-listan. Artikeln bygger på [kopierings aktivitet i Azure Data Factory](copy-activity-overview.md), som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SharePoint Online List-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från SharePoint Online-listan till alla mottagar data lager som stöds. En lista över data lager som kopierings aktiviteten stöder som källor och mottagare finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Mer specifikt använder den här SharePoint-listan Online Connector autentisering av tjänstens huvud namn och hämtar data via OData-protokollet.

> [!TIP]
> Den här anslutningen har stöd för att kopiera data från SharePoint Online- **listan** men inte till en fil. Lär dig hur du kopierar filen från avsnittet [Kopiera filen från SharePoint Online](#copy-file-from-sharepoint-online) .

## <a name="prerequisites"></a>Förutsättningar

SharePoint-listans Online Connector använder tjänstens huvud namns autentisering för att ansluta till SharePoint. Följ dessa steg för att konfigurera det:

1. Registrera en program enhet i Azure Active Directory (Azure AD) genom [att följa registrera ditt program med en Azure AD-klient](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

    - Program-ID
    - Program nyckel
    - Klientorganisations-ID

2. Bevilja SharePoint Online-webbplats behörighet till ditt registrerade program: 

    > [!NOTE]
    > Den här åtgärden kräver behörighet för SharePoint Online-webbplatsens ägare. Du kan hitta ägaren genom att gå till start sidan för webbplatsen – > Klicka på "X-medlemmar" i det högra hörnet-> Markera som har rollen "ägare".

    1. Öppna SharePoint Online-webbplatsens länk, t. ex. `https://[your_site_url]/_layouts/15/appinv.aspx` (Ersätt webbplats-URL: en).
    2. Sök efter det program-ID som du har registrerat, Fyll i de tomma fälten och klicka på "skapa".

        - App-domän: `localhost.com`
        - Omdirigerings-URL: `https://www.localhost.com`
        - Begäran-XML för behörighet:

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        ![behörighet för SharePoint-beviljande](media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png)

    3. Klicka på "lita på det" för den här appen.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory entiteter som är speciella för SharePoint Online List Connector.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för en länkad SharePoint Online-lista:

| **Egenskap**        | **Beskrivning**                                              | **Obligatoriskt** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| typ                | Egenskapen Type måste anges till: **SharePointOnlineList**.  | Ja          |
| siteUrl             | SharePoint Online-webbplatsens URL, `https://contoso.sharepoint.com/sites/siteName` t. ex.. | Ja          |
| servicePrincipalId  | Program-ID för programmet som är registrerat i Azure Active Directory. | Ja          |
| servicePrincipalKey | Programmets nyckel. Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja          |
| tenantId            | Det klient-ID som ditt program finns under.          | Ja          |
| connectVia          | [Integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från [nödvändiga komponenter](#prerequisites)tidigare i den här artikeln. Om inget värde anges används standard Azure Integration Runtime. | Inga           |

**Exempel:**

```json
{
    "name": "SharePointOnlineList",
    "properties": {
        "type": "SharePointOnlineList",
        "typeProperties": {
            "siteUrl": "<site URL>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenantId": "<tenant ID>"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar och länkade tjänster](concepts-datasets-linked-services.md). Följande avsnitt innehåller en lista över de egenskaper som stöds av SAP Table-datauppsättningen.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens **typ** -egenskap måste anges till **SharePointOnlineLResource**. | Ja |
| listName | Namnet på SharePoint Online-listan. | Ja |

**Exempel**

```json
{
    "name": "SharePointOnlineListDataset",
    "properties":
    {
        "type": "SharePointOnlineListResource",
        "linkedServiceName": {
            "referenceName": "<SharePoint Online List linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "listName": "<name of the list>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera aktivitets egenskaper

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md).  Följande avsnitt innehåller en lista över de egenskaper som stöds av SharePoint Online List-källan.

### <a name="sharepoint-online-list-as-source"></a>SharePoint Online-lista som källa

Om du vill kopiera data från SharePoint Online-listan stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **SharePointOnlineListSource**. | Ja |
| DocumentDB | Anpassade alternativ för OData-frågor för att filtrera data. Exempel: `"$top=10&$select=Title,Number"`. | Inga |
| httpRequestTimeout | Tids gränsen (i sekund) för HTTP-begäran för att få ett svar. Standardvärdet är 300 (5 minuter). | Inga |

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromSharePointOnlineList",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SharePoint Online List input dataset name>",
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
            "source": {
                "type": "SharePointOnlineListSource",
                "query": "<OData query e.g. $top=10&$select=Title,Number>"
            }, 
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!NOTE]
> I Azure Data Factory kan du inte välja fler än en *alternativ* data typ för en SharePoint Online-lista.

## <a name="data-type-mapping-for-sharepoint-online-list"></a>Data typs mappning för SharePoint Online-lista

När du kopierar data från SharePoint Online-listan används följande mappningar mellan SharePoint Online-listans data typer och Azure Data Factory interimistiska data typer. 

| **Data typ för SharePoint Online**                 | **OData-datatyp**                                  | **Azure Data Factory data typen Interim** |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| Enskild rad med text                             | Edm.String                                           | Sträng                                   |
| Flera rader med text                          | Edm.String                                           | Sträng                                   |
| Val (meny att välja från)                    | Edm.String                                           | Sträng                                   |
| Tal (1, 1,0, 100)                            | Edm.Double                                           | Double                                   |
| Valuta ($, ¥, €)                              | Edm.Double                                           | Double                                   |
| Datum och tid                                   | EDM. DateTime                                         | DateTime                                 |
| Uppslag (information som redan finns på webbplatsen)       | Edm.Int32                                            | Int32                                    |
| Ja/Nej (kryss ruta)                              | Edm.Boolean                                          | Boolesk                                  |
| Person eller grupp                                 | Edm.Int32                                            | Int32                                    |
| Hyperlänk eller bild                            | Edm.String                                           | Sträng                                   |
| Beräknad (beräkning baserad på andra kolumner) | EDM. String/EDM. Double/EDM. DateTime/EDM. Boolean | Sträng/Double/DateTime/Boolean     |
| Bilaga                                      | Stöds inte                                        |                                          |
| Uppgiftsresultat                                    | Stöds inte                                        |                                          |
| Externa data                                   | Stöds inte                                        |                                          |
| Hanterade metadata                                | Stöds inte                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>Kopiera filen från SharePoint Online

Du kan kopiera filen från SharePoint Online genom att använda **webb aktivitet** för att autentisera och hämta ÅTKOMSTTOKEN från SPO och sedan skicka till efterföljande **kopierings aktivitet** för att kopiera data med **http-anslutning som källa**.

![Kopiera fil flöde i SharePoint](media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png)

1. Följ [krav](#prerequisites) avsnittet för att skapa AAD-program och bevilja behörighet till SharePoint Online. 

2. Skapa en **webb aktivitet** för att hämta åtkomsttoken från SharePoint Online:

    - **URL**: `https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2` . Ersätt klient-ID: t.
    - **Metod**: post
    - **Rubriker**:
        - Innehålls typ: Application/x-www-form-urlencoded
    - **Brödtext**:  `grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]` . Ersätt klient-ID, klient hemlighet, klient-ID och klient namn.

    > [!CAUTION]
    > Ange alternativet för säkra utdata till sant i webb aktivitet för att förhindra att token-värdet loggas som oformaterad text. Alla ytterligare aktiviteter som använder det här värdet ska ha sina säkra indatatyps-alternativ inställt på sant.

3. Kedja med en **kopierings aktivitet** med HTTP-anslutning som källa för att kopiera SharePoint Online-filinnehållet:

    - HTTP-länkad tjänst:
        - **Bas-URL**: `https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value` . Ersätt webbplats-URL: en och den relativa sökvägen till filen. Exempel på relativ sökväg till filen som `/sites/site2/Shared Documents/TestBook.xlsx` .
        - **Autentiseringstyp:** Anonym *(om du vill använda Bearer-token som kon figurer ATS i kopierings aktivitetens källa senare)*
    - Data uppsättning: Välj det format som du vill använda. Om du vill kopiera filen som den är väljer du binär typ.
    - Kopiera aktivitets Källa:
        - **Metod för begäran**: Hämta
        - **Ytterligare rubrik**: Använd följande uttryck `@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}` som använder den Bearer-token som genereras av den överordnade webb aktiviteten som Authorization-huvud. Ersätt webb aktivitetens namn.
    - Konfigurera kopierings aktivitets sinken som vanligt.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg

En lista över data lager som kopierings aktiviteten stöder som källor och handfat i Azure Data Factory finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
