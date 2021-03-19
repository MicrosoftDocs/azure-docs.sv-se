---
title: Ansluta MetaMask till ett Azure blockchain service-nätverk
description: Anslut till ett Azure blockchain service-nätverk med MetaMask och distribuera ett smart kontrakt.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 4a45e02a861ff20a4dc774668a4e008f9b42aeea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90530445"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Snabb start: använda MetaMask för att ansluta och distribuera ett smart kontrakt

I den här snabb starten ska du använda MetaMask för att ansluta till ett Azure blockchain service-nätverk och använda Remix för att distribuera ett smart kontrakt. Metamask är ett webb läsar tillägg som hanterar en eter-plån bok och utför smarta kontrakts åtgärder.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Slutför [snabb start: skapa en blockchain-medlem med hjälp av Azure Portal](create-member.md) eller [snabb start: skapa en Azure blockchain service blockchain-medlem med Azure CLI](create-member-cli.md)
* Installera [MetaMask Browser-tillägg](https://metamask.io)
* Generera en MetaMask [plån bok](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time)

## <a name="get-endpoint-address"></a>Hämta slut punkts adress

Du behöver Azure blockchain-tjänstens slut punkts adress för att ansluta till blockchain-nätverket. Slut punkts adressen och åtkomst nycklarna finns i Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till din Azure blockchain service-medlem.
1. Välj **Transactions-noder** och noden standard transaktions nod.

    ![Välj noden standard transaktion](./media/connect-metamask/transaction-nodes.png)

1. Välj **anslutnings strängar > åtkomst nycklar**.
1. Kopiera slut punkts adressen från **https (åtkomst nyckel 1)**.

    ![Anslutningssträng](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Anslut MetaMask

1. Öppna MetaMask browser extension och logga in.
1. I list rutan nätverk väljer du  **anpassad RPC**.

    ![Anpassat RPC](./media/connect-metamask/custom-rpc.png)

1. I **nytt nätverk > ny RPC-URL** klistrar du in slut punkts adressen som du kopierade ovan.
1. Välj **Spara**.

    Om anslutningen lyckades visas det privata nätverket i list rutan nätverk.

    ![Nytt nätverk](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Distribuera Smart kontrakt

Remix är en webbläsarbaserad utvecklings miljö. Med MetaMask och Remix tillsammans kan du distribuera och vidta åtgärder för smarta kontrakt.

1. Öppna webbläsaren och navigera till `https://remix.ethereum.org`.
1. Välj **ny fil** på fliken **Start** under **fil**.

    Ge den nya filen ett namn `simple.sol` .

    ![Skapa fil](./media/connect-metamask/create-file.png)

    Välj **OK**.
1. I Remix-redigeraren klistrar du in följande **enkla smarta kontrakt** kod.

    ```solidity
    pragma solidity ^0.5.0;
             
    contract simple {
        uint balance;
                 
        constructor() public{
            balance = 0;
        }
                 
        function add(uint _num) public {
            balance += _num;
        }
                 
        function get() public view returns (uint){
            return balance;
        }
    }
    ```

    Det **enkla kontraktet** deklarerar en tillstånds variabel med namnet **Saldo**. Två funktioner har definierats. Funktionen **Add** lägger till ett tal att **balansera**. Funktionen **Get** returnerar värdet för **saldot**.
1. För att kompilera kontraktet väljer du först fönstret halvledare compiler och väljer sedan  **compile Simple. sol**.

    ![Skärm dum par visar ett kontrakt som kompileras.](./media/connect-metamask/compile.png)

1. Välj rutan **distribuera & körning** och ange sedan att **miljön** ska **injicera Web3** för att ansluta via MetaMask till din blockchain-medlem.

    ![Fliken kör](./media/connect-metamask/injected-web3.png)

1. Välj det **enkla** kontraktet och **distribuera**.

    ![Skärm dum par visar distributions-och körnings transaktioner med ett kontrakt valt och distribuera valt.](./media/connect-metamask/deploy.png)


1. Ett MetaMask-meddelande varnar dig om otillräckliga medel för att utföra transaktionen.

    För ett offentligt blockchain nätverk skulle du behöva betala för transaktions kostnaden. Eftersom det här är ett privat nätverk i ett konsortium kan du ställa in gas priset på noll.

1.  Välj **gas avgift > redigera > Avancerat** och Ställ in **gas priset** på 0.

    ![Gas pris](./media/connect-metamask/gas-price.png)

    Välj **Spara**.

1. Välj **Bekräfta** om du vill distribuera det smarta kontraktet till blockchain.
1. I avsnittet **distribuerade kontrakt** expanderar du det **enkla** kontraktet.

    ![Distribuerat kontrakt](./media/connect-metamask/deployed-contract.png)

    Två åtgärder, **Lägg till** och **Hämta**, mappa till de funktioner som definierats i kontraktet.

1. Om du vill utföra en **Lägg till** transaktion på blockchain anger du ett nummer som du vill lägga till och väljer sedan **Lägg till**. Du kan få ett fel meddelande om gas uppskattning från Remix: "du skickar transaktionen till en privat blockchain som inte kräver gas". Välj **Skicka transaktion** för att framtvinga transaktionen.
1. På samma sätt som när du distribuerade kontraktet, varnar ett MetaMask-meddelande om att det inte finns tillräckligt med pengar för att genomföra transaktionen.

    Eftersom det här är ett privat nätverk i ett konsortium kan vi ställa in gas priset på noll.

1. Välj **gas avgift > redigera > Avancerat**, ange **gas priset** till 0 och välj **Spara**.
1. Välj **Bekräfta** för att skicka transaktionen till blockchain.
1. Välj **Hämta** åtgärd. Detta är ett anrop för att fråga Node-data. En transaktion behövs inte.

Fel söknings fönstret för Remix visar information om transaktionerna på blockchain:

![Fel söknings historik](./media/connect-metamask/debug.png)

Du kan se det **enkla** uppdraget att skapa, transaktioner för **enkel. Lägg till** och anropa **enkel. get**.

Om du vill se transaktions historiken i MetaMask öppnar du MetaMask Browser-tillägget och tittar i avsnittet **Historik** för en logg över det distribuerade kontraktet och transaktionerna.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du MetaMask Browser-tillägget för att ansluta till en Azure blockchain service Transaction-nod, distribuera ett smart kontrakt och skicka en transaktion till blockchain. Testa nästa självstudie för att använda Azure blockchain Development Kit för Ethereum och Truffle för att skapa, bygga, distribuera och köra en smart kontrakts funktion via en transaktion.

> [!div class="nextstepaction"]
> [Skapa, skapa och distribuera smarta avtal i Azure blockchain-tjänsten](send-transaction.md)