---
title: Konfigurera en privat Azure-länk för ett Azure Cosmos-konto
description: Lär dig hur du konfigurerar en privat Azure-länk för att få åtkomst till ett Azure Cosmos-konto genom att använda en privat IP-adress i ett virtuellt nätverk.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/16/2020
ms.author: thweiss
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9a6db0d25165059581d7ffafa5b8e7fd19330c87
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629654"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Konfigurera en privat Azure-länk för ett Azure Cosmos-konto
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Med hjälp av en privat Azure-länk kan du ansluta till ett Azure Cosmos-konto via en privat slut punkt. Den privata slut punkten är en uppsättning privata IP-adresser i ett undernät i det virtuella nätverket. Du kan sedan begränsa åtkomsten till ett Azure Cosmos-konto via privata IP-adresser. När en privat länk kombineras med begränsade NSG-principer bidrar den till att minska risken för data exfiltrering. Mer information om privata slut punkter finns i artikeln [Azure Private Link](../private-link/private-link-overview.md) .

> [!NOTE]
> Den privata länken förhindrar inte att dina Azure Cosmos-slutpunkter löses av en offentlig DNS. Filtrering av inkommande begär Anden sker på program nivå, inte på transport-eller nätverks nivå.

Med privat länk kan användarna få åtkomst till ett Azure Cosmos-konto inifrån det virtuella nätverket eller från ett peer-kopplat virtuellt nätverk. Resurser som är mappade till privat länk är också tillgängliga lokalt via privat peering via VPN eller Azure ExpressRoute. 

Du kan ansluta till ett Azure Cosmos-konto som kon figurer ATS med en privat länk med hjälp av metoden för automatisk eller manuell godkännande. Läs mer i avsnittet om [godkännande av arbets flöde](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) i dokumentationen för privat länk. 

I den här artikeln beskrivs hur du skapar en privat slutpunkt. Det förutsätter att du använder metoden för automatiskt godkännande.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Skapa en privat slut punkt med hjälp av Azure Portal

Använd följande steg för att skapa en privat slut punkt för ett befintligt Azure Cosmos-konto med hjälp av Azure Portal:

1. I fönstret **alla resurser** väljer du ett Azure Cosmos-konto.

1. Välj **privata slut punkts anslutningar** i listan över inställningar och välj sedan **privat slut punkt**:

   :::image type="content" source="./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png" alt-text="Val för att skapa en privat slut punkt i Azure Portal":::

1. I fönstret **skapa en privat slut punkt – grundläggande** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj en resursgrupp.|
    | **Instansinformation** |  |
    | Name | Ange ett namn för din privata slut punkt. Om det här namnet tas skapar du ett unikt. |
    |Region| Välj den region där du vill distribuera privat länk. Skapa den privata slut punkten på samma plats som det virtuella nätverket finns på.|
    |||
1. Välj **Nästa: resurs**.
1. I **skapa en privat slut punkt – resurs**, anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj **Anslut till en Azure-resurs i min katalog**. <br/><br/> Du kan sedan välja en av dina resurser för att konfigurera en privat länk. Eller så kan du ansluta till någon annans resurs genom att använda ett resurs-ID eller alias som de har delat med dig.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft. AzureCosmosDB/databaseAccounts**. |
    | Resurs |Välj ditt Azure Cosmos-konto. |
    |Målunderresurs |Välj den Azure Cosmos DB API-typ som du vill mappa. Standardvärdet är bara ett alternativ för SQL-, MongoDB-och Cassandra-API: er. För Gremlin-och tabell-API: er kan du också välja **SQL** eftersom dessa API: er är kompatibla med SQL-API: et. |
    |||

1. Välj **Nästa: konfiguration**.
1. I **skapa en privat slut punkt – konfiguration** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    |**Nätverk**| |
    | Virtuellt nätverk| Välj ditt virtuella nätverk. |
    | Undernät | Välj ditt undernät. |
    |**Privat DNS-integrering**||
    |Integrera med privat DNS-zon |Välj **Ja**. <br><br/> För att kunna ansluta privat med din privata slut punkt behöver du en DNS-post. Vi rekommenderar att du integrerar din privata slut punkt med en privat DNS-zon. Du kan också använda dina egna DNS-servrar eller skapa DNS-poster med hjälp av värd filerna på dina virtuella datorer. |
    |Privat DNS-zon |Välj **privatelink.documents.Azure.com**. <br><br/> Den privata DNS-zonen fastställs automatiskt. Du kan inte ändra den med hjälp av Azure Portal.|
    |||

1. Välj **Granska + skapa**. På sidan **Granska + skapa** verifierar Azure konfigurationen.
1. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

