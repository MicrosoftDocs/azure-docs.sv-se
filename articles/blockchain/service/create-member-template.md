---
title: Skapa en Azure blockchain service-medlem med hjälp av Azure Resource Manager mall
description: Lär dig hur du skapar en Azure blockchain service-medlem med hjälp av Azure Resource Manager mall.
services: azure-resource-manager
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, references_regions
ms.date: 09/16/2020
ms.openlocfilehash: e9893336f2e6633519853aceecc945ee6bf0bf4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91292769"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-arm-template"></a>Snabb start: skapa en Azure blockchain service-medlem med en ARM-mall

I den här snabb starten distribuerar du en ny blockchain-medlem och konsortiet i Azure blockchain-tjänsten med hjälp av en Azure Resource Manager-mall (ARM-mall). En Azure blockchain-tjänst medlem är en blockchain-nod i ett privat konsortium blockchain-nätverk. När du konfigurerar en medlem kan du skapa eller ansluta till ett konsortiums nätverk. Du behöver minst en medlem för ett konsortiums nätverk. Antalet blockchain-medlemmar som krävs av deltagarna beror på ditt scenario. Konsortiet deltagare kan ha en eller flera blockchain-medlemmar eller de kan dela medlemmar med andra deltagare. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/201-blockchain-asaservice/).

:::code language="json" source="~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json":::

Azure-resurser som definierats i mallen:

* [**Microsoft. blockchain/blockchainMembers**](/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande länk för att logga in i Azure och öppna en mall.

    [![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Ange inställningarna för Azure blockchain service-medlemmen.

    Inställning | Beskrivning
    --------|------------
    Prenumeration | Välj den Azure-prenumeration som du vill använda för din tjänst. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp | Skapa ett nytt resurs grupp namn eller Välj en befintlig från din prenumeration.
    Region | Välj en region för att skapa resurs gruppen. Alla medlemmar i konsortiet måste finnas på samma plats. Tillgängliga platser för distributionen är *westeurope, öster, Sydostasien, westeurope, europanorra, westus2* och *japanöstra*. Funktioner kanske inte är tillgängliga i vissa regioner. Azure blockchain Data Manager finns i följande Azure-regioner: USA, östra och Västeuropa.
    BC-medlems namn | Välj ett unikt namn för Azure blockchain service-medlemmen. Blockchain-medlems namnet får bara innehålla gemena bokstäver och siffror. Det första tecknet måste vara en bokstav. Värdet måste vara mellan 2 och 20 tecken långt.
    Namn på konsortium | Ange ett unikt namn. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md).
    Medlems lösen ord | Lösen ordet för medlemmens standard transaktions nod. Använd lösen ordet för grundläggande autentisering vid anslutning till blockchain-medlemmens offentliga standard transaktions nod.
    Konto lösen ord för konsortiets hantering | Konto lösen ordet för konsortiet används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Den används för hantering av konsortier.
    SKU-nivå | Pris nivån för den nya tjänsten. Välj mellan **standard** -och **Basic** -nivåer. Använd *Basic* -nivån för utveckling, testning och bevis på koncept. Använd *standard* nivån för distributioner av produktions nivåer. Använd även *standard* -nivån om du använder blockchain Data Manager eller skickar en stor mängd privata transaktioner. Det finns inte stöd för att ändra pris nivån mellan Basic och standard när medlems skapande har skapats.
    SKU-namn | Nodens konfiguration och kostnad för den nya tjänsten. Använd **B0** för Basic och **S0** för standard.
    Location | Välj en plats där du vill skapa medlemmen. Som standard används resurs gruppens plats `[resourceGroup().location]` . Alla medlemmar i konsortiet måste finnas på samma plats. Tillgängliga platser för distributionen är *westeurope, öster, Sydostasien, westeurope, europanorra, westus2* och *japanöstra*. Funktioner kanske inte är tillgängliga i vissa regioner. Azure blockchain Data Manager finns i följande Azure-regioner: USA, östra och Västeuropa.

1. Välj **Granska + skapa** för att verifiera och distribuera mallen.

  Azure Portal används här för att distribuera mallen. Du kan också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan använda Azure Portal för att visa information om den distribuerade Azure blockchain-tjänstemedlemen. I portalen går du till resurs gruppen som innehåller din Azure blockchain service-medlem. Välj den blockchain-medlem som du skapade.

![Distribuerad information om Azure blockchain-medlems översikt i Azure Portal](./media/create-member-template/deployed-member.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda den blockchain-medlem som du skapade för nästa snabb start eller självstudier. När de inte längre behövs kan du ta bort resurserna genom att ta bort resurs gruppen som du skapade för snabb starten.

Så här tar du bort resursgruppen:

1. I Azure Portal navigerar du till **resurs grupp** i det vänstra navigerings fönstret och väljer den resurs grupp som du vill ta bort.
2. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resurs gruppens namn och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en Azure blockchain service-medlem och ett nytt konsortium. Prova nästa snabb start för att använda Azure blockchain Development Kit för Ethereum för att ansluta till en Azure blockchain-tjänstemedlem.

> [!div class="nextstepaction"]
> [Använd Visual Studio Code för att ansluta till Azure blockchain-tjänsten](connect-vscode.md)