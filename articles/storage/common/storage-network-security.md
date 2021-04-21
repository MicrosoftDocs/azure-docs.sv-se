---
title: Konfigurera Azure Storage brandväggar och virtuella nätverk | Microsoft Docs
description: Konfigurera nätverkssäkerhet på flera lager för ditt lagringskonto med hjälp Azure Storage brandväggar och Azure Virtual Network.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 5e8123c252d99b2999eeef42fecae189a05e382b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778129"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurera brandväggar och virtuella nätverk i Azure Storage

Azure Storage tillhandahåller en skiktbaserad säkerhetsmodell. Med den här modellen kan du skydda och styra åtkomstnivån till dina lagringskonton som dina program och företagsmiljöer kräver, baserat på typ och delmängd av nätverk eller resurser som används. När nätverksregler har konfigurerats kan endast program som begär data via den angivna uppsättningen nätverk eller via den angivna uppsättningen Azure-resurser komma åt ett lagringskonto. Du kan begränsa åtkomsten till ditt lagringskonto till begäranden som kommer från angivna IP-adresser, IP-intervall, undernät i ett Azure Virtual Network (VNet) eller resursinstanser av vissa Azure-tjänster.

Lagringskonton har en offentlig slutpunkt som kan nås via Internet. Du kan också skapa privata slutpunkter för ditt lagringskonto , som tilldelar en privat IP-adress från ditt VNet till [lagringskontot](storage-private-endpoints.md)och skyddar all trafik mellan ditt VNet och lagringskontot via en privat länk. Azure Storage-brandväggen ger åtkomstkontroll för den offentliga slutpunkten för ditt lagringskonto. Du kan också använda brandväggen för att blockera all åtkomst via den offentliga slutpunkten när du använder privata slutpunkter. Konfigurationen av lagringsbrandväggen gör det också möjligt att välja betrodda Azure-plattformstjänster för säker åtkomst till lagringskontot.

Ett program som har åtkomst till ett lagringskonto när nätverksregler tillämpas kräver fortfarande rätt auktorisering för begäran. Auktorisering stöds med Azure Active Directory autentiseringsuppgifter (Azure AD) för blobar och köer, med en giltig åtkomstnyckel för kontot eller med en SAS-token.

> [!IMPORTANT]
> Om du startar brandväggsregler för ditt lagringskonto blockeras inkommande begäranden om data som standard, såvida inte begärandena kommer från en tjänst som fungerar i ett Azure Virtual Network (VNet) eller från tillåtna offentliga IP-adresser. Begäranden som blockeras är bland annat från andra Azure-tjänster, från Azure Portal, från loggnings- och måtttjänster och så vidare.
>
> Du kan bevilja åtkomst till Azure-tjänster som fungerar inifrån ett VNet genom att tillåta trafik från det undernät som är värd för tjänstinstansen. Du kan också aktivera ett begränsat antal scenarier via undantagsmekanismen som beskrivs nedan. För att komma åt data från lagringskontot via Azure Portal måste du vara på en dator inom den betrodda gränsen (antingen IP eller VNet) som du har ställt in.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenarier

För att skydda ditt lagringskonto bör du först konfigurera en regel för att neka åtkomst till trafik från alla nätverk (inklusive Internettrafik) på den offentliga slutpunkten som standard. Sedan bör du konfigurera regler som beviljar åtkomst till trafik från specifika virtuella nätverk. Du kan också konfigurera regler för att bevilja åtkomst till trafik från valda offentliga IP-adressintervall för Internet, vilket aktiverar anslutningar från specifika Internet- eller lokala klienter. Med den här konfigurationen kan du skapa en säker nätverksgräns för dina program.

Du kan kombinera brandväggsregler som tillåter åtkomst från specifika virtuella nätverk och från offentliga IP-adressintervall på samma lagringskonto. Brandväggsregler för lagring kan tillämpas på befintliga lagringskonton eller när du skapar nya lagringskonton.

Regler för lagringsbrandvägg gäller för den offentliga slutpunkten för ett lagringskonto. Du behöver inga brandväggsregler för att tillåta trafik för privata slutpunkter för ett lagringskonto. Processen att godkänna skapandet av en privat slutpunkt ger implicit åtkomst till trafik från undernätet som är värd för den privata slutpunkten.

