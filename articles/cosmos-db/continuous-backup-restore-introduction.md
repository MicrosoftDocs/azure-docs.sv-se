---
title: Kontinuerlig säkerhets kopiering med funktionen för återställning av tidpunkt i Azure Cosmos DB
description: Azure Cosmos DB funktionen för återställning av en viss tidpunkt hjälper till att återställa data från en oavsiktlig skrivning, borttagnings åtgärd eller för att återställa data i valfri region. Läs om priser och aktuella begränsningar.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: d1dc108ecec93dddeb768eb61af425ba67f23002
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393147"
---
# <a name="continuous-backup-with-point-in-time-restore-preview-feature-in-azure-cosmos-db"></a>Kontinuerlig säkerhets kopiering med funktionen för återställning av en viss tidpunkt (för hands version) i Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funktionen för återställning av tidpunkt (kontinuerlig säkerhets kopiering) för Azure Cosmos DB är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB funktionen för återställning av en viss tidpunkt (för hands version) hjälper till med flera scenarier, till exempel följande:

* För att återställa från en oavsiktlig Skriv-eller borttagnings åtgärd i en behållare.
* Återställa ett borttaget konto, en databas eller en behållare.
* För att återställa till en region (där säkerhets kopior fanns) vid återställnings tidpunkten.