När du har godkänt en privat länk för ett Azure Cosmos-Azure Portal konto är alternativet **alla nätverk** i rutan **brand vägg och virtuella nätverk** inte tillgängligt.

Följande tabell visar mappningen mellan olika Azure Cosmos-kontos API-typer, underordnade resurser som stöds och motsvarande namn för privata zoner. Du kan också komma åt Gremlin-och Tabell-API konton via SQL API, så det finns två poster för dessa API: er.

|API-typ för Azure Cosmos-konto  |Under resurser som stöds (eller grupp-ID) |Namn på privat zon  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tabell    |    Tabell     |   privatelink.table.cosmos.azure.com    |
|Tabell     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Hämta de privata IP-adresserna

När den privata slut punkten har allokerats kan du fråga IP-adresserna. Så här visar du IP-adresserna från Azure Portal:

1. Välj **Alla resurser**.
1. Sök efter den privata slut punkt som du skapade tidigare. I det här fallet är det **cdbPrivateEndpoint3**.
1. Välj fliken **Översikt** för att se DNS-inställningar och IP-adresser.

:::image type="content" source="./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png" alt-text="Privata IP-adresser i Azure Portal":::

Flera IP-adresser skapas per privat slut punkt:

* En för den globala (region-oberoende) slut punkten för Azure Cosmos-kontot
* En för varje region där Azure Cosmos-kontot har distribuerats

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Skapa en privat slut punkt med hjälp av Azure PowerShell

Kör följande PowerShell-skript för att skapa en privat slut punkt med namnet "MyPrivateEndpoint" för ett befintligt Azure Cosmos-konto. Ersätt variabel värden med informationen för din miljö.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrera den privata slut punkten med en privat DNS-zon

När du har skapat den privata slut punkten kan du integrera den med en privat DNS-zon med hjälp av följande PowerShell-skript:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>Hämta de privata IP-adresserna

När den privata slut punkten har allokerats kan du fråga IP-adresserna och FQDN-mappningen med hjälp av följande PowerShell-skript:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Skapa en privat slut punkt med hjälp av Azure CLI

Kör följande Azure CLI-skript för att skapa en privat slut punkt med namnet "myPrivateEndpoint" för ett befintligt Azure Cosmos-konto. Ersätt variabel värden med informationen för din miljö.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrera den privata slut punkten med en privat DNS-zon

När du har skapat den privata slut punkten kan du integrera den med en privat DNS-zon med hjälp av följande Azure CLI-skript:

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Skapa en privat slut punkt med hjälp av en Resource Manager-mall

Du kan konfigurera en privat länk genom att skapa en privat slut punkt i ett undernät för virtuellt nätverk. Du uppnår detta genom att använda en Azure Resource Manager mall.

Använd följande kod för att skapa en Resource Manager-mall med namnet "PrivateEndpoint_template.jspå." Den här mallen skapar en privat slut punkt för ett befintligt Azure Cosmos SQL API-konto i ett befintligt virtuellt nätverk.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**Definiera parameter filen för mallen**

Skapa en parameter fil för mallen och ge den namnet "PrivateEndpoint_parameters.jspå". Lägg till följande kod i parameter filen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**Distribuera mallen med hjälp av ett PowerShell-skript**

Skapa ett PowerShell-skript med hjälp av följande kod. Innan du kör skriptet ersätter du prenumerations-ID, resurs gruppens namn och andra variabel värden med informationen för din miljö.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

I PowerShell-skriptet `GroupId` kan variabeln bara innehålla ett värde. Det här värdet är kontots API-typ. Tillåtna värden är: `Sql` , `MongoDB` , `Cassandra` , `Gremlin` och `Table` . Vissa Azure Cosmos-konto typer är tillgängliga via flera API: er. Exempel:

* Ett Gremlin-API-konto kan nås från både Gremlin-och SQL-API-konton.
* Ett Tabell-API konto kan nås från både tabell-och SQL-API-konton.

För dessa konton måste du skapa en privat slut punkt för varje API-typ. Motsvarande API-typ anges i `GroupId` matrisen.

När mallen har distribuerats kan du se utdata som liknar det som visas i följande bild. `provisioningState`Värdet är `Succeeded` om de privata slut punkterna har kon figurer ATS korrekt.

:::image type="content" source="./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png" alt-text="Distributions utdata för Resource Manager-mallen":::

När mallen har distribuerats reserveras de privata IP-adresserna i under nätet. Brand Väggs regeln för Azure Cosmos-kontot har kon figurer ATS för att endast godkänna anslutningar från den privata slut punkten.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrera den privata slut punkten med en Privat DNS zon

