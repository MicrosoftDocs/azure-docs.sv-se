---
title: Distribuera Azure-brandväggen med Tillgänglighetszoner med PowerShell
description: I den här artikeln får du lära dig hur du distribuerar en Azure-brandvägg med Tillgänglighetszoner med hjälp av Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 46ecc4754a064e26e61365ed6ca167606bef9d81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94656113"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Distribuera en Azure Firewall med Tillgänglighetszoner med Azure PowerShell

Azure-brandväggen kan konfigureras under distributionen för att omfatta flera Tillgänglighetszoner för ökad tillgänglighet.

Den här funktionen möjliggör följande scenarier:

- Du kan öka tillgänglighet till 99,99% drift tid. Mer information finns i Azure Firewall [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). SLA för 99,99% drift tid erbjuds när två eller fler Tillgänglighetszoner har valts.
- Du kan också associera Azure-brandväggen till en speciell zon, precis för närhets skäl, med service standard service avtalet för 99,95%.

Mer information om Azure Firewall Tillgänglighetszoner finns i [Vad är Azure-brandvägg?](overview.md)

Följande Azure PowerShell exempel visar hur du kan distribuera en Azure-brandvägg med Tillgänglighetszoner.

## <a name="create-a-firewall-with-availability-zones"></a>Skapa en brand vägg med Tillgänglighetszoner

I det här exemplet skapas en brand vägg i zon 1, 2 och 3.

När den offentliga standard-IP-adressen skapas, anges ingen specifik zon. Detta skapar en zon redundant IP-adress som standard. Offentliga standard IP-adresser kan konfigureras antingen i alla zoner eller i en enda zon.

Det är viktigt att du vet att du inte kan ha en brand vägg i zon 1 och en IP-adress i zon 2. Men du kan ha en brand vägg i zon 1 och IP-adress i alla zoner, eller en brand vägg och en IP-adress i samma zon för närhet.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1) `
  -Zone 1,2,3
```

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Övervaka Azure Firewall-loggar](./firewall-diagnostics.md)