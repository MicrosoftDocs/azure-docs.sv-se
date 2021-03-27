---
title: Själv studie kurs om att skapa, skapa & distribuera smarta avtal – Azure blockchain service
description: Självstudie om hur du använder Azure blockchain Development Kit för Ethereum-tillägget i Visual Studio Code för att skapa, bygga och distribuera ett smart kontrakt på Azure blockchain-tjänsten.
ms.date: 11/30/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: 4c2df952480d2c30de10838c3d0f7714fc7e6126
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628653"
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
**HelloBlockchain** -kontraktets **SendRequest** -funktion ändrar variabeln **RequestMessage** State. Att ändra tillstånd för ett blockchain nätverk görs via en transaktion. Du kan skapa ett skript för att köra **SendRequest** -funktionen via en transaktion.

1. Skapa en ny fil i roten av Truffle-projektet och ge den namnet `sendrequest.js` . Lägg till följande Web3 JavaScript-kod i filen.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. När ett projekt skapas i Azure blockchain Development Kit genereras konfigurations filen för Truffle med information om ditt konsortiums blockchain Network Endpoint. Öppna **truffle-config.js** i projektet. Konfigurations filen visar två nätverk: en namngiven utveckling och en med samma namn som konsortiet.
1. I rutan för VS-kodens terminalfönster använder du Truffle för att köra skriptet i ditt konsortium blockchain-nätverk. I meny raden i terminalfönstret väljer du fliken **Terminal** och **PowerShell** i list rutan.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Ersätt \<blockchain network\> med namnet på blockchain-nätverket som definierats i **truffle-config.js**.

Truffle kör skriptet i ditt blockchain-nätverk.

![Utdata som visar transaktionen har skickats](./media/send-transaction/execute-transaction.png)

När du kör ett kontrakts funktion via en transaktion bearbetas inte transaktionen förrän ett block har skapats. Funktioner som är avsedda att köras via en transaktion returnerar ett transaktions-ID i stället för ett retur värde.

## <a name="query-contract-state"></a>Status för fråga kontrakt

Smarta kontrakt funktioner kan returnera det aktuella värdet för State-variabler. Nu ska vi lägga till en funktion för att returnera värdet för en tillstånds variabel.

1. I **HelloBlockchain. sol** lägger du till en **GetMessage** -funktion i det smarta **HelloBlockchain** -kontraktet.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    Funktionen returnerar meddelandet som lagras i en tillstånds variabel baserat på kontraktets aktuella status.

1. Högerklicka på **HelloBlockchain. sol** och välj **Bygg kontrakt** på menyn för att kompilera ändringarna i det smarta kontraktet.
1. Du distribuerar genom att högerklicka på **HelloBlockchain. sol** och välja **distribuera kontrakt** på menyn. När du uppmanas väljer du ditt Azure blockchain Consortium-nätverk i kommando paletten.
1. Skapa sedan ett skript som använder för att anropa funktionen **GetMessage** . Skapa en ny fil i roten av Truffle-projektet och ge den namnet `getmessage.js` . Lägg till följande Web3 JavaScript-kod i filen.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. I rutan för VS-kodens terminalfönster använder du Truffle för att köra skriptet i ditt blockchain-nätverk. I meny raden i terminalfönstret väljer du fliken **Terminal** och **PowerShell** i list rutan.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Ersätt \<blockchain network\> med namnet på blockchain-nätverket som definierats i **truffle-config.js**.

Skriptet frågar det smarta kontraktet genom att anropa funktionen getMessage. Det aktuella värdet för **RequestMessage** status-variabeln returneras.

![Utdata från GetMessage-frågan som visar det aktuella värdet för RequestMessage State-variabel](./media/send-transaction/execute-get.png)

Observera att värdet inte är **Hej, blockchain!**. I stället är det returnerade värdet en plats hållare. När du ändrar och distribuerar kontraktet distribueras det ändrade kontraktet till en ny adress och State-variablerna tilldelas värden i konstruktorn för smarta kontrakt. Truffle-exemplet för **2_deploy_contracts.js** migreringen distribuerar det smarta kontraktet och skickar ett värde för plats hållare som ett argument. Konstruktorn anger **RequestMessage** tillstånds variabeln till värdet för plats hållaren och det som returneras.

1. Om du vill ställa in **RequestMessage** tillstånds variabel och fråga efter värdet kör du **sendrequest.js** och **getmessage.js** skript igen.

    ![Utdata från SendRequest-och GetMessage-skript som visar RequestMessage har angetts](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** anger **RequestMessage** tillstånds variabeln till **Hej, blockchain!** och **getmessage.js** frågar kontraktet efter värde för **RequestMessage** State-variabeln och returnerar **Hej, blockchain!**.
## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resurserna genom att ta bort `myResourceGroup` resurs gruppen som du skapade i snabb starten för att *skapa en blockchain-medlems* krav.

Så här tar du bort resursgruppen:

1. I Azure Portal navigerar du till **resurs grupp** i det vänstra navigerings fönstret och väljer den resurs grupp som du vill ta bort.
1. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resurs gruppens namn och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat ett exempel på ett heltäckande projekt med Azure blockchain Development Kit. Du har skapat och distribuerat ett smart kontrakt, sedan kallat en funktion via en transaktion i ett blockchain Consortium-nätverk som finns i Azure blockchain-tjänsten.

> [!div class="nextstepaction"]
> [Utveckla blockchain-program med Azure blockchain service](develop.md)
