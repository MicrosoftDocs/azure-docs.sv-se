---
title: Konfigurera anpassade regler för WAF & standard regel uppsättning för Azure-front dörr
description: Lär dig hur du konfigurerar en WAF-princip består av både anpassade och hanterade regler för en befintlig front dörrs slut punkt.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 9a0e262db9f5c37189a589eefc451a88dd5ea8c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563417"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Konfigurera en brand Väggs princip för webb program med hjälp av Azure PowerShell

En princip för Azure Web Application-brandvägg (WAF) definierar kontroller som krävs när en begäran anländer till en front dörr.
Den här artikeln visar hur du konfigurerar en WAF-princip som består av vissa anpassade regler och med Azure-hanterad standard regel uppsättning aktive rad.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar konfigurera en princip för hastighets begränsning ställer du in din PowerShell-miljö och skapar en profil för en front dörr.

### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön

Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](/powershell/azure/) på en lokal dator och använda det i alla PowerShell-sessioner. Följ anvisningarna på sidan för att logga in med dina Azure-autentiseringsuppgifter och installera AZ PowerShell-modulen.

#### <a name="sign-in-to-azure"></a>Logga in på Azure

```
Connect-AzAccount

```
Se till att du har installerat den senaste versionen av PowerShellGet, innan du installerar Front Door-modulen. Kör kommandot nedan och öppna PowerShell igen.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Installera AZ. ytterdörr-modulen 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Skapa en profil för en front dörr

Skapa en profil för en frontend-dörr genom att följa anvisningarna i [snabb start: skapa en profil för front dörren](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Anpassad regel baserat på http-parametrar

I följande exempel visas hur du konfigurerar en anpassad regel med två matchnings villkor med hjälp av [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Begär Anden kommer från en angiven plats som definieras av referent, och frågesträngen innehåller inte "Password". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Anpassad regel baserat på http-metod för begäran

Skapa en regel för att blockera "sätt" [-metoden med New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) enligt följande:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Skapa en anpassad regel baserat på storleks begränsning

I följande exempel skapas en blockerande regel för att blockera begär Anden med en URL som är längre än 100 tecken med Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Lägg till hanterad standard regel uppsättning

I följande exempel skapas en hanterad standard regel uppsättning med hjälp av Azure PowerShell:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Konfigurera en säkerhets princip

Hitta namnet på den resurs grupp som innehåller profilen för front dörren med hjälp av `Get-AzResourceGroup` . Konfigurera sedan en säkerhets princip med skapade regler i föregående steg med [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) i den angivna resurs gruppen som innehåller profilen för den främre dörren.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Länka princip till en frontend-slutpunkt

Länka objektet säkerhets princip till en befintlig frontend-slutpunkt på klient sidan och uppdatera egenskaperna för front dörren. Börja med att hämta det främre dörr objektet med [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Sedan ställer du in egenskapen frontend- *WebApplicationFirewallPolicyLink* till *resourceId* för "$myWAFPolicy $" som skapades i föregående steg med [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

I exemplet nedan används resurs grupps namnet *myResourceGroupFD1* med antagandet att du har skapat en profil för front dörren med hjälp av anvisningarna i [snabb start: skapa en artikel i front dörren](../../frontdoor/quickstart-create-front-door.md) . I exemplet nedan ersätter du $frontDoorName med namnet på din profil för din front dörr. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Du behöver bara ange egenskapen *WebApplicationFirewallPolicyLink* en gång för att länka en säkerhets princip till en front dörrs klient del. Efterföljande princip uppdateringar tillämpas automatiskt på klient delen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [front dörren](../../frontdoor/front-door-overview.md) 
- Lär dig mer om [WAF med front dörren](afds-overview.md)