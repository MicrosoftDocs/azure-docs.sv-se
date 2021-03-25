---
title: Planera distribution av Azure Service Fabric-kluster
description: Lär dig mer om att planera och förbereda för en produktions Service Fabric kluster distribution till Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 82521487b9a3e9438784e010a32cf6df8e7be2ef
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046325"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planera och förbereda för en kluster distribution

Det är mycket viktigt att planera och förbereda för distribution av produktions kluster.  Det finns många faktorer att tänka på.  Den här artikeln vägleder dig igenom stegen för att förbereda kluster distributionen.

## <a name="read-the-best-practices-information"></a>Läs informationen om bästa praxis
För att kunna hantera Azure Service Fabric-program och-kluster, finns det åtgärder som vi rekommenderar för att optimera tillförlitligheten i produktions miljön.  Mer information finns i [metod tips för Service Fabric program och kluster](./service-fabric-best-practices-security.md).

## <a name="select-the-os-for-the-cluster"></a>Välj operativ system för klustret
Service Fabric gör det möjligt att skapa Service Fabric-kluster på alla virtuella datorer eller datorer som kör Windows Server eller Linux.  Innan du distribuerar klustret måste du välja operativ system: Windows eller Linux.  Varje nod (virtuell dator) i klustret kör samma OS, men du kan inte blanda virtuella Windows-och Linux-datorer i samma kluster.

## <a name="capacity-planning"></a>Kapacitetsplanering
Vid distribution till en produktionsmiljö är det viktigt med kapacitetsplanering. Här är några saker att tänka på i samband med den här processen.

* Det första antalet nodtyper för klustret 
* Egenskaperna för varje nodtyp (storlek, antal instanser, primär, Internet som är riktad, antal virtuella datorer osv.)
* Klustrets egenskaper för tillförlitlighet och hållbarhet

### <a name="select-the-initial-number-of-node-types"></a>Välj det första antalet nodtyper
Först måste du ta reda på vad klustret som du skapar ska användas för. Vilka typer av program som du planerar att distribuera till det här klustret? Har ditt program flera tjänster och behöver de vara offentliga eller Internet riktade? Har dina tjänster (som utgör ditt program) olika infrastruktur behov, till exempel större RAM-minne eller högre CPU-cykler? Ett Service Fabric kluster kan bestå av fler än en nodtyp: en primär nodtyp och en eller flera typer som inte är av en primär typ. Varje nodtyp mappas till en skalnings uppsättning för virtuella datorer. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. [Nodens egenskaper och placerings begränsningar][placementconstraints] kan konfigureras för att begränsa vissa tjänster till vissa nodtyper.  Mer information finns i [Service Fabric kluster kapacitets planering](service-fabric-cluster-capacity.md).

### <a name="select-node-properties-for-each-node-type"></a>Välj Node-egenskaper för varje nodtyp
Node types definierar VM SKU, antal och egenskaper för de virtuella datorerna i den associerade skalnings uppsättningen.

Minimi storleken på virtuella datorer för varje nodtyp bestäms av den [hållbarhets nivå][durability] som du väljer för nodtypen.

Det minsta antalet virtuella datorer för den primära nodtypen avgörs av den [Tillförlitlighets nivå][reliability] som du väljer.

