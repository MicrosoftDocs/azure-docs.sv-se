---
title: Konfigurera tjänst slut punkts principer – Azure HDInsight
description: Lär dig hur du konfigurerar tjänst slut punkts principer för ditt virtuella nätverk med Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: f2752e5ab2bf7c2926ec9e2c0e4929eab91ed377
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88530978"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>Konfigurera tjänst slut punkts principer för virtuella nätverk för Azure HDInsight

Den här artikeln innehåller information om hur du implementerar tjänst slut punkts principer på virtuella nätverk med Azure HDInsight.

## <a name="background"></a>Bakgrund

Med Azure HDInsight kan du skapa kluster i ditt eget virtuella nätverk. Om du behöver tillåta utgående trafik från ditt virtuella nätverk till andra Azure-tjänster som lagrings konton kan du skapa [tjänst slut punkts principer](../virtual-network/virtual-network-service-endpoint-policies-overview.md). Tjänst slut punkts principer som skapas via Azure Portal, men du kan bara skapa en princip för ett enskilt konto, alla konton i en prenumeration eller alla konton i en resurs grupp.

Som en hanterad tjänst samlar Azure HDInsight dock in data och loggfiler från varje kluster i vissa lagrings konton i varje region. För att dessa data ska kunna komma åt HDInsight från det virtuella nätverket måste du skapa tjänst slut punkts principer som tillåter utgående trafik till vissa data insamlings punkter som hanteras av Azure HDInsight.

## <a name="service-endpoint-policies-for-hdinsight"></a>Tjänst slut punkts principer för HDInsight

Dessa tjänst slut punkts principer har stöd för följande funktioner:

- Samling loggar och telemetri om skapande av kluster, jobb körning och plattforms åtgärder som skalning.
- Bifoga virtuella hård diskar (VHD) till nyligen skapade klusternoder för etablering av program vara och bibliotek i klustret.

Om tjänst slut punkts principer inte har skapats för att aktivera det här data flödet kan skapande av kluster Miss lyckas och Azure HDInsight kan inte ge stöd för dina kluster.

## <a name="create-service-endpoint-policies-for-hdinsight"></a>Skapa tjänst slut punkts principer för HDInsight

Kontrol lera att rätt tjänst slut punkts principer är kopplade till ditt virtuella nätverk innan du skapar nya kluster. Annars kan skapandet av klustret Miss lyckas eller resultera i fel.

Använd följande process för att skapa de nödvändiga tjänst slut punkts principerna:

1. Bestäm den region där du vill skapa ditt HDInsight-kluster.
1. Leta upp regionen i [listan över resurser för tjänst slut punkts principer](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json), vilket ger alla resurs grupper för hanterings konton för HDInsight-hantering.
1. Välj listan över resurs grupper för din region. Ett exempel på resurserna för `Canada Central` visas nedan:

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG"
    ],
    ```

1. Infoga listan med resurs grupper i installations skriptet som skrivits i Azure CLI eller Azure PowerShell.

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    Om du föredrar att konfigurera din tjänst slut punkts princip med PowerShell, använder du följande kodfragment.
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```

## <a name="next-steps"></a>Nästa steg

* [Azure HDInsight Virtual Network-arkitektur](hdinsight-virtual-network-architecture.md)
* [Konfigurera virtuell nätverksinstallation](./network-virtual-appliance.md)
