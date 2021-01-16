---
title: Pris modell för Azure Cosmos DB
description: I den här artikeln beskrivs pris sättnings modellen för Azure Cosmos DB och hur det fören klar kostnads hanteringen och kostnads planeringen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 573fc4fac413ceed50246bc6fb8df1d9db021c94
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247479"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Prismodell i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Prissättningsmodellen för Azure Cosmos DB gör det enkelt att planera och hantera dina kostnader. Med Azure Cosmos DB betalar du för de åtgärder som du utför mot databasen och för det lagrings utrymme som förbrukas av dina data.

- **Databas åtgärder**: hur du debiteras för dina databas åtgärder beror på vilken typ av Azure Cosmos-konto du använder.

  - **Tillhandahållet data flöde**: ett [tillhandahållet data flöde](set-throughput.md) (kallas även reserverat data flöde) ger hög prestanda i valfri skala. Du anger det data flöde som du behöver i [enheter för programbegäran](request-units.md) per sekund (ru/s) och Azure Cosmos DB dedikera de resurser som krävs för att tillhandahålla det konfigurerade data flödet. Du kan [etablera data flöde för antingen en databas eller en behållare](set-throughput.md). Utifrån dina arbets belastnings behov kan du skala data flödet uppåt/nedåt när som helst eller använda [autoskalning](provision-throughput-autoscale.md) (även om det finns ett minsta data flöde som krävs för en databas eller en behållare för att garantera service avtal). Du faktureras per timme för maximalt tillhandahållet data flöde i en specifik timme.

   > [!NOTE]
   > Eftersom den etablerade data flödes modellen dedikerar resurser till din behållare eller databas debiteras du för det data flöde som du har allokerat även om du inte kör några arbets belastningar.

  - Utan **Server**: i [Server](serverless.md) fritt läge behöver du inte etablera några data flöden när du skapar resurser i ditt Azure Cosmos-konto. I slutet av fakturerings perioden debiteras du för den mängd enheter för programbegäran som har använts av databas åtgärderna.

- **Lagring**: du debiteras ett fast pris för den totala mängden lagrings utrymme (i GB) som används av dina data och index för en specifik timme. Lagringen debiteras enligt förbrukningen, så du behöver inte reservera någon lagring i förväg. Du faktureras bara för den lagring som du använder.

Pris sättnings modellen i Azure Cosmos DB är konsekvent i alla API: er. Mer information finns på [sidan Azure Cosmos DB prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/), [förstå din Azure Cosmos DB faktura](understand-your-bill.md) och [hur Azure Cosmos DB prissättnings modell är kostnads effektiv för kunder](total-cost-ownership.md).

Om du distribuerar ditt Azure Cosmos DB-konto till en icke-myndighets region i USA, är det ett minimi pris för både databas-och containerbaserade data flöde i det etablerade data flödes läget. Det finns inget minimi pris i läget utan server. Prissättningen varierar beroende på vilken region du använder, se [sidan Azure Cosmos DB prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/) för den senaste pris informationen.

## <a name="try-azure-cosmos-db-for-free"></a>Testa Azure Cosmos DB kostnadsfritt

Azure Cosmos DB erbjuder många alternativ för utvecklare utan kostnad. Dessa alternativ är:

