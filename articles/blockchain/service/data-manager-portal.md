---
title: Konfigurera blockchain Data Manager med hjälp av Azure Portal Azure blockchain-tjänsten
description: Skapa och hantera blockchain-Data Manager för Azure blockchain-tjänsten med hjälp av Azure Portal.
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: a151c28d31bf0bb7f21185fb161315d42f9563d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85200689"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Konfigurera Blockchain Data Manager med Azure-portalen

Konfigurera blockchain Data Manager för Azure blockchain-tjänsten för att avbilda blockchain-data och skicka dem till ett Azure Event Grid ämne.

Om du vill konfigurera en blockchain Data Manager instans:

* Skapa en blockchain Data Manager instans för en Azure blockchain service Transaction-nod
* Lägg till dina blockchain-program

## <a name="prerequisites"></a>Förutsättningar

* Slutför [snabb start: skapa en blockchain-medlem med hjälp av Azure Portal](create-member.md) eller [snabb start: skapa en Azure blockchain service blockchain-medlem med Azure CLI](create-member-cli.md). *Standard* nivån för Azure blockchain service rekommenderas när du använder blockchain Data Manager.
* Skapa ett [Event Grid ämne](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Lär dig mer om [händelse hanterare i Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Skapa instans

En blockchain Data Manager-instans ansluter och övervakar en Azure blockchain service Transaction-nod. Endast användare med åtkomst till Transaction-noden kan skapa en anslutning. En instans fångar alla RAW-block och rå transaktions data från Transaction-noden. Blockchain Data Manager publicerar ett **RawBlockAndTransactionMsg** -meddelande som är en supermängd av information som returneras från web3. ETH [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) och [getTransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) -frågor.

En utgående anslutning skickar blockchain-data till Azure Event Grid. Du konfigurerar en enda utgående anslutning när du skapar instansen. Blockchain Data Manager har stöd för flera Event Grid ämnen utgående anslutningar för en specifik blockchain Data Manager-instans. Du kan skicka blockchain-data till ett enda mål eller skicka blockchain-data till flera mål. Lägg till ytterligare utgående anslutningar till instansen om du vill lägga till ett annat mål.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till den Azure blockchain service-medlem som du vill ansluta till blockchain Data Manager. Välj **Blockchain Data Manager**.
1. Välj **Lägg till**.

    ![Lägg till blockchain Data Manager](./media/data-manager-portal/add-instance.png)

    Ange följande information:

    Inställning | Beskrivning
    --------|------------
    Name | Ange ett unikt namn för en ansluten blockchain Data Manager. Namnet på blockchain Data Manager får innehålla gemener och siffror och får innehålla högst 20 tecken.
    Transaction Node | Välj en Transaction-nod. Endast transaktionsloggfiler som du har Läs behörighet visas.
    Anslutningsnamn | Ange ett unikt namn på den utgående anslutningen där blockchain transaktions data skickas.
    Event Grid-slutpunkt | Välj ett event Grid-ämne i samma prenumeration som blockchain Data Manager-instansen.

1. Välj **OK**.

    Det tar mindre än en minut att skapa en blockchain Data Manager-instans. När instansen har distribuerats startas den automatiskt. En körning av en blockchain Data Manager-instans fångar blockchain-händelser från Transaction-noden och skickar data till utgående anslutningar.

    Den nya instansen visas i listan över blockchain Data Manager instanser för Azure blockchain-tjänstemedlem.

    ![Lista över blockchain data medlems instanser](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Lägg till blockchain-program

Om du lägger till ett blockchain-program avkodar Data Manager blockchain händelse-och egenskaps tillstånd för programmet. Annars skickas endast rå block och rå transaktions information. Blockchain Data Manager identifierar också kontrakts adresser när kontraktet distribueras. Du kan lägga till flera blockchain-program till en blockchain Data Manager-instans.

> [!IMPORTANT]
> Blockchain-program som deklarerar [mat ris typer](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) eller [mappnings typer](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) stöds för närvarande inte fullt ut. Egenskaper som är deklarerade som matris-eller mappnings typer avkodas inte i *ContractPropertiesMsg* -eller *DecodedContractEventsMsg* -meddelanden.

Blockchain Data Manager kräver ett smart kontrakt ABI och distribuerad bytekod-fil för att lägga till programmet.

### <a name="get-contract-abi-and-bytecode"></a>Hämta kontrakts ABI och bytekod

ABI för kontrakt definierar de smarta kontrakts gränssnitten. Den beskriver hur du interagerar med det smarta kontraktet. Du kan använda [Azure blockchain Development Kit för Ethereum-tillägget](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) för att kopiera kontrakts ABI till Urklipp.

1. I fönstret Visual Studio Code Explorer expanderar du mappen **Bygg/kontrakt** i ditt projekt.
1. Högerklicka på JSON-filen för kontraktets metadata. Fil namnet är det smarta kontrakt namnet följt av **. JSON** -tillägget.
1. Välj **kopiera kontrakt ABI**.

    ![Fönstret Visual Studio Code med alternativet kopiera kontrakt ABI](./media/data-manager-portal/abi-devkit.png)

    Kontraktets ABI kopieras till Urklipp.

1. Spara **ABI** -matrisen som en JSON-fil. Till exempel *abi.jspå*. Du använder filen i ett senare steg.

Blockchain-Data Manager kräver distribuerad bytekod för det smarta kontraktet. Den distribuerade bytekod-filen skiljer sig från det smarta kontraktets bytekod. Du kan använda Azure blockchain Development Kit-tillägget för att kopiera bytekod till Urklipp.

1. I fönstret Visual Studio Code Explorer expanderar du mappen **Bygg/kontrakt** i ditt projekt.
1. Högerklicka på JSON-filen för kontraktets metadata. Fil namnet är det smarta kontrakt namnet följt av **. JSON** -tillägget.
1. Välj **Kopiera transaktions-bytekod**.

    ![Fönstret Visual Studio Code med valet kopiera transaktions-bytekod](./media/data-manager-portal/bytecode-devkit.png)

    Bytekod kopieras till Urklipp.

1. Spara **bytekod** -värdet som en JSON-fil. Till exempel *bytecode.jspå*. Du använder filen i ett senare steg.

I följande exempel visas *abi.jspå* och *bytecode.jspå* filer som är öppna i vs Code-redigeraren. Dina filer bör se ut ungefär så här.

![Exempel på abi.jspå och bytecode.jspå filer](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Skapa kontrakts ABI och bytekod-URL

Blockchain Data Manager kräver att kontrakts ABI och bytekod-filerna kan nås av en URL när du lägger till ett program. Du kan använda ett Azure Storage-konto för att tillhandahålla en privat tillgänglig URL.

#### <a name="create-storage-account"></a>Skapa lagringskonto

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Ladda upp kontraktmallar

1. Skapa en ny behållare för lagrings kontot. Välj **behållare > behållare**.

    ![Skapa en lagrings konto behållare](./media/data-manager-portal/create-container.png)

    | Fält | Beskrivning |
    |-------|-------------|
    | Name  | Ge containern ett namn. Till exempel *smartcontract* |
    | Offentlig åtkomstnivå | Välj *privat (ingen anonym åtkomst)* |

1. Klicka på **OK** för att skapa containern.
1. Välj behållaren och välj sedan **Ladda upp**.
1. Välj båda JSON-filerna som du skapade i avsnittet [Hämta kontrakt ABI och bytekod](#get-contract-abi-and-bytecode) .

    ![Ladda upp BLOB](./media/data-manager-portal/upload-blobs.png)

    Välj **Överför**.

#### <a name="generate-url"></a>Generera URL

Generera en signatur för delad åtkomst för varje blob.

1. Välj ABI JSON-blobben.
1. Välj **generera SAS**
1. Ange önskat förfallo datum för signaturen **och välj sedan generera BLOB SAS-token och URL**.

    ![Generera SAS-token](./media/data-manager-portal/generate-sas.png)

1. Kopiera **URL: en för BLOB SAS** och spara den för nästa avsnitt.
1. Upprepa stegen för att [generera URL: er](#generate-url) för BYTEKOD-JSON-blobben.

### <a name="add-application-to-instance"></a>Lägg till program till instans

1. Välj din blockchain-Data Manager instans från instans listan.
1. Välj **blockchain-program**.
1. Välj **Lägg till**.

    ![Lägg till ett blockchain-program](./media/data-manager-portal/add-application.png)

    Ange namnet på blockchain-programmet och URL: en för smart kontrakt ABI och bytekod.

    Inställning | Beskrivning
    --------|------------
    Name | Ange ett unikt namn för blockchain-programmet som ska spåras.
    Kontrakt ABI | URL-sökväg till kontraktets ABI-fil. Mer information finns i [skapa kontrakts ABI och BYTEKOD URL](#create-contract-abi-and-bytecode-url).
    Kontraktets bytekod | URL-sökväg till bytekod-fil. Mer information finns i [skapa kontrakts ABI och BYTEKOD URL](#create-contract-abi-and-bytecode-url).

1. Välj **OK**.

    När programmet har skapats visas programmet i listan över blockchain-program.

    ![Lista över blockchain program](./media/data-manager-portal/artifact-list.png)

Du kan ta bort Azure Storage kontot eller använda det för att konfigurera fler blockchain-program. Om du vill ta bort Azure Storages kontot kan du ta bort resurs gruppen. När du tar bort resursgruppen raderas även det kopplade lagringskontot och eventuella andra resurser som är kopplade till resursgruppen.

## <a name="stop-instance"></a>Stoppa instans

Stoppa blockchain Manager-instansen när du vill sluta samla in blockchain-händelser och skicka data till utgående anslutningar. När instansen stoppas uppstår inga kostnader för blockchain Data Manager. Mer information finns i [prissättning](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Gå till **Översikt** och välj **stoppa**.

    ![Stoppa instans](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Nästa steg

Testa nästa självstudie och skapa en blockchain transaktions meddelande Utforskare med blockchain Data Manager och Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Använda Blockchain Data Manager för att skicka data till Azure Cosmos DB](data-manager-cosmosdb.md)