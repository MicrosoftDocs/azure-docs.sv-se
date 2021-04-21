---
title: 'Snabbstart: Konfigurera en Azure NetApp Files NFS-volym'
description: Snabbstart – Beskriver hur du snabbt ställer in Azure NetApp Files och skapar en volym.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-azurecli, subject-armqs
ms.openlocfilehash: 77d5ce2cc903be51b7a38d6edc34bb8424c52ddb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786107"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Snabbstart: Konfigurera Azure NetApp Files skapa en NFS-volym

Den här artikeln visar hur du snabbt Azure NetApp Files och skapar en NFS-volym. 

I den här snabbstarten ställer du in följande objekt:

- Registrering för Azure NetApp Files och NetApp-resursprovider
- Ett NetApp-konto
- En kapacitetspool
- En NFS-volym för Azure NetApp Files

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Alla funktioner som du kan aktivera för en NFS-volym och relevanta överväganden finns i [Skapa en NFS-volym.](azure-netapp-files-create-volumes.md) 

## <a name="before-you-begin"></a>Innan du börjar

> [!IMPORTANT]
> Du måste beviljas åtkomst till Azure NetApp Files tjänsten. Information om hur du begär åtkomst till tjänsten finns [Azure NetApp Files sidan med inskickade väntelistor.](https://aka.ms/azurenetappfiles)  Du måste vänta på ett officiellt bekräftelsemeddelande från Azure NetApp Files innan du fortsätter.

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Registrera dig för Azure NetApp Files och NetApp-resursprovidern

> [!NOTE]
> Registreringsprocessen kan ta lite tid att slutföra.
>

# <a name="portal"></a>[Portal](#tab/azure-portal)

För registreringssteg med portalen öppnar du en Cloud Shell som anges ovan och följer dessa Azure CLI-steg:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Den här artikeln kräver att Azure PowerShell Az version 2.6.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta din nuvarande version. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du vill kan du använda Cloud Shell i en PowerShell-session i stället.

1. I en PowerShell-kommandotolk (eller PowerShell Cloud Shell-session) anger du den prenumeration som har godkänts för Azure NetApp Files:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Registrera Azure-resursprovidern:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Förbered din miljö för Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="template"></a>[Mall](#tab/template)

Inga.

Använd Azure Portal, PowerShell eller Azure CLI för att registrera Azure NetApp Files och NetApp-resursprovidern.

Se [Registrera för Azure NetApp Files](azure-netapp-files-register.md) mer information.

---

## <a name="create-a-netapp-account"></a>Skapa ett NetApp-konto

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I Azure Portal sökrutan anger du **Azure NetApp Files** och väljer **sedan Azure NetApp Files** i listan som visas.

      ![Välj Azure NetApp Files](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Klicka på **+ Lägg till** för att skapa ett nytt NetApp-konto.

     ![Skapa ett nytt NetApp-konto](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Ange följande information i fönstret Nytt NetApp-konto:
   1. Ange **myaccount1** som kontonamn.
   2. Välj din prenumeration.
   3. Välj **Skapa ny** för att skapa en ny resursgrupp. Ange **myRG1** som resursgruppsnamn. Klicka på **OK**.
   4. Välj din kontoplats.

      ![Fönstret Nytt NetApp-konto](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)

      ![Fönstret Resursgrupp](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Klicka **på Skapa** för att skapa ditt nya NetApp-konto.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definiera några variabler så att vi kan referera till dem i resten av exemplen:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ```

    > [!NOTE]
    > En lista över [regioner som stöds finns](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) i Produkt tillgänglig per region.
    > Om du vill hämta regionnamnet som stöds av våra kommandoradsverktyg använder du `Get-AzLocation | select Location`
    >

1. Skapa en ny resursgrupp med kommandot [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Skapa Azure NetApp Files-konto [med kommandot New-AzNetAppFilesAccount:](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount)

    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definiera några variabler så att vi kan referera till dem i resten av exemplen:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ```

    > [!NOTE]
    > En lista över [regioner som stöds finns](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) i Produkt tillgänglig per region.
    > Om du vill hämta regionnamnet som stöds av våra kommandoradsverktyg använder du `az account list-locations --query "[].{Region:name}" --out table`
    >

2. Skapa en ny resursgrupp med kommandot [az group create:](/cli/azure/group#az_group_create)

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Skapa Azure NetApp Files konto med [kommandot az netappfiles account](/cli/azure/netappfiles/account#az_netappfiles_account_create) create:

    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```

# <a name="template"></a>[Mall](#tab/template)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Följande kodfragment visar hur du skapar ett NetApp-konto i en Azure Resource Manager mall (ARM-mall) med hjälp av [resursen Microsoft.NetApp/netAppAccounts.](/azure/templates/microsoft.netapp/netappaccounts) Om du vill köra koden laddar du ned den [fullständiga ARM-mallen från](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) vår GitHub-lagringsplatsen.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="177-183":::

<!-- Block begins with "type": "Microsoft.NetApp/netAppAccounts", -->

---

## <a name="set-up-a-capacity-pool"></a>Konfigurera en kapacitetspool

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. På bladet Azure NetApp Files hantering väljer du ditt NetApp-konto (**myaccount1**).

    ![Välj NetApp-konto](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)

2. På bladet Azure NetApp Files för NetApp-kontot klickar du på **Kapacitetspooler.**

    ![Klicka på Kapacitetspooler](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)

3. Klicka **på + Lägg till pooler**.

    ![Klicka på Lägg till pooler](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Ange information för kapacitetspoolen:
    * Ange **mypool1** som poolnamn.
    * Välj **Premium** som servicenivå.
    * Ange **4 (TiB)** som poolstorlek.
    * Använd  QoS-typen Auto.

5. Klicka på **Skapa**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definiera några nya variabler för framtida bruk

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Skapa en ny kapacitetspool med hjälp av [New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definiera några nya variabler för framtida bruk

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Skapa en ny kapacitetspool med hjälp av [az netappfiles-poolen create](/cli/azure/netappfiles/pool#az_netappfiles_pool_create)

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

# <a name="template"></a>[Mall](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

Följande kodfragment visar hur du skapar en kapacitetspool i en Azure Resource Manager-mall (ARM-mall) med hjälp av [resursen Microsoft.NetApp/netAppAccounts/capacityPools.](/azure/templates/microsoft.netapp/netappaccounts/capacitypools) Om du vill köra koden laddar du ned den [fullständiga ARM-mallen från](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) vår GitHub-lagringsplatsen.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="184-196":::

<!-- LN 185, block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools", -->

---

## <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Skapa en NFS-volym för Azure NetApp Files

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. På bladet Azure NetApp Files netApp-kontot klickar du på **Volymer.**

    ![Klicka på Volymer](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)

2. Klicka på **+ Lägg till volymen**.

    ![Klicka på Lägg till volymer](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)

3. I fönstret Skapa en volym anger du information för volymen:
   1. Ange **myvol1** som volymnamn.
   2. Välj din kapacitetspool (**mypool1**).
   3. Använd standardvärdet för kvoten.
   4. Under virtuellt nätverk klickar du **på Skapa nytt** för att skapa ett nytt virtuellt Azure-nätverk (Vnet).  Fyll sedan i följande information:
       * Ange **myvnet1** som Vnet-namn.
       * Ange ett adressutrymme för din inställning, till exempel 10.7.0.0/16
       * Ange **myANFsubnet** som undernätsnamn.
       * Ange adressintervallet för undernätet, till exempel 10.7.0.0/24. Du kan inte dela det dedikerade undernätet med andra resurser.
       * Välj **Microsoft.NetApp/volumes för** undernätsdelegering.
       * Klicka **på OK** för att skapa det virtuella nätverket.
   5. I undernätet väljer du det nyligen skapade virtuella nätverket (**myvnet1**) som delegatundernät.

      ![Skapa ett volymfönster](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)

      ![Fönstret Skapa virtuellt nätverk](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)

4. Klicka **på** Protokoll och utför sedan följande åtgärder:
    * Välj **NFS** som protokolltyp för volymen.
    * Ange **myfilepath1** som den filsökväg som ska användas för att skapa exportsökvägen för volymen.
    * Välj NFS-version (**NFSv3** eller **NFSv4.1**) för volymen.
      Se [överväganden](azure-netapp-files-create-volumes.md#considerations) och [bästa praxis för](azure-netapp-files-create-volumes.md#best-practice) NFS-versioner.

    ![Ange NFS-protokoll för snabbstart](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Klicka på **Granska + skapa**.

    ![Fönstret Granska och skapa](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)

6. Granska informationen för volymen och klicka sedan på **Skapa**.
    Den skapade volymen visas på bladet Volymer.

    ![Volym som skapats](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Skapa en undernätsdelegering till "Microsoft.NetApp/volumes" [med kommandot New-AzDelegation.](/powershell/module/az.network/new-azdelegation)

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Skapa en undernätskonfiguration med [kommandot New-AzVirtualNetworkSubnetConfig.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Skapa det virtuella nätverket med hjälp av [kommandot New-AzVirtualNetwork.](/powershell/module/az.network/new-azvirtualnetwork)

    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Skapa volymen med kommandot [New-AzNetAppFilesVolume.](/powershell/module/az.netappfiles/new-aznetappfilesvolume)

    ```powershell-interactive
    $volumeSizeBytes = 1099511627776 # 100GiB
    $subnetId = $vnet.Subnets[0].Id

    New-AzNetAppFilesVolume -ResourceGroupName $resourceGroup `
        -Location $location `
        -AccountName $anfAccountName `
        -PoolName $poolName `
        -Name "myvol1" `
        -UsageThreshold $volumeSizeBytes `
        -SubnetId $subnetId `
        -CreationToken "myfilepath1" `
        -ServiceLevel $serviceLevel `
        -ProtocolType NFSv3
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definiera några variabler för senare användning.

    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Skapa ett virtuellt nätverk utan undernät med kommandot [az network vnet create.](/cli/azure/network/vnet#az_network_vnet_create)

    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Skapa ett delegerat undernät med [kommandot az network vnet subnet create.](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Skapa volymen med kommandot [az netappfiles volume create.](/cli/azure/netappfiles/volume#az_netappfiles_volume_create)

    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within subscription and region

    az netappfiles volume create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --name "myvol1" \
        --service-level $SERVICE_LEVEL \
        --vnet $VNET_ID \
        --subnet $SUBNET_ID \
        --usage-threshold $VOLUME_SIZE_GiB \
        --file-path $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

# <a name="template"></a>[Mall](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

Följande kodfragment visar hur du ställer in ett VNet och skapar en Azure NetApp Files-volym i en Azure Resource Manager mall (ARM-mall). VNet-installationen använder [resursen Microsoft.Network/virtualNetworks.](/azure/templates/Microsoft.Network/virtualNetworks) När volymen skapas används [resursen Microsoft.NetApp/netAppAccounts/capacityPools/volumes.](/azure/templates/microsoft.netapp/netappaccounts/capacitypools/volumes) Om du vill köra koden laddar du ned den [fullständiga ARM-mallen från](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) vår GitHub-lagringsplatsen.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="148-176":::

<!-- Block begins with  "type": "Microsoft.Network/virtualNetworks", -->

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="197-229":::

<!-- Block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes", -->

---

## <a name="clean-up-resources"></a>Rensa resurser

# <a name="portal"></a>[Portal](#tab/azure-portal)

När du är klar och om du vill kan du ta bort resursgruppen. Åtgärden att ta bort en resursgrupp går inte att ångra.

> [!IMPORTANT]
> Alla resurser i resursgrupperna tas bort permanent och kan inte ångras.

1. I Azure Portal sökrutan anger du **Azure NetApp Files** och väljer **sedan Azure NetApp Files** i listan som visas.

2. I listan över prenumerationer klickar du på den resursgrupp (myRG1) som du vill ta bort.

    ![Navigera till resursgrupper](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. På resursgruppssidan klickar du på Ta **bort resursgrupp.**

    ![Skärmbild som visar knappen Ta bort resursgrupp.](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png)

    Ett fönster öppnas och visar en varning om vilka resurser som tas bort med resursgruppen.

4. Ange namnet på resursgruppen (myRG1) för att bekräfta att du vill ta bort resursgruppen permanent och alla resurser i den och klicka sedan på **Ta bort**.

    ![Bekräfta borttagning av resursgrupp](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png )

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

När du är klar och om du vill kan du ta bort resursgruppen. Åtgärden att ta bort en resursgrupp går inte att ångra.

> [!IMPORTANT]
> Alla resurser i resursgrupperna tas bort permanent och kan inte ångras.

1. Ta bort resursgruppen med kommandot [Remove-AzResourceGroup.](/powershell/module/az.resources/remove-azresourcegroup)

    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

När du är klar och om du vill kan du ta bort resursgruppen. Åtgärden att ta bort en resursgrupp går inte att ångra.

> [!IMPORTANT]
> Alla resurser i resursgrupperna tas bort permanent och kan inte ångras.

1. Ta bort resursgrupp med hjälp av [kommandot az group delete.](/cli/azure/group#az_group_delete)

    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```

# <a name="template"></a>[Mall](#tab/template)

Inga.

Använd Azure Portal, PowerShell eller Azure CLI för att ta bort resursgruppen.

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)

> [!div class="nextstepaction"]
> [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)

> [!div class="nextstepaction"]
> [Skapa en NFS-volym](azure-netapp-files-create-volumes.md)
