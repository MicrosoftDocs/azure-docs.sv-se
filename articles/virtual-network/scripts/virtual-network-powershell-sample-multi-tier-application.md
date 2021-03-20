---
title: Skapa ett VNet för program med flera nivåer – Azure PowerShell skript exempel
description: Skapa ett virtuellt nätverk för flera nivå program – Azure PowerShell skript exempel.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51cafb0a344c5b6226b3b033d8c60327eaf2c05f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91268000"
---
# <a name="create-a-network-for-multi-tier-applications-script-sample"></a>Skapa ett nätverk för flernivåprogram – skriptexempel

Det här skriptexemplet skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP och SSH, medan trafik till serverdelsundernätet är begränsad till MySQL, port 3306. När du kört skriptet har du två virtuella datorer, en i varje undernät, som du kan distribuera webbservern och MySQL-programvaran till.

Du kan köra skriptet från Azure [Cloud Shell](https://shell.azure.com/powershell), eller från en lokal installation av PowerShell. Om du använder PowerShell lokalt kräver det här skriptet version 1.0.0 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!-- gitHub issue https://github.com/MicrosoftDocs/azure-docs/issues/17748 -->
 Ett undernäts-ID tilldelas när du har skapat ett virtuellt nätverk, mer specifikt med hjälp av cmdleten New-AzVirtualNetwork med alternativet -Subnet (Undernät). Om du konfigurerar undernätet med hjälp av cmdleten New-AzVirtualNetworkSubnetConfig före anropet till New-AzVirtualNetwork ser du inte undernäts-ID förrän du har anropat New-AzVirtualNetwork.

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, ett virtuellt nätverk och nätverkssäkerhetsgrupper. Varje kommando i följande tabell länkar till kommandospecifik dokumentation:

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt Azure-nätverk och klientdelsundernät. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar ett serverdelsundernät. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress för åtkomst till den virtuella datorn från Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Skapar virtuella nätverksgränssnitt och kopplar dem till det virtuella nätverkets klient- och serverdelsundernät. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Skapar nätverkssäkerhetsgrupper (NSG) som är kopplade till klient- och serverdelsundernäten. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |Skapar NSG-regler som tillåter eller blockerar specifika portar till specifika undernät. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Skapar virtuella datorer och kopplar ett nätverkskort till varje virtuell dator. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för virtuella nätverk finns i [PowerShell-exempel för virtuella nätverk](../powershell-samples.md).
