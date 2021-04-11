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
ms.openlocfilehash: 1c1faf14eb101da41679f9f0596e1e750a3c6a51
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166272"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Tillgängliga storlekar för Azure Cloud Services (utökad support)

I den här artikeln beskrivs tillgängliga storlekar för virtuella datorer för Cloud Services (utökade support) instanser.   

| SKU-familj |  ACU/Core | 
|---|---|
| [A5-7](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series)| 100 |
|[A8-A11](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series---compute-intensive-instances) | 225* |
|[AV2](../virtual-machines/av2-series.md) | 100 | 
|[Styr](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#d-series) | 160 | 
|[Dv2](../virtual-machines/dv2-dsv2-series.md) | 160 – 190 * |
|[Dv3](../virtual-machines/dv3-dsv3-series.md) | 160 – 190 * |
|[Dav4](../virtual-machines/dav4-dasv4-series.md) | 230 – 260 |
|[Eav4](../virtual-machines/eav4-easv4-series.md) | 230 – 260 |
|[Ev3](../virtual-machines/ev3-esv3-series.md) | 160 – 190 * |
|[Projektredovisnings](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#g-series) | 180-240 * |
|[&](../virtual-machines/h-series.md) | 290 - 300* | 

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

Om du vill hämta en lista över tillgängliga storlekar se [resurs-SKU: er – lista](/rest/api/compute/resourceskus/list) och tillämpa följande filter:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Nästa steg 
- Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support).
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [mall](deploy-template.md) eller [Visual Studio](deploy-visual-studio.md).
