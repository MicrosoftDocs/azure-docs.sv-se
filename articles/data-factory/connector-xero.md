---
title: Kopiera data från Xero med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från Xero till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: jingwang
ms.openlocfilehash: d795f8355943032751b911423b8aaa93b2df3206
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366916"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Kopiera data från Xero med hjälp av Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från Xero. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Xero-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Xero till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder denna Xero-anslutning:

- OAuth 2,0-och OAuth 1,0-autentisering. För OAuth 1,0 stöder Connector Xero [privata program](https://developer.xero.com/documentation/getting-started/getting-started-guide) , men inte för offentliga program.
- Alla Xero-tabeller (API-slutpunkter) utom "rapporter".

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Xero-anslutaren.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten Xero:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **Xero** | Ja |
| connectionProperties | En grupp egenskaper som definierar hur du ansluter till Xero. | Ja |
| ***Under `connectionProperties` :*** | | |
| värd | Slut punkten för Xero-servern ( `api.xero.com` ).  | Ja |
| authenticationType | Tillåtna värden är `OAuth_2.0` och `OAuth_1.0` . | Ja |
| consumerKey | För OAuth 2,0 anger du **klient-ID** för ditt Xero-program.<br>För OAuth 1,0 anger du den konsument nyckel som är kopplad till Xero-programmet.<br>Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| privateKey | För OAuth 2,0 anger du **klient hemligheten** för ditt Xero-program.<br>För OAuth 1,0 anger du den privata nyckeln från den. PEM-fil som skapades för ditt Xero privata program, se [skapa ett offentligt/privat nyckel par](https://developer.xero.com/documentation/auth-and-limits/create-publicprivate-key). Obs! det går inte att **skapa PrivateKey. pem med numbits 512** med `openssl genrsa -out privatekey.pem 512` 1024. Ta med all text från. pem-filen, inklusive UNIX-slutpunkter (\n), se exemplet nedan.<br/><br>Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenantId | Det klient-ID som är associerat med ditt Xero-program. Gäller för OAuth 2,0-autentisering.<br>Lär dig hur du hämtar klient-ID: t från [kontrol lera de innehavare som du har behörighet att komma åt](https://developer.xero.com/documentation/oauth2/auth-flow). | Ja för OAuth 2,0-autentisering |
| refreshToken | Gäller för OAuth 2,0-autentisering.<br/>OAuth 2,0-uppdateringstoken är associerad med Xero-programmet och används för att uppdatera åtkomsttoken. åtkomsttoken upphör att gälla efter 30 minuter. Lär dig mer om hur Xero-auktoriseringsarkivet fungerar och hur du hämtar uppdateringstoken från [den här artikeln](https://developer.xero.com/documentation/oauth2/auth-flow). Om du vill hämta en uppdateringstoken måste du begära [offline_access-omfånget](https://developer.xero.com/documentation/oauth2/scopes). <br/>**Känn till begränsning**: Obs! Xero återställer uppdateringstoken när den används för att uppdatera åtkomsttoken. För drift arbets belastning måste du, innan varje kopierings aktivitet körs, ange en giltig uppdateringstoken för att ADF ska användas.<br/>Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja för OAuth 2,0-autentisering |
| useEncryptedEndpoints | Anger om data källans slut punkter krypteras med HTTPS. Standardvärdet är True.  | Inga |
| useHostVerification | Anger om värd namnet krävs i serverns certifikat för att matcha värd namnet för servern vid anslutning via TLS. Standardvärdet är True.  | Inga |
| usePeerVerification | Anger om du vill verifiera serverns identitet vid anslutning via TLS. Standardvärdet är True.  | Inga |

**Exempel: OAuth 2,0-autentisering**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": { 
                "host": "api.xero.com",
                "authenticationType":"OAuth_2.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<client ID>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<client secret>"
                },
                "tenantId": "<tenant ID>", 
                "refreshToken": {
                    "type": "SecureString",
                    "value": "<refresh token>"
                }, 
                "useEncryptedEndpoints": true, 
                "useHostVerification": true, 
                "usePeerVerification": true
            }            
        }
    }
}
```

**Exempel: OAuth 1,0-autentisering**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": {
                "host": "api.xero.com", 
                "authenticationType":"OAuth_1.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                }, 
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}
```

**Värde för exempel på privat nyckel:**

Inkludera all text från. pem-filen, inklusive UNIX-slutpunkter (\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Xero DataSet.

Om du vill kopiera data från Xero anger du egenskapen type för data uppsättningen till **XeroObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **XeroObject** | Ja |
| tableName | Tabellens namn. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Xero-källan.

### <a name="xero-as-source"></a>Xero som källa

Om du vill kopiera data från Xero anger du käll typen i kopierings aktiviteten till **XeroSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **XeroSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM Contacts"`. | Nej (om "tableName" i data uppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Tänk på följande när du anger Xero-frågan:

- Tabeller med komplexa objekt kommer att delas upp i flera tabeller. Bank transaktioner har till exempel en komplex data struktur "rad objekt", så data för bank transaktioner mappas till tabellen `Bank_Transaction` och `Bank_Transaction_Line_Items` , med `Bank_Transaction_ID` som sekundär nyckel, för att länka dem tillsammans.

- Xero-data är tillgängliga via två scheman: `Minimal` (standard) och `Complete` . Det fullständiga schemat innehåller nödvändiga anrops tabeller som kräver ytterligare data (t. ex. ID-kolumn) innan du gör den önskade frågan.

Följande tabeller har samma information i schemat minimal och fullständig. Om du vill minska antalet API-anrop använder du minimalt schema (standard).

- Bank_Transactions
- Contact_Groups 
- Kontakter 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Fakturor 
- Invoices_Credit_Notes
- Invoices_ förskotts betalningar 
- Invoices_Overpayments 
- Manual_Journals 
- Överbetalningar 
- Overpayments_Allocations 
- Förskotts betalningar 
- Prepayments_Allocations 
- Kvitton 
- Receipt_Validation_Errors 
- Tracking_Categories

Följande tabeller kan bara frågas med fullständigt schema:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ personer 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ betalningar 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds av kopierings aktiviteten finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