Använd följande kod för att skapa en Resource Manager-mall med namnet "PrivateZone_template.jspå." Den här mallen skapar en privat DNS-zon för ett befintligt Azure Cosmos SQL API-konto i ett befintligt virtuellt nätverk.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

Använd följande kod för att skapa en Resource Manager-mall med namnet "PrivateZoneRecords_template.jspå."

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**Definiera parameter filen för mallen**

Skapa följande två parameter fil för mallen. Skapa PrivateZone_parameters.jspå. med följande kod:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Skapa PrivateZoneRecords_parameters.jspå. med följande kod:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**Distribuera mallen med hjälp av ett PowerShell-skript**

Skapa ett PowerShell-skript med hjälp av följande kod. Innan du kör skriptet ersätter du prenumerations-ID, resurs gruppens namn och andra variabel värden med informationen för din miljö.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Konfigurera anpassad DNS

Du bör använda en privat DNS-zon i det undernät där du har skapat den privata slut punkten. Konfigurera slut punkterna så att varje privat IP-adress mappas till en DNS-post. (Se `fqdns` egenskapen i svaret som visas ovan.)

När du skapar den privata slut punkten kan du integrera den med en privat DNS-zon i Azure. Om du väljer att använda en anpassad DNS-zon i stället måste du konfigurera den för att lägga till DNS-poster för alla privata IP-adresser som reserver ATS för den privata slut punkten.

> [!IMPORTANT]
> Det är DNS-matchningen för dina begär Anden som avgör om dessa begär Anden går över dina privata slut punkter eller om den offentliga standard vägen ska användas. Kontrol lera att din lokala DNS korrekt refererar till de privata IP-adresser som har mappats av din privata slut punkt.

## <a name="private-link-combined-with-firewall-rules"></a>Privat länk kombinerat med brand Väggs regler

Följande situationer och resultat är möjliga när du använder en privat länk i kombination med brand Väggs regler:

* Om du inte konfigurerar några brand Väggs regler kan all trafik komma åt ett Azure Cosmos-konto som standard.

* Om du konfigurerar offentlig trafik eller en tjänst slut punkt och skapar privata slut punkter, auktoriseras olika typer av inkommande trafik av motsvarande typ av brand Väggs regel. Om en privat slut punkt konfigureras i ett undernät där tjänstens slut punkt också har kon figurer ATS:
  * trafik till det databas konto som mappats av den privata slut punkten dirigeras via privat slut punkt,
  * trafik till andra databas konton från under nätet dirigeras via tjänst slut punkten.

* Om du inte konfigurerar någon offentlig trafik eller tjänst slut punkt och skapar privata slut punkter kan du bara komma åt Azure Cosmos-kontot via de privata slut punkterna. Om du inte konfigurerar offentlig trafik eller en tjänst slut punkt efter att alla godkända privata slut punkter har avvisats eller tagits bort, är kontot öppet för hela nätverket, såvida inte PublicNetworkAccess har angetts till Disabled (se avsnittet nedan).

## <a name="blocking-public-network-access-during-account-creation"></a>Blockera offentlig nätverks åtkomst när kontot skapas

Som det beskrivs i föregående avsnitt, och om vissa brand Väggs regler har angetts, gör ett Azure Cosmos-konto tillgängligt via enbart privata slut punkter genom att lägga till en privat slut punkt. Det innebär att Azure Cosmos-kontot kan nås från offentlig trafik när det har skapats och innan en privat slut punkt läggs till. För att säkerställa att åtkomsten till det offentliga nätverket är inaktive rad även innan du skapar privata slut punkter, kan du ställa in `publicNetworkAccess` flaggan på `Disabled` när kontot skapas. Observera att den här flaggan prioriteras över alla IP-eller virtuella nätverks regler. all offentlig och virtuell nätverks trafik blockeras när flaggan är inställd på `Disabled` , även om käll-IP-adressen eller det virtuella nätverket tillåts i brand Väggs konfigurationen.

Se [denna Azure Resource Manager mall](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/) för ett exempel som visar hur du använder den här flaggan.

## <a name="adding-private-endpoints-to-an-existing-cosmos-account-with-no-downtime"></a>Lägga till privata slut punkter till ett befintligt Cosmos-konto utan drift avbrott

Att lägga till en privat slut punkt till ett befintligt konto resulterar som standard i ett kort stillestånd på cirka 5 minuter. Följ anvisningarna nedan för att undvika den här stillestånds tiden:

