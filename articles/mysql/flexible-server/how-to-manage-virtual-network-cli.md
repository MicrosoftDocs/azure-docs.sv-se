---
title: Hantera virtuella nätverk – Azure CLI – Azure Database for MySQL – flexibel server
description: Skapa och hantera virtuella nätverk för Azure Database for MySQL – flexibel server med hjälp av Azure CLI
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7da8062f18d737af9d19df54863bc56c7268910c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776925"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Skapa och hantera virtuella nätverk för Azure Database for MySQL – flexibel server med hjälp av Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL flexibel server är för närvarande i offentlig förhandsversion

Azure Database for MySQL – flexibel server stöder två typer av ömsesidigt uteslutande nätverksanslutningsmetoder för att ansluta till din flexibla server. De två alternativen är:

- Offentlig åtkomst (tillåtna IP-adresser)
- Privat åtkomst (VNet-integrering)

I den här artikeln fokuserar vi på att skapa MySQL-server med privat åtkomst **(VNet-integrering) med** hjälp av Azure CLI. Med *privat åtkomst (VNet-integrering)* kan du distribuera din flexibla server till din egen [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md). Virtuella Azure-nätverk tillhandahåller privat och säker nätverkskommunikation. I Privat åtkomst är anslutningarna till MySQL-servern begränsade till endast inom ditt virtuella nätverk. Mer information om det finns i Privat [åtkomst (VNet-integrering).](./concepts-networking.md#private-access-vnet-integration)

I Azure Database for MySQL flexibel server kan du bara distribuera servern till ett virtuellt nätverk och undernät när servern skapas. När den flexibla servern har distribuerats till ett virtuellt nätverk och undernät kan du inte flytta den till ett annat virtuellt nätverk, undernät eller till offentlig åtkomst *(tillåtna IP-adresser).*

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Det [Azure Cloud Shell](../../cloud-shell/overview.md) är ett kostnadsfritt interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också öppna Cloud Shell en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Välj **Kopiera** för att kopiera kodblocken, klistra in det i Cloud Shell och välj Retur **för** att köra den.

Om du föredrar att installera och använda CLI lokalt kräver den här snabbstarten Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Förutsättningar

Du måste logga in på ditt konto med kommandot [az login.](/cli/azure/reference-index#az_login) Observera **ID-egenskapen,** som refererar till **prenumerations-ID för** ditt Azure-konto.

```azurecli-interactive
az login
```

Välj den specifika prenumerationen under ditt konto med [kommandot az account](/cli/azure/account#az_account_set) set. Anteckna **ID-värdet från az** login-utdata som ska användas som värde för **prenumerationsargumentet** i kommandot .  Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Om du vill hämta alla dina prenumerationer använder [du az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-mysql-flexible-server-using-cli"></a>Skapa Azure Database for MySQL flexibel server med CLI
Du kan använda kommandot `az mysql flexible-server` för att skapa den flexibla servern med privat åtkomst *(VNet-integrering).* Det här kommandot använder privat åtkomst (VNet-integrering) som standardanslutningsmetod. Ett virtuellt nätverk och undernät skapas åt dig om inget anges. Du kan också ange det befintliga virtuella nätverket och undernätet med hjälp av undernäts-ID. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Det finns olika alternativ för att skapa en flexibel server med HJÄLP av CLI, som du ser i exemplen nedan.

>[!Important]
> Med det här kommandot delegeras undernätet **till Microsoft.DBforMySQL/flexibleServers**. Delegeringen innebär att endast flexibla Azure Database for MySQL-servrar kan använda det här undernätet. Inga andra Azure-resurstyper kan finnas i det delegerade undernätet.
>

I referensdokumentationen [för](/cli/azure/mysql/flexible-server) Azure CLI finns en fullständig lista över konfigurerbara CLI-parametrar. I kommandona nedan kan du till exempel ange resursgruppen.

- Skapa en flexibel server med ett virtuellt standardnätverk, undernät med standardadressprefix
    ```azurecli-interactive
    az mysql flexible-server create
    ```
- Skapa en flexibel server med ett befintligt virtuellt nätverk och undernät. Om det angivna virtuella nätverket och undernätet inte finns skapas ett virtuellt nätverk och ett undernät med standardadressprefix.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --subnet mySubnet
    ```

- Skapa en flexibel server med hjälp av ett befintligt virtuellt nätverk, undernät och med hjälp av undernäts-ID:t. Det angivna undernätet bör inte ha någon annan resurs distribuerad, och det här undernätet delegeras till **Microsoft.DBforMySQL/flexibleServers** om det inte redan har delegerats.
    ```azurecli-interactive
    az mysql flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > Det virtuella nätverket och undernätet ska finnas i samma region och prenumeration som din flexibla server.
<
- Skapa en flexibel server med hjälp av ett nytt virtuellt nätverk, undernät med icke-standardadressprefix.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
I referensdokumentationen [för](/cli/azure/mysql/flexible-server) Azure CLI finns en fullständig lista över konfigurerbara CLI-parametrar.


## <a name="next-steps"></a>Nästa steg
- Läs mer om [nätverk i Azure Database for MySQL Flexibel server.](./concepts-networking.md)
- [Skapa och hantera Azure Database for MySQL virtuellt servernätverk med hjälp av Azure Portal](./how-to-manage-virtual-network-portal.md).
- Läs mer om [Azure Database for MySQL flexible server virtual network](./concepts-networking.md#private-access-vnet-integration).