---
title: Dataredundans
titleSuffix: Azure Storage
description: Förstå dataredundans i Azure Storage. Data i ditt Microsoft Azure Storage-konto replikeras för hållbarhet och hög tillgänglighet.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d01e125ef3e905191d76c9f4888e790958211dff
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607328"
---
# <a name="azure-storage-redundancy"></a>Redundans i Azure Storage

Azure Storage lagrar alltid flera kopior av dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskin varu haverier, nätverks-eller strömavbrott, och massiv natur katastrofer. Redundans garanterar att ditt lagrings konto uppfyller sina tillgänglighets-och hållbarhets mål även om de inte är i rätt tid.

När du bestämmer vilket alternativ för redundans som passar bäst för ditt scenario bör du fundera över kompromisserna mellan lägre kostnader och högre tillgänglighet. De faktorer som hjälper dig att avgöra vilket alternativ för redundans som du bör välja bland:  

- Hur dina data replikeras i den primära regionen
- Om dina data replikeras till en andra region som är geografiskt avlägsen till den primära regionen, för att skydda mot regionala haverier
- Huruvida programmet kräver Läs behörighet till replikerade data i den sekundära regionen om den primära regionen blir otillgänglig av någon anledning

## <a name="redundancy-in-the-primary-region"></a>Redundans i den primära regionen

Data i ett Azure Storage-konto replikeras alltid tre gånger i den primära regionen. Azure Storage innehåller två alternativ för hur dina data replikeras i den primära regionen:

- **Lokalt Redundant lagring (LRS)** kopierar dina data synkront tre gånger inom en enda fysisk plats i den primära regionen. LRS är det minst dyra replikeringsalternativ, men det rekommenderas inte för program som kräver hög tillgänglighet.
- **Zone-redundant lagring (ZRS)** kopierar dina data synkront över tre tillgänglighets zoner i Azure i den primära regionen. För program som kräver hög tillgänglighet rekommenderar Microsoft att du använder ZRS i den primära regionen och även replikerar till en sekundär region.

> [!NOTE]
> Microsoft rekommenderar att du använder ZRS i den primära regionen för Azure Data Lake Storage Gen2 arbets belastningar.

### <a name="locally-redundant-storage"></a>Lokalt redundant lagring

Lokalt redundant lagring (LRS) replikerar dina data tre gånger inom ett enda data Center i den primära regionen. LRS tillhandahåller minst 99,999999999% (11 nio) objekts hållbarhet under ett angivet år.

