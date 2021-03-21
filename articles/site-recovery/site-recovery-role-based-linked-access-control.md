---
title: Hantera rollbaserad åtkomst kontroll i Azure Site Recovery
description: Den här artikeln beskriver hur du använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att hantera Azure Site Recovery åtkomst.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: d3e1334f513e8ac587d639758d83ce080c5b4ab9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516910"
---
# <a name="manage-site-recovery-access-with-azure-role-based-access-control-azure-rbac"></a>Hantera Site Recovery åtkomst med rollbaserad åtkomst kontroll i Azure (Azure RBAC)

Rollbaserad åtkomst kontroll i Azure (Azure RBAC) möjliggör detaljerad åtkomst hantering för Azure. Med hjälp av Azure RBAC kan du åtskilja ansvars områden i ditt team och bara bevilja vissa åtkomst behörigheter till användare som behövs för att utföra vissa jobb.

Azure Site Recovery tillhandahåller tre inbyggda roller för att kontrol lera Site Recovery hanterings åtgärder. Lär dig mer om [inbyggda Azure-roller](../role-based-access-control/built-in-roles.md)

* [Site Recovery-bidragsgivare](../role-based-access-control/built-in-roles.md#site-recovery-contributor) – Den här rollen har alla behörigheter som krävs för att hantera Azure Site Recovery-åtgärder i ett Recovery Services-valv. En användare med denna roll kan dock inte skapa eller ta bort ett Recovery Services-valv eller tilldela behörighet till andra användare. Den här rollen lämpar sig bäst för haveri beredskaps administratörer som kan aktivera och hantera haveri beredskap för program eller hela organisationer, om det är fallet.
* [Site Recovery-operatör](../role-based-access-control/built-in-roles.md#site-recovery-operator) – Den här rollen har behörighet att utföra och hantera operationer för redundans och återställning av fel. En användare med den här rollen kan inte aktivera eller inaktivera replikering, skapa eller ta bort valv, registrera ny infrastruktur eller tilldela behörigheter till andra användare. Den här rollen lämpar sig bäst för en haveri beredskaps operatör som kan redundansväxla virtuella datorer eller program när de instrueras av program ägare och IT-administratörer i en verklig eller simulerad katastrof situation, till exempel en DR-granskning. Efter haveri stängningen kan DR-operatören återskydda och återställa de virtuella datorerna.
* [Site Recovery-läsare](../role-based-access-control/built-in-roles.md#site-recovery-reader) – Den här rollen har behörighet att visa all Site Recovery-hantering. Den här rollen lämpar sig bäst för en IT-övervakning som kan övervaka det aktuella skyddet och utlösa support biljetter om så behövs.

Om du vill definiera egna roller för ännu mer kontroll, se så här [skapar du anpassade roller](../role-based-access-control/custom-roles.md) i Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Behörigheter som krävs för att aktivera replikering för nya virtuella datorer
När en ny virtuell dator replikeras till Azure med hjälp av Azure Site Recovery verifieras den associerade användarens åtkomst nivåer för att säkerställa att användaren har de behörigheter som krävs för att använda de Azure-resurser som krävs för att Site Recovery.

Om du vill aktivera replikering för en ny virtuell dator måste en användare ha:
* Behörighet att skapa en virtuell dator i den valda resurs gruppen
* Behörighet att skapa en virtuell dator i det valda virtuella nätverket
* Behörighet att skriva till det valda lagrings kontot

En användare behöver följande behörigheter för att slutföra replikeringen av en ny virtuell dator.

> [!IMPORTANT]
>Se till att relevanta behörigheter läggs till per distributions modell (Resource Manager/klassisk) som används för resurs distribution.

> [!NOTE]
> Om du aktiverar replikering för en virtuell Azure-dator och vill tillåta att Site Recovery hanterar uppdateringar och samtidigt aktiverar replikering, kan du även skapa ett nytt Automation-konto, vilket innebär att du måste ha behörighet att skapa ett Automation-konto i samma prenumeration som valvet.

| **Resurstyp** | **Distributionsmodell** | **Behörighet** |
| --- | --- | --- |
| Compute | Resource Manager | Microsoft. Compute/availabilitySets/Read |
|  |  | Microsoft. Compute/virtualMachines/Read |
|  |  | Microsoft. Compute/virtualMachines/Write |
|  |  | Microsoft. Compute/virtualMachines/Delete |
|  | Klassisk | Microsoft. ClassicCompute/domän namn/läsa |
|  |  | Microsoft. ClassicCompute/domän namn/skrivning |
|  |  | Microsoft. ClassicCompute/domän namn/ta bort |
|  |  | Microsoft. ClassicCompute/virtualMachines/Read |
|  |  | Microsoft. ClassicCompute/virtualMachines/Write |
|  |  | Microsoft. ClassicCompute/virtualMachines/Delete |
| Nätverk | Resource Manager | Microsoft. Network/networkInterfaces/Read |
|  |  | Microsoft. Network/networkInterfaces/Write |
|  |  | Microsoft. Network/networkInterfaces/Delete |
|  |  | Microsoft. Network/networkInterfaces/JOIN/åtgärd |
|  |  | Microsoft. Network/virtualNetworks/Read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Klassisk | Microsoft. ClassicNetwork/virtualNetworks/Read |
|  |  | Microsoft. ClassicNetwork/virtualNetworks/JOIN/åtgärd |
| Storage | Resource Manager | Microsoft. Storage/storageAccounts/Read |
|  |  | Microsoft. Storage/storageAccounts/listnycklar/åtgärd |
|  | Klassisk | Microsoft. ClassicStorage/storageAccounts/Read |
|  |  | Microsoft. ClassicStorage/storageAccounts/Listnycklar/Action |
| Resursgrupp | Resource Manager | Microsoft. Resources/Deployments/* |
|  |  | Microsoft. Resources/Subscriptions/resourceGroups/Read |

Överväg att använda de [inbyggda rollerna](../role-based-access-control/built-in-roles.md) "Virtual Machine Contributor" och "klassisk virtuell dator deltagare" för Resource Manager respektive klassiska distributions modeller.

## <a name="next-steps"></a>Nästa steg
* [Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md): kom igång med Azure rbac i Azure Portal.
* Lär dig hur du hanterar åtkomst med:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST-API](../role-based-access-control/role-assignments-rest.md)
* [Fel sökning av Azure RBAC](../role-based-access-control/troubleshooting.md): få förslag på hur du löser vanliga problem.
