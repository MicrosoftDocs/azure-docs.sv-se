---
title: Konfigurera WAF-regel för IP-begränsning för Azure-front dörren
description: Lär dig hur du konfigurerar en brand Väggs regel för webb program för att begränsa IP-adresser för en befintlig Azure frontend-slutpunkt.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 12/22/2020
ms.author: tyao
ms.openlocfilehash: 60a4ef47bc30955c918983d54f613cbdb5cbed73
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746770"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Konfigurera en regel för IP-begränsning med en brand vägg för webbaserade program för Azures front dörr

Den här artikeln visar hur du konfigurerar regler för IP-begränsning i en brand vägg för webbaserade program (WAF) för Azures front dörr med hjälp av Azure Portal, Azure CLI, Azure PowerShell eller en Azure Resource Manager mall.

En IP-adress – baserad åtkomst kontroll regel är en anpassad WAF-regel som låter dig styra åtkomsten till dina webb program. Det gör du genom att ange en lista över IP-adresser eller IP-adressintervall i CIDR-format (Classless Inter-Domain routing). Det finns två typer av matchande variabler i IP-adress matchning, **RemoteAddr** och **SocketAddr**. RemoteAddr är den ursprungliga klient-IP-adressen som vanligt vis skickas via X-vidarebefordrad – för begär ande huvudet. SocketAddr är Källans IP-WAF ser ut. Om användaren är bakom en proxyserver är SocketAddr ofta proxyserverns proxyserveradress.

Som standard är ditt webb program tillgängligt från Internet. Om du vill begränsa åtkomsten till klienter från en lista med kända IP-adresser eller IP-adressintervall kan du skapa en IP-matchande regel som innehåller listan med IP-adresser som matchande värden och anger operatorn till "inte" (negation är sant) och åtgärden som ska **blockeras**. När en regel för IP-begränsning har tillämpats får begär Anden som härstammar från adresser utanför listan över tillåtna 403 ett svar som inte tillåts.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Konfigurera en WAF-princip med Azure Portal

### <a name="prerequisites"></a>Förutsättningar

Skapa en profil för Azures frontend-dörr genom att följa anvisningarna i [snabb start: skapa en frontend-dörr för ett globalt webb program med hög](../../frontdoor/quickstart-create-front-door.md)tillgänglighet.

### <a name="create-a-waf-policy"></a>Skapa en WAF-princip

1. På Azure Portal väljer du **skapa en resurs**, skriver  **WebApplication-brandvägg** i sökrutan och väljer **brand vägg för webbaserade program (WAF)**.
2. Välj **Skapa**.
3. På sidan **skapa en WAF-princip** använder du följande värden för att slutföra fliken **grundläggande** :
   
   |Inställning  |Värde  |
   |---------|---------|
   |Princip för     |Global WAF (Front dörr)|
   |Prenumeration     |Välj din prenumeration|
   |Resursgrupp     |Välj den resurs grupp där din frontend-dörr finns.|
   |Principnamn     |Ange ett namn för principen|
   |Princip tillstånd     |Enabled|

   Välj **Nästa: princip inställningar**

1. På fliken **princip inställningar** väljer du **förhindra**. För **block svars texten** skriver *du blockerad!* så du kan se att den anpassade regeln gäller.
2. Välj **Nästa: hanterade regler**.
3. Välj **Nästa: anpassade regler**.
4. Välj **Lägg till anpassad regel**.
5. På sidan **Lägg till anpassad regel** använder du följande test värden för att skapa en anpassad regel:

   |Inställning  |Värde  |
   |---------|---------|
   |Namn på anpassad regel     |FdWafCustRule|
   |Status     |Enabled|
   |Regeltyp     |Matchning|
   |Prioritet    |100|
   |Matchnings typ     |IP-adress|
   |Matcha variabel|RemoteAddr|
   |Åtgärd|Innehåller inte|
   |IP-adress eller intervall|10.10.10.0/24|
   |Dra|Neka trafik|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Anpassad regel":::

   Välj **Lägg till**.
6. Välj **Nästa: Association**.
7. Välj **Lägg till klient dels värd**.
8. För **klient dels värd** väljer du din klient dels värd och väljer **Lägg till**.
9. Välj **Granska + skapa**.
10. När princip valideringen har passerat väljer du **skapa**.

### <a name="test-your-waf-policy"></a>Testa din WAF-princip

1. När din WAF-princip har distribuerats bläddrar du till värd namnet för klient delen av klient delen.
2. Du bör se ditt anpassade block meddelande.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="Test av WAF-regel":::

   > [!NOTE]
   > En privat IP-adress har avsiktligt använts i den anpassade regeln för att garantera att regeln utlöses. I en verklig distribution skapar du *Tillåt* och *neka* regler med IP-adresser för din specifika situation.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Konfigurera en WAF-princip med Azure CLI

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar konfigurera en princip för IP-begränsning ställer du in CLI-miljön och skapar en profil för Azure-frontend-dörren.

#### <a name="set-up-the-azure-cli-environment"></a>Konfigurera Azure CLI-miljön
1. Installera [Azure CLI](/cli/azure/install-azure-cli)eller Använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure Portal. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Välj knappen **prova** i CLI-kommandona som följer och logga sedan in på ditt Azure-konto i Cloud Shell-sessionen som öppnas. När sessionen har startats anger `az extension add --name front-door` du för att lägga till Azures front dörrs tillägg.
 2. Om du använder CLI lokalt i bash loggar du in på Azure med hjälp av `az login` .

