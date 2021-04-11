---
title: Metod tips för lagring och säkerhets kopiering
titleSuffix: Azure Kubernetes Service
description: Lär dig metod tips för kluster operatörer för lagring, data kryptering och säkerhets kopiering i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 9b3ee6fd7eea958a573743b21bf8940458e2a965
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104923"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Metod tips för lagring och säkerhets kopiering i Azure Kubernetes service (AKS)

När du skapar och hanterar kluster i Azure Kubernetes service (AKS) behöver dina program ofta lagring. Se till att du förstår Pod prestanda behov och åtkomst metoder så att du kan välja bästa lagring för ditt program. Storleken på AKS kan påverka dina lagrings alternativ. Planera för att säkerhetskopiera och testa återställnings processen för bifogad lagring.

I den här artikeln fokuserar vi på lagrings överväganden för kluster operatörer. I den här artikeln lär du dig:

> [!div class="checklist"]
> * Vilka lagrings typer som är tillgängliga.
> * Så här ändrar du storlek på AKS-noder för lagrings prestanda.
> * Skillnader mellan dynamisk och statisk etablering av volymer.
> * Sätt att säkerhetskopiera och skydda dina data volymer.

## <a name="choose-the-appropriate-storage-type"></a>Välj lämplig lagrings typ

> **Vägledning och metodtips**
> 
> Förstå programmets behov för att välja rätt lagring. Använd hög prestanda, SSD-lagring för produktions arbets belastningar. Planera för nätverks baserad lagring när du behöver flera samtidiga anslutningar.

Program kräver ofta olika typer och hastigheter för lagring. Fastställ den lämpligaste lagrings typen genom att ställa följande frågor. 
* Behöver dina program lagrings enheter som ansluter till enskilda poddar?
* Behöver dina program lagrings utrymme delat över flera poddar? 
* Är lagringen för skrivskyddad åtkomst till data?
* Kommer lagringen att användas för att skriva stora mängder strukturerade data? 

I följande tabell beskrivs tillgängliga lagrings typer och deras funktioner:

| Användningsfall | Volym-plugin | Läs/skriv en gång | Skrivskyddat antal | Läs/skriv många | Stöd för Windows Server-behållare |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Delad konfiguration       | Azure Files   | Ja | Ja | Ja | Ja |
| Strukturerade AppData        | Azure-diskar   | Ja | Inga  | Inga  | Ja |
| Ostrukturerade data, fil system åtgärder | [BlobFuse][blobfuse] | Ja | Ja | Ja | Inga |

AKS tillhandahåller två primära typer av säker lagring för volymer som backas upp av Azure-diskar eller Azure Files. Båda använder standard-Azure Storage tjänst kryptering (SSE) som krypterar data i vila. Diskar kan inte krypteras med Azure Disk Encryption på AKS-noden.

Både Azure Files-och Azure-diskar är tillgängliga på prestanda nivåerna standard och Premium:

- *Premium* diskar
    - Backas upp av Solid State-diskar med höga prestanda (SSD). 
    - Rekommenderas för alla produktions arbets belastningar.
- *Standard* diskar
    - Backas upp av vanliga snurrande diskar (HDD).
    - Passar för arkivering eller data som används ofta.