Nätverksregler tillämpas på alla nätverksprotokoll för Azure Storage, inklusive REST och SMB. För att komma åt data med verktyg som Azure Portal, Storage Explorer och AZCopy måste explicita nätverksregler konfigureras.

När nätverksregler tillämpas tillämpas de för alla begäranden. SAS-token som beviljar åtkomst till en specifik IP-adress begränsar åtkomsten för tokeninnehavaren, men beviljar inte ny åtkomst utöver konfigurerade nätverksregler.

Virtuell datordisktrafik (inklusive monterings- och demontera-åtgärder och disk-I/O) påverkas inte av nätverksregler. REST-åtkomst till sidblobar skyddas av nätverksregler.

Klassiska lagringskonton stöder inte brandväggar och virtuella nätverk.

Du kan använda ohanterade diskar i lagringskonton med nätverksregler som tillämpas för att backa upp och återställa virtuella datorer genom att skapa ett undantag. Den här processen dokumenteras i [avsnittet Hantera undantag](#manage-exceptions) i den här artikeln. Brandväggundantag gäller inte för hanterade diskar eftersom de redan hanteras av Azure.

## <a name="change-the-default-network-access-rule"></a>Ändra standardåtkomstregeln för nätverk

Som standard godkänner lagringskonton anslutningar från klienter i alla nätverk. Om du vill begränsa åtkomsten till valda nätverk måste du först ändra standardåtgärden.

> [!WARNING]
> Om du gör ändringar av nätverksreglerna kan det påverka programmens möjlighet att ansluta till Azure Storage. Om du anger **standardnätverksregeln till neka** blockeras all åtkomst till data om inte specifika nätverksregler **som** beviljar åtkomst också tillämpas. Se till att bevilja åtkomst till alla tillåtna nätverk som använder nätverksregler innan du ändrar standardregeln för att neka åtkomst.

### <a name="managing-default-network-access-rules"></a>Hantera standardregler för nätverksåtkomst

Du kan hantera standardregler för nätverksåtkomst för lagringskonton via Azure Portal, PowerShell eller CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till det lagringskonto som du vill skydda.

2. Välj på inställningsmenyn **Nätverk**.

3. Om du vill neka åtkomst som standard väljer du att tillåta åtkomst från **Valda nätverk**. Om du vill tillåta trafik från alla nätverk väljer du att tillåta åtkomst från **Alla nätverk**.

4. Klicka på **Spara** för att tillämpa dina ändringar.

<a id="powershell"></a>

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) [och logga in](/powershell/azure/authenticate-azureps).

2. Visa status för standardregeln för lagringskontot.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. Ställ in standardregeln på att neka nätverksåtkomst som standard.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. Ange standardregeln för att tillåta nätverksåtkomst som standard.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI och](/cli/azure/install-azure-cli) [logga in](/cli/azure/authenticate-azure-cli).

