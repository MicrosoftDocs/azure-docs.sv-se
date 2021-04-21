---
title: Private Link – Azure CLI – Azure Database for PostgreSQL – enskild server
description: Lär dig hur du konfigurerar privat länk för Azure Database for PostgreSQL – enskild server från Azure CLI
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 410da33b531524f4a6458df13a89807fedd739a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773341"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-cli"></a>Skapa och hantera Private Link för Azure Database for PostgreSQL – enskild server med CLI

En privat slutpunkt är den grundläggande byggstenen för privat länk i Azure. Det gör det möjligt för Azure-Virtual Machines (VM) att kommunicera privat med privata länkresurser. I den här artikeln får du lära dig hur du använder Azure CLI för att skapa en virtuell dator i en Azure Virtual Network och en Azure Database for PostgreSQL enskild server med en privat Azure-slutpunkt.

> [!NOTE]
> Funktionen private link är endast tillgänglig för Azure Database for PostgreSQL servrar i Generell användning eller minnesoptimerade prisnivåer. Se till att databasservern är på någon av dessa prisnivåer.

## <a name="prerequisites"></a>Förutsättningar

Om du vill gå igenom den här guiden behöver du:

- En [Azure Database for PostgreSQL server och databas](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i stället, måste du köra Azure CLI version 2.0.28 eller senare i den här snabbstarten. Kör `az --version` för att hitta den installerade versionen. Se [Installera Azure CLI](/cli/azure/install-azure-cli) för installations- eller uppgraderingsinformation.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa en resurs måste du skapa en resursgrupp som ska vara värd för Virtual Network. Skapa en resursgrupp med [az group create](/cli/azure/group). I det här exemplet skapas en resursgrupp *med namnet myResourceGroup* på *platsen westeurope:*

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa en Virtual Network med [az network vnet create](/cli/azure/network/vnet). I det här exemplet skapas en Virtual Network med *namnet myVirtualNetwork* med ett undernät med namnet *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Inaktivera principer för privata slutpunkter för undernät 
Azure distribuerar resurser till ett undernät i ett virtuellt nätverk, så du måste skapa eller uppdatera undernätet för att inaktivera nätverksprinciper för [privata slutpunkter.](../private-link/disable-private-endpoint-network-policy.md) Uppdatera en undernätskonfiguration med *namnet mySubnet* med [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Skapa den virtuella datorn 
Skapa en virtuell dator med az vm create. När du uppmanas till det anger du ett lösenord som ska användas som inloggningsuppgifter för den virtuella datorn. I det här exemplet skapas en virtuell dator med *namnet myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```

 Anteckna den virtuella datorns offentliga IP-adress. Du kommer att använda den här adressen när du ansluter till den virtuella datorn från Internet i nästa steg.

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Skapa en Azure Database for PostgreSQL – enskild server 
Skapa en Azure Database for PostgreSQL med kommandot az postgres server create. Kom ihåg att namnet på Din PostgreSQL-server måste vara unikt i Azure, så ersätt platshållarvärdet med dina egna unika värden som du använde ovan: 

```azurecli-interactive
# Create a server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

## <a name="create-the-private-endpoint"></a>Skapa den privata slutpunkten 
Skapa en privat slutpunkt för PostgreSQL-servern i din Virtual Network: 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforPostgreSQL/servers" --query "id" -o tsv) \    
    --group-id postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Konfigurera Privat DNS zon 
Skapa en Privat DNS zon för PostgreSQL-serverdomänen och skapa en kopplingslänk till Virtual Network. 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.postgres.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.postgres.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for PostgreSQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Det fullständiga domännamnet i DNS-inställningen för kunden matchar inte den privata IP-adressen som har konfigurerats. Du måste konfigurera en DNS-zon för det konfigurerade FQDN som visas [här.](../dns/dns-operations-recordsets-portal.md)

> [!NOTE]
> I vissa fall Azure Database for PostgreSQL och VNet-undernätet i olika prenumerationer. I dessa fall måste du kontrollera följande konfigurationer:
> - Kontrollera att båda prenumerationerna har **resursprovidern Microsoft.DBforPostgreSQL** registrerad. Mer information finns i [resursproviders.](../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Anslut till den virtuella *datorn myVm* från Internet på följande sätt:

1. I portalens sökfältet anger du *myVm*.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Hämta RDP-fil**. Azure skapar en Remote Desktop Protocol *(.rdp)* och laddar ned den till datorn.

1. Öppna filen *downloaded.rdp.*

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användarnamn och lösenord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja Fler **alternativ Använd ett** annat konto för att  >  **ange** autentiseringsuppgifterna som du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Få åtkomst till PostgreSQL-servern privat från den virtuella datorn

1. På fjärrskrivbordet på *myVM* öppnar du PowerShell.

2. Ange  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

   Du får ett meddelande som liknar detta:

   ```azurepowershell
   Server:  UnKnown
   Address:  168.63.129.16
   Non-authoritative answer:
   Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
   Address:  10.1.3.4
   ```

3. Testa den privata länkanslutningen för PostgreSQL-servern med valfri tillgänglig klient. I följande exempel används [Azure Data Studio](/sql/azure-data-studio/download) för att göra åtgärden.

4. I **Ny anslutning** anger eller väljer du den här informationen:

   | Inställning | Värde |
   | ------- | ----- |
   | Servertyp| Välj **PostgreSQL**.|
   | Servernamn| Välj *mydemopostgresserver.privatelink.postgres.database.azure.com* |
   | Användarnamn | Ange det användarnamn username@servername som angavs när PostgreSQL-servern skapades. |
   |Lösenord |Ange ett lösenord som angavs när PostgreSQL-servern skapades. |
   |SSL|Välj **Obligatorisk.**|
   ||

5. Välj Anslut.

6. Bläddra i databaser från den vänstra menyn.

7. (Valfritt) Skapa eller fråga efter information från postgreSQL-servern.

8. Stäng fjärrskrivbordsanslutningen till myVm.

## <a name="clean-up-resources"></a>Rensa resurser 
När den inte längre behövs kan du använda az group delete för att ta bort resursgruppen och alla resurser som den har: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Vad är privat Azure-slutpunkt?](../private-link/private-endpoint-overview.md)
