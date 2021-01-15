---
title: Skapa ett virtuellt nätverk för Azure-peering – olika distributions modeller – olika prenumerationer
titlesuffix: Azure Virtual Network
description: Lär dig hur du skapar en virtuell nätverks-peering mellan virtuella nätverk som skapats via olika Azure-distributions modeller som finns i olika Azure-prenumerationer.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 29a64931ac92eacf8948ae067118b6b25198c85d
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223473"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Skapa en virtuell nätverks-peering-olika distributions modeller och prenumerationer

I den här självstudien lär du dig att skapa en virtuell nätverks-peering mellan virtuella nätverk som skapats via olika distributions modeller. De virtuella nätverken finns i olika prenumerationer. Peering av två virtuella nätverk gör det möjligt för resurser i olika virtuella nätverk att kommunicera med varandra med samma bandbredd och svars tid som om resurserna fanns i samma virtuella nätverk. Läs mer om [peering för virtuella nätverk](virtual-network-peering-overview.md).

Stegen för att skapa en virtuell nätverks-peering skiljer sig beroende på om de virtuella nätverken finns i samma, eller olika, prenumerationer och vilken Azure- [distributions modell](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som de virtuella nätverken skapas via. Lär dig hur du skapar en virtuell nätverks-peering i andra scenarier genom att klicka på scenariot i följande tabell:

|Azure-distributionsmodell  | Azure-prenumeration  |
|--------- |---------|
|[Båda Resource Manager](tutorial-connect-virtual-networks-portal.md) |Samma|
|[Båda Resource Manager](create-peering-different-subscriptions.md) |Olika|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models.md) |Samma|

