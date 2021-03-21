---
title: vCPU kvoter för Azure
description: Lär dig mer om vCPU kvoter för Azure Virtual Machines.
author: cynthn
ms.service: virtual-machines
ms.subservice: quota
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: ca7d95a9916aafdab2550eee48ea05ddfa5874c1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560726"
---
# <a name="check-vcpu-quotas-using-azure-powershell"></a>Kontrol lera vCPU-kvoter med Azure PowerShell

VCPU kvoter för virtuella datorer och skalnings uppsättningar för virtuella datorer ordnas på två nivåer för varje prenumeration i varje region. Den första nivån är den totala regionala virtuella processorer och den andra nivån är de olika virtuella datorerna i VM-serien, till exempel D-seriens virtuella processorer. När en ny virtuell dator distribueras får inte virtuella processorer för den virtuella datorn överskrida vCPU-kvoten för den virtuella datorns storleks familj eller den totala regionala vCPU-kvoten. Om någon av dessa kvoter överskrids, kommer distributionen av virtuella datorer inte att tillåtas. Det finns också en kvot för det totala antalet virtuella datorer i regionen. Informationen om var och en av dessa kvoter visas i avsnittet **användning och kvoter** på **prenumerations** sidan i [Azure Portal](https://portal.azure.com), eller så kan du fråga efter värden med PowerShell.

> [!NOTE]
> Kvoten beräknas baserat på det totala antalet kärnor i som används både allokerat och friallokerat. Om du behöver ytterligare kärnor begär du [en kvot ökning](../../azure-portal/supportability/resource-manager-core-quotas-request.md) eller tar bort virtuella datorer som inte längre behövs. 
 
## <a name="check-usage"></a>Kontrollera användning

Du kan använda cmdleten [Get-AzVMUsage](/powershell/module/az.compute/get-azvmusage) för att kontrol lera din kvot användning.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

Resultatet kommer att se ut ungefär så här:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>Reserverade VM-instanser
Reserverade VM-instanser, som är begränsade till en enda prenumeration utan flexibilitet för virtuella datorer, lägger till en ny aspekt i vCPU-kvoterna. Värdena beskriver antalet instanser av den angivna storleken som måste distribueras i prenumerationen. De fungerar som en plats hållare i kvot systemet för att säkerställa att kvoten är reserverad för att säkerställa att reserverade VM-instanser kan distribueras i prenumerationen. Om till exempel en enskild prenumeration har 10 Standard_D1 reserverade VM-instanser är användnings gränsen för Standard_D1 reserverade VM-instanser 10. Detta gör att Azure ser till att det alltid finns minst 10 virtuella processorer tillgängligt i den totala regionala virtuella processorer-kvoten som ska användas för Standard_D1 instanser och det finns minst 10 virtuella processorer i standard kvoten på D-serien som ska användas för Standard_D1-instanser.

Om en kvot ökning krävs för att köpa en enskild prenumeration i RI, kan du [begära en kvot ökning](../../azure-portal/supportability/resource-manager-core-quotas-request.md) för din prenumeration.

## <a name="next-steps"></a>Nästa steg

Mer information om fakturering och kvoter finns i Azure- [prenumerationer, tjänst gränser, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=/azure/billing/TOC.json).
