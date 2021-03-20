---
title: Konfigurera Virtual Network-baserad åtkomst för ett Azure Cosmos-konto
description: I det här dokumentet beskrivs de steg som krävs för att konfigurera en tjänst slut punkt för virtuellt nätverk för Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d63d21f4c49e3c7aef035208477ac9fc79f2e51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94637191"
---
# <a name="configure-access-to-azure-cosmos-db-from-virtual-networks-vnet"></a>Konfigurera åtkomst till Azure Cosmos DB från virtuella nätverk (VNet)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Du kan konfigurera Azure Cosmos-kontot så att det bara tillåter åtkomst från ett speciellt undernät i ett virtuellt nätverk (VNet). Genom att aktivera [tjänstens slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) för att komma åt Azure Cosmos db i under nätet i ett virtuellt nätverk skickas trafiken från det under nätet till Azure Cosmos dB med under nätets identitet och Virtual Network. När Azure Cosmos DB tjänstens slut punkt har Aktiver ATS kan du begränsa åtkomsten till under nätet genom att lägga till den i ditt Azure Cosmos-konto.

Som standard är ett Azure Cosmos-konto tillgängligt från alla källor om begäran åtföljs av en giltig autentiseringstoken. När du lägger till ett eller flera undernät inom virtuella nätverk får endast begär Anden som kommer från dessa undernät ett giltigt svar. Begär Anden som kommer från en annan källa får ett 403-svar (ej tillåtet). 

Du kan konfigurera Azure Cosmos DB konton för att enbart tillåta åtkomst från ett speciellt undernät i ett virtuellt Azure-nätverk. Så här begränsar du åtkomsten till ett Azure Cosmos DB konto med anslutningar från ett undernät i ett virtuellt nätverk:

1. Aktivera under nätet för att skicka undernät och virtuell nätverks identitet till Azure Cosmos DB. Du kan åstadkomma detta genom att aktivera en tjänst slut punkt för Azure Cosmos DB på det angivna under nätet.

1. Lägg till en regel i Azure Cosmos DB kontot för att ange under nätet som en källa från vilken kontot kan nås.