* **Azure Cosmos DB kostnads fri nivå**: Azure Cosmos DB kostnads fri nivå är det enkelt att komma igång, utveckla och testa dina program, eller till och med köra små produktions arbets belastningar utan kostnad. När den kostnads fria nivån har Aktiver ATS för ett konto får du de första 400 RU/s och 5 GB lagrings utrymme i kontot kostnads fritt för kontots livstid. Du kan ha upp till ett konto med en kostnads fri nivå per Azure-prenumeration och måste välja när du skapar kontot. Kom igång genom att [skapa ett nytt konto i Azure Portal med kostnads fri nivå aktive rad](create-cosmosdb-resources-portal.md) eller använda en [arm-mall](./manage-with-templates.md#free-tier).

* **Kostnads fritt Azure-konto**: Azure erbjuder en [kostnads fri nivå](https://azure.microsoft.com/free/) som ger dig $200 i Azure-krediter under de första 30 dagarna och en begränsad mängd kostnads fria tjänster i 12 månader. Mer information finns i [Kostnadsfritt Azure-konto](../cost-management-billing/manage/avoid-charges-free-account.md). Azure Cosmos DB är en del av ett kostnads fritt Azure-konto. I synnerhet för Azure Cosmos DB erbjuder det här kostnads fria kontot 25 GB lagring och 400 RU/s av etablerade data flöden för hela året.

* **Prova Azure Cosmos DB kostnads fritt**: Azure Cosmos DB erbjuder en tidsbegränsad upplevelse genom att använda Try Azure Cosmos dB för kostnads fria konton. Du kan skapa ett Azure Cosmos DB konto, skapa databas och samlingar och köra ett exempel program med hjälp av snabb starter och självstudier. Du kan köra exempel programmet utan att prenumerera på ett Azure-konto eller använda ditt kredit kort. [Försök Azure Cosmos DB kostnads fria](https://azure.microsoft.com/try/cosmosdb/) erbjudanden Azure Cosmos db i en månad, med möjlighet att förnya ditt konto valfritt antal gånger.

* **Azure Cosmos DB emulator**: Azure Cosmos DB emulator är en lokal miljö som emulerar Azure Cosmos DBS tjänsten i utvecklings syfte. Emulatorn erbjuds utan kostnad och med hög kvalitet på moln tjänsten. Med hjälp av Azure Cosmos DB-emulatorn kan du utveckla och testa dina program lokalt, utan att skapa en Azure-prenumeration eller skapa några kostnader. Du kan utveckla dina program med hjälp av emulatorn lokalt innan du börjar arbeta med produktionen. När du är nöjd med programmets funktioner mot emulatorn kan du växla till att använda det Azure Cosmos DB kontot i molnet och markant Spara pengar. Mer information om emulator finns i [använda Azure Cosmos dB för utvecklings-och testnings](local-emulator.md) artiklar.

## <a name="pricing-with-reserved-capacity"></a>Prissättning med reserverad kapacitet

Azure Cosmos DB [reserverad kapacitet](cosmos-db-reserved-capacity.md) hjälper dig att spara pengar när du använder det tillhandahållna data flödes läget genom förskotts betalning för Azure Cosmos DB resurser i ett år eller tre år. Du kan avsevärt minska dina kostnader med ett års-eller tre års åtagande och spara mellan 20-65% rabatt jämfört med den vanliga prissättningen. Azure Cosmos DB reserverad kapacitet hjälper dig att sänka kostnaderna genom förskotts betalning för det etablerade data flödet (RU/s) under en period på ett år eller tre år och du får rabatt på det data flöde som du har tillhandahållit. 

Reserverad kapacitet ger rabatt på fakturan, den påverkar inte körningsstatusen för dina Azure Cosmos DB-resurser. Reserverad kapacitet är konsekvent för alla API: er, som innehåller MongoDB-, Cassandra-, SQL-, Gremlin-och Azure-tabeller och alla regioner över hela världen. Du kan lära dig mer om reserverad kapacitet i [förskott för Azure Cosmos DB resurser med reserverad kapacitet](cosmos-db-reserved-capacity.md) artikel och köpa reserverad kapacitet från [Azure Portal](https://portal.azure.com/).

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om hur du optimerar kostnaderna för Azure Cosmos DB resurser i följande artiklar:

* Lär dig mer om [optimering för utveckling och testning](optimize-dev-test.md)
* Lär dig mer om [att förstå din Azure Cosmos DB faktura](understand-your-bill.md)
* Läs mer om hur du [optimerar data flödes kostnaden](optimize-cost-throughput.md)
* Läs mer om hur du [optimerar lagrings kostnader](optimize-cost-storage.md)
* Läs mer om hur [du optimerar kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Lär dig mer om hur [du optimerar kostnaden för frågor](./optimize-cost-reads-writes.md)
* Lär dig mer om hur [du optimerar kostnaden för Cosmos-konton med flera regioner](optimize-cost-regions.md)
* Lär dig mer om [Azure Cosmos DB reserverad kapacitet](cosmos-db-reserved-capacity.md)
* Lär dig mer om [Azure Cosmos DB emulator](local-emulator.md)