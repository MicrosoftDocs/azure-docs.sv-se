---
title: Översikt över dataskydd
titleSuffix: Azure Storage
description: Alternativen för data skydd som är tillgängliga för Blob Storage och Azure Data Lake Storage Gen2 data gör att du kan skydda dina data från att tas bort eller skrivas över. Om du behöver återställa data som har tagits bort eller skrivits över, kan den här guiden hjälpa dig att välja det återställnings alternativ som passar bäst för ditt scenario.
services: storage
author: tamram
ms.service: storage
ms.date: 03/22/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: prishet
ms.subservice: common
ms.openlocfilehash: afd98e629500bc90cc9ddd1ed4ab2472f733e845
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803800"
---
# <a name="data-protection-overview"></a>Översikt över dataskydd

Azure Storage tillhandahåller data skydd för Blob Storage och Azure Data Lake Storage Gen2 för att hjälpa dig att förbereda för scenarier där du behöver återställa data som har tagits bort eller skrivits över. Det är viktigt att tänka på hur du bäst skyddar dina data innan en incident inträffar som kan äventyra den. Den här guiden hjälper dig att besluta i förväg vilka data skydds funktioner som scenariot kräver och hur du implementerar dem. Om du behöver återställa data som har tagits bort eller skrivits över, ger den här översikten även vägledning om hur du går vidare, baserat på ditt scenario.

