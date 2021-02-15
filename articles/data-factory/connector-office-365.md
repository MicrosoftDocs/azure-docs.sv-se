---
title: Kopiera data från Office 365 med Azure Data Factory
description: Lär dig hur du kopierar data från Office 365 till mottagar data lager med stöd för kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: a7df69e7c5701074b40d6fa8340d8a0e247f00de
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393011"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Kopiera data från Office 365 till Azure med Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory integreras med [Microsoft Graph data Connect](/graph/data-connect-concept-overview), så att du kan ta med de omfattande organisations data i din Office 365-klient i Azure på ett skalbart sätt och bygga analys program och extrahera insikter baserat på dessa värdefulla data till gångar. Integrering med Privileged Access Management ger säker åtkomst kontroll för värdefulla granskade data i Office 365.  Se [den här länken](/graph/data-connect-concept-overview) om du vill ha en översikt över Microsoft Graph data Connect och referera till [den här länken](/graph/data-connect-policies#licensing) för licens information.

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från Office 365. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds
Med ADF Office 365 Connector och Microsoft Graph data Connect kan du skala inmatningen av olika typer av data uppsättningar från Exchange E-mail-aktiverade post lådor, inklusive adress boks kontakter, Kalender händelser, e-postmeddelanden, användar information, inställningar för post lådor och så vidare.  Mer information finns [här](/graph/data-connect-datasets) om du vill se en fullständig lista över tillgängliga data uppsättningar.

