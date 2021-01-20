---
title: Reserverad kapacitet i Azure Cosmos DB för att optimera kostnader
description: Lär dig hur du köper Azure Cosmos DB reserverad kapacitet för att spara pengar på dina beräknings kostnader.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 5c2d1d286572b21879742a1a9c6ab3975441373d
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602677"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Optimera kostnaden för reserverad kapacitet i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Reserverad Azure Cosmos DB-kapacitet hjälper dig att spara pengar genom att allokera till en reservation för Azure Cosmos DB-resurser i ett år eller tre år. Med reserverad Azure Cosmos DB-kapacitet kan du få rabatt på det dataflöde som tillhandahålls för Cosmos DB-resurser. Exempel på resurser är databaser och containrar (tabeller, samlingar och diagram).

Azure Cosmos DB reserverad kapacitet kan avsevärt minska dina Cosmos DBs kostnader &mdash; upp till 65 procent på vanliga priser med ett års-eller tre års åtagande. Reserverad kapacitet ger en fakturerings rabatt och påverkar inte körnings statusen för dina Azure Cosmos DB-resurser.

Azure Cosmos DB reserverad kapacitet täcker data flöde som har allokerats för dina resurser. Den omfattar inte avgifter för lagring och nätverk. Så snart du köper en reservation debiteras inte längre de data flödes kostnader som matchar reservations attributen enligt priserna för betala per användning. Mer information om reservationer finns i artikeln [Azure-reservationer](../cost-management-billing/reservations/save-compute-costs-reservations.md) .

Du kan köpa Azure Cosmos DB reserverad kapacitet från [Azure Portal](https://portal.azure.com). Betala för reservationen [i förväg eller via månadsbetalningar](../cost-management-billing/reservations/prepare-buy-reservation.md). Så här köper du reserverad kapacitet:

* Du måste ha ägar rollen för minst ett företag eller en enskild prenumeration med priser enligt principen betala per användning.  
* För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com). Eller, om inställningen är inaktive rad, måste du vara en EA-administratör i prenumerationen.
* För ett CSP-program (Cloud Solution Provider) kan endast administratörs agenter eller försäljnings agenter köpa Azure Cosmos DB reserverad kapacitet.

## <a name="determine-the-required-throughput-before-purchase"></a>Ta reda på vilket data flöde som krävs före köpet

Storleken på det reserverade kapacitets köpet bör baseras på den totala mängden data flöde som de befintliga eller närmast distribuerade Azure Cosmos DB resurserna kommer att använda per timme. Exempel: köpa 30 000 RU/s reserverad kapacitet om det är ett konsekvent användnings mönster per timme. I det här exemplet faktureras alla etablerade data flöden över 30 000 RU/s med din taxa för betala per användning. Om det etablerade data flödet är under 30 000 RU/s i en timme, kommer den extra reserverade kapaciteten för den timmen att tas ur drift.

Vi beräknar inköps rekommendationer baserat på ditt användnings mönster per timme. Användning under de senaste 7, 30 och 60 dagarna analyseras och reserverat kapacitets köp som maximerar besparingarna rekommenderas. Du kan visa rekommenderade reservations storlekar i Azure Portal med hjälp av följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).  

2. Välj **alla tjänster**-  >  **reservationer**  >  **Lägg till**.

3. I fönstret **inköps reservationer** väljer du **Azure Cosmos DB**.

4. Välj fliken **rekommenderas** för att Visa rekommenderade reservationer:

Du kan filtrera rekommendationerna efter följande attribut:

- **Period** (1 år eller 3 år)
- **Fakturerings frekvens** (månatlig eller uppifrån)
- **Data flödes typ** (ru/s vs multi-region Write ru/s)

Dessutom kan du begränsa rekommendationerna till en enda resurs grupp, en enda prenumeration eller hela din Azure-registrering. 

Här är en exempel rekommendation:

:::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png" alt-text="Rekommendationer för reserverad kapacitet":::

Den här rekommendationen att köpa en 30 000 RU/s-reservation anger att, mellan 3 års reservationer, en 30 000 RU/s reservations storlek kommer att maximera besparingarna. I det här fallet beräknas rekommendationen utifrån de senaste 30 dagarna efter Azure Cosmos DB användning. Om kunden förväntar sig att de senaste 30 dagarna av Azure Cosmos DB användning är representativa för framtida användning, skulle de kunna göra besparingar genom att köpa en 30 000 RU/s-reservation.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Köp Azure Cosmos DB reserverad kapacitet

