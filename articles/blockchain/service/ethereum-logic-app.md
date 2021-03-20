---
title: Använda Ethereum blockchain Connector med Azure Logic Apps-Azure blockchain-tjänsten
description: Använd Ethereum blockchain-anslutningen med Azure Logic Apps för att utlösa smarta kontrakts funktioner och reagera på smarta kontrakt händelser.
ms.date: 08/31/2020
ms.topic: how-to
ms.reviewer: caleteet
ms.openlocfilehash: 411337908553e58c252a0ed1a42d17f76195c720
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96763799"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Använd Ethereum blockchain-anslutningen med Azure Logic Apps

Använd [Ethereum blockchain-anslutningen](/connectors/blockchainethereum/) med [Azure Logic Apps](../../logic-apps/index.yml) för att utföra åtgärder för smarta kontrakt och reagera på smarta kontrakt händelser. Den här artikeln förklarar hur du kan använda Ethereum blockchain-anslutningen för att skicka blockchain information till en annan tjänst eller anropa en blockchain-funktion. Anta till exempel att du vill skapa en REST-baserad mikrotjänst som returnerar information från en blockchain-redovisning. Genom att använda en Logic app kan du godkänna HTTP-begäranden som efterfrågar information som lagras i en blockchain-redovisning.

## <a name="prerequisites"></a>Förutsättningar

- Slutför den valfria snabb starten [: Använd Visual Studio Code för att ansluta till ett Azure blockchain service Consortium-nätverk](connect-vscode.md). Snabb starten guidar dig när du installerar [Azure blockchain Development Kit för Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) och ställer in din blockchain Development-miljö.
- Om du är nybörjare på Azure Logic Apps bör du granska Microsoft Learn modulerna [Introduktion till Azure Logic Apps](/learn/modules/intro-to-logic-apps/) och [anropa ett API från ett Logic Apps arbets flöde med hjälp av en anpassad anslutning](/learn/modules/logic-apps-and-custom-connectors/).

## <a name="create-a-logic-app"></a>Skapa en logikapp

Azure Logic Apps hjälper dig att schemalägga och automatisera affärs processer och arbets flöden när du behöver integrera system och tjänster. Först skapar du en logik som använder Ethereum blockchain-anslutningen.

