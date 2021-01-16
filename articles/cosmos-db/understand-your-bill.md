---
title: Förstå din Azure Cosmos DB faktura
description: Den här artikeln förklarar hur du förstår din Azure Cosmos DB faktura med några exempel.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2020
ms.reviewer: sngun
ms.openlocfilehash: b0d27cb2f941db374a144cbd5c026444730e24e8
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247513"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Förstå Azure Cosmos DB-fakturan
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Som en fullständigt hanterad molnbaserad databas tjänst, Azure Cosmos DB fören klar faktureringen genom att endast debiteras för databas åtgärder och förbrukad lagring. Det finns inga ytterligare licens avgifter, kostnader för maskin vara, verktyg eller kostnader jämfört med lokala eller IaaS alternativ. När du funderar på funktionerna i flera regioner i Azure Cosmos DB ger databas tjänsten en betydande minskning av kostnaderna jämfört med befintliga lokala eller IaaS-lösningar.

- **Databas åtgärder**: hur du debiteras för dina databas åtgärder beror på vilken typ av Azure Cosmos-konto du använder.

  - **Etablerade data flöde**: du debiteras per timme för det högsta etablerade data flödet för en specifik timme, i steg om 100 ru/s.
  - Utan **Server**: du debiteras per timme för den totala mängden enheter för programbegäran som används av databas åtgärderna.

- **Lagring**: du debiteras ett fast pris för den totala mängden lagrings utrymme (i GB) som används av dina data och index för en specifik timme.

