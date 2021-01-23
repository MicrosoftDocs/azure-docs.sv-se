---
title: Tillgängliga storlekar för Azure Cloud Services (utökad support)
description: Tillgängliga storlekar för Azure Cloud Services-distributioner (utökad support)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: c3515b559ef647b9a043a04282b4edfc6e7fa9be
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744656"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Tillgängliga storlekar för Azure Cloud Services (utökad support)

I den här artikeln beskrivs tillgängliga storlekar för virtuella datorer för Cloud Services (utökade support) instanser.   

| SKU-familj |  ACU/Core | 
|---|---|
| [A5-7](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series)| 100 |
|[A8-A11](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series---compute-intensive-instances) | 225* |
|[AV2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 100 | 
|[Styr](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#d-series) | 160 | 
|[Dv2](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series) | 160 – 190 * |
|[Dv3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) | 160 – 190 * |
|[Ev3](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) | 160 – 190 *
|[Projektredovisnings](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#g-series) | 180-240 * |
|[&](https://docs.microsoft.com/azure/virtual-machines/h-series) | 290 - 300* | 

>[!NOTE]
> ACU:er som visas med * använder Intel® Turbo-teknik för att öka processorfrekvensen och prestanda. Prestandaökningens storlek kan variera beroende på storleken på den virtuella datorn, arbetsbelastningen och andra arbetsbelastningar som körs på samma värd.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Konfigurera storlekar för Cloud Services (utökad support)

Du kan ange den virtuella datorns storlek för en roll instans som en del av tjänst modellen i tjänst definitions filen. Storleken på rollen bestämmer antalet processor kärnor, minnes kapacitet och den lokala fil system storleken.

Du kan till exempel ställa in instans storleken för webb rollen på `Standard_D2` : 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>Ändra storleken på en befintlig roll

Ändra storleken på en befintlig roll genom att ändra storleken på den virtuella datorn i tjänst definitions filen (csdef), paketera om moln tjänsten och distribuera den igen. 

## <a name="get-a-list-of-available-sizes"></a>Hämta en lista över tillgängliga storlekar 

Om du vill hämta en lista över tillgängliga storlekar se [resurs-SKU: er – lista](https://docs.microsoft.com/rest/api/compute/resourceskus/list) och tillämpa följande filter:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Nästa steg 
- Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support).
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [mall](deploy-template.md) eller [Visual Studio](deploy-visual-studio.md).
