---
title: Konfigurera Azure Storage brand väggar och virtuella nätverk | Microsoft Docs
description: Konfigurera skiktad nätverks säkerhet för ditt lagrings konto med hjälp Azure Storage brand väggar och Azure Virtual Network.
services: storage
author: santoshc
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 8172abb5e220f28061c7826af24a5d9a2043f4ad
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219917"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurera brandväggar och virtuella nätverk i Azure Storage

Azure Storage tillhandahåller en skiktbaserad säkerhetsmodell. Med den här modellen kan du skydda och kontrol lera åtkomst nivån till dina lagrings konton som dina program och företags miljöer kräver, baserat på typen och delmängd av de nätverk eller resurser som används. När nätverks regler har kon figurer ATS kan endast program som begär data i den angivna uppsättningen nätverk eller via den angivna uppsättningen Azure-resurser komma åt ett lagrings konto. Du kan begränsa åtkomsten till ditt lagrings konto till begär Anden som kommer från angivna IP-adresser, IP-adressintervall, undernät i ett Azure-Virtual Network (VNet) eller resurs instanser av vissa Azure-tjänster.

Lagrings konton har en offentlig slut punkt som kan nås via Internet. Du kan också skapa [privata slut punkter för ditt lagrings konto](storage-private-endpoints.md), som tilldelar en privat IP-adress från ditt VNet till lagrings kontot och skyddar all trafik mellan ditt VNet och lagrings kontot över en privat länk. Azure Storage-brandväggen ger åtkomst kontroll för den offentliga slut punkten för ditt lagrings konto. Du kan också använda brand väggen för att blockera all åtkomst via den offentliga slut punkten när du använder privata slut punkter. Konfigurationen av lagrings brand väggen aktiverar även de betrodda Azure Platform-tjänsterna för att få åtkomst till lagrings kontot på ett säkert sätt.

Ett program som har åtkomst till ett lagrings konto när nätverks regler tillämpas kräver fortfarande korrekt auktorisering för begäran. Auktorisering stöds med Azure Active Directory (Azure AD)-autentiseringsuppgifter för blobbar och köer, med en giltig konto åtkomst nyckel eller med en SAS-token.

> [!IMPORTANT]
> Att aktivera brand Väggs regler för ditt lagrings konto blockerar inkommande begär Anden om data som standard, om inte begär Anden kommer från en tjänst som körs i ett Azure-Virtual Network (VNet) eller från tillåtna offentliga IP-adresser. Begär Anden som blockeras inkluderar de från andra Azure-tjänster, från Azure Portal, från loggnings-och mått tjänster och så vidare.
>
> Du kan bevilja åtkomst till Azure-tjänster som fungerar inifrån ett VNet genom att tillåta trafik från det undernät som är värd för tjänst instansen. Du kan också aktivera ett begränsat antal scenarier med hjälp av undantags mekanismen som beskrivs nedan. Om du vill komma åt data från lagrings kontot via Azure Portal måste du vara på en dator inom den betrodda gränser (antingen IP eller VNet) som du konfigurerar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenarier

Om du vill skydda ditt lagrings konto bör du först konfigurera en regel för att neka åtkomst till trafik från alla nätverk (inklusive Internet trafik) på den offentliga slut punkten som standard. Sedan bör du konfigurera regler som beviljar åtkomst till trafik från vissa virtuella nätverk. Du kan också konfigurera regler för att bevilja åtkomst till trafik från valda offentliga IP-adressintervall för Internet, vilket möjliggör anslutningar från vissa Internet-eller lokala klienter. Med den här konfigurationen kan du bygga en säker nätverks gränser för dina program.

Du kan kombinera brand Väggs regler som tillåter åtkomst från vissa virtuella nätverk och från offentliga IP-adressintervall på samma lagrings konto. Lagrings brand Väggs regler kan tillämpas på befintliga lagrings konton eller när du skapar nya lagrings konton.

