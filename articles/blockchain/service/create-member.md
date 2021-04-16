---
title: Skapa en Azure Blockchain Service medlem – Azure Portal
description: Skapa en Azure Blockchain Service medlem för ett blockkedjekonsortium med hjälp av Azure Portal.
ms.reviewer: ravastra
ms.date: 07/16/2020
ms.topic: quickstart
ms.custom:
- references_regions
- mode-portal
ms.openlocfilehash: 9fe8b7856a99930c16b4173e432fa0b30642582c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536199"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Snabbstart: Skapa en Azure Blockchain Service-medlem med hjälp av Azure-portalen

I den här snabbstarten distribuerar du en ny blockkedjemedlem och ett konsortium i Azure Blockchain Service med hjälp av Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Inga.

## <a name="create-a-blockchain-member"></a>Skapa en blockkedjemedlem

En Azure Blockchain Service är en blockkedjenod i ett privat konsortiums blockkedjenätverk. När du etablerar en medlem kan du skapa eller ansluta till ett konsortiumnätverk. Du behöver minst en medlem för ett konsortiumnätverk. Antalet blockkedjemedlemmar som deltagarna behöver beror på ditt scenario. Konsortiumdeltagarna kan ha en eller flera blockkedjemedlemmar eller dela medlemmar med andra deltagare. Mer information om consortia finns i [Azure Blockchain Service consortium](consortium.md).

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. Välj **Blockchain**  >  **Azure Blockchain Service (förhandsversion)**.

    ![Skapa tjänst](./media/create-member/create-member.png)

    Inställning | Beskrivning
    --------|------------
    Prenumeration | Välj den Azure-prenumeration som du vill använda för din tjänst. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp | Skapa ett nytt resursgruppsnamn eller välj ett befintligt i din prenumeration.
    Region | Välj en region för att skapa medlemmen. Alla medlemmar i konsortiumet måste vara på samma plats. Funktioner kanske inte är tillgängliga i vissa regioner. Azure Blockchain Data Manager finns i följande Azure-regioner: USA, östra och Europa, västra.
    Protokoll | För närvarande Azure Blockchain Service Preview kvorumprotokollet.
    Konsortium | Ange ett unikt namn för ett nytt konsortium. Om du går med i ett konsortium via en inbjudan väljer du det konsortium som du ansluter till. Mer information om consortia finns i [Azure Blockchain Service consortium](consortium.md).
    Name | Välj ett unikt namn för Azure Blockchain Service medlem. Namnet på blockkedjemedlemmen får bara innehålla gemener och siffror. Det första tecknet måste vara en bokstav. Värdet måste vara mellan 2 och 20 tecken långt.
    Lösenord för medlemskonto | Lösenordet för medlemskontot används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Du använder medlemskontot och medlemskontolösenordet för konsortiumhantering.
    Priser | Nodkonfigurationen och kostnaden för den nya tjänsten. Välj länken **Ändra** för att välja mellan **Standard-** **och Basic-nivåer.** Använd *Basic-nivån* för utveckling, testning och konceptbevis. Använd *standardnivån* för distributioner i produktionsklass. Använd även *standardnivån* om du använder Blockchain Data Manager eller skickar en stor mängd privata transaktioner. Det finns inte stöd för att ändra prisnivån mellan Basic och Standard när medlemmar har skapats.
    Nodlösenord | Lösenordet för medlemmens standardtransaktionsnod. Använd lösenordet för grundläggande autentisering vid anslutning till blockkedjemedlemmens offentliga slutpunkt för standardtransaktionsnod.

1. Välj **Granska + skapa för** att verifiera dina inställningar. Välj **Skapa** för att etablera tjänsten. Etableringen tar cirka 10 minuter.
1. Välj **Meddelanden** i verktygsfältet för att övervaka distributionsprocessen.
1. Efter distributionen går du till blockkedjemedlemmen.

Välj **Översikt.** Du kan visa grundläggande information om din tjänst, inklusive RootContract-adressen och medlemskontot.

![Översikt över blockkedjemedlem](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda medlemmen som du skapade för nästa snabbstart eller självstudie. När de inte längre behövs kan du ta bort resurserna genom att ta bort `myResourceGroup` resursgruppen som du skapade för snabbstarten.

Så här tar du bort resursgruppen:

1. I fönstret Azure Portal du till **Resursgrupp i** det vänstra navigeringsfönstret och väljer den resursgrupp som du vill ta bort.
2. Välj **Ta bort resursgrupp**. Verifiera borttagningen genom att ange resursgruppens namn och välj Ta **bort.**

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en Azure Blockchain Service medlem och ett nytt konsortium. Prova nästa snabbstart för att använda Azure Blockchain Development Kit för Ethereum för att ansluta till en Azure Blockchain Service medlem.

> [!div class="nextstepaction"]
> [Använda Visual Studio Code för att ansluta till Azure Blockchain Service](connect-vscode.md)
