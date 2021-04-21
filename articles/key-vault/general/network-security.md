---
title: Konfigurera Azure Key Vault brandväggar och virtuella nätverk – Azure Key Vault
description: Stegvisa instruktioner för att konfigurera Key Vault och virtuella nätverk
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8352deb00f6954d862b9e44646cce1604e2c5428
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749626"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurera Azure Key Vault brandväggar och virtuella nätverk

Den här artikeln ger dig vägledning om hur du konfigurerar Azure Key Vault brandväggen. Det här dokumentet beskriver de olika konfigurationerna för Key Vault-brandväggen i detalj och innehåller stegvisa instruktioner om hur du konfigurerar Azure Key Vault att fungera med andra program och Azure-tjänster.

Mer information finns i [Tjänstslutpunkter för virtuellt nätverk för Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="firewall-settings"></a>Brandväggsinställningar

I det här avsnittet går vi in på de olika Azure Key Vault som brandväggen kan konfigureras på.

### <a name="key-vault-firewall-disabled-default"></a>Key Vault brandväggen inaktiverad (standard)

När du skapar ett nytt nyckelvalv inaktiveras som standard Azure Key Vault brandväggen. Alla program och Azure-tjänster kan komma åt nyckelvalvet och skicka begäranden till nyckelvalvet. Observera att den här konfigurationen inte innebär att alla användare kommer att kunna utföra åtgärder på ditt nyckelvalv. Nyckelvalvet begränsar fortfarande till hemligheter, nycklar och certifikat som lagras i nyckelvalvet genom att kräva Azure Active Directory behörighet för autentisering och åtkomstprincip. Mer information om autentisering av nyckelvalv finns i dokumentet om grundläggande nyckelvalvsautentisering [här.](./authentication-fundamentals.md) Mer information finns i Access [Azure Key Vault bakom en brandvägg.](./access-behind-firewall.md)

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Key Vault brandväggen aktiverad (endast betrodda tjänster)

När du aktiverar Key Vault-brandväggen får du ett alternativ för att "Tillåt betrodda Microsoft-tjänster att kringgå brandväggen". Listan över betrodda tjänster omfattar inte alla Azure-tjänster. Azure DevOps finns till exempel inte med i listan över betrodda tjänster. **Detta innebär inte att tjänster som inte visas i listan över betrodda tjänster inte är betrodda eller osäkra.** Listan över betrodda tjänster omfattar tjänster där Microsoft styr all kod som körs på tjänsten. Eftersom användare kan skriva anpassad kod i Azure-tjänster som Azure DevOps ger Microsoft inte möjlighet att skapa ett godkännande för tjänsten. Dessutom, bara för att en tjänst visas i listan över betrodda tjänster, innebär det inte att den tillåts för alla scenarier. 

Om du vill ta reda på om en tjänst som du försöker använda finns i listan över betrodda tjänster kan du se följande dokument [här.](./overview-vnet-service-endpoints.md#trusted-services)
Följ anvisningarna här för portalen, Azure CLI och [Powershell för instruktioner](https://docs.microsoft.com/azure/key-vault/general/network-security#use-the-azure-portal)

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Key Vault brandväggen aktiverad (IPv4-adresser och -intervall – statiska IP-adresser)

Om du vill ge en viss tjänst åtkomst till nyckelvalvet via Key Vault Firewall kan du lägga till tjänstens IP-adress i listan över tillåtna nyckelvalvsbrandväggen. Den här konfigurationen passar bäst för tjänster som använder statiska IP-adresser eller välkända intervall. Det finns en gräns på 1 000 CIDR-intervall för det här fallet.

Utför följande steg för att tillåta en IP-adress eller ett intervall för en Azure-resurs, till exempel en webbapp eller logikapp.

1. Logga in på Azure Portal
1. Välj resursen (specifik instans av tjänsten)
1. Klicka på bladet Egenskaper under Inställningar
1. Leta efter fältet "IP-adress".
1. Kopiera det här värdet eller intervallet och ange det i key vault-brandväggens lista över tillåtna värden.

Om du vill tillåta en hel Azure-tjänst Key Vault brandväggen använder du listan över offentligt dokumenterade datacenter-IP-adresser för Azure [här.](https://www.microsoft.com/download/details.aspx?id=41653) Hitta IP-adresserna som är associerade med den tjänst som du vill använda i den region som du vill använda och lägg till dessa IP-adresser i Key Vault-brandväggen med hjälp av stegen ovan.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Key Vault-brandväggen aktiverad (virtuella nätverk – dynamiska IP-adresser)

Om du försöker tillåta en Azure-resurs, till exempel en virtuell dator via nyckelvalvet, kanske du inte kan använda statiska IP-adresser och du kanske inte vill tillåta att alla IP-adresser för Azure Virtual Machines får åtkomst till ditt nyckelvalv.

I det här fallet bör du skapa resursen i ett virtuellt nätverk och sedan tillåta trafik från det specifika virtuella nätverket och undernätet att komma åt ditt nyckelvalv. Gör detta genom att utföra följande steg.

1. Logga in på Azure Portal
1. Välj det nyckelvalv som du vill konfigurera
1. Välj bladet Nätverk
1. Välj + Lägg till befintligt virtuellt nätverk
1. Välj det virtuella nätverk och undernät som du vill tillåta genom nyckelvalvsbrandväggen.

### <a name="key-vault-firewall-enabled-private-link"></a>Key Vault Firewall Enabled (Private Link)

Information om hur du konfigurerar en privat länkanslutning i nyckelvalvet finns i dokumentet [här.](./private-link-service.md)

> [!IMPORTANT]
> När brandväggsreglerna är i kraft kan användarna bara utföra Key Vault [dataplansåtgärder](security-overview.md#privileged-access) när deras begäranden kommer från tillåtna virtuella nätverk eller IPv4-adressintervall. Detta gäller även åtkomst Key Vault från Azure Portal. Även om användarna kan bläddra till ett nyckelvalv från Azure Portal kanske de inte kan lista nycklar, hemligheter eller certifikat om deras klientdator inte finns med i listan över tillåtna. Detta påverkar även Key Vault av andra Azure-tjänster. Användare kan eventuellt se en lista över nyckelvalv, men inte lista nycklar, om brandväggsregler förhindrar klientdatorn.

> [!NOTE]
> Tänk på följande konfigurationsbegränsningar:
> * Högst 127 regler för virtuellt nätverk och 127 IPv4-regler tillåts. 
> * IP-nätverksregler tillåts endast för offentliga IP-adresser. IP-adressintervall som är reserverade för privata nätverk (enligt definitionen i RFC 1918) tillåts inte i IP-regler. Privata nätverk innehåller adresser som börjar med **10.**, **172.16-31** och **192.168.**. 
> * Endast IPv4-adresser stöds för närvarande.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Så här konfigurerar du Key Vault och virtuella nätverk med hjälp av Azure Portal:

1. Bläddra till det nyckelvalv som du vill skydda.
2. Välj **Nätverk** och sedan fliken **Brandväggar och virtuella** nätverk.
3. Under **Tillåt åtkomst från** väljer du Valda **nätverk**.
4. Om du vill lägga till befintliga virtuella nätverk i brandväggar och regler för virtuella nätverk väljer **du + Lägg till befintliga virtuella nätverk**.
5. På det nya bladet som öppnas väljer du den prenumeration, de virtuella nätverk och undernät som du vill tillåta åtkomst till det här nyckelvalvet. Om de virtuella nätverk och undernät som du väljer inte har tjänstslutpunkter aktiverade, bekräftar du att du vill aktivera tjänstslutpunkter och väljer **Aktivera**. Det kan ta upp till 15 minuter att gälla.
6. Under **IP-nätverk** lägger du till IPv4-adressintervall genom att skriva IPv4-adressintervall i [CIDR-notation (Classless Inter-Domain Routing)](https://tools.ietf.org/html/rfc4632) eller enskilda IP-adresser.
7. Om du vill tillåta att Microsoft Trusted Services kringgår Key Vault Firewall väljer du Ja. En fullständig lista över aktuella Key Vault Betrodda tjänster finns på följande länk. [Azure Key Vault betrodda tjänster](./overview-vnet-service-endpoints.md#trusted-services)
7. Välj **Spara**.

Du kan också lägga till nya virtuella nätverk och undernät och sedan aktivera tjänstslutpunkter för de nyligen skapade virtuella nätverken och undernäten genom att välja + Lägg till **nytt virtuellt nätverk**. Följ sedan anvisningarna.

## <a name="use-the-azure-cli"></a>Använda Azure CLI 

Så här konfigurerar du Key Vault och virtuella nätverk med hjälp av Azure CLI

1. [Installera Azure CLI](/cli/azure/install-azure-cli) och [logga in](/cli/azure/authenticate-azure-cli).

2. Lista tillgängliga regler för virtuellt nätverk. Om du inte har angett några regler för det här nyckelvalvet är listan tom.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Aktivera en tjänstslutpunkt för Key Vault på ett befintligt virtuellt nätverk och undernät.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Lägg till en nätverksregel för ett virtuellt nätverk och undernät.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Lägg till ett IP-adressintervall som trafik tillåts från.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Om det här nyckelvalvet ska vara tillgängligt för alla betrodda tjänster anger du `bypass` till `AzureServices` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Aktivera nätverksregler genom att ange standardåtgärden till `Deny` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Så här konfigurerar du Key Vault och virtuella nätverk med hjälp av PowerShell:

1. Installera den senaste [Azure PowerShell](/powershell/azure/install-az-ps)och [logga in](/powershell/azure/authenticate-azureps).

2. Lista tillgängliga regler för virtuellt nätverk. Om du inte har angett några regler för det här nyckelvalvet är listan tom.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Aktivera tjänstslutpunkt för Key Vault på ett befintligt virtuellt nätverk och undernät.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Lägg till en nätverksregel för ett virtuellt nätverk och undernät.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Lägg till ett IP-adressintervall som trafik ska tillåtas från.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Om det här nyckelvalvet ska vara tillgängligt för alla betrodda tjänster anger du `bypass` till `AzureServices` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Aktivera nätverksregler genom att ange standardåtgärden till `Deny` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referenser
* ARM-mallreferens: [Azure Key Vault ARM-mallreferens](/azure/templates/Microsoft.KeyVault/vaults)
* Azure [CLI-kommandon: az keyvault network-rule](/cli/azure/keyvault/network-rule)
* Azure PowerShell cmdlets: [Get-AzKeyVault,](/powershell/module/az.keyvault/get-azkeyvault) [Add-AzKeyVaultNetworkRule,](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule) [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Nästa steg

* [Tjänstslutpunkter för virtuellt nätverk för Key Vault](overview-vnet-service-endpoints.md)
* [Azure Key Vault säkerhetsöversikt](security-overview.md)
