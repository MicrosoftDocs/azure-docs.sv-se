---
title: Dataredundans
titleSuffix: Azure Storage
description: Förstå dataredundans i Azure Storage. Data i ditt Microsoft Azure Storage-konto replikeras för hållbarhet och hög tillgänglighet.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3665421ddbdd9cf079ff4aab9377fc9164a1599c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575368"
---
# <a name="azure-storage-redundancy"></a>Redundans i Azure Storage

Azure Storage alltid flera kopior av dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskinvarufel, nätverks- eller strömavbrott och enorma naturkatastrofer. Redundans säkerställer att ditt lagringskonto uppfyller sina tillgänglighets- och hållbarhetsmål även vid fel.

När du bestämmer vilket redundansalternativ som passar bäst för ditt scenario bör du överväga kompromisserna mellan lägre kostnader och högre tillgänglighet. Faktorerna som hjälper dig att avgöra vilket redundansalternativ du bör välja är:  

- Hur dina data replikeras i den primära regionen
- Om dina data replikeras till en andra region som är geografiskt avlägsna till den primära regionen, för att skydda mot regionala katastrofer
- Om ditt program kräver läsbehörighet till replikerade data i den sekundära regionen om den primära regionen av någon anledning blir otillgänglig

## <a name="redundancy-in-the-primary-region"></a>Redundans i den primära regionen

Data i ett Azure Storage-konto replikeras alltid tre gånger i den primära regionen. Azure Storage innehåller två alternativ för hur dina data replikeras i den primära regionen:

- **Lokalt redundant lagring (LRS)** kopierar dina data synkront tre gånger inom en enda fysisk plats i den primära regionen. LRS är det billigaste replikeringsalternativet, men rekommenderas inte för program som kräver hög tillgänglighet.
- **Zonredundant lagring (ZRS)** kopierar dina data synkront över tre Azure-tillgänglighetszoner i den primära regionen. För program som kräver hög tillgänglighet rekommenderar Microsoft att du använder ZRS i den primära regionen och även replikerar till en sekundär region.

> [!NOTE]
> Microsoft rekommenderar att du använder ZRS i den primära regionen för Azure Data Lake Storage Gen2 arbetsbelastningar.

### <a name="locally-redundant-storage"></a>Lokalt redundant lagring

Lokalt redundant lagring (LRS) replikerar dina data tre gånger inom ett enda datacenter i den primära regionen. LRS ger minst 99,9999999999 % (11 nior) objekts hållbarhet under ett visst år.