> [!NOTE]
> När en tjänst slut punkt för ditt Azure Cosmos DB-konto är aktive rad i ett undernät är källan till den trafik som når Azure Cosmos DB växlar från en offentlig IP-adress till ett virtuellt nätverk och ett undernät. Trafik växlingen gäller för alla Azure Cosmos DB-konton som nås från det här under nätet. Om dina Azure Cosmos DB-konton har en IP-baserad brand vägg för att tillåta det här under nätet, matchar inte längre begär Anden från det tjänstbaserade under nätet IP-brandväggens regler och de avvisas.
>
> Mer information finns i de steg som beskrivs i avsnittet [Migrera från en IP-brandvägg till en lista över virtuella nätverks åtkomst kontrol listor](#migrate-from-firewall-to-vnet) i den här artikeln.

I följande avsnitt beskrivs hur du konfigurerar en tjänst slut punkt för virtuella nätverk för ett Azure Cosmos DB-konto.

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Konfigurera en tjänst slut punkt med hjälp av Azure Portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurera en tjänst slut punkt för ett befintligt virtuellt Azure-nätverk och undernät

1. Från bladet **alla resurser** letar du upp det Azure Cosmos DB konto som du vill skydda.

1. Välj **brand väggar och virtuella nätverk** på menyn Inställningar och välj att tillåta åtkomst från **valda nätverk**.

1. Om du vill bevilja åtkomst till ett befintligt virtuellt nätverks undernät går du till **virtuella nätverk** och väljer **Lägg till befintligt virtuellt Azure-nätverk**.

1. Välj den **prenumeration** som du vill lägga till ett virtuellt Azure-nätverk från. Välj de virtuella Azure- **nätverk** och **undernät** som du vill ge åtkomst till ditt Azure Cosmos DB-konto. Välj sedan **Aktivera** för att aktivera valda nätverk med tjänst slut punkter för "Microsoft. AzureCosmosDB". När den är klar väljer du **Lägg till**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Välj virtuellt nätverk och undernät":::

1. När Azure Cosmos DB-kontot har Aktiver ATS för åtkomst från ett virtuellt nätverk kommer det att tillåta trafik från endast det valda under nätet. Det virtuella nätverk och undernät som du har lagt till ska visas som visas på följande skärm bild:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Virtuellt nätverk och undernät har kon figurer ATS":::

> [!NOTE]
> Om du vill aktivera tjänst slut punkter för virtuella nätverk måste du ha följande prenumerations behörigheter:
>   * Prenumeration med virtuellt nätverk: nätverks deltagare
>   * Prenumeration med Azure Cosmos DB konto: DocumentDB Account Contributor
>   * Om ditt virtuella nätverk och Azure Cosmos DB konto finns i olika prenumerationer, kontrollerar du att prenumerationen som har ett virtuellt nätverk också har en `Microsoft.DocumentDB` registrerad resurs leverantör. Information om hur du registrerar en resurs leverantör finns i artikeln [Azure Resource providers och types](../azure-resource-manager/management/resource-providers-and-types.md) .

Här följer anvisningar för att registrera prenumeration med Resource Provider.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurera en tjänst slut punkt för ett nytt virtuellt Azure-nätverk och undernät

1. Från bladet **alla resurser** letar du upp det Azure Cosmos DB konto som du vill skydda.  

1. Välj **brand väggar och virtuella Azure-nätverk** på menyn Inställningar och välj att tillåta åtkomst från **valda nätverk**.  

1. Om du vill bevilja åtkomst till ett nytt virtuellt Azure-nätverk går du till **virtuella nätverk** och väljer **Lägg till nytt virtuellt nätverk**.  

1. Ange den information som krävs för att skapa ett nytt virtuellt nätverk och välj sedan **skapa**. Under nätet skapas med en tjänst slut punkt för "Microsoft. AzureCosmosDB" aktive rad.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Välj ett virtuellt nätverk och undernät för ett nytt virtuellt nätverk":::

Om ditt Azure Cosmos DB-konto används av andra Azure-tjänster som Azure Kognitiv sökning eller nås från Stream Analytics eller Power BI kan du tillåta åtkomst genom att välja **acceptera anslutningar från globala Azure-datacenter**.

För att se till att du har åtkomst till Azure Cosmos DB mått från portalen måste du aktivera **Tillåt åtkomst från Azure Portal** alternativ. Mer information om de här alternativen finns i artikeln [Konfigurera en IP-brandvägg](how-to-configure-firewall.md) . När du har aktiverat åtkomst väljer du **Spara** för att spara inställningarna.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Ta bort ett virtuellt nätverk eller undernät

1. Från bladet **alla resurser** letar du reda på det Azure Cosmos DB konto som du har tilldelat tjänstens slut punkter för.  

1. Välj **brand väggar och virtuella nätverk** på menyn Inställningar.  

1. Om du vill ta bort ett virtuellt nätverk eller en under näts regel väljer du **...** bredvid det virtuella nätverket eller under nätet och väljer **ta bort**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Ta bort ett virtuellt nätverk":::

1. Klicka på **Spara** för att tillämpa dina ändringar.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Konfigurera en tjänst slut punkt med hjälp av Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd följande steg för att konfigurera en tjänst slut punkt till ett Azure Cosmos DB konto genom att använda Azure PowerShell:  

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) och [Logga](/powershell/azure/authenticate-azureps)in.  

1. Aktivera tjänstens slut punkt för ett befintligt undernät i ett virtuellt nätverk.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

   > [!NOTE]
   > När du använder PowerShell eller Azure CLI måste du ange den fullständiga listan med IP-filter och ACL: er för virtuella nätverk i parametrar, inte bara de som behöver läggas till.

1. Hämta information om virtuellt nätverk.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Förbereda en regel för Cosmos DB Virtual Network

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Uppdatera Azure Cosmos DB konto egenskaper med den nya Virtual Network slut punkts konfigurationen: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Kör följande kommando för att kontrol lera att ditt Azure Cosmos DB-konto har uppdaterats med den virtuella nätverks tjänst slut punkt som du konfigurerade i föregående steg:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Konfigurera en tjänst slut punkt med hjälp av Azure CLI

Azure Cosmos-konton kan konfigureras för tjänst slut punkter när de skapas eller uppdateras vid ett senare tillfälle om under nätet redan har kon figurer ATS för dem. Tjänst slut punkter kan också aktive ras på Cosmos-kontot där under nätet inte har kon figurer ATS än och sedan börjar fungera när under nätet konfigureras senare. Den här flexibiliteten gör det möjligt för administratörer som inte har åtkomst till både Cosmos-kontot och virtuella nätverks resurser att göra sina konfigurationer oberoende av varandra.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Skapa ett nytt Cosmos-konto och Anslut det till ett Server dels under nät för ett nytt virtuellt nätverk

