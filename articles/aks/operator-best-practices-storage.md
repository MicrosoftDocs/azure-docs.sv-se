---
title: Metod tips för lagring och säkerhets kopiering
titleSuffix: Azure Kubernetes Service
description: Lär dig metod tips för kluster operatörer för lagring, data kryptering och säkerhets kopiering i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c683cbf7802fbf5420ec95d49e2dfda624ce093f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551803"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Metod tips för lagring och säkerhets kopiering i Azure Kubernetes service (AKS)

När du skapar och hanterar kluster i Azure Kubernetes service (AKS) behöver dina program ofta lagring. Det är viktigt att förstå prestanda behov och åtkomst metoder för poddar så att du kan tillhandahålla lämplig lagring för program. Storleken på AKS kan påverka de här lagrings alternativen. Du bör också planera för hur du säkerhetskopierar och testar återställnings processen för den anslutna lagringen.

I den här artikeln fokuserar vi på lagrings överväganden för kluster operatörer. I den här artikeln lär du dig:

> [!div class="checklist"]
> * Vilka typer av lagrings utrymme är tillgängliga
> * Så här ändrar du storlek på AKS-noder för lagrings prestanda
> * Skillnader mellan dynamisk och statisk etablering av volymer
> * Sätt att säkerhetskopiera och skydda dina data volymer

## <a name="choose-the-appropriate-storage-type"></a>Välj lämplig lagrings typ

**Vägledning för bästa praxis** – förstå programmets behov av att välja rätt lagring. Använd hög prestanda, SSD-lagring för produktions arbets belastningar. Planera för nätverks baserad lagring när det finns behov av flera samtidiga anslutningar.

Program kräver ofta olika typer och hastigheter för lagring. Behöver dina program lagrings enheter som ansluter till enskilda poddar eller delas över flera poddar? Är lagringen för skrivskyddad åtkomst till data eller för att skriva stora mängder strukturerade data? Dessa lagrings behov avgör den lämpligaste typen av lagrings utrymme som ska användas.

I följande tabell beskrivs tillgängliga lagrings typer och deras funktioner:

| Användningsfall | Volym-plugin | Läs/skriv en gång | Skrivskyddat antal | Läs/skriv många | Stöd för Windows Server-behållare |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Delad konfiguration       | Azure Files   | Ja | Ja | Ja | Ja |
| Strukturerade AppData        | Azure-diskar   | Ja | Nej  | Nej  | Ja |
| Ostrukturerade data, fil system åtgärder | [BlobFuse][blobfuse] | Ja | Ja | Ja | Nej |

De två primära typerna av lagring som tillhandahålls för volymer i AKS backas upp av Azure-diskar eller Azure Files. För att förbättra säkerheten, använder båda typerna av lagring Azure Storage Service Encryption (SSE) som standard som krypterar data i vila. Diskar kan för närvarande inte krypteras med hjälp av Azure Disk Encryption på AKS Node-nivå.

Både Azure Files-och Azure-diskar är tillgängliga på prestanda nivåerna standard och Premium:

- *Premium* diskar backas upp av solid state-hårddiskar med höga prestanda (SSD). Premium diskar rekommenderas för alla produktions arbets belastningar.
- *Standard* diskar backas upp av vanliga snurrande diskar (HDD) och är lämpliga för data som kan arkiveras eller används ofta.

