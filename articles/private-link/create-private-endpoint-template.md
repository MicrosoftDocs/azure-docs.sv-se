---
title: Snabb start – skapa en privat slut punkt med en ARM-mall
description: I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skapa en privat slut punkt.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: e80adc16e08e676ac2daabec01a11c10d537c547
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562953"
---
# <a name="quickstart-create-a-private-endpoint-by-using-an-arm-template"></a>Snabb start: skapa en privat slut punkt med en ARM-mall

I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skapa en privat slut punkt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Du kan också slutföra den här snabb starten genom att använda [Azure Portal](create-private-endpoint-portal.md), [Azure POWERSHELL](create-private-endpoint-powershell.md)eller [Azure CLI](create-private-endpoint-cli.md).

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Du behöver ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Granska mallen

Den här mallen skapar en privat slut punkt för en instans av Azure SQL Database.

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-private-endpoint-sql/).

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft. SQL/Servers**](/azure/templates/microsoft.sql/servers): instansen av SQL Database med exempel databasen.
- [**Microsoft. SQL/Servers/databaser**](/azure/templates/microsoft.sql/servers/databases): exempel databasen.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): det virtuella nätverk där den privata slut punkten distribueras.
- [**Microsoft. Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints): den privata slut punkten för att få åtkomst till instansen av SQL Database.
- [**Microsoft. Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones): zonen som används för att matcha den privata slut PUNKTens IP-adress.
- [**Microsoft. Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft. Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups): den zon grupp som används för att koppla den privata slut punkten till en privat DNS-zon.
- [**Microsoft. Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): den offentliga IP-adress som används för att få åtkomst till den virtuella datorn.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): nätverks gränssnittet för den virtuella datorn.
- [**Microsoft. Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): den virtuella dator som används för att testa den privata anslutningen med en privat slut punkt till instansen av SQL Database.

## <a name="deploy-the-template"></a>Distribuera mallen

Så här distribuerar du ARM-mallen till Azure:

1. Om du vill logga in på Azure och öppna mallen väljer du **distribuera till Azure**. Mallen skapar den privata slut punkten, instansen av SQL Database, nätverks infrastrukturen och en virtuell dator som ska verifieras.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Välj eller skapa en resurs grupp.
3. Ange inloggning och lösen ord för SQL-administratören.
4. Ange administratörs användar namn och lösen ord för den virtuella datorn.
5. Läs villkors satsen. Om du samtycker väljer du **Jag accepterar villkoren som anges ovan**  >  **köpet**. Distributionen kan ta 20 minuter eller längre att slutföra.

## <a name="validate-the-deployment"></a>Verifiera distributionen

> [!NOTE]
> ARM-mallen genererar ett unikt namn för den virtuella datorns myVm<b>{UniqueID}</b> -resurs och för resursen SQL Database SQLServer<b>{UniqueID}</b> . Ersätt ditt genererade värde för **{UniqueID}**.

### <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Anslut till VM- _myVm {UniqueID}_ från Internet på följande sätt:

1. I portalens Sök fält anger du _myVm {UniqueID}_.

2. Välj **Anslut**. **Anslut till den virtuella datorn** öppnas.

3. Välj **Hämta RDP-fil**. Azure skapar en Remote Desktop Protocol-fil (_. RDP_) och laddar ned den till datorn.

4. Öppna den nedladdade RDP-filen.

   a. Välj **Anslut** om du uppmanas att göra det.

   b. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

      > [!NOTE]
      > Du kan behöva välja **fler alternativ**  >  **Använd ett annat konto** för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

5. Välj **OK**.

6. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

7. När den virtuella datorns skriv bord visas kan du minimera det och gå tillbaka till det lokala Skriv bordet.

### <a name="access-the-sql-database-server-privately-from-the-vm"></a>Få åtkomst till SQL Database servern privat från den virtuella datorn

Så här ansluter du till SQL Database-servern från den virtuella datorn med hjälp av den privata slut punkten.

1.  Öppna PowerShell i fjärr skrivbordet för _myVM {UniqueID}_.
2.  Ange följande: nslookup SQLServer {UniqueID}. Database. Windows. net. 
    Du får ett meddelande som liknar detta:

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  Installera SQL Server Management Studio.
4.  I **Anslut till Server** anger eller väljer du den här informationen:
    - **Server typ**: Välj **databas motor**.
    - **Server namn**: Välj **SQLServer {UniqueID}. Database. Windows. net**.
    - **Användar namn**: Ange ett användar namn som angavs vid skapandet.
    - **Lösen ord**: Ange ett lösen ord som anges när du skapar.
    - **Kom ihåg lösen ord**: Välj  **Ja**.

5.  Välj **Anslut**.
6.  Gå till **databaser** på menyn till vänster.
7.  Du kan också skapa eller fråga efter information från _exempel-DB_.
8.  Stäng fjärr skrivbords anslutningen till _myVm {UniqueID}_.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med den privata slut punkten tar du bort resurs gruppen. Den privata slut punkten och alla relaterade resurser tas bort.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

Mer information om de tjänster som stöder en privat slut punkt finns i:
> [!div class="nextstepaction"]
> [Tillgänglighet för privat länk](private-link-overview.md#availability)