#### <a name="create-an-azure-front-door-profile"></a>Skapa en profil för Azures frontend-dörr
Skapa en profil för Azures frontend-dörr genom att följa anvisningarna i [snabb start: skapa en frontend-dörr för ett globalt webb program med hög](../../frontdoor/quickstart-create-front-door.md)tillgänglighet.

### <a name="create-a-waf-policy"></a>Skapa en WAF-princip

Skapa en WAF-princip med hjälp av kommandot [AZ Network frontend-dörr WAF-policy Create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) . I exemplet nedan ersätter du princip namnet *IPAllowPolicyExampleCLI* med ett unikt princip namn.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Lägg till en anpassad kontroll regel för IP-åtkomst

Använd kommandot [AZ Network front-dörr WAF-policy "Create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) " för att lägga till en anpassad kontroll regel för IP-åtkomst för WAF-principen som du nyss skapade.

I följande exempel:
-  Ersätt *IPAllowPolicyExampleCLI* med din unika princip som skapats tidigare.
-  Ersätt *IP-Address-Range-1*, *IP-Address-Range-2* med ditt eget intervall.

Börja med att skapa en regel för IP-Tillåt för principen som skapats från föregående steg. 
> [!NOTE]
> **--överlåtet** krävs eftersom en regel måste ha ett matchnings villkor för att kunna läggas till i nästa steg.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Lägg sedan till matchnings villkor i regeln:

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
Hitta en WAF-princips ID med hjälp av kommandot [AZ Network frontend-dörr WAF-policy show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) . Ersätt *IPAllowPolicyExampleCLI* i följande exempel med din unika princip som du skapade tidigare.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Länka en WAF-princip till en klients front-end-värd i Azure

Ställ in *WebApplicationFirewallPolicyLink* -ID: t för Azures frontend-dörr i princip-ID med hjälp av kommandot [AZ Network front-dörr Update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) . Ersätt *IPAllowPolicyExampleCLI* med din unika princip som du skapade tidigare.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
I det här exemplet tillämpas WAF-principen på **FrontendEndpoints [0]**. Du kan länka WAF-principen till vilken som helst av dina klient delar.
> [!Note]
> Du behöver bara ange egenskapen **WebApplicationFirewallPolicyLink** en gång för att länka en WAF-princip till en front-end-klient i Azure. Efterföljande princip uppdateringar tillämpas automatiskt på klient delen.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Konfigurera en WAF-princip med Azure PowerShell

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar konfigurera en princip för IP-begränsning ställer du in din PowerShell-miljö och skapar en profil för Azures frontend-dörr.

#### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell innehåller en uppsättning cmdletar som använder [Azure Resource Manager](../../azure-resource-manager/management/overview.md) -modellen för att hantera Azure-resurser.

Du kan installera [Azure PowerShell](/powershell/azure/) på en lokal dator och använda det i alla PowerShell-sessioner. Följ instruktionerna på sidan för att logga in på PowerShell med dina Azure-autentiseringsuppgifter och installera sedan AZ-modulen.

1. Anslut till Azure med hjälp av följande kommando och Använd sedan en interaktiv dialog ruta för att logga in.
    ```
    Connect-AzAccount
    ```
 2. Kontrol lera att du har den aktuella versionen av PowerShellGet-modulen installerad innan du installerar en Azure-modul för Inkorgen. Kör följande kommando och öppna PowerShell igen.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installera modulen AZ. ytterdörr med hjälp av följande kommando. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Skapa en profil för Azures frontend-dörr
Skapa en profil för Azures frontend-dörr genom att följa anvisningarna i [snabb start: skapa en frontend-dörr för ett globalt webb program med hög](../../frontdoor/quickstart-create-front-door.md)tillgänglighet.

### <a name="define-an-ip-match-condition"></a>Definiera ett villkor för IP-matchning
Använd kommandot [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) för att definiera ett villkor för IP-matchning.
I följande exempel ersätter du *IP-Address-Range-1*, *IP-Address-Range-2* med ditt eget intervall.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Skapa en anpassad IP-regel för Tillåt

Använd kommandot [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) för att definiera en åtgärd och ange en prioritet. I följande exempel kommer begär Anden som inte från klient-IP-adresser som matchar listan att blockeras.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Konfigurera en WAF-princip
Hitta namnet på den resurs grupp som innehåller Azures profil för front dörren med hjälp av `Get-AzResourceGroup` . Konfigurera sedan en WAF-princip med IP-regeln med hjälp av [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Länka en WAF-princip till en klients front-end-värd i Azure

Länka ett WAF-princip objekt till en befintlig klient dels värd och uppdatera egenskaperna för Azures front dörr. Börja med att hämta Azures frontend-dörr genom att använda [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Ange sedan egenskapen **WebApplicationFirewallPolicyLink** till resurs-ID för *$IPAllowPolicyExamplePS*, som skapades i föregående steg, med hjälp av kommandot [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) .

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> I det här exemplet tillämpas WAF-principen på **FrontendEndpoints [0]**. Du kan länka en WAF-princip till någon av dina klient delar. Du behöver bara ange egenskapen **WebApplicationFirewallPolicyLink** en gång för att länka en WAF-princip till en front-end-klient i Azure. Efterföljande princip uppdateringar tillämpas automatiskt på klient delen.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Konfigurera en WAF-princip med en Resource Manager-mall
Om du vill visa mallen som skapar en Azure-princip för frontend-dörren och en WAF-princip med anpassade regler för IP-begränsning går du till [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en profil för Azures frontend-dörr](../../frontdoor/quickstart-create-front-door.md).