Det går inte att skapa en virtuell nätverks-peering mellan två virtuella nätverk som distribueras via den klassiska distributions modellen. I den här självstudien används virtuella nätverk som finns i samma region. Den här självstudien peererar virtuella nätverk i samma region. Du kan också peer-virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region). Vi rekommenderar att du bekantar dig med [peering-kraven och begränsningarna](virtual-network-manage-peering.md#requirements-and-constraints) innan du peer-koppla virtuella nätverk.

När du skapar en virtuell nätverks-peering mellan virtuella nätverk som finns i olika prenumerationer, kan prenumerationerna associeras med samma Azure Active Directory-klient. Om du inte redan har en Azure Active Directory klient kan du snabbt [skapa en](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant).

Du kan använda [Azure Portal](#portal), Azures [kommando rads gränssnitt](#cli) (CLI) eller Azure [PowerShell](#powershell) för att skapa en virtuell nätverks-peering. Klicka på något av länkarna ovan om du vill gå direkt till stegen för att skapa en virtuell nätverks-peering med ditt eget verktyg.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Skapa peering-Azure Portal

I den här självstudien används olika konton för varje prenumeration. Om du använder ett konto som har behörigheter till båda prenumerationerna kan du använda samma konto för alla steg, hoppa över stegen för att logga ut från portalen och hoppa över stegen för att tilldela de virtuella nätverken till en annan användar behörighet.

1. Logga in på [Azure Portal](https://portal.azure.com) som användare a. Det konto som du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks-peering. En lista över behörigheter finns i [behörigheter för virtuella nätverks-peering](virtual-network-manage-peering.md#permissions).
2. Klicka på **+ ny**, klicka på **nätverk** och sedan på **virtuellt nätverk**.
3. I bladet **Skapa virtuellt nätverk** anger du eller väljer värden för följande inställningar och klickar sedan på **skapa**:
    - **Namn**: *myVnetA*
    - **Adress utrymme**: *10.0.0.0/16*
    - **Under näts namn**: *standard*
    - **Adress intervall för under nätet**: *10.0.0.0/24*
    - **Prenumeration**: Välj prenumeration A.
    - **Resurs grupp**: Välj **Skapa ny** och ange *myResourceGroupA*
    - **Plats**: *östra USA*
4. I rutan **Sök resurser** överst i portalen skriver du *myVnetA*. Klicka på **myVnetA** när den visas i Sök resultaten. Ett blad visas för det virtuella **myVnetA** -nätverket.
5. På bladet **myVnetA** som visas klickar du på **åtkomst kontroll (IAM)** i den lodräta listan med alternativ på vänster sida av bladet.
6. Klicka på **+ Lägg till roll tilldelning** på bladet **myVnetA-Access Control (IAM)** som visas.
7. På bladet **Lägg till roll tilldelning** som visas väljer du **nätverks deltagare** i rutan **roll** .
8. I rutan **Välj** väljer du användare b eller skriver användare b e-postadress för att söka efter den. Listan med användare som visas är från samma Azure Active Directory klient som det virtuella nätverk som du konfigurerar peering för. Klicka på användare b när den visas i listan.
9. Klicka på **Spara**.
10. Logga ut från portalen som användare a och logga sedan in som användare b.
11. Klicka på **+ ny**, Skriv *virtuellt nätverk* i rutan **Sök i Marketplace** och klicka sedan på **virtuellt nätverk** i Sök resultatet.
12. Välj **klassiskt** i rutan **Välj en distributions modell** på bladet **Virtual Network** som visas och klicka sedan på **skapa**.
13. I rutan skapa virtuellt nätverk (klassisk) som visas anger du följande värden:

    - **Namn**: *myVnetB*
    - **Adress utrymme**: *10.1.0.0/16*
    - **Under näts namn**: *standard*
    - **Adress intervall för under nätet**: *10.1.0.0/24*
    - **Prenumeration**: Välj prenumeration B.
    - **Resurs grupp**: Välj **Skapa ny** och ange *myResourceGroupB*
    - **Plats**: *östra USA*

14. I rutan **Sök resurser** överst i portalen skriver du *myVnetB*. Klicka på **myVnetB** när den visas i Sök resultaten. Ett blad visas för det virtuella **myVnetB** -nätverket.
15. På bladet **myVnetB** som visas klickar du på **Egenskaper** i den lodräta listan med alternativ på vänster sida av bladet. Kopiera **resurs-ID**, som används i ett senare steg. Resurs-ID liknar följande exempel: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. Slutför steg 5-9 för myVnetB och ange **användare a** i steg 8.
17. Logga ut från portalen som användare b och logga in som användare a.
18. I rutan **Sök resurser** överst i portalen skriver du *myVnetA*. Klicka på **myVnetA** när den visas i Sök resultaten. Ett blad visas för det virtuella **myVnet** -nätverket.
19. Klicka på **myVnetA**.
20. På bladet **myVnetA** som visas klickar du på **peering** i den lodräta listan med alternativ på vänster sida av bladet.
21. Klicka på **+ Lägg till** i bladet **myVnetA-peering** som visades.
22. I bladet **Lägg till peering** som visas anger eller väljer du följande alternativ och klickar sedan på **OK**:
     - **Namn**: *myVnetAToMyVnetB*
     - **Distributions modell för virtuellt nätverk**: Välj **klassisk**.
     - **Jag känner till mitt resurs-ID**: Markera den här kryss rutan.
     - **Resurs-ID**: Ange resurs-ID: t för myVnetB från steg 15.
     - **Tillåt åtkomst till virtuellt nätverk:** Se till att **aktive rad** är markerat.
    Inga andra inställningar används i den här självstudien. Om du vill veta mer om alla peering-inställningar läser du [Hantera peering för virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
23. När du klickar på **OK** i föregående steg stängs bladet **Lägg till peering** och du ser bladet **myVnetA-peering** igen. Efter några sekunder visas peering du skapade på bladet. **Ansluten** visas i kolumnen **peering-status** för **myVnetAToMyVnetB** -peering som du skapade. Peer kopplingen har nu upprättats. Det finns inget behov av att peer-koppla det virtuella nätverket (klassisk) till det virtuella nätverket (Resource Manager).

    Alla Azure-resurser som du skapar i ett virtuellt nätverk kan nu kommunicera med varandra via deras IP-adresser. Om du använder Azures standard namn matchning för virtuella nätverk kan inte resurserna i de virtuella nätverken matcha namn i de virtuella nätverken. Om du vill matcha namn i virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [namn matchning med hjälp av en egen DNS-Server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Valfritt**: om du skapar virtuella datorer som inte ingår i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att verifiera anslutningen.
25. **Valfritt**: om du vill ta bort de resurser som du skapar i den här självstudien utför du stegen i avsnittet [ta bort resurser](#delete-portal) i den här artikeln.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Skapa peering – Azure CLI

I den här självstudien används olika konton för varje prenumeration. Om du använder ett konto som har behörigheter till båda prenumerationerna kan du använda samma konto för alla steg, hoppa över stegen för att logga ut från Azure och ta bort de rader med skript som skapar användar roll tilldelningar. Ersätt UserA@azure.com och UserB@azure.com i alla följande skript med de användar namn som du använder för användare a och användare b. Utför följande steg med hjälp av den klassiska Azure-CLI: en och Azure CLI. Du kan slutföra stegen från Azure Cloud Shell genom att välja knappen **prova** i något av följande steg, eller genom att installera den [klassiska CLI](/cli/azure/install-classic-cli) och [CLI](/cli/azure/install-azure-cli) och köra kommandona på den lokala datorn.

1. Om du använder Cloud Shell går du vidare till steg 2, eftersom Cloud Shell automatiskt loggar in dig på Azure. Öppna en kommando-session och logga in på Azure med hjälp av `azure login` kommandot.
2. Kör den klassiska CLI i Service Management-läge genom att ange `azure config mode asm` kommandot.
3. Ange följande klassiska CLI-kommando för att skapa det virtuella nätverket (klassisk):

    ```console
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

4. Återstående steg måste utföras med hjälp av ett bash-gränssnitt med Azure CLI (inte klassisk CLI).
5. Kopiera följande skript till en text redigerare på din dator. Ersätt `<SubscriptionB-Id>` med ditt prenumerations-ID. Om du inte känner till ditt prenumerations-ID anger du `az account show` kommandot. Värdet för **ID** i utdata är ditt prenumerations-ID. kopiera det ändrade skriptet, klistra in det i CLI-sessionen och tryck sedan på `Enter` .

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    När du skapade det virtuella nätverket (klassisk) i steg 4 skapade Azure det virtuella nätverket i resurs gruppen *standard nätverk* .
6. Logga användare b från Azure och logga in som användare a i CLI.
7. Skapa en resurs grupp och ett virtuellt nätverk (Resource Manager). Kopiera följande skript, klistra in det i till CLI-sessionen och tryck sedan på `Enter` .

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

8. Skapa en virtuell nätverks-peering mellan de två virtuella nätverk som skapats via de olika distributions modellerna. Kopiera följande skript till en text redigerare på din dator. Ersätt `<SubscriptionB-id>` med ditt prenumerations-ID. Om du inte känner till ditt prenumerations-ID anger du `az account show` kommandot. Värdet för **ID** i utdata är ditt prenumerations-ID. Azure skapade det virtuella nätverk (klassisk) som du skapade i steg 4 i en resurs grupp med namnet *default-Networking*. Klistra in det ändrade skriptet i CLI-sessionen och tryck sedan på `Enter` .

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. När skriptet har körts granskar du peering för det virtuella nätverket (Resource Manager). Kopiera följande skript och klistra in det i CLI-sessionen:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```

    Utdata visar **anslutna** i kolumnen **PeeringState** .

    Alla Azure-resurser som du skapar i ett virtuellt nätverk kan nu kommunicera med varandra via deras IP-adresser. Om du använder Azures standard namn matchning för virtuella nätverk kan inte resurserna i de virtuella nätverken matcha namn i de virtuella nätverken. Om du vill matcha namn i virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [namn matchning med hjälp av en egen DNS-Server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. **Valfritt**: om du skapar virtuella datorer som inte ingår i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att verifiera anslutningen.
11. **Valfritt**: om du vill ta bort de resurser som du skapar i den här självstudien slutför du stegen i [ta bort resurser](#delete-cli) i den här artikeln.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Skapa peering – PowerShell

I den här självstudien används olika konton för varje prenumeration. Om du använder ett konto som har behörigheter till båda prenumerationerna kan du använda samma konto för alla steg, hoppa över stegen för att logga ut från Azure och ta bort de rader med skript som skapar användar roll tilldelningar. Ersätt UserA@azure.com och UserB@azure.com i alla följande skript med de användar namn som du använder för användare a och användare b. 

1. Installera den senaste versionen av PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) -och [AZ](https://www.powershellgallery.com/packages/Az) -modulerna. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta en PowerShell-session.
3. I PowerShell loggar du in på användare b-prenumerationen som användare b genom att ange `Add-AzureAccount` kommandot. Det konto som du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks-peering. En lista över behörigheter finns i [behörigheter för virtuella nätverks-peering](virtual-network-manage-peering.md#permissions).
4. Om du vill skapa ett virtuellt nätverk (klassiskt) med PowerShell måste du skapa en ny eller ändra en befintlig nätverks konfigurations fil. Lär dig hur du [exporterar, uppdaterar och importerar filer för nätverks konfiguration](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). Filen bör innehålla följande **VirtualNetworkSite** -element för det virtuella nätverk som används i den här självstudien:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
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

5. Logga in på användare b-prenumerationen som användare b för att använda Resource Manager-kommandon genom att ange `Connect-AzAccount` kommandot.
6. Tilldela användare a-behörigheter till det virtuella nätverket B. Kopiera följande skript till en text redigerare på din dator och Ersätt `<SubscriptionB-id>` med ID: t för prenumeration B. Om du inte känner till prenumerations-ID: t anger du `Get-AzSubscription` kommandot för att visa det. Värdet för **ID** i det returnerade resultatet är ditt PRENUMERATIONS-ID. Azure skapade det virtuella nätverk (klassisk) som du skapade i steg 4 i en resurs grupp med namnet *default-Networking*. Om du vill köra skriptet kopierar du det ändrade skriptet, klistrar in det i PowerShell och trycker sedan på `Enter` .

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Logga ut från Azure som användare b och logga in på användare a-prenumerationen som användare a genom att ange `Connect-AzAccount` kommandot. Det konto som du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks-peering. En lista över behörigheter finns i [behörigheter för virtuella nätverks-peering](virtual-network-manage-peering.md#permissions).
8. Skapa det virtuella nätverket (Resource Manager) genom att kopiera följande skript, klistra in det i PowerShell och sedan trycka på `Enter` :

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Tilldela användare b-behörigheter till myVnetA. Kopiera följande skript till en text redigerare på din dator och Ersätt `<SubscriptionA-Id>` med ID: t för prenumeration a. Om du inte känner till prenumerations-ID: t anger du `Get-AzSubscription` kommandot för att visa det. Värdet för **ID** i det returnerade resultatet är ditt PRENUMERATIONS-ID. Klistra in den ändrade versionen av skriptet i PowerShell och tryck sedan på `Enter` för att köra den.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Kopiera följande skript till en text redigerare på din dator och Ersätt `<SubscriptionB-id>` med ID: t för prenumeration B. För peer-myVnetA till myVNetB kopierar du det ändrade skriptet, klistrar in det i PowerShell och trycker sedan på `Enter` .

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Visa peering-statusen för myVnetA genom att kopiera följande skript, klistra in det i PowerShell och trycka på `Enter` .

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Statusen är **ansluten**. Den ändras till **ansluten** när du har ställt in peering på MyVnetA från myVnetB.

    Alla Azure-resurser som du skapar i ett virtuellt nätverk kan nu kommunicera med varandra via deras IP-adresser. Om du använder Azures standard namn matchning för virtuella nätverk kan inte resurserna i de virtuella nätverken matcha namn i de virtuella nätverken. Om du vill matcha namn i virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [namn matchning med hjälp av en egen DNS-Server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Valfritt**: om du skapar virtuella datorer som inte ingår i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att verifiera anslutningen.
13. **Valfritt**: om du vill ta bort de resurser som du skapar i den här självstudien slutför du stegen i [ta bort resurser](#delete-powershell) i den här artikeln.

## <a name="delete-resources"></a><a name="delete"></a>Ta bort resurser
När du är klar med den här självstudien kanske du vill ta bort de resurser som du skapade i självstudien, så du får inte använda avgifter för användning. Om du tar bort en resurs grupp tas även alla resurser i resurs gruppen bort.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. Skriv **myResourceGroupA** i rutan Portal Sök. Klicka på **myResourceGroupA** i Sök resultaten.
2. Klicka på ikonen **ta bort** på bladet **myResourceGroupA** .
3. Bekräfta borttagningen genom att ange **myResourceGroupA** i rutan **Skriv resurs gruppens namn** och klicka på **ta bort**.
4. I rutan **Sök resurser** överst i portalen skriver du *myVnetB*. Klicka på **myVnetB** när den visas i Sök resultaten. Ett blad visas för det virtuella **myVnetB** -nätverket.
5. Klicka på **ta bort** på bladet **myVnetB** .
6. Bekräfta borttagningen genom att klicka på **Ja** i rutan **ta bort virtuellt nätverk** .

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Logga in på Azure med CLI för att ta bort det virtuella nätverket (Resource Manager) med följande kommando:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Logga in på Azure med hjälp av klassisk CLI för att ta bort det virtuella nätverket (klassisk) med följande kommandon:

   ```console
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. I kommando tolken PowerShell anger du följande kommando för att ta bort det virtuella nätverket (Resource Manager):

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. Om du vill ta bort det virtuella nätverket (klassisk) med PowerShell måste du ändra en befintlig nätverks konfigurations fil. Lär dig hur du [exporterar, uppdaterar och importerar filer för nätverks konfiguration](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). Ta bort följande VirtualNetworkSite-element för det virtuella nätverk som används i den här självstudien:

   ```xml
   <VirtualNetworkSite name="myVnetB" Location="East US">
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