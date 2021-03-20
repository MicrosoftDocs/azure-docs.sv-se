---
title: Reserverade beräknings priser – Azure Database for PostgreSQL-Scale Scale (citus)
description: Förskottsbetal för Azure Database for PostgreSQL-storskalig (citus) beräknings resurser med reserverad kapacitet.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 35d5d196eccb222bf17e0a129fe4e4041b1f6053
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600728"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Förskotts beräkning för Azure Database for PostgreSQL-storskalig (citus) Compute-resurser med reserverad kapacitet

Azure Database for PostgreSQL – storskalig (citus) hjälper dig nu att spara pengar genom att betala för beräknings resurser jämfört med priser enligt principen betala per användning. Med citus-reserverad kapacitet, gör du ett överplacerat åtagande på citus-servergruppen () för en eller tre års period för att få en betydande rabatt på beräknings kostnaderna. Om du vill köpa citus-reserverad kapacitet måste du ange Azure-regionen, reservations perioden och fakturerings frekvensen.

> [!IMPORTANT]
> Den här artikeln är om reserverad kapacitet för Azure Database for PostgreSQL – storskalig (citus). Information om reserverad kapacitet för Azure Database for PostgreSQL – enskild server finns i [Förskottsbetala för Azure Database for PostgreSQL – beräknings resurser för en enda server med reserverad kapacitet](./concept-reserved-pricing.md).

Du behöver inte tilldela reservation till vissa citus-Server grupper. En citus-servergrupp (redan körs) eller som nyligen distribueras får inte fördelarna med reserverad prissättning. Genom att köpa en reservation är det dags att betala för beräknings kostnaderna för ett år eller tre år. När du köper en reservation debiteras inte längre de citus-beräknings avgifter som matchar reservations attributen enligt priserna för betala per användning. 

