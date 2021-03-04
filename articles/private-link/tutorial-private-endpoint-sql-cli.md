---
title: Självstudie – ansluta till en Azure SQL-Server med en privat Azure-slutpunkt – Azure CLI
description: Använd den här självstudien för att lära dig att skapa en Azure SQL-Server med en privat slut punkt med hjälp av Azure CLI
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: ab530817e912495049ad3fe8b36f9a5fad47eb2c
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096589"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>Självstudie – ansluta till en Azure SQL-Server med en privat Azure-slutpunkt – Azure CLI

Den privata Azure-slutpunkten är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, till exempel virtuella datorer, att kommunicera med privata länk resurser privat.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och en skydds-värd.
> * Skapa en virtuell dator.
> * Skapa en Azure SQL-Server och en privat slut punkt.
> * Testa anslutningen till den privata SQL Server-slutpunkten.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Logga in på Azure Portal och kontrol lera att din prenumeration är aktiv genom att köra `az login` .
* Kontrol lera din version av Azure CLI i ett terminalfönster eller kommando fönster genom att köra `az --version` . Den senaste versionen finns i den senaste versions [informationen](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Om du inte har den senaste versionen uppdaterar du installationen genom att följa [installations guiden för ditt operativ system eller din plattform](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med [AZ Group Create](/cli/azure/group#az_group_create):

* Med namnet **CreateSQLEndpointTutorial-RG**. 
* På den **östra** platsen.

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Skapa ett virtuellt nätverk och en skydds-värd

I det här avsnittet ska du skapa ett virtuellt nätverk, ett undernät och en skydds-värd. 

Skydds-värden kommer att användas för att ansluta säkert till den virtuella datorn för att testa den privata slut punkten.

Skapa ett virtuellt nätverk med [AZ Network VNet Create](/cli/azure/network/vnet#az_network_vnet_create)

* Med namnet **myVNet**.
* Adressprefix för **10.0.0.0/16**.
* Undernät med namnet **myBackendSubnet**.
* Undernätsprefixet för **10.0.0.0/24**.
* I resurs gruppen **CreateSQLEndpointTutorial-RG** .
* Plats för **öster**.

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Uppdatera under nätet för att inaktivera nätverks principer för privata slut punkter för den privata slut punkten med [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) för att skapa en offentlig IP-adress för skydds-värden:

* Skapa en standard zon för redundant offentlig IP-adress med namnet **myBastionIP**.
* I **CreateSQLEndpointTutorial-RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

Använd [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) för att skapa ett skydds-undernät:

* Med namnet **AzureBastionSubnet**.
* Adressprefix för **10.0.1.0/24**.
* I virtuellt nätverk **myVNet**.
* I resurs gruppen **CreateSQLEndpointTutorial-RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Använd [AZ Network skydds Create](/cli/azure/network/bastion#az-network-bastion-create) för att skapa en skydds-värd:

* Med namnet **myBastionHost**.
* I **CreateSQLEndpointTutorial-RG**.
* Associerat med offentliga IP- **myBastionIP**.
* Associerad med **myVNet** för virtuellt nätverk.
* På **östra** platsen.

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Det kan ta några minuter innan Azure skydds-värden kan distribueras.

## <a name="create-test-virtual-machine"></a>Skapa virtuell test dator

I det här avsnittet ska du skapa en virtuell dator som ska användas för att testa den privata slut punkten.

Skapa en virtuell dator med [AZ VM Create](/cli/azure/vm#az_vm_create). När du uppmanas till det anger du ett lösen ord som ska användas som autentiseringsuppgifter för den virtuella datorn:

* Med namnet **myVM**.
* I **CreateSQLEndpointTutorial-RG**.
* I nätverks- **myVNet**.
* I undernät **myBackendSubnet**.
* **Win2019Datacenter** för Server avbildning.

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

## <a name="create-an-azure-sql-server"></a>Skapa en Azure SQL-Server

I det här avsnittet ska du skapa en SQL-Server och-databas.

Använd [AZ SQL Server Create](/cli/azure/sql/server#az_sql_server_create) för att skapa en SQL-Server:

* Ersätt **\<sql-server-name>** med ditt unika Server namn.
* Ersätt **\<your-password>** med ditt lösen ord.
* I **CreateSQLEndpointTutorial-RG**.
* I **östra** regionen.

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

Använd [AZ SQL DB Create](/cli/azure/sql/db#az_sql_db_create) för att skapa en databas:

* Namngiven **databas**.
* I **CreateSQLEndpointTutorial-RG**.
* Ersätt **\<sql-server-name>** med ditt unika Server namn.

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>Skapa privat slut punkt

I det här avsnittet ska du skapa den privata slut punkten.

Använd [AZ SQL Server-lista](/cli/azure/sql/server#az_sql_server_list) för att placera resurs-ID: t för SQL-servern i en gränssnitts variabel.

Använd [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) för att skapa slut punkten och anslutningen:

* Med namnet **myPrivateEndpoint**.
* I resurs gruppen **CreateSQLEndpointTutorial-RG**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* Anslutning med **namnet för anslutning.**

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

I det här avsnittet ska du skapa och konfigurera den privata DNS-zonen med [AZ Network Private-DNS Zone Create](/cli/azure/ext/privatedns/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Du använder [AZ Network Private-DNS Link VNet Create](/cli/azure/ext/privatedns/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) för att skapa den virtuella nätverks länken till DNS-zonen.

Du skapar en DNS-zon grupp med [AZ Network Private-Endpoint DNS-Zone-Group Create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zon med namnet **privatelink.Database.Windows.net**
* I virtuellt nätverk **myVNet**.
* I resurs gruppen **CreateSQLEndpointTutorial-RG**.
* DNS-länk med namnet **myDNSLink**.
* Kopplad till **myPrivateEndpoint**.
* Zon grupp med namnet **MyZoneGroup**.

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

## <a name="test-connectivity-to-private-endpoint"></a>Testa anslutningen till privat slut punkt

I det här avsnittet ska du använda den virtuella datorn som du skapade i föregående steg för att ansluta till SQL Server över den privata slut punkten.

1. Logga in på [Azure-portalen](https://portal.azure.com) 
 
2. Välj **resurs grupper** i det vänstra navigerings fönstret.

3. Välj **CreateSQLEndpointTutorial-RG**.

4. Välj **myVM**.

5. På sidan Översikt för **myVM** väljer du **Anslut** sedan **skydds**.

6. Välj knappen blå **användnings skydds** .

7. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

8. Öppna Windows PowerShell på servern när du har anslutit.

9. Ange `nslookup <sqlserver-name>.database.windows.net`. Ersätt **\<sqlserver-name>** med namnet på den SQL Server som du skapade i föregående steg.  Du får ett meddelande som liknar det som visas nedan:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    En privat IP-adress för **10.0.0.5** returneras för SQL Server-namnet.  Adressen finns i under nätet för det virtuella nätverk som du skapade tidigare.


10. Installera [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) på **myVM**.

11. Öppna **SQL Server Management Studio**.

12. I **Anslut till Server** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Servertyp | Välj **Databasmotor**.|
    | Servernamn | Ange **\<sql-server-name> . Database.Windows.net** |
    | Autentisering | Välj **SQL Server-autentisering**. |
    | Användarnamn | Ange det användar namn som du angav när servern skapades |
    | Lösenord | Ange lösen ordet som du angav när servern skapades |
    | Kom ihåg lösenordet | Välj **Ja**. |

13. Välj **Anslut**.

14. Bläddra bland databaser på den vänstra menyn.

15. Du kan också Skapa eller fråga efter information från **mysqldatabase**.

16. Stäng skydds-anslutningen till **myVM**. 

## <a name="clean-up-resources"></a>Rensa resurser 

När du är klar med den privata slut punkten, SQL Server och den virtuella datorn tar du bort resurs gruppen och alla resurser den innehåller: 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en:

* Virtuellt nätverk och skydds-värd.
* Virtuell dator.
* Azure SQL Server med privat slut punkt.

Du använde den virtuella datorn för att testa anslutningen på ett säkert sätt till SQL Server över den privata slut punkten.

Som nästa steg kanske du också är intresse rad av arkitektur scenariot för [webbappen med privat anslutning till Azure SQL Database](https://docs.microsoft.com/azure/architecture/example-scenario/private-web-app/private-web-app) , som ansluter ett webb program utanför det virtuella nätverket till en Databass privata slut punkt.

Lär dig hur du skapar en privat länk-tjänst:
> [!div class="nextstepaction"]
> [Skapa en Private Link-tjänst](create-private-link-service-portal.md)