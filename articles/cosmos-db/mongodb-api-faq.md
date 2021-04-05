---
title: Vanliga frågor och svar om Azure Cosmos DBs API för MongoDB
description: Få svar på vanliga frågor om Azure Cosmos DBs API för MongoDB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 08899018d03209dab09f61d4dd74feceee03b246
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019028"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>Vanliga frågor och svar om Azure Cosmos DBs API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DBs API för MongoDB är ett protokoll för högpresterande protokoll som gör det möjligt för program att enkelt och transparent kommunicera med den inbyggda Azure Cosmos-databasmotorn genom att använda befintliga SDK: er och driv rutiner som stöds av communityn för MongoDB. Utvecklare kan nu använda befintliga MongoDB-verktygs kedjor och-kunskaper för att skapa program som utnyttjar Azure Cosmos DB. Utvecklare drar nytta av de unika funktionerna i Azure Cosmos DB, som inkluderar global distribution med Skriv replikering i flera regioner, automatisk indexering, säkerhets kopierings underhåll, ekonomiskt säkerhetskopierade service avtal (service avtal) osv.

## <a name="how-do-i-connect-to-my-database"></a>Hur gör jag för att ansluta till min databas?

Det snabbaste sättet att ansluta till en Cosmos-databas med Azure Cosmos DB s API för MongoDB är att gå vidare till [Azure Portal](https://portal.azure.com). Gå till ditt konto och klicka sedan på **Snabbstart** på den vänstra navigerings menyn. Snabb start är det bästa sättet att hämta kodfragment för att ansluta till databasen.

Azure Cosmos DB tillämpar strikta säkerhets krav och standarder. Azure Cosmos DB konton kräver autentisering och säker kommunikation via TLS, så se till att använda TLSv 1.2.

Mer information finns i [ansluta till din Cosmos-databas med Azure Cosmos DB s API för MongoDB](connect-mongodb-account.md).

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Felkoder när du använder Azure Cosmos DB s API för MongoDB?

Tillsammans med de vanliga fel koderna för MongoDB har Azure Cosmos DBs API för MongoDB sina egna specifika felkoder. Du hittar dem i [fel söknings guiden](mongodb-troubleshoot.md).

## <a name="supported-drivers"></a>Driv rutiner som stöds

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Finns Simba-drivrutinen för MongoDB som stöds för användning med Azure Cosmos DB s API för MongoDB?

Ja, du kan använda Simba Mongo ODBC-drivrutin med Azure Cosmos DB s API för MongoDB

## <a name="next-steps"></a>Nästa steg

* [Bygg en .NET-webbapp med hjälp av Azure Cosmos DB s API för MongoDB](create-mongodb-dotnet.md)
* [Skapa en konsol app med Java och MongoDB-API: et i Azure Cosmos DB](create-mongodb-java.md)