Förstå programmets prestanda behov och åtkomst mönster för att välja lämplig lagrings nivå. Mer information om Managed Disks storlekar och prestanda nivåer finns i [Översikt över Azure Managed disks][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Skapa och Använd lagrings klasser för att definiera program behov

Vilken typ av lagrings utrymme som du använder definieras med hjälp av Kubernetes- *lagrings klasser*. Lagrings klassen refereras sedan till i POD eller distributions specifikationen. Dessa definitioner fungerar tillsammans för att skapa lämplig lagring och ansluta den till poddar. Mer information finns i [lagrings klasser i AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Storlek på noderna för lagrings behov

**Vägledning för bästa praxis** – varje nods storlek stöder maximalt antal diskar. Olika Node-storlekar ger också olika mängder lokal lagring och nätverks bandbredd. Planera för dina program krav för att distribuera lämplig storlek på noderna.

AKS-noder körs som virtuella Azure-datorer. Olika typer och storlekar för den virtuella datorn är tillgängliga. Varje VM-storlek ger en annan mängd kärn resurser, till exempel processor och minne. De här VM-storlekarna har ett maximalt antal diskar som kan anslutas. Lagrings prestanda varierar också mellan VM-storlekar för maximalt lokalt och kopplat disk-IOPS (indata/utdata-åtgärder per sekund).

Om dina program kräver Azure-diskar som lagrings lösning, planera för och välj en lämplig VM-storlek för nod. Mängden CPU och minne är inte den enda faktorn när du väljer en VM-storlek. Lagrings funktionerna är också viktiga. Både *Standard_B2ms* och *Standard_DS2_v2* VM-storlekar innehåller till exempel en liknande mängd processor-och minnes resurser. Deras potentiella lagrings prestanda skiljer sig åt, som visas i följande tabell:

| Nodtyp och storlek | Virtuell processor | Minne (GiB) | Maximalt antal datadiskar | Högsta antal ej cachelagrade disk-IOPS | Högsta antal cachelagrade data flöden (Mbit/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1 920                  | 22,5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6 400                  | 96                             |

Här kan *Standard_DS2_v2* dubblera antalet anslutna diskar och ger tre till fyra gånger mängden IOPS och disk data flöde. Om du bara tittar på de grundläggande beräknings resurserna och de jämförda kostnaderna kan du välja storlek på *Standard_B2ms* virtuell dator och ha dåliga lagrings prestanda och begränsningar. Arbeta med ditt program utvecklings team för att förstå deras lagrings kapacitet och prestanda behov. Välj lämplig VM-storlek för AKS-noderna för att uppfylla eller överskrida prestanda behoven. Regelbundna bas linje program för att justera VM-storlek efter behov.

Mer information om tillgängliga VM-storlekar finns i [storlekar för virtuella Linux-datorer i Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Konfigurera volymer dynamiskt

**Rekommendationer om bästa praxis** – att minska hanterings kostnaderna och att skala, inte statiskt skapa och tilldela beständiga volymer. Använd dynamisk etablering. I lagrings klasserna definierar du lämplig åter krav princip för att minimera onödiga lagrings kostnader när poddar har tagits bort.

När du behöver koppla lagring till poddar använder du beständiga volymer. De här permanenta volymerna kan skapas manuellt eller dynamiskt. Manuellt skapande av permanenta volymer lägger till hanterings kostnader och begränsar din möjlighet att skala. Använd dynamisk beständig volym etablering för att förenkla lagrings hanteringen och göra det möjligt för dina program att växa och skala efter behov.

![Beständiga volym anspråk i ett Azure Kubernetes Services-kluster (AKS)](media/concepts-storage/persistent-volume-claims.png)

Med ett permanent volym anspråk (PVC) kan du dynamiskt skapa lagrings utrymme efter behov. De underliggande Azure-diskarna skapas som poddar-begäran. I pod-definitionen begär du att en volym ska skapas och kopplas till en angiven monterings Sök väg.

Begrepp för hur du skapar och använder volymer dynamiskt finns i anspråk på [beständiga volymer][aks-concepts-storage-pvcs].

Information om hur du visar dessa volymer finns i så här skapar och använder du en permanent volym med [Azure-diskar][dynamic-disks] eller [Azure Files][dynamic-files].

Ange lämplig *reclaimPolicy*som en del av definitionerna för lagrings klass. Den här reclaimPolicy styr beteendet för den underliggande Azure Storage-resursen när Pod tas bort och den permanenta volymen kanske inte längre krävs. Den underliggande lagrings resursen kan tas bort eller sparas för användning med en framtida pod. ReclaimPolicy kan ställas in för att *behålla* eller *ta bort*. Förstå dina program behov och implementera regelbundna kontroller för lagring som hålls kvar för att minimera mängden ej använda lagrings utrymme som används och faktureras.

Mer information om alternativ för lagrings klass finns i principer för att [frigöra lagrings utrymme][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Skydda och säkerhetskopiera dina data

**Vägledning för bästa praxis** – säkerhetskopiera dina data med hjälp av ett lämpligt verktyg för din lagrings typ, till exempel Velero eller Azure Backup. Verifiera integriteten och säkerheten för dessa säkerhets kopior.

När dina program lagrar och använder data som behålls på diskar eller filer måste du göra regelbundna säkerhets kopior eller ögonblicks bilder av dessa data. Azure-diskar kan använda inbyggda ögonblicks bild tekniker. Du kan behöva söka efter dina program för att tömma skrivningar till disken innan du utför ögonblicks bild åtgärden. [Velero][velero] kan säkerhetskopiera beständiga volymer tillsammans med ytterligare kluster resurser och konfigurationer. Om du inte kan [ta bort tillstånd från dina program][remove-state]säkerhetskopierar du data från permanenta volymer och testar regelbundet återställnings åtgärderna för att kontrol lera data integriteten och de processer som krävs.

Förstå begränsningarna för de olika metoderna för säkerhets kopiering av data och om du behöver ta data offline innan ögonblicks bilden. Med data säkerhets kopieringar kan du inte nödvändigt vis återställa program miljön för kluster distribution. Mer information om dessa scenarier finns i [metod tips för verksamhets kontinuitet och haveri beredskap i AKS][best-practices-multi-region].

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på metod tips för lagring i AKS. Mer information om grundläggande lagrings anvisningar i Kubernetes finns i [lagrings koncept för program i AKS][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/linux/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
