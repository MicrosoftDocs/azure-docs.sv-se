---
title: Konfigurera WAF-regel för IP-begränsning för Azure Front Door
description: Lär dig hur du konfigurerar en Web Application Firewall regel för att begränsa IP-adresser för en Azure Front Door slutpunkt.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 12/22/2020
ms.author: tyao
ms.openlocfilehash: 32bf7a5ecc93fa23c8c704dc346048c26c086121
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860859"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Konfigurera en regel för IP-begränsning med en Web Application Firewall för Azure Front Door

Den här artikeln visar hur du konfigurerar IP-begränsningsregler i en Web Application Firewall (WAF) för Azure Front Door med hjälp av Azure Portal, Azure CLI, Azure PowerShell eller en Azure Resource Manager-mall.

En IP-adressbaserad åtkomstkontrollregel är en anpassad WAF-regel som gör att du kan styra åtkomsten till dina webbprogram. Den gör detta genom att ange en lista över IP-adresser eller IP-adressintervall i formatet CIDR (Classless Inter-Domain Routing). Det finns två typer av matchningsvariabler i IP-adressmatchning: **RemoteAddr** **och SocketAddr**. RemoteAddr är den ursprungliga klient-IP-adressen som vanligtvis skickas via begärandehuvudet X-Forwarded-For. SocketAddr är källans IP-adress som WAF ser. Om användaren finns bakom en proxyserver är SocketAddr ofta proxyserverns adress.

Som standard är webbappen tillgänglig från Internet. Om du vill begränsa åtkomsten till klienter från en lista över kända IP-adresser eller IP-adressintervall kan du skapa en IP-matchande regel som innehåller listan över IP-adresser som matchande värden och anger operatorn till "Not" (negate is true) och åtgärden blockera **.** När en regel för IP-begränsning tillämpas får begäranden som kommer från adresser utanför den här tillåtna listan ett 403 Forbidden-svar.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Konfigurera en WAF-princip med Azure Portal

### <a name="prerequisites"></a>Förutsättningar

