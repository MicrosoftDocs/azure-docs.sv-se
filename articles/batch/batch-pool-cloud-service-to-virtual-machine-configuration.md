---
title: Migrera konfigurationen av batch-poolen från Cloud Services till Virtual Machines
description: Lär dig hur du uppdaterar din konfiguration för poolen till den senaste och rekommenderade konfigurationen
ms.topic: how-to
ms.date: 1/6/2021
ms.openlocfilehash: d987a185efb6593fd541dd14fa74b6c4d3ca41be
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234315"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Migrera konfigurationen av batch-poolen från Cloud Services till Virtual Machines

Batch-pooler kan skapas med antingen [cloudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) eller [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration). ' virtualMachineConfiguration ' är den rekommenderade konfigurationen eftersom den stöder alla batch-funktioner. cloudServiceConfiguration-pooler stöder inte alla funktioner och inga nya funktioner planeras.

Om du använder cloudServiceConfiguration-pooler rekommenderar vi starkt att du flyttar till Använd virtualMachineConfiguration-pooler. Detta gör att du kan dra nytta av alla batch-funktioner, till exempel ett expanderat [urval av VM-serien](batch-pool-vm-sizes.md), virtuella Linux-datorer, [behållare](batch-docker-container-workloads.md), [Azure Resource Manager virtuella nätverk](batch-virtual-network.md)och [disk kryptering för noder](disk-encryption.md).

I den här artikeln beskrivs hur du migrerar till ' virtualMachineConfiguration '.

## <a name="new-pools-are-required"></a>Nya pooler krävs

Det går inte att uppdatera befintliga aktiva pooler från ' cloudServiceConfiguration ' till ' virtualMachineConfiguration ', nya pooler måste skapas. Att skapa pooler med hjälp av "virtualMachineConfiguration" stöds av alla batch-API: er, kommando rads verktyg, Azure Portal och Batch Explorer gränssnittet.

**I självstudierna [.net](tutorial-parallel-dotnet.md) och [python](tutorial-parallel-python.md) finns exempel på hur du skapar en pool med hjälp av "virtualMachineConfiguration".**

## <a name="pool-configuration-differences"></a>Skillnader i konfiguration av pool

Följande bör övervägas när konfigurationen för poolen uppdateras:

- cloudServiceConfiguration för pooler är alltid Windows OS, virtualMachineConfiguration-pooler kan vara Linux eller Windows OS.
- Jämfört med "cloudServiceConfiguration"-pooler har virtualMachineConfiguration-pooler en mer omfattande uppsättning funktioner, till exempel container support, data diskar och disk kryptering.
- virtualMachineConfiguration i pooler använder hanterade OS-diskar. Vilken [typ av hanterad disk](../virtual-machines/disks-types.md) som används för varje nod beror på vilken VM-storlek som valts för poolen. Om storleken på den virtuella datorn har angetts för poolen, till exempel Standard_D2s_v3, så används Premium SSD. Om en icke-s VM-storlek anges, till exempel Standard_D2_v3, används en standard-HDD.

   > [!IMPORTANT]
   > Precis som med Virtual Machines och Virtual Machine Scale Sets innebär den OS-hanterade disk som används för varje nod en kostnad, vilket är ytterligare kostnader för de virtuella datorerna. Det finns ingen disk kostnad för OS för "cloudServiceConfiguration"-noder eftersom operativ system disken skapas på de lokala SSD-noderna.

- Start-och borttagnings tider för pooler och nod kan skilja sig något mellan "cloudServiceConfiguration"-pooler och "virtualMachineConfiguration"-pooler.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [konfigurationer av pooler](nodes-and-pools.md#configurations).
- Läs mer om [metod tips för pool](best-practices.md#pools).
- REST API referens för [addition](https://docs.microsoft.com/rest/api/batchservice/pool/add) och [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)för pooler.
