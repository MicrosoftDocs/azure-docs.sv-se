---
title: Migrera konfigurationen av batch-poolen från Cloud Services till Virtual Machines
description: Lär dig hur du uppdaterar din konfiguration för poolen till den senaste och rekommenderade konfigurationen
ms.topic: how-to
ms.date: 2/16/2021
ms.openlocfilehash: 9cbcf3864526bd8f8132f3b0f729e2d728e07bb8
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546048"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Migrera konfigurationen av batch-poolen från Cloud Services till Virtual Machines

Batch-pooler kan skapas med antingen [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) eller [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration). ' virtualMachineConfiguration ' är den rekommenderade konfigurationen eftersom den stöder alla batch-funktioner. cloudServiceConfiguration-pooler stöder inte alla funktioner och inga nya funktioner planeras.

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

## <a name="azure-data-factory-custom-activity-pools"></a>Azure Data Factory anpassade aktivitets grupper

Azure Batch pooler kan användas för att köra Data Factory anpassade aktiviteter. Alla ' cloudServiceConfiguration '-pooler som används för att köra anpassade aktiviteter måste tas bort och nya ' virtualMachineConfiguration '-pooler skapas.

- Pipelines bör pausas innan borttagning/återskapas för att säkerställa att inga körningar avbryts.
- Samma pool-ID kan användas för att undvika konfigurations ändringar för länkad tjänst.
- Återuppta pipelines när nya pooler har skapats.

Mer information om hur du använder Azure Batch för att köra Data Factory anpassade aktiviteter:

- [Azure Batch länkad tjänst](../data-factory/compute-linked-services.md#azure-batch-linked-service)
- [Anpassade aktiviteter i en Data Factory pipeline](../data-factory/transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [konfigurationer av pooler](nodes-and-pools.md#configurations).
- Läs mer om [metod tips för pool](best-practices.md#pools).
- REST API referens för [addition](/rest/api/batchservice/pool/add) och [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration)för pooler.