2. Visa status för standardregeln för lagringskontot.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. Ställ in standardregeln på att neka nätverksåtkomst som standard.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. Ange standardregeln för att tillåta nätverksåtkomst som standard.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```
---

## <a name="grant-access-from-a-virtual-network"></a>Bevilja åtkomst från ett virtuellt nätverk

Du kan konfigurera lagringskonton så att de endast tillåter åtkomst från specifika undernät. De tillåtna undernäten kan tillhöra ett VNet i samma prenumeration eller i en annan prenumeration, inklusive prenumerationer som tillhör en annan Azure Active Directory klientorganisation.

Aktivera en [tjänstslutpunkt](../../virtual-network/virtual-network-service-endpoints-overview.md) för Azure Storage i det virtuella nätverket. Tjänstslutpunkten dirigerar trafik från det virtuella nätverket via en optimal sökväg till Azure Storage tjänsten. Identiteterna för undernätet och det virtuella nätverket överförs också med varje begäran. Administratörer kan sedan konfigurera nätverksregler för lagringskontot som tillåter att begäranden tas emot från specifika undernät i ett VNet. Klienter som beviljas åtkomst via dessa nätverksregler måste fortsätta att uppfylla auktoriseringskraven för lagringskontot för att komma åt data.

Varje lagringskonto stöder upp till 200 regler för virtuellt nätverk, som kan kombineras med [IP-nätverksregler.](#grant-access-from-an-internet-ip-range)

### <a name="available-virtual-network-regions"></a>Tillgängliga regioner för virtuella nätverk

I allmänhet fungerar tjänstslutpunkter mellan virtuella nätverk och tjänstinstanser i samma Azure-region. När du använder tjänstslutpunkter Azure Storage det här omfånget till att omfatta [den parkopplade regionen](../../best-practices-availability-paired-regions.md). Tjänstslutpunkter ger kontinuitet under en regional redundans och åtkomst till skrivskyddade instanser av geo-redundant lagring (RA-GRS). Nätverksregler som beviljar åtkomst från ett virtuellt nätverk till ett lagringskonto beviljar också åtkomst till ra-GRS-instanser.

När du planerar för haveriberedskap under ett regionalt avbrott bör du skapa de virtuella nätverken i den parkopplade regionen i förväg. Aktivera tjänstslutpunkter för Azure Storage, med nätverksregler som beviljar åtkomst från dessa alternativa virtuella nätverk. Tillämpa sedan dessa regler på dina geo-redundanta lagringskonton.

> [!NOTE]
> Tjänstslutpunkter gäller inte för trafik utanför regionen för det virtuella nätverket och det avsedda regionparet. Du kan bara tillämpa nätverksregler som beviljar åtkomst från virtuella nätverk till lagringskonton i den primära regionen för ett lagringskonto eller i den angivna parkopplade regionen.

### <a name="required-permissions"></a>Behörigheter som krävs

Om du vill använda en regel för virtuella nätverk med ett lagringskonto måste användaren ha rätt behörighet för de undernät som läggs till. Att tillämpa en regel [](../../role-based-access-control/built-in-roles.md#storage-account-contributor) kan utföras av en lagringskontodeltagare eller en användare som har fått behörighet till `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` [Azure-resursprovideråtgärden](../../role-based-access-control/resource-provider-operations.md#microsoftnetwork) via en anpassad Azure-roll.

Lagringskontot och de virtuella nätverk som beviljas åtkomst kan finnas i olika prenumerationer, inklusive prenumerationer som ingår i en annan Azure AD-klientorganisation.

> [!NOTE]
> Konfiguration av regler som beviljar åtkomst till undernät i virtuella nätverk som ingår i en annan Azure Active Directory-klientorganisation stöds för närvarande endast via Powershell, CLI och REST API:er. Sådana regler kan inte konfigureras via Azure Portal, även om de kan visas i portalen.

### <a name="managing-virtual-network-rules"></a>Hantera regler för virtuellt nätverk

Du kan hantera regler för virtuella nätverk för lagringskonton via Azure Portal, PowerShell eller CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till det lagringskonto som du vill skydda.

2. Välj på inställningsmenyn **Nätverk**.

3. Kontrollera att du har valt att tillåta åtkomst från **Valda nätverk**.

4. Om du vill bevilja åtkomst till ett virtuellt nätverk med en ny  nätverksregel **går** du till Virtuella nätverk, väljer Lägg till befintligt virtuellt **nätverk,** väljer Virtuella nätverk och **undernätsalternativ** och väljer sedan **Lägg till.** Om du vill skapa ett nytt virtuellt nätverk och ge det åtkomst väljer du **Lägg till nytt virtuellt nätverk**. Ange den information som krävs för att skapa det nya virtuella nätverket och välj sedan **Skapa.**

    > [!NOTE]
    > Om en tjänstslutpunkt för Azure Storage inte tidigare har konfigurerats för det valda virtuella nätverket och undernäten kan du konfigurera den som en del av den här åtgärden.
    >
    > För närvarande visas endast virtuella nätverk som tillhör Azure Active Directory klientorganisation för val när regeln skapas. Om du vill bevilja åtkomst till ett undernät i ett virtuellt nätverk som tillhör en annan klientorganisation använder du PowerShell, CLI eller REST API:er.

5. Om du vill ta bort ett virtuellt nätverk eller en undernätsregel väljer du **...** för att öppna snabbmenyn för det virtuella nätverket eller undernätet och väljer Ta **bort**.

6. Välj **Spara** för att tillämpa ändringarna.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) [och logga in](/powershell/azure/authenticate-azureps).

2. Lista regler för virtuellt nätverk.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. Aktivera tjänstslutpunkt för Azure Storage på ett befintligt virtuellt nätverk och undernät.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. Lägg till en nätverksregel för ett virtuellt nätverk och undernät.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Om du vill lägga till en nätverksregel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klientorganisation använder du en fullständigt kvalificerad **Parameter för VirtualNetworkResourceId** i formuläret "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

5. Ta bort en nätverksregel för ett virtuellt nätverk och undernät.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Se till att [ange standardregeln till](#change-the-default-network-access-rule) **neka**, annars har nätverksregler ingen effekt.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI och](/cli/azure/install-azure-cli) [logga in](/cli/azure/authenticate-azure-cli).

2. Lista regler för virtuellt nätverk.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. Aktivera tjänstslutpunkt för Azure Storage på ett befintligt virtuellt nätverk och undernät.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. Lägg till en nätverksregel för ett virtuellt nätverk och undernät.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Om du vill lägga till en regel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klientorganisation använder du ett fullständigt kvalificerat undernäts-ID i formuläret "/subscriptions/ \<subscription-ID\> /resourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /subnets/ \<subnet-name\> ".
    >
    > Du kan använda **prenumerationsparametern** för att hämta undernäts-ID:t för ett VNet som tillhör en annan Azure AD-klientorganisation.

5. Ta bort en nätverksregel för ett virtuellt nätverk och undernät.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Se till att [ange standardregeln till](#change-the-default-network-access-rule) **neka**, annars har nätverksregler ingen effekt.

---

## <a name="grant-access-from-an-internet-ip-range"></a>Bevilja åtkomst från ett IP-intervall för Internet

Du kan använda IP-nätverksregler för att tillåta åtkomst från specifika ip-adressintervall för offentliga Internet genom att skapa IP-nätverksregler. Varje lagringskonto stöder upp till 200 regler. Dessa regler beviljar åtkomst till specifika Internetbaserade tjänster och lokala nätverk och blockerar allmän Internettrafik.

Följande begränsningar gäller för IP-adressintervall.

- IP-nätverksregler tillåts  endast för offentliga Internet-IP-adresser. 

  IP-adressintervall som är reserverade för privata nätverk (enligt [definitionen i RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) tillåts inte i IP-regler. Privata nätverk innehåller adresser som börjar med _10.*_, _172.16.*_  -  _172.31.*_ och _192.168.*_.

- Du måste ange tillåtna Internetadressintervall med [CIDR-notation](https://tools.ietf.org/html/rfc4632) i form *av 16.17.18.0/24* eller som enskilda IP-adresser som *16.17.18.19*. 

- Små adressintervall med prefixstorlekarna "/31" eller "/32" stöds inte. Dessa intervall bör konfigureras med hjälp av enskilda IP-adressregler. 

- Endast IPV4-adresser stöds för konfiguration av lagringsbrandväggsregler.

IP-nätverksregler kan inte användas i följande fall:

- Begränsa åtkomsten till klienter i samma Azure-region som lagringskontot.
  
  IP-nätverksregler har ingen effekt på begäranden som kommer från samma Azure-region som lagringskontot. Använd [regler för virtuellt](#grant-access-from-a-virtual-network) nätverk för att tillåta begäranden i samma region. 

- Begränsa åtkomsten till klienter i en [länkad region som](../../best-practices-availability-paired-regions.md) finns i ett VNet som har en tjänstslutpunkt.

- Begränsa åtkomsten till Azure-tjänster som distribueras i samma region som lagringskontot.

  Tjänster som distribueras i samma region som lagringskontot använder privata Azure IP-adresser för kommunikation. Därför kan du inte begränsa åtkomsten till specifika Azure-tjänster baserat på deras offentliga utgående IP-adressintervall.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurera åtkomst från lokala nätverk

Om du vill bevilja åtkomst från dina lokala nätverk till ditt lagringskonto med en IP-nätverksregel måste du identifiera de Internetuppriktade IP-adresser som används av nätverket. Kontakta nätverksadministratören om du behöver hjälp.

Om du använder [ExpressRoute](../../expressroute/expressroute-introduction.md) lokalt för offentlig peering eller Microsoft-peering, måste du identifiera de NAT IP-adresser som används. För offentlig peering, använder varje ExpressRoute-krets som standard två NAT IP-adresser, som används för Azure-tjänsttrafik när trafiken kommer till Microsoft Azure-stamnätverket. För Microsoft-peering är de NAT IP-adresser som används antingen kundtilldelar eller tillhandahålls av tjänstleverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du en supportbegäran hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Lär dig mer om [NAT för ExpressRoute offentliga peering och Microsoft-peering.](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Hantera IP-nätverksregler

Du kan hantera IP-nätverksregler för lagringskonton via Azure Portal, PowerShell eller CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till det lagringskonto som du vill skydda.

2. Välj på inställningsmenyn **Nätverk**.

3. Kontrollera att du har valt att tillåta åtkomst från **Valda nätverk**.

4. Om du vill bevilja åtkomst till ett Internet-IP-intervall anger du IP-adress eller adressintervall (i CIDR-format) under   >  **Brandväggsadressintervall**.

5. Om du vill ta bort en IP-nätverksregel väljer du papperskorgsikonen bredvid adressintervallet.

6. Klicka på **Spara** för att tillämpa dina ändringar.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) [och logga in](/powershell/azure/authenticate-azureps).

2. Visa en lista över IP-nätverksregler.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. Lägg till en nätverksregel för en enskild IP-adress.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. Lägg till en nätverksregel för ett IP-adressintervall.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. Ta bort en nätverksregel för en enskild IP-adress.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. Ta bort en nätverksregel för ett IP-adressintervall.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Se till att [ange standardregeln till](#change-the-default-network-access-rule) neka , **annars** har nätverksregler ingen effekt.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI och](/cli/azure/install-azure-cli) [logga in](/cli/azure/authenticate-azure-cli).

1. Visa en lista över IP-nätverksregler.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. Lägg till en nätverksregel för en enskild IP-adress.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. Lägg till en nätverksregel för ett IP-adressintervall.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. Ta bort en nätverksregel för en enskild IP-adress.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. Ta bort en nätverksregel för ett IP-adressintervall.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Se till att [ange standardregeln till](#change-the-default-network-access-rule) neka , **annars** har nätverksregler ingen effekt.

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Bevilja åtkomst från Azure-resursinstanser (förhandsversion)

I vissa fall kan ett program vara beroende av Azure-resurser som inte kan isoleras via ett virtuellt nätverk eller en IP-adressregel. Du vill dock fortfarande skydda och begränsa lagringskontots åtkomst till endast programmets Azure-resurser. Du kan konfigurera lagringskonton för att tillåta åtkomst till specifika resursinstanser för vissa Azure-tjänster genom att skapa en resursinstansregel. 

Vilka typer av åtgärder som en resursinstans kan utföra på lagringskontodata bestäms av [Azure-rolltilldelningarna](storage-auth-aad.md#assign-azure-roles-for-access-rights) för resursinstansen. Resursinstanser måste komma från samma klientorganisation som ditt lagringskonto, men de kan tillhöra valfri prenumeration i klientorganisationen.

> [!NOTE]
> Den här funktionen är i offentlig förhandsversion och är tillgänglig i alla offentliga molnregioner.

> [!NOTE]
> Resursinstansregler stöds för närvarande endast för Azure Synapse. Stöd för andra Azure-tjänster som anges i avsnittet Betrodd åtkomst baserat på [system](#trusted-access-system-assigned-managed-identity) tilldelad hanterad identitet i den här artikeln kommer att vara tillgängligt under de kommande veckorna.


### <a name="portal"></a>[Portal](#tab/azure-portal)

Du kan lägga till eller ta bort resursnätverksregler i Azure Portal.

1. Kom igång genom att logga in på [Azure-portalen](https://portal.azure.com/).

2. Leta upp ditt lagringskonto och visa kontoöversikten.

3. Välj **Nätverk** för att visa konfigurationssidan för nätverk.

4. I **listrutan Resurstyp** väljer du resurstypen för din resursinstans. 

5. I **listrutan Instansnamn** väljer du resursinstansen. Du kan också välja att inkludera alla resursinstanser i den aktiva klientorganisationen, prenumerationen eller resursgruppen.

6. Klicka på **Spara** för att tillämpa dina ändringar. Resursinstansen visas i **avsnittet Resursinstanser** på sidan nätverksinställningar. 

Om du vill ta bort resursinstansen väljer du borttagningsikonen ( :::image type="icon" source="media/storage-network-security/delete-icon.png"::: ) bredvid resursinstansen.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan använda PowerShell-kommandon för att lägga till eller ta bort resursnätverksregler.

> [!IMPORTANT]
> Se till att [ange standardregeln till](#change-the-default-network-access-rule) **neka**, annars har nätverksregler ingen effekt.

#### <a name="install-the-preview-module"></a>Installera förhandsversionsmodulen

Installera den senaste versionen av PowershellGet-modulen. Stäng och öppna PowerShell-konsolen igen.

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

Installera **Az. Storage** Preview-modulen.

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

Mer information om hur du installerar PowerShell-moduler finns [i Installera Azure PowerShell modulen](/powershell/azure/install-az-ps)

#### <a name="grant-access"></a>Bevilja åtkomst

Lägg till en nätverksregel som beviljar åtkomst från en resursinstans.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

Ange flera resursinstanser samtidigt genom att ändra nätverksregeluppsättningen.

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>Ta bort åtkomst

Ta bort en nätverksregel som beviljar åtkomst från en resursinstans.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

Ta bort alla nätverksregler som beviljar åtkomst från resursinstanser.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Visa en lista över tillåtna resursinstanser

Visa en fullständig lista över resursinstanser som har beviljats åtkomst till lagringskontot.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan använda Azure CLI-kommandon för att lägga till eller ta bort resursnätverksregler.

#### <a name="install-the-preview-extension"></a>Installera förhandsversionstillägget

1. Öppna [Azure Cloud Shell](../../cloud-shell/overview.md), eller om du [](/cli/azure/install-azure-cli) har installerat Azure CLI lokalt öppnar du ett kommandokonsolprogram, till exempel Windows PowerShell.

2. Kontrollera sedan att den version av Azure CLI som du har installerat är `2.13.0` eller högre med hjälp av följande kommando.

   ```azurecli
   az --version
   ```

   Om din version av Azure CLI är lägre än `2.13.0` installerar du en senare version. Se [Installera Azure CLI.](/cli/azure/install-azure-cli)

3. Skriv följande kommando för att installera förhandsversionstillägget.

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>Bevilja åtkomst

Lägg till en nätverksregel som beviljar åtkomst från en resursinstans.

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>Ta bort åtkomst

Ta bort en nätverksregel som beviljar åtkomst från en resursinstans.

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Visa en lista över tillåtna resursinstanser

Visa en fullständig lista över resursinstanser som har beviljats åtkomst till lagringskontot.

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-trusted-azure-services"></a>Bevilja åtkomst till betrodda Azure-tjänster 

Vissa Azure-tjänster fungerar från nätverk som inte kan ingå i dina nätverksregler. Du kan bevilja en delmängd av sådana betrodda Azure-tjänster åtkomst till lagringskontot, samtidigt som nätverksregler för andra appar upprätthålls. Dessa betrodda tjänster använder sedan stark autentisering för att på ett säkert sätt ansluta till ditt lagringskonto.

Du kan bevilja åtkomst till betrodda Azure-tjänster genom att skapa ett undantag för nätverksregeln. Stegvisa anvisningar finns i avsnittet Hantera [undantag i](#manage-exceptions) den här artikeln.

När du beviljar åtkomst till betrodda Azure-tjänster beviljar du följande typer av åtkomst:

- Betrodd åtkomst för utvalda åtgärder till resurser som är registrerade i din prenumeration.
- Betrodd åtkomst till resurser baserat på system tilldelad hanterad identitet.

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>Betrodd åtkomst för resurser som registrerats i din prenumeration

Resurser för vissa tjänster, **när de är** registrerade  i din prenumeration , kan komma åt ditt lagringskonto i samma prenumeration för utvalda åtgärder, till exempel att skriva loggar eller säkerhetskopiera.  I följande tabell beskrivs varje tjänst och vilka åtgärder som tillåts. 

| Tjänst                  | Resursproviderns namn     | Tillåtna åtgärder                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | Kör säkerhetskopior och återställningar av ohanterade diskar på virtuella IAAS-datorer. (krävs inte för hanterade diskar). [Läs mer](../../backup/backup-overview.md). |
| Azure Data Box           | Microsoft.DataBox          | Möjliggör import av data till Azure med hjälp av Data Box-enhet. [Läs mer](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Skapa anpassade avbildningar och installera artefakter. [Läs mer](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft.EventGrid        | Aktivera Blob Storage publicering av händelser och Event Grid att publicera till lagringsköer. Lär dig mer [om bloblagringshändelser](../../event-grid/overview.md#event-sources) [och publicering till köer](../../event-grid/event-handlers.md). |
| Azure Event Hubs         | Microsoft.EventHub         | Arkivera data med Event Hubs Capture. [Läs mer](../../event-hubs/event-hubs-capture-overview.md). |
| Azure File Sync          | Microsoft.StorageSync      | Med det här alternativet kan du omvandla den virtuella filservern till ett cacheminne för Azure-filresurser. Möjliggör synkronisering av flera webbplatser, snabb haveriberedskap och säkerhetskopiering på molnsidan. [Läs mer](../file-sync/file-sync-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Etablera det första innehållet i standardfilsystemet för ett nytt HDInsight-kluster. [Läs mer](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Azure Import Export      | Microsoft.ImportExport     | Gör det möjligt att importera data Azure Storage eller exportera data från Azure Storage med hjälp Azure Storage Import/Export-tjänsten. [Läs mer](../../import-export/storage-import-export-service.md).  |
| Azure Monitor            | Microsoft.Insights         | Tillåter skrivning av övervakningsdata till ett skyddat lagringskonto, inklusive resursloggar, Azure Active Directory inloggnings- och granskningsloggar och Microsoft Intune loggar. [Läs mer](../../azure-monitor/roles-permissions-security.md). |
| Azure-nätverkstjänster         | Microsoft.Network          | Lagra och analysera nätverkstrafikloggar, inklusive via Network Watcher och Trafikanalys tjänster. [Läs mer](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Aktivera replikering för haveriberedskap för virtuella Azure IaaS-datorer när du använder brandväggsaktiverad cache, källa eller mållagringskonton.  [Läs mer](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>Betrodd åtkomst baserat på system tilldelad hanterad identitet

I följande tabell visas tjänster som kan ha åtkomst till dina lagringskontodata om resursinstanserna för dessa tjänster har rätt behörighet. Om du vill bevilja behörighet måste du uttryckligen [tilldela en Azure-roll till](storage-auth-aad.md#assign-azure-roles-for-access-rights) den system tilldelade [hanterade identiteten](../../active-directory/managed-identities-azure-resources/overview.md) för varje resursinstans. I det här fallet motsvarar åtkomstomfånget för instansen den Azure-roll som tilldelats den hanterade identiteten. 

> [!TIP]
> Det rekommenderade sättet att bevilja åtkomst till specifika resurser är att använda resursinstansregler. Information om hur du beviljar åtkomst till specifika resursinstanser finns i [avsnittet Bevilja åtkomst från Azure-resursinstanser (förhandsversion)](#grant-access-specific-instances) i den här artikeln.


| Tjänst                        | Resursproviderns namn                 | Syfte            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | Ger Api Management-tjänsten åtkomst till lagringskonton bakom brandväggen med hjälp av principer. [Läs mer](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy). |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | Ger Cognitive Search åtkomst till lagringskonton för indexering, bearbetning och frågor. |
| Azure Cognitive Services       | Microsoft.CognitiveService/accounts    | Ger Cognitive Services åtkomst till lagringskonton. |
| Azure Container Registry Tasks | Microsoft.ContainerRegistry/registries | ACR-uppgifter kan komma åt lagringskonton när du skapar containeravbildningar. |
| Azure Data Factory             | Microsoft.DataFactory/factories        | Tillåter åtkomst till lagringskonton via ADF-körningen. |
| Azure Data Share               | Microsoft.DataShare/accounts           | Tillåter åtkomst till lagringskonton via Data Share. |
| Azure DevTest Labs             | Microsoft.DevTestLab/labs              | Ger åtkomst till lagringskonton via DevTest Labs. |
| Azure IoT Hub                  | Microsoft.Devices/IotHubs              | Tillåter att data från en IoT-hubb skrivs till Blob Storage. [Läs mer](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft.Logic/workflows              | Gör det möjligt för logikappar att komma åt lagringskonton. [Läs mer](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning-tjänsten | Microsoft.MachineLearningServices      | Auktoriserade Azure Machine Learning skriver experimentutdata, modeller och loggar till Blob Storage och läser data. [Läs mer](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources). |
| Azure Media Services           | Microsoft.Media/mediaservices          | Ger åtkomst till lagringskonton via Media Services. |
| Azure Migrate                  | Microsoft.Migrate/migrateprojects      | Ger åtkomst till lagringskonton via Azure Migrate. |
| Azure Purview                  | Microsoft.Purview/accounts             | Tillåter Purview att komma åt lagringskonton. |
| Azure Remote Rendering         | Microsoft.MixedReality/remoteRenderingAccounts | Ger åtkomst till lagringskonton via Remote Rendering. |
| Azure Site Recovery            | Microsoft.RecoveryServices/vaults      | Ger åtkomst till lagringskonton via Site Recovery. |
| Azure SQL Database             | Microsoft.Sql                          | Tillåter [skrivning](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) av granskningsdata till lagringskonton bakom brandväggen. |
| Azure Synapse Analytics        | Microsoft.Sql                          | Tillåter import och export av data från specifika SQL-databaser med copy-instruktionen eller PolyBase (i dedikerad pool) eller funktionen och externa tabeller `openrowset` i en serverlös pool. [Läs mer](../../azure-sql/database/vnet-service-endpoint-rule-overview.md). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics              | Tillåter att data från ett strömningsjobb skrivs till Blob Storage. [Läs mer](../../stream-analytics/blob-output-managed-identity.md). |
| Azure Synapse Analytics        | Microsoft.Synapse/workspaces           | Ger åtkomst till data i Azure Storage från Azure Synapse Analytics. |

## <a name="grant-access-to-storage-analytics"></a>Bevilja åtkomst till lagringsanalys

I vissa fall krävs åtkomst till att läsa resursloggar och mått utanför nätverksgränsen. När du konfigurerar åtkomst för betrodda tjänster till lagringskontot kan du tillåta läsåtkomst för loggfiler, måtttabeller eller båda genom att skapa ett undantag för nätverksregeln. Stegvisa anvisningar finns i avsnittet **Hantera undantag** nedan. Mer information om hur du arbetar med lagringsanalys finns i [Använda Azure Storage analytics för att samla in loggar och måttdata.](./storage-analytics.md) 

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>Hantera undantag

Du kan hantera undantag i nätverksregeln via Azure Portal, PowerShell eller Azure CLI v2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till det lagringskonto som du vill skydda.

2. Välj på inställningsmenyn **nätverk**.

3. Kontrollera att du har valt att tillåta åtkomst från **Valda nätverk**.

4. Under **Undantag** väljer du de undantag som du vill bevilja.

5. Klicka på **Spara** för att tillämpa dina ändringar.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) [och logga in](/powershell/azure/authenticate-azureps).

2. Visa undantagen för nätverksreglerna för lagringskontot.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. Konfigurera undantagen till nätverksreglerna för lagringskontot.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. Ta bort undantagen från nätverksreglerna för lagringskontot.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Se till att [ange att standardregeln](#change-the-default-network-access-rule) **nekar**, eller att ta bort undantag har ingen effekt.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI och](/cli/azure/install-azure-cli) [logga in](/cli/azure/authenticate-azure-cli).

2. Visa undantagen för nätverksreglerna för lagringskontot.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. Konfigurera undantagen till nätverksreglerna för lagringskontot.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. Ta bort undantagen från nätverksreglerna för lagringskontot.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Se till att [ange att standardregeln](#change-the-default-network-access-rule) **nekar**, eller att ta bort undantag har ingen effekt.

---

## <a name="next-steps"></a>Nästa steg

Läs mer om tjänstslutpunkter för Azure-nätverk [i Tjänstslutpunkter.](../../virtual-network/virtual-network-service-endpoints-overview.md)

Gå djupare in Azure Storage säkerhet i [Azure Storage säkerhetsguide.](../blobs/security-recommendations.md)