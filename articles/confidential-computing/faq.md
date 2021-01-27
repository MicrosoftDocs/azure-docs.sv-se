---
title: Vanliga frågor och svar om Azure konfidentiell data behandling
description: Svar på vanliga frågor om Azures konfidentiella data behandling.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: 38bf12b46002e767bba50cf833637e2c8ace078f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98872340"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Vanliga frågor och svar om Azures konfidentiella data behandling

Den här artikeln innehåller svar på några av de vanligaste frågorna om att köra [konfidentiella arbets belastningar på virtuella Azure-datorer](overview.md).

Om ditt Azure-problem inte åtgärdas i den här artikeln kan du gå till Azure-forumen på [MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera ditt problem i dessa forum eller publicera på [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Du kan också skicka in en support förfrågan för Azure. Om du vill skicka en supportbegäran väljer du hämta support på [Sidan Support för Azure](https://azure.microsoft.com/support/options/).

## <a name="confidential-computing-virtual-machines"></a>Konfidentiell bearbetning Virtual Machines <a id="vm-faq"></a>

**Hur kan jag distribuera virtuella datorer i DCsv2-serien på Azure?**

Här följer några exempel på hur du kan distribuera en virtuell DCsv2-dator:
   - Använda en [Azure Resource Manager mall](../virtual-machines/windows/template-description.md)
   - Från [Azure Portal](https://portal.azure.com/#create/hub)
   - I Azure-tjänsten för [konfidentiella data behandling (virtuell dator)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) Marketplace. Med mallen Marketplace-lösning kan du begränsa en kund till de scenarier som stöds (regioner, avbildningar, tillgänglighet, disk kryptering). 

**Kommer alla OS-avbildningar att fungera med Azures konfidentiella data behandling?**

Nej. De virtuella datorerna kan bara distribueras på generation 2-operativsystem med Ubuntu Server 18,04, Ubuntu Server 16,04, Windows Server 2019 Data Center och Windows Server 2016 Data Center. Läs mer om generation 2 virtuella datorer i [Linux](../virtual-machines/generation-2.md) och [Windows](../virtual-machines/generation-2.md)

**DCsv2 virtuella datorer är nedtonade i portalen och jag kan inte välja en**

Utifrån informations bubblan bredvid den virtuella datorn finns det olika åtgärder att vidta:
   -    **UnsupportedGeneration**: ändra avbildningen av den virtuella dator avbildningen till "Gen2".
   -    **NotAvailableForSubscription**: regionen är inte tillgänglig ännu för din prenumeration. Välj en tillgänglig region.
   -    **InsufficientQuota**: [skapa en support förfrågan för att öka din kvot](../azure-portal/supportability/per-vm-quota-requests.md). De kostnads fria utvärderings prenumerationerna har ingen kvot för konfidentiella datorer. 

**DCsv2 virtuella datorer visas inte när jag försöker söka efter dem i väljaren för Portal storlek**

Kontrol lera att du har valt en [tillgänglig region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines). Kontrol lera också att du väljer "Rensa alla filter" i storleks väljaren. 

**Kan jag aktivera accelererat nätverk med Azures konfidentiella data behandling?**

 Nej. Accelererat nätverk stöds inte på DC-Series eller DCsv2-Series virtuella datorer. Det går inte att aktivera accelererat nätverk för en konfidentiell dator distribution eller Azure Kubernetes service Cluster-distribution som körs på konfidentiell dator användning.

**Kan jag använda Azures dedikerade värd med de här datorerna?**

Ja. Azure-dedikerad värd har stöd för virtuella datorer i DCsv2-serien. Den dedikerade Azure-värden tillhandahåller en fysisk server för enskild klient för att köra virtuella datorer på. Användarna använder vanligt vis Azure-dedikerad värd för att uppfylla kraven på efterlevnad av fysisk säkerhet, data integritet och övervakning. 

**Jag får ett fel meddelande om distribution av Azure Resource Manager mallar: "åtgärden kunde inte slutföras eftersom den resulterar i att den godkända standard DcsV2 Family Core-kvoten överskrids"**

[Skapa en support förfrågan för att öka din kvot](../azure-portal/supportability/per-vm-quota-requests.md). De kostnads fria utvärderings prenumerationerna har ingen kvot för konfidentiella datorer. 

**Vad är skillnaden mellan DCsv2-Series och DC-Series virtuella datorer?**

DC-Series virtuella datorer som körs på äldre 6-kärnors Intel-processorer med Intel SGX och har mindre total minne, mindre EPC-minne (enklaven Page cache) och är bara tillgängliga i två regioner (östra USA och Europa väst i Standard_DC2s och Standard_DC4s storlekar). Det finns inga planer på att göra dessa virtuella datorer allmänt tillgängliga och rekommenderas inte för produktions användning. Om du vill distribuera de virtuella datorerna använder du den  [konfidentiella beräknings DC-Series VM [Preview]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) Marketplace-instans.

**Är virtuella DCsv2-datorer tillgängliga globalt?**

Nej. För närvarande är de här virtuella datorerna bara tillgängliga i utvalda regioner. På [sidan produkter per region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) finns de senaste tillgängliga regionerna. 

**Är Hyper-Threading på de här datorerna?**

Hyper-Threading har inaktiverats för alla Azures kluster för konfidentiella data behandling.

**Hur gör jag för att installerar du Open enklaven SDK på de virtuella DCsv2-datorerna?**
   
Anvisningar om hur du installerar OE SDK på en Azure-dator eller en lokal dator finns i anvisningarna på [Öppna SDK-GitHub för enklaven](https://github.com/openenclave/openenclave).
     
Du kan också titta på den öppna enklaven SDK-GitHub för OS-användarspecifika installations anvisningar:
   - [Installera OE SDK i Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [Installera OE SDK på Ubuntu 18,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
   - [Installera OE SDK på Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)