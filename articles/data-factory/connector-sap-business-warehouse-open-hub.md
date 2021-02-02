---
title: Kopiera data från SAP Business Warehouse via öppen hubb
description: Lär dig hur du kopierar data från SAP Business Warehouse (BW) via öppna hubb till mottagar data lager med stöd för en kopierings aktivitet i en Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/02/2020
ms.openlocfilehash: ca8fad59e581ef3f5a3ebf585356564d539f0bbd
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430738"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopiera data från SAP Business Warehouse via öppen hubb med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från ett SAP Business Warehouse (BW) via öppen hubb. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

>[!TIP]
>Information om hur du hanterar ADF: s övergripande support på SAP data integrations scenario finns i [SAP-dataintegrering med Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) med detaljerad introduktion till varje SAP-koppling, comparsion och vägledning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Detta SAP Business Warehouse via öppen Hub Connector stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från SAP Business Warehouse via öppna hubbar till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder detta SAP Business Warehouse Open Hub Connector:

- SAP Business Warehouse **version 7,01 eller senare (i en senaste SAP-support paket stack som publicerats efter året 2015)**. SAP BW/4HANA stöds inte av den här anslutningen.
- Kopiera data via den lokala mål destinations tabellen, som under kan vara DSO, InfoCube, multitillhandahållare, DataSource osv.
- Kopiera data med grundläggande autentisering.
- Ansluta till en SAP-program Server eller SAP Message Server.
- Hämtar data via RFC.

## <a name="sap-bw-open-hub-integration"></a>SAP BW integrering med öppen hubb 

[SAP BW Open Hub service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) är ett effektivt sätt att extrahera data från SAP BW. I följande diagram visas en av de vanligaste flödes kunderna i sina SAP-system, i vilket fall data flödar från SAP ECC-> PSA-> DSO-> Cube.

SAP BW OHD (Open Hub destination) definierar målet som SAP-data vidarebefordras till. Alla objekt som stöds av SAP Dataöverföring process (DTP) kan användas som data källor med öppen hubb, till exempel DSO, InfoCube, DataSource osv. Mål typ för öppen hubb – där vidarebefordrade data lagras – kan vara databas tabeller (lokala eller fjärranslutna) och flata filer. Det här SAP BW öppna Hub Connector-stöd för att kopiera data från den lokala OHD-tabellen i BW. Om du använder andra typer kan du ansluta direkt till databasen eller fil systemet med andra anslutningar.

