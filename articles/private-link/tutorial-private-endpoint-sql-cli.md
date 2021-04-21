---
title: 'Självstudie: Ansluta till en Azure SQL server med hjälp av en privat Azure-slutpunkt – Azure CLI'
description: Använd den här självstudien för att lära dig hur du skapar en Azure SQL-server med en privat slutpunkt med Hjälp av Azure CLI
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: a8fafeaaf974893c9a1a71115912f2a7b019ddd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771829"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>Självstudie: Ansluta till en Azure SQL server med hjälp av en privat Azure-slutpunkt – Azure CLI

Privat Azure-slutpunkt är den grundläggande byggstenen för Private Link i Azure. Det gör att Azure-resurser, till exempel virtuella datorer, kan kommunicera med Private Link resurser privat.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och en skyddsvärd.
> * Skapa en virtuell dator.
> * Skapa en Azure SQL server och privat slutpunkt.
> * Testa anslutningen till den privata slutpunkten för SQL Server.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Logga in på Azure Portal och kontrollera att din prenumeration är aktiv genom att köra `az login` .
* Kontrollera din version av Azure CLI i en terminal eller ett kommandofönster genom att köra `az --version` . Den senaste versionen finns i den senaste [versionsanteckningarna.](/cli/azure/release-notes-azure-cli?tabs=azure-cli)
  * Om du inte har den senaste versionen uppdaterar du installationen genom att följa [installationsguiden för ditt operativsystem eller din plattform.](/cli/azure/install-azure-cli)

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create):

* Med **namnet CreateSQLEndpointTutorial-rg**. 
* På **platsen eastus.**

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Skapa ett virtuellt nätverk och en skyddsvärd

I det här avsnittet skapar du ett virtuellt nätverk, ett undernät och en skyddsvärd. 

Skyddsvärden används för att ansluta säkert till den virtuella datorn för att testa den privata slutpunkten.

Skapa ett virtuellt nätverk med [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)

