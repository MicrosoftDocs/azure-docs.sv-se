---
title: Själv studie kurs om att skapa, skapa & distribuera smarta avtal – Azure blockchain service
description: Självstudie om hur du använder Azure blockchain Development Kit för Ethereum-tillägget i Visual Studio Code för att skapa, bygga och distribuera ett smart kontrakt på Azure blockchain-tjänsten.
ms.date: 11/30/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: f7605a0c118a40e52210582d2411569795fb25ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96763697"
---
# <a name="tutorial-create-build-and-deploy-smart-contracts-on-azure-blockchain-service"></a>Självstudie: skapa, skapa och distribuera smarta avtal i Azure blockchain-tjänsten

I den här självstudien använder du tillägget Azure blockchain Development Kit for Ethereum i Visual Studio Code för att skapa, bygga och distribuera ett smart kontrakt på Azure blockchain-tjänsten. Du kan också använda utvecklings paketet för att köra en smart kontrakts funktion via en transaktion.

Du använder Azure blockchain Development Kit för att Ethereum:

> [!div class="checklist"]
> * Skapa ett smart kontrakt
> * Distribuera ett smart kontrakt
> * Köra en smart kontrakts funktion via en transaktion

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Slutför [snabb start: Använd Visual Studio Code för att ansluta till ett Azure blockchain service Consortium-nätverk](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure blockchain Development Kit för Ethereum-tillägg](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15. x eller högre](https://nodejs.org/download)
* [Git 2.10. x eller högre](https://git-scm.com)
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI-6.0.0](https://github.com/trufflesuite/ganache-cli)

I Windows krävs en installerad C++-kompilator för Node-Gyp-modulen. Du kan använda MSBuild-verktygen:

* Om Visual Studio 2017 är installerat konfigurerar du NPM att använda MSBuild-verktygen med kommandot `npm config set msvs_version 2017 -g`
* Om Visual Studio 2019 är installerat ställer du in sökvägen för MS build-verktyg för NPM. Till exempel `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Annars installerar du fristående VS build-verktyg med `npm install --global windows-build-tools` i ett förhöjdt *Kör som administratörs* kommando gränssnitt.

Mer information om Node-Gyp finns i [nod-Gyp-lagringsplatsen på GitHub](https://github.com/nodejs/node-gyp).

## <a name="create-a-smart-contract"></a>Skapa ett smart kontrakt

Azure blockchain Development Kit för Ethereum använder Project-mallar och Truffle-verktyg för att hjälpa Autogenerera, bygga och distribuera kontrakt. Innan du börjar måste du slutföra den nödvändiga [snabb starten: Använd Visual Studio Code för att ansluta till ett Azure blockchain service Consortium-nätverk](connect-vscode.md). Snabb starten guidar dig genom installationen och konfigurationen av Azure blockchain Development Kit för Ethereum.

1. Från kommando paletten VS Code väljer du **blockchain: nytt fast projekt**.
1. Välj **skapa Basic-projekt**.
1. Skapa en ny mapp med namnet `HelloBlockchain` och **Välj ny projekt Sök väg**.

Azure blockchain Development Kit skapar och initierar ett nytt solide-projekt åt dig. Det grundläggande projektet innehåller ett exempel på ett **HelloBlockchain** Smart-kontrakt och alla nödvändiga filer för att bygga och distribuera till din Consortium-medlem i Azure blockchain-tjänsten. Det kan ta flera minuter innan projektet skapas. Du kan övervaka förloppet i VS Codes Terminal-panel genom att välja utdata för Azure-blockchain.

Projekt strukturen ser ut som i följande exempel:

   ![Massiv projekt](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Bygg ett smart kontrakt

Smarta kontrakt finns i projektets **kontrakts** katalog. Du kompilerar smarta kontrakt innan du distribuerar dem till en blockchain. Använd kommandot **Bygg kontrakt** för att kompilera alla smarta kontrakt i projektet.

1. I sido rutan i VS Code-Utforskaren expanderar du mappen **kontrakt** i projektet.
1. Högerklicka på **HelloBlockchain. sol** och välj **Bygg kontrakt** på menyn.

    ![Välj menyn Bygg kontrakt ](./media/send-transaction/build-contracts.png)

Azure blockchain Development Kit använder Truffle för att kompilera smarta kontrakt.

![Utdata för Truffle-kompilator](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Distribuera ett smart kontrakt

Truffle använder migrations skript för att distribuera dina kontrakt till ett Ethereum-nätverk. Migreringar är JavaScript-filer som finns i projektets **migrerings** katalog.

1. Du distribuerar ditt smarta kontrakt genom att högerklicka på **HelloBlockchain. sol** och välja **distribuera kontrakt** på menyn.
1. Välj ditt Azure blockchain Consortium-nätverk i kommando paletten. Konsortiet blockchain-nätverk har lagts till i projektets Truffle-konfigurationsfil när du skapade projektet.
1. Välj **skapa** åtgärds kort. Välj ett fil namn och spara filen i projektmappen i projektmappen. Till exempel `myblockchainmember.env`. Den här filen används för att generera en privat Ethereum-nyckel för din blockchain-medlem.

Azure blockchain Development Kit använder Truffle för att köra migrations skriptet för att distribuera kontrakten till blockchain.

![Kontraktet har distribuerats](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Anropa en kontrakts funktion

**HelloBlockchain** -kontraktets **SendRequest** -funktion ändrar variabeln **RequestMessage** State. Att ändra tillstånd för ett blockchain nätverk görs via en transaktion. Du kan använda interaktions sidan för smart kontrakt i Azure blockchain Development Kit för att anropa funktionen **SendRequest** via en transaktion.

1. Om du vill interagera med ditt smarta kontrakt högerklickar du på **HelloBlockchain. sol** och väljer **Visa interaktions sidan för smart kontrakt** på menyn.

    ![Välj Visa interaktions sidan för smart kontrakt från menyn](./media/send-transaction/contract-interaction.png)

1. På sidan interaktion kan du välja en distribuerad kontrakt version, anropa funktioner, Visa aktuella tillstånd och visa metadata.

    ![Exempel på interaktions sida för smart kontrakt](./media/send-transaction/interaction-page.png)

1. Om du vill anropa funktionen Smart kontrakt väljer du kontrakt åtgärden och skickar argumenten. Välj **SendRequest** kontrakt åtgärd och ange **Hej, blockchain!** för parametern **requestMessage** . Välj **Kör** för att anropa funktionen **SendRequest** via en transaktion.

    ![Kör SendRequest-åtgärd](./media/send-transaction/sendrequest-action.png)

När transaktionen har bearbetats visar interaktions avsnittet status ändringarna.

![Kontrakt status ändringar](./media/send-transaction/contract-state.png)

Funktionen SendRequest anger fälten **RequestMessage** och **State** . Det aktuella läget för **RequestMessage** är argumentet du skickade **Hej, blockchain**. Värdet för fältet **State** förblir **Request**.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resurserna genom att ta bort `myResourceGroup` resurs gruppen som du skapade i snabb starten för att *skapa en blockchain-medlems* krav.

Så här tar du bort resursgruppen:

1. I Azure Portal navigerar du till **resurs grupp** i det vänstra navigerings fönstret och väljer den resurs grupp som du vill ta bort.
1. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resurs gruppens namn och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat ett exempel på ett heltäckande projekt med Azure blockchain Development Kit. Du har skapat och distribuerat ett smart kontrakt, sedan kallat en funktion via en transaktion i ett blockchain Consortium-nätverk som finns i Azure blockchain-tjänsten.

> [!div class="nextstepaction"]
> [Utveckla blockchain-program med Azure blockchain service](develop.md)