I Azure Storage-dokumentationen refererar *data skydd* till strategier för att skydda lagrings kontot och data i den från att tas bort eller ändras, eller för att återställa data efter att de har tagits bort eller ändrats. Azure Storage erbjuder också alternativ för *haveri beredskap*, inklusive flera nivåer av redundans för att skydda dina data från tjänst avbrott på grund av maskin varu problem eller naturliga katastrofer, och kundhanterad redundansväxling i händelse av att data centret i den primära regionen blir otillgängligt. Mer information om hur dina data skyddas från avbrott i tjänsten finns i [haveri beredskap](#disaster-recovery).

## <a name="recommendations-for-basic-data-protection"></a>Rekommendationer för grundläggande data skydd

Om du letar efter grundläggande data skydds täckning för ditt lagrings konto och de data som det innehåller, rekommenderar Microsoft att du vidtar följande steg för att börja med:

- Konfigurera ett Azure Resource Manager lås på lagrings kontot för att skydda kontot från borttagnings-eller konfigurations ändringar. [Lära sig mer...](../common/lock-account-resource.md)
- Aktivera mjuk borttagning av behållare för lagrings kontot för att återställa en borttagen behållare och dess innehåll. [Lära sig mer...](soft-delete-container-enable.md)
- Spara status för en blob med jämna mellanrum:
  - För Blob Storage arbets belastningar aktiverar du Blob-versioner för att automatiskt spara statusen för dina data varje gång en BLOB tas bort eller skrivs över. [Lära sig mer...](versioning-enable.md)
  - För Azure Data Lake Storage arbets belastningar ska du ta manuella ögonblicks bilder för att spara statusen för dina data vid en viss tidpunkt. [Lära sig mer...](snapshots-overview.md)

Dessa alternativ, samt ytterligare alternativ för data skydd för andra scenarier, beskrivs mer detaljerat i följande avsnitt.

En översikt över kostnader som ingår i dessa funktioner finns i [Översikt över kostnads överväganden](#summary-of-cost-considerations).

## <a name="overview-of-data-protection-options"></a>Översikt över alternativ för data skydd

I följande tabell sammanfattas de tillgängliga alternativen i Azure Storage för vanliga data skydds scenarier. Välj de scenarier som gäller för din situation för att lära dig mer om de alternativ som är tillgängliga för dig. Observera att inte alla funktioner är tillgängliga just nu för lagrings konton med hierarkiskt namn område aktiverat.

| Scenario | Data skydds alternativ | Rekommendationer | Skydds förmån | Tillgängligt för Data Lake Storage |
|--|--|--|--|--|
| Förhindra att ett lagrings konto tas bort eller ändras. | Azure Resource Manager lås<br />[Lära sig mer...](../common/lock-account-resource.md) | Lås alla dina lagrings konton med ett Azure Resource Manager lås för att förhindra borttagning av lagrings kontot. | Skyddar lagrings kontot mot borttagnings-eller konfigurations ändringar.<br /><br />Skyddar inte behållare eller blobbar i kontot från att tas bort eller skrivas över. | Ja |
| Förhindra att en behållare och dess blobbar tas bort eller ändras för ett intervall som du styr. | Oföränderlighets-princip på en behållare<br />[Lära sig mer...](storage-blob-immutable-storage.md) | Ange en oföränderlighets-princip på en behållare för att skydda affärs kritiska dokument, till exempel, för att uppfylla juridiska eller reglerade krav på efterlevnad. | Skyddar en behållare och dess blobbar från alla borttagningar och skriv över.<br /><br />När ett juridiskt undantag eller en låst tidsbaserad bevarande princip tillämpas, skyddas lagrings kontot också från borttagning. Behållare för vilka ingen oföränderlighets-princip har angetts skyddas inte från borttagning. | Ja, i för hands version |
| Återställ en borttagen behållare inom ett angivet intervall. | Mjuk borttagning av behållare (förhands granskning)<br />[Lära sig mer...](soft-delete-container-overview.md) | Aktivera mjuk borttagning av behållare för alla lagrings konton, med ett minsta kvarhållningsintervall på 7 dagar.<br /><br />Aktivera BLOB-versioner och blob-mjuk borttagning tillsammans med behållarens mjuk borttagning för att skydda enskilda blobbar i en behållare.<br /><br />Lagra behållare som kräver olika bevarande perioder i separata lagrings konton. | En borttagen behållare och dess innehåll kan återställas inom kvarhållningsperioden.<br /><br />Endast behållar nivå åtgärder (t. ex. [borttagnings behållare](/rest/api/storageservices/delete-container)) kan återställas. Med mjuk borttagning av behållare kan du återställa en enskild BLOB i behållaren om denna BLOB tas bort. | Ja, i för hands version |
| Spara automatiskt statusen för en BLOB i en tidigare version när den skrivs över eller raderas. | BLOB-versioner<br />[Lära sig mer...](versioning-overview.md) | Aktivera BLOB-versioner, tillsammans med en mjuk borttagning av behållare och blob-mjuk borttagning, för lagrings konton där du behöver optimalt skydd för BLOB-data.<br /><br />Lagra BLOB-data som inte kräver versions hantering i ett separat konto för att begränsa kostnaderna. | Varje Blob överskrivning eller Delete-åtgärd skapar en ny version. En BLOB kan återställas från en tidigare version om blobben tas bort eller skrivs över. | Inga |
| Återställ en borttagen BLOB-eller blob-version inom ett angivet intervall. | BLOB-mjuk borttagning<br />[Lära sig mer...](soft-delete-blob-overview.md) | Aktivera BLOB-mjuk borttagning för alla lagrings konton, med ett minsta kvarhållningsintervall på 7 dagar.<br /><br />Aktivera BLOB-versioner och container mjuk borttagning tillsammans med BLOB-mjuk borttagning för optimalt skydd av BLOB-data.<br /><br />Lagra blobbar som kräver olika bevarande perioder i separata lagrings konton. | En borttagen BLOB-eller blob-version kan återställas inom kvarhållningsperioden. | Inga |
| Återställ en uppsättning block blobbar till en tidigare tidpunkt. | Återställning från tidpunkt<br />[Lära sig mer...](point-in-time-restore-overview.md) | Om du vill använda tidpunkts återställning för att återgå till ett tidigare tillstånd kan du utforma ditt program för att ta bort enskilda block blobbar i stället för att ta bort behållare. | En uppsättning block blobbar kan återställas till sitt tillstånd vid en viss tidpunkt tidigare.<br /><br />Endast åtgärder som utförs på block-blobbar återställs. Åtgärder som utförs på behållare, Page blobbar eller bifogade blobbar återställs inte. | Inga |
| Spara statusen för en BLOB manuellt vid en viss tidpunkt. | BLOB-ögonblicksbild<br />[Lära sig mer...](snapshots-overview.md) | Rekommenderas som ett alternativ till BLOB-versioner när versions hantering inte är lämplig för ditt scenario, på grund av kostnader eller andra överväganden, eller när lagrings kontot har ett hierarkiskt namn område aktiverat. | En BLOB kan återställas från en ögonblicks bild om blobben skrivs över. Om blobben tas bort, raderas även ögonblicks bilder. | Ja, i för hands version |
| En BLOB kan tas bort eller skrivas över, men data kopieras regelbundet till ett andra lagrings konto. | Samla in din egen lösning för att kopiera data till ett annat konto genom att använda Azure Storage objekt replikering eller ett verktyg som AzCopy eller Azure Data Factory. | Rekommenderas för att skydda dig mot oväntade avsiktliga åtgärder eller oförutsägbara scenarier.<br /><br />Skapa det andra lagrings kontot i samma region som det primära kontot för att undvika utgående kostnader. | Data kan återställas från det andra lagrings kontot om det primära kontot komprometteras på något sätt. | AzCopy och Azure Data Factory stöds.<br /><br />Objekt replikering stöds inte. |

## <a name="data-protection-by-resource-type"></a>Data skydd efter resurs typ

I följande tabell sammanfattas alternativen för Azure Storage data skydd enligt de resurser som de skyddar.

| Data skydds alternativ | Skyddar ett konto från att tas bort | Skyddar en behållare från att tas bort | Skyddar en BLOB från att tas bort | Skyddar en BLOB från att skriva över |
|--|--|--|--|--|
| Azure Resource Manager lås | Ja | Ingen<sup>1</sup> | Inga | Inga |
| Oföränderlighets-princip på en behållare | Ja<sup>2</sup> | Ja | Ja | Ja |
| Mjuk borttagning av behållare | Inga | Ja | Inga | Inga |
| Blob-version<sup>3</sup> | Inga | Inga | Ja | Ja |
| BLOB-mjuk borttagning<sup>3</sup> | Inga | Inga | Ja | Ja |
| Återställning av tidpunkt<sup>3</sup> | Inga | Inga | Ja | Ja |
| BLOB-ögonblicksbild | Inga | Inga | Inga | Ja |
| Samla in din egen lösning för att kopiera data till ett andra konto<sup>4</sup> | Inga | Ja | Ja | Ja |

<sup>1</sup> ett Azure Resource Manager lås skyddar inte en behållare från att tas bort.<br />
<sup>2</sup> när ett juridiskt undantag eller en låst tidsbaserad bevarande princip tillämpas för en behållare, skyddas lagrings kontot också från borttagning.<br />
<sup>3</sup> stöds för närvarande inte för data Lake Storage-arbetsbelastningar.<br />
<sup>4</sup> AzCopy och Azure Data Factory är alternativ som stöds för både Blob Storage-och data Lake Storage-arbetsbelastningar. Objekt replikering stöds endast för Blob Storage arbets belastningar.<br />

## <a name="recover-deleted-or-overwritten-data"></a>Återställa borttagna eller överskrivna data

Om du behöver återställa data som har skrivits över eller tagits bort, hur du fortsätter beror på vilka data skydds alternativ du har aktiverat och vilken resurs som påverkades. I följande tabell beskrivs de åtgärder som du kan vidta för att återställa data.

| Borttagen eller överskriven resurs | Möjliga återställnings åtgärder | Krav för återställning |
|--|--|--|
| Lagringskonto | Försök att återställa det borttagna lagrings kontot<br />[Lära sig mer...](../common/storage-account-recover.md) | Lagrings kontot skapades ursprungligen med Azure Resource Manager distributions modell och togs bort under de senaste 14 dagarna. Ett nytt lagrings konto med samma namn har inte skapats sedan det ursprungliga kontot togs bort. |
| Container | Återställa den Soft-borttagna behållaren och dess innehåll<br />[Lära sig mer...](soft-delete-container-enable.md) | Den mjuka borttagningen av behållare är aktive rad och kvarhållning av behållarens mjukhet har inte gått ut än. |
| Behållare och blobbar | Återställa data från ett andra lagrings konto | Alla container-och blob-åtgärder har faktiskt repliker ATS till ett andra lagrings konto. |
| BLOB (vilken typ som helst) | Återställa en BLOB från en tidigare version<sup>1</sup><br />[Lära sig mer...](versioning-enable.md) | BLOB-versioner aktive ras och bloben har en eller flera tidigare versioner. |
| BLOB (vilken typ som helst) | Återställa en mjuk borttagen BLOB<sup>1</sup><br />[Lära sig mer...](soft-delete-blob-enable.md) | BLOB-mjuk borttagning är aktiverat och kvarhållningsintervall för mjuk borttagning har inte gått ut. |
| BLOB (vilken typ som helst) | Återställa en BLOB från en ögonblicks bild<br />[Lära sig mer...](snapshots-manage-dotnet.md) | Blobben har en eller flera ögonblicks bilder. |
| Uppsättning block-blobbar | Återställa en uppsättning block blobbar till sitt tillstånd vid en tidigare tidpunkt<sup>1</sup><br />[Lära sig mer...](point-in-time-restore-manage.md) | Återställning av tidpunkt är aktiverat och återställnings punkten ligger inom kvarhållningsintervallet. Lagrings kontot har inte komprometterats eller är skadat. |
| Blob-version | Återställa en mjuk borttagnings version<sup>1</sup><br />[Lära sig mer...](soft-delete-blob-enable.md) | BLOB-mjuk borttagning är aktiverat och kvarhållningsintervall för mjuk borttagning har inte gått ut. |

<sup>1</sup> stöds för närvarande inte för data Lake Storage-arbetsbelastningar.

## <a name="summary-of-cost-considerations"></a>Sammanfattning av kostnads överväganden

I följande tabell sammanfattas kostnads överväganden för de olika data skydds alternativ som beskrivs i den här hand boken.

| Data skydds alternativ | Kostnadsöverväganden |
|-|-|
| Azure Resource Manager lås för ett lagrings konto | Ingen avgift för att konfigurera ett lås på ett lagrings konto. |
| Oföränderlighets-princip på en behållare | Ingen avgift för att konfigurera en oföränderlighets-princip på en behållare. |
| Mjuk borttagning av behållare | Ingen avgift för att aktivera mjuk borttagning av behållare för ett lagrings konto. Data i en mjuk, borttagen behållare faktureras med samma taxa som aktiva data tills den mjuk borttagnings behållaren tas bort permanent. |
| BLOB-versioner | Ingen avgift för att aktivera BLOB-versioner för ett lagrings konto. När BLOB-versioner har Aktiver ATS skapar varje Skriv-eller borttagnings åtgärd på en BLOB i kontot en ny version, vilket kan leda till ökade kapacitets kostnader.<br /><br />En blob-version faktureras baserat på unika block eller sidor. Kostnaderna ökar därför när bas-bloben avviker från en viss version. Att ändra en BLOB-eller BLOB-versions nivå kan ha en fakturerings påverkan. Mer information finns i [priser och fakturering](versioning-overview.md#pricing-and-billing).<br /><br />Använd livs cykel hantering för att ta bort äldre versioner vid behov för att kontrol lera kostnaderna. Mer information finns i [optimera kostnader genom att automatisera Azure-Blob Storage åtkomst nivåer](storage-lifecycle-management-concepts.md). |
| BLOB-mjuk borttagning | Ingen avgift för att aktivera mjuk borttagning av BLOB för ett lagrings konto. Data i en mjuk, borttagen BLOB debiteras enligt samma taxa som aktiva data tills den mjuk borttagnings bara bloben tas bort permanent. |
| Återställning från tidpunkt | Ingen avgift för att aktivera återställning vid tidpunkter för ett lagrings konto. men om du aktiverar återställning av punkt-i-tid aktive ras även BLOB-versioner, mjuk borttagning och ändrings flöden som kan resultera i ytterligare kostnader.<br /><br />Du debiteras för återställning av tidpunkt när du utför en återställnings åtgärd. Kostnaden för en återställnings åtgärd beror på hur mycket data som återställs. Mer information finns i [priser och fakturering](point-in-time-restore-overview.md#pricing-and-billing). |
| BLOB-ögonblicksbilder | Data i en ögonblicks bild faktureras baserat på unika block eller sidor. Kostnaderna ökar därför när bas-bloben avviker från ögonblicks bilden. Att ändra en BLOB eller en ögonblicks bilds nivå kan ha en fakturerings påverkan. Mer information finns i [priser och fakturering](snapshots-overview.md#pricing-and-billing).<br /><br />Använd livs cykel hantering för att ta bort äldre ögonblicks bilder vid behov för att kontrol lera kostnaderna. Mer information finns i [optimera kostnader genom att automatisera Azure-Blob Storage åtkomst nivåer](storage-lifecycle-management-concepts.md). |
| Kopiera data till ett andra lagrings konto | Att underhålla data på ett andra lagrings konto medför kapacitet och transaktionskostnader. Om det andra lagrings kontot finns i en annan region än käll kontot, debiteras även utgående kostnader när du kopierar data till det andra kontot. |

## <a name="disaster-recovery"></a>Haveriberedskap

Azure Storage behåller alltid flera kopior av dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskin varu haverier, nätverks-eller strömavbrott, och massiv natur katastrofer. Redundans garanterar att ditt lagrings konto uppfyller sina tillgänglighets-och hållbarhets mål även om de inte är i rätt tid. Mer information om hur du konfigurerar ditt lagrings konto för hög tillgänglighet finns i [Azure Storage redundans](../common/storage-redundancy.md).

Om ditt lagrings konto är redundant över två geografiska regioner (Geo-redundant) i händelse av att ett fel uppstår i ett Data Center, har du möjlighet att redundansväxla ditt konto från den primära regionen till den sekundära regionen. Mer information finns i [haveri beredskap och redundans för lagrings konton](../common/storage-disaster-recovery-guidance.md).

Kundhanterad redundans stöds för närvarande inte för lagrings konton med hierarkiskt namn område aktiverat. Mer information finns i [Blob Storage-funktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="next-steps"></a>Nästa steg

- [Redundans i Azure Storage](../common/storage-redundancy.md)
- [Haveriberedskap och lagringskontoredundans](../common/storage-disaster-recovery-guidance.md)
