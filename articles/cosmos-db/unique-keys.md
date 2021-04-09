---
title: Använd unika nycklar i Azure Cosmos DB
description: Lär dig hur du definierar och använder unika nycklar för en Azure Cosmos-databas. Den här artikeln beskriver också hur unika nycklar lägger till ett lager med data integritet.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 9eb2b916bfe6c73a1535afb077b04fbb081dd5f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685728"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Unika nyckel begränsningar i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Unika nycklar Lägg till ett lager med data integritet i en Azure Cosmos-behållare. Du skapar en unik nyckel princip när du skapar en Azure Cosmos-behållare. Med unika nycklar ser du till att ett eller flera värden i en logisk partition är unika. Du kan också garantera unika nycklar per [partitionsnyckel](partitioning-overview.md).

När du har skapat en behållare med en unik nyckel princip förhindras skapandet av en ny eller en uppdatering av ett befintligt objekt som resulterar i en dubblett i en logisk partition, enligt vad som anges i den unika nyckel begränsningen. Partitionsnyckel i kombination med den unika nyckeln garanterar att ett objekt är unikt inom omfånget för behållaren.

Överväg till exempel en Azure Cosmos-behållare med `Email address` som unik nyckel begränsning och `CompanyID` som partitionsnyckel. När du konfigurerar användarens e-postadress med en unik nyckel har varje objekt en unik e-postadress inom ett angivet `CompanyID` . Det går inte att skapa två objekt med dubbla e-postadresser och med samma partitionsnyckel. I Azure Cosmos DB SQL-API: n (Core) lagras objekten som JSON-värden. Dessa JSON-värden är Skift läges känsliga. När du väljer en egenskap som unik nyckel kan du infoga Skift läges känsliga värden för den egenskapen. Om du till exempel har en unik nyckel definierad i egenskapen namn, är "Gaby" inte detsamma som "Gaby" och du kan infoga båda i behållaren.

Om du vill skapa objekt med samma e-postadress, men inte samma förnamn, efter namn och e-postadress, lägger du till fler sökvägar i den unika nyckel principen. I stället för att skapa en unik nyckel som enbart baseras på e-postadressen, kan du också skapa en unik nyckel med en kombination av förnamn, efter namn och e-postadress. Den här nyckeln kallas en sammansatt unik nyckel. I det här fallet tillåts varje unik kombination av de tre värdena inom ett angivet `CompanyID` . 

Behållaren kan till exempel innehålla objekt med följande värden, där varje objekt följer den unika nyckel begränsningen.

|CompanyID|Förnamn|Efternamn|E-postadress|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Om du försöker infoga ett annat objekt med kombinationerna som anges i föregående tabell visas ett fel meddelande. Felet indikerar att den unika nyckel begränsningen inte uppfylldes. Du får antingen `Resource with specified ID or name already exists` eller `Resource with specified ID, name, or unique index already exists` som ett retur meddelande. 

## <a name="define-a-unique-key"></a>Definiera en unik nyckel

Du kan bara definiera unika nycklar när du skapar en Azure Cosmos-behållare. En unik nyckel är begränsad till en logisk partition. I föregående exempel, om du partitionerar behållaren baserat på post numret, slutar du med duplicerade objekt i varje logisk partition. Tänk på följande egenskaper när du skapar unika nycklar:

* Du kan inte uppdatera en befintlig behållare för att använda en annan unik nyckel. När en behållare har skapats med en unik nyckel princip kan du med andra ord inte ändra principen.

* Om du vill ange en unik nyckel för en befintlig behållare skapar du en ny behållare med den unika nyckel begränsningen. Använd lämpligt verktyg för datamigrering för att flytta data från den befintliga behållaren till den nya behållaren. För SQL-behållare använder du [verktyget datamigrering](import-data.md) för att flytta data. För MongoDB-behållare använder du [mongoimport.exe eller mongorestore.exe](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) för att flytta data.

* En unik nyckel princip kan innehålla högst 16 Sök vägs värden. Värdena kan till exempel vara `/firstName` , `/lastName` och `/address/zipCode` . Varje unik nyckel princip kan innehålla högst 10 unika nyckel begränsningar eller kombinationer. De kombinerade Sök vägarna för varje Unique index-begränsning får inte överstiga 60 byte. I det tidigare exemplet är förnamn, efter namn och e-postadress tillsammans en begränsning. Den här begränsningen använder 3 av de 16 möjliga Sök vägarna.

* När en behållare har en unik nyckel princip, [begär enhet (ru)](request-units.md) avgifter för att skapa, uppdatera och ta bort ett objekt är något högre.

* Null-optimerade unika nycklar stöds inte. Om vissa unika Sök vägs värden saknas, behandlas de som null-värden som ingår i unikhetsvillkoret. Därför kan det bara finnas ett enda objekt med ett null-värde för att uppfylla den här begränsningen.

* Unika nyckel namn är Skift läges känsliga. Anta till exempel att en behållare med den unika nyckel begränsningen är inställd på `/address/zipcode` . Om dina data har ett fält med namnet `ZipCode` Azure Cosmos DB infogar "null" som den unika nyckeln eftersom `zipcode` det inte är samma som `ZipCode` . På grund av den här Skift läges känsligheten kan inte alla andra poster med Postummer infogas eftersom dubbletten "null" bryter mot den unika nyckel begränsningen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [logiska partitioner](partitioning-overview.md)
* Lär dig [hur du definierar unika nycklar när du](how-to-define-unique-keys.md) skapar en behållare
