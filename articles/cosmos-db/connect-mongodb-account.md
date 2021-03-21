---
title: Ansluta ett MongoDB-program till Azure Cosmos DB
description: Lär dig hur du ansluter en MongoDB-app till Azure Cosmos DB genom att hämta anslutnings strängen från Azure Portal
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.reviewer: sngun
adobe-target: true
adobe-target-activity: DocsExp-A/B-384740-MongoDB-2.8.2021
adobe-target-experience: Experience B
adobe-target-content: ./connect-mongodb-account-experimental
ms.openlocfilehash: b3e10931307914f1471b8a6fbffd38953ee4717b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659485"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Ansluta ett MongoDB-program till Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Lär dig hur du ansluter MongoDB-appen till en Azure Cosmos DB med hjälp av en MongoDB-anslutningssträng. Du kan sedan använda en Azure Cosmos-databas som datalager för din MongoDB-app.

I den här självstudien beskrivs två sätt att hämta information om anslutningssträng:

- [Snabb starts metoden](#get-the-mongodb-connection-string-by-using-the-quick-start)för användning med .net, Node.js, MongoDB Shell-, Java-och python-drivrutiner
- [Metoden anpassad anslutnings sträng](#get-the-mongodb-connection-string-to-customize), som används med andra driv rutiner

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Om du inte har ett Azure-konto kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) nu.
- Ett Cosmos-konto. Instruktioner finns i [bygga en webbapp med hjälp av Azure Cosmos DBS API för MongoDB och .NET SDK](create-mongodb-dotnet.md).

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Hämta anslutnings strängen för MongoDB med hjälp av snabb start

1. Logga in på [Azure Portal](https://portal.azure.com)i en webbläsare.
2. På bladet **Azure Cosmos DB** väljer du API: et.
3. I det vänstra fönstret på konto bladet klickar du på **snabb start**.
4. Välj din plattform (**.net**, **Node.js**, **MongoDB Shell**, **Java**, **python**). Om du inte ser den driv rutin eller det verktyg som visas, behöver vi kontinuerligt dokumentera fler anslutnings kods tycken. Kommentera nedan om vad du vill se. Information om hur du kan skapa en egen anslutning finns [i Hämta kontots anslutnings sträng information](#get-the-mongodb-connection-string-to-customize).
5. Kopiera och klistra in kodfragmentet i MongoDB-appen.

    :::image type="content" source="./media/connect-mongodb-account/QuickStartBlade.png" alt-text="Bladet snabb start":::

## <a name="get-the-mongodb-connection-string-to-customize"></a>Hämta MongoDB-anslutningssträngen för att anpassa

1. Logga in på [Azure Portal](https://portal.azure.com)i en webbläsare.
2. På bladet **Azure Cosmos DB** väljer du API: et.
3. I det vänstra fönstret på konto bladet klickar du på **anslutnings sträng**.
4. Bladet **anslutnings sträng** öppnas. Den innehåller all information som krävs för att ansluta till kontot med hjälp av en driv rutin för MongoDB, inklusive en förkonstruerad anslutnings sträng.

   :::image type="content" source="./media/connect-mongodb-account/ConnectionStringBlade.png" alt-text="Bladet Anslutningssträng" lightbox= "./media/connect-mongodb-account/ConnectionStringBlade.png" :::

## <a name="connection-string-requirements"></a>Krav för anslutnings sträng

> [!Important]
> Azure Cosmos DB har stränga säkerhetskrav och säkerhetsstandarder. Azure Cosmos DB konton kräver autentisering och säker kommunikation via *TLS*.

Azure Cosmos DB stöder URI-formatet standard MongoDB-anslutnings sträng, med ett par särskilda krav: Azure Cosmos DB konton kräver autentisering och säker kommunikation via TLS. Formatet för anslutnings strängen är alltså:

`mongodb://username:password@host:port/[database]?ssl=true`

Värdena för den här strängen är tillgängliga på bladet **anslutnings sträng** som visades tidigare:

* Användar namn (obligatoriskt): Cosmos-konto namn.
* Lösen ord (obligatoriskt): Cosmos-kontots lösen ord.
* Värd (obligatoriskt): FQDN för Cosmos-kontot.
* Port (krävs): 10255.
* Databas (valfritt): databasen som anslutningen använder. Om ingen databas anges är standard databasen "test".
* SSL = true (krävs)

Överväg till exempel kontot som visas på bladet **anslutnings sträng** . En giltig anslutnings sträng är:

`mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true`

## <a name="driver-requirements"></a>Driv rutins krav

Alla driv rutiner som stöder Wire Protocol version 3,4 eller senare stöder Azure Cosmos DB API för MongoDB.

Mer specifikt måste klient driv rutinerna ha stöd för TLS-tillägget SNI (service Name Identification) och/eller anslutnings sträng för appName. Om `appName` parametern anges måste den tas med i värdet för anslutnings strängen i Azure Portal.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.
