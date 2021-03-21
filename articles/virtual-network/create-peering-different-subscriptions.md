---
title: Skapa en VNet-peering – olika prenumerationer
titlesuffix: Azure Virtual Network
description: Lär dig hur du skapar en virtuell nätverks-peering mellan virtuella nätverk som skapats via Resource Manager som finns i olika Azure-prenumerationer i samma eller en annan Azure Active Directory klient organisation.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: kumud
ms.openlocfilehash: 1f8b7162787e870337918794f353ab8dc94c1965
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518253"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions-and-azure-active-directory-tenants"></a>Skapa ett virtuellt nätverk peering – Resource Manager, olika prenumerationer och Azure Active Directory klienter

I den här självstudien lär du dig att skapa en virtuell nätverks-peering mellan virtuella nätverk som skapats via Resource Manager. De virtuella nätverken finns i olika prenumerationer som kan höra till olika Azure Active Directory-klienter (Azure AD). Peering av två virtuella nätverk gör det möjligt för resurser i olika virtuella nätverk att kommunicera med varandra med samma bandbredd och svars tid som om resurserna fanns i samma virtuella nätverk. Läs mer om [peering för virtuella nätverk](virtual-network-peering-overview.md).

Stegen för att skapa en virtuell nätverks-peering skiljer sig beroende på om de virtuella nätverken finns i samma, eller olika, prenumerationer och vilken Azure- [distributions modell](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som de virtuella nätverken skapas via. Lär dig hur du skapar en virtuell nätverks-peering i andra scenarier genom att välja scenariot i följande tabell:

|Azure-distributionsmodell  | Azure-prenumeration  |
|--------- |---------|
|[Båda Resource Manager](tutorial-connect-virtual-networks-portal.md) |Samma|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models.md) |Samma|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models-subscriptions.md) |Olika|

