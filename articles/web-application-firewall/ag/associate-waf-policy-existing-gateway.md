---
title: Associera en brand Väggs princip för webb program med en befintlig Azure Application Gateway
description: Lär dig hur du associerar en brand Väggs princip för webb program med en befintlig Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74083911"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Associera en WAF-princip med en befintlig Application Gateway

Du kan använda Azure PowerShell för att [skapa en WAF-princip](create-waf-policy-ag.md), men du kanske redan har ett Application Gateway och bara vill associera en WAF-princip till den. I den här artikeln behöver du bara göra det. du skapar en WAF-princip och kopplar den till en redan befintlig Application Gateway. 

1. Hämta din Application Gateway-och brand Väggs princip. Om du inte har en befintlig brand Väggs princip går du till steg 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. Valfritt Skapa en brand Väggs princip.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Om du skapar den här WAF-principen för över gång från en WAF-konfiguration till en WAF-princip måste principen vara en exakt kopia av din gamla konfiguration. Det innebär att varje undantag, anpassad regel, inaktive rad regel grupp osv. måste vara exakt samma som i WAF-konfigurationen.
3. Valfritt Du kan konfigurera WAF-principen så att den passar dina behov. Detta inkluderar anpassade regler, inaktive ring av regler/regel grupper, undantag, inställning av fil överförings gränser osv. Om du hoppar över det här steget väljs alla standardvärden. 
   
4. Spara principen och koppla den till din Application Gateway. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Nästa steg
[Lär dig mer om anpassade regler.](configure-waf-custom-rules.md)