Lagrings brand Väggs regler gäller för den offentliga slut punkten för ett lagrings konto. Du behöver inga brand Väggs åtkomst regler för att tillåta trafik för privata slut punkter för ett lagrings konto. Processen med att godkänna skapandet av en privat slut punkt ger implicit åtkomst till trafik från det undernät som är värd för den privata slut punkten.

Nätverks regler tillämpas på alla nätverks protokoll för Azure Storage, inklusive REST och SMB. Om du vill komma åt data med hjälp av verktyg som Azure Portal, Storage Explorer och AZCopy måste explicita nätverks regler konfigureras.

När nätverks reglerna tillämpas tillämpas de för alla begär Anden. SAS-token som beviljar åtkomst till en speciell IP-adress som gör att du kan begränsa åtkomsten till token-innehavaren, men inte bevilja ny åtkomst utöver konfigurerade nätverks regler.

Disk trafik på den virtuella datorn (inklusive åtgärder för montering och demontering och disk-i/o) påverkas inte av nätverks regler. REST-åtkomst till Page blobbar skyddas av nätverks regler.

Klassiska lagrings konton stöder inte brand väggar och virtuella nätverk.

Du kan använda ohanterade diskar i lagrings konton med nätverks regler som används för att säkerhetskopiera och återställa virtuella datorer genom att skapa ett undantag. Den här processen dokumenteras i avsnittet [Hantera undantag](#manage-exceptions) i den här artikeln. Brand Väggs undantag gäller inte för hanterade diskar eftersom de redan hanteras av Azure.

## <a name="change-the-default-network-access-rule"></a>Ändra standardåtkomstregeln för nätverk

Som standard godkänner lagringskonton anslutningar från klienter i alla nätverk. Om du vill begränsa åtkomsten till valda nätverk måste du först ändra standardåtgärden.

> [!WARNING]
> Om du gör ändringar av nätverksreglerna kan det påverka programmens möjlighet att ansluta till Azure Storage. Om du anger standard nätverks regeln till **neka** blockeras alla åtkomst till data, om inte vissa nätverks regler som **beviljar** åtkomst också tillämpas. Se till att bevilja åtkomst till alla tillåtna nätverk som använder nätverksregler innan du ändrar standardregeln för att neka åtkomst.

### <a name="managing-default-network-access-rules"></a>Hantera standardregler för nätverksåtkomst

Du kan hantera standard regler för nätverks åtkomst för lagrings konton via Azure Portal, PowerShell eller CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till det lagringskonto som du vill skydda.

2. Välj på menyn Inställningar som kallas **nätverk**.

3. Om du vill neka åtkomst som standard väljer du att tillåta åtkomst från **valda nätverk**. Om du vill tillåta trafik från alla nätverk väljer du att tillåta åtkomst från **Alla nätverk**.

4. Klicka på **Spara** för att tillämpa dina ändringar.

<a id="powershell"></a>

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) och [Logga](/powershell/azure/authenticate-azureps)in.

2. Visa status för standard regeln för lagrings kontot.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. Ange standard regeln för att neka nätverks åtkomst som standard.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. Ange standard regeln för att tillåta nätverks åtkomst som standard.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in.

