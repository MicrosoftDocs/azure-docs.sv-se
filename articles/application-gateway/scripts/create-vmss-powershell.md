---
title: Azure PowerShell-skriptexempel – Hantera webbtrafik | Microsoft Docs
description: Azure PowerShell-skriptexempel – Hantera webbtrafik med en programgateway och en VM-skalningsuppsättning.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: a4f1961f9c7d79a95a0e8f4cc3fc18bdeac2f36f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89078715"
---
# <a name="manage-web-traffic-with-azure-powershell"></a>Hantera webbtrafik med hjälp av Azure PowerShell

Med det här skriptet skapar du en programgateway som använder en VM-skalningsuppsättning för serverdelen. Programgatewayen kan sedan konfigureras för att hantera webbtrafik. När du har kört skriptet kan du testa programgatewayen med hjälp av dess offentliga IP-adress.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss.ps1 "Create application gateway")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, programgatewayen och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar undernätskonfigurationen. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar det virtuella nätverket med hjälp av undernätskonfigurationen. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar den offentliga IP-adressen för programgatewayen. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Skapar den konfiguration som associerar ett undernät med programgatewayen. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Skapar den konfiguration som tilldelar en offentlig IP-adress till programgatewayen. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Tilldelar en port som ska användas för åtkomst till programgatewayen. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Skapar en serverdelspool för en programgateway. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Konfigurerar inställningar för serverdelspoolen. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Skapar en lyssnare. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Skapar en hanteringsregel. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Ange nivå och kapacitet för en programgateway. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Skapar en programgateway. |
| [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) | Skapar en lagringsprofil för skalningsuppsättningen. |
| [Set-AzVmssOsProfile](/powershell/module/az.compute/set-azvmssosprofile) | Definierar operativsystemet för skalningsuppsättningen. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.compute/add-azvmssnetworkinterfaceconfiguration) | Definierar nätverksgränssnittet för skalningsuppsättningen. |
| [New-AzVmss](/powershell/module/az.compute/new-azvm) | Skapar en VM-skalningsuppsättning. |
| [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) | Hämtar den offentliga IP-adressen för en programgateway. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. | 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för programgatewayer finns i [dokumentationen för Azure Application Gateway](../powershell-samples.md).
