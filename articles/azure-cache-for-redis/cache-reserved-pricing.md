---
title: Förskotts beräkning med reserverad kapacitet – Azure cache för Redis
description: Förskottsbetala för Azure cache för Redis Compute-resurser med reserverad kapacitet
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 2f3472aa495042749410bc0b9635f0924a02e1fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98598560"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Förskottsbetala för Azure cache för Redis Compute-resurser med reserverad kapacitet

Azure cache för Redis hjälper dig nu att spara pengar genom att betala för beräknings resurser jämfört med priset för betala per användning. Med Azure cache för Redis reserverad kapacitet gör du ett förskott på cachen under en eller tre års period för att få en betydande rabatt på beräknings kostnaderna. Om du vill köpa Azure-cache för Redis reserverad kapacitet måste du ange Azure-regionen, tjänst nivån och perioden.

Du behöver inte tilldela reservationen till en speciell Azure-cache för Redis-instanser. En Azure-cache som redan körs för Redis eller som nyligen distribueras får automatiskt fördelarna med reserverad prissättning, upp till den reserverade cachestorleken. Genom att köpa en reservation betalar du för beräknings kostnaderna för en period på en eller tre år. Så snart du köper en reservation debiteras inte längre Azure-cachen för Redis-beräknings avgifter som matchar reservations attributen enligt priserna för betala per användning. En reservation omfattar inte nätverks-eller lagrings kostnader som är kopplade till cacheminnet. I slutet av reservations perioden upphör fakturerings förmånen och Azure-cachen för Redis debiteras enligt priset för betala per användning. Reservationer förnyas inte automatiskt. Information om priser finns i [Azure-cache för reserverat kapacitets erbjudande för Redis](https://azure.microsoft.com/pricing/details/cache).

Du kan köpa Azure cache för Redis reserverad kapacitet i [Azure Portal](https://portal.azure.com/). Så här köper du den reserverade kapaciteten:

* Du måste ha ägar rollen för minst ett företag eller en enskild prenumeration med priser enligt principen betala per användning.
* För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Eller, om inställningen är inaktive rad, måste du vara en EA-administratör i prenumerationen.
* För ett CSP-program (Cloud Solution Provider) kan endast administratörs agenter eller försäljnings agenter köpa Azure cache för reserverad Redis-kapacitet.

Information om hur företags kunder och kunder som betalar per användning debiteras för reservations köp finns i [förstå Azure reservation-användning för företagets registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) och [förstå Azure reservation-användning för din prenumeration enligt principen betala per](../cost-management-billing/reservations/understand-reserved-instance-usage.md)användning.


## <a name="determine-the-right-cache-size-before-purchase"></a>Fastställ storleken på rätt cachestorlek innan köpet

Reservationens storlek bör baseras på den totala mängden minnes storlek som används av den befintliga eller närmast distribuerade cachen inom en angiven region och med samma tjänst nivå.

Anta till exempel att du kör två cacheminnen – en till 13 GB och den andra vid 26 GB. Du behöver både i minst ett år. Dessutom antar vi att du planerar att skala de befintliga 13 GB cacheminnena till 26 GB under en månad för att uppfylla dina säsongs behov och sedan skala tillbaka. I det här fallet kan du köpa antingen 1 P2 cache och 1 P3 cache eller 3 P2 cacheminnen på ett engångs reservation för att maximera besparingarna. Du får rabatt på den totala mängden cache-minne som du reserverar, oberoende av hur detta belopp allokeras i cacheminnet.


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Köp Azure-cache för Redis reserverad kapacitet

Du kan köpa en reserverad virtuell dator instans i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/). Betala för reservationen [i förväg eller via månadsbetalningar](../cost-management-billing/reservations/prepare-buy-reservation.md).

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Alla tjänster** > **Reservationer**.
3. Välj **Lägg till** och sedan i fönstret köp reservationer väljer du **Azure cache för Redis** för att köpa en ny reservation för dina cacheminnen.
4. Fyll i de obligatoriska fälten. Befintliga eller nya databaser som matchar de attribut du väljer kvalificera för att hämta den reserverade kapacitets rabatten. Det faktiska antalet Azure-cache för Redis-instanser som erhåller rabatten beror på omfattning och kvantitet som valts.


![Översikt över reserverade priser](media/cache-reserved-pricing/cache-reserved-price.png)


I följande tabell beskrivs obligatoriska fält.

| Fält | Beskrivning |
| :------------ | :------- |
| Prenumeration   | Prenumerationen som används för att betala för Azure cache för Redis reserverad kapacitets reservation. Betalnings metoden för prenumerationen debiteras de direkta kostnaderna för Azure-cachen för Redis reserverad kapacitet. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller ett enskilt avtal med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). För företagsprenumerationer dras avgifterna från registreringens saldo för Azure-förskottsbetalning (kallades tidigare ekonomiskt åtagande) eller debiteras som överförbrukning. För en enskild prenumeration med priset betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.
| Omfång | Reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: </br></br> Reservations rabatten tillämpas på Azure cache för Redis-instanser som körs i alla prenumerationer i din fakturerings kontext. För företags kunder är det delade omfånget registreringen och innehåller alla prenumerationer i registreringen. För Betala per användning-kunder gäller den delade omfattningen samtliga Betala per användning-prenumerationer som kontoadministratören har skapat.</br></br> **Enskild prenumeration** tillämpas reservations rabatten på Azure cache för Redis-instanser i den här prenumerationen. </br></br> **En enda resurs grupp**, reservations rabatten tillämpas på Azure cache för Redis-instanser i den valda prenumerationen och den valda resurs gruppen i den prenumerationen.
| Region | Den Azure-region som omfattas av Azure-cachen för Redis reserverad kapacitets reservation.
| Prisnivå | Tjänst nivån för Azure cache för Redis-servrar.
| Period | Ett år eller tre år
| Kvantitet | Mängden data bearbetnings resurser som köps i Azure-cachen för Redis reserverad kapacitet reservation. Antalet är ett antal cacheminnen i den valda Azure-regionen och tjänst nivån som reserveras och kommer att få fakturerings rabatten. Om du till exempel kör eller planerar att köra en Azure-cache för Redis-servrar med den totala cache-kapaciteten på 26 GB i regionen USA, östra, anger du den kvantitet som ger dig motsvarande 26 GB för att maximera fördelarna med alla cacheminnen. Detta kan vara 1 P3 eller 2 P2 cacheminnen.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="cache-size-flexibility"></a>Storleks flexibilitet för cache

Storleks flexibiliteten i cacheminnet hjälper dig att skala upp eller ned i en tjänst nivå och region, utan att förlora den reserverade kapacitets förmånen.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Reservations rabatten tillämpas automatiskt på Azure-cachen för Redis-instanser som matchar reservations omfånget och attributen. Du kan uppdatera omfånget för reservationen via Azure Portal, PowerShell, Azure CLI eller API: et.

*  Information om hur reserverade kapacitets rabatter tillämpas på Azure cache för Redis finns i [förstå reservations rabatten i Azure](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* Mer information om Azure-reservationer finns i följande artiklar:

    * [Vad är Azure-reservationer?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    * [Hantera Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    * [Förstå reservationsrabatter i Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
    * [Förstå reservationsanvändning för din Betala per användning-prenumeration](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
    * [Förstå reservationsanvändning för din Enterprise-registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    * [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](/partner-center/azure-reservations)