1. På [Azure-portalen](https://portal.azure.com) väljer du **Skapa en resurs** > **Integrering** > **Logikapp**.
1. Under **skapa logisk app**, anger du information om var du vill skapa din Logic app. När du är klar väljer du **skapa**.

    Mer information om hur du skapar Logic Apps finns i [skapa automatiserade arbets flöden med Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. När Azure har distribuerat din app väljer du din Logic app-resurs.
1. I Logic Apps designer väljer du **Tom Logic app** under **mallar**.

Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren körs skapar Logic Apps-motorn en logikappinstans som startar och kör ditt arbetsflöde.

Ethereum blockchain-anslutningen har en utlösare och flera åtgärder. Vilken utlösare eller åtgärd du använder beror på ditt scenario. Följ avsnittet i den här artikeln som bäst matchar ditt scenario.

Om arbets flödet:

* Utlöses när en händelse inträffar i blockchain [med hjälp av händelse utlösaren](#use-the-event-trigger).
* Frågar eller distribuerar ett smart kontrakt, [Använd åtgärder](#use-actions).
* Följ ett vanligt scenario och [generera ett arbets flöde med Developer Kit](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Använda händelse utlösaren

Använd händelse utlösare för Ethereum blockchain när du vill att en Logic app ska köras efter att ett smart kontrakts händelse inträffar. Till exempel vill du skicka ett e-postmeddelande när ett smart kontrakts funktion anropas.

1. I Logic Apps designer väljer du Ethereum blockchain-anslutningen.
1. Välj **när ett smart kontrakt händelse inträffar** på fliken **utlösare** .
1. Ändra eller [skapa en API-anslutning](#create-an-api-connection) till Azure blockchain-tjänsten.
1. Ange information om det smarta kontrakt som du vill söka efter händelser i.

    ![Logic Apps designer med egenskaper för händelse utlösare](./media/ethereum-logic-app/event-properties.png)

    | Egenskap | Beskrivning |
    |----------|-------------|
    | **Kontrakt ABI** | ABI (kontrakt programmets binära gränssnitt) definierar de smarta kontrakts gränssnitten. Mer information finns i [Hämta kontrakts ABI](#get-the-contract-abi). |
    | **Smart kontrakt adress** | Kontrakt adressen är mål adressen för det smarta kontraktet på Ethereum-blockchain. Mer information finns i [Hämta kontrakt adressen](#get-the-contract-address). |
    | **Händelse namn** | Välj ett smart kontrakt händelse som ska kontrol leras. Händelsen utlöser Logic-appen. |
    | **Intervall** och **frekvens** | Välj hur ofta du vill söka efter händelsen. |

1. Välj **Spara**.

Om du vill slutföra din Logic-app kan du lägga till ett nytt steg som utför en åtgärd baserat på händelse utlösaren Ethereum blockchain. Skicka till exempel ett e-postmeddelande.

## <a name="use-actions"></a>Använda åtgärder

Använd Ethereum blockchain-åtgärder när du vill att en Logic app ska utföra en åtgärd på blockchain-redovisningen. Du vill till exempel skapa en REST-baserad mikrotjänst som anropar ett smart kontrakts funktion när en HTTP-begäran görs till en Logic-app.

Kopplings åtgärder kräver en utlösare. Du kan använda en Ethereum blockchain-anslutnings åtgärd som nästa steg efter en utlösare, till exempel en utlösare för HTTP-begäran för en mikrotjänst.

1. I Logic Apps designer väljer du **nytt steg** efter en utlösare.
1. Välj Ethereum blockchain-anslutningen.
1. Välj en av de tillgängliga åtgärderna på fliken **åtgärder** .

    ![Logic Apps designer med åtgärder egenskaper](./media/ethereum-logic-app/action-properties.png)

1. Ändra eller [skapa en API-anslutning](#create-an-api-connection) till Azure blockchain-tjänsten.
1. Ange följande information om funktionen Smart kontrakt beroende på vilken åtgärd du har valt.

    | Egenskap | Beskrivning |
    |----------|-------------|
    | **Kontrakt ABI** | ABI för kontrakt definierar de smarta kontrakts gränssnitten. Mer information finns i [Hämta kontrakts ABI](#get-the-contract-abi). |
    | **Kontraktets bytekod** | Det kompilerade bytet för smart kontrakt. Mer information finns i [Hämta kontraktets bytekod](#get-the-contract-bytecode). |
    | **Smart kontrakt adress** | Kontrakt adressen är mål adressen för det smarta kontraktet på Ethereum-blockchain. Mer information finns i [Hämta kontrakt adressen](#get-the-contract-address). |
    | **Namn på Smart kontrakts funktion** | Välj funktions namnet Smart kontrakt för åtgärden. Listan fylls i från informationen i kontrakts ABI. |

    När du har valt ett smart kontrakts funktions namn kan du se obligatoriska fält för funktions parametrar. Ange värdena eller det dynamiska innehåll som krävs för ditt scenario.

Du kan nu använda din Logic app. När Logic app-händelsen utlöses körs åtgärden Ethereum blockchain. En utlösare för HTTP-begäran kör till exempel en Ethereum blockchain-åtgärd för att fråga ett smart kontrakts tillstånds värde. Den här frågan resulterar i ett HTTP-svar som returnerar värdet.

## <a name="generate-a-workflow"></a>Skapa ett arbets flöde

Azure blockchain Development Kit för Ethereum Visual Studio Code Extension kan generera Logic app-arbetsflöden för vanliga scenarier. Det finns fyra scenarier:

* Data publicering till en Azure SQL Database-instans
* Händelse publicering till en instans av Azure Event Grid eller Azure Service Bus
* Rapport publicering
* REST-baserad mikrotjänst

 Azure blockchain Development Kit använder Truffle för att förenkla blockchain-utvecklingen. Om du vill generera en Logi Kap par baserat på ett smart kontrakt behöver du en Truffle-lösning för det smarta kontraktet. Du behöver också en anslutning till ditt Azure blockchain service Consortium-nätverk. Mer information finns i [använda Visual Studio Code för att ansluta till en Azure blockchain service Consortium-snabb start](connect-vscode.md).

Följande steg genererar till exempel en REST-baserad Logic-app som baseras på det smarta **HelloBlockchain** snabb starts avtal:

1. I Visual Studio Code Explorer-sidofält expanderar du mappen **kontrakt** i din lösning.
1. Högerklicka på **HelloBlockchain. sol** och välj **skapa mikrotjänster för smarta kontrakt** på menyn.

    ![Visual Studio Code-fönstret med alternativet generera mikrotjänster för smarta kontrakt](./media/ethereum-logic-app/generate-logic-app.png)

1. I paletten kommando väljer du **Logic app**.
1. Ange **kontrakt adressen**. Mer information finns i [Hämta kontrakt adressen](#get-the-contract-address).
1. Välj Azure-prenumeration och resurs grupp för Logic app.

    Logic app-konfigurationen och-kodfragment skapas i **generatedLogicApp** -katalogen.

1. Visa katalogen **generatedLogicApp/HelloBlockchain** . Det finns en Logic app JSON-fil för varje smart kontrakts funktion, händelse och egenskap.
1. Öppna filen **generatedLogicApp/HelloBlockchain/service/property.RequestMessage.logicapp.jspå** filen och kopiera innehållet.

    ![JSON-fil med kod att kopiera](./media/ethereum-logic-app/requestmessage.png)

1. I din Logic app väljer du **vyn logiskt program kod**. Ersätt den befintliga JSON-filen med den genererade Logic app-JSON.

    ![Kodvyn för Logic app med ny ersatt app-kod](./media/ethereum-logic-app/code-view.png)

1. Välj **Designer** för att växla till designvyn-vyn.
1. Logic-appen innehåller de grundläggande stegen för scenariot. Du måste dock uppdatera konfigurations informationen för Ethereum blockchain-anslutningen.
1. Välj steget **anslutningar** och ändra eller [skapa en API-anslutning](#create-an-api-connection) till Azure blockchain-tjänsten.

    ![Design läge med val av anslutningar](./media/ethereum-logic-app/microservice-logic-app.png)

1. Du kan nu använda din Logic app. För att testa den REST-baserade mikrotjänsten skickar du en HTTP POST-begäran till URL: en för logi Kap App-begäran. Kopiera **http post-URL-** innehållet från steget **när en http-begäran tas emot** .

    ![Logic Apps Designer-fönstret med HTTP POST-URL: en](./media/ethereum-logic-app/post-url.png)

1. Använd sväng för att skapa en HTTP POST-begäran. Ersätt platshållartexten *\<HTTP POST URL\>* med URL: en från föregående steg.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    Kommandot spiral returnerar ett svar från Logic-appen. I det här fallet är svaret utdata från funktionen **RequestMessage** Smart kontrakt.

    ![Kod utdata från funktionen RequestMessage Smart kontrakt](./media/ethereum-logic-app/curl.png)

Mer information om hur du använder Development Kit finns på [wiki-sidan för Azure blockchain Development Kit för Ethereum](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>Skapa en API-anslutning

En API-anslutning till en blockchain krävs för Ethereum blockchain-anslutningen. Du kan använda API-anslutningen för flera Logic Apps. Vissa egenskaper krävs och andra är beroende av ditt scenario.

> [!IMPORTANT]
> En privat nyckel eller konto adress och ett lösen ord krävs för att skapa transaktioner i en blockchain. Endast en form av autentisering krävs. Du behöver inte ange både privat nyckel och konto information. Frågande kontrakt kräver ingen transaktion. Om du använder åtgärder som efterfrågar kontrakt status krävs inte den privata nyckeln eller konto adressen och lösen ordet.

För att hjälpa dig att skapa en anslutning till en Azure blockchain-tjänst medlem, har följande lista möjliga egenskaper som du kan behöva beroende på ditt scenario.

| Egenskap | Beskrivning |
|----------|-------------|
|**Anslutningsnamn** | API-anslutningens namn. Krävs. |
|**Ethereum RPC-slutpunkt** | HTTP-adressen för Azure blockchain service Transaction-noden. Krävs. Mer information finns i [Hämta RPC-slutpunkten](#get-the-rpc-endpoint). |
|**Privat nyckel** | Ethereum-kontots privata nyckel. Privat nyckel eller konto adress och lösen ord krävs för transaktioner. Mer information finns i [Hämta den privata nyckeln](#get-the-private-key). |
|**Konto adress** | Medlems konto adress för Azure blockchain-tjänst. Privat nyckel eller konto adress och lösen ord krävs för transaktioner. Mer information finns i [Hämta konto adressen](#get-the-account-address). |
|**Konto lösen ord** | Konto lösen ordet anges när du skapar medlemmen. Information om hur du återställer lösen ordet finns i [Ethereum-konto](consortium.md#ethereum-account).|

## <a name="get-the-rpc-endpoint"></a>Hämta RPC-slutpunkten

Azure blockchain-tjänstens RPC-slutpunkt krävs för att ansluta till ett blockchain-nätverk. Du kan hämta slut punkts adressen genom att använda Azure blockchain Development Kit för Ethereum eller Azure Portal.

**Använda utvecklings paketet:**

1. Under **Azure blockchain service** i Visual Studio Code högerklickar du på konsortiet.
1. Välj **Kopiera RPC-slutpunkt**.

    ![Fönstret Visual Studio Code som visar konsortiet med adress valet kopiera RPC-slutpunkt](./media/ethereum-logic-app/devkit-rpc.png)

    RPC-slutpunkten kopieras till Urklipp.

**Så här använder du Azure Portal:**

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till din Azure blockchain service-medlem. Välj **Transactions-noder** och noden standard transaktions nod.

    ![Sidan Transaction Nodes med valet (standard nod)](./media/ethereum-logic-app/transaction-nodes.png)

1. Välj **anslutnings strängar**  >  **åtkomst nycklar**.
1. Kopiera slut punkts adressen från **https (åtkomst nyckel 1)** eller **https (åtkomst nyckel 2)**.

    ![Azure Portal med åtkomst nycklar för anslutnings strängen](./media/ethereum-logic-app/connection-string.png)

    RPC-slutpunkten är HTTPS-URL: en, som innehåller adressen och åtkomst nyckeln för din Azure blockchain service member Transaction-nod.

## <a name="get-the-private-key"></a>Hämta den privata nyckeln

Du kan använda Ethereum-kontots privata nyckel för att autentisera när du skickar en transaktion till blockchain. Ditt Ethereum-kontos offentliga och privata nycklar genereras från ett 12-ord. Azure blockchain Development Kit för Ethereum genererar en genom gång när du ansluter till en Azure blockchain service Consortium-medlem. Du kan hämta slut punkts adressen genom att använda tillägget Development Kit.

1. Öppna Command-paletten (F1) i Visual Studio Code.
1. Välj **blockchain: Hämta privat nyckel**.
1. Välj den välliggande du sparade när du anslöt till konsortiet medlem.

    ![Med ett alternativ för att välja kommando rads verktyget](./media/ethereum-logic-app/private-key.png)

    Den privata nyckeln kopieras till Urklipp.

## <a name="get-the-account-address"></a>Hämta konto adressen

Du kan använda medlems kontot och lösen ordet för att autentisera när du skickar en transaktion till blockchain. Lösen ordet anges när du skapar medlemmen.

1. I Azure Portal går du till översikts sidan för Azure blockchain-tjänsten.
1. Kopiera **medlems konto** adressen.

    ![Översikts sida med medlems konto adressen](./media/ethereum-logic-app/member-account.png)

Mer information om konto adress och lösen ord finns i [Ethereum-konto](consortium.md#ethereum-account).

## <a name="get-the-contract-abi"></a>Hämta kontrakts ABI

ABI för kontrakt definierar de smarta kontrakts gränssnitten. Den beskriver hur du interagerar med det smarta kontraktet. Du kan hämta kontrakts ABI med hjälp av Azure blockchain Development Kit för Ethereum. Du kan också hämta den från den kontrakts ETA data fil som skapats av halvledare-kompilatorn.

**Använda utvecklings paketet:**

Om du har använt utvecklings paketet eller Truffle för att bygga ditt smarta kontrakt kan du använda tillägget för att kopiera kontrakts ABI till Urklipp.

1. I fönstret Visual Studio Code Explorer expanderar du mappen **Bygg/kontrakt** i ditt projekt.
1. Högerklicka på JSON-filen för kontraktets metadata. Fil namnet är det smarta kontrakt namnet följt av **. JSON** -tillägget.
1. Välj **kopiera kontrakt ABI**.

    ![Fönstret Visual Studio Code med alternativet kopiera kontrakt ABI](./media/ethereum-logic-app/abi-devkit.png)

    Kontraktets ABI kopieras till Urklipp.

**Använda filen med kontraktets metadata:**

1. Öppna den kontrakts ETA data fil som finns i mappen **build/kontrakt** i ditt projekt. Fil namnet är det smarta kontrakt namnet följt av **. JSON** -tillägget.
1. Hitta avsnittet **ABI** i JSON-filen.
1. Kopiera **ABI** JSON-matrisen.

    ![ABI-kod i kontraktets metadatafil](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>Hämta kontraktets bytekod

Kontraktets bytekod är det kompilerade smarta kontraktet som körs av den virtuella Ethereum-datorn. Du kan hämta kontraktets bytekod genom att använda Azure blockchain Development Kit för Ethereum. Du kan också hämta den från halvledar kompilatorn.

**Använda utvecklings paketet:**

Om du har använt utvecklings paketet eller Truffle för att bygga ditt smarta kontrakt kan du använda tillägget för att kopiera kontraktets bytekod till Urklipp.

1. I fönstret Visual Studio Code Explorer expanderar du mappen **Bygg/kontrakt** i ditt projekt.
1. Högerklicka på JSON-filen för kontraktets metadata. Fil namnet är det smarta kontrakt namnet följt av **. JSON** -tillägget.
1. Välj **Kopiera kontrakts bytekod**.

    ![Fönstret Visual Studio Code med alternativet Kopiera kontrakts kod byte](./media/ethereum-logic-app/bytecode-devkit.png)

    Kontraktets bytekod kopieras till Urklipp.

**Använda filen med kontraktets metadata:**

1. Öppna den kontrakts ETA data fil som finns i mappen **build/kontrakt** i ditt projekt. Fil namnet är det smarta kontrakt namnet följt av **. JSON** -tillägget.
1. Hitta elementet **bytekod** i JSON-filen.
1. Kopiera värdet för **bytekod** .

    ![Visual Studio Code-fönstret med bytekod i metadata](./media/ethereum-logic-app/bytecode-metadata.png)

**Så här använder du en halvledare-kompilator:**

Använd kommandot `solc --bin <smart contract>.sol` för att generera kontraktets bytekod.

## <a name="get-the-contract-address"></a>Hämta kontrakt adressen

Kontrakt adressen är mål adressen för det smarta kontraktet på Ethereum-blockchain. Du använder den här adressen för att skicka en transaktion eller ett fråge tillstånd för ett smart kontrakt. Du kan hämta kontrakts adressen från Truffle och filen med kontraktets metadata.

**Så här använder du Truffle migrera utdata:**

Truffle visar kontrakt adressen efter att det smarta kontraktet har distribuerats. Kopiera **kontrakt adressen** från utdata.

![Truffle för migrering med kontrakt adressen i Visual Studio Code](./media/ethereum-logic-app/contract-address-truffle.png)

**Använda filen med kontraktets metadata:**

1. Öppna den kontrakts ETA data fil som finns i mappen **build/kontrakt** i ditt projekt. Fil namnet är det smarta kontrakt namnet följt av **. JSON** -tillägget.
1. Hitta avsnittet **nätverk** i JSON-filen.
1. Privata nätverk identifieras av ett heltals nätverks-ID. Hitta värdet Address i avsnittet Network.
1. Kopiera värdet för **adress** .

![Metadata med Address-värdet i Visual Studio Code](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Nästa steg

Titta på vanliga scenarier i videon som [gör mer med Logic Apps](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
