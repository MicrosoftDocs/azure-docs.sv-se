---
title: Snabb start – skapa en privat Azure-slutpunkt med Azure CLI
description: Använd den här snabb starten för att lära dig hur du skapar en privat slut punkt med hjälp av Azure CLI.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: f74a143859f0a6629c88f0dcb61a97697f49d0be
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889237"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Snabb start: skapa en privat slut punkt med Azure CLI

Kom igång med en privat Azure-länk genom att använda en privat slut punkt för att ansluta säkert till en Azure-webbapp.

I den här snabb starten skapar du en privat slut punkt för en Azure-webbapp och distribuerar en virtuell dator för att testa den privata anslutningen.  

Privata slut punkter kan skapas för olika typer av Azure-tjänster, till exempel Azure SQL och Azure Storage.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure-webbapp med en **PremiumV2-** eller högre App Service-plan distribuerad i din Azure-prenumeration.  
    * Mer information och ett exempel finns i [snabb start: skapa en ASP.net Core webbapp i Azure](../app-service/quickstart-dotnetcore.md). 
    * En detaljerad själv studie kurs om hur du skapar en webbapp och en slut punkt finns i [Självstudier: Anslut till en webbapp med en privat Azure-slutpunkt](tutorial-private-endpoint-webapp-portal.md).
* Logga in på Azure Portal och kontrol lera att din prenumeration är aktiv genom att köra `az login` .
* Kontrol lera din version av Azure CLI i ett terminalfönster eller kommando fönster genom att köra `az --version` . Den senaste versionen finns i den senaste versions [informationen](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Om du inte har den senaste versionen uppdaterar du installationen genom att följa [installations guiden för ditt operativ system eller din plattform](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med [AZ Group Create](/cli/azure/group#az_group_create):

* Med namnet **CreatePrivateEndpointQS-RG**. 
* På den **östra** platsen.

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
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
* I resurs gruppen **CreatePrivateEndpointQS-RG** .
* Plats för **öster**.

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
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
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) för att skapa en offentlig IP-adress för skydds-värden:

* Skapa en standard zon för redundant offentlig IP-adress med namnet **myBastionIP**.
* I **CreatePrivateEndpointQS-RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

Använd [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) för att skapa ett skydds-undernät:

* Med namnet **AzureBastionSubnet**.
* Adressprefix för **10.0.1.0/24**.
* I virtuellt nätverk **myVNet**.
* I resurs gruppen **CreatePrivateEndpointQS-RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Använd [AZ Network skydds Create](/cli/azure/network/bastion#az-network-bastion-create) för att skapa en skydds-värd:

* Med namnet **myBastionHost**.
* I **CreatePrivateEndpointQS-RG**.
* Associerat med offentliga IP- **myBastionIP**.
* Associerad med **myVNet** för virtuellt nätverk.
* På **östra** platsen.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
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
* I **CreatePrivateEndpointQS-RG**.
* I nätverks- **myVNet**.
* I undernät **myBackendSubnet**.
* **Win2019Datacenter** för Server avbildning.

```azurecli-interactive
az vm create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-private-endpoint"></a>Skapa privat slut punkt

I det här avsnittet ska du skapa den privata slut punkten.

Använd [AZ webapp-listan](/cli/azure/webapp#az_webapp_list) för att placera resurs-ID för webbappen som du tidigare skapade i en gränssnitts variabel.

Använd [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) för att skapa slut punkten och anslutningen:

* Med namnet **myPrivateEndpoint**.
* I resurs gruppen **CreatePrivateEndpointQS-RG**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* Anslutning med **namnet för anslutning.**
* Din webapp **\<webapp-resource-group-name>** .

```azurecli-interactive
id=$(az webapp list \
    --resource-group <webapp-resource-group-name> \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-id sites \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Konfigurera den privata DNS-zonen

I det här avsnittet ska du skapa och konfigurera den privata DNS-zonen med [AZ Network Private-DNS Zone Create](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Du använder [AZ Network Private-DNS Link VNet Create](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) för att skapa den virtuella nätverks länken till DNS-zonen.

Du skapar en DNS-zon grupp med [AZ Network Private-Endpoint DNS-Zone-Group Create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zon med namnet **privatelink.azurewebsites.net**
* I virtuellt nätverk **myVNet**.
* I resurs gruppen **CreatePrivateEndpointQS-RG**.
* DNS-länk med namnet **myDNSLink**.
* Kopplad till **myPrivateEndpoint**.
* Zon grupp med namnet **MyZoneGroup**.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name "privatelink.azurewebsites.net"

az network private-dns link vnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --zone-name "privatelink.azurewebsites.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreatePrivateEndpointQS-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.azurewebsites.net" \
   --zone-name webapp
```

## <a name="test-connectivity-to-private-endpoint"></a>Testa anslutningen till privat slut punkt

I det här avsnittet ska du använda den virtuella datorn som du skapade i föregående steg för att ansluta till SQL Server över den privata slut punkten.

1. Logga in på [Azure-portalen](https://portal.azure.com) 
 
2. Välj **resurs grupper** i det vänstra navigerings fönstret.

3. Välj **CreatePrivateEndpointQS-RG**.

4. Välj **myVM**.

5. På sidan Översikt för **myVM** väljer du **Anslut** sedan **skydds**.

6. Välj knappen blå **användnings skydds** .

7. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

8. Öppna Windows PowerShell på servern när du har anslutit.

9. Ange `nslookup <your-webapp-name>.azurewebsites.net`. Ersätt **\<your-webapp-name>** med namnet på den webbapp som du skapade i föregående steg.  Du får ett meddelande som liknar det som visas nedan:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    En privat IP-adress för **10.0.0.5** returneras för namnet på webb programmet.  Adressen finns i under nätet för det virtuella nätverk som du skapade tidigare.

10. Öppna Internet Explorer i skydds-anslutningen till **myVM**.

11. Ange URL: en för din webbapp, **https:// \<your-webapp-name> . azurewebsites.net**.

12. Du får standard sidan webbapp om ditt program inte har distribuerats:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Standard webb program sida." border="true":::

13. Stäng anslutningen till **myVM**.

## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slut punkten och den virtuella datorn använder du [AZ Group Delete](/cli/azure/group#az_group_delete) för att ta bort resurs gruppen och alla resurser den har:

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en:

* Virtuellt nätverk och skydds-värd.
* Virtuell dator.
* Privat slut punkt för en Azure-webbapp.

Du använde den virtuella datorn för att testa anslutningen på ett säkert sätt till webbappen över den privata slut punkten.

Mer information om de tjänster som stöder en privat slut punkt finns i:
> [!div class="nextstepaction"]
> [Tillgänglighet för privat länk](private-link-overview.md#availability)