![SAP BW öppna hubben](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Flöde för delta extrahering

ADF SAP BW Open Hub Connector erbjuder två valfria egenskaper: `excludeLastRequest` och `baseRequestId` som kan användas för att hantera delta-belastning från öppna hubb. 

- **excludeLastRequestId**: om posterna för den senaste begäran ska uteslutas. Standardvärdet är True. 
- **baseRequestId**: ID för begäran om delta inläsning. När den har angetts hämtas endast data med requestId som är större än värdet för den här egenskapen. 

Som helhet består extraheringen från SAP InfoProviders till Azure Data Factory (ADF) av två steg: 

1. **SAP BW dataöverföring process (DTP)** I det här steget kopieras data från en SAP BW InfoProvider till en SAP BW öppen Hub-tabell 

1. **ADF-datakopia** I det här steget läses öppna Hub-tabellen av ADF-kopplingen 

![Flöde för delta extrahering](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

I det första steget körs en DTP. Varje körning skapar ett nytt ID för SAP-begäran. Fråge-ID: t lagras i den öppna Hub-tabellen och används sedan av ADF-kopplingen för att identifiera delta. De två stegen körs asynkront: DTP utlöses av SAP och data kopieringen för ADF utlöses via ADF. 

Som standard läser ADF inte den senaste förändringen från den öppna Hub-tabellen (alternativet "exkludera senaste begäran" är sant). Det innebär att data i ADF inte är 100% uppdaterade med data i den öppna Hub-tabellen (det sista deltaet saknas). I retur ser den här proceduren till att inga rader går förlorade av asynkron extrahering. Det fungerar fint även när ADF läser den öppna Hub-tabellen medan DTP fortfarande skriver till samma tabell. 

Du lagrar vanligt vis det maximala kopierade förfrågnings-ID: t i den senaste körningen av ADF i ett data lager för mellanlagring (till exempel Azure blob i diagrammet ovan). Därför läses samma begäran inte en andra gång med ADF i den efterföljande körningen. Observera att data inte tas bort automatiskt från den öppna Hub-tabellen.

För korrekt delta hantering är det inte tillåtet att ha fråge-ID från olika DTPs i samma öppna Hub-tabell. Därför får du inte skapa fler än en DTP för varje Open Hub-destination (OHD). När du behöver fullständig och delta extrahering från samma InfoProvider bör du skapa två OHDs för samma InfoProvider. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här SAP Business Warehouse-anslutningen med öppen hubb måste du:

- Konfigurera en egen värd Integration Runtime med version 3,13 eller senare. Mer information finns i artikeln om [egen värd integration runtime](create-self-hosted-integration-runtime.md) .

- Hämta **64-bitars [SAP .net Connector 3,0](https://support.sap.com/en/product/connectors/msnet.html)** från SAP: s webbplats och installera den på den lokala IR-datorn. När du installerar går du till fönstret valfria installations steg och kontrollerar att du väljer alternativet **Installera sammansättningar i GAC** som det visas i följande bild. 

    ![Installera SAP .NET Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- SAP-användare som används i Data Factory BW-anslutaren måste ha följande behörigheter: 

    - Auktorisering för RFC och SAP BW. 
    - Behörigheter till "kör"-aktiviteten för Authorization-objektet "S_SDSAUTH".

- Skapa en SAP Open Hub-måltyp som **databas tabell** med alternativet "teknisk nyckel" markerat.  Vi rekommenderar också att du lämnar kryss rutan ta bort data från tabellen som omarkerade även om det inte är obligatoriskt. Använd DTP (direkt körning eller integrering i befintlig process kedja) för att landa data från källobjektet (till exempel kub) som du har valt till mål tabellen med öppen hubb.

## <a name="getting-started"></a>Komma igång

> [!TIP]
>
> En genom gång av hur du använder SAP BW Open Hub Connector finns i [läsa in data från SAP Business Warehouse (BW) med hjälp av Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är specifika för SAP Business Warehouse Open Hub Connector.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för SAP Business Warehouse-länkad hubb:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **SapOpenHub** | Ja |
| server | Namnet på den server där SAP BW-instansen finns. | Ja |
| systemNumber | System numret för det SAP BW systemet.<br/>Tillåtet värde: tvåsiffrigt decimal tal representeras som en sträng. | Ja |
| messageServer | Värd namnet för SAP Message Server.<br/>Använd för att ansluta till en SAP-meddelande Server. | Inga |
| messageServerService | Tjänst namnet eller port numret för meddelande servern.<br/>Använd för att ansluta till en SAP-meddelande Server. | Inga |
| systemId | ID: t för det SAP-system där tabellen finns.<br/>Använd för att ansluta till en SAP-meddelande Server. | Inga |
| logonGroup | Inloggnings gruppen för SAP-systemet.<br/>Använd för att ansluta till en SAP-meddelande Server. | Inga |
| ClientID | Klient-ID för klienten i SAP W-systemet.<br/>Tillåtet värde: tre-siffrigt decimal tal representeras som en sträng. | Ja |
| language | Språk som används i SAP-systemet. | Nej (Standardvärdet är **en**)|
| userName | Namnet på den användare som har åtkomst till SAP-servern. | Ja |
| password | Lösenordet för användaren. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Det krävs en egen värd Integration Runtime som anges i [krav](#prerequisites). |Ja |

**Exempel:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW Open Hub-datauppsättningen.

Om du vill kopiera data från och till SAP BW öppna hubben, anger du egenskapen type för data uppsättningen till **SapOpenHubTable**. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **SapOpenHubTable**.  | Ja |
| openHubDestinationName | Namnet på det öppna hubb målet att kopiera data från. | Ja |

Om du har angett `excludeLastRequest` och `baseRequestId` i data uppsättning stöds det fortfarande som det är, medan du föreslås att använda den nya modellen i aktivitets källan som går framåt.

**Exempel:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW öppna Hub-källan.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW öppna hubben som källa

Om du vill kopiera data från SAP BW öppna hubben, stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **SapOpenHubSource**. | Ja |
| excludeLastRequest | Om posterna för den senaste begäran ska uteslutas. | Nej (standard är **Sant**) |
| baseRequestId | ID för begäran om delta inläsning. När den har angetts hämtas endast data med requestId som är **större än** värdet för den här egenskapen.  | Inga |

>[!TIP]
>Om den öppna Hub-tabellen bara innehåller de data som genereras av ID: t för en enskild begäran, till exempel, gör du alltid fullständig belastning och skriver över befintliga data i tabellen, eller så kör du bara DTP en gång för test, kom ihåg att avmarkera alternativet "excludeLastRequest" för att kopiera data.

För att påskynda data inläsningen kan du ställa in [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) på kopierings aktiviteten för att läsa in data från SAP BW öppen hubb parallellt. Om du till exempel ställer in `parallelCopies` till fyra kör Data Factory samtidigt fyra RFC-anrop, och varje RFC-anrop hämtar en del av data från SAP BW öppna Hub-tabellen partitionerad med ID för DTP-begäran och paket-ID. Detta gäller när antalet unika ID för DTP-begäran och paket-ID är större än värdet för `parallelCopies` . När du kopierar data till ett filbaserat data lager, skrivs det också om att skriva till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Data typs mappning för SAP BW öppen hubb

När du kopierar data från SAP BW öppna hubben används följande mappningar från SAP BW data typer för att Azure Data Factory interimistiska data typer. Se [mappningar av schema och data typer](copy-activity-schema-and-type-mapping.md) för att lära dig mer om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren.

| Typ av SAP-ABAP | Data fabrikens interimistiska datatyp |
|:--- |:--- |
| C (sträng) | Sträng |
| I (heltal) | Int32 |
| F (float) | Double |
| D (datum) | Sträng |
| T (tid) | Sträng |
| P (BCD-packad, valuta, decimal, KVT) | Decimal |
| N (Numc) | Sträng |
| X (binärt och RAW) | Sträng |

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="troubleshooting-tips"></a>Felsökningstips

**Symptom:** Om du kör SAP BW på HANA och Observera att endast en delmängd data kopieras med hjälp av den automatiska kopierings aktiviteten (1 000 000 rader) är den möjliga orsaken att du aktiverar alternativet "SAP HANA körning" i DTP, vilket innebär att ADF bara kan hämta den första data uppsättningen.

**Lösning:** Inaktivera alternativet "SAP HANA körning" i DTP, bearbeta om data och försök sedan att köra kopierings aktiviteten igen.

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