Det går inte att skapa en virtuell nätverks-peering mellan två virtuella nätverk som distribueras via den klassiska distributions modellen. Om du behöver ansluta virtuella nätverk som båda har skapats via den klassiska distributions modellen kan du använda en Azure- [VPN gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att ansluta de virtuella nätverken.

Den här självstudien peererar virtuella nätverk i samma region. Du kan också peer-virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region). Vi rekommenderar att du bekantar dig med [peering-kraven och begränsningarna](virtual-network-manage-peering.md#requirements-and-constraints) innan du peer-koppla virtuella nätverk.

Du kan använda [Azure Portal](#portal), Azure [Command-Line Interface](#cli) (CLI), azure [PowerShell](#powershell)eller en [Azure Resource Manager mall](#template) för att skapa en virtuell nätverks-peering. Välj något av de föregående verktyg-länkarna för att gå direkt till stegen för att skapa en virtuell nätverks-peering med hjälp av ditt eget verktyg.

Om de virtuella nätverken finns i olika prenumerationer och prenumerationerna är kopplade till olika Azure Active Directory klienter, slutför du följande steg innan du fortsätter:
1. Lägg till användaren från varje Active Directory klient organisation som [gäst användare](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) i den motsatta Azure Active Directory klienten.
1. Varje användare måste acceptera gästanvändarinbjudan från den andra Azure Active Directory-klientorganisationen.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Skapa peering-Azure Portal

Följande steg använder olika konton för varje prenumeration. Om du använder ett konto som har behörigheter till båda prenumerationerna kan du använda samma konto för alla steg, hoppa över stegen för att logga ut från portalen och hoppa över stegen för att tilldela de virtuella nätverken till en annan användar behörighet.

1. Logga in på [Azure Portal](https://portal.azure.com) som *användare a*. Det konto som du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks-peering. En lista över behörigheter finns i [behörigheter för virtuella nätverks-peering](virtual-network-manage-peering.md#permissions).
2. Välj **+ skapa en resurs**, Välj **nätverk** och välj sedan **virtuellt nätverk**.
3. Välj eller ange följande exempel värden för följande inställningar och välj sedan **skapa**:
    - **Namn**: *myVnetA*
    - **Adress utrymme**: *10.0.0.0/16*
    - **Under näts namn**: *standard*
    - **Adress intervall för under nätet**: *10.0.0.0/24*
    - **Prenumeration**: Välj prenumeration A.
    - **Resurs grupp**: Välj **Skapa ny** och ange *myResourceGroupA*
    - **Plats**: *östra USA*
4. I rutan **Sök resurser** överst i portalen skriver du *myVnetA*. Välj **myVnetA** när den visas i Sök resultaten.
5. Välj **åtkomst kontroll (IAM)** i den lodräta listan med alternativ på vänster sida.
6. Under **myVnetA-Access Control (IAM)** väljer du **+ Lägg till roll tilldelning**.
7. Välj **nätverks deltagare** i rutan **roll** .
8. I rutan **Välj** väljer du *användare b* eller skriver användare b e-postadress för att söka efter den.
9. Välj **Spara**.
10. Under **myVnetA-Access Control (IAM)** väljer du **Egenskaper** i den lodräta listan med alternativ på vänster sida. Kopiera **resurs-ID**, som används i ett senare steg. Resurs-ID liknar följande exempel: `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA` .
11. Logga ut från portalen som användare a och logga sedan in som användare b.
12. Slutför steg 2-3, ange eller Välj följande värden i steg 3:

    - **Namn**: *myVnetB*
    - **Adress utrymme**: *10.1.0.0/16*
    - **Under näts namn**: *standard*
    - **Adress intervall för under nätet**: *10.1.0.0/24*
    - **Prenumeration**: Välj prenumeration B.
    - **Resurs grupp**: Välj **Skapa ny** och ange *myResourceGroupB*
    - **Plats**: *östra USA*

13. I rutan **Sök resurser** överst i portalen skriver du *myVnetB*. Välj **myVnetB** när den visas i Sök resultaten.
14. Under **myVnetB** väljer du **Egenskaper** i den lodräta listan med alternativ på vänster sida. Kopiera **resurs-ID**, som används i ett senare steg. Resurs-ID liknar följande exempel: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB` .
15. Välj **åtkomst kontroll (IAM)** under **myVnetB** och slutför sedan steg 5-10 för myVnetB och ange **användare a** i steg 8.
16. Logga ut från portalen som användare b och logga in som användare a.
17. I rutan **Sök resurser** överst i portalen skriver du *myVnetA*. Välj **myVnetA** när den visas i Sök resultaten.
18. Välj **myVnetA**.
19. Under **Inställningar** väljer du **peering**.
20. Under **myVnetA-peering** väljer du **+ Lägg till**
21. Under **Lägg till peering**, anger eller väljer du följande alternativ och väljer sedan **OK**:
     - **Namn**: *myVnetAToMyVnetB*
     - **Distributions modell för virtuellt nätverk**: Välj **Resource Manager**.
     - **Jag känner till mitt resurs-ID**: Markera den här kryss rutan.
     - **Resurs-ID**: Ange resurs-ID: t från steg 14.
     - **Tillåt åtkomst till virtuellt nätverk:** Se till att **aktive rad** är markerat.
    Inga andra inställningar används i den här självstudien. Om du vill veta mer om alla peering-inställningar läser du [Hantera peering för virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
22. Peering som du har skapat visas som en kort väntan när du har valt **OK** i föregående steg. **Initierad** visas i kolumnen **peering-status** för **myVnetAToMyVnetB** -peering som du skapade. Du har peer-kopplat myVnetA till myVnetB, men nu måste du peer-myVnetB till myVnetA. Peering måste skapas i båda riktningarna för att resurser i de virtuella nätverken ska kunna kommunicera med varandra.
23. Logga ut från portalen som användare a och logga in som användare b.
24. Slutför steg 17-21 igen för myVnetB. I steg 21 namnger du peering- *myVnetBToMyVnetA*, väljer *myVnetA* för **virtuellt nätverk** och anger ID: t från steg 10 i rutan **resurs-ID** .
25. Några sekunder efter att du har valt **OK** för att skapa peering för myVnetB, visas **myVnetBToMyVnetA** -peering som du nyss **skapade i** kolumnen **peering-status** .
26. Logga ut från portalen som användare b och logga in som användare a.
27. Slutför steg 17-19 igen. **Peering-statusen** för **myVnetAToVNetB** -peering är nu också **ansluten**. Peer kopplingen upprättas efter att du ser **ansluten** i kolumnen **peering status** för båda virtuella nätverken i peer kopplingen. Alla Azure-resurser som du skapar i ett virtuellt nätverk kan nu kommunicera med varandra via deras IP-adresser. Om du använder Azures standard namn matchning för virtuella nätverk kan inte resurserna i de virtuella nätverken matcha namn i de virtuella nätverken. Om du vill matcha namn i virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [namn matchning med hjälp av en egen DNS-Server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Valfritt**: om du skapar virtuella datorer som inte ingår i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att verifiera anslutningen.
29. **Valfritt**: om du vill ta bort de resurser som du skapar i den här självstudien utför du stegen i avsnittet [ta bort resurser](#delete-portal) i den här artikeln.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Skapa peering – Azure CLI

I den här självstudien används olika konton för varje prenumeration. Om du använder ett konto som har behörigheter till båda prenumerationerna kan du använda samma konto för alla steg, hoppa över stegen för att logga ut från Azure och ta bort de rader med skript som skapar användar roll tilldelningar. Ersätt UserA@azure.com och UserB@azure.com i alla följande skript med de användar namn som du använder för användare a och användare b.

Följande skript:

- Kräver Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Fungerar i ett bash-gränssnitt. Läs mer om körningsalternativ för Azure CLI-skript i Windows-klienter i informationen om att [installera Azure CLI för Windows](/cli/azure/install-azure-cli-windows).

I stället för att installera CLI och dess beroenden kan du använda Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Välj knappen **testa** i skriptet som följer, som anropar en Cloud Shell som du kan logga in på ditt Azure-konto med.

1. Öppna en CLI-session och logga in på Azure som användare a med hjälp av `azure login` kommandot. Det konto som du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks-peering. En lista över behörigheter finns i [behörigheter för virtuella nätverks-peering](virtual-network-manage-peering.md#permissions).
2. Kopiera följande skript till en text redigerare på din dator, Ersätt `<SubscriptionA-Id>` med ID för prenumerationen och kopiera sedan det ändrade skriptet, klistra in det i CLI-sessionen och tryck på `Enter` . Om du inte känner till ditt prenumerations-ID anger du `az account show` kommandot. Värdet för **ID** i utdata är ditt prenumerations-ID.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. Logga ut från Azure som användare a med `az logout` kommandot och logga sedan in på Azure som användare b. Det konto som du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks-peering. En lista över behörigheter finns i [behörigheter för virtuella nätverks-peering](virtual-network-manage-peering.md#permissions).
4. Skapa myVnetB. Kopiera skript innehållet i steg 2 till en text redigerare på din dator. Ersätt `<SubscriptionA-Id>` med ID: t för prenumerationb. Ändra 10.0.0.0/16 till 10.1.0.0/16, ändra alla till B och alla BS till A. kopiera det ändrade skriptet, klistra in det i CLI-sessionen och tryck på `Enter` .
5. Logga ut från Azure som användare b och logga in på Azure som användare a.
6. Skapa en virtuell nätverks-peering från myVnetA till myVnetB. Kopiera följande skript innehåll till en text redigerare på din dator. Ersätt `<SubscriptionB-Id>` med ID: t för prenumerationb. Om du vill köra skriptet kopierar du det ändrade skriptet, klistrar in det i CLI-sessionen och trycker på RETUR.

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Visa peering-statusen för myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Statusen **initieras**. Den ändras till **ansluten** när du skapar peer-kopplingen till MyVnetA från myVnetB.

8. Logga ut användare a från Azure och logga in på Azure som användare b.
9. Skapa peering från myVnetB till myVnetA. Kopiera skript innehållet i steg 6 till en text redigerare på din dator. Ersätt `<SubscriptionB-Id>` med ID för prenumerationen och ändra alla till B och alla BS till A. När du har gjort ändringarna kopierar du det ändrade skriptet, klistrar in det i CLI-sessionen och trycker på `Enter` .
10. Visa peering-statusen för myVnetB. Kopiera skript innehållet i steg 7 till en text redigerare på din dator. Ändra A till B för resurs gruppen och virtuella nätverks namn, kopiera skriptet, klistra in det ändrade skriptet i till CLI-sessionen och tryck sedan på `Enter` . Peering-statusen är **ansluten**. Peering-statusen för myVnetA ändras till **ansluten** efter att du har skapat peer-kopplingen från MyVnetB till myVnetA. Du kan logga användare a tillbaka till Azure och slutföra steg 7 igen för att kontrol lera peering-statusen för myVnetA.

    > [!NOTE]
    > Peer kopplingen upprättas inte förrän peering-statusen är **ansluten** för båda virtuella nätverken.

11. **Valfritt**: om du skapar virtuella datorer som inte ingår i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att verifiera anslutningen.
12. **Valfritt**: om du vill ta bort de resurser som du skapar i den här självstudien slutför du stegen i [ta bort resurser](#delete-cli) i den här artikeln.

Alla Azure-resurser som du skapar i ett virtuellt nätverk kan nu kommunicera med varandra via deras IP-adresser. Om du använder Azures standard namn matchning för virtuella nätverk kan inte resurserna i de virtuella nätverken matcha namn i de virtuella nätverken. Om du vill matcha namn i virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [namn matchning med hjälp av en egen DNS-Server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="create-peering---powershell"></a><a name="powershell"></a>Skapa peering – PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I den här självstudien används olika konton för varje prenumeration. Om du använder ett konto som har behörigheter till båda prenumerationerna kan du använda samma konto för alla steg, hoppa över stegen för att logga ut från Azure och ta bort de rader med skript som skapar användar roll tilldelningar. Ersätt UserA@azure.com och UserB@azure.com i alla följande skript med de användar namn som du använder för användare a och användare b.

1. Bekräfta att du har Azure PowerShell version 1.0.0 eller senare. Du kan göra detta genom att köra `Get-Module -Name Az` vi rekommenderar att du installerar den senaste versionen av PowerShell [AZ-modulen](/powershell/azure/install-az-ps). Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta en PowerShell-session.
3. I PowerShell loggar du in på Azure som användare a genom att ange `Connect-AzAccount` kommandot. Det konto som du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks-peering. En lista över behörigheter finns i [behörigheter för virtuella nätverks-peering](virtual-network-manage-peering.md#permissions).
4. Skapa en resurs grupp och ett virtuellt nätverk A. Kopiera följande skript till en text redigerare på din dator. Ersätt `<SubscriptionA-Id>` med ID: t för prenumerationen. Om du inte känner till ditt prenumerations-ID anger du `Get-AzSubscription` kommandot för att visa det. Värdet för **ID** i det returnerade resultatet är ditt PRENUMERATIONS-ID. Om du vill köra skriptet kopierar du det ändrade skriptet, klistrar in det i PowerShell och trycker sedan på `Enter` .

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Logga ut användare a från Azure och logga in användare b. Det konto som du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks-peering. En lista över behörigheter finns i [behörigheter för virtuella nätverks-peering](virtual-network-manage-peering.md#permissions).
6. Kopiera skript innehållet i steg 4 till en text redigerare på din dator. Ersätt `<SubscriptionA-Id>` med ID: t för prenumeration B. ändra 10.0.0.0/16 till 10.1.0.0/16. Ändra allt till och med B och BS till A. Om du vill köra skriptet kopierar du det ändrade skriptet, klistrar in i PowerShell och trycker sedan på `Enter` .
7. Logga ut användare b från Azure och logga in användare a.
8. Skapa peering från myVnetA till myVnetB. Kopiera följande skript till en text redigerare på din dator. Ersätt `<SubscriptionB-Id>` med ID: t för prenumeration B. Om du vill köra skriptet kopierar du det ändrade skriptet, klistrar in på PowerShell och trycker sedan på `Enter` .

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. Visa peering-statusen för myVnetA.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Statusen **initieras**. Den ändras till **ansluten** när du har ställt in peering på MyVnetA från myVnetB.

10. Logga ut användare a från Azure och logga in användare b.
11. Skapa peering från myVnetB till myVnetA. Kopiera skript innehållet i steg 8 till en text redigerare på din dator. Ersätt `<SubscriptionB-Id>` med ID: t för prenumeration A och ändra alla till B och alla BS till A. Om du vill köra skriptet kopierar du det ändrade skriptet, klistrar in det i PowerShell och trycker sedan på `Enter` .
12. Visa peering-statusen för myVnetB. Kopiera skript innehållet i steg 9 till en text redigerare på din dator. Ändra A till B för resurs gruppen och virtuella nätverks namn. Om du vill köra skriptet klistrar du in det ändrade skriptet i PowerShell och trycker sedan på `Enter` . Statusen är **ansluten**. Peering-statusen för **myVnetA** ändras till **ansluten** efter att du har skapat peer-kopplingen från **myVnetB** till **myVnetA**. Du kan logga användare a tillbaka till Azure och slutföra steg 9 igen för att kontrol lera peering-statusen för myVnetA.

    > [!NOTE]
    > Peer kopplingen upprättas inte förrän peering-statusen är **ansluten** för båda virtuella nätverken.

    Alla Azure-resurser som du skapar i ett virtuellt nätverk kan nu kommunicera med varandra via deras IP-adresser. Om du använder Azures standard namn matchning för virtuella nätverk kan inte resurserna i de virtuella nätverken matcha namn i de virtuella nätverken. Om du vill matcha namn i virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [namn matchning med hjälp av en egen DNS-Server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Valfritt**: om du skapar virtuella datorer som inte ingår i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att verifiera anslutningen.
14. **Valfritt**: om du vill ta bort de resurser som du skapar i den här självstudien slutför du stegen i [ta bort resurser](#delete-powershell) i den här artikeln.

## <a name="create-peering---resource-manager-template"></a><a name="template"></a>Skapa peering – Resource Manager-mall

1. För att skapa ett virtuellt nätverk och tilldela lämpliga [behörigheter](virtual-network-manage-peering.md#permissions), slutför du stegen i avsnitten [Portal](#portal), [Azure CLI](#cli)eller [PowerShell](#powershell) i den här artikeln.
2. Spara texten som följer efter en fil på den lokala datorn. Ersätt `<subscription ID>` med användare a prenumerations-ID. Du kan till exempel spara filen som vnetpeeringA.jspå.

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. Logga in på Azure som användare a och distribuera mallen med hjälp av [portalen](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [POWERSHELL](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template-or-bicep-file)eller [Azure CLI](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template-or-bicep-file). Ange fil namnet som du sparade exempel-JSON-texten i steg 2 till.
4. Kopiera exempel-JSON från steg 2 till en fil på datorn och ändra de rader som börjar med:
   - **namn**: ändra *myVnetA/myVnetAToMyVnetB* till *myVnetB/myVnetBToMyVnetA*.
   - **ID**: Ersätt `<subscription ID>` med användare B prenumerations-ID och ändra *myVnetB* till *myVnetA*.
5. Slutför steg 3 igen och logga in på Azure som användare b.
6. **Valfritt**: om du skapar virtuella datorer som inte ingår i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att verifiera anslutningen.
7. **Valfritt**: om du vill ta bort de resurser som du skapar i den här självstudien slutför du stegen i avsnittet [ta bort resurser](#delete) i den här artikeln med hjälp av antingen Azure Portal, PowerShell eller Azure CLI.

## <a name="delete-resources"></a><a name="delete"></a>Ta bort resurser
När du är klar med den här självstudien kanske du vill ta bort de resurser som du skapade i självstudien, så du får inte använda avgifter för användning. Om du tar bort en resurs grupp tas även alla resurser i resurs gruppen bort.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. Logga in på Azure Portal som användare a.
2. Skriv **myResourceGroupA** i rutan Portal Sök. I Sök resultaten väljer du **myResourceGroupA**.
3. Välj **Ta bort**.
4. Bekräfta borttagningen genom att ange **myResourceGroupA** i rutan **Skriv resurs gruppens namn** och välj sedan **ta bort**.
5. Logga ut från portalen som användare a och logga in som användare b.
6. Slutför steg 2-4 för myResourceGroupB.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Logga in på Azure som användare a och kör följande kommando:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Logga ut från Azure som användare a och logga in som användare b.
3. Kör följande kommando:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Logga in på Azure som användare a och kör följande kommando:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Logga ut från Azure som användare a och logga in som användare b.
3. Kör följande kommando:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Nästa steg

- Bekanta dig med viktiga [peering-begränsningar och beteenden för virtuella nätverk](virtual-network-manage-peering.md#requirements-and-constraints) innan du skapar en virtuell nätverks-peering för produktions användning.
- Lär dig mer om alla [peering-inställningar för virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
- Lär dig hur du [skapar en nätverkstopologi med nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) med peering av virtuella nätverk.