Se de lägsta rekommendationerna för [primära nodtyper](service-fabric-cluster-capacity.md#primary-node-type), [tillstånds känsliga arbets belastningar på icke-primära nodtyper](service-fabric-cluster-capacity.md#stateful-workloads)och [tillstånds lösa arbets belastningar på icke-primära nodtyper](service-fabric-cluster-capacity.md#stateless-workloads).

Fler än det minsta antalet noder bör baseras på antalet repliker av de program/tjänster som du vill köra i den här nodtypen.  Med [kapacitets planering för Service Fabric program](service-fabric-capacity-planning.md) kan du beräkna vilka resurser du behöver för att köra dina program. Du kan alltid skala klustret uppåt eller nedåt senare för att justera för att ändra program arbets belastning. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Använd tillfälliga OS-diskar för skalnings uppsättningar för virtuella datorer

*Tillfälliga OS-diskar* har skapats på den lokala virtuella datorn (VM) och sparas inte på fjärr Azure Storage. De rekommenderas för alla Service Fabric Node-typer (primär och sekundär), på grund av traditionella beständiga OS-diskar, tillfälliga OS-diskar:

* Minska svars tiden för Läs/skriv till OS-disk
* Aktivera snabbare återställnings-och avbildnings hanterings åtgärder
* Minska totalkostnaden (diskarna är kostnads fria och debiteras inga ytterligare lagrings kostnader)

Tillfälliga OS-diskar är inte en speciell Service Fabric funktion, utan i stället en funktion i de *skalnings uppsättningar för virtuella* Azure-datorer som mappas till Service Fabric Node-typer. Om du använder dem med Service Fabric krävs följande i klustrets Azure Resource Manager mall:

1. Se till att Node-typerna har [stöd för Azure VM-storlekar som stöds](../virtual-machines/ephemeral-os-disks.md) för tillfälliga OS-diskar och att storleken på den virtuella datorn har tillräckligt med cachestorlek för att stöda storleken på operativ system disken (se *Obs!* ) Exempel:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Se till att välja en VM-storlek med en cachestorlek som är lika stor som eller större än storleken på den virtuella datorns OS-disk, annars kan din Azure-distribution leda till fel (även om den först accepteras).

2. Ange en version av en skalnings uppsättning för virtuella datorer ( `vmssApiVersion` ) av `2018-06-01` eller senare:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. I avsnittet skalnings uppsättning för virtuell dator i distributions mal len anger `Local` du alternativ för `diffDiskSettings` :

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

> [!NOTE]
> Användar program får inte ha några beroenden/filer/artefakter på OS-disken eftersom operativ system disken skulle förloras vid en uppgradering av operativ systemet.

> [!NOTE]
> Det går inte att uppgradera befintliga icke-tillfälliga VMSS på plats för att använda tillfälliga diskar.
> Om du vill migrera måste användarna [lägga till](./virtual-machine-scale-set-scale-node-type-scale-out.md) en ny NodeType med tillfälliga diskar, flytta arbets belastningarna till den nya nodeType-& [ta bort](./service-fabric-how-to-remove-node-type.md) den befintliga NodeType.
>

Mer information och ytterligare konfigurations alternativ finns i [tillfälliga OS-diskar för virtuella Azure-datorer](../virtual-machines/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Välj nivåer för hållbarhet och pålitlighet för klustret
Hållbarhets nivån används för att ange systemet de privilegier som dina virtuella datorer har med den underliggande Azure-infrastrukturen. I den primära nodtypen kan du med den här behörigheten Service Fabric pausa alla infrastruktur förfrågningar för virtuella datorer (till exempel en VM-omstart, återställning av virtuella datorer eller migrering av virtuella datorer) som påverkar kraven på kvorum för system tjänsterna och dina tillstånds känsliga tjänster. I de icke-primära nodtypen kan den här behörigheten Service Fabric pausa alla infrastruktur begär Anden för virtuella datorer (t. ex. omstart av virtuell dator, avbildning av virtuell dator och migrering av virtuella datorer) som påverkar kraven för de tillstånds känsliga tjänsterna.  För fördelarna med de olika nivåerna och rekommendationerna på vilken nivå som ska användas och när, se behållar [egenskaperna för klustret][durability].

Tillförlitlighets nivån används för att ange antalet repliker av de system tjänster som du vill köra i det här klustret på den primära nodtypen. Antalet repliker, desto mer pålitliga system tjänsterna finns i klustret.  För fördelarna med de olika nivåerna och rekommendationerna på vilken nivå som ska användas och när, se [Tillförlitlighets egenskaperna för klustret][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Aktivera omvänd proxy och/eller DNS
Tjänster som ansluter till varandra i ett kluster kan i allmänhet direkt komma åt slut punkterna för andra tjänster eftersom noderna i ett kluster finns i samma lokala nätverk. För att göra det enklare att ansluta mellan tjänster Service Fabric tillhandahåller ytterligare tjänster: en [DNS-tjänst](service-fabric-dnsservice.md) och en [omvänd proxy-tjänst](service-fabric-reverseproxy.md).  Båda tjänsterna kan aktive ras när du distribuerar ett kluster.

Eftersom många tjänster, särskilt behållar tjänster, kan ha ett befintligt URL-namn, kan det vara bra att lösa dessa med standard-DNS-protokollet (i stället för Naming Service-protokollet) är bekvämt, särskilt i program "lyft och Shift"-scenarier. Detta är exakt vad DNS-tjänsten gör. Du kan mappa DNS-namn till ett tjänst namn och därmed matcha slut punktens IP-adresser.

Den omvända proxyn adresser tjänster i klustret som exponerar HTTP-slutpunkter (inklusive HTTPS). Den omvända proxyn fören klar att anropa andra tjänster avsevärt genom att tillhandahålla ett angivet URI-format.  Den omvända proxyn hanterar även de åtgärder för att lösa, ansluta och testa om som krävs för att en tjänst ska kunna kommunicera med en annan.

## <a name="prepare-for-disaster-recovery"></a>Förbereda för haveriberedskap
En viktig del av att leverera hög tillgänglighet säkerställer att tjänsterna kan överleva alla olika typer av problem. Detta är särskilt viktigt för problem som är oplanerade och utanför din kontroll. [För att förbereda för haveri beredskap](service-fabric-disaster-recovery.md) beskrivs några vanliga fel lägen som kan vara katastrofer om de inte modelleras och hanteras korrekt. Den diskuterar också åtgärder och åtgärder som ska vidtas om en olycka ändå har skett.

## <a name="production-readiness-checklist"></a>Checklista för produktionsberedskap
Är ditt program och kluster redo att ta produktions trafik? Innan du distribuerar klustret till produktion kan du köra genom [Check listan för produktions beredskap](service-fabric-production-readiness-checklist.md). Se till att ditt program och kluster fungerar smidigt genom att gå igenom objekten i den här check listan. Vi rekommenderar starkt att alla dessa objekt checkas ut innan du börjar arbeta med produktionen.

## <a name="next-steps"></a>Nästa steg
* [Skapa ett Service Fabric kluster som kör Windows](./service-fabric-best-practices-security.md)
* [Skapa ett Service Fabric kluster som kör Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster