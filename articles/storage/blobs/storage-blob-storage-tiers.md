---
title: Åtkomst nivåer för Azure Blob Storage – frekvent, låg frekvent och Arkiv lag ring
description: Läs om åtkomst nivåer för frekvent åtkomst, låg frekvent åtkomst och arkivering för Azure Blob Storage. Granska lagrings konton som stöder skiktning.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: klaasl
ms.openlocfilehash: 67534e70904c70f7bf9dda44502e723916bdce93
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928801"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Åtkomst nivåer för Azure Blob Storage – frekvent, låg frekvent och Arkiv lag ring

Azure Storage erbjuder olika åtkomst nivåer, så att du kan lagra BLOB-Datadata på det mest kostnads effektiva sättet. Tillgängliga åtkomst nivåer är:

- **Snabb** – optimerad för att lagra data som används ofta.
- **Cool** -optimerad för att lagra data som inte används ofta och som lagras i minst 30 dagar.
- **Arkiv** -optimerad för att lagra data som sällan används och lagras i minst 180 dagar med flexibla latens krav, i ordningen av timmar.

Följande överväganden gäller för olika åtkomstnivåer:

- Åtkomst nivån kan ställas in på en BLOB under eller efter uppladdning.
- Endast de lågfrekventa och frekventa åtkomstnivåerna kan anges på kontonivå. Arkiv åtkomst nivån kan bara ställas in på blobnivå.
- Data i låg frekvent åtkomst nivå har något lägre tillgänglighet, men har fortfarande hög hållbarhet, hämtnings tid och data flödes egenskaper som liknar frekventa data. För låg frekventa data är något lägre tillgänglighet och högre åtkomst kostnader acceptabla för att minska den totala lagrings kostnaden jämfört med heta data. Mer information finns i [SLA för Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).
- Data i Arkiv åtkomst nivån lagras offline. Arkiv nivån ger de lägsta lagrings kostnaderna, men även de högsta åtkomst kostnaderna och svars tiden.
- Frekventa och låg frekventa nivåer stöder alla alternativ för redundans. Arkiv nivån stöder endast LRS, GRS och RA-GRS.
- Data lagrings gränser anges på konto nivå och inte per åtkomst nivå. Du kan välja att använda hela gränsen på en nivå eller på alla tre nivåer.

Data som lagras i molnet växer i exponentiell takt. Om du vill hålla kontroll på och optimera kostnaderna för dina växande lagringsbehov är det en bra idé att ordna data baserat på attribut som åtkomstfrekvens och planerad kvarhållningsperiod. Data som lagras i molnet kan vara olika beroende på hur de genereras, bearbetas och används under sin livslängd. Vissa data används aktivt och ändras under livslängden. Vissa data används ofta i början av livslängden och sedan minskar användning drastiskt när dessa data blir äldre. Vissa data är inaktiva i molnet och används sällan, om ens någonsin, efter att de har lagrats.

Var och en av dessa data åtkomst scenarier fördelar från en annan åtkomst nivå som är optimerad för ett visst åtkomst mönster. Med nivåerna frekvent åtkomst, låg frekvent åtkomst och Arkiv lag rings nivå kan Azure Blob Storage hantera olika åtkomst nivåer med separata pris modeller.

Följande verktyg och klient bibliotek stöder alla nivåer på BLOB-nivå och Arkiv lag ring.

- Azure Portal
- PowerShell
- Azure CLI-verktyg
- .NET-klientbibliotek
- Java-klientbibliotek
- Python-klientbibliotek
- Node.js klient bibliotek

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Lagringskonton med stöd för flera lagringsnivåer

Objekt lagrings data skiktning mellan frekvent, låg frekvent och arkivering stöds i Blob Storage-och Generell användning v2-konton (GPv2). Generell användning v1-konton (GPv1) stöder inte nivåer. Du kan enkelt konvertera befintliga GPv1 eller Blob Storage konton till GPv2-konton via Azure Portal. GPv2 tillhandahåller nya priser och funktioner för blobbar, filer och köer. Vissa funktioner och pris klipp erbjuds endast i GPv2-konton. Vissa arbets belastningar kan vara dyrare på GPv2 än GPv1. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