För närvarande kan du bara **Kopiera data från Office 365 till [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage GEN1](connector-azure-data-lake-store.md)och [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) i JSON-format** (Skriv setOfObjects) i en enda kopierings aktivitet. Om du vill läsa in Office 365 i andra typer av data lager eller i andra format, kan du kedja den första kopierings aktiviteten med en efterföljande kopierings aktivitet för att ytterligare läsa in data till någon av de [ADF-mål arkiv som stöds](copy-activity-overview.md#supported-data-stores-and-formats) (se kolumnen "stöds som mottagare" i kolumnen "data lager och format som stöds").

>[!IMPORTANT]
>- Azure-prenumerationen som innehåller data fabriken och data lagret för mottagare måste ligga under samma Azure Active Directory-klient (Azure AD) som Office 365-klienten.
>- Se till att den Azure Integration Runtime region som används för kopierings aktiviteten och målet finns i samma region där Office 365-klientens användares post låda finns. Mer information finns [här](concepts-integration-runtime.md#integration-runtime-location) för att förstå hur Azure IRS platsen bestäms. Se [tabellen här](/graph/data-connect-datasets#regions) för en lista över de Office-regioner som stöds och motsvarande Azure-regioner.
>- Autentisering av tjänstens huvud namn är den enda autentiseringsmekanismen som stöds för Azure Blob Storage, Azure Data Lake Storage Gen1 och Azure Data Lake Storage Gen2 som mål arkiv.

## <a name="prerequisites"></a>Förutsättningar

Om du vill kopiera data från Office 365 till Azure måste du utföra följande nödvändiga steg:

- Din Office 365-klient administratör måste slutföra de åtgärder som beskrivs [här](/graph/data-connect-get-started).
- Skapa och konfigurera ett Azure AD-webbprogram i Azure Active Directory.  Instruktioner finns i [skapa ett Azure AD-program](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
- Anteckna följande värden, som du kommer att använda för att definiera den länkade tjänsten för Office 365:
    - Klient-ID. Instruktioner finns i [Hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
    - Program-ID och program nyckel.  Instruktioner finns i [Hämta program-ID och autentiseringsnyckel](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
- Lägg till den användar identitet som ska göra data åtkomst förfrågan som ägare av Azure AD-webbprogrammet (från Azure AD-webbprogrammet > inställningar > ägare > Lägg till ägare). 
    - Användar identiteten måste vara i Office 365-organisationen som du hämtar data från och får inte vara gäst användare.

## <a name="approving-new-data-access-requests"></a>Godkänna nya begär Anden om data åtkomst

Om detta är första gången du begär data för den här kontexten (en kombination av vilken data tabell som ska användas, vilket mål konto som läses in i, och vilken användar identitet som gör data åtkomst förfrågan, visas status för kopierings aktiviteten som "pågår" och endast när du klickar på [länken "information" under åtgärder](copy-activity-overview.md#monitoring) visas statusen som "RequestingConsent".  En medlem i gruppen för godkännande av data åtkomst måste godkänna begäran i Privileged Access Management innan data extraheringen kan fortsätta.

Läs mer om hur god kännaren kan godkänna [begäran om data](/graph/data-connect-pam) åtkomst [och referera till](/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) en förklaring om den övergripande integreringen med Privileged Access Management, inklusive hur du konfigurerar gruppen för data åtkomst godkännande.

## <a name="policy-validation"></a>Principverifiering

Om ADF skapas som en del av en hanterad app och tilldelningar av Azure-principer görs på resurser i hanterings resurs gruppen, kommer ADF att kontrol lera att princip tilldelningarna är tvingade för varje kopierings aktivitets körning. [Här](/graph/data-connect-policies#policies) hittar du en lista över principer som stöds.

## <a name="getting-started"></a>Komma igång

>[!TIP]
>En genom gång av hur du använder Office 365-anslutning finns i [läsa in data från Office 365](load-office-365-data.md) -artikeln.

Du kan skapa en pipeline med kopierings aktiviteten genom att använda något av följande verktyg eller SDK: er. Välj en länk för att gå till en själv studie kurs med stegvisa instruktioner för att skapa en pipeline med en kopierings aktivitet. 

- [Azure-portalen](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST-API](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager mall](quickstart-create-data-factory-resource-manager-template.md). 

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Office 365-anslutaren.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade Office 365-tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **Office365** | Ja |
| office365TenantId | Azure-klient-ID som Office 365-kontot tillhör. | Ja |
| servicePrincipalTenantId | Ange den klient information under vilken Azure AD-webbappen finns. | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i Data Factory. | Ja |
| connectVia | Den Integration Runtime som ska användas för att ansluta till data lagret.  Om inget värde anges används standard Azure Integration Runtime. | Inga |

>[!NOTE]
> Skillnaden mellan **office365TenantId** och **servicePrincipalTenantId** och motsvarande värde för att tillhandahålla:
>- Om du är företags utvecklare som utvecklar ett program mot Office 365-data för din organisations användning, ska du ange samma klient-ID för båda egenskaperna, som är din organisations AAD-klient-ID.
>- Om du är en ISV-utvecklare som utvecklar ett program för dina kunder kommer office365TenantId att vara kundens (program installations program) AAD-klient-ID och servicePrincipalTenantId vara ditt företags AAD-klient-ID.

**Exempel:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av data uppsättningen för Office 365.

Följande egenskaper stöds för att kopiera data från Office 365:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **Office365Table** | Ja |
| tableName | Namnet på data uppsättningen som ska extraheras från Office 365. [Här](/graph/data-connect-datasets#datasets) hittar du en lista över Office 365-datauppsättningar som är tillgängliga för extrahering. | Ja |

Om du har angett `dateFilterColumn` , `startTime` , `endTime` , och `userScopeFilterUri` i data uppsättning, stöds den fortfarande som-är, medan du föreslås att använda den nya modellen i aktivitets källan som går framåt.

**Exempel**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Office 365-källan.

### <a name="office-365-as-source"></a>Office 365 som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** för att kopiera data från Office 365:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **Office365Source** | Ja |
| allowedGroups | Grupp urvals predikat.  Använd den här egenskapen för att välja upp till 10 användar grupper som data ska hämtas för.  Om inga grupper anges returneras data för hela organisationen. | Inga |
| userScopeFilterUri | När `allowedGroups` egenskapen inte anges kan du använda ett predikat-uttryck som tillämpas på hela klienten för att filtrera de specifika rader som ska extraheras från Office 365. Predikatet format ska matcha fråge formatet för Microsoft Graph API: er, `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'` t. ex.. | Inga |
| dateFilterColumn | Namn på kolumnen DateTime-filter. Använd den här egenskapen för att begränsa det tidsintervall som Office 365-data extraheras för. | Ja om data uppsättningen har en eller flera DateTime-kolumner. [Här](/graph/data-connect-filtering#filtering) hittar du en lista över data uppsättningar som kräver detta datetime-filter. |
| startTime | Starta DateTime-värdet för att filtrera på. | Ja om `dateFilterColumn` har angetts |
| endTime | Slutdatum/tid-värde att filtrera på. | Ja om `dateFilterColumn` har angetts |
| outputColumns | Matris för de kolumner som ska kopieras till Sink. | Inga |

**Exempel:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).