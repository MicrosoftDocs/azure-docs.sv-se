---
title: Konfigurera Azure Key Vault brand väggar och virtuella nätverk – Azure Key Vault
description: Steg för steg-anvisningar för att konfigurera Key Vault brand väggar och virtuella nätverk
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: sudbalas
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3639237a0dc34c521fd3fa52631fdb19c26ec284
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936353"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurera Azure Key Vault brand väggar och virtuella nätverk

I den här artikeln får du vägledning om hur du konfigurerar Azure Key Vault brand väggen. Det här dokumentet innehåller de olika konfigurationerna för Key Vault brand väggen i detalj, och innehåller stegvisa instruktioner för hur du konfigurerar Azure Key Vault att arbeta med andra program och Azure-tjänster.

Mer information finns i [tjänst slut punkter för virtuella nätverk för Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="firewall-settings"></a>Brand Väggs inställningar

Det här avsnittet beskriver de olika sätt som Azure Key Vault brand väggen kan konfigureras på.

### <a name="key-vault-firewall-disabled-default"></a>Key Vault brand väggen är inaktive rad (standard)

När du skapar ett nytt nyckel valv är Azure Key Vault brand väggen som standard inaktive rad. Alla program och Azure-tjänster har åtkomst till nyckel valvet och skickar begär anden till nyckel valvet. OBS! den här konfigurationen innebär inte att alla användare kommer att kunna utföra åtgärder i nyckel valvet. Nyckel valvet begränsar fortfarande till hemligheter, nycklar och certifikat som lagras i Key Vault genom att kräva Azure Active Directory autentiserings-och åtkomst princip behörigheter. Om du vill veta mer om Key Vault-autentisering mer detaljerat kan du läsa grunderna i Key Vault Authentication-dokumentet [här](./authentication-fundamentals.md).

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Key Vault brand vägg aktive rad (endast betrodda tjänster)

När du aktiverar Key Vault brand vägg får du ett alternativ för att tillåta att betrodda Microsoft-tjänster kringgår den här brand väggen. Listan över betrodda tjänster omfattar inte varje enskild Azure-tjänst. Till exempel finns inte Azure-DevOps på listan över betrodda tjänster. **Detta innebär inte att tjänster som inte finns med i listan över betrodda tjänster inte är betrodda eller oskyddade.** Listan över betrodda tjänster omfattar tjänster där Microsoft kontrollerar all kod som körs på tjänsten. Eftersom användarna kan skriva anpassad kod i Azure-tjänster som Azure DevOps, ger Microsoft inte möjlighet att skapa ett RAM godkännande för tjänsten. Dessutom är det inte tillåtet att använda en tjänst i listan över betrodda tjänster, men det är inte tillåtet för alla scenarier.

Om du vill ta reda på om en tjänst som du försöker använda finns i listan över betrodda tjänster kan du läsa följande dokument [här](./overview-vnet-service-endpoints.md#trusted-services).

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Key Vault brand vägg aktive rad (IPv4-adresser och intervall – statiska IP-adresser)

Om du vill auktorisera en viss tjänst för att få åtkomst till Key Vault via Key Vault brand väggen kan du lägga till dess IP-adress i listan över tillåtna brand vägg för nyckel valv. Den här konfigurationen är bäst för tjänster som använder statiska IP-adresser eller välkända intervall.

Utför följande steg för att tillåta en IP-adress eller ett intervall av en Azure-resurs, till exempel en webbapp eller en Logic app.

1. Logga in på Azure Portal
1. Välj resurs (en speciell instans av tjänsten)
1. Klicka på bladet "egenskaper" under "Inställningar"
1. Leta efter fältet "IP-adress".
1. Kopiera det här värdet eller området och ange det i listan över tillåtna i Key Vault-brandväggen.

Om du vill tillåta en hel Azure-tjänst går du till Key Vault brand väggen med hjälp av listan över offentligt dokumenterade IP-adresser för data Center för Azure [här](https://www.microsoft.com/download/details.aspx?id=41653). Hitta de IP-adresser som är kopplade till den tjänst som du vill ha i den region som du vill använda och Lägg till dessa IP-adresser i Key Vault-brand väggen med hjälp av stegen ovan.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Key Vault brand vägg aktive rad (virtuella nätverk – dynamiska IP-adresser)

Om du försöker tillåta en Azure-resurs, till exempel en virtuell dator via Key Vault, kanske du inte kan använda statiska IP-adresser och du kanske inte vill att alla IP-adresser för Azure Virtual Machines ska få åtkomst till ditt nyckel valv.

I det här fallet bör du skapa resursen i ett virtuellt nätverk och sedan tillåta trafik från det angivna virtuella nätverket och under nätet att komma åt ditt nyckel valv. Det gör du genom att utföra följande steg.

1. Logga in på Azure Portal
1. Välj det nyckel valv som du vill konfigurera
1. Välj bladet nätverk
1. Välj "+ Lägg till befintligt virtuellt nätverk"
1. Välj det virtuella nätverk och undernät som du vill tillåta via nyckel valvs brand väggen.

### <a name="key-vault-firewall-enabled-private-link"></a>Key Vault brand vägg aktive rad (privat länk)

Information om hur du konfigurerar en privat länk anslutning i ditt nyckel valv finns i dokumentet [här](./private-link-service.md).

> [!IMPORTANT]
> När brand Väggs reglerna är aktiva kan användarna bara utföra Key Vault [data Plans](secure-your-key-vault.md#data-plane-access-control) åtgärder när deras begär Anden härstammar från tillåtna virtuella nätverk eller IPv4-adress intervall. Detta gäller även för att komma åt Key Vault från Azure Portal. Även om användarna kan bläddra till ett nyckel valv från Azure Portal, kanske de inte kan lista nycklar, hemligheter eller certifikat om deras klient dator inte finns i listan över tillåtna. Detta påverkar också Key Vault väljare från andra Azure-tjänster. Användarna kanske kan se en lista över nyckel valv, men inte lista nycklar, om brand Väggs reglerna förhindrar sin klient dator.

> [!NOTE]
> Tänk på följande konfigurations begränsningar:
> * Högst 127 virtuella nätverks regler och 127 IPv4-regler är tillåtna. 
> * IP-nätverksadresser tillåts endast för offentliga IP-adresser. IP-adressintervall som är reserverade för privata nätverk (enligt definitionen i RFC 1918) tillåts inte i IP-regler. Privata nätverk innehåller adresser som börjar med **10.**, **172.16-31** och **192,168.**.. 
> * Endast IPv4-adresser stöds för tillfället.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Så här konfigurerar du Key Vault brand väggar och virtuella nätverk med hjälp av Azure Portal:

1. Bläddra till det nyckel valv som du vill skydda.
2. Välj **nätverk** och välj sedan fliken **brand väggar och virtuella nätverk** .
3. Under **Tillåt åtkomst från** väljer du **valda nätverk**.
4. Om du vill lägga till befintliga virtuella nätverk i brand väggar och regler för virtuella nätverk väljer du **+ Lägg till befintliga virtuella nätverk**.
5. På det nya bladet som öppnas väljer du den prenumeration, de virtuella nätverk och undernät som du vill ge åtkomst till det här nyckel valvet. Om de virtuella nätverk och undernät du väljer inte har aktiverat tjänst slut punkter, bekräftar du att du vill aktivera tjänstens slut punkter och väljer **Aktivera**. Det kan ta upp till 15 minuter att börja gälla.
6. Under **IP-nätverk** lägger du till IPv4-adress intervall genom att ange IPv4-adress intervall i [CIDR-notering (Classless Inter-Domain routing)](https://tools.ietf.org/html/rfc4632) eller enskilda IP-adresser.
7. Välj Ja om du vill tillåta att Microsoft-betrodda tjänster kringgår Key Vault brand väggen. En fullständig lista över aktuella Key Vault betrodda tjänster finns i följande länk. [Azure Key Vault betrodda tjänster](./overview-vnet-service-endpoints.md#trusted-services)
7. Välj **Spara**.

Du kan också lägga till nya virtuella nätverk och undernät och sedan aktivera tjänstens slut punkter för de nyligen skapade virtuella nätverken och under näten genom att välja **+ Lägg till nytt virtuellt nätverk**. Följ sedan anvisningarna.

## <a name="use-the-azure-cli"></a>Använda Azure CLI 

Så här konfigurerar du Key Vault brand väggar och virtuella nätverk med hjälp av Azure CLI

1. [Installera Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in.

2. Visa lista över tillgängliga virtuella nätverks regler. Om du inte har angett några regler för det här nyckel valvet kommer listan att vara tom.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Aktivera en tjänst slut punkt för Key Vault på ett befintligt virtuellt nätverk och undernät.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Lägg till en nätverks regel för ett virtuellt nätverk och undernät.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Lägg till ett IP-adressintervall som trafiken ska tillåtas från.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Om det här nyckel valvet ska vara tillgängligt för alla betrodda tjänster anger `bypass` du till `AzureServices` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Aktivera nätverks reglerna genom att ställa in standard åtgärden till `Deny` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Så här konfigurerar du Key Vault brand väggar och virtuella nätverk med hjälp av PowerShell:

1. Installera den senaste [Azure PowerShell](/powershell/azure/install-az-ps)och [Logga](/powershell/azure/authenticate-azureps)in.

2. Visa lista över tillgängliga virtuella nätverks regler. Om du inte har angett några regler för det här nyckel valvet kommer listan att vara tom.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Aktivera tjänstens slut punkt för Key Vault på ett befintligt virtuellt nätverk och undernät.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Lägg till en nätverks regel för ett virtuellt nätverk och undernät.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Lägg till ett IP-adressintervall som trafiken ska tillåtas från.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Om det här nyckel valvet ska vara tillgängligt för alla betrodda tjänster anger `bypass` du till `AzureServices` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Aktivera nätverks reglerna genom att ställa in standard åtgärden till `Deny` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referenser
* Referens för ARM-mall: [referens för Azure Key Vault arm-mall](/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI-kommandon: [AZ-nätverk-regel](/cli/azure/keyvault/network-rule)
* Azure PowerShell cmdlet: [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Nästa steg

* [Tjänst slut punkter för virtuella nätverk för Key Vault](overview-vnet-service-endpoints.md)
* [Översikt över Azure Key Vault säkerhet](security-overview.md)