Azure Cosmos DB utför säkerhets kopiering av data i bakgrunden utan att behöva använda extra allokerat data flöde (ru: er) eller påverka databasens prestanda och tillgänglighet. Kontinuerliga säkerhets kopieringar görs i varje region där kontot finns. Följande bild visar hur en behållare med Skriv region i västra USA, läser regioner i östra och östra USA 2 säkerhets kopie ras till ett Azure Blob Storage-konto i respektive region. Som standard lagrar varje region säkerhets kopian i lokalt redundanta lagrings konton. Om regionen har aktiverade [tillgänglighets zoner](high-availability.md#availability-zone-support) lagras säkerhets kopian i Zone-Redundant lagrings konton.

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Azure Cosmos DB säkerhets kopiering av data till Azure-Blob Storage." lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

Det tillgängliga tidsfönstret för återställning (kallas även kvarhållningsperiod) är det lägre värdet av följande två: *30 dagar tillbaka tidigare från nu* eller *upp till resursens skapande tid*. Tidpunkten för återställning kan vara valfri tidsstämpel inom kvarhållningsperioden.

I offentlig för hands version kan du återställa Azure Cosmos DB-kontot för SQL API eller MongoDB-innehåll i tid till ett annat konto med [Azure Portal](continuous-backup-restore-portal.md), [Azure Command Line Interface](continuous-backup-restore-command-line.md) (az CLI), [Azure PowerShell](continuous-backup-restore-powershell.md)eller [Azure Resource Manager](continuous-backup-restore-template.md).

## <a name="what-is-restored"></a>Vad återställs?

I ett stabilt tillstånd säkerhets kopie ras alla mutationer som utförs på käll kontot (som innehåller databaser, behållare och objekt) asynkront inom 100 sekunder. Om säkerhets kopierings mediet (som är Azure-lagring) är otillgängligt eller inte tillgängligt behålls mutationerna lokalt tills mediet är tillgängligt och sedan töms de för att förhindra förlust av åtgärder som kan återställas.

Du kan välja att återställa en kombination av etablerade data flödes behållare, delade data flödes databaser eller hela kontot. Restore-åtgärden återställer alla data och dess index egenskaper till ett nytt konto. Med återställnings processen ser du till att alla data som återställs i ett konto, en databas eller en behållare är konsekventa med den angivna återställnings tiden. Varaktigheten för återställning beror på mängden data som behöver återställas.

> [!NOTE]
> Med läget för kontinuerlig säkerhets kopiering tas säkerhets kopiorna i varje region där ditt Azure Cosmos DB-konto är tillgängligt. Säkerhets kopieringar som görs för varje region konto är lokalt redundanta som standard och zonen är redundanta om ditt konto har aktiverat funktionen [tillgänglighets zon](high-availability.md#availability-zone-support) för den regionen. Restore-åtgärden återställer alltid data till ett nytt konto.

## <a name="what-is-not-restored"></a>Vad återställs inte?

Följande konfigurationer återställs inte efter tidpunkts återställningen:

* Inställningar för brand vägg, VNET, privat slut punkt.
* Konsekvens inställningar. Som standard återställs kontot med konsekvens i sessionen.  
* Områdena.
* Lagrade procedurer, utlösare, UDF: er.

Du kan lägga till dessa konfigurationer till det återställda kontot när återställningen har slutförts.

## <a name="restore-scenarios"></a>Återställningsscenarier

Här följer några av de viktiga scenarier som åtgärdas med funktionen för återställning av tidpunkter. Scenarier [a] till [c] visar hur du utlöser en återställning om tidsstämpeln för återställning är känd i förväg.
Det kan dock finnas scenarier där du inte vet den exakta tiden för oavsiktlig borttagning eller skada. Scenarier [d] och [e] visar hur du _identifierar_ tidsstämpeln för återställning med hjälp av de nya API: erna för händelse-feed på återställas-databasen eller behållare.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="Livs cykel händelser med tidsstämplar för ett återställas-konto." lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

a. **Återställ borttaget konto** – alla borttagna konton som du kan återställa visas från rutan **Återställ** . Till exempel om *konto A* tas bort vid tidsstämpeln T3. I det här fallet är tidsstämpeln precis före T3, plats, mål konto namn, resurs grupp och mål konto namn tillräckligt för att återställa från [Azure Portal](continuous-backup-restore-portal.md#restore-deleted-account), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)eller [CLI](continuous-backup-restore-command-line.md#trigger-restore).  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="Livs cykel händelser med tidsstämplar för en återställas-databas och-behållare." lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

b. **Återställ data för ett konto i en viss region** – till exempel om *konto a* finns i två regioner *östra USA* och *västra USA* vid tidsstämpeln T3. Om du behöver en kopia av kontot A i *västra USA* kan du göra en tidpunkts återställning från [Azure Portal](continuous-backup-restore-portal.md), [POWERSHELL](continuous-backup-restore-powershell.md#trigger-restore)eller [CLI](continuous-backup-restore-command-line.md#trigger-restore) med västra USA som mål plats.

c. **Återställ från en oavsiktlig Skriv-eller borttagnings åtgärd i en behållare med en känd Restore-tidsstämpel** – till exempel om du **vet** att innehållet i *container 1* i *databas 1* har ändrats av misstag vid tidsstämpel T3. Du kan göra en återställning av en tidpunkt från [Azure Portal](continuous-backup-restore-portal.md#restore-live-account), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)eller [CLI](continuous-backup-restore-command-line.md#trigger-restore) till ett annat konto vid tidsstämpeln T3 för att återställa det önskade tillståndet för behållaren.

d. **Återställ ett konto till en tidigare tidpunkt innan den oavsiktliga borttagningen av databasen** – i [Azure Portal](continuous-backup-restore-portal.md#restore-live-account)kan du använda fönstret för händelse inmatning för att avgöra när en databas har tagits bort och hitta återställnings tiden. På samma sätt kan du med [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) och [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)identifiera händelsen borttagning av databasen genom att räkna upp flödet för databas händelser och sedan utlösa kommandot Restore med de obligatoriska parametrarna.

e. **Återställ ett konto till en tidigare tidpunkt innan du tar bort eller ändrar behållar egenskaperna av misstag.** – I [Azure Portal](continuous-backup-restore-portal.md#restore-live-account)kan du använda fönstret för händelse inmatning för att fastställa när en behållare har skapats, ändrats eller tagits bort för att hitta återställnings tiden. På samma sätt kan du med [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) och [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)identifiera alla behållar händelser genom att räkna upp flödet för behållar händelser och sedan utlösa kommandot Restore med nödvändiga parametrar.

## <a name="permissions"></a>Behörigheter

Med Azure Cosmos DB kan du isolera och begränsa återställnings behörigheter för kontinuerligt säkerhets kopierings konto till en speciell roll eller ett huvud konto. Kontots ägare kan utlösa en återställning och tilldela en roll till andra huvud konton för att utföra återställnings åtgärden. Mer information finns i artikeln [behörigheter](continuous-backup-restore-permissions.md) .

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>Prissättning

Azure Cosmos DB konton som har kontinuerlig säkerhets kopiering aktiverat debiteras ytterligare en månatlig avgift för att *lagra säkerhets kopian* och *återställa dina data*. Restore Cost läggs till varje gång återställnings åtgärden initieras. Om du konfigurerar ett konto med kontinuerlig säkerhets kopiering men inte återställer data, ingår bara lagrings kostnaden för säkerhets kopiering på fakturan.

Följande exempel baseras på priset för ett Azure Cosmos-konto som distribueras i en icke-myndighets region i USA. Prissättningen och beräkningen kan variera beroende på vilken region du använder, se [sidan Azure Cosmos DB prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/) för den senaste pris informationen.

* Alla konton som aktive ras med kontinuerlig säkerhets kopierings policy påförs ytterligare en månatlig avgift för säkerhets kopierings lagring som beräknas enligt följande:

  $0,20/GB * data storlek i GB i konto * antal regioner

* Varje återställnings-API-anrop påförs en engångs avgift. Avgiften är en funktion av mängden data återställning och beräknas enligt följande:

  $0.15/GB * data storlek i GB.

Om du till exempel har 1 – TB data i två regioner gör du följande:

* Lagrings kostnaden för säkerhets kopiering beräknas som (1000 * 0,20 * 2) = $400 per månad

* Restore Cost beräknas som (1000 * 0,15) = $150 per återställning

## <a name="current-limitations-public-preview"></a>Aktuella begränsningar (offentlig för hands version)

För närvarande är tidpunkten för att återställa till en offentlig för hands version och har följande begränsningar:

* Det är bara Azure Cosmos DB-API:er för SQL och MongoDB som stöds för kontinuerlig säkerhetskopiering. API:er för Cassandra, Table och Gremlin stöds ännu inte.

* Det går inte att konvertera ett befintligt konto med standard principen för periodisk säkerhets kopiering till att använda kontinuerligt säkerhets kopierings läge.

* Azure-suveräna och Azure Government moln regioner stöds ännu inte.

* Konton med Kundhanterade nycklar stöds inte för att använda kontinuerlig säkerhets kopiering.

* Skriv konton med flera regioner stöds inte.

* Konton med Synapse-länk aktiverat stöds inte.

* Det återställda kontot skapas i samma region där källkontot finns. Det går inte att återställa ett konto till en region där det inte finns något källkonto.

* Återställnings fönstret är bara 30 dagar och kan inte ändras.

* Säkerhetskopieringarna är inte automatiskt skyddade mot geohaverier. Du måste uttryckligen lägga till en annan region för att ha återhämtning av kontot och säkerhetskopian.

* När en återställning pågår ska du inte ändra eller ta bort principer för identitets-och åtkomst hantering (IAM) som ger behörighet för kontot eller ändra eventuella VNET-och brand Väggs konfiguration.

* Azure Cosmos DB-API:et för SQL- eller MongoDB-konton som skapar ett unikt index när containern har skapats, stöds inte för kontinuerlig säkerhetskopiering. Det är bara containrar som skapar ett unikt index när den initiala containern skapas som stöds. För MongoDB-konton skapar du ett unikt index med hjälp av [tilläggs kommandon](mongodb-custom-commands.md).

* Funktionen för återställning till tidpunkt återställer alltid till ett nytt Azure Cosmos-konto. Det finns för närvarande inte stöd för att återställa till ett befintligt konto. Om du är intresse rad av att ge feedback om återställning på plats kan du kontakta Azure Cosmos DB-teamet via din konto representant eller [UserVoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

* Alla nya API: er som exponeras för List `RestorableDatabaseAccount` ,,, kan `RestorableSqlDatabases` `RestorableSqlContainer` `RestorableMongodbDatabase` `RestorableMongodbCollection` ändras när funktionen är i för hands version.

* Efter återställning är det möjligt att det konsekventa indexet kan återskapas för vissa samlingar. Du kan kontrol lera status för återställnings åtgärden via egenskapen [IndexTransformationProgress](how-to-manage-indexing-policy.md) .

* Med återställningsprocessen återställs alla egenskaper för en container, inklusive dess TTL-konfiguration. Därför är det möjligt att data som har återställts tas bort omedelbart om du har konfigurerat detta. För att förhindra detta måste tidsstämpeln för återställningen vara innan TTL-egenskaperna lades till i containern.

## <a name="next-steps"></a>Nästa steg

* Konfigurera och hantera kontinuerlig säkerhets kopiering med [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)eller [Azure Resource Manager](continuous-backup-restore-template.md).
* [Hantera behörigheter](continuous-backup-restore-permissions.md) som krävs för att återställa data med kontinuerligt säkerhets kopierings läge.
* [Resurs modell för kontinuerligt säkerhets kopierings läge](continuous-backup-restore-resource-model.md)