Blob Storage-och GPv2-konton exponerar attributet **åtkomst nivå** på konto nivå. Med det här attributet kan du ange standard åtkomst nivå för alla blobar som inte uttryckligen anges på objekt nivå. För objekt med den nivå som uttryckligen anges gäller inte konto nivån. Arkiv nivån kan endast tillämpas på objekt nivå. Du kan när som helst växla mellan åtkomst nivåer.

Använd GPv2 i stället för Blob Storage konton för skiktning. GPv2 har stöd för alla funktioner som Blob Storage-konton, plus mycket mer. Priserna mellan Blob Storage och GPv2 är nästan identiska, men vissa nya funktioner och pris klipp är bara tillgängliga på GPv2-konton.

Prisstrukturen för GPv1- och GPv2-konton skiljer sig åt, så kunderna bör noggrant utvärdera båda alternativen innan de bestämmer sig för att använda GPv2-konton. Du kan enkelt konvertera ett befintligt Blob Storage eller GPv1-konto till GPv2 via en enklicksprocess. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

## <a name="hot-access-tier"></a>Frekvent lagringsnivå

Den frekventa åtkomstnivån har högre lagringskostnader än lågfrekvent åtkomstnivå och arkivåtkomstnivå, men den har lägst åtkomstkostnader. Exempel på användningsscenarier för frekvent åtkomstnivå är:

- Data som används aktivt eller förväntas läsas från och skrivas ofta
- Data som mellanlagras för bearbetning och eventuell migrering till låg frekvent åtkomst nivå

## <a name="cool-access-tier"></a>Lågfrekvent åtkomstnivå

Den lågfrekventa åtkomstnivån har lägre lagringskostnader och högre åtkomstkostnader jämfört med frekvent lagring. Den här nivån är avsedd för data som är kvar på den lågfrekventa nivån i minst 30 dagar. Exempel på användningsscenarier för lågfrekvent åtkomstnivå är:

- Kortsiktig säkerhets kopiering och haveri beredskap
- Äldre data används inte ofta, men förväntas vara tillgängliga omedelbart vid åtkomst
- Stora data mängder som behöver lagras kostnads effektivt, medan mer data samlas in för framtida bearbetning

## <a name="archive-access-tier"></a>Arkivlagring

Arkiv åtkomst nivån har lägst lagrings kostnad men högre kostnader för data hämtning jämfört med frekventa och låg frekventa nivåer. Data måste finnas kvar på Arkiv nivå i minst 180 dagar eller omfattas av en avgift för tidig borttagning. Data på Arkiv nivån kan ta flera timmar att hämta beroende på den angivna ÅTERUPPVÄCKNING-prioriteten. För små objekt kan en rehydratisera med hög prioritet hämta objektet från arkivet under en timme. Mer information finns i avsnittet om [dehydratisera BLOB-data från Arkiv](storage-blob-rehydration.md) lag rings nivån.

När en BLOB finns i Arkiv lag ring är BLOB-data offline och kan inte läsas eller ändras. Om du vill läsa eller ladda ned en BLOB i arkivet måste du först extrahera den till en onlinenivå. Du kan inte ta ögonblicks bilder av en BLOB i Arkiv lag ring. BLOB-metadata är dock online och tillgängliga, så att du kan lista BLOB, egenskaper, metadata och blob-taggar. Det är inte tillåtet att ange eller ändra BLOB-metadata i arkivet. Du kan dock ange och ändra BLOB-index taggar. För blobbar i arkivet finns de enda giltiga åtgärderna för att [Hämta](/rest/api/storageservices/get-blob-properties)BLOB-egenskaper [, Hämta BLOB-metadata](/rest/api/storageservices/get-blob-metadata), [Ange BLOB](/rest/api/storageservices/set-blob-tags)-taggar, [Hämta BLOB-Taggar](/rest/api/storageservices/get-blob-tags), [hitta blobbar med Taggar](/rest/api/storageservices/find-blobs-by-tags), [list blobbar](/rest/api/storageservices/list-blobs), [Ange BLOB-nivå](/rest/api/storageservices/set-blob-tier), [Kopiera BLOB](/rest/api/storageservices/copy-blob)och [ta bort BLOB](/rest/api/storageservices/delete-blob).

Exempel på användnings scenarier för Arkiv åtkomst nivån är:

- Långsiktig säkerhetskopiering, sekundär säkerhetskopiering och datauppsättningar för arkivering
- Ursprungliga data (RAW) som måste bevaras, även efter att de har bearbetats till slutligt användbart formulär
- Efterlevnads-och arkiverings data som behöver lagras under en längre tid och som inte har kunnat nås