LRS är det redundansalternativ som har lägst kostnad och har minst hållbarhet jämfört med andra alternativ. LRS skyddar dina data mot serverrack och enhetsfel. Men om en katastrof, till exempel brand eller översvämningar inträffar i datacentret, kan alla repliker av ett lagringskonto som använder LRS gå förlorade eller oåterkalleliga. För att minska den här risken rekommenderar Microsoft att du använder [zonredundant](#zone-redundant-storage) lagring (ZRS), [geo-redundant](#geo-redundant-storage) lagring (GRS) eller [geo-zonredundant lagring](#geo-zone-redundant-storage) (GZRS).

En skrivbegäran till ett lagringskonto som använder LRS sker synkront. Skrivåtgärden returneras först när data har skrivits till alla tre replikerna.

Följande diagram visar hur dina data replikeras inom ett enda datacenter med LRS:

:::image type="content" source="media/storage-redundancy/locally-redundant-storage.png" alt-text="Diagram som visar hur data replikeras i ett enda datacenter med LRS":::

LRS är ett bra alternativ för följande scenarier:

- Om ditt program lagrar data som enkelt kan rekonstrueras om dataförlust uppstår kan du välja LRS.
- Om ditt program är begränsat till att endast replikera data inom ett land eller en region på grund av datastyrningskrav kan du välja LRS. I vissa fall kan de parkopplade regionerna där data geo-replikeras finnas i ett annat land eller en annan region. Mer information om parkopplade regioner finns i [Azure-regioner.](https://azure.microsoft.com/regions/)

### <a name="zone-redundant-storage"></a>Zonredundant lagring

Zonredundant lagring (ZRS) replikerar dina Azure Storage data synkront över tre Azure-tillgänglighetszoner i den primära regionen. Varje tillgänglighetszon är en separat fysisk plats med fristående strömförsörjning, nedkylning och nätverk. ZRS ger hållbarhet Azure Storage dataobjekt på minst 99,99999999999 % (12 nia) under ett visst år.

Med ZRS är dina data fortfarande tillgängliga för både läs- och skrivåtgärder även om en zon blir otillgänglig. Om en zon blir otillgänglig utför Azure nätverksuppdateringar, till exempel DNS-åter pekare. De här uppdateringarna kan påverka ditt program om du har åtkomst till data innan uppdateringarna har slutförts. När du utformar program för ZRS bör du följa metoder för hantering av tillfälliga fel, inklusive implementera återförsöksprinciper med exponentiell back off.

En skrivbegäran till ett lagringskonto som använder ZRS sker synkront. Skrivåtgärden returneras först när data har skrivits till alla repliker i de tre tillgänglighetszonerna.

Microsoft rekommenderar att du använder ZRS i den primära regionen för scenarier som kräver konsekvens, hållbarhet och hög tillgänglighet. ZRS rekommenderas också för att begränsa replikering av data till inom ett land eller en region för att uppfylla datastyrningskrav.

Följande diagram visar hur dina data replikeras mellan tillgänglighetszoner i den primära regionen med ZRS:

:::image type="content" source="media/storage-redundancy/zone-redundant-storage.png" alt-text="Diagram som visar hur data replikeras i den primära regionen med ZRS":::

ZRS ger utmärkt prestanda, kort svarstid och återhämtning för dina data om de blir tillfälligt otillgängliga. ZRS kanske dock inte i sig skyddar dina data mot en regional katastrof där flera zoner påverkas permanent. För att skydda mot regionala [](#geo-zone-redundant-storage) katastrofer rekommenderar Microsoft att du använder geo-zonredundant lagring (GZRS), som använder ZRS i den primära regionen och även geo-replikerar dina data till en sekundär region.

I följande tabell visas vilka typer av lagringskonton som stöder ZRS i vilka regioner:

| Typ av lagringskonto | Regioner som stöds | Tjänster som stöds |
|--|--|--|
| Generell användning v2<sup>1</sup> | (Afrika) Sydafrika, norra<br /> (Asien och stillahavsområdet) Sydostasien<br /> (Asien och stillahavsområdet) Australien, östra<br /> (Asien och stillahavsområdet) Japan, östra<br /> (Kanada) Kanada, centrala<br /> (Europa) Europa, norra<br /> (Europa) Europa, västra<br /> (Europa) Frankrike, centrala<br /> (Europa) Tyskland, västra centrala<br /> (Europa) Storbritannien, södra<br /> (Sydamerika) Brasilien, södra<br /> (USA) USA, centrala<br /> (USA) USA, östra<br /> (USA) USA, östra 2<br /> (USA) USA, södra centrala<br /> (USA) USA, västra<br /> (USA) USA, västra 2 | Blockblobar<br /> Sidblobar<sup>2</sup><br /> Filresurser (standard)<br /> Tables<br /> Köer<br /> |
| BlockBlobStorage<sup>1</sup> | Sydostasien<br /> Australien, östra<br /> Nordeuropa<br /> Västeuropa<br /> Frankrike, centrala <br /> Japan, östra<br /> Storbritannien, södra <br /> USA, Östra <br /> USA, östra 2 <br /> USA, västra 2| Endast Premium-blockblobar |
| FileStorage | Sydostasien<br /> Australien, östra<br /> Nordeuropa<br /> Västeuropa<br /> Frankrike, centrala <br /> Japan, östra<br /> Storbritannien, södra <br /> USA, Östra <br /> USA, östra 2 <br /> USA, västra 2 | Endast Premium-filresurser |

<sup>1</sup> Arkivnivån stöds för närvarande inte för ZRS-konton.<br />
<sup>2</sup> Lagringskonton som innehåller Azure-hanterade diskar för virtuella datorer använder alltid LRS. Ohanterade Azure-diskar bör också använda LRS. Det går att skapa ett lagringskonto för ohanterade Azure-diskar som använder GRS, men det rekommenderas inte på grund av potentiella problem med konsekvens över asynkron geo-replikering. Varken hanterade eller ohanterade diskar stöder ZRS eller GZRS. Mer information om hanterade diskar finns i [Prissättning för Azure Managed Disks.](https://azure.microsoft.com/pricing/details/managed-disks/)

Information om vilka regioner som stöder ZRS finns **i Tjänstsupport per region**  i Vad är [Azure-tillgänglighetszoner?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Redundans i en sekundär region

För program som kräver hög tillgänglighet kan du välja att kopiera data i ditt lagringskonto till en sekundär region som ligger hundratals kilometer från den primära regionen. Om ditt lagringskonto kopieras till en sekundär region är dina data beständiga även vid ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte kan återställas.

När du skapar ett lagringskonto väljer du den primära regionen för kontot. Den parkopplade sekundära regionen bestäms baserat på den primära regionen och kan inte ändras. Mer information om regioner som stöds av Azure finns i [Azure-regioner.](https://azure.microsoft.com/global-infrastructure/regions/)

Azure Storage två alternativ för att kopiera data till en sekundär region:

- **Geo-redundant lagring (GRS)** kopierar dina data synkront tre gånger inom en enda fysisk plats i den primära regionen med hjälp av LRS. Därefter kopieras dina data asynkront till en enda fysisk plats i den sekundära regionen. Inom den sekundära regionen kopieras dina data synkront tre gånger med hjälp av LRS.
- **Geo-zonredundant lagring (GZRS)** kopierar dina data synkront över tre Azure-tillgänglighetszoner i den primära regionen med hjälp av ZRS. Därefter kopieras dina data asynkront till en enda fysisk plats i den sekundära regionen. Inom den sekundära regionen kopieras dina data synkront tre gånger med hjälp av LRS.

> [!NOTE]
> Den främsta skillnaden mellan GRS och GZRS är hur data replikeras i den primära regionen. Inom den sekundära regionen replikeras data alltid synkront tre gånger med hjälp av LRS. LRS i den sekundära regionen skyddar dina data mot maskinvarufel.

Med GRS eller GZRS är data i den sekundära regionen inte tillgängliga för läs- eller skrivåtkomst såvida det inte finns en redundans till den sekundära regionen. För läsåtkomst till den sekundära regionen konfigurerar du lagringskontot så att det använder RA-GRS (Read-Access Geo Redundant Storage) eller RA-GZRS (Read-Access Geo-Zone Redundant Storage). Mer information finns i Läs [åtkomst till data i den sekundära regionen](#read-access-to-data-in-the-secondary-region).

Om den primära regionen blir otillgänglig kan du välja att redundans växla över till den sekundära regionen. När redundansen har slutförts blir den sekundära regionen den primära regionen och du kan läsa och skriva data igen. Mer information om haveriberedskap och hur du redundans redundansar till den sekundära regionen finns i [Haveriberedskap och redundans för lagringskonto.](storage-disaster-recovery-guidance.md)

> [!IMPORTANT]
> Eftersom data replikeras till den sekundära regionen asynkront kan ett fel som påverkar den primära regionen resultera i dataförlust om den primära regionen inte kan återställas. Intervallet mellan de senaste skrivningar till den primära regionen och den senaste skrivningen till den sekundära regionen kallas mål för återställningspunkt (RPO). RPO:et anger den tidpunkt till vilken data kan återställas. Azure Storage har vanligtvis ett RPO på mindre än 15 minuter, även om det för närvarande inte finns något serviceavtal för hur lång tid det tar att replikera data till den sekundära regionen.

### <a name="geo-redundant-storage"></a>Geo-redundant lagring

Geo-redundant lagring (GRS) kopierar dina data synkront tre gånger inom en enda fysisk plats i den primära regionen med hjälp av LRS. Sedan kopieras dina data asynkront till en enda fysisk plats i en sekundär region som ligger hundratals kilometer från den primära regionen. GRS ger hållbarhet för Azure Storage-dataobjekt på minst 99,999999999999999 % (16 nior) under ett visst år.

En skrivåtgärd utförs först på den primära platsen och replikeras med hjälp av LRS. Uppdateringen replikeras sedan asynkront till den sekundära regionen. När data skrivs till den sekundära platsen replikeras de också på den platsen med hjälp av LRS.

Följande diagram visar hur dina data replikeras med GRS eller RA-GRS:

:::image type="content" source="media/storage-redundancy/geo-redundant-storage.png" alt-text="Diagram som visar hur data replikeras med GRS eller RA-GRS":::

### <a name="geo-zone-redundant-storage"></a>Geografiskt zonredundant lagring

Geo-zonredundant lagring (GZRS) kombinerar den höga tillgänglighet som tillhandahålls av redundans mellan tillgänglighetszoner med skydd mot regionala avbrott som tillhandahålls av geo-replikering. Data i ett GZRS-lagringskonto [](../../availability-zones/az-overview.md) kopieras över tre Azure-tillgänglighetszoner i den primära regionen och replikeras också till en sekundär geografisk region för skydd mot regionala katastrofer. Microsoft rekommenderar att du använder GZRS för program som kräver maximal konsekvens, hållbarhet och tillgänglighet, utmärkt prestanda och återhämtning vid haveriberedskap.

Med ett GZRS-lagringskonto kan du fortsätta att läsa och skriva data om en tillgänglighetszon blir otillgänglig eller inte kan återställas. Dessutom är dina data beständiga i händelse av ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte kan återställas. GZRS är utformat för att ge minst 99,999999999999999 % (16 nia) objekts hållbarhet under ett visst år.

Följande diagram visar hur dina data replikeras med GZRS eller RA-GZRS:

:::image type="content" source="media/storage-redundancy/geo-zone-redundant-storage.png" alt-text="Diagram som visar hur data replikeras med GZRS eller RA-GZRS":::

Endast allmänna v2-lagringskonton stöder GZRS och RA-GZRS. Mer information om typer av lagringskonton finns i [Översikt över Azure Storage-konton](storage-account-overview.md). GZRS och RA-GZRS stöder blockblobar, sidblobar (förutom VHD-diskar), filer, tabeller och köer.

GZRS och RA-GZRS stöds i följande regioner:

- (Afrika) Sydafrika, norra
- (Asien och stillahavsområdet) Asien, östra
- (Asien och stillahavsområdet) Sydostasien
- (Asien och stillahavsområdet) Australien, östra
- (Asien och stillahavsområdet) Indien, centrala
- (Asien och stillahavsområdet) Japan, östra
- (Asien och stillahavsområdet) Sydkorea, centrala
- (Kanada) Kanada, centrala
- (Europa) Europa, norra
- (Europa) Europa, västra
- (Europa) Frankrike, centrala
- (Europa) Tyskland, västra centrala
- (Europa) Mellanöstern
- (Europa) Schweiz, norra
- (Europa) Storbritannien, södra
- (Mellanöstern) Förenade Arabemiraten, norra
- (Sydamerika) Brasilien, södra
- (USA) USA, centrala
- (USA) USA, östra
- (USA) USA, östra 2
- (USA) USA, norra centrala
- (USA) USA, södra centrala
- (USA) USA, västra
- (USA) USA, västra 2

Information om priser finns i prisinformation för [blobar,](https://azure.microsoft.com/pricing/details/storage/blobs) [filer,](https://azure.microsoft.com/pricing/details/storage/files/) [köer](https://azure.microsoft.com/pricing/details/storage/queues/)och [tabeller.](https://azure.microsoft.com/pricing/details/storage/tables/)

## <a name="read-access-to-data-in-the-secondary-region"></a>Läsåtkomst till data i den sekundära regionen

Geo-redundant lagring (med GRS eller GZRS) replikerar dina data till en annan fysisk plats i den sekundära regionen för att skydda mot regionala avbrott. Dessa data kan dock bara läsas om kunden eller Microsoft initierar en redundans från den primära till den sekundära regionen. När du aktiverar läsåtkomst till den sekundära regionen kan dina data alltid läsas, även i en situation där den primära regionen blir otillgänglig. För läsåtkomst till den sekundära regionen aktiverar du read-access geo-redundant storage (RA-GRS) eller read-access geo-zone-redundant storage (RA-GZRS).

> [!NOTE]
> Azure Files stöder inte ra-GRS (read-access geo-redundant storage) och geo-zonredundant lagring med läsbehörighet (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Utforma dina program för läsåtkomst till den sekundära

Om ditt lagringskonto har konfigurerats för läsåtkomst till den sekundära regionen kan du utforma dina program så att de sömlöst växlar till att läsa data från den sekundära regionen om den primära regionen av någon anledning blir otillgänglig. 

Den sekundära regionen är tillgänglig för läsåtkomst när du aktiverar RA-GRS eller RA-GZRS, så att du kan testa programmet i förväg för att se till att det läses korrekt från den sekundära vid ett avbrott. Mer information om hur du utformar dina program för hög tillgänglighet finns i [Använda geo-redundans för att utforma program med hög tillgänglighet.](geo-redundant-design.md)

När läsåtkomst till den sekundära är aktiverad kan programmet läsas från den sekundära slutpunkten och från den primära slutpunkten. Den sekundära slutpunkten lägger till suffixet *–secondary* i kontonamnet. Om din primära slutpunkt för Blob Storage till exempel är `myaccount.blob.core.windows.net` är den sekundära slutpunkten `myaccount-secondary.blob.core.windows.net` . Kontots åtkomstnycklar för ditt lagringskonto är desamma för både den primära och den sekundära slutpunkten.

### <a name="check-the-last-sync-time-property"></a>Kontrollera egenskapen Tidpunkt för senaste synkronisering

Eftersom data replikeras till den sekundära regionen asynkront ligger den sekundära regionen ofta bakom den primära regionen. Om ett fel inträffar i den primära regionen är det troligt att alla skrivningar till den primära regionen ännu inte har replikerats till den sekundära regionen.

För att avgöra vilka skrivåtgärder som har replikerats till den sekundära regionen kan ditt program kontrollera egenskapen **Senaste synkroniseringstid** för ditt lagringskonto. Alla skrivåtgärder som skrivits till den primära regionen före den senaste synkroniseringen har replikerats till den sekundära regionen, vilket innebär att de är tillgängliga för läsning från den sekundära regionen. Skrivåtgärder som skrivits till den primära regionen efter den senaste synkroniseringen kan ha replikerats till den sekundära regionen, vilket innebär att de kanske inte är tillgängliga för läsåtgärder.

Du kan fråga värdet för egenskapen **Tidpunkt för** senaste synkronisering med hjälp Azure PowerShell, Azure CLI eller något av Azure Storage klientbiblioteken. Egenskapen **Last Sync Time (Tid** för senaste synkronisering) är ett datum-/tidsvärde för GMT. Mer information finns i Kontrollera [egenskapen Last Sync Time för ett lagringskonto.](last-sync-time-get.md)

## <a name="summary-of-redundancy-options"></a>Sammanfattning av redundansalternativ

Tabellerna i följande avsnitt sammanfattar de redundansalternativ som är tillgängliga för Azure Storage

### <a name="durability-and-availability-parameters"></a>Parametrar för hållbarhet och tillgänglighet

I följande tabell beskrivs nyckelparametrarna för varje redundansalternativ:

| Parameter | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Procent hållbarhet för objekt under ett visst år | minst 99,9999999999 % (11 nia) | minst 99,99999999999 % (12 nia) | minst 99,9999999999999999 % (16 nia) | minst 99,9999999999999999 % (16 nia) |
| Tillgänglighet för läsbegäranden | Minst 99,9 % (99 % för den coola åtkomstnivån) | Minst 99,9 % (99 % för den coola åtkomstnivån) | Minst 99,9 % (99 % för den coola åtkomstnivån) för GRS<br /><br />Minst 99,99 % (99,9 % för den coola åtkomstnivån) för RA-GRS | Minst 99,9 % (99 % för den coola åtkomstnivån) för GZRS<br /><br />Minst 99,99 % (99,9 % för den coola åtkomstnivån) för RA-GZRS |
| Tillgänglighet för skrivbegäranden | Minst 99,9 % (99 % för den coola åtkomstnivån) | Minst 99,9 % (99 % för den coola åtkomstnivån) | Minst 99,9 % (99 % för den coola åtkomstnivån) | Minst 99,9 % (99 % för den coola åtkomstnivån) |
| Antal kopior av data som underhålls på separata noder | Tre kopior inom en enda region | Tre kopior över separata tillgänglighetszoner inom en enda region | Totalt sex kopior, inklusive tre i den primära regionen och tre i den sekundära regionen | Totalt sex kopior, inklusive tre över separata tillgänglighetszoner i den primära regionen och tre lokalt redundanta kopior i den sekundära regionen |

### <a name="durability-and-availability-by-outage-scenario"></a>Hållbarhet och tillgänglighet efter avbrottsscenario

Följande tabell visar om dina data är beständiga och tillgängliga i ett visst scenario, beroende på vilken typ av redundans som gäller för ditt lagringskonto:

| Avbrottsscenario | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| En nod i ett datacenter blir otillgänglig | Ja | Ja | Ja | Ja |
| Ett helt datacenter (zonindeligt eller icke-zonindeligt) blir otillgängligt | Inga | Ja | Ja<sup>1</sup> | Yes |
| Ett regionomfattande avbrott inträffar i den primära regionen | Inga | Inga | Ja<sup>1</sup> | Ja<sup>1</sup> |
| Läsåtkomst till den sekundära regionen är tillgänglig om den primära regionen blir otillgänglig | Inga | Inga | Ja (med RA-GRS) | Ja (med RA-GZRS) |

<sup>1 Konto</sup> redundans krävs för att återställa skrivtillgänglighet om den primära regionen blir otillgänglig. Mer information finns i [Haveriberedskap och redundans för lagringskonto.](storage-disaster-recovery-guidance.md)

### <a name="supported-azure-storage-services"></a>Stöd Azure Storage tjänster

I följande tabell visas vilka redundansalternativ som stöds av varje Azure Storage tjänst.

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br />Azure Managed Disks | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br /> | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br /> |

### <a name="supported-storage-account-types"></a>Lagringskontotyper som stöds

I följande tabell visas vilka redundansalternativ som stöds av varje typ av lagringskonto. Information om lagringskontotyper finns i Översikt [över lagringskonto.](storage-account-overview.md)

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| General-purpose v2<br /> General-purpose v1<br /> BlockBlobStorage<br /> BlobStorage<br /> FileStorage | General-purpose v2<br /> BlockBlobStorage<br /> FileStorage | General-purpose v2<br /> General-purpose v1<br /> BlobStorage | General-purpose v2 |

Alla data för alla lagringskonton kopieras enligt redundansalternativ för lagringskontot. Objekt som blockblobar, tilläggsblobar, sidblobar, köer, tabeller och filer kopieras. Data på alla nivåer, inklusive arkivnivån, kopieras. Mer information om blobnivåer finns i [Åtkomstnivåer för Azure Blob Storage: hot, cool och archive.](../blobs/storage-blob-storage-tiers.md)

Prisinformation för varje redundansalternativ finns i [Azure Storage prissättning.](https://azure.microsoft.com/pricing/details/storage/)

> [!NOTE]
> Azure Premium Disklagring för närvarande endast stöd för lokalt redundant lagring (LRS). Blockbloblagringskonton stöder lokalt redundant lagring (LRS) och zonredundant lagring (ZRS) i vissa regioner.

## <a name="data-integrity"></a>Dataintegritet

Azure Storage regelbundet verifierar integriteten för data som lagras med hjälp av cykliska redundanskontroller (CPC). Om skadade data identifieras repareras de med redundanta data. Azure Storage också kontrollsumor på all nätverkstrafik för att identifiera skadade datapaket vid lagring eller hämtning av data.

## <a name="see-also"></a>Se även

- [Kontrollera egenskapen Last Sync Time för ett lagringskonto](last-sync-time-get.md)
- [Ändra redundansalternativ för ett lagringskonto](redundancy-migration.md)
- [Använd geo-redundans för att utforma program med hög tillgänglighet](geo-redundant-design.md)
- [Haveriberedskap och lagringskontoredundans](storage-disaster-recovery-guidance.md)
