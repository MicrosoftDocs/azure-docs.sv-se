---
title: Köra arbets belastningar på kostnads effektiva virtuella datorer med låg prioritet
description: Lär dig hur du etablerar virtuella datorer med låg prioritet för att minska kostnaderna för Azure Batch arbets belastningar.
author: mscurrell
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: seodec18
ms.openlocfilehash: cafc7216e8112640f823ecee1aea055ab78b3fc6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098476"
---
# <a name="use-low-priority-vms-with-batch"></a>Använda lågprioriterade virtuella datorer med Batch

Azure Batch erbjuder virtuella datorer med låg prioritet (VM) för att minska kostnaderna för batch-arbetsbelastningar. Virtuella datorer med låg prioritet gör nya typer av batch-arbetsbelastningar möjliga genom att göra det möjligt att använda en stor mängd beräknings kraft för en mycket låg kostnad.

Virtuella datorer med låg prioritet drar nytta av överskotts kapacitet i Azure. När du anger virtuella datorer med låg prioritet i dina pooler kan Azure Batch använda detta överskott, om det är tillgängligt.

Kompromissen med att använda virtuella datorer med låg prioritet är att de virtuella datorerna inte alltid ska vara tillgängliga för tilldelning eller som kan avbrytas när som helst, beroende på tillgänglig kapacitet. Av den anledningen är virtuella datorer med låg prioritet lämplig för batch-och asynkron bearbetning av arbets belastningar där jobbets slut för ande tid är flexibelt och arbetet distribueras över flera virtuella datorer.

