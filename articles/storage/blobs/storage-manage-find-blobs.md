---
title: Hantera och hitta Azure blob-data med blobb index Taggar (för hands version)
description: Lär dig hur du använder BLOB-taggar för att kategorisera, hantera och fråga efter BLOB-objekt.
author: twooley
ms.author: twooley
ms.date: 03/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions
ms.openlocfilehash: 2188aaea0cf5a4616291d3fdad839aefb2dbc413
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280708"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags-preview"></a>Hantera och hitta Azure blob-data med blobb index Taggar (för hands version)

När data uppsättningarna blir större kan det vara svårt att hitta ett särskilt objekt i en fart på data. BLOB index-Taggar ger data hanterings-och identifierings funktioner med hjälp av taggattribut för nyckel värde index. Du kan kategorisera och hitta objekt inom en enda behållare eller över alla behållare i ditt lagrings konto. När data kraven ändras kan objekt kategoriseras dynamiskt genom att deras index etiketter uppdateras. Objekt kan behållas på plats med sin nuvarande behållar organisation.

Med blobb index taggar kan du:

- Kategorisera dina blobar dynamiskt med hjälp av nyckel-värde index Taggar
- Hitta snabbt vissa taggade blobbar i ett helt lagrings konto
- Ange villkorliga beteenden för BLOB-API: er baserat på utvärderingen av index Taggar
- Använd index taggar för avancerade kontroller av funktioner som [BLOB Lifecycle Management](storage-lifecycle-management-concepts.md)

Tänk dig ett scenario där du har miljon tals blobbar i ditt lagrings konto, som används av många olika program. Du vill hitta alla relaterade data från ett enda projekt. Du är inte säker på vad som är inom ramen för att data kan spridas över flera behållare med olika namngivnings konventioner. Programmen laddar dock upp alla data med Taggar baserat på deras projekt. I stället för att söka igenom miljon tals blobbar och jämföra namn och egenskaper kan du använda `Project = Contoso` som identifierings villkor. BLOB-index filtrerar alla behållare över hela lagrings kontot för att snabbt hitta och returnera bara uppsättningen av 50 blobbar från `Project = Contoso` .