LRS är det lägsta alternativet för redundans och ger minsta möjliga hållbarhet jämfört med andra alternativ. LRS skyddar dina data mot Server rack och enhets problem. Men om en katastrof, till exempel Fire eller översvämning sker i data centret, kan alla repliker av ett lagrings konto med LRS gå förlorade eller oåterkalleligt. För att minska den här risken rekommenderar Microsoft att använda [zon-redundant lagring](#zone-redundant-storage) (ZRS), [Geo-redundant lagring](#geo-redundant-storage) (GRS) eller [geo-Zone-redundant lagring](#geo-zone-redundant-storage) (GZRS).

En Skriv förfrågan till ett lagrings konto som använder LRS sker synkront. Skriv åtgärden returneras bara efter att data har skrivits till alla tre repliker.

Följande diagram visar hur dina data replikeras i ett enda data Center med LRS:

:::image type="content" source="media/storage-redundancy/locally-redundant-storage.png" alt-text="Diagram över hur data replikeras i ett enda data Center med LRS":::

LRS är ett bra val för följande scenarier:

- Om ditt program lagrar data som enkelt kan rekonstrueras om data går förlorade, kan du välja LRS.
- Om ditt program är begränsat till att bara replikera data inom ett land eller en region på grund av data styrnings krav, kan du välja LRS. I vissa fall kan de kopplade regionerna som data är geo-replikerade till finnas i ett annat land eller en annan region. Mer information om kopplade regioner finns i Azure- [regioner](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Zonredundant lagring

Zone-redundant lagring (ZRS) replikerar dina Azure Storage data synkront över tre tillgänglighets zoner i Azure i den primära regionen. Varje tillgänglighetszon är en separat fysisk plats med fristående strömförsörjning, nedkylning och nätverk. ZRS erbjuder hållbarhet för Azure Storage data objekt på minst 99,9999999999% (12 9) under ett angivet år.

Med ZRS är dina data fortfarande tillgängliga för både Läs-och skriv åtgärder även om en zon blir otillgänglig. Om en zon blir otillgänglig gör Azure nätverks uppdateringar, till exempel DNS-ompekare. De här uppdateringarna kan påverka ditt program om du har åtkomst till data innan uppdateringarna har slutförts. När du skapar program för ZRS följer du praxis för hantering av tillfälliga fel, inklusive implementering av principer för återförsök med exponentiell säkerhets kopiering.

En Skriv förfrågan till ett lagrings konto som använder ZRS sker synkront. Skriv åtgärden returneras bara efter att data har skrivits till alla repliker i de tre tillgänglighets zonerna.

Microsoft rekommenderar att du använder ZRS i den primära regionen för scenarier som kräver konsekvens, hållbarhet och hög tillgänglighet. ZRS rekommenderas också för att begränsa replikeringen av data till inom ett land eller en region för att uppfylla data styrnings kraven.

Följande diagram visar hur dina data replikeras mellan tillgänglighets zoner i den primära regionen med ZRS:

:::image type="content" source="media/storage-redundancy/zone-redundant-storage.png" alt-text="Diagram över hur data replikeras i den primära regionen med ZRS":::

ZRS tillhandahåller utmärkta prestanda, låg latens och återhämtning för dina data om de blir tillfälligt otillgängliga. Men ZRS i sig kanske inte skyddar dina data mot en regional katastrof där flera zoner ständigt påverkas. För skydd mot regionala katastrofer rekommenderar Microsoft att använda [geo-Zone-redundant lagring](#geo-zone-redundant-storage) (GZRS), som använder ZRS i den primära regionen och även geo-replikerar dina data till en sekundär region.

Följande tabell visar vilka typer av lagrings konton som stöder ZRS i vilka regioner:

| Typ av lagringskonto | Regioner som stöds | Tjänster som stöds |
|--|--|--|
| Allmänt-syfte v2<sup>1</sup> | Centralafrika Sydafrika, norra<br /> (Asien och stillahavsområdet) Asien, östra<br /> (Asien och stillahavsområdet) Sydostasien<br /> (Asien och stillahavsområdet) Östra Australien<br /> (Asien och stillahavsområdet) Centrala Indien<br /> (Asien och stillahavsområdet) Japan, östra<br /> (Asien och stillahavsområdet) Korea, centrala<br /> Kanadas Centrala Kanada<br /> Östeuropa Nord Europa<br /> Östeuropa Västeuropa<br /> Östeuropa Centrala Frankrike<br /> Östeuropa Tyskland, västra centrala<br /> Östeuropa Östra Norge<br /> Östeuropa Schweiz, norra<br /> Östeuropa Storbritannien, södra<br /> (Mellanöstern) Förenade Arabemiraten, norra<br /> (Sydamerika) Södra Brasilien<br /> USA USA, centrala<br /> (USA) USA, östra<br /> USA USA, östra 2<br /> USA Norra centrala USA<br />USA Södra centrala USA<br /> USA Västra USA<br /> USA USA, västra 2 | Blockblobar<br /> Page blobbar<sup>2</sup><br /> Fil resurser (standard)<br /> Tables<br /> Köer<br /> |
| BlockBlobStorage<sup>1</sup> | Centralafrika Sydafrika, norra<br /> (Asien och stillahavsområdet) Asien, östra<br /> (Asien och stillahavsområdet) Sydostasien<br /> (Asien och stillahavsområdet) Östra Australien<br /> (Asien och stillahavsområdet) Centrala Indien<br /> (Asien och stillahavsområdet) Japan, östra<br /> (Asien och stillahavsområdet) Korea, centrala<br /> Kanadas Centrala Kanada<br /> Östeuropa Nord Europa<br /> Östeuropa Västeuropa<br /> Östeuropa Centrala Frankrike<br /> Östeuropa Tyskland, västra centrala<br /> Östeuropa Östra Norge<br /> Östeuropa Schweiz, norra<br /> Östeuropa Storbritannien, södra<br /> (Mellanöstern) Förenade Arabemiraten, norra<br /> (Sydamerika) Södra Brasilien<br /> USA USA, centrala<br /> (USA) USA, östra<br /> USA USA, östra 2<br /> USA Norra centrala USA<br />USA Södra centrala USA<br /> USA Västra USA<br /> USA USA, västra 2 | Endast Premium block-blobbar |
| FileStorage | Centralafrika Sydafrika, norra<br /> (Asien och stillahavsområdet) Asien, östra<br /> (Asien och stillahavsområdet) Sydostasien<br /> (Asien och stillahavsområdet) Östra Australien<br /> (Asien och stillahavsområdet) Centrala Indien<br /> (Asien och stillahavsområdet) Japan, östra<br /> (Asien och stillahavsområdet) Korea, centrala<br /> Kanadas Centrala Kanada<br /> Östeuropa Nord Europa<br /> Östeuropa Västeuropa<br /> Östeuropa Centrala Frankrike<br /> Östeuropa Tyskland, västra centrala<br /> Östeuropa Östra Norge<br /> Östeuropa Schweiz, norra<br /> Östeuropa Storbritannien, södra<br /> (Mellanöstern) Förenade Arabemiraten, norra<br /> (Sydamerika) Södra Brasilien<br /> USA USA, centrala<br /> (USA) USA, östra<br /> USA USA, östra 2<br /> USA Norra centrala USA<br />USA Södra centrala USA<br /> USA Västra USA<br /> USA USA, västra 2 | Premium-filer endast resurser |

<sup>1</sup> Arkiv nivån stöds för närvarande inte för ZRS-konton.<br />
<sup>2</sup> lagrings konton som innehåller Azure Managed disks för virtuella datorer använder alltid LRS. Azure-ohanterade diskar bör också använda LRS. Det går att skapa ett lagrings konto för Azure unmanaged disks som använder GRS, men det rekommenderas inte på grund av potentiella problem med konsekvens över asynkron geo-replikering. Varken hanterade eller ohanterade diskar har stöd för ZRS eller GZRS. Mer information om hanterade diskar finns i [prissättning för Azure Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

Information om vilka regioner som stöder ZRS finns i **tjänster support efter region**  i [Vad är Azure-tillgänglighetszoner?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Redundans i en sekundär region

För program som kräver hög tillgänglighet kan du också välja att kopiera data i ditt lagrings konto till en sekundär region som är hundratals mil bort från den primära regionen. Om ditt lagrings konto kopieras till en sekundär region, är dina data varaktiga, även om det är ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte går att återvinna.

När du skapar ett lagrings konto väljer du den primära regionen för kontot. Den kopplade sekundära regionen bestäms utifrån den primära regionen och kan inte ändras. Mer information om regioner som stöds av Azure finns i [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).

Azure Storage erbjuder två alternativ för att kopiera data till en sekundär region:

- **Geo-redundant lagring (GRS)** kopierar dina data synkront tre gånger inom en enda fysisk plats i den primära regionen med hjälp av LRS. Därefter kopieras dina data asynkront till en enda fysisk plats i den sekundära regionen. I den sekundära regionen kopieras dina data synkront tre gånger med hjälp av LRS.
- **Geo-Zone-redundant lagring (GZRS)** kopierar dina data synkront över tre tillgänglighets zoner i Azure i den primära regionen med hjälp av ZRS. Därefter kopieras dina data asynkront till en enda fysisk plats i den sekundära regionen. I den sekundära regionen kopieras dina data synkront tre gånger med hjälp av LRS.

> [!NOTE]
> Den främsta skillnaden mellan GRS och GZRS är hur data replikeras i den primära regionen. I den sekundära regionen replikeras alltid data synkront under tre gånger med hjälp av LRS. LRS i den sekundära regionen skyddar dina data mot maskin varu problem.

Med GRS eller GZRS är data i den sekundära regionen inte tillgängliga för Läs-eller skriv åtkomst, såvida det inte finns någon redundansväxling till den sekundära regionen. För Läs behörighet till den sekundära regionen konfigurerar du ditt lagrings konto så att det använder Read-Access Geo-redundant lagring (RA-GRS) eller Läs åtkomst till geo-Zone-redundant lagring (RA-GZRS). Mer information finns i [Läs åtkomst till data i den sekundära regionen](#read-access-to-data-in-the-secondary-region).

Om den primära regionen blir otillgänglig kan du välja att redundansväxla till den sekundära regionen. När redundansväxlingen har slutförts blir den sekundära regionen den primära regionen och du kan läsa och skriva data igen. Mer information om haveri beredskap och om hur du växlar över till den sekundära regionen finns i [haveri beredskap och redundans för lagrings konton](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Eftersom data replikeras till den sekundära regionen asynkront kan ett haveri som påverkar den primära regionen leda till data förlust om den primära regionen inte kan återställas. Intervallet mellan de senaste skrivningarna till den primära regionen och den senaste skrivningen till den sekundära regionen kallas för återställnings punkt mål. Återställnings punkten anger tidpunkten då data kan återställas. Azure Storage vanligt vis har en återställning på mindre än 15 minuter, även om det inte finns något service avtal för hur lång tid det tar att replikera data till den sekundära regionen.

### <a name="geo-redundant-storage"></a>Geo-redundant lagring

Geo-redundant lagring (GRS) kopierar dina data synkront tre gånger inom en enda fysisk plats i den primära regionen med hjälp av LRS. Den kopierar sedan dina data asynkront till en enda fysisk plats i en sekundär region som är hundratals mil bort från den primära regionen. GRS erbjuder hållbarhet för Azure Storage data objekt på minst 99.99999999999999% (16 9) under ett angivet år.

En Skriv åtgärd allokeras först till den primära platsen och replikeras med hjälp av LRS. Uppdateringen replikeras sedan asynkront till den sekundära regionen. När data skrivs till den sekundära platsen replikeras de också inom den platsen med hjälp av LRS.

Följande diagram visar hur dina data replikeras med GRS eller RA-GRS:

:::image type="content" source="media/storage-redundancy/geo-redundant-storage.png" alt-text="Diagram över hur data replikeras med GRS eller RA-GRS":::

### <a name="geo-zone-redundant-storage"></a>Geografiskt zonredundant lagring

Geo-Zone-redundant lagring (GZRS) kombinerar den höga tillgänglighet som tillhandahålls av redundans över tillgänglighets zoner med skydd från regionala avbrott som tillhandahålls av geo-replikering. Data i ett GZRS lagrings konto kopieras över tre [tillgänglighets zoner i Azure](../../availability-zones/az-overview.md) i den primära regionen och replikeras också till en sekundär geografisk region för skydd mot regionala haverier. Microsoft rekommenderar att du använder GZRS för program som kräver maximal konsekvens, hållbarhet och tillgänglighet, utmärkt prestanda och återhämtning för haveri beredskap.

Med ett GZRS lagrings konto kan du fortsätta att läsa och skriva data om en tillgänglighets zon blir otillgänglig eller inte kan återställas. Dessutom är dina data också varaktiga när det gäller ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte kan återskapas. GZRS har utformats för att ge minst 99.99999999999999% (16 9) objekts hållbarhet under ett angivet år.

Följande diagram visar hur dina data replikeras med GZRS eller RA-GZRS:

:::image type="content" source="media/storage-redundancy/geo-zone-redundant-storage.png" alt-text="Diagram över hur data replikeras med GZRS eller RA-GZRS":::

Endast General-Purpose v2-lagrings konton stöder GZRS och RA-GZRS. Mer information om typer av lagringskonton finns i [Översikt över Azure Storage-konton](storage-account-overview.md). GZRS och RA-GZRS stöder block-blobbar, Page blobbar (utom för VHD-diskar), filer, tabeller och köer.

GZRS och RA-GZRS stöds i följande regioner:

- Centralafrika Sydafrika, norra
- (Asien och stillahavsområdet) Asien, östra
- (Asien och stillahavsområdet) Sydostasien
- (Asien och stillahavsområdet) Östra Australien
- (Asien och stillahavsområdet) Centrala Indien
- (Asien och stillahavsområdet) Japan, östra
- (Asien och stillahavsområdet) Korea, centrala
- Kanadas Centrala Kanada
- Östeuropa Nord Europa
- Östeuropa Västeuropa
- Östeuropa Centrala Frankrike
- Östeuropa Tyskland, västra centrala
- Östeuropa Östra Norge
- Östeuropa Schweiz, norra
- Östeuropa Storbritannien, södra
- (Mellanöstern) Förenade Arabemiraten, norra
- (Sydamerika) Södra Brasilien
- USA USA, centrala
- (USA) USA, östra
- USA USA, östra 2
- USA Norra centrala USA
- USA Södra centrala USA
- USA Västra USA
- USA USA, västra 2

Information om priser finns i pris information för [blobbar](https://azure.microsoft.com/pricing/details/storage/blobs), [filer](https://azure.microsoft.com/pricing/details/storage/files/), [köer](https://azure.microsoft.com/pricing/details/storage/queues/)och [tabeller](https://azure.microsoft.com/pricing/details/storage/tables/).

## <a name="read-access-to-data-in-the-secondary-region"></a>Läs åtkomst till data i den sekundära regionen

Geo-redundant lagring (med GRS eller GZRS) replikerar dina data till en annan fysisk plats i den sekundära regionen för att skydda mot regionala avbrott. Men dessa data är tillgängliga för läsning endast om kunden eller Microsoft initierar en redundansväxling från den primära till den sekundära regionen. När du aktiverar Läs åtkomst till den sekundära regionen, är dina data tillgängliga för läsning hela tiden, inklusive i en situation där den primära regionen blir otillgänglig. Om du vill ha Läs behörighet till den sekundära regionen ska du aktivera Geo-redundant lagring med Läs behörighet (RA-GRS) eller Läs åtkomst till geo-Zone-redundant lagring (RA-GZRS).

> [!NOTE]
> Azure Files har inte stöd för Geo-redundant lagring med Läs åtkomst (RA-GRS) och Read-Access geo-Zone-redundant lagring (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Utforma dina program för Läs behörighet till den sekundära

Om ditt lagrings konto har kon figurer ATS för Läs åtkomst till den sekundära regionen kan du utforma dina program för att sömlöst växla till att läsa data från den sekundära regionen, om den primära regionen inte är tillgänglig av någon anledning. 

Den sekundära regionen är tillgänglig för Läs behörighet när du har aktiverat RA-GRS eller RA-GZRS, så att du kan testa programmet i förväg för att se till att det kommer att läsas från den sekundära datorn i händelse av ett avbrott. Mer information om hur du utformar dina program för hög tillgänglighet finns i [använda GEO-redundans för att skapa program med hög](geo-redundant-design.md)tillgänglighet.

När Läs åtkomst till den sekundära är aktive rad kan programmet läsa från den sekundära slut punkten samt från den primära slut punkten. Den sekundära slut punkten lägger till suffixet *– sekundärt* till konto namnet. Om din primära slut punkt för Blob Storage t. ex `myaccount.blob.core.windows.net` . är, är den sekundära slut punkten `myaccount-secondary.blob.core.windows.net` . Konto åtkomst nycklarna för ditt lagrings konto är desamma för både den primära och den sekundära slut punkten.

### <a name="check-the-last-sync-time-property"></a>Kontrollera egenskapen Tidpunkt för senaste synkronisering

Eftersom data replikeras till den sekundära regionen asynkront är den sekundära regionen ofta bakom den primära regionen. Om ett haveri inträffar i den primära regionen är det troligt att alla skrivningar till den primära inte ännu har repliker ATS till den sekundära.

För att avgöra vilka Skriv åtgärder som har repliker ATS till den sekundära regionen, kan programmet kontrol lera den **senaste synkroniseringstid** -egenskapen för ditt lagrings konto. Alla Skriv åtgärder som skrivs till den primära regionen före den senaste synkroniseringstid-tiden har repliker ATS till den sekundära regionen, vilket innebär att de är tillgängliga för läsning från den sekundära. Eventuella Skriv åtgärder som skrivs till den primära regionen efter den senaste synkroniseringen kanske inte har repliker ATS till den sekundära regionen, vilket innebär att de inte är tillgängliga för Läs åtgärder.

Du kan fråga värdet för den **senaste synkroniseringstid** -egenskapen med Azure PowerShell, Azure CLI eller något av Azure Storage-klient biblioteken. Den **senaste synkroniseringstid** -egenskapen är ett GMT-datum/tid-värde. Mer information finns i [kontrol lera den senaste synkroniseringstid-egenskapen för ett lagrings konto](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Sammanfattning av alternativ för redundans

Tabellerna i följande avsnitt sammanfattar de alternativ för redundans som är tillgängliga för Azure Storage

### <a name="durability-and-availability-parameters"></a>Parametrar för hållbarhet och tillgänglighet

I följande tabell beskrivs viktiga parametrar för varje alternativ för redundans:

| Parameter | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Procentuellt hållbarhet för objekt under ett angivet år | minst 99,999999999% (11 9) | minst 99,9999999999% (12 9-) | minst 99.99999999999999% (16 9) | minst 99.99999999999999% (16 9) |
| Tillgänglighet för Läs begär Anden | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) för GRS<br /><br />Minst 99,99% (99,9% för låg frekvent åtkomst nivå) för RA-GRS | Minst 99,9% (99% för låg frekvent åtkomst nivå) för GZRS<br /><br />Minst 99,99% (99,9% för låg frekvent åtkomst nivå) för RA-GZRS |
| Tillgänglighet för Skriv förfrågningar | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) | Minst 99,9% (99% för låg frekvent åtkomst nivå) |
| Antal kopior av data som underhålls på separata noder | Tre kopior inom en enda region | Tre kopior i olika tillgänglighets zoner inom en enda region | Sex kopior totalt, inklusive tre i den primära regionen och tre i den sekundära regionen | Sex kopior totalt, inklusive tre olika tillgänglighets zoner i den primära regionen och tre lokalt redundanta kopior i den sekundära regionen |

### <a name="durability-and-availability-by-outage-scenario"></a>Scenario för hållbarhet och tillgänglighet efter avbrott

Följande tabell visar om dina data är beständiga och tillgängliga i ett specifikt scenario, beroende på vilken typ av redundans som gäller för ditt lagrings konto:

| Avbrott-scenario | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| En nod i ett Data Center blir otillgänglig | Ja | Ja | Ja | Ja |
| Ett helt data Center (zonindelade eller icke-zonindelade) blir otillgängligt | Inga | Ja | Ja<sup>1</sup> | Yes |
| Ett områdes omfattande avbrott uppstår i den primära regionen | Inga | Inga | Ja<sup>1</sup> | Ja<sup>1</sup> |
| Läs behörighet till den sekundära regionen är tillgängligt om den primära regionen blir otillgänglig | Inga | Inga | Ja (med RA-GRS) | Ja (med RA-GZRS) |

<sup>1</sup> växling vid fel krävs för att återställa Skriv tillgängligheten om den primära regionen blir otillgänglig. Mer information finns i [haveri beredskap och redundans för lagrings konton](storage-disaster-recovery-guidance.md).

### <a name="supported-azure-storage-services"></a>Azure Storage tjänster som stöds

I följande tabell visas vilka alternativ för redundans som stöds av varje Azure Storage-tjänst.

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br />Azure Managed disks | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br /> | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br /> |

### <a name="supported-storage-account-types"></a>Typer av lagrings konton som stöds

Följande tabell visar vilka alternativ för redundans som stöds av varje typ av lagrings konto. Information om lagrings konto typer finns i [Översikt över lagrings konto](storage-account-overview.md).

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| General-purpose v2<br /> General-purpose v1<br /> BlockBlobStorage<br /> BlobStorage<br /> FileStorage | General-purpose v2<br /> BlockBlobStorage<br /> FileStorage | General-purpose v2<br /> General-purpose v1<br /> BlobStorage | General-purpose v2 |

Alla data för alla lagrings konton kopieras enligt alternativen för redundans för lagrings kontot. Objekt som innehåller block-blobbar, tillägg av blobbar, sid blobbar, köer, tabeller och filer kopieras. Data på alla nivåer, inklusive Arkiv nivån, kopieras. Mer information om BLOB-nivåer finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](../blobs/storage-blob-storage-tiers.md)lag rings nivåer.

Pris information för varje alternativ för redundans finns i [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> Azure Premium-Disklagring har för närvarande endast stöd för lokalt redundant lagring (LRS). Blockera Blob Storage-konton har stöd för lokalt redundant lagring (LRS) och zon redundant lagring (ZRS) i vissa regioner.

## <a name="data-integrity"></a>Dataintegritet

Azure Storage verifierar regelbundet integriteten för data som lagras med hjälp av cyklisk redundans kontroll (CRCs). Om skadade data upptäcks, repare ras den med hjälp av redundanta data. Azure Storage beräknar också kontroll summor på all nätverks trafik för att identifiera skadade data paket när data lagras eller hämtas.

## <a name="see-also"></a>Se även

- [Kontrol lera den senaste synkroniseringstid-egenskapen för ett lagrings konto](last-sync-time-get.md)
- [Ändra redundans alternativ för ett lagrings konto](redundancy-migration.md)
- [Använd GEO-redundans för att skapa program med hög tillgänglighet](geo-redundant-design.md)
- [Haveriberedskap och lagringskontoredundans](storage-disaster-recovery-guidance.md)
