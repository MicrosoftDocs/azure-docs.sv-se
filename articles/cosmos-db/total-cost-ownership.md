---
title: Total ägande kostnad (TCO) med Azure Cosmos DB
description: I den här artikeln jämförs den totala ägande kostnaden för Azure Cosmos DB med IaaS och lokala databaser
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: eb06d101170685bea5ff231612e9ffbf8df467da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93079299"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Total ägande kostnad (TCO) med Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB har en detaljerad styrning av flera klientorganisationer och resurser. Den här designen gör att du kan använda Azure Cosmos DB till en betydligt lägre kostnad och spara pengar. Azure Cosmos DB har för närvarande stöd för fler än 280 kundarbetsbelastningar på en enskild dator och densiteten ökar hela tiden, och du kan köra tusentals kundarbetsbelastningar i ett kluster. Repliker av kundernas arbetsbelastningar belastningsutjämnas mellan olika datorer i ett kluster och mellan olika kluster i ett datacenter. Mer information finns i [Azure Cosmos DB: push-gränsen för globalt distribuerade databaser](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). På grund av resurs styrning, flera innehavare och inbyggd integrering med resten av Azure-infrastrukturen, är Azure Cosmos DB i genomsnitt 4 till 6 gånger billigare än MongoDB, Cassandra eller andra OSS-NoSQL som körs på IaaS och upp till 10 gånger billigare än databas motorerna som körs lokalt. Se papperet till [den totala kostnaden för (icke) ägarskapet av en NoSQL Database-moln tjänst](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

OSS NoSQL Database-lösningar, som Apache Cassandra, MongoDB, HBase, motorer har utformats för lokalt. När de erbjuds som en hanterad tjänst motsvarar de en Resource Manager-mall med en klient databas för hantering av etablerade kluster och övervaknings stöd. OSS NoSQL-arkitekturer kräver betydande drifts kostnader och experten kan vara svår och dyrt att hitta. Å andra sidan är Azure Cosmos DB en fullständigt hanterad moln tjänst som gör att utvecklare kan fokusera på affärs innovation i stället för att hantera och underhålla databas infrastruktur.

Till skillnad från en molnbaserad databas tjänst Azure Cosmos DB har OSS NoSQL-databasmotorn inte utformats och skapats tillsammans med resurs styrningen eller detaljerade flera innehavare som de grundläggande arkitektoniska principerna. OSS NoSQL Database-motorer som Cassandra och MongoDB gör ett grundläggande antagande om att alla resurser på den virtuella datorn där de körs är tillgängliga för deras användning. Många av dessa databas motorer fungerar inte om mängden resurser sjunker under ett visst tröskelvärde. För till exempel små VM-instanser, och de är tillgängliga med leverantörs rekommenderade konfigurationer, rekommenderar vi vanligt vis storskaliga virtuella datorer med högre kostnad. Det är därför inte möjligt att vara värd för en OSS-NoSQL eller någon annan lokal databas motor och göra den tillgänglig med hjälp av en förbruknings-baserad avgifts modell som begär Anden per sekund eller förbrukad lagring.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Den totala ägande kostnaden för Azure Cosmos DB

