---
title: Skapa en Azure blockchain service-medlem – Azure CLI
description: Skapa en Azure blockchain service-medlem för ett blockchain-konsortium med hjälp av Azure CLI.
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3442c3b6023edcde97aabcb13e91120ba6811027
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323081"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Snabb start: skapa en Azure blockchain-tjänst blockchain-medlem med Azure CLI

I den här snabb starten distribuerar du en ny blockchain-medlem och konsortiet i Azure blockchain-tjänsten med hjälp av Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Inga.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda CLI lokalt kräver den här snabb starten Azure CLI version 2.0.51 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Förbered din miljö

1. Logga in.

    Logga in med kommandot [az login](/cli/azure/reference-index#az-login) om du använder en lokal CLI-installation.

    ```azurecli
    az login
    ```

    Slutför autentiseringsprocessen genom att följa anvisningarna i terminalen.

1. Installera Azure CLI-tillägget.

    När du arbetar med tilläggsreferenser för Azure CLI måste du först installera tillägget.  Med Azure CLI-tillägg får du tillgång till experimentella kommandon som inte finns med i standardversionen av CLI:t ännu.  Läs mer om tillägg, bland annat hur du uppdaterar och avinstallerar dem, i [Använda tillägg med Azure CLI](/cli/azure/azure-cli-extensions-overview).

    Installera [tillägget för Azure blockchain-tjänsten](/cli/azure/ext/blockchain/blockchain) genom att köra följande kommando:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

1. Skapa en resursgrupp.

    Azure blockchain-tjänsten, till exempel alla Azure-resurser, måste distribueras till en resurs grupp. Resursgrupper gör det enkelt att organisera och hantera relaterade Azure-resurser.

    I den här snabb starten skapar du en resurs grupp med namnet _myResourceGroup_ på den _östra_ platsen med följande [AZ Group Create](/cli/azure/group#az-group-create) -kommando:

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>Skapa en blockchain-medlem

En Azure blockchain-tjänst medlem är en blockchain-nod i ett privat konsortium blockchain-nätverk. När du konfigurerar en medlem kan du skapa eller ansluta till ett konsortiums nätverk. Du behöver minst en medlem för ett konsortiums nätverk. Antalet blockchain-medlemmar som krävs av deltagarna beror på ditt scenario. Konsortiet deltagare kan ha en eller flera blockchain-medlemmar eller de kan dela medlemmar med andra deltagare. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md).

Det finns flera parametrar och egenskaper som du måste skicka. Ersätt exempel parametrarna med dina värden.

```azurecli-interactive
az blockchain member create \
                            --resource-group "MyResourceGroup" \
                            --name "myblockchainmember" \
                            --location "eastus" \
                            --password "strongMemberAccountPassword@1" \
                            --protocol "Quorum" \
                            --consortium "myconsortium" \
                            --consortium-management-account-password "strongConsortiumManagementPassword@1" \
                            --sku "Basic"
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser skapas. Använd den resurs grupp som du skapade i föregående avsnitt.
| **Namn** | Ett unikt namn som identifierar din Azure blockchain service blockchain-medlem. Namnet används för den offentliga slut punktens adress. Till exempel `myblockchainmember.blockchain.azure.com`.
| **sökvägen** | Azure-region där blockchain-medlemmen skapas. Till exempel `westus2`. Välj den plats som är närmast dina användare eller dina andra Azure-program. Funktioner kanske inte är tillgängliga i vissa regioner. Azure blockchain Data Manager finns i följande Azure-regioner: USA, östra och Västeuropa.
| **lösenord** | Lösen ordet för medlemmens standard transaktions nod. Använd lösen ordet för grundläggande autentisering vid anslutning till blockchain-medlemmens offentliga standard transaktions nod.
| **protokollhanterare** | Blockchain-protokoll. För närvarande stöds *kvorum* protokoll.
| **Consortium** | Namnet på konsortiet att ansluta till eller skapa. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md).
| **konsortiet-Management-Account-Password** | Konto lösen ordet för konsortiet kallas även medlems kontots lösen ord. Medlems kontots lösen ord används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Du använder medlems kontot och medlems kontots lösen ord för hantering av konsortier.
| **SKU** | Nivå typ. *Standard* eller *Basic*. Använd *Basic* -nivån för utveckling, testning och bevis på koncept. Använd *standard* nivån för distributioner av produktions nivåer. Använd även *standard* -nivån om du använder blockchain Data Manager eller skickar en stor mängd privata transaktioner. Det finns inte stöd för att ändra pris nivån mellan Basic och standard när medlems skapande har skapats.

Det tar cirka 10 minuter att skapa blockchain-medlemmen och de resurser som stöds.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda den blockchain-medlem som du skapade för nästa snabb start eller självstudier. När de inte längre behövs kan du ta bort resurserna genom att ta bort `myResourceGroup` resurs gruppen som du skapade för snabb starten.

Kör följande kommando för att ta bort resurs gruppen och alla relaterade resurser.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en Azure blockchain service-medlem och ett nytt konsortium. Prova nästa snabb start för att använda Azure blockchain Development Kit för Ethereum för att ansluta till en Azure blockchain-tjänstemedlem.

> [!div class="nextstepaction"]
> [Använd Visual Studio Code för att ansluta till Azure blockchain-tjänsten](connect-vscode.md)
