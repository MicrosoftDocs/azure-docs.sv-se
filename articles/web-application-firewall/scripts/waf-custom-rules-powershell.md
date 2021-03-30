---
title: Azure PowerShell skript exempel som använder anpassade WAF-regler
description: Azure PowerShell skript exempel – skapa brand vägg för webbaserade program på Application Gateway anpassade regler
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: sample
ms.date: 09/30/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8d800e77ed0e609091d31d9b3177af447ac06f5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079514"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>Skapa anpassade WAF-regler med Azure PowerShell

Det här skriptet skapar en Application Gateway brand vägg för webbaserade program som använder anpassade regler. Den anpassade regeln blockerar trafik om begär ande huvudet innehåller User-Agent *evilbot*.

## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-powershell-module"></a>Azure PowerShell-modul

Om du väljer att installera och använda Azure PowerShell lokalt kräver det här skriptet Azure PowerShell-modulens version 2.1.0 eller senare.

1. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul).
2. Skapa en anslutning med Azure genom att köra `Connect-AzAccount`.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, programgatewayen och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
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
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Skapar en automatisk skalnings konfiguration för Application Gateway.|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Skapar en match-variabel för brand Väggs villkor.|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Skapar ett matchnings villkor för en anpassad regel.|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Skapar en ny anpassad regel för brand Väggs principen för Application Gateway.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Skapar en brand Väggs princip för Application Gateway.|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Skapar en WAF-konfiguration för en Application Gateway.|

## <a name="next-steps"></a>Nästa steg

- Mer information om anpassade regler för WAF finns i [anpassade regler för brand vägg för webbaserade program](../ag/custom-waf-rules-overview.md)
- Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).
