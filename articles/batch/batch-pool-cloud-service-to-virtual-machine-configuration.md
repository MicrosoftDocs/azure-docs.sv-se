---
title: Migrera konfigurationen av batch-poolen från Cloud Services till Virtual Machines
description: Lär dig hur du uppdaterar din konfiguration för poolen till den senaste och rekommenderade konfigurationen
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: a176c4df1737a340a546b4ab7926447cd821350d
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200566"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>Migrera konfiguration av batch-pool från Cloud Services till virtuell dator

För närvarande kan batch-pooler skapas med antingen [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) eller [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration). Vi rekommenderar att du endast använder konfiguration av virtuella datorer, eftersom den här konfigurationen stöder alla batch-funktioner.

Cloud Services konfigurations-pooler stöder inte några av de aktuella batch-funktionerna och har inte stöd för nya funktioner som har lagts till. Du kommer inte att kunna skapa nya CloudServiceConfiguration-pooler eller lägga till nya noder i befintliga pooler [efter den 29 februari 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/).

Om batch-lösningarna för närvarande använder "cloudServiceConfiguration"-pooler rekommenderar vi att du ändrar till "virtualMachineConfiguration" så snart som möjligt. Detta gör att du kan dra nytta av alla batch-funktioner, till exempel ett expanderat [urval av VM-serien](batch-pool-vm-sizes.md), virtuella Linux-datorer, [behållare](batch-docker-container-workloads.md), [Azure Resource Manager virtuella nätverk](batch-virtual-network.md)och [disk kryptering för noder](disk-encryption.md).

## <a name="create-a-pool-using-virtual-machine-configuration"></a>Skapa en pool med konfiguration av virtuell dator

Det går inte att växla en befintlig aktiv pool som använder ' cloudServiceConfiguration ' för att använda ' virtualMachineConfiguration '. I stället måste du skapa nya pooler. När du har skapat dina nya "virtualMachineConfiguration"-pooler och replikerat alla dina jobb och aktiviteter kan du ta bort den gamla cloudServiceConfiguration'pools som du inte längre använder.

Alla batch-API: er, kommando rads verktyg, Azure Portal och användar gränssnittet Batch Explorer låter dig skapa pooler med "virtualMachineConfiguration".

En genom gång av processen för att skapa pooler som använder "virtualMachineConfiguration, finns i själv studie kursen för [.net](tutorial-parallel-dotnet.md) eller [python](tutorial-parallel-python.md).

## <a name="pool-configuration-differences"></a>Skillnader i konfiguration av pool

Några av de viktigaste skillnaderna mellan de två konfigurationerna är:

- cloudServiceConfiguration för pooler använder bara Windows OS. virtualMachineConfiguration-pooler kan använda antingen Linux eller Windows OS.
- Jämfört med "cloudServiceConfiguration"-pooler har virtualMachineConfiguration-pooler en mer omfattande uppsättning funktioner, till exempel container support, data diskar och disk kryptering.
- Start-och borttagnings tider för pooler och nod kan skilja sig något mellan "cloudServiceConfiguration"-pooler och "virtualMachineConfiguration"-pooler.
- virtualMachineConfiguration i pooler använder hanterade OS-diskar. Vilken [typ av hanterad disk](../virtual-machines/disks-types.md) som används för varje nod beror på vilken VM-storlek som valts för poolen. Om storleken på den virtuella datorn har angetts för poolen, till exempel Standard_D2s_v3, så används Premium SSD. Om en icke-s VM-storlek anges, till exempel Standard_D2_v3, används en standard-HDD.

   > [!IMPORTANT]
   > Precis som med Virtual Machines och Virtual Machine Scale Sets innebär den OS-hanterade disk som används för varje nod en kostnad, vilket är ytterligare kostnader för de virtuella datorerna. Det finns ingen disk kostnad för OS för "cloudServiceConfiguration"-noder eftersom operativ system disken skapas på noderna lokalt SSD.

## <a name="azure-data-factory-custom-activity-pools"></a>Azure Data Factory anpassade aktivitets grupper

Azure Batch pooler kan användas för att köra Data Factory anpassade aktiviteter. Alla ' cloudServiceConfiguration '-pooler som används för att köra anpassade aktiviteter måste tas bort och nya ' virtualMachineConfiguration '-pooler skapas.

När du skapar dina nya pooler för att köra Data Factory anpassade aktiviteter, följer du dessa metoder:

- Pausa alla pipelines innan du skapar nya pooler och tar bort de gamla för att se till att inga körningar avbryts.
- Samma pool-ID kan användas för att undvika konfigurations ändringar för länkad tjänst.
- Återuppta pipelines när nya pooler har skapats.

Mer information om hur du använder Azure Batch för att köra Data Factory anpassade aktiviteter finns i [Azure Batch länkade tjänster](../data-factory/compute-linked-services.md#azure-batch-linked-service) och  [anpassade aktiviteter i en Data Factory pipeline](../data-factory/transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [konfigurationer av pooler](nodes-and-pools.md#configurations).
- Läs mer om [metod tips för pool](best-practices.md#pools).
- Se REST API referens för addition och [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration)för [pooler](/rest/api/batchservice/pool/add) .