Etablerings modellen utan server för Azure Cosmos DB eliminerar behovet av att överetablera databas infrastrukturen. Azure Cosmos DB resurser tillhandahålls utan behov av specialiserade konfigurationer eller licenser. Det innebär att Azure Cosmos DB-program som stöds kan köras med så mycket som en 70 procents totala ägande kostnad jämfört med OSS NoSQL-databaser. Några exempel på real tids exempel finns i [kund användning – fall](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Andra fördelar med Azure Cosmos DB pris modellen är:

* **Bra värde för priset:** Marknads analytiker, kunder och partner har bekräftat ett större värde för alla funktioner som Azure Cosmos DB erbjuder ett mycket lägre pris jämfört med vad kunderna kan få när de implementerar dessa lösningar på egen hand eller via andra leverantörer. Databasen innehåller en sådan global distribution, flera region skrivningar, väldefinierade och intuitiva konsekvens modeller, automatisk indexering är avsevärt förenklad med Azure Cosmos DB utan komplexitet, omkostnader eller stillestånds tid.

* **Ingen NoSQL DevOps-administration krävs:** Med Azure Cosmos DB en behöver inte använda DevOps för att hantera distributioner, utföra underhåll, skalning eller korrigering. Du kan köra alla arbets belastningar som du skulle göra med OSS NoSQL-kluster som finns lokalt eller i moln infrastrukturen.

:::image type="content" source="./media/total-cost-ownership/tco.png" alt-text="Azure Cosmos DB ägande kostnad" border="false":::

* **Möjlighet att bygga ut elastiskt:** Azure Cosmos DB data flöde kan skalas upp och ned så att du kan minska ägande kostnaden under låg belastnings tider. OSS NoSQL-kluster som distribueras i moln infrastruktur erbjuder begränsad elastiskhet och lokala distributioner är inte elastiska av definition. I Azure Cosmos DB, om du etablerar mer data flöde, garanterar du att data flödet skalas linjärt. Den här säkerheten säkerhets kopie ras av finansiella service avtal och vid 99 percentilen i valfri skala.

* **Stor skala:** En hanterad tjänst som Azure Cosmos DB fungerar med ett stort antal noder som integreras internt med nätverk, lagring och beräkningar. På grund av Azure Cosmos DB stor standardisering kan du spara kostnaderna.

* **Optimerad för molnet:** Azure Cosmos DB har utformats från grunden med detaljerade flera innehavare och prestanda isolering. Detta möjliggör optimal placering, körning och balansering av tusentals klienter och deras arbets belastningar i kluster och data Center. Den aktuella generationen av OSS NoSQL-databaser fungerar däremot lokalt med hela den virtuella datorn och förutsätter att du kör en enskild klient arbets belastning. De här databaserna är inte heller utformade för att utnyttja moln leverantörens infrastruktur och maskin vara i hela utsträckning. Till exempel är en OSS NoSQL-databas motor inte medveten om skillnaderna mellan en virtuell dator och en rutins avbildnings uppgradering, eller det faktum att Premium disken redan är på väg att replikeras. Den kan inte dra nytta av dessa förmåner och de fördelar och besparingar som kunderna behöver.

* **Du betalar per timme:** För storskaliga arbets belastningar som behöver skalas när som helst debiteras du bara per timme. Arbets belastningarna i ett program varierar ofta över tiden på året och av de data som efter frågas. Med Azure Cosmos DB kan du skala upp eller ned efter behov och bara betala för det du behöver. Med lokala eller IaaS-värdbaserade system kan du inte matcha den här modellen eftersom det inte finns något sätt att inaktivera maskin varan varje timme. I sådana fall kan du eventuellt spara mellan 10 och 14 gånger i genomsnitt med Azure Cosmos DB.

* **Du får flera funktioner kostnads fritt:** I Azure Cosmos DB är Skriv arbets belastningar avsevärt billigare jämfört med alternativa databas tjänster. Dessutom erbjuder Azure Cosmos DB funktioner som till exempel [Automatisk indexering](index-policy.md), [Time to Live (TTL)](time-to-live.md), [ändrings flöde](change-feed.md) och andra utan ytterligare avgifter, något som annars debiteras av andra databas tjänster.

* **Använder enhetlig valuta för olika arbets belastningar:** Till skillnad från alternativa erbjudanden i Azure Cosmos DB behöver du inte segmentera arbets belastningar, till exempel i läsningar och skrivningar. Eller etablera data flöde på en typ av per arbets belastning som är läst genom strömning eller Skriv data flöde. I Azure Cosmos DB reserveras det etablerade data flödet med en enhetlig och normaliserad valuta i termer av enheter för programbegäran eller RU/SEK. Azure Cosmos DB tvingar dig inte att tilldela arbets belastningarna prioritet, utföra kapacitets planering eller betala för varje typ av kapacitet separat. Med den här metoden kan du enkelt växla samma RU/s mellan olika åtgärder och arbets belastnings typer.

* **Kräver inte etablering av virtuella datorer för skalning:** De flesta operativa databaser kräver att du går med stora virtuella datorer för att undvika problem med störningar och för att lösa resurs styrning om du vill skala. Detta innebär både bördan och det direkta åtagandet för kundernas kostnader. Med Azure Cosmos DB kan du starta små och stora mängder arbets belastnings storlekar sömlöst och utan drift avbrott eller påverkan på data tillgänglighet.

* **Du kan använda ett tillhandahållet data flöde för max gränsen:** Genom att använda subcore-multiplexering i Azure Cosmos DB kan du fylla det etablerade data flödet i större utsträckning än IaaS värdbaserade alternativ eller erbjudanden från tredje part. Den här metoden sparar mycket mer än de alternativa lösningarna.

* **Djup integrering av Azure Cosmos DB med andra Azure-tjänster.** Azure Cosmos DB har en inbyggd integrering med nätverk, Compute, Azure Functions (Server lös), Azure IoT och andra Azure-tjänster. Med den här integrationen får du bästa möjliga prestanda och snabba data replikering över hela världen med robusta garantier. Lösningar från tredje part kommer inte att kunna matcha eller skulle normalt debitera en Premium för att erbjuda sådana funktioner.

* **Du får automatiskt hög tillgänglighet, med minst 10-20 fel domäner som standard:** Azure Cosmos DB stöder distribution av arbets belastningar över fel domäner, en funktion som är kritisk för hög tillgänglighet. Den ger 99,999 hög tillgänglighet för läsningar och skrivningar i 99-percentilen över hela världen. Kostnaden för att implementera något som liknar detta på din egen eller via en lösning från tredje part är hög.

* **Du får automatiskt alla företags funktioner utan extra kostnad.** Azure Cosmos DB erbjuder den mest omfattande uppsättningen certifieringar för regelefterlevnad, säkerhet och kryptering i vila och i rörelse utan extra kostnad (jämfört med vår tävling). Du får automatiskt regional tillgänglighet var som helst i världen. Du kan sträcka databasen över valfritt antal Azure-regioner och lägga till eller ta bort regioner var som helst.

* **Du kan spara upp till 65% av kostnaderna med reserverad kapacitet:** Azure Cosmos DB [reserverad kapacitet](cosmos-db-reserved-capacity.md) hjälper dig att spara pengar genom förskotts betalning för Azure Cosmos DB resurser i ett år eller tre år. Du kan avsevärt minska dina kostnader med ett års-eller tre års åtagande och spara mellan 20-65% rabatt jämfört med den vanliga prissättningen. På verksamhets kritiska arbets belastningar kan du få bättre service avtal i förhållande till etablerings kapaciteten.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [hur Azure Cosmos DB prissättnings modell kostnads effektivt för kunder](total-cost-ownership.md)
* Läs mer om [optimering för utveckling och testning](optimize-dev-test.md)
* Läs mer om hur du [optimerar data flödes kostnaden](optimize-cost-throughput.md)
* Läs mer om hur du [optimerar lagrings kostnader](optimize-cost-storage.md)
* Läs mer om hur [du optimerar kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Lär dig mer om hur [du optimerar kostnaden för frågor](./optimize-cost-reads-writes.md)
* Lär dig mer om hur [du optimerar kostnaden för Cosmos-konton med flera regioner](optimize-cost-regions.md)
* Läs mer om [den totala kostnaden för (icke) ägarskap av en NoSQL Database-moln tjänst](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
