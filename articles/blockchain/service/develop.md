---
title: Översikt över Azure blockchain service-utveckling
description: Introduktion till utveckling av lösningar i Azure blockchain-tjänsten.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80348385"
---
# <a name="azure-blockchain-service-development-overview"></a>Översikt över Azure blockchain service-utveckling

Med Azure blockchain-tjänsten kan du skapa konsortier blockchain-nätverk för att möjliggöra företags scenarier som till gångs spårning, digital token, lojalitet och belöning, försörjnings kedjans ekonomi och härkomst. I följande avsnitt introduceras Azure blockchain service Development för att implementera Enterprise blockchain-lösningar.

## <a name="connecting-to-azure-blockchain-service"></a>Ansluta till Azure blockchain-tjänsten

Det finns olika typer av klienter för blockchain-nätverk, inklusive fullständiga noder, lätta noder och fjärrklienter. Azure blockchain service skapar ett blockchain-nätverk som innehåller noder. Du kan använda olika klienter som din gateway till Azure blockchain-tjänsten för blockchain-utveckling. Azure blockchain-tjänsten erbjuder grundläggande autentisering eller åtkomst nyckel som en utvecklings slut punkt. Följande är populära klienter som du kan använda Connect.

### <a name="visual-studio-code"></a>Visuell Studio-kod

Du kan ansluta till medlemmar i konsortiet med Visual Studio Code-tillägget för Azure blockchain Development Kit. När du har anslutit till ett konsortium kan du kompilera, bygga och distribuera smarta kontrakt till en Azure blockchain service Consortium-medlem.

För att utveckla avancerade lösningar för företags blockchain krävs ett utvecklings ramverk för att ansluta till olika blockchain-nätverk och hantera livs cykel för smarta kontrakt. De flesta projekt interagerar med minst två blockchain-noder. Utvecklare använder en lokal blockchain under utvecklingen. När programmet är redo för testning eller lansering distribuerar utvecklaren till ett blockchain-nätverk. Till exempel det huvudsakliga offentliga Ethereum-nätverket eller Azure blockchain-tjänsten. Azure blockchain Development Kit för Ethereum-tillägget i Visual Studio Code använder Truffle. Truffle är ett populärt blockchain Development Framework för att skriva, kompilera, distribuera och testa decentraliserade program på Ethereum Blockchains. Du kan också tänka på Truffle som ett ramverk som försöker sömlöst integrera Smart kontrakt utveckling och traditionell webb utveckling.

Mer information finns i [snabb start: använda Visual Studio Code för att ansluta till ett Azure blockchain service Consortium-nätverk](connect-vscode.md).

### <a name="metamask"></a>MetaMask

MetaMask är en webbläsarbaserad plån boks tjänst (fjärrklient), RPC-klient och grundläggande kontrakts Utforskare. Till skillnad från andra webb läsar-plåner, infogar MetaMask en Web3-instans i webbläsarens JavaScript-kontext, som fungerar som en RPC-klient som ansluter till en rad olika Ethereum Blockchains (*mainnet*, *Ropsten* testnet, *Kovan testnet*, Local RPC Node osv.). Du kan enkelt konfigurera anpassade RPC för att ansluta till Azure blockchain-tjänsten och starta blockchain-utveckling med Remix.

Mer information finns i [snabb start: använda MetaMask för att ansluta och distribuera ett smart kontrakt](connect-metamask.md)

### <a name="geth"></a>Geth

Geth är kommando rads gränssnittet för att köra en fullständig Ethereum-nod som implementerats i go. Du behöver inte köra fullständig nod, men kan starta den interaktiva konsolen som tillhandahåller en Java Script runtime-miljö som visar ett JavaScript-API för att interagera med Azure blockchain-tjänsten.

Mer information finns i [snabb start: använda Geth för att ansluta till en Azure blockchain service Transaction-nod](connect-geth.md).

## <a name="ethereum-quorum-private-transactions"></a>Privata transaktioner för Ethereum-kvorum

Kvorum är ett Ethereum distribuerat redovisnings protokoll med transaktion plus avtals sekretess och nya enighets metoder. Viktiga förbättringar jämfört med Go-Ethereum:

* **Sekretess** -kvorum stöder privata transaktioner och privata kontrakt via offentlig och privat delstats delning och använder sig av peer-to-peer-krypterade meddelande utbyten för riktad överföring av privata data till nätverks deltagare.
* **Alternativa vedertagna mekanismer** – bevis-från-arbete eller bevis på att det inte behövs något som har behörighet för ett nätverk. Kvorum har flera sammanställda mekanismer som är utformade för konsortier som RAFT och IBFT.  I Azure blockchain service används den IBFTa enighets metoden.
* **Peer-behörighet** – nod-och peer-behörighet med smarta kontrakt garanterar att endast kända parter kan ansluta till nätverket.
* **Högre prestanda** – kvorum ger högre prestanda än offentliga Geth.

## <a name="block-explorers"></a>Blockera Explorer

Blockera Explorer är online blockchain webbläsare som visar enskilda block innehåll, transaktions adress data och historik. Basic block information är tillgänglig via Azure Monitor i Azure blockchain-tjänsten. Men om du behöver mer detaljerad information under utvecklingen kan blockerande Utforskare vara användbara.  Följande block Utforskaren fungerar med Azure blockchain-tjänsten:

* [Epirus Azure blockchain service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) från Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

Du kan också bygga en egen block Utforskare genom att använda blockchain Data Manager och Azure Cosmos DB, se [Självstudier: Använd blockchain-Data Manager för att skicka data till Azure Cosmos DB](data-manager-cosmosdb.md).

## <a name="tps-measurement"></a>TPS-mått

Eftersom blockchain används i fler företags scenarier är det viktigt att du använder TPS-hastighet för att undvika Flask halsar och system ineffektivitet. Hög transaktions taxa kan vara svårt att underhålla inom en decentraliserad blockchain. En korrekt TPS-mätning kan påverkas av olika faktorer, till exempel Server tråd, storlek på transaktionskö, nätverks fördröjning och säkerhet. Om du behöver mäta TPS-hastigheten under utvecklingen är ett populärt verktyg för öppen källkod [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Nästa steg

Prova en snabb start med Azure blockchain Development Kit för Ethereum för att ansluta till ett konsortium på Azure blockchain-tjänsten.

> [!div class="nextstepaction"]
> [Använd Visual Studio Code för att ansluta till Azure blockchain-tjänsten](connect-vscode.md)