1. Logga in på [Azure-portalen](https://portal.azure.com).  

2. Välj **alla tjänster**-  >  **reservationer**  >  **Lägg till**.  

3. I fönstret **inköps reservationer** väljer du **Azure Cosmos DB** för att köpa en ny reservation.  

4. Fyll i de obligatoriska fälten enligt beskrivningen i följande tabell:

   :::image type="content" source="./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png" alt-text="Fylla i formuläret för reserverad kapacitet":::

   |Fält  |Beskrivning  |
   |---------|---------|
   |Omfång   |   Alternativ som styr hur många prenumerationer som kan använda fakturerings förmånen som är associerad med reservationen. Den styr också hur reservationen tillämpas på vissa prenumerationer. <br/><br/>  Om du väljer **delad** tillämpas reservations rabatten på Azure Cosmos DB instanser som körs i en prenumeration i din fakturerings kontext. Fakturerings kontexten baseras på hur du registrerade dig för Azure. För företags kunder är det delade omfånget registreringen och innehåller alla prenumerationer i registreringen. För kunder som betalar per användning är det delade omfånget alla enskilda prenumerationer med priser enligt principen betala per användning som har skapats av konto administratören.  <br/><br/>  Om du väljer **enskild prenumeration** tillämpas reservations rabatten på Azure Cosmos DB instanser i den valda prenumerationen. <br/><br/> Om du väljer **enskild resurs grupp** tillämpas reservations rabatten på Azure Cosmos DB instanser i den valda prenumerationen och den valda resurs gruppen i den prenumerationen. <br/><br/> Du kan ändra reservations omfånget när du har köpt den reserverade kapaciteten.  |
   |Prenumeration  |   Prenumeration som används för att betala för Azure Cosmos DB reserverad kapacitet. Betalnings metoden för den valda prenumerationen används för att debitera kostnaderna. Prenumerationen måste vara någon av följande typer: <br/><br/>  Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P): för en företags prenumeration dras avgifterna från registreringens förskotts betalning i Azure (tidigare kallat betalnings åtagande) eller debiteras som överanvändning. <br/><br/> Individuell prenumeration med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P): för en enskild prenumeration med taxan betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.    |
   | Resursgrupp | Resurs grupp som den reserverade kapacitets rabatten tillämpas på. |
   |Term  |   Ett år eller tre år.   |
   |Data flödes typ   |  Data flödet tillhandahålls som enheter för programbegäran. Du kan köpa en reservation för det etablerade data flödet för båda konfigurationerna – enstaka region skrivningar och flera region skrivningar. Data flödes typen har två värden att välja mellan: 100 RU/s per timme och 100 flera regioner skrivningar RU/s per timme.|
   | Reserverade kapacitets enheter| Den mängd data flöde som du vill reservera. Du kan beräkna det här värdet genom att fastställa det data flöde som krävs för alla Cosmos DB resurser (till exempel databaser eller behållare) per region. Sedan multiplicerar du det med antalet regioner som du associerar med din Cosmos-databas. Exempel: om du har fem regioner med 1 000 000 RU/SEK i varje region väljer du 5 000 000 RU/SEK för reservations kapacitets köpet. |


5. När du har fyllt i formuläret beräknas det pris som krävs för att köpa den reserverade kapaciteten. I resultatet visas även procent andelen rabatt som du får med de valda alternativen. Klicka sedan på **Välj**

6. I fönstret **inköps reservationer** granskar du rabatten och priset för reservationen. Detta reservations pris gäller Azure Cosmos DB resurser med data flöde som har allokerats i alla regioner.  

   :::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png" alt-text="Reserverad kapacitets Sammanfattning":::

7. Välj **Granska + köp** och **Köp nu**. Följande sida visas när köpet har slutförts:

När du har köpt en reservation tillämpas den omedelbart på befintliga Azure Cosmos DB resurser som matchar villkoren i reservationen. Om du inte har några befintliga Azure Cosmos DB-resurser kommer reservationen att gälla när du distribuerar en ny Cosmos DB-instans som matchar villkoren i reservationen. I båda fallen startar reservations perioden direkt efter ett lyckat köp.

När reservationen går ut fortsätter dina Azure Cosmos DB instanser att köras och debiteras enligt de vanliga priserna för betala per användning.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Nästa steg

Reservations rabatten tillämpas automatiskt på Azure Cosmos DB resurser som matchar reservations omfånget och attributen. Du kan uppdatera omfånget för reservationen via Azure Portal, PowerShell, Azure CLI eller API: et.

*  Information om hur reserverade kapacitets rabatter tillämpas på Azure Cosmos DB finns i [förstå reservations rabatten i Azure](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md).

* Mer information om Azure-reservationer finns i följande artiklar:

   * [Vad är Azure-reservationer?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Hantera Azure-reservationer](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [Förstå reservationsanvändning för din Enterprise-registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [Förstå reservationsanvändning för din Betala per användning-prenumeration](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Azure-reservationer i Partner Center CSP-programmet](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).