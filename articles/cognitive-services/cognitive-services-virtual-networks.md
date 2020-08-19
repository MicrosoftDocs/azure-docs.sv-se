---
title: Virtuella nätverk
titleSuffix: Azure Cognitive Services
description: Konfigurera skiktad nätverks säkerhet för dina Cognitive Services-resurser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: dapine
ms.openlocfilehash: 808d42c821272882bbf0e01a36e49f7f10b30efa
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505035"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Konfigurera virtuella nätverk för Azure Cognitive Services

Azure Cognitive Services tillhandahåller en skiktad säkerhets modell. Med den här modellen kan du begränsa åtkomsten till dina Cognitive Services-konton till utvalda nätverk. När du har konfigurerat nätverksregler kan endast program som begär data över den angivna uppsättningen nätverk komma åt kontot. Du kan begränsa åtkomsten till dina resurser med filtrering av begäranden. Tillåter endast förfrågningar från angivna IP-adresser, IP-intervall eller från en lista över undernät i [virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md).

Ett program som har åtkomst till en Cognitive Services-resurs när nätverks regler tillämpas kräver auktorisering. Auktorisering stöds med [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (autentiseringsuppgifter för Azure AD) eller med en giltig API-nyckel.

> [!IMPORTANT]
> Om du aktiverar brand Väggs regler för ditt Cognitive Services-konto blockeras inkommande begär Anden för data som standard. För att tillåta begär Anden genom måste något av följande villkor vara uppfyllda:

> * Begäran bör härstamma från en tjänst som körs i ett Azure-Virtual Network (VNet) i listan över tillåtna undernät för mål Cognitive Services kontot. Slut punkten i begär Anden som kommer från VNet måste anges som den [anpassade under domänen](cognitive-services-custom-subdomains.md) för ditt Cognitive Services-konto.
> * Eller så måste begäran härstamma från en lista över tillåtna IP-adresser.
>
> Begär Anden som blockeras inkluderar de från andra Azure-tjänster, från Azure Portal, från loggnings-och mått tjänster och så vidare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenarier

För att skydda din Cognitive Services-resurs bör du först konfigurera en regel för att neka åtkomst till trafik från alla nätverk (inklusive Internet trafik) som standard. Sedan bör du konfigurera regler som beviljar åtkomst till trafik från vissa virtuella nätverk. Med den här konfigurationen kan du bygga en säker nätverks gränser för dina program. Du kan också konfigurera regler för att bevilja åtkomst till trafik från Välj offentliga IP-adressintervall för Internet, aktivera anslutningar från vissa Internet-eller lokala klienter.

Nätverks regler tillämpas på alla nätverks protokoll till Azure Cognitive Services, inklusive REST och WebSocket. Om du vill komma åt data med hjälp av verktyg som Azure test-konsoler måste explicita nätverks regler konfigureras. Du kan använda nätverks regler för befintliga Cognitive Services-resurser eller när du skapar nya Cognitive Services resurser. När nätverks reglerna tillämpas tillämpas de för alla begär Anden.

## <a name="supported-regions-and-service-offerings"></a>Regioner och tjänst erbjudanden som stöds

Virtuella nätverk (virtuella nätverk) stöds i [regioner där Cognitive Services är tillgängliga](https://azure.microsoft.com/global-infrastructure/services/). Om kognitiva tjänster inte visas i listan, har den för närvarande inte stöd för virtuella nätverk.

> [!div class="checklist"]
> * [Avvikelseidentifiering](./anomaly-detector/index.yml)
> * [Visuellt innehåll](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Ansikte](./face/index.yml)
> * [Formigenkänning](./form-recognizer/index.yml)
> * [Language Understanding](./luis/index.yml)
> * [Personanpassning](./personalizer/index.yml)
> * [Textanalys](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)
> * [Translator Text](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#virtual-network-support)
> * [Avancerad läsare](./immersive-reader/index.yml)

## <a name="service-tags"></a>Tjänsttaggar

Cognitive Services stöder service märken för konfiguration av nätverks regler. Tjänsterna i listan nedan ingår i **CognitiveServicesManagement** service tag.

> [!div class="checklist"]
> * [Avvikelseidentifiering](./anomaly-detector/index.yml)
> * [Visuellt innehåll](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Ansikte](./face/index.yml)
> * [Formigenkänning](./form-recognizer/index.yml)
> * [Language Understanding (LUIS)](./luis/index.yml)
> * [Personanpassning](./personalizer/index.yml)
> * [Textanalys](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)
> * [Översättare](./translator/index.yml)
> * [Tal tjänst](./speech-service/index.yml)
> * [Avancerad läsare](./immersive-reader/index.yml)

## <a name="change-the-default-network-access-rule"></a>Ändra standardåtkomstregeln för nätverk

Som standard accepterar Cognitive Services-resurser anslutningar från klienter i ett nätverk. Om du vill begränsa åtkomsten till valda nätverk måste du först ändra standardåtgärden.

> [!WARNING]
> Om du gör ändringar i nätverks reglerna kan det påverka dina programs möjlighet att ansluta till Azure Cognitive Services. Om du anger standard nätverks regeln till **neka** blockeras alla åtkomst till data, om inte vissa nätverks regler som **beviljar** åtkomst också tillämpas. Se till att bevilja åtkomst till alla tillåtna nätverk som använder nätverksregler innan du ändrar standardregeln för att neka åtkomst. Om du tillåter att du listar IP-adresser för ditt lokala nätverk måste du lägga till alla möjliga offentliga IP-adresser från ditt lokala nätverk.

### <a name="managing-default-network-access-rules"></a>Hantera standardregler för nätverksåtkomst

Du kan hantera standard regler för nätverks åtkomst för Cognitive Services resurser via Azure Portal, PowerShell eller Azure CLI.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. Gå till den Cognitive Services resurs som du vill skydda.

1. Välj menyn **resurs hantering** som kallas **virtuellt nätverk**.

   ![Virtuellt nätverks alternativ](media/vnet/virtual-network-blade.png)

1. Om du vill neka åtkomst som standard väljer du att tillåta åtkomst från **valda nätverk**. Med de **valda nätverks** inställningarna fristående, som inte åtföljs av konfigurerade **virtuella nätverk** eller **adress intervall** , nekas all åtkomst effektivt. När all åtkomst nekas är begär Anden som försöker använda Cognitive Services resursen inte tillåtna. Azure Portal Azure PowerShell eller Azure CLI kan fortfarande användas för att konfigurera Cognitive Services resursen.
1. Om du vill tillåta trafik från alla nätverk väljer du att tillåta åtkomst från **Alla nätverk**.

   ![Neka virtuella nätverk](media/vnet/virtual-network-deny.png)

1. Klicka på **Spara** för att tillämpa dina ändringar.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-az-ps) och [Logga](/powershell/azure/authenticate-azureps)in eller Välj **prova**.

1. Visa status för standard regeln för Cognitive Services resursen.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Ange standard regeln för att neka nätverks åtkomst som standard.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Ange standard regeln för att tillåta nätverks åtkomst som standard.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in, eller Välj **prova**.

1. Visa status för standard regeln för Cognitive Services resursen.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Ange standard regeln för att neka nätverks åtkomst som standard.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Ange standard regeln för att tillåta nätverks åtkomst som standard.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Bevilja åtkomst från ett virtuellt nätverk

Du kan konfigurera Cognitive Services resurser så att endast åtkomst från vissa undernät tillåts. De tillåtna under näten kan tillhöra ett VNet i samma prenumeration eller i en annan prenumeration, inklusive prenumerationer som tillhör en annan Azure Active Directory klient.

Aktivera en [tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) för Azure Cognitive Services i VNet. Tjänst slut punkten dirigerar trafik från VNet via en optimal sökväg till Azure Cognitive Services-tjänsten. Identiteterna för under nätet och det virtuella nätverket överförs också med varje begäran. Administratörer kan sedan konfigurera nätverks regler för den Cognitive Services resurs som tillåter att förfrågningar tas emot från vissa undernät i ett VNet. Klienter som beviljats åtkomst via dessa nätverks regler måste fortsätta att uppfylla kraven för auktorisering av Cognitive Services resurs för att komma åt data.

Varje Cognitive Services-resurs har stöd för upp till 100 virtuella nätverks regler, som kan kombineras med [IP-nätverks-regler](#grant-access-from-an-internet-ip-range).

### <a name="required-permissions"></a>Behörigheter som krävs

Om du vill tillämpa en virtuell nätverks regel på en Cognitive Services resurs måste användaren ha rätt behörighet för de undernät som läggs till. Den behörighet som krävs är standard *deltagar* rollen eller rollen *Cognitive Services Contributor* . Nödvändiga behörigheter kan också läggas till i anpassade roll definitioner.

Cognitive Services resurs och de virtuella nätverk som beviljats åtkomst kan finnas i olika prenumerationer, inklusive prenumerationer som ingår i en annan Azure AD-klient.

> [!NOTE]
> Konfiguration av regler som beviljar åtkomst till undernät i virtuella nätverk som ingår i en annan Azure Active Directory klient stöds för närvarande bara via PowerShell-, CLI-och REST-API: er. Sådana regler kan inte konfigureras via Azure Portal, men de kan visas i portalen.

### <a name="managing-virtual-network-rules"></a>Hantera virtuella nätverks regler

Du kan hantera virtuella nätverks regler för Cognitive Services resurser via Azure Portal, PowerShell eller Azure CLI.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. Gå till den Cognitive Services resurs som du vill skydda.

1. Välj menyn **resurs hantering** som kallas **virtuellt nätverk**.

1. Kontrol lera att du har valt att tillåta åtkomst från **valda nätverk**.

1. Om du vill bevilja åtkomst till ett virtuellt nätverk med en befintlig nätverks regel väljer du **Lägg till befintligt virtuellt nätverk**under **virtuella nätverk**.

   ![Lägg till befintligt vNet](media/vnet/virtual-network-add-existing.png)

1. Välj alternativ för **virtuella nätverk** och **undernät** och välj sedan **Aktivera**.

   ![Lägg till befintlig vNet-information](media/vnet/virtual-network-add-existing-details.png)

1. Om du vill skapa ett nytt virtuellt nätverk och bevilja det åtkomst väljer du **Lägg till nytt virtuellt nätverk**.

   ![Lägg till nytt vNet](media/vnet/virtual-network-add-new.png)

1. Ange den information som krävs för att skapa det nya virtuella nätverket och välj sedan **skapa**.

   ![Skapa vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Om en tjänst slut punkt för Azure Cognitive Services inte tidigare har kon figurer ATS för det valda virtuella nätverket och under nätet, kan du konfigurera den som en del av den här åtgärden.
    >
    > För närvarande visas endast virtuella nätverk som hör till samma Azure Active Directory klient organisation för val av skapande av regel. Om du vill bevilja åtkomst till ett undernät i ett virtuellt nätverk som tillhör en annan klient organisation använder du PowerShell-, CLI-eller REST-API: er.

1. Om du vill ta bort ett virtuellt nätverk eller en under näts regel väljer du **...** för att öppna snabb menyn för det virtuella nätverket eller under nätet och välj **ta bort**.

   ![Ta bort vNet](media/vnet/virtual-network-remove.png)

1. Klicka på **Spara** för att tillämpa dina ändringar.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-az-ps) och [Logga](/powershell/azure/authenticate-azureps)in eller Välj **prova**.

1. Lista över virtuella nätverks regler.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Aktivera tjänst slut punkten för Azure Cognitive Services på ett befintligt virtuellt nätverk och undernät.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Lägg till en nätverks regel för ett virtuellt nätverk och undernät.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Om du vill lägga till en nätverks regel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klient använder du en fullständigt kvalificerad **VirtualNetworkResourceId** -parameter i formatet "/Subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-Name/subnets/Subnet-Name".

1. Ta bort en nätverks regel för ett virtuellt nätverk och undernät.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in, eller Välj **prova**.

1. Lista över virtuella nätverks regler.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Aktivera tjänst slut punkten för Azure Cognitive Services på ett befintligt virtuellt nätverk och undernät.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Lägg till en nätverks regel för ett virtuellt nätverk och undernät.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Om du vill lägga till en regel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klient använder du ett fullständigt kvalificerat undernät-ID i formatet "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".
    > 
    > Du kan använda parametern **Subscription** för att hämta Undernäts-ID: t för ett VNet som tillhör en annan Azure AD-klient.

1. Ta bort en nätverks regel för ett virtuellt nätverk och undernät.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

***

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) för **neka**eller att nätverks regler inte har någon påverkan.

## <a name="grant-access-from-an-internet-ip-range"></a>Bevilja åtkomst från ett IP-intervall på internet

Du kan konfigurera Cognitive Services-resurser för att tillåta åtkomst från vissa offentliga Internet-IP-adressintervall. Den här konfigurationen beviljar åtkomst till vissa tjänster och lokala nätverk, vilket effektivt blockerar allmän Internet trafik.

Ange tillåtna Internet adress intervall med [CIDR-notering](https://tools.ietf.org/html/rfc4632) i formuläret `16.17.18.0/24` eller som enskilda IP-adresser som `16.17.18.19` .

   > [!Tip]
   > Små adress intervall som använder sig av prefixlängden "/31" eller "/32" stöds inte. Dessa intervall ska konfigureras med hjälp av enskilda IP-adressintervall.

IP-nätverks regler tillåts endast för **offentliga Internet** -IP-adresser. IP-adressintervall som är reserverade för privata nätverk (enligt definitionen i [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) tillåts inte i IP-regler. Privata nätverk innehåller adresser som börjar med `10.*` , `172.16.*`  -  `172.31.*` , och `192.168.*` .

   > [!NOTE]
   > IP-nätverksanslutningar har ingen påverkan på begär Anden som kommer från samma Azure-region som den Cognitive Services resursen. Använd [regler för virtuella nätverk](#grant-access-from-a-virtual-network) för att tillåta begäran om samma region.

Endast IPV4-adresser stöds för tillfället. Varje Cognitive Services-resurs har stöd för upp till 100 IP-nätverksnummer som kan kombineras med [regler för virtuella nätverk](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurera åtkomst från lokala nätverk

Om du vill bevilja åtkomst från dina lokala nätverk till din Cognitive Services-resurs med en IP-nätverks regel måste du identifiera de Internet adresser som IP-adresser används i nätverket. Kontakta nätverks administratören om du behöver hjälp.

Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md) lokalt för offentlig peering eller Microsoft-peering måste du identifiera NAT IP-adresserna. För offentlig peering använder varje ExpressRoute-krets som standard två NAT-IP-adresser. Varje används för Azure-tjänstetrafik när trafiken går in Microsoft Azure nätverkets stamnät. De NAT-IP-adresser som används för Microsoft-peering är antingen kunden eller tillhandahålls av tjänst leverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du en supportbegäran hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Lär dig mer om [NAT för ExpressRoute offentliga peering och Microsoft-peering.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Hantera IP-nätverks regler

Du kan hantera IP-nätverks regler för Cognitive Services resurser via Azure Portal, PowerShell eller Azure CLI.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. Gå till den Cognitive Services resurs som du vill skydda.

1. Välj menyn **resurs hantering** som kallas **virtuellt nätverk**.

1. Kontrol lera att du har valt att tillåta åtkomst från **valda nätverk**.

1. Om du vill bevilja åtkomst till ett Internet-IP-intervall anger du IP-adressen eller adress intervallet (i [CIDR-format](https://tools.ietf.org/html/rfc4632)) under **brand Väggs**  >  **adress intervall**. Endast giltiga offentliga IP-adresser (icke-reserverade) accepteras.

   ![Lägg till IP-intervall](media/vnet/virtual-network-add-ip-range.png)

1. Om du vill ta bort en IP-nätverks regel väljer du pappers korgs <span class="docon docon-delete x-hidden-focus"></span> ikonen bredvid adress intervallet.

   ![Ta bort IP-intervall](media/vnet/virtual-network-delete-ip-range.png)

1. Klicka på **Spara** för att tillämpa dina ändringar.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-az-ps) och [Logga](/powershell/azure/authenticate-azureps)in eller Välj **prova**.

1. Lista IP-nätverksnummer.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Lägg till en nätverks regel för en enskild IP-adress.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Lägg till en nätverks regel för ett IP-adressintervall.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Ta bort en nätverks regel för en enskild IP-adress.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Ta bort en nätverks regel för ett IP-adressintervall.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in, eller Välj **prova**.

1. Lista IP-nätverksnummer.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Lägg till en nätverks regel för en enskild IP-adress.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Lägg till en nätverks regel för ett IP-adressintervall.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Ta bort en nätverks regel för en enskild IP-adress.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Ta bort en nätverks regel för ett IP-adressintervall.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) för **neka**eller att nätverks regler inte har någon påverkan.

## <a name="use-private-endpoints"></a>Använda privata slutpunkter

Du kan använda [privata slut punkter](../private-link/private-endpoint-overview.md) för Cognitive Services resurser för att tillåta klienter på ett virtuellt nätverk (VNet) att säkert få åtkomst till data via en [privat länk](../private-link/private-link-overview.md). Den privata slut punkten använder en IP-adress från VNet-adressutrymmet för din Cognitive Services-resurs. Nätverks trafik mellan klienterna i VNet och resursen passerar VNet och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet.

Med privata slut punkter för Cognitive Services resurser kan du:

* Skydda din Cognitive Services-resurs genom att konfigurera brand väggen för att blockera alla anslutningar på den offentliga slut punkten för tjänsten Cognitive Services.
* Öka säkerheten för VNet genom att göra det möjligt att blockera exfiltrering av data från det virtuella nätverket.
* Anslut säkert till Cognitive Services resurser från lokala nätverk som ansluter till VNet med [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-locations.md) med privat peering.

### <a name="conceptual-overview"></a>Begreppsmässig översikt

En privat slut punkt är ett särskilt nätverks gränssnitt för en Azure-resurs i ditt [VNet](../virtual-network/virtual-networks-overview.md). Att skapa en privat slut punkt för din Cognitive Services-resurs ger säker anslutning mellan klienter i ditt VNet och din resurs. Den privata slut punkten tilldelas en IP-adress från det virtuella nätverkets IP-adressintervall. Anslutningen mellan den privata slut punkten och Cognitive Services tjänsten använder en säker privat länk.

Program i det virtuella nätverket kan ansluta till tjänsten via den privata slut punkten sömlöst med samma anslutnings strängar och auktoriseringsbeslut som de skulle använda i övrigt. Undantaget är tal tjänsten som kräver en separat slut punkt. Se avsnittet om [privata slut punkter med tal tjänsten](#private-endpoints-with-the-speech-service). Privata slut punkter kan användas med alla protokoll som stöds av den Cognitive Services resursen, inklusive REST.

Privata slut punkter kan skapas i undernät som använder [tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md). Klienter i ett undernät kan ansluta till en Cognitive Services resurs med hjälp av privat slut punkt och använda tjänst slut punkter för att få åtkomst till andra.

När du skapar en privat slut punkt för en Cognitive Services-resurs i ditt VNet, skickas en begäran om godkännande till Cognitive Services resurs ägare. Om användaren som begär att den privata slut punkten ska skapas även är ägare till resursen, godkänns den här medgivande förfrågningen automatiskt.

Cognitive Services resurs ägare kan hantera medgivande förfrågningar och privata slut punkter via fliken "*privata slut punkter*" för resursen Cognitive Services i [Azure Portal](https://portal.azure.com).

### <a name="private-endpoints"></a>Privata slut punkter

När du skapar den privata slut punkten måste du ange Cognitive Services resursen som den ansluter till. Mer information om hur du skapar en privat slut punkt finns i:

* [Skapa en privat slut punkt med hjälp av det privata länk centret i Azure Portal](../private-link/create-private-endpoint-portal.md)
* [Skapa en privat slut punkt med Azure CLI](../private-link/create-private-endpoint-cli.md)
* [Skapa en privat slut punkt med hjälp av Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Ansluter till privata slut punkter

Klienter i ett VNet som använder den privata slut punkten bör använda samma anslutnings sträng för Cognitive Services resursen som klienter som ansluter till den offentliga slut punkten. Undantaget är tal tjänsten som kräver en separat slut punkt. Se avsnittet om [privata slut punkter med tal tjänsten](#private-endpoints-with-the-speech-service). Vi förlitar sig på DNS-matchning för att automatiskt dirigera anslutningarna från VNet till Cognitive Services resurs via en privat länk. Tal tjänsten 

Vi skapar en [privat DNS-zon](../dns/private-dns-overview.md) som är kopplad till det virtuella nätverket med nödvändiga uppdateringar för privata slut punkter som standard. Men om du använder en egen DNS-server kan du behöva göra ytterligare ändringar i DNS-konfigurationen. Avsnittet om [DNS-ändringar](#dns-changes-for-private-endpoints) nedan beskriver de uppdateringar som krävs för privata slut punkter.

### <a name="private-endpoints-with-the-speech-service"></a>Privata slut punkter med tal tjänsten

När du använder privata slut punkter med tal tjänsten måste du använda en anpassad slut punkt för att anropa tal tjänsten. Du kan inte använda den globala slut punkten. Slut punkten måste följa det här mönstret: `{account}.{stt|tts|voice|dls}.speech.microsoft.com` .

### <a name="dns-changes-for-private-endpoints"></a>DNS-ändringar för privata slut punkter

När du skapar en privat slut punkt uppdateras DNS CNAME-resursposten för Cognitive Services resursen till ett alias i en under domän med prefixet "*privatelink*". Som standard skapar vi också en [privat DNS-zon](../dns/private-dns-overview.md)som motsvarar under domänen "*privatelink*" med DNS a-resursposter för de privata slut punkterna.

När du löser slut punkts-URL: en från utanför VNet med den privata slut punkten matchas den offentliga slut punkten för Cognitive Services resursen. Vid matchning från det VNet som är värd för den privata slut punkten matchas slut punktens URL till den privata slut punktens IP-adress.

Den här metoden ger åtkomst till Cognitive Services resurs med samma anslutnings sträng för klienter i det virtuella nätverket som är värd för privata slut punkter och klienter utanför VNet.

Om du använder en anpassad DNS-server i nätverket måste klienterna kunna matcha det fullständigt kvalificerade domän namnet (FQDN) för Cognitive Services resurs slut punkten till den privata slut punktens IP-adress. Konfigurera DNS-servern så att den delegerar din privata länk under domän till det virtuella nätverkets privata DNS-zon.

> [!TIP]
> När du använder en anpassad eller lokal DNS-server bör du konfigurera DNS-servern så att den matchar Cognitive Services resurs namnet i under domänen "privatelink" till den privata slut punkten för IP-adressen. Du kan göra detta genom att delegera privatelink-underdomänen till det virtuella nätverkets privata DNS-zon eller konfigurera DNS-zonen på DNS-servern och lägga till DNS-posterna.

Mer information om hur du konfigurerar en egen DNS-server för att stödja privata slut punkter finns i följande artiklar:

* [Namnmatchning för resurser i virtuella nätverk i Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
* [DNS-konfiguration för privata slut punkter](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)

### <a name="pricing"></a>Prissättning

Pris information finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Nästa steg

* Utforska de olika [Azure-Cognitive Services](welcome.md)
* Läs mer om [Azure Virtual Network-tjänstens slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md)