1. Lägg till IP-eller virtuella nätverks regler i brand Väggs konfigurationen för att uttryckligen tillåta klient anslutningar.
1. Vänta i 10 minuter och se till att konfigurations uppdateringen tillämpas.
1. Konfigurera din nya privata slut punkt.
1. Ta bort brand Väggs reglerna som anges i steg 1.

## <a name="port-range-when-using-direct-mode"></a>Port intervall när Direct-läge används

När du använder en privat länk med ett Azure Cosmos-konto via en anslutning i direkt läge, måste du se till att hela intervallet TCP-portarna (0-65535) är öppen.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Uppdatera en privat slut punkt när du lägger till eller tar bort en region

Om du inte använder en privat DNS-zon grupp måste du lägga till eller ta bort DNS-poster för det kontot för att lägga till eller ta bort regioner i ett Azure Cosmos-konto. När regionerna har lagts till eller tagits bort kan du uppdatera under nätets privata DNS-zon så att den återspeglar de tillagda eller borttagna DNS-posterna och deras motsvarande privata IP-adresser.

Anta till exempel att du distribuerar ett Azure Cosmos-konto i tre regioner: "västra USA", "Central USA" och "Västeuropa". När du skapar en privat slut punkt för ditt konto reserveras fyra privata IP-adresser i under nätet. Det finns en IP-adress för var och en av de tre regionerna och det finns en IP-adress för global/region-oberoende-slutpunkten.

Senare kan du lägga till en ny region (till exempel "USA, östra") till Azure Cosmos-kontot. När du har lagt till den nya regionen måste du lägga till en motsvarande DNS-post till antingen din privata DNS-zon eller din anpassade DNS.

Du kan använda samma steg när du tar bort en region. När du har tagit bort regionen måste du ta bort motsvarande DNS-post från antingen din privata DNS-zon eller din anpassade DNS.

## <a name="current-limitations"></a>Aktuella begränsningar

Följande begränsningar gäller när du använder en privat länk med ett Azure Cosmos-konto:

* Du kan inte ha mer än 200 privata slut punkter på ett enda Azure Cosmos-konto.

* När du använder en privat länk med ett Azure Cosmos-konto via en anslutning via direkt läge, kan du bara använda TCP-protokollet. HTTP-protokollet stöds inte för närvarande.

* När du använder Azure Cosmos DB s API för MongoDB-konton stöds en privat slut punkt för konton på Server version 3,6 (det vill säga konton som använder slut punkten i formatet `*.mongo.cosmos.azure.com` ). Privat länk stöds inte för konton på Server version 3,2 (det vill säga konton som använder slut punkten i formatet `*.documents.azure.com` ). Om du vill använda en privat länk bör du migrera gamla konton till den nya versionen.

* När du använder en Azure Cosmos DBs API för MongoDB-konto som har en privat länk, kanske vissa verktyg eller bibliotek inte fungerar eftersom de automatiskt utsträckar `appName` parametern från anslutnings strängen. Den här parametern krävs för att ansluta till kontot över en privat slut punkt. Vissa verktyg, t. ex. Visual Studio Code, tar inte bort den här parametern från anslutnings strängen och är därför kompatibel.

* En nätverks administratör bör minst beviljas `Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action` behörigheten i Azure Cosmos-kontots omfång för att skapa automatiskt godkända privata slut punkter.

### <a name="limitations-to-private-dns-zone-integration"></a>Begränsningar för integrering av privata DNS-zoner

Om du inte använder en privat DNS-replikeringsgrupp tas DNS-poster i den privata DNS-zonen inte bort automatiskt när du tar bort en privat slut punkt eller tar bort en region från Azure Cosmos-kontot. Du måste ta bort DNS-posterna manuellt innan:

* Lägger till en ny privat slutpunkt som är länkad till den här privata DNS-zonen.
* Lägga till en ny region i alla databas konton som har privata slut punkter länkade till den här privata DNS-zonen.

Om du inte rensar DNS-posterna kan oväntade data Plans problem uppstå. Dessa problem omfattar data avbrott i regioner som lagts till efter borttagning av privata slut punkter eller region borttagning.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cosmos DB säkerhetsfunktioner finns i följande artiklar:

* Information om hur du konfigurerar en brand vägg för Azure Cosmos DB finns i [brand Väggs stöd](how-to-configure-firewall.md).

* Information om hur du konfigurerar en tjänst slut punkt för ett virtuellt nätverk för ditt Azure Cosmos-konto finns i [Konfigurera åtkomst från virtuella nätverk](how-to-configure-vnet-service-endpoint.md).

* Mer information om privat länk finns i dokumentationen till [Azures privata länkar](../private-link/private-link-overview.md) .
