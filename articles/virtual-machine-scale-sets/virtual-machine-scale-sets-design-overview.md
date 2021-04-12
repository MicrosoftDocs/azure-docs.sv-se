---
title: Design överväganden för Azure Virtual Machine Scale Sets
description: Lär dig mer om design överväganden för din Azure-Virtual Machine Scale Sets. Jämför skalnings uppsättnings funktioner med VM-funktioner.
keywords: virtuell Linux-dator, skalnings uppsättningar för virtuella datorer
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 04446243ed827cca4972a4b606c4930e74a2c704
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774974"
---
# <a name="design-considerations-for-scale-sets"></a>Design överväganden för skalnings uppsättningar
I den här artikeln beskrivs design överväganden för Virtual Machine Scale Sets. Information om vad Virtual Machine Scale Sets finns i [Översikt över Virtual Machine Scale Sets](./overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>När ska du använda skalnings uppsättningar i stället för virtuella datorer?
I allmänhet är skalnings uppsättningar användbara för att distribuera infrastruktur med hög tillgänglighet där en uppsättning datorer har liknande konfiguration. Vissa funktioner är dock bara tillgängliga i skalnings uppsättningar medan andra funktioner endast är tillgängliga i virtuella datorer. För att kunna fatta ett välgrundat beslut om när du ska använda varje teknik bör du först ta en titt på några av de vanliga funktionerna som är tillgängliga i skalnings uppsättningar, men inte virtuella datorer:

### <a name="scale-set-specific-features"></a>Funktioner för skalnings uppsättning

- När du har angett konfigurationen för skalnings uppsättningen kan du uppdatera egenskapen *Capacity* för att distribuera fler virtuella datorer parallellt. Den här processen är bättre än att skriva ett skript för att dirigera distribution av många enskilda virtuella datorer parallellt.
- Du kan [använda automatisk skalning i Azure för att automatiskt skala en skalnings uppsättning](./virtual-machine-scale-sets-autoscale-overview.md) men inte enskilda virtuella datorer.
- Du kan ändra [avbildningens skalnings uppsättnings virtuella datorer](/rest/api/compute/virtualmachinescalesets/reimage) men [inte enskilda virtuella datorer](/rest/api/compute/virtualmachines).
- Du kan [överetablera](#overprovisioning) skalnings uppsättningar för virtuella datorer för ökad tillförlitlighet och snabbare distributions tider. Du kan inte överetablera enskilda virtuella datorer om du inte skriver anpassad kod för att utföra den här åtgärden.
- Du kan ange en [uppgraderings princip](./virtual-machine-scale-sets-upgrade-scale-set.md) som gör det enkelt att distribuera uppgraderingar mellan virtuella datorer i din skalnings uppsättning. Med enskilda virtuella datorer måste du dirigera uppdateringar själv.

### <a name="vm-specific-features"></a>VM-/regionsspecifika funktioner

Vissa funktioner är för närvarande endast tillgängliga i virtuella datorer:

- Du kan avbilda en avbildning från en enskild virtuell dator, men inte från en virtuell dator i en skalnings uppsättning.
- Du kan migrera en enskild virtuell dator från inbyggda diskar till hanterade diskar, men du kan inte migrera virtuella dator instanser i en skalnings uppsättning.
- Du kan tilldela offentliga IPv6-IP-adresser till enskilda virtuella nätverkskort (NIC) för virtuella datorer, men det går inte att göra detta för virtuella dator instanser i en skalnings uppsättning. Du kan tilldela offentliga IPv6-IP-adresser till belastnings utjämning framför antingen enskilda virtuella datorer eller virtuella datorers skalnings uppsättningar.

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Skalnings uppsättningar med Azure Managed Disks
Skalnings uppsättningar kan skapas med [azure Managed disks](../virtual-machines/managed-disks-overview.md) istället för traditionella Azure Storage-konton. Managed Disks ger följande fördelar:
- Du behöver inte skapa en uppsättning Azure Storage-konton i förväg för de virtuella datorerna med skalnings uppsättningen.
- Du kan definiera [anslutna data diskar](virtual-machine-scale-sets-attached-disks.md) för de virtuella datorerna i din skalnings uppsättning.
- Skalnings uppsättningar kan konfigureras för att [stödja upp till 1 000 virtuella datorer i en mängd](virtual-machine-scale-sets-placement-groups.md). 

Om du har en befintlig mall kan du också [Uppdatera mallen så att den använder Managed disks](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Användar hanterad lagring
En skalnings uppsättning som inte har definierats med Azure Managed Disks förlitar sig på användardefinierade lagrings konton för att lagra de virtuella datorernas OS-diskar i uppsättningen. Förhållandet mellan 20 virtuella datorer per lagrings konto eller mindre rekommenderas för att uppnå maximal IO och även dra nytta av _överetablering_ (se nedan). Vi rekommenderar också att du sprider de inledande tecknen i lagrings konto namnen över alfabetet. Detta hjälper till att sprida belastningen mellan olika interna system. 


## <a name="overprovisioning"></a>Överetablering
Skalnings uppsättningar som är standard för att "överetablera" virtuella datorer. När den här inställningen är aktive rad, ökar skalnings uppsättningen i själva verket fler virtuella datorer än du bad om och tar sedan bort de extra virtuella datorerna när det begärda antalet virtuella datorer har etablerats. Överetablering förbättrar etableringen av lyckade kostnader och minskar distributions tiden. Du debiteras inte för de extra virtuella datorerna och de räknas inte över till dina kvot gränser.

Under överetablering kan du förbättra etableringen av lyckade kostnader, men det kan orsaka förvirrande beteende för ett program som inte är utformat för att hantera extra virtuella datorer som visas och sedan visas. Om du vill inaktivera överetablering ser du till att du har följande sträng i mallen: `"overprovision": "false"` . Mer information finns i [skalnings uppsättningen REST API-dokumentationen](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Om din skalnings uppsättning använder användar hanterad lagring och du inaktiverar överetablering, kan du ha fler än 20 virtuella datorer per lagrings konto, men vi rekommenderar inte att gå över 40 för prestanda orsaker i i/o. 

## <a name="limits"></a>Gränser
En skalnings uppsättning som bygger på en Marketplace-avbildning (kallas även plattforms avbildning) och som kon figurer ATS för att använda Azure Managed Disks har stöd för en kapacitet på upp till 1 000 virtuella datorer. Om du konfigurerar din skalnings uppsättning så att den stöder fler än 100 virtuella datorer fungerar inte alla scenarier (till exempel belastnings utjämning). Mer information finns i [arbeta med stora skalnings uppsättningar för virtuella datorer](virtual-machine-scale-sets-placement-groups.md). 

En skalnings uppsättning som kon figurer ATS med användar hanterade lagrings konton är för närvarande begränsad till 100 virtuella datorer (och 5 lagrings konton rekommenderas för den här skalan).

En skalnings uppsättning som bygger på en anpassad avbildning (en som skapats av dig) kan ha en kapacitet på upp till 600 virtuella datorer när den har kon figurer ATS med Azure Managed disks. Om skalnings uppsättningen har kon figurer ATS med användar hanterade lagrings konton måste den skapa alla OS-diskar för virtuella hård diskar i ett lagrings konto. Därför är det högsta rekommenderade antalet virtuella datorer i en skalnings uppsättning som bygger på en anpassad avbildning och ett användar hanterat lagrings utrymme 20. Om du inaktiverar överetablering kan du gå upp till 40.

För fler virtuella datorer än dessa gränser måste du distribuera flera skalnings uppsättningar som visas i [den här mallen](https://azure.microsoft.com/resources/templates/301-custom-images-at-scale/).
