---
title: Hämta data uppsättningar med stor kostnad återkommande med export
description: Den här artikeln hjälper dig att regelbundet exportera stora mängder data med export från Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 465225341bdffc984ac6cbc82ba94eb656ad60df
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509702"
---
# <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Hämta data uppsättningar med stor kostnad återkommande med export

Den här artikeln hjälper dig att regelbundet exportera stora mängder data med export från Azure Cost Management. Att exportera är det rekommenderade sättet att hämta icke sammansatta kostnadsdata. Det gäller särskilt när användningsfilerna är för stora för att kunna anropas och laddas ned på ett pålitligt sätt med hjälp av API:et för användningsinformation. Exporterade data placeras i det Azure Storage-konto som du väljer. Därifrån kan du läsa in data i dina egna system och analysera dem efter behov. Information om hur du konfigurerar exporter i Azure-portalen finns i [Exportera data](tutorial-export-acm-data.md).

Om du vill automatisera exporter i olika omfång är exempel-API:ets begäran i nästa avsnitt en bra utgångspunkt. Du kan använda export-API:et för att skapa automatiska exporter som en del av din allmänna miljökonfiguration. Med automatiska exporter ser du till att du har de data du behöver. Du kan använda dem i din organisations system när du utökar användningen av Azure.

## <a name="common-export-configurations"></a>Vanliga exportkonfigurationer

Innan du skapar din första export bör du ta hänsyn till ditt scenario och de konfigurationsalternativ som är nödvändiga för det. Ta följande exportalternativ som exempel:

- **Upprepning** – Bestäm hur ofta exportjobbet ska köras och när en fil ska placeras i ditt Azure Storage-konto. Välj mellan varje dag, varje vecka och varje månad. Försök att konfigurera upprepningen så att den matchar de dataimportjobb som används av organisationens interna system.
- **Upprepningsperiod** – Bestäm hur länge exporten ska vara giltig. Filerna exporteras endast under upprepningsperioden.
- **Tidsram** – Bestäm mängden data som ska genereras av exporten vid en specifik körning. Vanliga alternativ är MonthToDate och WeekToDate.
- **StartDate** – Konfigurera när du vill att exportschemat ska börja. En export skapas på StartDate och därefter baserat på din upprepning.
- **Typ** – Det finns tre exporttyper:
  - ActualCost – Visar den totala användningen och kostnaderna för den angivna perioden, allt eftersom de ackumuleras och visas på fakturan.
  - AmortizedCost – Visar den totala användningen och kostnaderna för den angivna perioden, med amortering tillämpad på de reservationsinköpskostnader som gäller.
  - Användning – Alla exporter som skapats före 20 juli 2020 är av typen Användning. Uppdatera alla schemalagda exporter som antingen ActualCost eller AmortizedCost.
- **Kolumner** – Definierar de datafält som du vill ska ingå i exportfilen. De motsvarar de fält som är tillgängliga i API:et för användningsinformation. Mer information finns i [API för användningsinformation](/rest/api/consumption/usagedetails/list).

## <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Skapa en daglig export för perioden hittills under månaden för en prenumeration

URL för begäran: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

## <a name="copy-large-azure-storage-blobs"></a>Kopiera stora Azure Storage-blobbar

Du kan använda Cost Management för att schemalägga export av Azures användnings information till dina Azure Storage-konton som blobbar. De resulterande BLOB-storlekarna kan vara över gigabyte i storlek. Azure Cost Management teamet arbetade med Azure Storages teamet för att testa kopiering av stora Azure Storage-blobbar. Resultaten beskrivs i följande avsnitt. Du kan vänta på liknande resultat när du kopierar Storage-blobbar från en Azure-region till en annan.

För att testa prestandan har team överfört blobbar från lagrings konton i regionen USA, västra till samma och andra regioner. Gruppen uppmätta hastigheter som sträcker sig från 2 GB per sekund i samma region till 150 MB per sekund till lagrings konton i den Asien, sydöstra regionen.

### <a name="test-configuration"></a>Testa konfiguration

För att mäta BLOB-överföringshastigheter skapade teamet ett enkelt .NET-konsol program som refererar till den senaste versionen (v 2.0.1) av Azure Data flytt Library (DLM) via NuGet. DLM är ett SDK som tillhandahålls av Azure Storage-teamet som underlättar programmatisk åtkomst till deras överförings tjänster. Sedan skapade de standard v2-lagrings konton i flera regioner och använder västra USA som käll region. De fyllde lagrings kontona där med behållare, där var och en är 10 2-GB block blobbar. De kopierade Behållarna till andra lagrings konton med hjälp av DLM _-metoden TransferManager. CopyDirectoryAsync ()_ med alternativet _CopyMethod. ServiceSideSyncCopy_ . Testerna utfördes på en dator som kör Windows 10 med 12 kärnor och 1 – GbE-nätverk.

Program inställningar som används:

- _TransferManager.Configurations. ParallelOperations_-  =  _miljö. ProcessorCount \* 32_. Teamet upptäckte att inställningen har störst inverkan på det totala data flödet. Värdet 32 gånger antalet kärnor tillhandahöll det bästa data flödet för test klienten.
- _ServicePointManager. DefaultConnectionLimit = int. MaxValue_. Genom att ställa in det på ett högsta värde skickas fullständig kontroll över överförings parallellitet till _ParallelOperations_ -inställningen ovan.
- _TransferManager.Configurations. Block storlek = 4 194 304_. Det hade viss påverkan på överförings priser med 4 MB, vilket bevisar att det är bäst för testning.

Mer information och exempel kod finns i länkar i avsnittet [Nästa steg](#next-steps) .

### <a name="test-results"></a>Testresultat

| **Test nummer** | **Till region** | **Blobar** | **Tid (SEK)** | **MB/s** | **Kommentarer** |
| --- | --- | --- | --- | --- | --- |
| 1 | WestUS | 2 GB x 10 | 10 | 2 000 |   |
| 2 | WestUS2 | 2 GB x 10 | 33 | 600 |   |
| 3 | Platsen eastus | 2 GB x 10 | 67 | 300 |   |
| 4 | Platsen eastus | 2 GB x 10 x 4 | 99 | 200 | 4 parallella överföringar med 8 lagrings konton: 4 västra till 4 East-genomsnitt per överföring |
| 6 | Platsen eastus | 2 GB x 10 x 4 | 92 | 870 | 4 parallella överföringar från 1 lagrings konto till ett annat |
| 5 | Platsen eastus | 2G x 10 x 8 | 148 | 135 | 8 parallella överföringar med 8 lagrings konton: 4 väst till 4x2 East-genomsnitt per överföring |
| 7 | SE Asien | 2 GB x 10 | 133 | 150 |   |
| 8 | SE Asien | 2 GB x 10 x 4 | 444 | 180 | 4 parallella överföringar från 1 lagrings konto till ett annat |

### <a name="sync-transfer-characteristics"></a>Synkronisera överförings egenskaper

Här följer några av egenskaperna för den Sync-överföring på service sidan som används med DML som är relevanta för dess användning:

- DML kan överföra en enda BLOB eller en katalog. För katalog överföring kan du använda ett Sök mönster för att matcha på BLOB-prefix.
- Blockering av BLOB-överföringar sker parallellt. Allt klart mot slutet av överförings processen. Enskilda blob-block överförs parallellt.
- Överföringen körs asynkront på klienten. Överförings statusen är tillgänglig regelbundet via ett återanrop till en metod som kan definieras i ett _TransferContext_ -objekt.
- Överföringen skapar kontroll punkter under dess förlopp och exponerar ett _TransferCheckpoint_ -objekt. Objektet representerar den senaste kontroll punkten via _TransferContext_ -objektet. Om _TransferCheckpoint_ sparas innan en överföring avbryts/avbryts, kan överföringen återupptas från kontroll punkten i upp till sju dagar. Överföringen kan återupptas från alla kontroll punkter, inte bara den senaste.
- Om överförings klient processen stoppas och startas om utan att du implementerar kontroll punkts funktionen.
  - Överföringen startas om innan eventuella BLOB-överföringar har slutförts.
  - När några av blobarna har slutförts startas överföringen om för bara de ofullständiga Blobbarna.
- Om du pausar klient körningen pausas överföringarna.
- Funktionen BLOB transfer sammanfattar klienten från tillfälliga haverier. Lagrings kontots begränsning orsakar till exempel normalt inte att överföringen Miss fungerar, men överföringen går långsamt.
- Överföringar på tjänst sidan har låg klient resursanvändning för processor och minne, viss nätverks bandbredd och anslutningar.

### <a name="async-transfer-characteristics"></a>Egenskaper för asynkron överföring

Du kan anropa metoden _TransferManager. CopyDirectoryAsync ()_ med alternativet _CopyMethod. ServiceSideAsyncCopy_ . Den fungerar på samma sätt som mekanismen för synkronisering av synkronisering från klient perspektivet, men med följande skillnader i drift:

- Överförings takten är mycket långsammare än den motsvarande Sync-överföringen (vanligt vis 10 MB/s eller mindre).
- Överföringen fortsätter även om klient processen avslutas.
- Även om kontroll punkter stöds, återupptas överföring med en _TransferCheckpoint_ inte vid kontroll punkts tiden, men vid överföringens aktuella tillstånd.

### <a name="test-summary"></a>Test Sammanfattning

Azure Blob Storage har stöd för höga globala överförings hastigheter med funktionen Sync-överföring på tjänst sidan. Det är enkelt att använda funktionen i .NET-program med hjälp av data flyttnings biblioteket. Cost Management exporter för att på ett tillförlitligt sätt kopiera hundratals gigabyte data till ett lagrings konto var som helst på mindre än en timme.

## <a name="next-steps"></a>Nästa steg

- Se källan för [Microsoft Azure Storage data flytt bibliotek](https://github.com/Azure/azure-storage-net-data-movement) .
- [Överför data med biblioteket för data förflyttning](../../storage/common/storage-use-data-movement-library.md).
- Se exemplet på [AzureDmlBackup exempel programs](https://github.com/markjbrown/AzureDmlBackup) källa.
- Läs [hög genom strömning med Azure Blob Storage](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage).