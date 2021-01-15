---
title: Skapa ett virtuellt nätverk för Azure-peering – olika distributions modeller – samma prenumeration | Microsoft Docs
description: Lär dig hur du skapar en virtuell nätverks-peering mellan virtuella nätverk som skapats via olika Azure-distributions modeller som finns i samma Azure-prenumeration.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 1ff9fcbb693f7e606c07985f9bce9acd60c5591a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222980"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Skapa en virtuell nätverks-peering-olika distributions modeller, samma prenumeration

I den här självstudien lär du dig att skapa en virtuell nätverks-peering mellan virtuella nätverk som skapats via olika distributions modeller. Båda virtuella nätverken finns i samma prenumeration. Peering av två virtuella nätverk gör det möjligt för resurser i olika virtuella nätverk att kommunicera med varandra med samma bandbredd och svars tid som om resurserna fanns i samma virtuella nätverk. Läs mer om [peering för virtuella nätverk](virtual-network-peering-overview.md).

Stegen för att skapa en virtuell nätverks-peering skiljer sig beroende på om de virtuella nätverken finns i samma, eller olika, prenumerationer och vilken Azure- [distributions modell](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som de virtuella nätverken skapas via. Lär dig hur du skapar en virtuell nätverks-peering i andra scenarier genom att klicka på scenariot i följande tabell:

|Azure-distributionsmodell  | Azure-prenumeration  |
|--------- |---------|
|[Båda Resource Manager](tutorial-connect-virtual-networks-portal.md) |Samma|
|[Båda Resource Manager](create-peering-different-subscriptions.md) |Olika|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models-subscriptions.md) |Olika|

Det går inte att skapa en virtuell nätverks-peering mellan två virtuella nätverk som distribueras via den klassiska distributions modellen. Om du behöver ansluta virtuella nätverk som båda har skapats via den klassiska distributions modellen kan du använda en Azure- [VPN gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att ansluta de virtuella nätverken.

Den här självstudien peererar virtuella nätverk i samma region. Du kan också peer-virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region). Vi rekommenderar att du bekantar dig med [peering-kraven och begränsningarna](virtual-network-manage-peering.md#requirements-and-constraints) innan du peer-koppla virtuella nätverk.

Du kan använda Azure Portal, Azure [Command-Line Interface](#cli) (CLI), Azure [PowerShell](#powershell)eller en Azure Resource Manager mall för att skapa en virtuell nätverks-peering. Klicka på något av länkarna ovan om du vill gå direkt till stegen för att skapa en virtuell nätverks-peering med ditt eget verktyg.

## <a name="create-peering---azure-portal"></a>Skapa peering-Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com). Det konto som du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks-peering. En lista över behörigheter finns i [behörigheter för virtuella nätverks-peering](virtual-network-manage-peering.md#requirements-and-constraints).
2. Klicka på **+ ny**, klicka på **nätverk** och sedan på **virtuellt nätverk**.
3. I bladet **Skapa virtuellt nätverk** anger du eller väljer värden för följande inställningar och klickar sedan på **skapa**:
    - **Namn**: *myVnet1*
    - **Adress utrymme**: *10.0.0.0/16*
    - **Under näts namn**: *standard*
    - **Adress intervall för under nätet**: *10.0.0.0/24*
    - **Prenumeration**: Välj din prenumeration
    - **Resurs grupp**: Välj **Skapa ny** och ange *myResourceGroup*
    - **Plats**: *östra USA*
4. Klicka på **+ Ny**. I rutan **Sök på Marketplace skriver du det** *virtuella nätverket*. Klicka på **virtuellt nätverk** när det visas i Sök resultaten.
5. Välj **klassiskt** i rutan **Välj en distributions modell** på bladet **virtuellt nätverk** och klicka sedan på **skapa**.
6. I bladet **Skapa virtuellt nätverk** anger du eller väljer värden för följande inställningar och klickar sedan på **skapa**:
    - **Namn**: *myVnet2*
    - **Adress utrymme**: *10.1.0.0/16*
    - **Under näts namn**: *standard*
    - **Adress intervall för under nätet**: *10.1.0.0/24*
    - **Prenumeration**: Välj din prenumeration
    - **Resurs grupp**: Välj **Använd befintlig** och välj *myResourceGroup*
    - **Plats**: *östra USA*
7. I rutan **Sök resurser** överst i portalen skriver du *myResourceGroup*. Klicka på **myResourceGroup** när den visas i Sök resultaten. Ett blad visas för resurs gruppen **myresourcegroup** . Resurs gruppen innehåller de två virtuella nätverk som skapades i föregående steg.
8. Klicka på **myVNet1**.
9. På bladet **myVnet1** som visas klickar du på **peering** i den lodräta listan med alternativ på vänster sida av bladet.
10. Klicka på **+ Lägg till** i bladet **myVnet1-peering** som visades.
11. I bladet **Lägg till peering** som visas anger eller väljer du följande alternativ och klickar sedan på **OK**:
     - **Namn**: *myVnet1ToMyVnet2*
     - **Distributions modell för virtuellt nätverk**: Välj **klassisk**.
     - **Prenumeration**: Välj din prenumeration
     - **Virtuellt nätverk**: Klicka på **Välj ett virtuellt nätverk** och klicka sedan på **myVnet2**.
     - **Tillåt åtkomst till virtuellt nätverk:** Se till att **aktive rad** är markerat.
    Inga andra inställningar används i den här självstudien. Om du vill veta mer om alla peering-inställningar läser du [Hantera peering för virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
12. När du klickar på **OK** i föregående steg stängs bladet **Lägg till peering** och du ser bladet **myVnet1-peering** igen. Efter några sekunder visas peering du skapade på bladet. **Ansluten** visas i kolumnen **peering-status** för **myVnet1ToMyVnet2** -peering som du skapade.

    Peer kopplingen har nu upprättats. Alla Azure-resurser som du skapar i ett virtuellt nätverk kan nu kommunicera med varandra via deras IP-adresser. Om du använder Azures standard namn matchning för virtuella nätverk kan inte resurserna i de virtuella nätverken matcha namn i de virtuella nätverken. Om du vill matcha namn i virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [namn matchning med hjälp av en egen DNS-Server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Valfritt**: om du skapar virtuella datorer som inte ingår i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att verifiera anslutningen.
14. **Valfritt**: om du vill ta bort de resurser som du skapar i den här självstudien utför du stegen i avsnittet [ta bort resurser](#delete-portal) i den här artikeln.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Skapa peering – Azure CLI

Utför följande steg med hjälp av den klassiska Azure-CLI: en och Azure CLI. Du kan slutföra stegen från Azure Cloud Shell genom att välja knappen **prova** i något av följande steg, eller genom att installera den [klassiska CLI](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) och [CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) och köra kommandona på den lokala datorn.

1. Om du använder Cloud Shell går du vidare till steg 2, eftersom Cloud Shell automatiskt loggar in dig på Azure. Öppna en kommando-session och logga in på Azure med hjälp av `azure login` kommandot.
2. Kör CLI i Service Management-läge genom att ange `azure config mode asm` kommandot.
3. Ange följande kommando för att skapa det virtuella nätverket (klassiskt):

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Kör följande bash CLI-skript med hjälp av CLI, inte den klassiska CLI. Alternativ för att köra bash CLI-skript på Windows-dator finns i [Installera Azure CLI i Windows](/cli/azure/install-azure-cli-windows).

   ```azurecli-interactive
   #!/bin/bash

   # Create a resource group.
   az group create \
     --name myResourceGroup \
     --location eastus

   # Create the virtual network (Resource Manager).
   az network vnet create \
     --name myVnet1 \
     --resource-group myResourceGroup \
     --location eastus \
     --address-prefix 10.0.0.0/16
   ```

5. Skapa en virtuell nätverks-peering mellan de två virtuella nätverk som skapats via olika distributions modeller med hjälp av CLI. Kopiera följande skript till en text redigerare på din dator. Ersätt `<subscription id>` med ditt prenumerations-ID. Om du inte känner till ditt prenumerations-ID anger du `az account show` kommandot. Värdet för **ID** i utdata är ditt PRENUMERATIONS-ID. Klistra in det ändrade skriptet i till CLI-sessionen och tryck sedan på `Enter` .

   ```azurecli-interactive
   # Get the ID for VNet1.
   vnet1Id=$(az network vnet show \
     --resource-group myResourceGroup \
     --name myVnet1 \
     --query id --out tsv)

   # Peer VNet1 to VNet2.
   az network vnet peering create \
     --name myVnet1ToMyVnet2 \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
     --allow-vnet-access
   ```

6. När skriptet har körts granskar du peering för det virtuella nätverket (Resource Manager). Kopiera följande kommando, klistra in det i CLI-sessionen och tryck sedan på `Enter` :

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   Utdata visar **anslutna** i kolumnen **PeeringState** .

   Alla Azure-resurser som du skapar i ett virtuellt nätverk kan nu kommunicera med varandra via deras IP-adresser. Om du använder Azures standard namn matchning för virtuella nätverk kan inte resurserna i de virtuella nätverken matcha namn i de virtuella nätverken. Om du vill matcha namn i virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [namn matchning med hjälp av en egen DNS-Server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Valfritt**: om du skapar virtuella datorer som inte ingår i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att verifiera anslutningen.
8. **Valfritt**: om du vill ta bort de resurser som du skapar i den här självstudien slutför du stegen i [ta bort resurser](#delete-cli) i den här artikeln.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Skapa peering – PowerShell

1. Installera den senaste versionen av PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) -och [AZ](https://www.powershellgallery.com/packages/Az/) -modulerna. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta en PowerShell-session.
3. I PowerShell loggar du in på Azure genom att ange `Add-AzureAccount` kommandot. Det konto som du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks-peering. En lista över behörigheter finns i [behörigheter för virtuella nätverks-peering](virtual-network-manage-peering.md#requirements-and-constraints).
4. Om du vill skapa ett virtuellt nätverk (klassiskt) med PowerShell måste du skapa en ny eller ändra en befintlig nätverks konfigurations fil. Lär dig hur du [exporterar, uppdaterar och importerar filer för nätverks konfiguration](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). Filen bör innehålla följande **VirtualNetworkSite** -element för det virtuella nätverk som används i den här självstudien:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Om du importerar en ändrad nätverks konfigurations fil kan det orsaka ändringar i befintliga virtuella nätverk (klassisk) i din prenumeration. Se till att du bara lägger till det tidigare virtuella nätverket och att du inte ändrar eller tar bort befintliga virtuella nätverk från din prenumeration.
5. Logga in på Azure för att skapa det virtuella nätverket (Resource Manager) genom att ange `Connect-AzAccount` kommandot. Det konto som du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks-peering. En lista över behörigheter finns i [behörigheter för virtuella nätverks-peering](virtual-network-manage-peering.md#requirements-and-constraints).
6. Skapa en resurs grupp och ett virtuellt nätverk (Resource Manager). Kopiera skriptet, klistra in det i PowerShell och tryck sedan på `Enter` .

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Skapa en virtuell nätverks-peering mellan de två virtuella nätverk som skapats via de olika distributions modellerna. Kopiera följande skript till en text redigerare på din dator. Ersätt `<subscription id>` med ditt prenumerations-ID. Om du inte känner till ditt prenumerations-ID anger du `Get-AzSubscription` kommandot för att visa det. Värdet för **ID** i det returnerade resultatet är ditt PRENUMERATIONS-ID. Om du vill köra skriptet kopierar du det ändrade skriptet från text redigeraren, högerklickar sedan i PowerShell-sessionen och trycker sedan på `Enter` .

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. När skriptet har körts granskar du peering för det virtuella nätverket (Resource Manager). Kopiera följande kommando, klistra in det i PowerShell-sessionen och tryck sedan på `Enter` :

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Utdata visar **anslutna** i kolumnen **PeeringState** .

    Alla Azure-resurser som du skapar i ett virtuellt nätverk kan nu kommunicera med varandra via deras IP-adresser. Om du använder Azures standard namn matchning för virtuella nätverk kan inte resurserna i de virtuella nätverken matcha namn i de virtuella nätverken. Om du vill matcha namn i virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [namn matchning med hjälp av en egen DNS-Server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Valfritt**: om du skapar virtuella datorer som inte ingår i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att verifiera anslutningen.
10. **Valfritt**: om du vill ta bort de resurser som du skapar i den här självstudien slutför du stegen i [ta bort resurser](#delete-powershell) i den här artikeln.

## <a name="delete-resources"></a><a name="delete"></a>Ta bort resurser

När du är klar med den här självstudien kanske du vill ta bort de resurser som du skapade i självstudien, så du får inte använda avgifter för användning. Om du tar bort en resurs grupp tas även alla resurser i resurs gruppen bort.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. Skriv **myResourceGroup** i rutan Portal Sök. Klicka på **myResourceGroup** i Sök resultaten.
2. Klicka på ikonen **ta bort** på bladet **myResourceGroup** .
3. Bekräfta borttagningen genom att ange **myResourceGroup** i rutan **Skriv resurs gruppens namn** och klicka på **ta bort**.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Använd Azure CLI för att ta bort det virtuella nätverket (Resource Manager) med följande kommando:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Använd den klassiska CLI: en för att ta bort det virtuella nätverket (klassisk) med följande kommandon:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Ange följande kommando för att ta bort det virtuella nätverket (Resource Manager):

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Om du vill ta bort det virtuella nätverket (klassisk) med PowerShell måste du ändra en befintlig nätverks konfigurations fil. Lär dig hur du [exporterar, uppdaterar och importerar filer för nätverks konfiguration](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). Ta bort följande VirtualNetworkSite-element för det virtuella nätverk som används i den här självstudien:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Om du importerar en ändrad nätverks konfigurations fil kan det orsaka ändringar i befintliga virtuella nätverk (klassisk) i din prenumeration. Se till att du bara tar bort det tidigare virtuella nätverket och att du inte ändrar eller tar bort andra befintliga virtuella nätverk från din prenumeration.

## <a name="next-steps"></a>Nästa steg

- Bekanta dig med viktiga [peering-begränsningar och beteenden för virtuella nätverk](virtual-network-manage-peering.md#requirements-and-constraints) innan du skapar en virtuell nätverks-peering för produktions användning.
- Lär dig mer om alla [peering-inställningar för virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
- Lär dig hur du [skapar en nätverkstopologi med nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) med peering av virtuella nätverk.