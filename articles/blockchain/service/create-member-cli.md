---
title: Skapa en Azure Blockchain Service medlem – Azure CLI
description: Skapa en Azure Blockchain Service medlem för ett blockkedjekonsortium med hjälp av Azure CLI.
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 2d94640e1c6a037893fb1ad9198b82195c46dd24
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768133"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Snabbstart: Skapa en Azure Blockchain Service blockkedjemedlem med Azure CLI

I den här snabbstarten distribuerar du en ny blockkedjemedlem och ett konsortium i Azure Blockchain Service hjälp av Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Inga.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda CLI lokalt kräver den här snabbstarten Azure CLI version 2.0.51 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du gå till [Installera Azure CLI.](/cli/azure/install-azure-cli)

## <a name="prepare-your-environment"></a>Förbered din miljö

1. Logga in.

    Logga in med kommandot [az login](/cli/azure/reference-index#az_login) om du använder en lokal CLI-installation.

    ```azurecli
    az login
    ```

    Slutför autentiseringsprocessen genom att följa anvisningarna i terminalen.

1. Installera Azure CLI-tillägget.

    När du arbetar med tilläggsreferenser för Azure CLI måste du först installera tillägget.  Med Azure CLI-tillägg får du tillgång till experimentella kommandon som inte finns med i standardversionen av CLI:t ännu.  Läs mer om tillägg, bland annat hur du uppdaterar och avinstallerar dem, i [Använda tillägg med Azure CLI](/cli/azure/azure-cli-extensions-overview).

    Installera tillägget [för Azure Blockchain Service](/cli/azure/ext/blockchain/blockchain) genom att köra följande kommando:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

1. Skapa en resursgrupp.

    Azure Blockchain Service måste precis som alla Azure-resurser distribueras till en resursgrupp. Resursgrupper gör det enkelt att organisera och hantera relaterade Azure-resurser.

    För den här snabbstarten skapar du en resursgrupp med namnet _myResourceGroup_ på _platsen eastus_ med följande [az group create-kommando:](/cli/azure/group#az_group_create)

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>Skapa en blockkedjemedlem

En Azure Blockchain Service medlem är en blockkedjenod i ett privat konsortiums blockkedjenätverk. När du etablerar en medlem kan du skapa eller ansluta till ett konsortiumnätverk. Du behöver minst en medlem för ett konsortiumnätverk. Antalet blockkedjemedlemmar som deltagarna behöver beror på ditt scenario. Konsortiumdeltagarna kan ha en eller flera blockkedjemedlemmar eller dela medlemmar med andra deltagare. Mer information om consortia finns i [Azure Blockchain Service consortium](consortium.md).

Det finns flera parametrar och egenskaper som du måste skicka. Ersätt exempelparametrarna med dina värden.

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
| **resursgrupp** | Resursgruppnamn där Azure Blockchain Service resurser skapas. Använd den resursgrupp som du skapade i föregående avsnitt.
| **Namn** | Ett unikt namn som identifierar din Azure Blockchain Service blockkedjemedlem. Namnet används för den offentliga slutpunktsadressen. Till exempel `myblockchainmember.blockchain.azure.com`.
| **Plats** | Azure-region där blockkedjemedlemmen skapas. Till exempel `westus2`. Välj den plats som är närmast dina användare eller dina andra Azure-program. Funktioner kanske inte är tillgängliga i vissa regioner. Azure Blockchain Data Manager finns i följande Azure-regioner: USA, östra och Europa, västra.
| **lösenord** | Lösenordet för medlemmens standardtransaktionsnod. Använd lösenordet för grundläggande autentisering vid anslutning till blockkedjemedlemmens offentliga slutpunkt för standardtransaktionsnod.
| **Protokollet** | Blockkedjeprotokoll. *Kvorumprotokoll* stöds för närvarande.
| **Konsortium** | Namnet på det konsortium som ska anslutas eller skapas. Mer information om consortia finns i [Azure Blockchain Service consortium](consortium.md).
| **consortium-management-account-password** | Lösenordet för ett konsortiumkonto kallas även för lösenordet för medlemskontot. Lösenordet för medlemskontot används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Du använder medlemskontot och medlemskontots lösenord för konsortiumhantering.
| **Sku** | Nivåtyp. *Standard* eller *Basic*. Använd *Basic-nivån* för utveckling, testning och konceptbevis. Använd *standardnivån* för distributioner i produktionsklass. Använd även *standardnivån* om du använder Blockchain Data Manager eller skickar en stor mängd privata transaktioner. Det går inte att ändra prisnivån mellan Basic och Standard när medlemmen har skapats.

Det tar cirka 10 minuter att skapa blockkedjemedlemmen och stödresurser.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda blockkedjemedlemmen som du skapade för nästa snabbstart eller självstudie. När de inte längre behövs kan du ta bort resurserna genom att ta bort `myResourceGroup` resursgruppen som du skapade för snabbstarten.

Kör följande kommando för att ta bort resursgruppen och alla relaterade resurser.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en Azure Blockchain Service medlem och ett nytt konsortium. Prova nästa snabbstart för att använda Azure Blockchain Development Kit för Ethereum för att ansluta till en Azure Blockchain Service medlem.

> [!div class="nextstepaction"]
> [Använda Visual Studio Code för att ansluta till Azure Blockchain Service](connect-vscode.md)
