---
title: Skapa en Azure Blockchain Service medlem med hjälp Azure Resource Manager mall
description: Lär dig hur du skapar Azure Blockchain Service medlem med hjälp Azure Resource Manager mall.
services: azure-resource-manager
ms.date: 09/16/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- references_regions
- mode-arm
ms.openlocfilehash: c49e45ae84d58d62460d493887e2a4e78e40ba32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536304"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-arm-template"></a>Snabbstart: Skapa en Azure Blockchain Service medlem med hjälp av en ARM-mall

I den här snabbstarten distribuerar du en ny blockkedjemedlem och ett konsortium i Azure Blockchain Service med hjälp av en Azure Resource Manager mall (ARM-mall). En Azure Blockchain Service är en blockkedjenod i ett privat konsortiums blockkedjenätverk. När du etablerar en medlem kan du skapa eller ansluta till ett konsortiumnätverk. Du behöver minst en medlem för ett konsortiumnätverk. Antalet blockkedjemedlemmar som deltagarna behöver beror på ditt scenario. Konsortiumdeltagare kan ha en eller flera blockkedjemedlemmar eller dela medlemmar med andra deltagare. Mer information om consortia finns i [Azure Blockchain Service consortium](consortium.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/201-blockchain-asaservice/).

:::code language="json" source="~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json":::

Azure-resurser som definieras i mallen:

* [**Microsoft.Blockchain/blockchainMembers**](/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande länk för att logga in i Azure och öppna en mall.

    [![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Ange inställningarna för Azure Blockchain Service medlem.

    Inställning | Beskrivning
    --------|------------
    Prenumeration | Välj den Azure-prenumeration som du vill använda för din tjänst. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp | Skapa ett nytt resursgruppsnamn eller välj ett befintligt från din prenumeration.
    Region | Välj en region för att skapa resursgruppen. Alla medlemmar i konsortiumet måste finnas på samma plats. Tillgängliga platser för distributionen är *westeurope, eastus, southeastasia, westeurope, northeurope, westus2* och *japaneast*. Funktioner kanske inte är tillgängliga i vissa regioner. Azure Blockchain Data Manager finns i följande Azure-regioner: USA, östra och Europa, västra.
    Bc-medlemsnamn | Välj ett unikt namn för Azure Blockchain Service medlem. Blockkedjemedlemsnamnet får bara innehålla gemener och siffror. Det första tecknet måste vara en bokstav. Värdet måste vara mellan 2 och 20 tecken långt.
    Namn på konsortium | Ange ett unikt namn. Mer information om consortia finns i [Azure Blockchain Service consortium](consortium.md).
    Medlemslösenord | Lösenordet för medlemmens standardtransaktionsnod. Använd lösenordet för grundläggande autentisering vid anslutning till blockkedjemedlemmens offentliga slutpunkt för standardtransaktionsnod.
    Kontolösenord för consortium-hantering | Lösenordet för konsortiumkontot används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Den används för konsortiumhantering.
    SKU-nivå | Prisnivån för den nya tjänsten. Välj mellan **nivåerna Standard** **och** Basic. Använd *Basic-nivån* för utveckling, testning och konceptbevis. Använd *standardnivån* för distributioner i produktionsklass. Använd även *standardnivån* om du använder Blockchain Data Manager eller skickar en stor mängd privata transaktioner. Det går inte att ändra prisnivån mellan Basic och Standard när medlemmen har skapats.
    SKU-namn | Nodkonfigurationen och kostnaden för den nya tjänsten. Använd **B0** för Basic och **S0** för Standard.
    Location | Välj en plats för att skapa medlemmen. Som standard används resursgruppens `[resourceGroup().location]` plats. Alla medlemmar i konsortiumet måste finnas på samma plats. Tillgängliga platser för distributionen är *westeurope, eastus, southeastasia, westeurope, northeurope, westus2* och *japaneast*. Funktioner kanske inte är tillgängliga i vissa regioner. Azure Blockchain Data Manager finns i följande Azure-regioner: USA, östra och Europa, västra.

1. Välj **Granska + skapa** för att verifiera och distribuera mallen.

  Den Azure Portal används här för att distribuera mallen. Du kan också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../../azure-resource-manager/templates/deploy-powershell.md)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan använda Azure Portal för att visa information om den distribuerade Azure Blockchain Service medlem. I portalen går du till den resursgrupp som innehåller Azure Blockchain Service medlem. Välj den blockkedjemedlem som du skapade.

![Distribuerad Azure Blockchain medlemsöversiktsinformation i Azure Portal](./media/create-member-template/deployed-member.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda blockkedjemedlemmen som du skapade för nästa snabbstart eller självstudie. När de inte längre behövs kan du ta bort resurserna genom att ta bort resursgruppen som du skapade för snabbstarten.

Så här tar du bort resursgruppen:

1. I fönstret Azure Portal du till **Resursgrupp i** det vänstra navigeringsfönstret och väljer den resursgrupp som du vill ta bort.
2. Välj **Ta bort resursgrupp**. Verifiera borttagningen genom att ange resursgruppens namn och välj Ta **bort.**

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en Azure Blockchain Service medlem och ett nytt konsortium. Prova nästa snabbstart för att använda Azure Blockchain Development Kit för Ethereum för att ansluta till en Azure Blockchain Service medlem.

> [!div class="nextstepaction"]
> [Använda Visual Studio Code för att ansluta till Azure Blockchain Service](connect-vscode.md)