Skapa en Azure Front Door profil genom att följa anvisningarna som beskrivs i Snabbstart: Skapa en Front Door för en [global webbapp med hög åtkomst.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Skapa en WAF-princip

1. På Azure Portal väljer du **Skapa en** resurs, skriver **Brandvägg** för webbaserade program i sökrutan och väljer **sedan Web Application Firewall (WAF).**
2. Välj **Skapa**.
3. På sidan **Skapa en WAF-princip** använder du följande värden för att slutföra **fliken Grunder:**
   
   |Inställning  |Värde  |
   |---------|---------|
   |Princip för     |Global WAF (Front Door)|
   |Prenumeration     |Välj din prenumeration|
   |Resursgrupp     |Välj den resursgrupp där Front Door är.|
   |Principnamn     |Ange ett namn för principen|
   |Principtillstånd     |Enabled|

   Välj **Nästa: Principinställningar**

1. På fliken **Principinställningar** väljer du **Prevention (Förebygga).** För Block **response body (Blockera** svarstext) *skriver du You've been blocked!* (Du har blockerats! så att du kan se att din anpassade regel gäller.
2. Välj **Nästa: Hanterade regler**.
3. Välj **Nästa: Anpassade regler**.
4. Välj **Lägg till anpassad regel.**
5. På sidan **Lägg till anpassad** regel använder du följande testvärden för att skapa en anpassad regel:

   |Inställning  |Värde  |
   |---------|---------|
   |Anpassat regelnamn     |FdWafCustRule|
   |Status     |Enabled|
   |Regeltyp     |Matchning|
   |Prioritet    |100|
   |Matchningstyp     |IP-adress|
   |Matcha variabel|RemoteAddr|
   |Åtgärd|Innehåller inte|
   |IP-adress eller intervall|10.10.10.0/24|
   |Sedan|Neka trafik|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Anpassad regel":::

   Välj **Lägg till**.
6. Välj **Nästa: Association**.
7. Välj **Lägg till värd för frontend.**
8. För **Frontend-värd** väljer du din frontend-värd och sedan Lägg **till**.
9. Välj **Granska + skapa**.
10. När principverifieringen har passerat väljer du **Skapa**.

### <a name="test-your-waf-policy"></a>Testa WAF-principen

1. När WAF-principdistributionen är klar bläddrar du Front Door namnet på din värd för frontend.
2. Du bör se ditt anpassade blockeringsmeddelande.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="Waf-regeltest":::

   > [!NOTE]
   > En privat IP-adress användes avsiktligt i den anpassade regeln för att garantera att regeln skulle utlösas. I en faktisk distribution skapar du *regler för att* tillåta och neka *med* hjälp av IP-adresser för just din situation.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Konfigurera en WAF-princip med Azure CLI

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar konfigurera en princip för IP-begränsning konfigurerar du CLI-miljön och skapar en Azure Front Door profil.

#### <a name="set-up-the-azure-cli-environment"></a>Konfigurera Azure CLI-miljön
1. Installera [Azure CLI](/cli/azure/install-azure-cli)eller använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure Portal. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Välj knappen **Prova i** de CLI-kommandon som följer och logga sedan in på ditt Azure-konto i den Cloud Shell som öppnas. När sessionen har startat anger du `az extension add --name front-door` för att lägga Azure Front Door tillägget.
 2. Om du använder CLI lokalt i Bash loggar du in på Azure med hjälp av `az login` .

#### <a name="create-an-azure-front-door-profile"></a>Skapa en Azure Front Door profil
Skapa en Azure Front Door profil genom att följa anvisningarna i Snabbstart: Skapa en Front Door för en [global webbapp med hög åtkomst.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Skapa en WAF-princip

Skapa en WAF-princip med [kommandot az network front-door waf-policy create.](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_create) I exemplet nedan ersätter du principnamnet *IPAllowPolicyExampleCLI* med ett unikt principnamn.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Lägga till en anpassad regel för IP-åtkomstkontroll

Använd kommandot [az network front-door waf-policy custom-rule create](/cli/azure/network/front-door/waf-policy/rule#az_network_front_door_waf_policy_rule_create) för att lägga till en anpassad regel för IP-åtkomstkontroll för WAF-principen som du nyss skapade.

I följande exempel:
-  Ersätt *IPAllowPolicyExampleCLI med* din unika princip som du skapade tidigare.
-  Ersätt *ip-address-range-1*, *ip-address-range-2* med ditt eget intervall.

Skapa först en tillåt-regel för IP för principen som skapades i föregående steg. 
> [!NOTE]
> **--defer** krävs eftersom en regel måste ha ett matchningsvillkor som ska läggas till i nästa steg.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Lägg sedan till matchningsvillkor i regeln:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Hitta ID för en WAF-princip 
Hitta EN WAF-princips ID med hjälp av [kommandot az network front-door waf-policy show.](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_show) Ersätt *IPAllowPolicyExampleCLI* i följande exempel med din unika princip som du skapade tidigare.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Länka en WAF-princip till Azure Front Door en värd på frontend-sidan

Ange Azure Front Door *WebApplicationFirewallPolicyLink* ID till princip-ID med hjälp av [kommandot az network front-door update.](/cli/azure/network/front-door#az_network_front_door_update) Ersätt *IPAllowPolicyExampleCLI* med din unika princip som du skapade tidigare.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
I det här exemplet tillämpas WAF-principen på **FrontendEndpoints[0]**. Du kan länka WAF-principen till någon av dina frontend-ändarna.
> [!Note]
> Du behöver bara ange egenskapen **WebApplicationFirewallPolicyLink** en gång för att länka en WAF-princip till en Azure Front Door-frontend. Efterföljande principuppdateringar tillämpas automatiskt på frontend-sidan.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Konfigurera en WAF-princip med Azure PowerShell

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar konfigurera en princip för IP-begränsning konfigurerar du PowerShell-miljön och skapar en Azure Front Door profil.

#### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell innehåller en uppsättning cmdlets som använder Azure Resource Manager [för](../../azure-resource-manager/management/overview.md) att hantera Azure-resurser.

Du kan installera [Azure PowerShell](/powershell/azure/) på en lokal dator och använda det i alla PowerShell-sessioner. Följ anvisningarna på sidan för att logga in på PowerShell med dina Azure-autentiseringsuppgifter och installera sedan Az-modulen.

1. Anslut till Azure med följande kommando och använd sedan en interaktiv dialogruta för att logga in.
    ```
    Connect-AzAccount
    ```
 2. Innan du installerar en Azure Front Door-modul kontrollerar du att du har den aktuella versionen av PowerShellGet-modulen installerad. Kör följande kommando och öppna sedan PowerShell igen.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installera Az.FrontDoor-modulen med hjälp av följande kommando. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Skapa en Azure Front Door profil
Skapa en Azure Front Door profil genom att följa anvisningarna som beskrivs i Snabbstart: Skapa en Front Door för en [global webbapp med hög åtkomst.](../../frontdoor/quickstart-create-front-door.md)

### <a name="define-an-ip-match-condition"></a>Definiera ett IP-matchningsvillkor
Använd kommandot [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) för att definiera ett IP-matchningsvillkor.
I följande exempel ersätter du *ip-address-range-1*, *ip-address-range-2* med ditt eget intervall.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Skapa en anpassad regel för IP-tillåt

Använd kommandot [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) för att definiera en åtgärd och ange en prioritet. I följande exempel blockeras begäranden som inte kommer från klient-IP-adresser som matchar listan.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Konfigurera en WAF-princip
Leta upp namnet på den resursgrupp som innehåller Azure Front Door profilen med hjälp av `Get-AzResourceGroup` . Konfigurera sedan en WAF-princip med IP-regeln med [hjälp av New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Länka en WAF-princip till Azure Front Door en värd på frontend-sidan

Länka ett WAF-principobjekt till en befintlig frontend-värd och uppdatera Azure Front Door egenskaper. Först hämtar du Azure Front Door med hjälp av [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Ange sedan egenskapen **WebApplicationFirewallPolicyLink** till resurs-ID:t för *$IPAllowPolicyExamplePS*, som skapades i föregående steg, med hjälp av kommandot [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> I det här exemplet tillämpas WAF-principen på **FrontendEndpoints[0]**. Du kan länka en WAF-princip till någon av dina frontend-ändarna. Du behöver bara ange egenskapen **WebApplicationFirewallPolicyLink** en gång för att länka en WAF-princip till en Azure Front Door-frontend. Efterföljande principuppdateringar tillämpas automatiskt på frontend-sidan.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Konfigurera en WAF-princip med en Resource Manager mall
Om du vill visa mallen som skapar en Azure Front Door princip och en WAF-princip med anpassade IP-begränsningsregler går du till [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Nästa steg

- Lär dig hur [du skapar en Azure Front Door profil](../../frontdoor/quickstart-create-front-door.md).
