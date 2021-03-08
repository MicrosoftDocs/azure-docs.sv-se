---
title: Välj VM-storlekar och avbildningar för pooler
description: Hur du väljer mellan tillgängliga VM-storlekar och OS-versioner för datornoderna i Azure Batch pooler
ms.topic: conceptual
ms.date: 03/08/2021
ms.custom: seodec18
ms.openlocfilehash: 42b8743fac6a6c64e98271490f0bfc4671fa7698
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455203"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>Välj en VM-storlek och avbildning för Compute-noder i en Azure Batch pool

När du väljer en Node-storlek för en Azure Batch pool kan du välja bland nästan alla VM-storlekar som är tillgängliga i Azure. Azure erbjuder ett antal storlekar för virtuella Linux-och Windows-datorer för olika arbets belastningar.

## <a name="supported-vm-series-and-sizes"></a>VM-serien och storlekar som stöds

Det finns några undantag och begränsningar för att välja storlek på en virtuell dator för batch-poolen:

- Vissa VM-serier eller VM-storlekar stöds inte i batch.
- Vissa VM-storlekar är begränsade och måste vara särskilt aktiverade innan de kan allokeras.

### <a name="pools-in-virtual-machine-configuration"></a>Pooler i konfiguration av virtuell dator

Batch-pooler i den virtuella dator konfigurationen stöder nästan alla [VM-storlekar](../virtual-machines/sizes.md). I följande tabell finns mer information om storlekar och begränsningar som stöds.

| VM-serie  | Storlekar som stöds |
|------------|---------|
| Basic A | Alla storlekar *utom* Basic_A0 (a0) |
| A | Alla storlekar *utom* Standard_A0, Standard_A8, Standard_A9, Standard_A10, Standard_A11 |
| AV2 | Alla storlekar |
| B | Stöds inte |
| DC | Stöds inte |
| Dv2, DSv2 | Alla storlekar |
| Dv3, Dsv3 | Alla storlekar |
| Dav4, Dasv4 | Alla storlekar |
| Ddv4, Ddsv4 |  Alla storlekar |
| Dv4, Dsv4 | Stöds inte |
| Ev3, Esv3 | Alla storlekar, förutom E64is_v3 |
| Eav4, Easv4 | Alla storlekar |
| Edv4, Edsv4 |  Alla storlekar |
| Ev4, Esv4 | Stöds inte |
| F, FS | Alla storlekar |
| Fsv2 | Alla storlekar |
| G, GS | Alla storlekar |
| H | Alla storlekar |
| HB | Alla storlekar |
| HBv2 | Alla storlekar |
| HC | Alla storlekar |
| Ls | Alla storlekar |
| Lsv2 | Alla storlekar |
| M | Alla storlekar |
| Mv2<sup>1</sup> | Alla storlekar |
| NC | Alla storlekar |
| NCv2 | Alla storlekar |
| NCv3 | Alla storlekar |
| NCasT4_v3 | Alla storlekar |
| ND | Alla storlekar |
| NDv2 | Ingen – ännu inte tillgänglig |
| NV | Alla storlekar |
| NVv3 | Alla storlekar |
| NVv4 | Alla storlekar |
| SAP HANA | Stöds inte |

<sup>1</sup> dessa VM-serien kan bara användas med virtuella datorer i generation 2.

### <a name="using-generation-2-vm-images"></a>Använda virtuella dator avbildningar i generation 2

Vissa VM-serier, till exempel [Mv2](../virtual-machines/mv2-series.md), kan bara användas med [virtuella datorer i generation 2](../virtual-machines/generation-2.md). Generation 2 VM-avbildningar anges som alla VM-avbildningar med hjälp av egenskapen SKU för [imageReference](/rest/api/batchservice/pool/add#imagereference) -konfigurationen. "SKU"-strängarna har ett suffix som "-G2" eller "-Gen2". Om du vill hämta en lista över virtuella dator avbildningar som stöds av batch, inklusive generation 2-avbildningar, använder du ["lista över stödda bilder"](/rest/api/batchservice/account/listsupportedimages) API, [POWERSHELL](/powershell/module/az.batch/get-azbatchsupportedimage)eller [Azure CLI](/cli/azure/batch/pool/supported-images).

### <a name="pools-in-cloud-service-configuration"></a>Pooler i moln tjänst konfiguration

Batch-pooler i moln tjänst konfigurationen har stöd [för alla VM-storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **utom** följande:

| VM-serie  | Storlekar som inte stöds |
|------------|-------------------|
| A-serien   | Extra liten       |
| Av2-serien | Standard_A1_v2 Standard_A2_v2 Standard_A2m_v2 |

## <a name="size-considerations"></a>Överväganden gällande storlek

- **Program krav** – beakta egenskaperna och kraven för det program som ska köras på noderna. Det är lämpligt att ha i åtanke när du väljer den lämpligaste och mest kostnadseffektiva nodstorleken huruvida programmet är flertrådat och hur mycket minne det förbrukar. För [MPI arbets belastningar](batch-mpi.md) eller CUDA-program med flera instanser bör du överväga specialiserade [HPC](../virtual-machines/sizes-hpc.md) [-eller GPU-aktiverade VM-](../virtual-machines/sizes-gpu.md) storlekar. Mer information finns i [använda RDMA-kompatibla eller GPU-aktiverade instanser i batch-pooler](batch-pool-compute-intensive-sizes.md).

- **Aktiviteter per nod** – det är vanligt att välja en Node-storlek förutsatt att en aktivitet körs på en nod i taget. Det kan dock vara fördelaktigt att ha flera aktiviteter (och därför flera program instanser) [som körs parallellt](batch-parallel-node-tasks.md) på datornoderna under jobb körningen. I det här fallet är det vanligt att välja en storlek för flera kärnor för att hantera den ökade efter frågan av parallell körning.

- **Belastnings nivåer för olika aktiviteter** -alla noder i en pool har samma storlek. Om du avser att köra program med olika systemkrav och/eller belastningsnivåer rekommenderar vi att du använder olika pooler.

- **Region tillgänglighet** – en VM-serie eller storlek kanske inte är tillgänglig i de regioner där du skapar dina batch-konton. Information om hur du kontrollerar att en storlek är tillgänglig finns i [produkt tillgänglighet per region](https://azure.microsoft.com/regions/services/).

- **Kvoter** - [kärnor](batch-quota-limit.md#resource-quotas) i batch-kontot kan begränsa antalet noder i en särskild storlek som du kan lägga till i en batch-pool. Vid behov kan du [begära en kvot ökning](batch-quota-limit.md#increase-a-quota).

- **Konfiguration av pool** – i allmänhet har du fler alternativ för VM-storlek när du skapar en pool i konfigurationen för den virtuella datorn, jämfört med moln tjänst konfigurationen.

## <a name="supported-vm-images"></a>VM-avbildningar som stöds

Använd något av följande API: er för att returnera en lista med virtuella Windows-och Linux VM-avbildningar som stöds av batch, inklusive Node-agentens SKU-ID: n för varje avbildning:

- Batch-tjänst REST API: [lista över avbildningar som stöds](/rest/api/batchservice/account/listsupportedimages)
- PowerShell: [Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- Azure CLI: [AZ batch pool som stöds – avbildningar](/cli/azure/batch/pool/supported-images)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
- Information om hur du använder beräknings intensiva VM-storlekar finns i [använda RDMA-kompatibla eller GPU-aktiverade instanser i batch-pooler](batch-pool-compute-intensive-sizes.md).