I det här exemplet skapas det virtuella nätverket och under nätet med tjänst slut punkter som är aktiverade för båda när de skapas.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Anslut och konfigurera ett Cosmos-konto till ett Server dels under nät oberoende av varandra

Det här exemplet är avsett att visa hur du ansluter ett Azure Cosmos-konto till ett befintligt nytt virtuellt nätverk där under nätet ännu inte har kon figurer ATS för tjänst slut punkter. Detta görs med hjälp av `--ignore-missing-vnet-service-endpoint` parametern. Detta gör att konfigurationen av Cosmos-kontot kan slutföras utan fel innan konfigurationen till det virtuella nätverkets undernät har slutförts. När under näts konfigurationen är klar kommer Cosmos-kontot att vara tillgängligt via det konfigurerade under nätet.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="port-range-when-using-direct-mode"></a>Port intervall när Direct-läge används

När du använder tjänst slut punkter med ett Azure Cosmos-konto via en anslutning i direkt läge, måste du se till att TCP-portnumret från 10000 till 20000 är öppen.

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrera från en IP-brandväggsregel till en virtuell nätverks-ACL

Använd följande steg om du vill migrera ett Azure Cosmos DB konto från att använda IP-brandvägg för att använda tjänst slut punkter för virtuella nätverk.

När ett Azure Cosmos DB-konto har kon figurer ATS för en tjänst slut punkt för ett undernät skickas begär Anden från det under nätet till Azure Cosmos DB med information om virtuellt nätverk och undernäts källa i stället för en offentlig IP-adress för källan. Dessa begär Anden matchar inte längre ett IP-filter som kon figurer ATS på Azure Cosmos DB kontot, vilket är orsaken till att följande steg är nödvändiga för att undvika drift stopp.

Innan du fortsätter ska du aktivera Azure Cosmos DB tjänstens slut punkt på det virtuella nätverket och under nätet med hjälp av det steg som visas ovan i "Aktivera tjänstens slut punkt för ett befintligt undernät i ett virtuellt nätverk".

1. Hämta information om virtuellt nätverk och undernät:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Förbered ett nytt Virtual Network regel objekt för det Azure Cosmos DB kontot:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Uppdatera Azure Cosmos DB-kontot för att aktivera åtkomst till tjänst slut punkten från under nätet:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Upprepa föregående steg för alla Azure Cosmos DB-konton som nås från under nätet.

1. Ta bort IP-brandväggens regel för under nätet från Azure Cosmos DB kontots brand Väggs regler.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Här följer några vanliga frågor om hur du konfigurerar åtkomst från virtuella nätverk:

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Är antecknings böcker och Mongo/Cassandra-gränssnittet för närvarande kompatibelt med Virtual Network aktiverade konton?

För närvarande stöds inte [Mongo Shell](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) och [Cassandra Shell](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) -integreringar i Cosmos DB datautforskaren och [tjänsten Jupyter Notebooks](./cosmosdb-jupyter-notebooks.md). Detta är för närvarande i aktiv utveckling.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Kan jag ange både Virtual Network Service-slutpunkt och princip för IP-åtkomstkontroll på ett Azure Cosmos-konto? 

Du kan aktivera både Virtual Network Service-slutpunkten och en princip för IP-åtkomstkontroll (kallas även brand vägg) på ditt Azure Cosmos-konto. Dessa två funktioner är kompletterande och säkerställer gemensamt isolering och säkerhet för ditt Azure Cosmos-konto. Att använda IP-brandvägg säkerställer att statiska IP-adresser kan komma åt ditt konto. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Hur gör jag för att begränsa åtkomsten till undernät i ett virtuellt nätverk? 

Det finns två steg som krävs för att begränsa åtkomsten till Azure Cosmos-kontot från ett undernät. Först tillåter du att trafik från undernät bär under nätet och den virtuella nätverks identiteten till Azure Cosmos DB. Detta görs genom att aktivera tjänstens slut punkt för Azure Cosmos DB på under nätet. Sedan lägger du till en regel i Azure Cosmos-kontot som anger det här under nätet som en källa från vilken kontot kan nås.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Kommer virtuella nätverks-ACL: er och IP-brandvägg avvisa begär Anden eller anslutningar? 