> [!NOTE]
> Arkiv nivån stöds inte för ZRS-, GZRS-eller RA-GZRS-konton. Migrering från LRS till GRS stöds inte om lagrings kontot innehåller blobar på Arkiv nivån.

## <a name="account-level-tiering"></a>Skiktning på konto nivå

Blobbar i alla tre åtkomst nivåer kan finnas i samma konto. Alla blobar som inte har en uttryckligen tilldelad nivå härleder nivån från kontots åtkomst nivå inställning. Om åtkomst nivån kommer från kontot ser du att egenskapen **åtkomst nivå härledd** BLOB har angetts till true och att **åtkomst nivåns** BLOB-egenskap matchar konto nivån. I Azure Portal visas egenskapen _härledd åtkomst nivå_ med BLOB-åtkomst nivån som **aktiv (härledd)** eller låg frekvent **(härledd)**.

Att ändra kontots åtkomst nivå gäller för alla _härledda objekt i åtkomst nivån_ som lagras i kontot som inte har någon explicit nivå uppsättning. Om du växlar konto nivån från frekvent till låg frekvent kommer du att debiteras för Skriv åtgärder (per 10 000) för alla blobbar utan en uppsättnings nivå i GPv2-konton. Det kostar inget att ändra i Blob Storage-konton. Du debiteras för både Läs åtgärder (per 10 000) och data hämtning (per GB) om du växlar från låg frekvent till frekvent i Blob Storage-eller GPv2-konton.

Endast frekventa och frekventa åtkomst nivåer kan anges som standard åtkomst nivå för kontot. Arkivlagringsnivån kan endast anges på objektnivå. Vid BLOB-uppladdning kan du ange den åtkomst nivå som du vill använda som frekvent, låg frekvent eller Arkiv lag ring oavsett standard konto nivån. Med den här funktionen kan du skriva data direkt till Arkiv nivån för att realisera kostnads besparingar från det ögonblick då du skapar data i Blob Storage.

## <a name="blob-level-tiering"></a>Blobnivåindelning