Förstå programmets prestanda behov och åtkomst mönster för att välja lämplig lagrings nivå. Mer information om Managed Disks storlekar och prestanda nivåer finns i [Översikt över Azure Managed disks][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Skapa och Använd lagrings klasser för att definiera program behov

Definiera den typ av lagring som du vill använda med Kubernetes *lagrings klasser*. Lagrings klassen refereras sedan till i POD eller distributions specifikationen. Definitioner av lagrings klass fungerar tillsammans för att skapa rätt lagring och ansluta den till poddar. 

Mer information finns i [lagrings klasser i AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Storlek på noderna för lagrings behov

> **Vägledning och metodtips**
> 
> Varje nods storlek stöder maximalt antal diskar. Olika Node-storlekar ger också olika mängder lokal lagring och nätverks bandbredd. Planera lämpligt för att ditt program kräver att du distribuerar rätt storlek på noder.

AKS-noder körs som olika typer och storlekar för virtuella Azure-datorer. Varje VM-storlek ger:
* En annan mängd kärn resurser, till exempel processor och minne. 
* Ett maximalt antal diskar som kan anslutas. 

Lagrings prestanda varierar också mellan VM-storlekar för maximalt lokalt och kopplat disk-IOPS (indata/utdata-åtgärder per sekund).

Om dina program kräver Azure-diskar som lagrings lösning, utarbeta en lämplig nod VM-storlek. Lagrings funktioner och processor-och minnes mängder spelar en större roll när du bestämmer dig för en VM-storlek. 

Till exempel, medan både *Standard_B2ms* -och *Standard_DS2_v2* VM-storlekarna innehåller en liknande mängd processor-och minnes resurser är deras potentiella lagrings prestanda olika:

| Nodtyp och storlek | Virtuell processor | Minne (GiB) | Maximalt antal datadiskar | Högsta antal ej cachelagrade disk-IOPS | Högsta antal cachelagrade data flöden (Mbit/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1 920                  | 22,5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6 400                  | 96                             |

I det här exemplet erbjuder *Standard_DS2_v2* två gånger så många anslutna diskar och tre till fyra gånger mängden IOPS och disk data flöde. Om du bara jämförde Core Compute-resurser och jämförda kostnader kan du ha valt *Standard_B2ms* VM-storlek med dåliga lagrings prestanda och begränsningar. 

Arbeta med ditt program utvecklings team för att förstå deras lagrings kapacitet och prestanda behov. Välj lämplig VM-storlek för AKS-noderna för att uppfylla eller överskrida prestanda behoven. Regelbundna bas linje program för att justera VM-storlek efter behov.

Mer information om tillgängliga VM-storlekar finns i [storlekar för virtuella Linux-datorer i Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Konfigurera volymer dynamiskt

> **Vägledning och metodtips** 
>
> Undvik att ta bort och tilldela beständiga volymer genom att minska hanterings kostnader och aktivera skalning. Använd dynamisk etablering. I lagrings klasserna definierar du lämplig åter krav princip för att minimera onödiga lagrings kostnader när poddar har tagits bort.

Använd beständiga volymer för att koppla lagring till poddar. Beständiga volymer kan skapas manuellt eller dynamiskt. Att skapa beständiga volymer lägger till hanterings kostnader manuellt och begränsar din möjlighet att skala. I stället kan du etablera en permanent volym dynamiskt för att förenkla lagrings hanteringen och göra det möjligt för dina program att växa och skala efter behov.

![Beständiga volym anspråk i ett Azure Kubernetes Services-kluster (AKS)](media/concepts-storage/persistent-volume-claims.png)

Med ett permanent volym anspråk (PVC) kan du dynamiskt skapa lagrings utrymme efter behov. De underliggande Azure-diskarna skapas som poddar-begäranden. I pod-definitionen begär du att en volym ska skapas och kopplas till en angiven monterings Sök väg.

Begrepp för hur du skapar och använder volymer dynamiskt finns i anspråk på [beständiga volymer][aks-concepts-storage-pvcs].

Information om hur du visar dessa volymer finns i så här skapar och använder du en permanent volym med [Azure-diskar][dynamic-disks] eller [Azure Files][dynamic-files].

Ange lämplig *reclaimPolicy* som en del av definitionerna för lagrings klass. Den här reclaimPolicy styr beteendet för den underliggande Azure Storage-resursen när Pod tas bort. Den underliggande lagrings resursen kan antingen tas bort eller behållas för framtida Pod användning. Ange reclaimPolicy som ska *behållas* eller *tas bort*. 

Förstå dina program behov och implementera regelbundna kontroller för kvarhållen lagring för att minimera mängden oanvänt och fakturerat lagrings utrymme.

Mer information om alternativ för lagrings klass finns i principer för att [frigöra lagrings utrymme][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Skydda och säkerhetskopiera dina data

> **Vägledning och metodtips** 
> 
> Säkerhetskopiera dina data med hjälp av ett lämpligt verktyg för din lagrings typ, till exempel Velero eller Azure Backup. Verifiera integriteten och säkerheten för dessa säkerhets kopior.

När dina program lagrar och använder data som behålls på diskar eller filer måste du göra regelbundna säkerhets kopior eller ögonblicks bilder av dessa data. Azure-diskar kan använda inbyggda ögonblicks bild tekniker. Dina program kan behöva tömma skrivningar till disk innan du utför ögonblicks bild åtgärden. [Velero][velero] kan säkerhetskopiera beständiga volymer tillsammans med ytterligare kluster resurser och konfigurationer. Om du inte kan [ta bort tillstånd från dina program][remove-state]säkerhetskopierar du data från permanenta volymer och testar regelbundet återställnings åtgärderna för att kontrol lera data integriteten och de processer som krävs.

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
[managed-disks]: ../virtual-machines/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
