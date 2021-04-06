---
title: Konfigurera anpassade svar för brand vägg för webbaserade program (WAF) med Azures front dörr
description: Lär dig hur du konfigurerar en anpassad svarskod och ett meddelande när WAF blockerar en begäran.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 8fc6e71494df36cd6f823661b18e4a3d8ce2938c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563689"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>Konfigurera ett anpassat svar för brand vägg för Azure-webbprogram (WAF)

Som standard när WAF blockerar en begäran på grund av en matchad regel returneras en 403-status kod med meddelandet **blockerad** . Standard meddelandet innehåller även en spårnings referens sträng som kan användas för att länka till [logg poster](./waf-front-door-monitor.md) för begäran.  Du kan konfigurera en anpassad svars status kod och ett anpassat meddelande med referens sträng för ditt användnings fall. Den här artikeln beskriver hur du konfigurerar en anpassad svars sida när en begäran blockeras av WAF.

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>Konfigurera anpassad svars status kod och meddelande Använd Portal

Du kan konfigurera en anpassad svars status kod och-brödtext under "princip inställningar" från WAF-portalen.

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="Princip inställningar för WAF":::

I exemplet ovan sparade vi svars koden som 403 och konfigurerade ett kort "kontakta oss"-meddelande som visas i bilden nedan:

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="Exempel på anpassat svar":::

{{Azure-ref}} infogar den unika referens strängen i svars texten. Värdet matchar fältet TrackingReference i-och- `FrontdoorAccessLog` `FrontdoorWebApplicationFirewallLog` loggarna.

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>Konfigurera anpassad svars status kod och meddelande Använd PowerShell

### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön

Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](/powershell/azure/) på en lokal dator och använda det i alla PowerShell-sessioner. Följ anvisningarna på sidan för att logga in med dina Azure-autentiseringsuppgifter och installera AZ PowerShell-modulen.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ansluta till Azure med en interaktiv dialog ruta för inloggning

```
Connect-AzAccount
Install-Module -Name Az

```
Kontrol lera att du har den aktuella versionen av PowerShellGet installerad. Kör kommandot nedan och öppna PowerShell igen.
```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Installera AZ. ytterdörr-modulen 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Här skapar vi en resurs grupp med hjälp av [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>Skapa en ny WAF-princip med anpassat svar 

Nedan visas ett exempel på hur du skapar en ny WAF-princip med anpassad svars status kod inställd på 405 och meddelande till **du är blockerad.** Använd [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Ändra anpassade svars koder eller svars text inställningar för en befintlig WAF-princip med hjälp av [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [brand vägg för webbaserade program med Azures front dörr](../afds/afds-overview.md)