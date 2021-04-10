---
title: Skapa och konfigurera en Azure DDoS Protection plan med Azure PowerShell
description: Lär dig hur du skapar en DDoS Protection plan med Azure PowerShell
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 41d45b216337cc1b674a9be390f241c4e1bb062a
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103138"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-powershell"></a>Snabb start: skapa och konfigurera Azure DDoS Protection standard med hjälp av Azure PowerShell

Kom igång med Azure DDoS Protection standard med hjälp av Azure PowerShell. 

En DDoS skydds plan definierar en uppsättning virtuella nätverk som har DDoS Protection standard aktiverat, mellan prenumerationer. Du kan konfigurera en DDoS skydds plan för din organisation och länka virtuella nätverk från flera prenumerationer till samma plan. 

I den här snabb starten skapar du en DDoS skydds plan och länkar den till ett virtuellt nätverk. 

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell installerat lokalt eller Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ddos-protection-plan"></a>Skapa en DDoS Protection Plan

I Azure allokerar du relaterade resurser till en resursgrupp. Du kan antingen använda en befintlig resurs grupp eller skapa en ny.

Om du vill skapa en resurs grupp använder du [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I det här exemplet ska vi namnge vår resurs grupp _MyResourceGroup_ och använda platsen _USA, östra_ :

```azurepowershell-interactive
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

Skapa nu en DDoS-skydds plan med namnet _MyDdosProtectionPlan_:

```azurepowershell-interactive
New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"
```

## <a name="enable-ddos-for-a-virtual-network"></a>Aktivera DDoS för ett virtuellt nätverk

### <a name="enable-ddos-for-a-new-virtual-network"></a>Aktivera DDoS för ett nytt virtuellt nätverk

Du kan aktivera DDoS-skydd när du skapar ett virtuellt nätverk. I det här exemplet ska vi namnge vårt virtuella nätverk _MyVnet_: 

```azurepowershell-interactive
New-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup -Location "East US" -AddressPrefix 10.0.0.0/16
```

### <a name="enable-ddos-for-an-existing-virtual-network"></a>Aktivera DDoS för ett befintligt virtuellt nätverk

Du kan associera ett befintligt virtuellt nätverk när du skapar en DDoS skydds plan:

```azurepowershell-interactive
# Creates the DDoS protection plan
$ddosProtectionPlan = New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"

# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = New-Object Microsoft.Azure.Commands.Network.Models.PSResourceId

# Update the properties and enable DDoS protection
$vnet.DdosProtectionPlan.Id = $ddosProtectionPlan.Id
$vnet.EnableDdosProtection = $true
$vnet | Set-AzVirtualNetwork
``` 

## <a name="validate-and-test"></a>Validera och testa

Börja med att kontrol lera informationen om din DDoS-skydds plan:

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan
```

Kontrol lera att kommandot returnerar rätt information om din DDoS-skydds plan.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina resurser för nästa självstudie. Om du inte längre behöver tar du bort resurs gruppen _MyResourceGroup_ . När du tar bort resurs gruppen tar du även bort DDoS-skydds planen och alla relaterade resurser. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Så här inaktiverar du DDoS-skydd för ett virtuellt nätverk: 

```azurepowershell-interactive
# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = $null
$vnet.EnableDdosProtection = $false
$vnet | Set-AzVirtualNetwork
```

Om du vill ta bort en DDoS skydds plan måste du först ta bort alla virtuella nätverk från den.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig att visa och konfigurera telemetri för din DDoS-skydds plan fortsätter du till självstudierna.

> [!div class="nextstepaction"]
> [Visa och konfigurera telemetri för DDoS-skydd](telemetry.md)