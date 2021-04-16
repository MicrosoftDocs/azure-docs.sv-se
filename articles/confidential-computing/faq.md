---
title: Vanliga frågor och svar om konfidentiell databehandling i Azure
description: Svar på vanliga frågor om konfidentiell databehandling i Azure.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: 05c98102109d1925eb78d4558faf62b366801e77
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538986"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Vanliga frågor och svar om konfidentiell databehandling i Azure

Den här artikeln innehåller svar på några av de vanligaste frågorna om att köra [arbetsbelastningar för konfidentiell databehandling på virtuella Azure-datorer.](overview.md)

Om ditt Azure-problem inte åtgärdas i den här artikeln går du till Azure-forumen [på MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera problemet i dessa forum eller publicera det [ @AzureSupport på Twitter.](https://twitter.com/AzureSupport) Du kan också skicka en Azure-supportbegäran. Om du vill skicka en supportbegäran går du [till Azure-supportsidan](https://azure.microsoft.com/support/options/)och väljer Få support.

## <a name="confidential-computing-virtual-machines"></a>Konfidentiell databehandling Virtual Machines <a id="vm-faq"></a>

**Hur distribuerar jag virtuella datorer i DCsv2-serien på Azure?**

Här är några exempel på hur du kan distribuera en virtuell dator med DCsv2:
   - Använda en [Azure Resource Manager mall](../virtual-machines/windows/template-description.md)
   - Från [Azure Portal](https://portal.azure.com/#create/hub)
   - I [azures lösningsmall för konfidentiell databehandling (virtuell dator).](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) Marketplace-lösningsmallen hjälper till att begränsa en kund till de scenarier som stöds (regioner, avbildningar, tillgänglighet, diskkryptering). 

**Kommer alla OS-avbildningar att fungera med konfidentiell databehandling i Azure?**

Nej. De virtuella datorerna kan bara distribueras på andra generationens operativsystem med Ubuntu Server 18.04, Ubuntu Server 16.04, Windows Server 2019 Datacenter och Windows Server 2016 Datacenter. Läs mer om virtuella Gen 2-datorer på [Linux](../virtual-machines/generation-2.md) och [Windows](../virtual-machines/generation-2.md)

**Virtuella DCsv2-datorer är nedtonade i portalen och jag kan inte välja en**

Baserat på informationsbubblan bredvid den virtuella datorn finns det olika åtgärder att vidta:
   -    **UnsupportedGeneration**(Generation stöds inte): Ändra genereringen av avbildningen av den virtuella datorn till "Gen2".
   -    **NotAvailableForSubscription:** Regionen är ännu inte tillgänglig för din prenumeration. Välj en tillgänglig region.
   -    **InsufficientQuota:** [Skapa en supportbegäran för att öka kvoten.](../azure-portal/supportability/per-vm-quota-requests.md) Prenumerationer på kostnadsfria utvärderingsversion har ingen kvot för virtuella datorer för konfidentiell databehandling. 

**Virtuella DCsv2-datorer visas inte när jag försöker söka efter dem i portalens storleksväljare**

Kontrollera att du har valt en [tillgänglig region.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) Se också till att du väljer "rensa alla filter" i storleksväljaren. 

**Kan jag aktivera accelererat nätverk med konfidentiell databehandling i Azure?**

 Nej. Accelererat nätverk stöds inte på DC-Series eller DCsv2-Series virtuella datorer. Accelererat nätverk kan inte aktiveras för distribution av konfidentiella virtuella datorer för databehandling eller för Azure Kubernetes Service klusterdistribution som körs på konfidentiell databehandling.

**Kan jag använda Azure Dedicated Host med dessa datorer?**

Ja. Azure Dedicated Host stöd för virtuella datorer i DCsv2-serien. Azure Dedicated Host en fysisk server med en enda klient för att köra dina virtuella datorer på. Användare använder vanligtvis Azure Dedicated Host för att uppfylla efterlevnadskrav gällande fysisk säkerhet, dataintegritet och övervakning. 

**Jag får ett Azure Resource Manager fel vid malldistribution: "Åtgärden kunde inte slutföras eftersom den resulterar i att den godkända kvoten för DcsV2-familjens kärnor överskrids"**

[Skapa en supportbegäran för att öka din kvot.](../azure-portal/supportability/per-vm-quota-requests.md) Prenumerationer på kostnadsfria utvärderingsversion har ingen kvot för virtuella datorer för konfidentiell databehandling. 

**Vad är skillnaden mellan virtuella DCsv2-Series och DC-Series datorer?**

DC-Series virtuella datorer körs på äldre Intel-processorer med 6 kärnor med Intel SGX och har mindre totalt minne, mindre EPC-minne (Enclave Page Cache) och är endast tillgängliga i två regioner (USA, östra och Europa, västra i Standard_DC2s- och Standard_DC4s-storlekar). Det finns inga planer på att göra dessa virtuella datorer allmänt tillgängliga och de rekommenderas inte för produktionsanvändning. Om du vill distribuera de här virtuella datorerna använder [du Marketplace-instansen DC-Series Konfidentiell beräkning och virtuell dator [förhandsversion].](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview)

**Är csv2 virtuella datorer tillgängliga globalt?**

Nej. För närvarande är dessa virtuella datorer endast tillgängliga i utvalda regioner. De senaste [tillgängliga regionerna finns på](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) produktsidan efter region. 

**Är hypertrådning AV på de här datorerna?**

Flertrådsteknik är inaktiverad för alla azure-kluster för konfidentiell databehandling.

**Hur gör jag för att du installera Open Enclave SDK på de virtuella DCsv2-datorerna?**
   
Om du vill ha instruktioner om hur du installerar OE SDK på en Azure-dator eller en lokal dator följer du anvisningarna på GitHub för [Open Enclave SDK.](https://github.com/openenclave/openenclave)
     
Du kan också titta på GitHub-installationsinstruktioner för Open Enclave SDK för OS-specifika anvisningar:
   - [Installera OE SDK på Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [Installera OE SDK på Ubuntu 18.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)