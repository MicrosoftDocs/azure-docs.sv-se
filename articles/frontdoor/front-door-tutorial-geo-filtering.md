---
title: Självstudie – konfigurera geo-filtrering WAF-princip – Azure-front dörr
description: I den här självstudien får du lära dig hur du skapar en princip för geo-filtrering WAF och associerar principen med din befintliga klient dels värd för frontend-dörr.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: f9b5869f7dd472c50ffb2c1c0dce765200f53882
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91324035"
---
# <a name="tutorial-how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Självstudie: så här konfigurerar du en princip för geo-filtrering WAF för din front dörr
Den här självstudiekursen visar hur du använder Azure PowerShell till att skapa ett exempel på en princip för geofiltrering och associerar principen med din befintliga Front Door-klientdelsvärd. Den här exempel principen för geo-filtrering blockerar förfrågningar från alla andra länder/regioner utom USA.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Definiera matchnings villkor för geo-filtrering.
> - Lägg till en regels matchnings villkor för geo-filtrering.
> - Lägg till regler till en princip.
> - Länka WAF-principen till ytterdörr-frontend-värden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar
* Innan du börjar konfigurera en geo-filter-princip konfigurerar du din PowerShell-miljö och skapar en profil för en front dörr.
* Skapa en frontend-dörr genom att följa instruktionerna som beskrivs i [snabb start: skapa en profil för en front dörr](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Definiera matchnings villkor för geo-filtrering

Skapa ett exempel på matchnings villkor som väljer begär Anden som inte kommer från "US" med [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) på parametrar när ett matchnings villkor skapas. Det finns två bokstäver/regions koder för land/region- [mappning.](front-door-geo-filtering.md)

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Lägga till matchningsvillkor för geofiltrering i en regel med Action (Åtgärd) och Priority (Prioritet)

Skapa ett CustomRule `nonUSBlockRule` -objekt baserat på matchnings villkoret, en åtgärd och en prioritet med [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  En CustomRule (Anpassad regel) kan ha flera MatchCondition (Matchningsvillkor).  I det här exemplet anges Block (Blockera) för Action (Åtgärd) och 1 som Priority (Prioritet), högsta prioritetsnivån.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```
## <a name="add-rules-to-a-policy"></a>Lägga till regler i en princip
Hitta namnet på den resurs grupp som innehåller profilen för front dörren med hjälp av `Get-AzResourceGroup` . Skapa sedan ett `geoPolicy` princip objekt som innehåller `nonUSBlockRule`  kommandot [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) i den angivna resurs gruppen som innehåller profilen för den främre dörren. Du måste ange ett unikt namn för geo-filtrerings principen. 

I exemplet nedan används resurs gruppens namn *FrontDoorQS_rg0* med antagandet att du har skapat den främre dörren med instruktioner som finns i [snabb start: skapa en artikel i front dörren](quickstart-create-front-door.md) . I exemplet nedan ersätter du princip namnet *geoPolicyAllowUSOnly* med ett unikt princip namn.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName FrontDoorQS_rg0 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```
## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Länka WAF-princip till en klients frontend-värd
Länka WAF-principobjektet till den befintliga värd för frontend-frontend och uppdatera front dörrs egenskaper. 

Det gör du genom att först hämta ditt frontend-objekt med [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName FrontDoorQS_rg0
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```
Ange sedan egenskapen frontend-WebApplicationFirewallPolicyLink till resourceId för funktionen `geoPolicy` [set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Du behöver bara ange egenskapen WebApplicationFirewallPolicyLink en gång för att länka en WAF-princip till en klient dels klient del värd. Efterföljande princip uppdateringar tillämpas automatiskt på klient dels värden.

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg konfigurerade du en geo-filtrerings regel som är kopplad till en WAF-princip. Du har sedan länkat principen till en klient dels värd för din frontend-dörr. Om du inte längre behöver geo-filtrerings regeln eller WAF-principen måste du först avassociera principen från klient dels värden innan WAF-principen kan tas bort.

:::image type="content" source="media/front-door-geo-filtering/front-door-disassociate-policy.png" alt-text="Avassociera WAF-princip":::

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa självstudie om du vill lära dig hur du konfigurerar en brand vägg för webbaserade program för din front dörr.

> [!div class="nextstepaction"]
> [Brandvägg för webbaserade program och Front Door](front-door-waf.md)