> [!IMPORTANT]
> BLOB index-taggar finns för närvarande i för **hands version** och är tillgängliga i alla offentliga regioner. Se [kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner av juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släpps till allmän tillgänglighet.

Information om hur du kommer igång med exempel på hur du använder BLOB-index finns i [använda BLOB-taggar för att hantera och söka efter data](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>BLOB-index Taggar och data hantering

Prefix för behållare och blob-namn är tvådimensionella kategoriseringar. BLOB index-Taggar möjliggör flerdimensionell kategorisering för [BLOB-datatyper (block, tillägg eller sida)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs). Flerdimensionell Kategorisering indexeras internt av Azure Blob Storage så att du snabbt kan hitta dina data.

Överväg följande fem blobbar i ditt lagrings konto:

- *container1/transaction.csv*
- *container2/campaign.docx*
- *foton/bannerphoto.png*
- *Arkiv/slutfört/2019review.pdf*
- *loggar/2020/01/01/logfile.txt*

Dessa blobbar separeras med hjälp av ett prefix för *behållare/virtuell mapp/BLOB-namn*. Du kan ange ett index tag-attribut för `Project = Contoso` på dessa fem blobbar för att kategorisera dem tillsammans samtidigt som du behåller sin aktuella prefix organisation. Att lägga till index Taggar eliminerar behovet av att flytta data genom att exponera möjligheten att filtrera och söka efter data med hjälp av indexet.

## <a name="setting-blob-index-tags"></a>Anger BLOB-index-Taggar

Blobb index taggar är nyckel värdes attribut som kan tillämpas på nya eller befintliga objekt i ditt lagrings konto. Du kan ange index Taggar under uppladdnings processen med hjälp av funktionen för att [lägga till BLOB](/rest/api/storageservices/put-blob), [Ange blockeringslistan](/rest/api/storageservices/put-block-list)eller [Kopiera BLOB](/rest/api/storageservices/copy-blob) -åtgärder och valfri `x-ms-tags` rubrik. Om du redan har blobbar i ditt lagrings konto kan du anropa [set BLOB-taggarna](/rest/api/storageservices/set-blob-tags) och skicka ett formaterat XML-dokument med index taggarna i bröd texten i begäran.

> [!IMPORTANT]
> Att ange BLOB-taggar kan utföras av [ägaren till lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) och av alla med en signatur för delad åtkomst som har behörighet att komma åt blobens Taggar ( `t` SAS-behörighet).
>
> Dessutom kan RBAC-användare med `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` behörigheten utföra den här åtgärden.

Du kan använda en enda tagg i blobben för att beskriva när dina data har slutförts.

> "processedDate" = "2020-01-01"

Du kan använda flera taggar i blobben för att vara mer beskrivande data.

> "Projekt" = "contoso"  
> "Klassificerad" = "sant"  
> "Status" = "Unprocessed"  
> "Prioritet" = ' 01 '

Om du vill ändra attributen för den befintliga index tag gen hämtar du de befintliga taggattribut, ändrar taggattribut och ersätter med åtgärden [Ange BLOB-Taggar](/rest/api/storageservices/set-blob-tags) . Om du vill ta bort alla index taggar från blobben anropar du `Set Blob Tags` åtgärden utan att ange taggattribut. Som BLOB-taggar är en under resurs till BLOB-datainnehållet, `Set Blob Tags` ändrar inte eventuellt underliggande innehåll och ändrar inte blobens senaste ändrings tid eller etag. Du kan skapa eller ändra index taggar för alla aktuella bas-blobar och tidigare versioner. Taggar för ögonblicks bilder eller mjuk borttagna blobbar kan dock inte ändras.

Följande begränsningar gäller för BLOB-index Taggar:

- Varje Blob kan ha upp till 10 BLOB-Taggar
- Etikett nycklar måste vara mellan ett och 128 tecken
- Tagg värden måste vara mellan noll och 256 tecken
- Etikett nycklar och värden är Skift läges känsliga
- Etikett nycklar och värden stöder bara sträng data typer. Siffror, datum, tider eller specialtecken sparas som strängar
- Etikett nycklar och värden måste följa följande namngivnings regler:
  - Alfanumeriska tecken:
    - **a** till **z** (små bokstäver)
    - **A** till **Z** (versaler)
    - **0** till **9** (siffror)
  - Giltiga specialtecken: blank steg, plus tecken, minus, punkt, kolon, likheter, under streck, snedstreck ( ` +-.:=_/` )

## <a name="getting-and-listing-blob-index-tags"></a>Hämta och Visa BLOB-index Taggar

BLOB index-Taggar lagras som en under resurs tillsammans med BLOB-data och kan hämtas oberoende av det underliggande BLOB-datainnehållet. Blobb index taggar för en enskild BLOB kan hämtas med åtgärden [Hämta BLOB-Taggar](/rest/api/storageservices/get-blob-tags) . [List blobs](/rest/api/storageservices/list-blobs) -åtgärden med `include:tags` parametern returnerar även alla blobbar i en behållare tillsammans med deras BLOB-index-taggar.

> [!IMPORTANT]
> Hämtning och registrering av BLOB-taggar kan utföras av [ägaren till lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) och av alla med en signatur för delad åtkomst som har behörighet att komma åt blobens Taggar ( `t` SAS-behörighet).
>
> Dessutom kan RBAC-användare med `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` behörigheten utföra den här åtgärden.

För alla blobbar med minst en BLOB-tagg `x-ms-tag-count` returneras i [list blobbar](/rest/api/storageservices/list-blobs), [Get BLOB](/rest/api/storageservices/get-blob)och [Get BLOB Properties](/rest/api/storageservices/get-blob-properties) -åtgärder som anger antalet index Taggar i blobben.

## <a name="finding-data-using-blob-index-tags"></a>Hitta data med hjälp av BLOB-Taggar

Indexerings motorn visar dina nyckel värdes attribut i ett flerdimensionellt index. När du har ställt in index taggarna finns de i blobben och kan hämtas direkt. Det kan ta en stund innan BLOB-indexet uppdateras. När BLOB-indexet uppdateras kan du använda de interna funktionerna för fråga och identifiering som erbjuds av Blob Storage.

[Med åtgärden hitta blobbar per tagg](/rest/api/storageservices/find-blobs-by-tags) kan du hämta en filtrerad uppsättning blobbar vars index taggar matchar ett angivet frågeuttryck. `Find Blobs by Tags` stöder filtrering över alla behållare i ditt lagrings konto eller så kan du begränsa filtreringen till bara en enda behållare. Eftersom alla index tag gen nycklar och värden är strängar, använder Relations operatorer en lexikografisk-sortering.

> [!IMPORTANT]
> Att söka efter data med hjälp av BLOB-taggar kan utföras av [ägaren av Storage BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) och av alla med en signatur för delad åtkomst som har behörighet att hitta blobbar med taggar ( `f` SAS-behörighet).
>
> Dessutom kan RBAC-användare med `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` behörigheten utföra den här åtgärden.

Följande villkor gäller för BLOB-index filtrering:

- Etikett nycklar ska omges av dubbla citat tecken (")
- Tagg värden och behållar namn ska omges av enkla citat tecken (')
- @-Tecknen tillåts bara för filtrering på ett angivet container namn (till exempel `@container = 'ContainerName'` )
- Filter tillämpas med lexikografisk sortering på strängar
- Samma sido intervall åtgärder på samma nyckel är ogiltiga (till exempel `"Rank" > '10' AND "Rank" >= '15'` )
- När du använder REST för att skapa ett filter uttryck ska tecknen vara URI-kodade

I tabellen nedan visas alla giltiga operatorer för `Find Blobs by Tags` :

|  Operator  |  Beskrivning  | Exempel |
|------------|---------------|---------|
|     =      |     Lika med     | `"Status" = 'In Progress'` |
|     >      |  Större än | `"Date" > '2018-06-18'` |
|     >=     |  Större än eller lika med | `"Priority" >= '5'` |
|     <      |  Mindre än   | `"Age" < '32'` |
|     <=     |  Mindre än eller lika med  | `"Company" <= 'Contoso'` |
|    AND     |  Logiska och  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | Omfång till en angiven behållare | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> Var bekant med lexicographical-beställning när du ställer in och frågar efter taggar.
>
> - Siffror sorteras före bokstäver. Siffror sorteras baserat på den första siffran.
> - Versaler sorteras före gemener.
> - Symbolerna är inte standard. Vissa symboler sorteras före numeriska värden. Andra symboler sorteras före eller efter bokstäver.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Villkorliga BLOB-åtgärder med BLOB-index Taggar

I REST-versioner 2019-10-10 och högre har de flesta [BLOB service-API: er](/rest/api/storageservices/operations-on-blobs) nu stöd för en villkorlig rubrik, `x-ms-if-tags` så att åtgärden endast lyckas om det angivna BLOB-indexets villkor är uppfyllt. Om villkoret inte är uppfyllt får du `error 412: The condition specified using HTTP conditional header(s) is not met` .

`x-ms-if-tags`Rubriken kan kombineras med andra befintliga http villkorliga huvuden (IF-match, If-None-Match och så vidare). Om flera villkorliga huvuden anges i en begäran måste alla utvärdera sant för att åtgärden ska lyckas. Alla villkorliga huvuden kombineras effektivt med logiska och.

I tabellen nedan visas giltiga operatorer för villkorliga åtgärder:

|  Operator  |  Beskrivning  | Exempel |
|------------|---------------|---------|
|     =      |     Lika med     | `"Status" = 'In Progress'` |
|     <>     |   Inte lika med   | `"Status" <> 'Done'` |
|     >      |  Större än | `"Date" > '2018-06-18'` |
|     >=     |  Större än eller lika med | `"Priority" >= '5'` |
|     <      |  Mindre än   | `"Age" < '32'` |
|     <=     |  Mindre än eller lika med  | `"Company" <= 'Contoso'` |
|    AND     |  Logiska och  | `"Rank" >= '010' AND "Rank" < '100'` |
|     ELLER     | Logiska eller   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> Det finns två ytterligare operatorer, inte lika med eller logiska eller, som tillåts i villkors `x-ms-if-tags` huvudet för BLOB-åtgärder, men som inte finns i `Find Blobs by Tags` åtgärden.

## <a name="platform-integrations-with-blob-index-tags"></a>Plattforms integrering med BLOB-Taggar

BLOB index-taggar kan inte bara hjälpa dig att kategorisera, hantera och söka efter dina BLOB-data, utan även tillhandahålla integrering med andra Blob Storage funktioner, till exempel [livs cykel hantering](storage-lifecycle-management-concepts.md).

### <a name="lifecycle-management"></a>Livs cykel hantering

Genom att använda `blobIndexMatch` som regel filter i livs cykel hantering kan du flytta data till låg frekventa nivåer eller ta bort data baserat på de index taggar som används för dina blobbar. Du kan vara mer detaljerad i reglerna och bara flytta eller ta bort blobbar om de matchar de angivna taggarna.

Du kan ange en BLOB-index matchning som en fristående filter uppsättning i en livs cykel regel för att tillämpa åtgärder på taggade data. Eller så kan du kombinera både ett prefix och ett BLOB-index för att matcha mer detaljerade data uppsättningar. Om du anger flera filter i en livs cykel regel tillämpas en logisk åtgärd och en åtgärd. Åtgärden gäller endast om *alla* filter villkor matchar.

Följande exempel på en hanterings regel för livs cykel gäller för att blockera blobbar i en behållare med namnet *videofiles*. Regel skikten är blobar endast till Arkiv lag ring om data matchar BLOB index tag gen kriterier för `"Status" == 'Processed' AND "Source" == 'RAW'` .

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Exempel på en matchnings regel för BLOB-index för livs cykel hantering i Azure Portal](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```

---

## <a name="permissions-and-authorization"></a>Behörigheter och auktorisering

Du kan ge åtkomst till BLOB-index-Taggar med någon av följande metoder:

- Använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge behörighet till ett Azure Active Directory (Azure AD) säkerhets objekt. Använd Azure AD för överlägsen säkerhet och enkel användning. Mer information om hur du använder Azure AD med BLOB-åtgärder finns i [bevilja åtkomst till blobbar och köer med hjälp av Azure Active Directory](../common/storage-auth-aad.md).
- Använda en signatur för delad åtkomst (SAS) för att delegera åtkomst till BLOB-index. Mer information om signaturer för delad åtkomst finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md).
- Använda konto åtkomst nycklar för att godkänna åtgärder med delad nyckel. Mer information finns i [auktorisera med delad nyckel](/rest/api/storageservices/authorize-with-shared-key).

BLOB index-taggar är en under resurs till BLOB-data. En användare med behörigheter eller en SAS-token för att läsa eller skriva blobbar kanske inte har åtkomst till BLOB-taggarna.

### <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Anropare som använder en [Azure AD-identitet](../common/storage-auth-aad.md) kan beviljas följande behörigheter för att arbeta med BLOB-taggar.

| Tag-åtgärder för BLOB index                                          | Azure RBAC-åtgärd                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [Ange BLOB-Taggar](/rest/api/storageservices/set-blob-tags)           | Microsoft. Storage/storageAccounts/blobServices/containers/blobbar/Taggar/skrivning    |
| [Hämta BLOB-Taggar](/rest/api/storageservices/get-blob-tags)           | Microsoft. Storage/storageAccounts/blobServices/containers/blobbar/Taggar/läsa     |
| [Hitta blobbar efter Taggar](/rest/api/storageservices/find-blobs-by-tags) | Microsoft. Storage/storageAccounts/blobServices/containers/blobbar/filter/åtgärd |

Ytterligare behörigheter, åtskilda från underliggande BLOB-data, krävs för index tag-åtgärder. Rollen som [data ägare för Storage BLOB](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) beviljas behörigheter för alla tre BLOB index tag-åtgärder. [Storage BLOB data Reader](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) beviljas bara behörigheter för och- `Find Blobs by Tags` `Get Blob Tags` åtgärder.

### <a name="sas-permissions"></a>SAS-behörigheter

Anropare som använder en [signatur för delad åtkomst (SAS)](../common/storage-sas-overview.md) kan beviljas begränsade behörigheter för att arbeta med BLOB-taggar.

#### <a name="blob-sas"></a>BLOB SAS

Följande behörigheter kan beviljas i en BLOB-SÄKERHETSASSOCIATIONER för att tillåta åtkomst till BLOB index-taggar. Enbart Läs-och Skriv behörighet för BLOB räcker inte för att tillåta läsning eller skrivning av index taggar.

| Behörighet | URI-symbol | Tillåtna åtgärder                |
|------------|------------|-----------------------------------|
| Index Taggar |     t      | Hämta och ange index taggar för en BLOB |

#### <a name="container-sas"></a>Behållar-SAS

Följande behörigheter kan beviljas i en behållar-SÄKERHETSASSOCIATIONER för att tillåta filtrering på BLOB-taggar. `Blob List`Behörigheten är inte tillräcklig för att tillåta filtrering av blobbar med index taggar.

| Behörighet | URI-symbol | Tillåtna åtgärder         |
|------------|------------|----------------------------|
| Index Taggar |     f      | Hitta blobbar med index Taggar |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Välja mellan metadata och blob-Taggar

Både BLOB-Taggar och metadata ger möjlighet att lagra godtyckliga användardefinierade nyckel värdes egenskaper tillsammans med en BLOB-resurs. Båda kan hämtas och anges direkt, utan att returnera eller ändra innehållet i blobben. Det går att använda både metadata och index taggar.

Endast index-Taggar indexeras automatiskt och görs sökbara av den interna Blob Storages tjänsten. Det går inte att indexera eller söka i metadata internt. Du måste använda en separat tjänst, till exempel [Azure Search](../../search/search-blob-ai-integration.md). BLOB index-Taggar har ytterligare behörigheter för att läsa, filtrera och skriva som är åtskilda från underliggande BLOB-data. Metadata använder samma behörigheter som blobben och returneras som HTTP-huvuden av åtgärderna [Get BLOB](/rest/api/storageservices/get-blob) och [Get BLOB Properties](/rest/api/storageservices/get-blob-properties) . BLOB index-taggarna krypteras i vila med en [Microsoft-hanterad nyckel](../common/storage-service-encryption.md). Metadata krypteras i vila med samma krypterings nyckel som angetts för BLOB-data.

I följande tabell sammanfattas skillnaderna mellan metadata och blob-index Taggar:

|              |   Metadata   |   BLOB index-Taggar  |
|--------------|--------------|--------------------|
| **Gränser**      | Ingen numerisk gräns, 8 KB totalt, SKIFT läges okänsligt | 10 Taggar per BLOB Max, 768 byte per tagg, SKIFT läges känslig |
| **Uppdateringar**    | Tillåts inte på Arkiv nivå, `Set Blob Metadata` ersätter alla befintliga metadata, `Set Blob Metadata` ändrar blobens senaste ändrings tid | Tillåts för alla åtkomst nivåer, `Set Blob Tags` ersätter alla befintliga taggar, `Set Blob Tags` ändrar inte blobens senaste ändrings tid |
| **Storage**     | Lagrad med BLOB-data | Under resurs för BLOB-data |
| **Indexerar & frågor** | Måste använda en separat tjänst, till exempel Azure Search | Indexering och frågor om funktioner som är inbyggda i Blob Storage |
| **Kryptering** | Krypterat i vila med samma krypterings nyckel som används för BLOB-data | Krypterat i vila med en Microsoft-hanterad krypterings nyckel |
| **Prissättning** | Storleken på metadata ingår i lagrings kostnaderna för en BLOB | Fast kostnad per index-tagg |
| **Rubrik svar** | Metadata returnerade som huvuden i `Get Blob` och `Get Blob Properties` | Tagg antal returnerat av `Get Blob` eller `Get Blob Properties` , taggar som returnerades endast av `Get Blob Tags` och `List Blobs` |
| **Behörigheter**  | Läs-eller Skriv behörighet till BLOB-data utökas till metadata | Ytterligare behörigheter krävs för att läsa, filtrera eller skriva index Taggar |
| **Namngivning** | Metadata-namn måste följa namngivnings reglerna för C#-identifierare | BLOB index-Taggar har stöd för ett bredare antal alfanumeriska tecken |

## <a name="pricing"></a>Priser

Priser för BLOB index är i offentlig för hands version och kan komma att ändras för allmän tillgänglighet. Du debiteras det genomsnittliga månatliga antalet index etiketter i ett lagrings konto. Indexerings motorn kostar inget. Begär anden till `Set Blob Tags` , `Get Blob Tags` och `Find Blobs by Tags` debiteras enligt deras respektive åtgärds typer. Mer information finns i avsnittet [om Block-Blob-prissättning](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability-and-storage-account-support"></a>Stöd för regional tillgänglighet och lagrings konto

BLOB index-taggar är bara tillgängliga på Generell användning v2-konton (GPv2) med hierarkiskt namn område (HNS) inaktiverat. Generell användning-konton (GPV1) stöds inte, men du kan uppgradera valfritt GPv1-konto till ett GPv2-konto.

Index Taggar stöds inte för Premium Storage-konton. Mer information om lagrings konton finns i [Översikt över Azure Storage-konto](../common/storage-account-overview.md).

BLOB index-taggar är för närvarande tillgängliga i alla offentliga regioner.

Om du vill komma igång läser du [använda taggar för BLOB-index för att hantera och söka efter data](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Du måste registrera din prenumeration innan du kan använda för hands versionen av BLOB-indexet på dina lagrings konton. Se avsnittet [villkor och kända problem](#conditions-and-known-issues) i den här artikeln.

### <a name="register-your-subscription-preview"></a>Registrera din prenumeration (för hands version)

Eftersom BLOB index-taggarna endast finns i en offentlig för hands version måste du registrera din prenumeration innan du kan använda funktionen. Kör följande PowerShell-eller CLI-kommandon om du vill skicka en begäran.

#### <a name="register-by-using-powershell"></a>Registrera med PowerShell

```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Registrera med Azure CLI

```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues"></a>Villkor och kända problem

I det här avsnittet beskrivs kända problem och villkor i den offentliga för hands versionen av BLOB index-taggar. Den här funktionen bör inte användas för produktions arbets belastningar förrän den når allmän tillgänglighet (GA) när beteendet kan ändras.

- För för hands versionen måste du först registrera din prenumeration innan du kan använda BLOB-index för ditt lagrings konto i för hands versions regionerna.
- Endast GPv2-konton stöds i för hands versionen. BLOB-, BlockBlobStorage-och HNS-aktiverade DataLake Gen2-konton stöds inte. GPv1-konton stöds inte.
- Överföring av sid-blobar med index Taggar bevarar inte taggarna. Ange taggar efter överföring av en sid-blob.
- När filtreringen är begränsad till en enda behållare kan det `@container` bara skickas om alla index Taggar i filter uttrycket är likhets kontroller (nyckel = värde).
- När du använder intervall operatorn med `AND` villkoret kan du bara ange samma index etikett nyckel namn ( `"Age" > '013' AND "Age" < '100'` ).
- Versions-och blob-index stöds inte. BLOB index-taggar bevaras för versioner, men skickas inte till BLOB-databasmotorn.
- Det finns inget API för att avgöra om index taggar är indexerade.
- Redundansväxling av kontot stöds inte. BLOB-indexet kanske inte uppdateras korrekt efter redundansväxlingen.
- Livs cykel hantering stöder bara likhets kontroller med BLOB-index matchning.
- `Copy Blob` kopierar inte BLOB-index-taggar från käll-bloben till den nya mål-bloben. Du kan ange de taggar som du vill använda för mål-bloben under kopierings åtgärden.
- `Copy Blob` (Asynkron kopia) från ett annat lagrings konto med tillämpade taggar på mål-bloben gör att BLOB-index motorn inte returnerar blobben och dess Taggar i filter uppsättningen. Använd `Copy Blob` från URL (synkronisera kopia).
- Taggarna sparas när ögonblicks bilder skapas. Att befordra en ögonblicks bild stöds dock inte och kan resultera i en tom tag-uppsättning.

## <a name="faq"></a>Vanliga frågor

**Kan du använda BLOB-index för att filtrera och fråga efter innehåll i mina blobbar?**

Nej, om du behöver söka i dina BLOB-data använder du Query acceleration eller Azure Search.

**Finns det några krav på index märknings värden?**

BLOB index-Taggar stöder bara sträng data typer och frågor returnerar resultat med lexicographical-ordning. För siffror är siffran noll. För datum och tider, lagra som ett ISO 8601-kompatibelt format.

**Är BLOB-index Taggar och Azure Resource Manager Taggar relaterade?**

Nej, Resource Manager-Taggar hjälper till att organisera kontroll Plans resurser som prenumerationer, resurs grupper och lagrings konton. Index Taggar tillhandahåller BLOB-hantering och identifiering av data planet.

## <a name="next-steps"></a>Nästa steg

Ett exempel på hur du använder BLOB-index finns i [använda BLOB-index för att hantera och söka efter data](storage-blob-index-how-to.md).

Lär dig mer om [livs cykel hantering](storage-lifecycle-management-concepts.md) och ange en regel med matchning av BLOB-index.