När IP-brandvägg eller åtkomst regler för virtuella nätverk läggs till får endast förfrågningar från tillåtna källor giltiga svar. Andra begär Anden avvisas med en 403 (förbjuden). Det är viktigt att skilja Azure Cosmos-kontots brand vägg från en brand vägg för anslutnings nivå. Källan kan fortfarande ansluta till tjänsten och själva anslutningarna avvisas inte.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Mina förfrågningar började bli blockerade när jag aktiverade tjänst slut punkten till Azure Cosmos DB på under nätet. Vad hände?

När tjänstens slut punkt för Azure Cosmos DB har Aktiver ATS på ett undernät når trafiken till konto växlarna från offentlig IP-adress till virtuellt nätverk och undernät. Om ditt Azure Cosmos-konto har en IP-baserad brand vägg, skulle trafik från tjänstens aktiverade undernät inte längre matcha IP-brandväggens regler och därför avvisas. Gå igenom stegen för att sömlöst migrera från IP-baserad brand vägg till virtuell nätverks baserad åtkomst kontroll.

### <a name="are-additional-azure-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Krävs ytterligare Azure RBAC-behörigheter för Azure Cosmos-konton med VNET-tjänstens slut punkter?

När du har lagt till tjänst slut punkterna för VNet i ett Azure Cosmos-konto måste du ha åtkomst till `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` åtgärden för alla virtuella nätverk som kon figurer ATS på ditt Azure Cosmos-konto för att göra ändringar i konto inställningarna. Den här behörigheten krävs eftersom verifierings processen validerar åtkomst till resurser (till exempel databas och virtuella nätverks resurser) innan du utvärderar några egenskaper.
 
Auktoriseringen verifierar behörighet för åtgärden VNet-resurs även om användaren inte anger de virtuella nätverkets ACL: er med Azure CLI. För närvarande har Azure Cosmos-kontots kontroll plan stöd för att ange det fullständiga läget för Azure Cosmos-kontot. En av parametrarna i kontroll planets anrop är `virtualNetworkRules` . Om den här parametern inte anges gör Azure CLI ett Hämta databas anrop för att hämta `virtualNetworkRules` och använder det här värdet i uppdaterings anropet.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Har de peer-virtuella nätverken också åtkomst till Azure Cosmos-kontot? 
Endast virtuella nätverk och deras undernät som har lagts till i Azure Cosmos-kontot har åtkomst. Deras peer-virtuella nätverk kan inte komma åt kontot förrän under näten i peer-kopplat virtuella nätverk har lagts till i kontot.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Vilket är det maximala antalet undernät som får åtkomst till ett enskilt Cosmos-konto? 
För närvarande kan du ha högst 256 undernät tillåtna för ett Azure Cosmos-konto.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Kan jag aktivera åtkomst från VPN och Express Route? 
För att få åtkomst till Azure Cosmos-kontot via Express Route från lokalt måste du aktivera Microsoft-peering. När du har angett IP-brandvägg eller åtkomst regler för virtuella nätverk kan du lägga till de offentliga IP-adresser som används för Microsoft-peering i ditt Azure Cosmos-konto IP-brandvägg för att ge lokala tjänster åtkomst till Azure Cosmos-kontot. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Måste jag uppdatera reglerna för nätverks säkerhets grupper (NSG)? 
NSG-regler används för att begränsa anslutningen till och från ett undernät med ett virtuellt nätverk. När du lägger till tjänst slut punkten för Azure Cosmos DB till under nätet behöver du inte öppna en utgående anslutning i NSG för ditt Azure Cosmos-konto. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Är tjänst slut punkter tillgängliga för alla virtuella nätverk?
Nej, endast Azure Resource Manager virtuella nätverk kan ha tjänstens slut punkt aktive rad. Klassiska virtuella nätverk har inte stöd för tjänst slut punkter.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Kan jag "acceptera anslutningar från offentliga Azure-datacenter" när åtkomst till tjänst slut punkten är aktive rad för Azure Cosmos DB?  
Detta krävs bara om du vill att ditt Azure Cosmos DBs konto ska kunna nås av andra Azure-tjänster från första part som Azure Data Factory, Azure Kognitiv sökning eller någon tjänst som distribueras i en specifik Azure-region.

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar en brand vägg för Azure Cosmos DB finns i artikeln om [brand Väggs stöd](how-to-configure-firewall.md) .
