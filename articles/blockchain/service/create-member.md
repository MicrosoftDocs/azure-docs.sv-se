---
title: Skapa en Azure blockchain service-medlem – Azure Portal
description: Skapa en Azure blockchain service-medlem för ett blockchain-konsortium med hjälp av Azure Portal.
ms.date: 07/16/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: 8a7f5aaea56f34e8107664ab786a14b59cd1cb7d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91292736"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Snabb start: skapa en Azure blockchain-tjänst blockchain-medlem med hjälp av Azure Portal

I den här snabb starten distribuerar du en ny blockchain-medlem och konsortiet i Azure blockchain-tjänsten med hjälp av Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Inga.

## <a name="create-a-blockchain-member"></a>Skapa en blockchain-medlem

En Azure blockchain-tjänst medlem är en blockchain-nod i ett privat konsortium blockchain-nätverk. När du konfigurerar en medlem kan du skapa eller ansluta till ett konsortiums nätverk. Du behöver minst en medlem för ett konsortiums nätverk. Antalet blockchain-medlemmar som krävs av deltagarna beror på ditt scenario. Konsortiet deltagare kan ha en eller flera blockchain-medlemmar eller de kan dela medlemmar med andra deltagare. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md).

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. Välj **blockchain**  >  **Azure blockchain service (för hands version)**.

    ![Skapa tjänst](./media/create-member/create-member.png)

    Inställning | Beskrivning
    --------|------------
    Prenumeration | Välj den Azure-prenumeration som du vill använda för din tjänst. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp | Skapa ett nytt resurs grupp namn eller Välj en befintlig från din prenumeration.
    Region | Välj en region för att skapa medlemmen. Alla medlemmar i konsortiet måste finnas på samma plats. Funktioner kanske inte är tillgängliga i vissa regioner. Azure blockchain Data Manager finns i följande Azure-regioner: USA, östra och Västeuropa.
    Protokoll | För närvarande stöder för hands versionen av Azure blockchain service protokollet kvorum.
    Konsortium | Ange ett unikt namn för ett nytt konsortium. Om du ansluter till ett konsortium via en inbjudan väljer du det konsortium som du ansluter till. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md).
    Name | Välj ett unikt namn för Azure blockchain service-medlemmen. Blockchain-medlems namnet får bara innehålla gemena bokstäver och siffror. Det första tecknet måste vara en bokstav. Värdet måste vara mellan 2 och 20 tecken långt.
    Konto lösen ord för medlem | Medlems kontots lösen ord används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Du använder medlems kontot och medlems kontots lösen ord för hantering av konsortier.
    Prissättning | Nodens konfiguration och kostnad för den nya tjänsten. Välj länken **ändra** om du vill välja mellan nivåerna **standard** och **Basic** . Använd *Basic* -nivån för utveckling, testning och bevis på koncept. Använd *standard* nivån för distributioner av produktions nivåer. Använd även *standard* -nivån om du använder blockchain Data Manager eller skickar en stor mängd privata transaktioner. Det finns inte stöd för att ändra pris nivån mellan Basic och standard när medlems skapande har skapats.
    Node-lösenord | Lösen ordet för medlemmens standard transaktions nod. Använd lösen ordet för grundläggande autentisering vid anslutning till blockchain-medlemmens offentliga standard transaktions nod.

1. Välj **Granska + skapa** för att validera inställningarna. Välj **skapa** för att etablera tjänsten. Etableringen tar cirka 10 minuter.
1. Välj **meddelanden** i verktygsfältet för att övervaka distributions processen.
1. Efter distributionen navigerar du till din blockchain-medlem.

Välj **Översikt**. du kan visa grundläggande information om tjänsten inklusive RootContract-adress och medlems konto.

![Översikt över blockchain-medlem](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda den medlem som du skapade för nästa snabb start eller självstudier. När de inte längre behövs kan du ta bort resurserna genom att ta bort `myResourceGroup` resurs gruppen som du skapade för snabb starten.

Så här tar du bort resursgruppen:

1. I Azure Portal navigerar du till **resurs grupp** i det vänstra navigerings fönstret och väljer den resurs grupp som du vill ta bort.
2. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resurs gruppens namn och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en Azure blockchain service-medlem och ett nytt konsortium. Prova nästa snabb start för att använda Azure blockchain Development Kit för Ethereum för att ansluta till en Azure blockchain-tjänstemedlem.

> [!div class="nextstepaction"]
> [Använd Visual Studio Code för att ansluta till Azure blockchain-tjänsten](connect-vscode.md)
