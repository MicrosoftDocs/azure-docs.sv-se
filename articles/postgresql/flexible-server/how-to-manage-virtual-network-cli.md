---
title: Hantera virtuella nätverk – Azure CLI – Azure Database for PostgreSQL-flexibel Server
description: Skapa och hantera virtuella nätverk för Azure Database for PostgreSQL flexibel server med hjälp av Azure CLI
author: rothja
ms.author: jroth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: b957e0f0752ceda42c28e606faf26896c2c96012
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551092"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Skapa och hantera virtuella nätverk för Azure Database for PostgreSQL flexibel server med hjälp av Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Azure Database for PostgreSQL-flexibel Server stöder två typer av ömsesidigt exklusiva nätverks anslutnings metoder för att ansluta till din flexibla Server. De två alternativen är:

* Offentlig åtkomst (tillåtna IP-adresser)
* Privat åtkomst (VNet-integrering)

I den här artikeln fokuserar vi på att skapa en PostgreSQL-server med **privat åtkomst (VNet-integrering)** med Azure CLI. Med *privat åtkomst (VNet-integrering)* kan du distribuera din flexibla server till din egen [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md). Virtuella Azure-nätverk tillhandahåller privat och säker nätverkskommunikation. I privat åtkomst är anslutningarna till PostgreSQL-servern begränsade till endast inom det virtuella nätverket. Mer information om det finns i [privat åtkomst (VNet-integrering)](./concepts-networking.md#private-access-vnet-integration).

I Azure Database for PostgreSQL-flexibel Server kan du bara distribuera servern till ett virtuellt nätverk och undernät när du skapar servern. När den flexibla servern har distribuerats till ett virtuellt nätverk och undernät kan du inte flytta den till ett annat virtuellt nätverk, undernät eller till *offentlig åtkomst (tillåtna IP-adresser)*.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också öppna Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Välj **Kopiera** för att kopiera kod blocken, klistra in den i Cloud Shell och välj **RETUR** för att köra den.

Om du föredrar att installera och använda CLI lokalt kräver den här snabb starten Azure CLI version 2,0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Förutsättningar

Du måste logga in på ditt konto med kommandot [AZ login](/cli/azure/reference-index#az-login) . Observera egenskapen **ID** som refererar till **prenumerations-ID** för ditt Azure-konto.

```azurecli-interactive
az login
```

Välj den aktuella prenumerationen under ditt konto med kommandot [AZ Account set](/cli/azure/account#az-account-set) . Anteckna **ID-** värdet från **AZ inloggnings** -utdata som ska användas som värde för argumentet **prenumeration** i kommandot. Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Använd [AZ Account List](/cli/azure/account#az-account-list)för att hämta alla prenumerationer.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-postgresql---flexible-server-using-cli"></a>Skapa Azure Database for PostgreSQL flexibel server med CLI
Du kan använda `az postgres flexible-server` kommandot för att skapa en flexibel server med *privat åtkomst (VNet-integrering)*. Det här kommandot använder privat åtkomst (VNet-integrering) som standard metod för anslutning. Ett virtuellt nätverk och undernät kommer att skapas åt dig om inget anges. Du kan också ange det befintliga virtuella nätverket och under nätet med hjälp av undernät-ID. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Det finns olika alternativ för att skapa en flexibel server med CLI som visas i exemplen nedan.

>[!Important]
> Genom att använda det här kommandot delegeras under nätet till **Microsoft. DBforPostgreSQL/flexibleServers**. Den här delegeringen innebär att endast Azure Database for PostgreSQL flexibla servrar kan använda det under nätet. Inga andra Azure-resurstyper kan finnas i det delegerade undernätet.
>
Läs mer i referens dokumentationen för Azure CLI <!--FIXME --> en fullständig lista över konfigurerbara CLI-parametrar. I nedanstående kommandon kan du till exempel välja resurs gruppen.

- Skapa en flexibel server med standard nätverk för virtuella nätverk, undernät med standard adressprefix
    ```azurecli-interactive
    az postgres flexible-server create
    ```
- Skapa en flexibel server med redan befintligt virtuellt nätverk och undernät. Om det angivna virtuella nätverket och under nätet inte finns skapas det virtuella nätverket och under nätet med standard-adressprefix.
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --subnet mySubnet
    ```
- Skapa en flexibel server med redan befintligt virtuellt nätverk, undernät och med hjälp av undernät-ID: t. Det tillhandahållna under nätet ska inte ha någon annan resurs som har distribuerats i det och det här under nätet delegeras till **Microsoft. DBforPostgreSQL/flexibleServers**, om det inte redan har delegerats.
    ```azurecli-interactive
    az postgres flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > Det virtuella nätverket och under nätet måste finnas i samma region och prenumeration som din flexibla Server.

- Skapa en flexibel server med ett nytt virtuellt nätverk, undernät med adressprefix som inte är standard
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
I [referens dokumentationen](/cli/azure/postgres/flexible-server) för Azure CLI hittar du en fullständig lista över KONFIGURERBARa CLI-parametrar.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [nätverk i Azure Database for PostgreSQL-flexibel Server](./concepts-networking.md).
- [Skapa och hantera Azure Database for PostgreSQL-flexibla Server nätverk med Azure Portal](./how-to-manage-virtual-network-portal.md).
- Lär dig mer om det [virtuella nätverk som Azure Database for PostgreSQL – flexibelt Server](./concepts-networking.md#private-access-vnet-integration).