På [sidan med priser](https://azure.microsoft.com/pricing/details/cosmos-db/) finns den senaste pris informationen.

I den här artikeln använder vi några exempel som hjälper dig att förstå informationen på din månatliga faktura. De tal som visas i exemplen kan vara annorlunda om dina Azure Cosmos-containrar har ett annat etablerat dataflöde, om de sträcker sig över flera regioner eller körs under en annan period under månaden. Alla exempel i den här artikeln beräknar fakturan baserat på pris informationen som visas på sidan med [priser](https://azure.microsoft.com/pricing/details/cosmos-db/).

> [!NOTE]
> Faktureringen är en del av en timmes klock timme, inte en varaktighet på 60 minuter. Alla exempel som visas i det här dokumentet baseras på priset för ett Azure Cosmos-konto som distribueras i en icke-myndighets region i USA. Prissättningen och beräkningen varierar beroende på vilken region du använder, se [sidan Azure Cosmos DB prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/) för den senaste pris informationen.

## <a name="billing-examples"></a>Fakturerings exempel

### <a name="billing-example---provisioned-throughput-on-a-container-full-month"></a>Fakturerings exempel – tillhandahållet data flöde på en behållare (hela månaden)

* Vi antar att du konfigurerar ett data flöde på 1 000 RU/s på en behållare och att det finns i 24 timmar * 30 dagar för månad = 720 timmar totalt.  

* 1 000 RU/SEK är 10 enheter om 100 RU/SEK per timme för varje timme som behållare finns (dvs. 1000/100 = 10). 

* Multiplicera 10 enheter per timme med kostnaden $0,008 (per 100 RU/SEK per timme) = $0,08 per timme. 

* Om du multiplicerar $0,08 per timme med antalet timmar i månaden är det lika med $0,08 * 24 timmar * 30 dagar = $57,60 för månaden.  

* Den totala månads fakturan visar 7 200 enheter (av 100 ru: er) som kommer att kosta $57,60.

### <a name="billing-example---provisioned-throughput-on-a-container-partial-month"></a>Fakturerings exempel – tillhandahållet data flöde på en behållare (del månad)

* Vi antar att vi skapar en behållare med ett tillhandahållet data flöde på 2 500 RU/s. Behållaren bor i 24 timmar under månaden (till exempel tar vi bort den 24 timmarna efter att vi skapat den).  

* Sedan ser vi 600 enheter på fakturan (2 500 RU/SEK/100 RU/SEK/Unit * 24 timmar). Kostnaden blir $4,80 (600 enheter * $0.008/Unit).

* Den totala fakturan för månaden blir $4,80.

### <a name="billing-example---serverless-container"></a>Fakturerings exempel – Server lös behållare

* Vi antar att vi skapar en server lös behållare. 

* Under en månad utfärdar vi databas begär Anden som utnyttjar totalt 500 000 enheter för programbegäran. Kostnaden blir $0,125 (500 000 * $0,25/miljoner).

* Den totala fakturan för månaden blir $0,125.

### <a name="billing-rate-if-storage-size-changes"></a>Fakturerings taxa om lagrings storlek ändras

Lagrings kapaciteten faktureras i enheter för den maximala Tim mängden data som lagras, i GB, under en månatlig period. Om du till exempel använder 100 GB lagrings utrymme för hälften av månaden och 50 GB under andra hälften av månaden debiteras du för motsvarande 75 GB lagrings utrymme under den månaden.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Fakturerings takt när container eller en uppsättning behållare är aktiva i mindre än en timme

Du faktureras det fasta priset för varje timme som behållaren eller databasen finns, oavsett användning eller om behållaren eller databasen är aktiv i mindre än en timme. Om du till exempel skapar en behållare eller databas och tar bort den 5 minuter senare, kommer fakturan att innehålla en timme.

### <a name="billing-rate-when-provisioned-throughput-on-a-container-or-database-scales-updown"></a>Fakturerings takt när ett allokerat data flöde på en behållare eller databas skalas upp/ned

Om du ökar det etablerade data flödet kl. 9:30 från 400 RU/s till 1 000 RU/SEK och sedan sänker det etablerade data flödet vid 10:45 tillbaka till 400 RU/SEK debiteras du för två timmar 1 000 RU/SEK. 

Om du ökar det etablerade data flödet för en behållare eller en uppsättning behållare på 9:30 AM från 100-K RU/s till 200-K RU/SEK och sedan sänker det etablerade data flödet vid 10:45 tillbaka till 100-K RU/SEK debiteras du för två timmar efter 200 K RU/SEK.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Fakturerings exempel: flera behållare, var och en med dedikerat allokerat data flödes läge

* Om du skapar ett Azure Cosmos-konto i USA, östra 2 med två behållare med ett tillhandahållet data flöde på 500 RU/SEK respektive 700 RU/s, skulle du ha ett totalt allokerat data flöde på 1 200 RU/SEK.  

* Du skulle debiteras 1200/100 * $0,008 = $0.096/timme. 

* Om data flödet behöver ändras och du har ökat varje container kapacitet med 500 RU/s samtidigt som du skapar en ny obegränsad container med 20 000 RU/s, blir den totala etablerade kapaciteten 22 200 RU/SEK (1 000 RU/SEK + 1 200 RU/SEK + 20, 000 ru/SEK).  

* Fakturan skulle sedan ändras till: $0,008 x 222 = $1.776/timme. 

* I en månad med 720 timmar (24 timmar * 30 dagar), om 500 timmar etablerade data flöde var 1 200 RU/SEK och de återstående 220 timmar etablerade data flöden var 22 200 RU/SEK, visar din månads faktura: 500 x $0.096/timme + 220 x $1.776/timme = $438.72/månad.

:::image type="content" source="./media/understand-your-bill/bill-example1.png" alt-text="Exempel på dedikerad data flödes faktura":::

### <a name="billing-example-containers-with-shared-provisioned-throughput-mode"></a>Fakturerings exempel: behållare med delat (etablerad) data flödes läge

* Om du skapar ett Azure Cosmos-konto i USA, östra 2 med två Azure Cosmos-databaser (med en uppsättning behållare som delar data flödet på databas nivå) med det etablerade data flödet 50-K RU/SEK och 70-K RU/SEK, skulle du ha ett totalt allokerat data flöde på 120 K RU/SEK.  

* Du debiteras 1200 x $0,008 = $9.60/timme. 

* Om data flödet behöver ändras och du har ökat varje databas etablerade data flöde med 10 000 RU/s för varje databas, och du lägger till en ny behållare till den första databasen med dedikerat data flödes läge på 15-K RU/SEK till din delade data flödes databas blir den totala etablerade kapaciteten 155-K RU/SEK (60 K RU/SEK + 80 K RU/SEK + 15 K RU/SEK).  

* Fakturan skulle sedan ändras till: 1 550 * $0,008 = $12.40/timme.  

* I en månad om 720 timmar, om 300 timmar etablerade data flöde var 120-K RU/SEK och de återstående 420 timmar etablerade data flöden var 155-K RU/SEK, visas din månads faktura: 300 x $9.60/timme + 420 x $12.40/timme = $2 880 + $5 208 = $8088/månad. 

:::image type="content" source="./media/understand-your-bill/bill-example2.png" alt-text="Faktura exempel för delad data flöde":::

## <a name="billing-examples-with-geo-replication"></a>Fakturerings exempel med geo-replikering  

Du kan när som helst lägga till/ta bort Azure-regioner var som helst i världen till ditt Azure Cosmos Database-konto. Det data flöde som du har konfigurerat för olika Azure Cosmos-databaser och behållare kommer att reserveras i var och en av de Azure-regioner som är kopplade till ditt Azure Cosmos Database-konto. Om summan av det etablerade data flödet (RU/s) som kon figurer ATS över alla databaser och behållare i ditt Azure Cosmos Database-konto (etablerad per timme) är T och antalet Azure-regioner som är kopplade till ditt databas konto är N, är det totala etablerade data flödet för en viss timme för ditt Azure Cosmos Database-konto lika med T x N RU/SEK. Kostnader för etablerade data flöden (enkla Skriv åtgärder) $0.008/timme per 100 RU/SEK och tillhandahållet data flöde med flera skrivbara regioner (konfigurationer med flera regioner) $0.016/per timme per 100 RU/SEK (se sidan med [priser](https://azure.microsoft.com/pricing/details/cosmos-db/)). Oavsett om det är en enskild Skriv region eller flera Skriv regioner kan du med Azure Cosmos DB läsa data från vilken region som helst.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Fakturerings exempel: Azure Cosmos-konto med flera regioner, enskild region skrivningar

Vi antar att du har en Azure Cosmos-behållare i USA, västra. Behållaren skapas med data flöde 10 000 RU/s och du lagrar 1 TB data den här månaden. Vi antar att du lägger till tre regioner (östra USA, norra Europa och Asien, östra) till ditt Azure Cosmos-konto, var och en med samma lagrings utrymme och data flöde. Den totala månads fakturan kommer att vara (förutsatt 30 dagar i månaden). Fakturan skulle vara följande: 

|**Objekt** |**Användning (månad)** |**Hastighet** |**Månatlig kostnad** |
|---------|---------|---------|-------|
|Data flödes faktura för behållare i västra USA      | 10 000 RU/SEK * 24 * 30    |$0,008 per 100 RU/SEK per timme   |$576|
|Data flödes faktura för 3 ytterligare regioner – östra USA, norra Europa och Asien, östra       | 3 * 10 000 RU/SEK * 24 * 30    |$0,008 per 100 RU/SEK per timme  |$1 728|
|Lagrings faktura för behållare i västra USA      | 250 GB    |$0,25/GB  |$62,50|
|Lagrings faktura för 3 ytterligare regioner – östra USA, norra Europa och Asien, östra      | 3 * 250 GB    |$0,25/GB  |$187,50|
|**Totalt**     |     |  |**$2 554**|

*Vi antar också att du tar ut 100 GB data varje månad från behållaren i västra USA för att replikera data till östra USA, norra Europa och Asien, östra. Du debiteras för utgående trafik enligt priser för data överföring.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Fakturerings exempel: Azure Cosmos-konto med flera regioner, flera region skrivningar

Vi antar att du skapar en Azure Cosmos-behållare i USA, västra. Behållaren skapas med data flöde 10 000 RU/s och du lagrar 1 TB data den här månaden. Vi antar att du lägger till tre regioner (östra USA, norra Europa och Asien, östra), var och en med samma lagring och data flöde och du vill kunna skriva till behållarna i alla regioner som är kopplade till ditt Azure Cosmos-konto. Den totala månads fakturan kommer att vara (förutsatt 30 dagar i månaden) enligt följande:

|**Objekt** |**Användning (månad)**|**Hastighet** |**Månatlig kostnad** |
|---------|---------|---------|-------|
|Data flödes faktura för container i USA, västra (alla regioner är skrivbara)       | 10 000 RU/SEK * 24 * 30    |$0,016 per 100 RU/SEK per timme    |$1 152 |
|Data flödes faktura för 3 ytterligare regioner – östra USA, norra Europa och Asien, östra (alla regioner är skrivbara)        | 3 * 10 000 RU/SEK * 24 * 30    |$0,016 per 100 RU/SEK per timme   |$3 456 |
|Lagrings faktura för behållare i västra USA      | 250 GB    |$0,25/GB  |$62,50|
|Lagrings faktura för 3 ytterligare regioner – östra USA, norra Europa och Asien, östra      | 3 * 250 GB    |$0,25/GB  |$187,50|
|**Totalt**     |     |  |**$6 010**|

*Vi antar också att du tar ut 100 GB data varje månad från behållaren i västra USA för att replikera data till östra USA, norra Europa och Asien, östra. Du debiteras för utgående trafik enligt priser för data överföring.*

### <a name="billing-example-azure-cosmos-account-with-multi-region-writes-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Fakturerings exempel: Azure Cosmos-konto med flera region skrivningar, data flöde på databas nivå, inklusive dedikerat data flödes läge för vissa behållare

Vi ska tänka på följande exempel, där vi har ett Azure Cosmos-konto med flera regioner där alla regioner är skrivbara (flera Write region config). För enkelhetens skull kommer vi att se till att lagrings storleken förblir konstant och inte ändras och utelämnar den här så att exemplet blir enklare. Det tillhandahållna data flödet under månaden varierar enligt följande (förutsatt 30 dagar eller 720 timmar): 

[0-100 timmar]:  

* Vi har skapat ett Azure Cosmos-konto i tre regioner (västra USA, östra USA, norra Europa), där alla regioner är skrivbara 

* Vi har skapat en databas (D1) med delat data flöde 10K RU/SEK 

* Vi har skapat en databas (D2) med delat data flöde 30-K RU/SEK och  

* Vi har skapat en behållare (C1) med dedikerat data flöde 20 K RU/SEK 

[101-200 timmar]:  

* Vi skalade upp databasen (D1) till 50 K RU/SEK 

* Vi skalade databasen (D2) till 70 000 RU/SEK  

* Behållaren har tagits bort (C1)  

[201-300 timmar]:  

* Vi skapade container (C1) igen med dedikerat data flöde 20 K RU/SEK 

[301-400 timmar]:  

* Vi har tagit bort en av regionerna från Azure Cosmos-kontot (antalet skrivbara regioner är nu 2) 

* Vi skalade ned databasen (D1) till 10 000 RU/SEK 

* Vi skalade databasen (D2) till 80 000 RU/SEK  

* Vi har tagit bort containern (C1) igen 

[401-500 timmar]:  

* Vi skalade ned databasen (D2) till 10 000 RU/SEK  

* Vi skapade container (C1) igen med dedikerat data flöde 20 K RU/SEK 

[501-700 timmar]:  

* Vi skalade databasen (D1) till 20 K RU/SEK  

* Vi skalade databasen (D2) till 100 000 RU/SEK  

* Vi har tagit bort containern (C1) igen  

[701-720 timmar]:  

* Vi skalade ned databasen (D2) till 50 K RU/SEK  

Ändringar i det totala etablerade data flödet under 720 timmar för månaden visas visuellt i bilden nedan: 

:::image type="content" source="./media/understand-your-bill/bill-example3.png" alt-text="Exempel på real tid":::

Den totala månads fakturan blir (förutsatt att 30 dagar/720 timmar per månad) beräknas enligt följande:

|**Hours**  |**RU/s** |**Objekt** |**Användning (varje timme)** |**Kostnad** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10 000 <br/>D2:30 000 <br/>C1:20 000 |Data flödes faktura för container i USA, västra (alla regioner är skrivbara)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Data flödes faktura för 2 ytterligare regioner: östra USA, norra Europa (alla regioner är skrivbara)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2 880  |
|[101-200] |D1:50 000 <br/>D2:70K <br/>C1:-- |Data flödes faktura för container i USA, västra (alla regioner är skrivbara)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |Data flödes faktura för 2 ytterligare regioner: östra USA, norra Europa (alla regioner är skrivbara)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5 760  |
|[201-300]  |D1:50 000 <br/>D2:70K <br/>C1:20 000 |Data flödes faktura för container i USA, västra (alla regioner är skrivbara)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2 240  |
| | |Data flödes faktura för 2 ytterligare regioner: östra USA, norra Europa (alla regioner är skrivbara)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6 720 |
|[301-400] |D1:10 000 <br/>D2:80K <br/>C1:-- |Data flödes faktura för container i USA, västra (alla regioner är skrivbara)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$1 440   |
| | |Data flödes faktura för 2 ytterligare regioner: östra USA, norra Europa (alla regioner är skrivbara)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2 880  |
|[401-500] |D1:10 000 <br>D2:10 000 <br>C1:20 000 |Data flödes faktura för container i USA, västra (alla regioner är skrivbara)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Data flödes faktura för 2 ytterligare regioner: östra USA, norra Europa (alla regioner är skrivbara)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1 280  |
|[501-700] |D1:20 000 <br>D2:100 000 <br>C1:-- |Data flödes faktura för container i USA, västra (alla regioner är skrivbara)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3 840  |
| | |Data flödes faktura för 2 ytterligare regioner: östra USA, norra Europa (alla regioner är skrivbara)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7 680  |
|[701-720] |D1:20 000 <br/>D2:50 000 <br/>C1:-- |Data flödes faktura för container i USA, västra (alla regioner är skrivbara)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Data flödes faktura för 2 ytterligare regioner: östra USA, norra Europa (alla regioner är skrivbara)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Total månads kostnad**  | |**$38 688**   |

## <a name="billing-examples-with-azure-cosmos-db-free-tier-accounts"></a><a id="azure-free-tier"></a>Fakturerings exempel med Azure Cosmos DB kostnads fria nivå konton

Med Azure Cosmos DB kostnads fri nivå får du de första 400 RU/s och 5 GB lagrings utrymme i ditt konto kostnads fritt, som tillämpas på konto nivå. RU/s och lagring utöver 400 RU/s och 5 GB debiteras enligt pris sidans priser. På fakturan visas ingen avgift eller ett rad objekt för de kostnads fria 400 ru/s-och 5 GB, bara RU/s och lagring utöver vad som omfattas av den kostnads fria nivån. 400 RU/s gäller för alla typer av RU/s-etablerade data flöden, autoskalning och flera regioner.  

> [!NOTE]
> Azure Cosmos DB kostnads fri nivå skiljer sig från det kostnads fria Azure-kontot. Azures kostnads fria konto erbjuder Azure-krediter och-resurser kostnads fritt under en begränsad tid. När du använder Azure Cosmos DB som en del av det här kostnads fria kontot får du 25 GB lagring och 400 RU/s med etablerade data flöde i 12 månader.

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Fakturerings exempel – behållare eller databas med etablerad data flöde
- Vi antar att vi skapar en databas eller behållare på ett kostnads fritt konto med 400 RU/s och 5 GB lagrings utrymme.
- Din faktura kommer inte att visa någon avgift för den här resursen. Din timkostnad och månads kostnad blir $0.
- Nu ska vi anta att du lägger till en annan databas eller behållare med 1000 RU/s och 10 GB lagrings utrymme i samma konto.
- Din faktura kommer nu att visa en avgift för 1000 RU/s och 10 GB lagrings utrymme. 

### <a name="billing-example---container-with-autoscale-throughput"></a>Fakturerings exempel – behållare med autoskalning av data flöde
- Låt oss anta att du på ett kostnads fritt konto skapar en behållare med automatisk skalning aktiverat, med en maximal RU/s på 4000 RU/s. Den här resursen kommer automatiskt att skalas mellan 400 RU/s-4000 RU/s. 
- Anta att det är minst 400 RU/s att resursen är i timmen 1 till timme 10. Under timme 11 skalas resursen upp till 1000 RU/s och sedan tillbaka till 400 RU/s inom timmen.
- I timmar 1 till och med 10 faktureras du $0 för data flödet, eftersom 400 RU/s har täckts av den kostnads fria nivån. 
- I timmar 11 faktureras du för en effektiv 1000 RU/s-400 RU/s = 600 RU/s, eftersom det här är den högsta RU/s i timmen. Detta är 6 enheter om 100 RU/s för timmen, så den totala data flödes kostnaden för timmen är 6 enheter * $0,012 = $0,072. 
- Lagrings utrymme utöver de första 5 GB faktureras enligt normala lagrings kostnader. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Fakturerings exempel – konto för en Skriv region för flera regioner
- Låt oss anta att du skapar en databas eller behållare med 1200 RU/s och 10 GB lagrings utrymme i ett kostnads fritt konto. Vi replikerar kontot till tre regioner och vi har ett enda Skriv regions konto.
- Som totalt, utan kostnads fri nivå, faktureras vi för 3 * 1200 RU/s = 3600 RU/s och 3 * 10 GB = 30 GB lagrings utrymme.
- Med den kostnads fria nivån rabatt, efter att ha tagit bort 400 RU/s och 5 GB lagring, kommer vi att faktureras för en effektiv 3200 RU/s (32-enheter) av ett allokerat data flöde med en enkel Skriv regions taxa och 25 GB lagrings utrymme.
- Månads kostnaden för RU/s blir: 32 enheter * $0,008 * 24 timmar * 31 dagar = $190,46. Den månatliga kostnaden för lagring blir: 25 GB * 0,25/GB = $6,25. Den totala kostnaden blir $190,46 + $6,25 = $196,71.
- OBS! om a-priset för RU/s eller lagring skiljer sig i regionerna, kommer den kostnads fria nivån 400 RU/s och 5 GB att återspegla de regioner som kontot skapades i.

### <a name="billing-example---multi-region-account-with-multiple-write-regions"></a>Fakturerings exempel – flera regioner, konto med flera Skriv regioner

Det här exemplet visar [priser för skrivningar i flera regioner](https://azure.microsoft.com/pricing/details/cosmos-db/) för konton som skapats efter den 1 december 2019. 
- Låt oss anta att du skapar en databas eller behållare med 1200 RU/s och 10 GB lagrings utrymme i ett kostnads fritt konto. Vi replikerar kontot till tre regioner och vi har ett konto för flera Skriv regioner. 
- Som totalt, utan kostnads fri nivå, faktureras vi för 3 * 1200 RU/s = 3600 RU/s och 3 * 10 GB = 30 GB lagrings utrymme.
- Med den kostnads fria nivån rabatt, efter att ha tagit bort 400 RU/s och 5 GB lagring, kommer vi att faktureras för en effektiv 3200 RU/s (32-enheter) med ett allokerat data flöde med en hastighet på flera Skriv regioner och 25 GB lagrings utrymme.
- Månads kostnaden för RU/s blir: 32 enheter * $0,016 * 24 timmar * 31 dagar = $380,93. Den månatliga kostnaden för lagring blir: 25 GB * 0,25/GB = $6,25. Den totala kostnaden blir $380,93 + $6,25 = $387,18.

## <a name="proactively-estimating-your-monthly-bill"></a>Proaktivt uppskatta din månads faktura  

Nu ska vi tänka på ett annat exempel, där du vill proaktivt uppskatta din faktura innan månadens slut. Du kan beräkna din faktura på följande sätt:

**Lagringskostnad**

* Genomsnittlig post storlek (KB) = 1 
* Antal poster = 100 000 000 
* Totalt lagrings utrymme (GB) = 100 
* Månatlig kostnad per GB = $0,25 
* Förväntad månatlig kostnad för lagring = $25,00 

**Data flödes kostnad**

|Åtgärdstyp| Begär Anden per sekund| Gmsn. RU/begäran| Ru: er krävs|
|----|----|----|----|
|Skriva| 100 | 5 | 500|
|Läs| 400| 1| 400|

Totalt RU/SEK: 500 + 400 = 900 timkostnad: 900/100 * $0,008 = $0,072 förväntad månads kostnad för data flöde (förutsatt att 31 dagar har upprättats): $0,072 * 24 * 31 = $53,57

**Total månads kostnad**

Total månads kostnad = månatlig kostnad för lagring + månatlig kostnad för data flöde total månads kostnad = $25,00 + $53,57 = $78,57

*Prissättningen kan variera beroende på region. Information om priser finns på [sidan med priser](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Fakturering med Azure Cosmos DB reserverad kapacitet

Med Azure Cosmos DB reserverad kapacitet kan du köpa etablerade data flöden i förväg (en reserverad kapacitet eller en reservation) som kan tillämpas på alla Azure Cosmos-databaser och behållare (för alla API: er eller data modeller) i alla Azure-regioner. Eftersom det etablerade data flödes priset varierar mellan olika regioner, bidrar det till att betrakta reserverad kapacitet som en monetär kredit som du har köpt till en rabatt, som kan hämtas från för det etablerade data flödet till respektive pris i varje region. Anta till exempel att du har ett Azure Cosmos-konto med en enda behållare etablerad med 50-K RU/SEK och globalt replikerade två regioner – östra USA och Japan, öst. Om du väljer alternativet betala per användning betalar du:  

* i östra USA: för 50-K RU/SEK enligt priset $0,008 per 100 RU/SEK i den regionen 

* i Japan, östra: för 50-K RU/SEK enligt priset $0,009 per 100 RU/SEK i regionen

Din totala faktura (utan reserverad kapacitet) blir (förutsatt 30 dagar eller 720 timmar): 

|**Region**| **Tim pris per 100 RU/s**|**Enheter (RU/s)**|**Fakturerat belopp (varje timme)**| **Fakturerat belopp (månatligt)**|
|----|----|----|----|----|
|East US|$0,008 |50 kB|$4|$2 880 |
|Japan, östra|$0,009 |50 kB| $4,50 |$3 240 |
|Totalt|||$8,50|$6 120 |

Vi rekommenderar att du har köpt reserverad kapacitet i stället. Du kan köpa reserverad kapacitet för 100-K RU/SEK till priset $56 064 i ett år (med 20% rabatt) eller $6,40 per timme. Se priser för reserverad kapacitet på [sidan prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Kostnaden för data flöde (betala per användning): 100 000 RU/SEK/100 * $0.008/timme * 8760 timmar på ett år = $70 080 

* Kostnaden för data flödet (med reserverad kapacitet) $70 080 rabatt med 20% = $56 064 

Det du har köpt på ett effektivt sätt är en kredit på $8 per timme, för 100 K RU/SEK med list priset i USA, östra till priset för $6,40 per timme. Du kan sedan rita ned från den här förbetalda data flödes reservationen per timme för den etablerade data flödes kapaciteten i alla globala Azure-regioner i de respektive regionala List priserna som angetts för din prenumeration. I det här exemplet, där du etablerar 50 K RU/SEK var och en i östra USA och Östra Japan, kommer du att kunna rita $8,00 värd för ett etablerat data flöde per timme och debiteras över $0,50 per timme (eller $360/månad). 

|**Region**| **Tim pris per 100 RU/s**|**Enheter (RU/s)**| **Fakturerat belopp (varje timme)**| **Fakturerat belopp (månatligt)**|
|----|----|----|----|----|
|East US|$0,008 |50 kB|$4|$2 880 |
|Japan, östra|$0,009 |50 kB| $4,50 |$3 240 |
|||Betala per användning|$8,50|$6120|
|Köpt reserverad kapacitet|$0,0064 (20% rabatt) |100 RU/SEK eller $8 kapacitet för inköpt |– $8|– $5 760 |
|Netto faktura|||0,50 USD |$360 |

## <a name="next-steps"></a>Nästa steg

Härnäst kan du fortsätta med att lära dig mer om kostnads optimering i Azure Cosmos DB med följande artiklar:

* Läs mer om [hur Cosmos DB prissättnings modell kostnads effektivt för kunder](total-cost-ownership.md)
* Läs mer om [optimering för utveckling och testning](optimize-dev-test.md)
* Läs mer om hur du [optimerar data flödes kostnaden](optimize-cost-throughput.md)
* Läs mer om hur du [optimerar lagrings kostnader](optimize-cost-storage.md)
* Läs mer om hur [du optimerar kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Lär dig mer om hur [du optimerar kostnaden för frågor](./optimize-cost-reads-writes.md)
* Läs mer om hur [du optimerar kostnaden för Azure Cosmos-konton med flera regioner](optimize-cost-regions.md)