Virtuella datorer med låg prioritet erbjuds till ett avsevärt minskat pris jämfört med dedikerade virtuella datorer. Pris information finns i [batch-priser](https://azure.microsoft.com/pricing/details/batch/).

> [!NOTE]
> [Virtuella](https://azure.microsoft.com/pricing/spot/) datorer för virtuella datorer är nu tillgängliga för virtuella [datorer med enskild instans](../virtual-machines/spot-vms.md) och [VM Scale set](../virtual-machine-scale-sets/use-spot.md). Virtuella datorer med virtuella datorer är en utveckling av virtuella datorer med låg prioritet, men skiljer sig åt i priset kan variera och ett valfritt högsta pris kan anges när du tilldelar virtuella datorer.
>
>Azure Batch pooler påbörjar stöd för virtuella datorer i framtiden med nya versioner av batch- [API: er och verktyg](./batch-apis-tools.md). När VM-stödet för virtuella datorer är tillgängligt kommer virtuella datorer med låg prioritet att bli inaktuella – de fortsätter att stödjas med aktuella API: er och verktygs versioner i minst 12 månader för att tillåta tillräckligt med tid för migrering till virtuella datorer.
>
> Virtuella datorer för virtuella datorer stöds bara för virtuella dator konfigurations pooler. Om du vill använda virtuella datorer för virtuella datorer måste alla konfigurations pooler för moln tjänster [migreras till konfigurations pooler för virtuella datorer](batch-pool-cloud-service-to-virtual-machine-configuration.md).

## <a name="batch-support-for-low-priority-vms"></a>Batch-stöd för virtuella datorer med låg prioritet

Azure Batch innehåller flera funktioner som gör det enkelt att använda och dra nytta av virtuella datorer med låg prioritet:

- Batch-pooler kan innehålla både dedikerade virtuella datorer och virtuella datorer med låg prioritet. Antalet olika typer av virtuella datorer kan anges när en pool skapas, eller ändras när som helst för en befintlig pool, med hjälp av den explicita storleks ändringen eller med automatisk skalning. Det går inte att ändra jobb-och uppgifts sändning, oavsett vilka VM-typer som finns i poolen. Du kan också konfigurera en pool för att fullständigt använda virtuella datorer med låg prioritet för att köra jobb så billigt som möjligt, men att skapa dedikerade virtuella datorer om kapaciteten sjunker under ett minimi tröskelvärde för att fortsätta köra jobb.
- Batch-pooler söker automatiskt efter mål antalet virtuella datorer med låg prioritet. Om de virtuella datorerna blockeras eller inte är tillgängliga försöker batchen ersätta den förlorade kapaciteten och återgår till målet.
- När aktiviteter avbryts identifierar batch och automatiskt köa uppgifter som ska köras igen.
- Virtuella datorer med låg prioritet har en separat vCPU-kvot som skiljer sig från den för dedikerade virtuella datorer. Kvoten för virtuella datorer med låg prioritet är högre än kvoten för dedikerade virtuella datorer, eftersom kostnader för låg prioritets datorer är lägre. Mer information finns i [batch-tjänstens kvoter och begränsningar](batch-quota-limit.md#resource-quotas).

> [!NOTE]
> Virtuella datorer med låg prioritet stöds för närvarande inte för batch-konton som skapats i [användar prenumerations läge](accounts.md).

## <a name="considerations-and-use-cases"></a>Överväganden och användnings fall

Många batch-arbetsbelastningar passar bra för virtuella datorer med låg prioritet. Överväg att använda dem när jobb är brutna i många parallella uppgifter, eller när du har många jobb som skalas ut och distribueras över flera virtuella datorer.

Några exempel på användnings områden för batch-bearbetning som lämpar sig väl för att använda virtuella datorer med låg prioritet:

- **Utveckling och testning**: i synnerhet om storskaliga lösningar utvecklas kan betydande besparingar realiseras. Alla typer av testning kan vara till fördel, men storskaliga belastnings test och Regressions testning är bra användnings områden.
- **Kompletterande kapacitet på begäran**: virtuella datorer med låg prioritet kan användas för att komplettera vanliga dedikerade virtuella datorer. När det är tillgängligt kan jobben skalas och därmed slutföras snabbare för lägre kostnader. När det inte är tillgängligt förblir bas linjen för dedikerade virtuella datorer tillgängliga.
- **Flexibel jobb körnings tid**: om det finns flexibilitet i tid då jobben måste slutföras, kan potentiella droppar i kapacitet tolereras. men med att lägga till virtuella datorer med låg prioritet körs de ofta snabbare och en lägre kostnad.

Batch-pooler kan konfigureras för att använda virtuella datorer med låg prioritet på några sätt:

- En pool kan endast använda virtuella datorer med låg prioritet. I det här fallet återställer batch alla kapaciteter som har åsidosatts när de är tillgängliga. Den här konfigurationen är det billigaste sättet att köra jobb på.
- Virtuella datorer med låg prioritet kan användas tillsammans med en fast bas linje för dedikerade virtuella datorer. Det fasta antalet dedikerade virtuella datorer garanterar att det alltid finns en kapacitet för att behålla jobbets förlopp.
- En pool kan använda en dynamisk blandning av dedikerade och låg prioritet för virtuella datorer, så att de billigare virtuella datorerna bara används när de är tillgängliga, men för att den totala mängden dedikerade virtuella datorer ska skalas upp vid behov. Den här konfigurationen ger en minimal mängd kapacitet som är tillgänglig för att hålla jobben förloppet.

Tänk på följande när du planerar användningen av virtuella datorer med låg prioritet:

- För att maximera användningen av överskotts kapacitet i Azure kan lämpliga jobb skalas ut.
- Ibland kanske virtuella datorer inte är tillgängliga eller har avbrutits, vilket resulterar i minskad kapacitet för jobb och kan leda till uppgifts avbrott och omkörning.
- Uppgifter med kortare körnings tider tenderar att fungera bäst med virtuella datorer med låg prioritet. Jobb med längre aktiviteter kan påverkas mer om de avbryts. Om tids krävande uppgifter implementerar kontroll punkter för att spara förlopp när de körs, kan den här effekten minska. 
- Långvariga MPI-jobb som använder flera virtuella datorer är inte väl lämpade att använda virtuella datorer med låg prioritet, eftersom en virtuell dator som har varit aktive rad kan leda till hela jobbet som måste köras igen.
- Noder med låg prioritet kan markeras som oanvändbara om [reglerna för nätverks säkerhets gruppen (NSG)](batch-virtual-network.md#network-security-groups-specifying-subnet-level-rules) är felaktigt konfigurerade.

## <a name="create-and-manage-pools-with-low-priority-vms"></a>Skapa och hantera pooler med virtuella datorer med låg prioritet

En batch-pool kan innehålla både dedikerade och lågprioriterade virtuella datorer (kallas även Compute-noder). Du kan ange mål antalet för datornoderna för både dedikerade virtuella datorer och låg prioritet. Mål antalet noder anger antalet virtuella datorer som du vill ha i poolen.

Om du till exempel vill skapa en pool med virtuella Azure-datorer (i det här fallet virtuella Linux-datorer) med ett mål på 5 dedikerade virtuella datorer och 20 låg prioritets datorer:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

Du kan hämta det aktuella antalet noder för både dedikerade och låg prioritets datorer:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Noder i poolen har en egenskap som anger om noden är en dedikerad eller låg prioritets dator:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Virtuella datorer kan ibland vara avblockerade. När detta inträffar köas de aktiviteter som kördes på de virtuella datorerna i den tidigare noden och körs igen.

För konfigurations pooler för virtuella datorer gör batch också följande:

- De virtuella datorernas tillstånd har uppdaterats **till att** blockeras. 
- Den virtuella datorn tas bort effektivt, vilket leder till förlust av data som lagras lokalt på den virtuella datorn.
- En List Nodes-åtgärd i poolen kommer fortfarande att returnera de väntande noderna.
- Poolen försöker kontinuerligt nå mål antalet tillgängliga noder med låg prioritet. När ersättnings kapaciteten hittas behåller noderna sina ID: n, men de initieras på nytt, så att du kan **skapa** och **Starta** tillstånd innan de är tillgängliga för schemaläggning.
- Antalet avstängningen är tillgängliga som ett mått i Azure Portal.

## <a name="scale-pools-containing-low-priority-vms"></a>Skala pooler som innehåller virtuella datorer med låg prioritet

Precis som med pooler som bara består av dedikerade virtuella datorer, är det möjligt att skala en pool som innehåller låg prioritets datorer genom att anropa metoden för att ändra storlek eller genom att använda autoskalning.

Åtgärden ändra storlek på poolen tar en andra valfri parameter som uppdaterar värdet för **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Autoskalning-formeln för pooler stöder virtuella datorer med låg prioritet enligt följande:

- Du kan hämta eller ange värdet för den användardefinierade variabeln **$TargetLowPriorityNodes**.
- Du kan hämta värdet för den användardefinierade variabeln **$CurrentLowPriorityNodes**.
- Du kan hämta värdet för den användardefinierade variabeln **$PreemptedNodeCount**. Den här variabeln returnerar antalet noder i det äldre läget och gör att du kan skala upp eller ned antalet dedikerade noder, beroende på antalet nekade noder som inte är tillgängliga.

## <a name="configure-jobs-and-tasks"></a>Konfigurera jobb och uppgifter

Jobb och aktiviteter kräver lite ytterligare konfiguration för noder med låg prioritet. Tänk på följande:

- Egenskapen JobManagerTask för ett jobb har en **AllowLowPriorityNode** -egenskap. När den här egenskapen är true kan jobb hanterarens aktivitet schemaläggas antingen på en dedikerad eller låg prioritets nod. Om det är falskt, är jobb Manager-aktiviteten endast schemalagd till en särskild nod.
- Den AZ_BATCH_NODE_IS_DEDICATED [miljövariabeln](batch-compute-node-environment-variables.md) är tillgänglig för ett aktivitets program så att den kan avgöra om den körs på en låg prioritet eller på en dedikerad nod.

## <a name="view-metrics-for-low-priority-vms"></a>Visa mått för virtuella datorer med låg prioritet

Nya mått är tillgängliga i [Azure Portal](https://portal.azure.com) för noder med låg prioritet. Dessa mått är:

- Antal Low-Priority noder
- Antal Low-Priority kärnor
- Antal misslyckade noder

För att visa dessa mått i Azure Portal

1. Navigera till ditt Batch-konto i Azure Portal.
2. Välj **mått** från avsnittet **övervakning** .
3. Välj de mått som du vill använda i **mått** listan.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
- Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
- Börja planera flytten från virtuella datorer med låg prioritet till virtuella datorer med dekor prioritet. Om du använder virtuella datorer med låg prioritet med **moln tjänst konfigurations** pooler bör du planera att migrera till [ **konfigurationer för virtuell dator**](nodes-and-pools.md#configurations) i stället.
