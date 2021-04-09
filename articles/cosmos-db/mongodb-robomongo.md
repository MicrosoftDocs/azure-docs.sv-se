---
title: Använd Robo 3T för att ansluta till Azure Cosmos DB
description: Lär dig hur du ansluter till Azure Cosmos DB med hjälp av Robo 3T och Azure Cosmos DB s API för MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: e096de5eea9bf5bbd47b976976192e62eb6aa33e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096420"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Använda Robo 3T med Azure Cosmos DB-API:et för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Om du vill ansluta till Cosmos-kontot med Robo 3T måste du:

* Hämta och installera [Robo 3T](https://robomongo.org/)
* Ha information om Cosmos DB- [anslutningssträng](connect-mongodb-account.md)

> [!NOTE]
> För närvarande stöds Robo 3T v 1.2 och lägre versioner med Cosmos DB s API för MongoDB.

## <a name="connect-using-robo-3t"></a>Ansluta med Robo 3T

Gör så här om du vill lägga till ditt Cosmos-konto i Robo 3T Connection Manager:

1. Hämta anslutnings informationen för ditt Cosmos-konto som kon figurer ATS med Azure Cosmos DB API-MongoDB med hjälp av instruktionerna [här](connect-mongodb-account.md).

    :::image type="content" source="./media/mongodb-robomongo/connectionstringblade.png" alt-text="Skärm bild av bladet anslutnings sträng":::
2. Kör *Robomongo* -programmet.

3. Klicka på knappen anslutning under **fil** för att hantera dina anslutningar. Klicka sedan på **skapa** i fönstret **MongoDB-anslutningar** , som öppnar fönstret **anslutnings inställningar** .

4. I fönstret **anslutnings inställningar** väljer du ett namn. Leta sedan upp **värden** och **porten** från anslutnings informationen i steg 1 och ange dem i **adress** respektive **port**.

    :::image type="content" source="./media/mongodb-robomongo/manageconnections.png" alt-text="Skärm bild av Robomongo hantera anslutningar":::
5. På fliken **autentisering** klickar du på **utför autentisering**. Ange sedan din databas (standard är *administratör*), **användar namn** och **lösen ord**.
Du hittar både **användar namn** och **lösen ord** i anslutnings informationen i steg 1.

    :::image type="content" source="./media/mongodb-robomongo/authentication.png" alt-text="Skärm bild av fliken Robomongo-autentisering":::
6. På fliken **SSL** kontrollerar du **Använd SSL-protokoll** och ändrar sedan **autentiseringsmetoden** till ett **självsignerat certifikat**.

    :::image type="content" source="./media/mongodb-robomongo/SSL.png" alt-text="Skärm bild av Robomongo SSL-fliken":::
7. Klicka slutligen på **test** för att kontrol lera att du kan ansluta och **Spara**.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.