Med skiktning på blobnivå kan du ladda upp data till den åtkomst nivå som du väljer med hjälp av åtgärden [Lägg till BLOB](/rest/api/storageservices/put-blob) eller Lägg till [blockera-lista](/rest/api/storageservices/put-block-list) och ändra nivån för dina data på objekt nivå med hjälp av funktionen [Ange BLOB-nivå](/rest/api/storageservices/set-blob-tier) eller [livs cykel hantering](#blob-lifecycle-management) . Du kan ladda upp data till den nödvändiga åtkomst nivån och sedan enkelt ändra BLOB-åtkomsten mellan nivåerna frekvent, låg frekvent eller Arkiv form när användnings mönster ändras, utan att behöva flytta data mellan konton. Alla ändrings begär Anden sker omedelbart och nivå förändringar mellan frekvent och låg frekvent omedelbar omedelbar inaktive ras. Det kan ta flera timmar att återuppväcks en BLOB från Arkiv lag rings nivån.

Tiden för den senaste ändringen på blobnivån är tillgänglig via blobegenskapen **Ändringstid för åtkomstnivå**. När en BLOB skrivs över på frekvent eller låg frekvent nivå ärver den nya bloben den nivå i blobben som skrevs av, såvida inte den nya BLOB-åtkomst nivån uttryckligen anges när den skapas. Om en BLOB finns på Arkiv nivån kan den inte skrivas över, så det är inte tillåtet att ladda upp samma BLOB i det här scenariot.

> [!NOTE]
> Arkivlagring och blobnivåindelning stöder endast blockblobar.

### <a name="blob-lifecycle-management"></a>Hantering av BLOB-livscykel

Med livs cykel hantering för Blob Storage får du en omfattande, regelbaserade princip som du kan använda för att överföra data till bästa åtkomst nivå och för att förfalla data i slutet av livs cykeln. Se [optimera kostnader genom att automatisera Azure-Blob Storage åtkomst nivåer](storage-lifecycle-management-concepts.md) för mer information.

> [!NOTE]
> Data som lagras i ett Block Blob Storage-konto (Premium prestanda) kan för närvarande inte på nivå till frekvent, låg frekvent eller arkiveras med hjälp av [Ange BLOB-nivå](/rest/api/storageservices/set-blob-tier) eller använda Azure Blob Storage livs cykel hantering.
> Om du vill flytta data måste du synkront kopiera blobar från Block Blob Storage-kontot till frekvent åtkomst nivå i ett annat konto med hjälp av [undantag från URL-API](/rest/api/storageservices/put-block-from-url) eller en version av AzCopy som stöder detta API.
> **Placera block från URL** -API: n kopierar synkront data på servern, vilket innebär att anropet slutförs endast när alla data har flyttats från den ursprungliga Server platsen till mål platsen.

### <a name="blob-level-tiering-billing"></a>Fakturering för blobnivåindelning

När en BLOB överförs eller flyttas mellan nivåer debiteras den enligt motsvarande hastighet omedelbart vid överföring eller nivå ändring.

När en BLOB flyttas till en låg frekvent nivå (frekvent >låg frekvent lagring, >arkiv lag ring eller cool->Arkiv) faktureras åtgärden som en Skriv åtgärd till mål nivån, där avgifter för Skriv åtgärden (per 10 000) och data skrivning (per GB) för mål nivån tillämpas.

När en BLOB flyttas till en varm nivå (Arkiv->låg frekvent, Arkiv->frekvent eller låg frekvent >frekvent) debiteras åtgärden som en läsning från käll nivån, där avgifterna Läs åtgärd (per 10 000) och data hämtning (per GB) för käll nivån tillämpas. Avgifter för [tidig borttagning](#cool-and-archive-early-deletion) för alla blobar som flyttas från låg frekvent lagring eller Arkiv lag rings nivå kan också gälla. [Återuppväcks data från Arkiv](storage-blob-rehydration.md) lag rings nivån tar tid och data debiteras arkiverade priser tills data återställs online och blob-nivån ändras till frekvent eller låg frekvent.

I följande tabell sammanfattas hur nivå ändringar faktureras.

| | **Skriv avgifter (åtgärd + åtkomst)** | **Läs avgifter (åtgärd + åtkomst)** |
| ---- | ----- | ----- |
| **Ange blobnivå** | varm-> sval<br> snabb – > Arkiv<br> kall > Arkiv | arkivera > cool<br> Arkiv-> het<br> hett > frekvent

### <a name="cool-and-archive-early-deletion"></a>Tidig borttagning i lågfrekvent lagring och i arkivlagring

Alla blobar som flyttas till den låg frekventa nivån (endast GPv2-konton) omfattas av en låg tidig borttagnings period på 30 dagar. En blob som flyttas till Arkiv nivån omfattas av en tidig borttagnings period på 180 dagar. Den här kostnaden beräknas proportionellt. Om en blob till exempel flyttas till arkivet och sedan tas bort eller flyttas till den frekventa nivån efter 45 dagar, kommer du att debiteras en avgift för tidig borttagning som motsvarar 135 (180 minus 45) dagar för att lagra bloben i arkivet.

Det finns viss information när du flyttar mellan låg frekvent nivå och Arkiv lag ring:

1. Om en BLOB härleds som låg frekvent baserat på lagrings kontots standard åtkomst nivå och blobben flyttas till arkivet, finns det ingen avgift för tidig borttagning.
1. Om en BLOB uttryckligen flyttas till den låg frekventa nivån och sedan flyttas till arkivet, gäller avgiften för tidig borttagning.

Beräkna tiden för tidig borttagning genom att använda den **senast ändrade** BLOB-egenskapen, om det inte har gjorts några ändringar i åtkomst nivån. Annars kan du använda när åtkomst nivån senast ändrades till låg frekvent eller arkivera genom att Visa BLOB-egenskapen: **åtkomst nivå – ändrings tid**. Mer information om BLOB-egenskaper finns i [Hämta BLOB-egenskaper](/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Jämför lagrings alternativ för Block Blob

I följande tabell visas en jämförelse av Premium Performance Block Blob Storage och åtkomst nivåerna frekvent, låg frekvent och Arkiv lag ring.

|                                           | **Förstklassig prestanda**   | **Frekvent nivå** | **Låg frekvent nivå**       | **Arkiv lag ring**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Tillgänglighet**                          | 99,9 %                     | 99,9 %        | 99 %                 | Offline           |
| **Tillgänglighet** <br> **(RA-GRS-läsningar)**  | Ej tillämpligt                       | 99,99 %       | 99,9 %               | Offline           |
| **Användnings kostnader**                         | Högre kostnader för lagring, lägre åtkomst och kostnad för transaktioner | Högre kostnader för lagring, lägre åtkomst och transaktionskostnader | Lägre kostnader för lagring, högre åtkomst och transaktionskostnader | Lägsta kostnader för lagring, högsta åtkomst och transaktionskostnader |
| **Minsta lagringstid**              | Saknas                       | Saknas          | 30 dagar<sup>1</sup> | 180 dagar
| **Svarstid** <br> **(Tid till första byte)** | Ensiffriga millisekunder | millisekunder | millisekunder        | timmar<sup>2</sup> |

<sup>1</sup> objekt på den lägsta nivån i GPv2-konton har en minsta Retentions tid på 30 dagar. Blob Storage-konton har inte minsta Retentions tid för den låg frekventa nivån.

<sup>2</sup> arkivlagring för närvarande har stöd för två ÅTERUPPVÄCKNING-prioriteter, höga och standard, som erbjuder olika tids fördröjningar och kostnader. Mer information finns i [rehydratisera BLOB-data från Arkiv](storage-blob-rehydration.md)lag rings nivån.

> [!NOTE]
> Blob Storage-konton har stöd för samma prestanda och skalbarhets mål som lagrings konton för generell användning v2. Mer information finns i [skalbarhets-och prestanda mål för Blob Storage](scalability-targets.md).

## <a name="pricing-and-billing"></a>Priser och fakturering

Alla lagrings konton använder en pris modell för Block-Blob-lagring baserat på nivån för varje blob. Tänk på följande saker om fakturering:

- **Lagringskostnader**: Utöver mängden data som lagras varierar lagringskostnaden beroende på åtkomstnivå. Kostnaden per gigabyte minskas när nivån blir mer lågfrekvent.
- **Kostnader för dataåtkomst**: Kostnaderna för dataåtkomst ökar när nivån blir mer lågfrekvent. För data i låg frekvent och Arkiv lag rings nivå debiteras du en åtkomst avgift per Gigabyte för läsningar.
- **Transaktionskostnader**: det finns en avgift per transaktion för alla nivåer som ökar när nivån blir kylare.
- **Kostnader för data överföring med geo-replikering**: den här avgiften gäller endast konton med geo-replikering konfigurerad, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.
- **Kostnader för utgående dataöverföring**: Utgående dataöverföringar (data som överförs utanför en Azure-region) debiteras för bandbreddsanvändning per gigabyte, på samma sätt som för allmänna lagringskonton.
- **Ändring av åtkomst nivån**: om du ändrar kontots åtkomst nivå ändras avgifterna för alla blobar som inte har någon explicit nivå uppsättning. Information om hur du ändrar åtkomst nivå för en enskild BLOB finns i [fakturering på BLOB-nivå](#blob-level-tiering-billing).

    Om du ändrar åtkomst nivån för en BLOB när versions hantering har Aktiver ATS, eller om bloben har ögonblicks bilder, kan ytterligare kostnader uppstå. Information om blobbar med aktiverade versioner finns i [priser och fakturering](versioning-overview.md#pricing-and-billing) i dokumentationen för BLOB-versioner. Information om blobbar med ögonblicks bilder finns i [priser och fakturering](snapshots-overview.md#pricing-and-billing) i dokumentationen för BLOB-ögonblicksbilder.

> [!NOTE]
> Mer information om priser för block-blobar finns i [blocking BLOB-prissättning](https://azure.microsoft.com/pricing/details/storage/blobs/). Mer information om avgifter för utgående data överföring finns på sidan med [pris information om bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/) .

## <a name="availability"></a>Tillgänglighet

Olika åtkomst nivåer, tillsammans med skiktning på blobnivå, är tillgängliga i utvalda regioner. En fullständig lista finns under [Tillgängliga Azure-produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du hanterar blobbar och konton över åtkomst nivåer.

- [Hantera nivån för en BLOB i ett Azure Storage konto](manage-access-tier.md)
- [Hantera standard konto åtkomst nivån för ett Azure Storage-konto](../common/manage-account-default-access-tier.md)
- [Optimera kostnader genom att automatisera Azure-Blob Storage åtkomst nivåer](storage-lifecycle-management-concepts.md)