En reservation avser inte program vara, nätverk eller lagrings kostnader som är associerade med citus-Server grupperna. I slutet av reservations perioden upphör fakturerings förmånen och citus-Server grupperna debiteras enligt priset för betala per användning. Reservationer förnyas inte. Information om priser finns i det [reserverade kapacitets erbjudandet Azure Database for PostgreSQL – citus)](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

Du kan köpa storskalig (citus) reserverad kapacitet i [Azure Portal](https://portal.azure.com/). Betala för reservationen [i förväg eller via månadsbetalningar](../cost-management-billing/reservations/prepare-buy-reservation.md). Så här köper du den reserverade kapaciteten:

* Du måste ha ägar rollen för minst en Enterprise-avtal (EA) eller en enskild prenumeration med priser enligt principen betala per användning.
* För Enterprise-avtal prenumerationer måste **Lägg till reserverade instanser** vara aktiverade i [EA-portalen](https://ea.azure.com/). Eller, om inställningen är inaktive rad, måste du vara Enterprise-avtal administratör för prenumerationen.
* För Cloud Solution Provider (CSP)-programmet kan endast administratörs agenter eller försäljnings agenter köpa citus-reserverad kapacitet.

Information om hur Enterprise-avtal kunder och kunder som betalar per användning debiteras för reservations köp, finns i:
- [Förstå Azure reservation-användning för din Enterprise-avtal-registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Förstå Azure reservation-användning för din betala per användning-prenumeration](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-server-group-size-before-purchase"></a>Fastställ rätt storlek på Server gruppen innan köpet

Reservations storleken baseras på den totala mängden beräkning som används av den befintliga eller närmast distribuerade koordinatorn och arbetsnoder i citus-servergrupper inom en angiven region.

Anta till exempel att du kör en citus-Server grupp med 16 vCore Coordinator och 3 8 vCore Worker-noder. Dessutom antar vi att du planerar att distribuera under nästa månad till en extra storskalig (citus)-Server grupp med 32 vCore-koordinatorer och 2 4 vCore Worker-arbetsnoder. Vi antar också att du behöver dessa resurser i minst ett år.

I det här fallet köper du en ett-års reservation för:

* Totalt 16 virtuella kärnor + 32 virtuella kärnor = 48 virtuella kärnor för koordinator-noder
* Totalt 3 noder x 8 virtuella kärnor + 2 noder x 4 virtuella kärnor = 24 + 8 = 32 virtuella kärnor för arbetsnoder

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Köp Azure Database for PostgreSQL reserverad kapacitet

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **Alla tjänster** > **Reservationer**.
1. Välj **Lägg till**. I fönstret **köp reservationer** väljer du **Azure Database for PostgreSQL** för att köpa en ny reservation för dina postgresql-databaser.
1. Välj den **storskaliga beräknings typen (citus)** för att köpa och klicka på **Välj**.
1. Granska kvantiteten för den valda beräknings typen på fliken **produkter** .
1. Fortsätt till fliken **köp + granska** för att slutföra köpet.

I följande tabell beskrivs obligatoriska fält.

| Fält        | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Prenumeration | Prenumerationen som används för att betala för den Azure Database for PostgreSQL reserverade kapacitets reservationen. Betalnings metoden för prenumerationen debiteras mot startkostnader för den Azure Database for PostgreSQL reserverade kapacitets reservationen. Prenumerations typen måste vara en Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller ett enskilt avtal med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). För en Enterprise-avtal-prenumeration dras avgifterna från registreringen för förskotts betalning i Azure (tidigare kallat betalnings åtagande) eller debiteras som överanvändning. För en enskild prenumeration med priset betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.                                                                                  |
| Omfång        | VCore-reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer **delad** tillämpas reservations rabatten för VCore på citus-Server grupper som körs i alla prenumerationer i din fakturerings kontext. För Enterprise-avtal-kunder är det delade omfånget registreringen och innehåller alla prenumerationer i registreringen. För kunder som betalar per användning är det delade omfånget alla prenumerationer enligt principen betala per användning som har skapats av konto administratören. Om du väljer **enskild prenumeration** tillämpas reservations rabatten för VCore på citus-Server grupper i den här prenumerationen. Om du väljer **enskild resurs grupp** tillämpas reservations rabatten på citus-Server grupper i den valda prenumerationen och den valda resurs gruppen i den prenumerationen. |
| Region       | Den Azure-region som omfattas av reservationen Azure Database for PostgreSQL – storskalig (citus) reserverad kapacitet.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Period         | Ett år eller tre år.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Kvantitet     | Mängden data bearbetnings resurser som köps i citus (reserverad kapacitet reservation). I synnerhet antalet koordinatorer eller arbetsnodens virtuella kärnor i den valda Azure-regionen som reserveras och som kommer att få fakturerings rabatten. Om du t. ex. kör (eller planerar att köra) citus-Server grupper med den totala beräknings kapaciteten för 64 koordinator Node virtuella kärnor och 32 Worker Node virtuella kärnor i regionen USA, östra, anger du kvantiteten som 64 och 32 för koordinator-och arbetsnoder för att maximera förmånen för alla servrar.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. För ytterligare information, se [självbetjänings utbyte och åter betalningar för Azure-reservationer](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>flexibilitet för vCore-storlek

vCore storleks flexibilitet hjälper dig att skala upp-eller ned-koordinatorn och arbetsnoder inom en region, utan att förlora den reserverade kapacitets förmånen.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Reservations rabatten för vCore tillämpas automatiskt på antalet citus-Server grupper som matchar Azure Database for PostgreSQL reserverade kapacitets omfånget och attributen. Du kan uppdatera omfånget för reserverad kapacitet för Azure Database for PostgreSQL – citus (Restore) genom Azure Portal, PowerShell, Azure CLI eller API: et.

Mer information om Azure-reservationer finns i följande artiklar:

* [Vad är Azure-reservationer?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Hantera Azure-reservationer](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Förstå reservations rabatten för Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Förstå reservations användningen för din prenumeration enligt principen betala per användning](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Förstå reservations användningen för din Enterprise-avtal-registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure-reservationer i Partner Center Cloud Solution Provider-program](/partner-center/azure-reservations)