2. Visa status för standard regeln för lagrings kontot.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. Ange standard regeln för att neka nätverks åtkomst som standard.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. Ange standard regeln för att tillåta nätverks åtkomst som standard.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```
---

## <a name="grant-access-from-a-virtual-network"></a>Bevilja åtkomst från ett virtuellt nätverk

Du kan konfigurera lagrings konton så att endast åtkomst från vissa undernät tillåts. De tillåtna under näten kan tillhöra ett VNet i samma prenumeration eller i en annan prenumeration, inklusive prenumerationer som tillhör en annan Azure Active Directory klient.

Aktivera en [tjänst slut punkt](../../virtual-network/virtual-network-service-endpoints-overview.md) för Azure Storage i VNet. Tjänst slut punkten dirigerar trafik från VNet via en optimal sökväg till Azure Storage tjänsten. Identiteterna för under nätet och det virtuella nätverket överförs också med varje begäran. Administratörer kan sedan konfigurera nätverks regler för det lagrings konto som tillåter att förfrågningar tas emot från vissa undernät i ett VNet. Klienter som beviljats åtkomst via dessa nätverks regler måste fortsätta att uppfylla kraven för auktorisering av lagrings kontot för att komma åt data.

Varje lagrings konto har stöd för upp till 200 virtuella nätverks regler, som kan kombineras med [IP-nätverks-regler](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Tillgängliga virtuella nätverks regioner

I allmänhet fungerar tjänst slut punkter mellan virtuella nätverk och tjänst instanser i samma Azure-region. När du använder tjänst slut punkter med Azure Storage växer det här omfånget för att inkludera den [kopplade regionen](../../best-practices-availability-paired-regions.md). Tjänst slut punkter tillåter kontinuitet under en regional redundans och till gång till skrivskyddade geo-redundanta lagrings instanser (RA-GRS). Nätverks regler som beviljar åtkomst från ett virtuellt nätverk till ett lagrings konto ger också åtkomst till valfri RA-GRS-instans.

När du planerar för haveri beredskap under ett regionalt avbrott bör du skapa virtuella nätverk i den kopplade regionen i förväg. Aktivera tjänstens slut punkter för Azure Storage med nätverks regler som beviljar åtkomst från dessa alternativa virtuella nätverk. Tillämpa sedan dessa regler på dina geo-redundanta lagrings konton.

> [!NOTE]
> Tjänstens slut punkter gäller inte för trafik utanför regionen för det virtuella nätverket och det angivna region paret. Du kan bara använda nätverks regler som beviljar åtkomst från virtuella nätverk till lagrings konton i den primära regionen för ett lagrings konto eller i den angivna kopplade regionen.

### <a name="required-permissions"></a>Behörigheter som krävs

Om du vill tillämpa en virtuell nätverks regel på ett lagrings konto måste användaren ha rätt behörighet för de undernät som läggs till. Den behörighet som krävs är *Anslut till ett undernät* och ingår i den inbyggda rollen *lagrings konto deltagare* . Den kan också läggas till i anpassade roll definitioner.

Lagrings kontot och de virtuella nätverk som beviljats åtkomst kan finnas i olika prenumerationer, inklusive prenumerationer som ingår i en annan Azure AD-klient.

> [!NOTE]
> Konfiguration av regler som beviljar åtkomst till undernät i virtuella nätverk som ingår i en annan Azure Active Directory klient stöds för närvarande bara via PowerShell-, CLI-och REST-API: er. Sådana regler kan inte konfigureras via Azure Portal, men de kan visas i portalen.

### <a name="managing-virtual-network-rules"></a>Hantera virtuella nätverks regler

Du kan hantera virtuella nätverks regler för lagrings konton via Azure Portal, PowerShell eller CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till det lagringskonto som du vill skydda.

2. Välj på menyn Inställningar som kallas **nätverk**.

3. Kontrol lera att du har valt att tillåta åtkomst från **valda nätverk**.

4. Om du vill bevilja åtkomst till ett virtuellt nätverk med en ny nätverks regel väljer du **Lägg till befintligt virtuellt nätverk** under **virtuella nätverk**, väljer alternativ för **virtuella** nätverk och **undernät** och väljer sedan **Lägg till**. Om du vill skapa ett nytt virtuellt nätverk och bevilja det åtkomst väljer du **Lägg till nytt virtuellt nätverk**. Ange den information som krävs för att skapa det nya virtuella nätverket och välj sedan **skapa**.

    > [!NOTE]
    > Om en tjänst slut punkt för Azure Storage inte tidigare har kon figurer ATS för det valda virtuella nätverket och under nätet, kan du konfigurera den som en del av den här åtgärden.
    >
    > För närvarande visas endast virtuella nätverk som hör till samma Azure Active Directory klient organisation för val av skapande av regel. Om du vill bevilja åtkomst till ett undernät i ett virtuellt nätverk som tillhör en annan klient organisation använder du PowerShell-, CLI-eller REST-API: er.

5. Om du vill ta bort ett virtuellt nätverk eller en under näts regel väljer du **...** för att öppna snabb menyn för det virtuella nätverket eller under nätet och välj **ta bort**.

6. Välj **Spara** för att tillämpa ändringarna.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) och [Logga](/powershell/azure/authenticate-azureps)in.

2. Lista över virtuella nätverks regler.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. Aktivera tjänstens slut punkt för Azure Storage på ett befintligt virtuellt nätverk och undernät.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. Lägg till en nätverks regel för ett virtuellt nätverk och undernät.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Om du vill lägga till en nätverks regel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klient använder du en fullständigt kvalificerad **VirtualNetworkResourceId** -parameter i formatet "/Subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-Name/subnets/Subnet-Name".

5. Ta bort en nätverks regel för ett virtuellt nätverk och undernät.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) för **neka** eller att nätverks regler inte har någon påverkan.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in.

2. Lista över virtuella nätverks regler.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. Aktivera tjänstens slut punkt för Azure Storage på ett befintligt virtuellt nätverk och undernät.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. Lägg till en nätverks regel för ett virtuellt nätverk och undernät.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Om du vill lägga till en regel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klient använder du ett fullständigt kvalificerat undernät-ID i formatet "/Subscriptions/ \<subscription-ID\> /ResourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /subnets/ \<subnet-name\> ".
    >
    > Du kan använda parametern **Subscription** för att hämta Undernäts-ID: t för ett VNet som tillhör en annan Azure AD-klient.

5. Ta bort en nätverks regel för ett virtuellt nätverk och undernät.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) för **neka** eller att nätverks regler inte har någon påverkan.

---

## <a name="grant-access-from-an-internet-ip-range"></a>Bevilja åtkomst från ett IP-intervall på internet

Du kan konfigurera lagrings konton så att de tillåter åtkomst från vissa offentliga IP-adressintervall för Internet. Den här konfigurationen beviljar åtkomst till vissa Internetbaserade tjänster och lokala nätverk och blockerar allmän Internet trafik.

Tillhandahålla tillåtna Internet adress intervall med [CIDR-notering](https://tools.ietf.org/html/rfc4632) i formatet *16.17.18.0/24* eller enskilda IP-adresser som *16.17.18.19*.

   > [!NOTE]
   > Små adress intervall som använder sig av prefixlängden "/31" eller "/32" stöds inte. Dessa intervall ska konfigureras med hjälp av enskilda IP-adressintervall.

IP-nätverks regler tillåts endast för **offentliga Internet** -IP-adresser. IP-adressintervall som är reserverade för privata nätverk (enligt definitionen i [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) tillåts inte i IP-regler. Privata nätverk innehåller adresser som börjar med _10. *_, _172,16. *_  -  _172,31. *_ och _192,168. *_.

   > [!NOTE]
   > IP-nätverksanslutningar har ingen påverkan på begär Anden som kommer från samma Azure-region som lagrings kontot. Använd [regler för virtuella nätverk](#grant-access-from-a-virtual-network) för att tillåta begäran om samma region.

  > [!NOTE]
  > Tjänster som distribueras i samma region som lagrings kontot använder privata Azure IP-adresser för kommunikation. Därför kan du inte begränsa åtkomsten till vissa Azure-tjänster baserat på deras offentliga utgående IP-adressintervall.

Endast IPV4-adresser stöds för konfiguration av lagrings brand Väggs regler.

Varje lagrings konto har stöd för upp till 200 IP-nätverksanslutningar.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurera åtkomst från lokala nätverk

Om du vill bevilja åtkomst från dina lokala nätverk till ditt lagrings konto med en IP-nätverks regel måste du identifiera de Internet adresser som IP-adresser används i nätverket. Kontakta nätverks administratören om du behöver hjälp.

Om du använder [ExpressRoute](../../expressroute/expressroute-introduction.md) lokalt för offentlig peering eller Microsoft-peering, måste du identifiera de NAT IP-adresser som används. För offentlig peering, använder varje ExpressRoute-krets som standard två NAT IP-adresser, som används för Azure-tjänsttrafik när trafiken kommer till Microsoft Azure-stamnätverket. För Microsoft-peering används de NAT-IP-adresser som används antingen för kunden eller tillhandahålls av tjänst leverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du en supportbegäran hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Lär dig mer om [NAT för ExpressRoute offentliga peering och Microsoft-peering.](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Hantera IP-nätverks regler

Du kan hantera IP-nätverks regler för lagrings konton via Azure Portal, PowerShell eller CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till det lagringskonto som du vill skydda.

2. Välj på menyn Inställningar som kallas **nätverk**.

3. Kontrol lera att du har valt att tillåta åtkomst från **valda nätverk**.

4. Om du vill bevilja åtkomst till ett Internet-IP-intervall anger du IP-adressen eller adress intervallet (i CIDR-format) under **brand Väggs**  >  **adress intervall**.

5. Om du vill ta bort en IP-nätverks regel väljer du pappers korgs ikonen bredvid adress intervallet.

6. Klicka på **Spara** för att tillämpa dina ändringar.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) och [Logga](/powershell/azure/authenticate-azureps)in.

2. Lista IP-nätverksnummer.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. Lägg till en nätverks regel för en enskild IP-adress.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. Lägg till en nätverks regel för ett IP-adressintervall.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. Ta bort en nätverks regel för en enskild IP-adress.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. Ta bort en nätverks regel för ett IP-adressintervall.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) för **neka** eller att nätverks regler inte har någon påverkan.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in.

1. Lista IP-nätverksnummer.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. Lägg till en nätverks regel för en enskild IP-adress.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. Lägg till en nätverks regel för ett IP-adressintervall.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. Ta bort en nätverks regel för en enskild IP-adress.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. Ta bort en nätverks regel för ett IP-adressintervall.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) för **neka** eller att nätverks regler inte har någon påverkan.

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Bevilja åtkomst från Azure Resource instances (för hands version)

I vissa fall kan ett program vara beroende av Azure-resurser som inte kan isoleras via ett virtuellt nätverk eller en IP-adress regel. Du vill dock fortfarande skydda och begränsa åtkomsten till lagrings kontot till endast ditt programs Azure-resurser. Du kan konfigurera lagrings konton för att tillåta åtkomst till vissa resurs instanser av vissa Azure-tjänster genom att skapa en resurs instans regel. 

De typer av åtgärder som en resurs instans kan utföra på lagrings konto data bestäms av resurs instansens [Azure-roll tilldelningar](storage-auth-aad.md#assign-azure-roles-for-access-rights) . Resurs instanser måste vara från samma klient organisation som ditt lagrings konto, men de kan tillhöra alla prenumerationer i klienten.

Listan med Azure-tjänster som stöds visas i avsnittet [betrodd åtkomst baserat på en hanterad identitet som tilldelats av systemet](#trusted-access-system-assigned-managed-identity) i den här artikeln.

> [!NOTE]
> Den här funktionen finns i en offentlig för hands version och är tillgänglig i alla offentliga moln regioner. 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Du kan lägga till eller ta bort nätverks regler för resurser i Azure Portal.

1. Kom igång genom att logga in på [Azure-portalen](https://portal.azure.com/).

2. Leta upp ditt lagringskonto och visa kontoöversikten.

3. Välj **nätverk** om du vill visa konfigurations sidan för nätverk.

4. I list rutan **resurs typ** väljer du resurs typen för resurs instansen. 

5. I list rutan **instans namn** väljer du resurs instansen. Du kan också välja att inkludera alla resurs instanser i den aktiva klienten, prenumerationen eller resurs gruppen.

6. Klicka på **Spara** för att tillämpa dina ändringar. Resurs instansen visas i avsnittet **resurs instanser** på sidan Nätverks inställningar. 

Om du vill ta bort resurs instansen väljer du borttagnings ikonen ( :::image type="icon" source="media/storage-network-security/delete-icon.png"::: ) bredvid resurs instansen.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan använda PowerShell-kommandon för att lägga till eller ta bort resurs nätverks regler.

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) för **neka** eller att nätverks regler inte har någon påverkan.

#### <a name="install-the-preview-module"></a>Installera Preview-modulen

Installera den senaste versionen av PowershellGet-modulen. Stäng sedan och öppna PowerShell-konsolen igen.

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

Installera **AZ. Storage** Preview module.

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

Mer information om hur du installerar PowerShell-moduler finns i [installera modulen Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

#### <a name="grant-access"></a>Bevilja åtkomst

Lägg till en nätverks regel som beviljar åtkomst från en resurs instans.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

Ange flera resurs instanser samtidigt genom att ändra nätverks regel uppsättningen.

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>Ta bort åtkomst

Ta bort en nätverks regel som beviljar åtkomst från en resurs instans.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

Ta bort alla nätverks regler som beviljar åtkomst från resurs instanser.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Visa en lista över tillåtna resurs instanser

Visa en fullständig lista över resurs instanser som har beviljats åtkomst till lagrings kontot.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan använda Azure CLI-kommandon för att lägga till eller ta bort resurs nätverks regler.

#### <a name="install-the-preview-extension"></a>Installera för hands versions tillägget

1. Öppna [Azure Cloud Shell](../../cloud-shell/overview.md), eller om du har [installerat](/cli/azure/install-azure-cli) Azure CLI lokalt öppnar du ett kommando konsol program, till exempel Windows PowerShell.

2. Kontrol lera sedan att den version av Azure CLI som du har installerat är `2.13.0` eller högre genom att använda följande kommando.

   ```azurecli
   az --version
   ```

   Om din version av Azure CLI är lägre än `2.13.0` kan du installera en senare version. Se [Installera Azure CLI](/cli/azure/install-azure-cli).

3. Skriv följande kommando för att installera förhands gransknings tillägget.

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>Bevilja åtkomst

Lägg till en nätverks regel som beviljar åtkomst från en resurs instans.

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>Ta bort åtkomst

Ta bort en nätverks regel som beviljar åtkomst från en resurs instans.

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Visa en lista över tillåtna resurs instanser

Visa en fullständig lista över resurs instanser som har beviljats åtkomst till lagrings kontot.

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-trusted-azure-services"></a>Bevilja åtkomst till betrodda Azure-tjänster 

Vissa Azure-tjänster körs från nätverk som inte kan ingå i dina nätverks regler. Du kan bevilja en delmängd av dessa betrodda Azure-tjänster åtkomst till lagrings kontot, samtidigt som nätverks reglerna för andra appar upprätthålls. Dessa betrodda tjänster använder sedan stark autentisering för att på ett säkert sätt ansluta till ditt lagrings konto.

Du kan bevilja åtkomst till betrodda Azure-tjänster genom att skapa en nätverks regel undantag. Steg-för-steg-anvisningar finns i avsnittet [Hantera undantag](#manage-exceptions) i den här artikeln.

När du beviljar åtkomst till betrodda Azure-tjänster ger du följande typer av åtkomst:

- Betrodd åtkomst för urvals åtgärder till resurser som har registrerats i din prenumeration.
- Betrodd åtkomst till resurser baserat på systemtilldelad hanterad identitet.

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>Betrodd åtkomst för resurser som är registrerade i din prenumeration

Resurser för vissa tjänster, **när de registreras i din prenumeration**, kan komma åt ditt lagrings konto **i samma prenumeration** för Select-åtgärder, till exempel skriva loggar eller säkerhets kopiering.  I följande tabell beskrivs varje tjänst och de åtgärder som tillåts. 

| Tjänst                  | Namn på resurs leverantör     | Tillåtna åtgärder                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft. RecoveryServices | Kör säkerhets kopiering och återställning av ohanterade diskar i virtuella IAAS-datorer. (krävs inte för Managed Disks). [Läs mer](../../backup/backup-overview.md). |
| Azure Data Box           | Microsoft. data-          | Gör det möjligt att importera data till Azure med hjälp av Data Box-enhet. [Läs mer](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft. DevTestLab       | Skapande av anpassad avbildning och artefakt installation. [Läs mer](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft. EventGrid        | Aktivera Blob Storage händelse publicering och Tillåt Event Grid att publicera till lagrings köer. Lär dig mer om [Blob Storage-händelser](../../event-grid/overview.md#event-sources) och [publicering till köer](../../event-grid/event-handlers.md). |
| Azure Event Hubs         | Microsoft. EventHub         | Arkivera data med Event Hubs avbildning. [Läs mer](../../event-hubs/event-hubs-capture-overview.md). |
| Azure File Sync          | Microsoft. StorageSync      | Gör att du kan omvandla din lokal fil server till ett cacheminne för Azure-filresurser. Tillåter synkronisering av flera platser, snabb katastrof återställning och säkerhets kopiering på moln sidan. [Läs mer](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft. HDInsight        | Etablera det inledande innehållet i standard fil systemet för ett nytt HDInsight-kluster. [Läs mer](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Azure import-export      | Microsoft. ImportExport     | Möjliggör import av data till Azure Storage eller export av data från Azure Storage med hjälp av Azure Storage import/export-tjänsten. [Läs mer](../../import-export/storage-import-export-service.md).  |
| Azure Monitor            | Microsoft. Insights         | Tillåter skrivning av övervaknings data till ett skyddat lagrings konto, inklusive resurs loggar, Azure Active Directory inloggnings-och gransknings loggar och Microsoft Intune loggar. [Läs mer](../../azure-monitor/platform/roles-permissions-security.md). |
| Azure-nätverk         | Microsoft.Network          | Lagra och analysera nätverks trafik loggar, inklusive via Network Watcher-och Trafikanalys-tjänsterna. [Läs mer](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Microsoft. SiteRecovery     | Aktivera replikering för haveri beredskap för virtuella Azure IaaS-datorer när du använder brand Väggs-aktiverade cache-, käll-eller mål lagrings konton.  [Läs mer](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>Betrodd åtkomst baserat på systemtilldelad hanterad identitet

I följande tabell visas tjänster som kan ha åtkomst till dina lagrings konto data om resurs instanserna för dessa tjänster får rätt behörighet. Om du vill bevilja behörighet måste du uttryckligen [tilldela en Azure-roll](storage-auth-aad.md#assign-azure-roles-for-access-rights) till den [systemtilldelade hanterade identiteten](../../active-directory/managed-identities-azure-resources/overview.md) för varje resurs instans. I det här fallet motsvarar åtkomst omfånget för instansen den Azure-roll som tilldelats den hanterade identiteten. 

> [!TIP]
> Det rekommenderade sättet att bevilja åtkomst till vissa resurser är att använda resurs instans regler. Information om hur du beviljar åtkomst till vissa resurs instanser finns i avsnittet [bevilja åtkomst från Azure Resource instances (för hands version)](#grant-access-specific-instances) i den här artikeln.


| Tjänst                        | Namn på resurs leverantör                 | Syfte            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | Aktiverar API Management-tjänstens åtkomst till lagrings konton bakom brand väggen med hjälp av principer. [Läs mer](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy). |
| Azure Cognitive Search         | Microsoft. search/searchServices        | Ger Kognitiv sökning-tjänster åtkomst till lagrings konton för indexering, bearbetning och frågor. |
| Azure Cognitive Services       | Microsoft. CognitiveService/konton    | Ger Cognitive Services åtkomst till lagrings konton. |
| Azure Container Registry Tasks | Microsoft. ContainerRegistry/register | ACR-aktiviteter kan komma åt lagrings konton när du skapar behållar avbildningar. |
| Azure Data Factory             | Microsoft. DataFactory/fabriker        | Ger åtkomst till lagrings konton via ADF-körningen. |
| Azure Data Share               | Microsoft. DataShare/konton           | Ger åtkomst till lagrings konton via data resurs. |
| Azure DevTest Labs             | Microsoft. DevTestLab/Labs              | Ger åtkomst till lagrings konton via DevTest Labs. |
| Azure IoT Hub                  | Microsoft. Devices/IotHubs              | Tillåter att data från en IoT-hubb skrivs till Blob Storage. [Läs mer](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft. Logic/arbets flöden              | Gör att Logic Apps kan komma åt lagrings konton. [Läs mer](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning-tjänsten | Microsoft.MachineLearningServices      | Auktoriserade Azure Machine Learning arbets ytor skriver experiment, modeller och loggar till Blob Storage och läser data. [Läs mer](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources). |
| Azure Media Services           | Microsoft. Media/Media Services          | Ger åtkomst till lagrings konton via Media Services. |
| Azure Migrate                  | Microsoft. Migrate/migrateprojects      | Ger åtkomst till lagrings konton via Azure Migrate. |
| Azure Purview                  | Microsoft. avdelningens kontroll/konton             | Tillåter avdelningens kontroll åtkomst till lagrings konton. |
| Azure Remote Rendering         | Microsoft. MixedReality/remoteRenderingAccounts | Ger åtkomst till lagrings konton via fjärrrendering. |
| Azure Site Recovery            | Microsoft. RecoveryServices/valv      | Ger åtkomst till lagrings konton via Site Recovery. |
| Azure SQL Database             | Microsoft.Sql                          | Tillåter att du [skriver](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) gransknings data till lagrings konton bakom brand väggen. |
| Azure Synapse Analytics        | Microsoft.Sql                          | Tillåter import och export av data från särskilda SQL-databaser med hjälp av KOPIERINGs instruktionen eller polybasen (i en dedikerad pool) eller `openrowset` funktionen och externa tabeller i en pool utan server. [Läs mer](../../azure-sql/database/vnet-service-endpoint-rule-overview.md). |
| Azure Stream Analytics         | Microsoft. StreamAnalytics              | Tillåter att data från ett strömmande jobb skrivs till Blob Storage. [Läs mer](../../stream-analytics/blob-output-managed-identity.md). |
| Azure Synapse Analytics        | Microsoft. Synapse/arbets ytor           | Ger åtkomst till data i Azure Storage från Azure Synapse Analytics. |

## <a name="grant-access-to-storage-analytics"></a>Bevilja åtkomst till lagrings analys

I vissa fall krävs åtkomst till läsa resurs loggar och mät värden utanför nätverks gränserna. När du konfigurerar betrodda tjänster till lagrings kontot kan du tillåta Läs åtkomst för loggfiler, mått tabeller eller både och genom att skapa en nätverks regel undantag. Steg-för-steg-anvisningar finns i avsnittet **Hantera undantag** nedan. Mer information om hur du arbetar med Storage Analytics finns i [använda Azure Storage Analytics för att samla in loggar och statistik data](./storage-analytics.md). 

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>Hantera undantag

Du kan hantera nätverks regel undantag via Azure Portal, PowerShell eller Azure CLI v2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till det lagringskonto som du vill skydda.

2. Välj på menyn Inställningar som kallas **nätverk**.

3. Kontrol lera att du har valt att tillåta åtkomst från **valda nätverk**.

4. Under **undantag** väljer du de undantag som du vill bevilja.

5. Klicka på **Spara** för att tillämpa dina ändringar.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) och [Logga](/powershell/azure/authenticate-azureps)in.

2. Visa undantagen för lagrings kontots nätverks regler.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. Konfigurera undantagen för lagrings kontots nätverks regler.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. Ta bort undantagen till lagrings kontots nätverks regler.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) som **neka** eller ta bort undantag har ingen påverkan.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in.

2. Visa undantagen för lagrings kontots nätverks regler.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. Konfigurera undantagen för lagrings kontots nätverks regler.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. Ta bort undantagen till lagrings kontots nätverks regler.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) som **neka** eller ta bort undantag har ingen påverkan.

---

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Network Service-slutpunkter i [tjänst slut punkter](../../virtual-network/virtual-network-service-endpoints-overview.md).

Gå djupare i Azure Storage säkerhet i [Azure Storage säkerhets guide](../blobs/security-recommendations.md).
