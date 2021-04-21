---
title: Snabbstart – Skapa en privat Azure-slutpunkt med Azure CLI
description: Använd den här snabbstarten om du vill lära dig hur du skapar en privat slutpunkt med Hjälp av Azure CLI.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 5088b4e50899a2643488103ba29a7e36a7f256ea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778363"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Snabbstart: Skapa en privat slutpunkt med Azure CLI

Kom igång med Azure Private Link med hjälp av en privat slutpunkt för att ansluta säkert till en Azure-webbapp.

I den här snabbstarten skapar du en privat slutpunkt för en Azure-webbapp och distribuerar en virtuell dator för att testa den privata anslutningen.  

Privata slutpunkter kan skapas för olika typer av Azure-tjänster, till exempel Azure SQL och Azure Storage.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* En Azure-webbapp med en **App Service-plan på PremiumV2-nivå** eller högre som distribuerats i din Azure-prenumeration.  
    * Mer information och ett exempel finns i [Snabbstart: Skapa en ASP.NET Core-webbapp i Azure](../app-service/quickstart-dotnetcore.md). 
    * En detaljerad självstudie om hur du skapar en webbapp och en slutpunkt finns [i Självstudie: Ansluta till en webbapp med en privat Azure-slutpunkt.](tutorial-private-endpoint-webapp-portal.md)
* Logga in på Azure Portal och kontrollera att din prenumeration är aktiv genom att köra `az login` .
* Kontrollera din version av Azure CLI i en terminal eller ett kommandofönster genom att köra `az --version` . Den senaste versionen finns i den senaste [versionsanteckningarna.](/cli/azure/release-notes-azure-cli?tabs=azure-cli)
  * Om du inte har den senaste versionen uppdaterar du installationen genom att följa [installationsguiden för ditt operativsystem eller din plattform.](/cli/azure/install-azure-cli)

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create):

* Med **namnet CreatePrivateEndpointQS-rg**. 
* På **platsen eastus.**

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
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
* I **resursgruppen CreatePrivateEndpointQS-rg.**
* Platsen **eastus**.

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
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
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Använd [az network public-ip create för](/cli/azure/network/public-ip#az_network_public_ip_create) att skapa en offentlig IP-adress för skyddsvärden:

* Skapa en redundant offentlig IP-adress i standardzonen med namnet **myBastionIP.**
* I **CreatePrivateEndpointQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

Använd [az network vnet subnet create för](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) att skapa ett skyddsundernät:

* Med **namnet AzureBastionSubnet**.
* Adressprefixet **10.0.1.0/24**.
* I det virtuella nätverket **myVNet**.
* I resursgruppen **CreatePrivateEndpointQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Använd [az network bastion create för](/cli/azure/network/bastion#az_network_bastion_create) att skapa en skyddsvärd:

* Med **namnet myBastionHost.**
* I **CreatePrivateEndpointQS-rg**.
* Associerat med offentlig IP **myBastionIP**.
* Associerat med det virtuella nätverket **myVNet**.
* På **platsen eastus.**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
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
* I **CreatePrivateEndpointQS-rg**.
* I nätverket **myVNet**.
* I undernätet **myBackendSubnet**.
* Serveravbildning **Win2019Datacenter**.

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

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-private-endpoint"></a>Skapa privat slutpunkt

I det här avsnittet skapar du den privata slutpunkten.

Använd [az webapp list för](/cli/azure/webapp#az_webapp_list) att placera resurs-ID:t för webbappen som du skapade tidigare i en gränssnittsvariabel.

Använd [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) för att skapa slutpunkten och anslutningen:

* Med **namnet myPrivateEndpoint**.
* I resursgruppen **CreatePrivateEndpointQS-rg**.
* I det virtuella nätverket **myVNet**.
* I undernätet **myBackendSubnet**.
* Anslutning med namnet **myConnection**.
* Din webbapp **\<webapp-resource-group-name>** .

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

I det här avsnittet skapar och konfigurerar du den privata DNS-zonen med [az network private-dns zone create](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Du använder az [network private-dns link vnet create för](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) att skapa den virtuella nätverkslänken till DNS-zonen.

Du skapar en dns-zongrupp med [az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zon med **namnet privatelink.azurewebsites.net**
* I det virtuella nätverket **myVNet**.
* I resursgruppen **CreatePrivateEndpointQS-rg**.
* DNS-länk med **namnet myDNSLink**.
* Associerat **med myPrivateEndpoint**.
* Zongrupp med namnet **MyZoneGroup**.

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

## <a name="test-connectivity-to-private-endpoint"></a>Testa anslutningen till den privata slutpunkten

I det här avsnittet använder du den virtuella dator som du skapade i föregående steg för att ansluta till SQL-servern via den privata slutpunkten.

1. Logga in på [Azure-portalen](https://portal.azure.com) 
 
2. Välj **Resursgrupper** i det vänstra navigeringsfönstret.

3. Välj **CreatePrivateEndpointQS-rg**.

4. Välj **myVM.**

5. På översiktssidan för **myVM** väljer du **Anslut** och sedan **Bastion**.

6. Välj den blå **knappen Använd Bastion.**

7. Ange det användarnamn och lösenord som du angav när den virtuella datorn skapades.

8. Öppna Windows PowerShell på servern när du har anslutt.

9. Ange `nslookup <your-webapp-name>.azurewebsites.net`. Ersätt **\<your-webapp-name>** med namnet på webbappen som du skapade i föregående steg.  Du får ett meddelande som liknar det som visas nedan:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    En privat IP-adress **på 10.0.0.5** returneras för webbappens namn.  Den här adressen finns i undernätet för det virtuella nätverk som du skapade tidigare.

10. I skyddsanslutningen till **myVM** öppnar du Internet Explorer.

11. Ange webbadressen till webbappen, **https:// \<your-webapp-name> .azurewebsites.net**.

12. Du får standardwebbappsidan om programmet inte har distribuerats:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Standardsidan för webbappen." border="true":::

13. Stäng anslutningen till **myVM**.

## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slutpunkten och den virtuella datorn använder du [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen och alla resurser den har:

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en:

* Virtuellt nätverk och skyddsvärd.
* Virtuell dator.
* Privat slutpunkt för en Azure-webbapp.

Du använde den virtuella datorn för att testa anslutningen på ett säkert sätt till webbappen i den privata slutpunkten.

Mer information om de tjänster som stöder en privat slutpunkt finns i:
> [!div class="nextstepaction"]
> [Private Link tillgänglighet](private-link-overview.md#availability)