* Med **namnet myVNet**.
* Adressprefixet **10.0.0.0/16**.
* Undernät med **namnet myBackendSubnet**.
* Undernätsprefixet **10.0.0.0/24**.
* I **resursgruppen CreateSQLEndpointTutorial-rg.**
* Platsen **eastus**.

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Uppdatera undernätet för att inaktivera privata slutpunktsnätverksprinciper för den privata slutpunkten med [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update):

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Använd [az network public-ip create för](/cli/azure/network/public-ip#az_network_public_ip_create) att skapa en offentlig IP-adress för skyddsvärden:

* Skapa en redundant offentlig IP-adress i standardzonen med namnet **myBastionIP.**
* I **CreateSQLEndpointTutorial-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

Använd [az network vnet subnet create för](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) att skapa ett skyddsundernät:

* Med **namnet AzureBastionSubnet**.
* Adressprefixet **10.0.1.0/24**.
* I det virtuella **nätverket myVNet**.
* I resursgruppen **CreateSQLEndpointTutorial-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Använd [az network bastion create för](/cli/azure/network/bastion#az_network_bastion_create) att skapa en skyddsvärd:

* Med **namnet myBastionHost.**
* I **CreateSQLEndpointTutorial-rg**.
* Associerad med offentlig IP **myBastionIP**.
* Associerat med det virtuella nätverket **myVNet**.
* På **platsen eastus.**

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Det kan ta några minuter för den Azure Bastion värden att distribueras.

## <a name="create-test-virtual-machine"></a>Skapa virtuell testdator

I det här avsnittet skapar du en virtuell dator som ska användas för att testa den privata slutpunkten.

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). När du uppmanas till det anger du ett lösenord som ska användas som autentiseringsuppgifter för den virtuella datorn:

* Med **namnet myVM**.
* I **CreateSQLEndpointTutorial-rg**.
* I nätverket **myVNet**.
* I undernätet **myBackendSubnet**.
* Serveravbildning **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-an-azure-sql-server"></a>Skapa en Azure SQL server

I det här avsnittet skapar du en SQL-server och databas.

Använd [az sql server create för](/cli/azure/sql/server#az_sql_server_create) att skapa en SQL-server:

* Ersätt **\<sql-server-name>** med ditt unika servernamn.
* Ersätt **\<your-password>** med ditt lösenord.
* I **CreateSQLEndpointTutorial-rg**.
* I **regionen eastus.**

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

Använd [az sql db create för](/cli/azure/sql/db#az_sql_db_create) att skapa en databas:

* Med **namnet myDataBase**.
* I **CreateSQLEndpointTutorial-rg**.
* Ersätt **\<sql-server-name>** med ditt unika servernamn.

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>Skapa privat slutpunkt

I det här avsnittet skapar du den privata slutpunkten.

Använd [az sql server list för](/cli/azure/sql/server#az_sql_server_list) att placera resurs-ID för SQL-servern i en gränssnittsvariabel.

Använd [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) för att skapa slutpunkten och anslutningen:

* Med **namnet myPrivateEndpoint**.
* I resursgruppen **CreateSQLEndpointTutorial-rg**.
* I det virtuella **nätverket myVNet**.
* I undernätet **myBackendSubnet**.
* Anslutning med namnet **myConnection**.

```azurecli-interactive
id=$(az sql server list \
    --resource-group CreateSQLEndpointTutorial-rg \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-ids sqlServer \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Konfigurera den privata DNS-zonen

I det här avsnittet skapar och konfigurerar du den privata DNS-zonen med [az network private-dns zone create](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Du använder az [network private-dns link vnet create för](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) att skapa den virtuella nätverkslänken till DNS-zonen.

Du skapar en dns-zongrupp med [az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zon med **namnet privatelink.database.windows.net**
* I det virtuella nätverket **myVNet**.
* I resursgruppen **CreateSQLEndpointTutorial-rg**.
* DNS-länk med **namnet myDNSLink**.
* Associerat **med myPrivateEndpoint**.
* Zongrupp med namnet **MyZoneGroup**.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name "privatelink.database.windows.net"

az network private-dns link vnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --zone-name "privatelink.database.windows.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreateSQLEndpointTutorial-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="test-connectivity-to-private-endpoint"></a>Testa anslutningen till den privata slutpunkten

I det här avsnittet använder du den virtuella dator som du skapade i föregående steg för att ansluta till SQL-servern via den privata slutpunkten.

1. Logga in på [Azure-portalen](https://portal.azure.com) 
 
2. Välj **Resursgrupper** i det vänstra navigeringsfönstret.

3. Välj **SkapaSQLEndpointTutorial-rg**.

4. Välj **myVM.**

5. På översiktssidan för **myVM** väljer du **Anslut** och sedan **Bastion**.

6. Välj den blå **knappen Använd Bastion.**

7. Ange det användarnamn och lösenord som du angav när den virtuella datorn skapades.

8. Öppna Windows PowerShell på servern när du har anslutt.

9. Ange `nslookup <sqlserver-name>.database.windows.net`. Ersätt **\<sqlserver-name>** med namnet på DEN SQL-server som du skapade i föregående steg.  Du får ett meddelande som liknar det som visas nedan:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    En privat IP-adress **på 10.0.0.5** returneras för SQL-servernamnet.  Den här adressen finns i undernätet för det virtuella nätverk som du skapade tidigare.


10. Installera [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) på **myVM**.

11. Öppna **SQL Server Management Studio**.

12. I **Anslut till server** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Servertyp | Välj **Databasmotor**.|
    | Servernamn | Ange **\<sql-server-name> .database.windows.net** |
    | Autentisering | Välj **SQL Server-autentisering**. |
    | Användarnamn | Ange det användarnamn som du angav när servern skapades |
    | Lösenord | Ange det lösenord som du angav när servern skapades |
    | Kom ihåg lösenordet | Välj **Ja**. |

13. Välj **Anslut**.

14. Bläddra i databaser på den vänstra menyn.

15. (Valfritt) Skapa eller fråga efter information **från mysqldatabase**.

16. Stäng skyddsanslutningen till **myVM**. 

## <a name="clean-up-resources"></a>Rensa resurser 

När du är klar med den privata slutpunkten, SQL Server och den virtuella datorn tar du bort resursgruppen och alla resurser som den innehåller: 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en:

* Virtuellt nätverk och skyddsvärd.
* Virtuell dator.
* Azure SQL server med privat slutpunkt.

Du använde den virtuella datorn för att testa anslutningen på ett säkert sätt till SQL-servern över den privata slutpunkten.

Som ett nästa steg kanske du  också är intresserad av webbappen med privat anslutning till Azure SQL-databasarkitekturscenariot, som ansluter en webbapp utanför det virtuella nätverket till den privata slutpunkten för en databas.
> [!div class="nextstepaction"]
> [Webbapp med privat anslutning till Azure SQL databas](/azure/architecture/example-scenario/private-web-app/private-web-app)