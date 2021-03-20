---
title: Spara beräknings kostnader med reserverad kapacitet
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Lär dig hur du köper Azure SQL Database-och SQL-hanterad instans reserverad kapacitet för att spara pengar på dina beräknings kostnader.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 10/13/2020
ms.openlocfilehash: cacd43502a01352c24f8fcfd85b12aac781dccbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98602518"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>Spara kostnader för resurser med reserverad kapacitet – Azure SQL Database & SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

Spara pengar med Azure SQL Database-och SQL-hanterad instans genom att genomföra en reservation för beräknings resurser jämfört med priset enligt principen betala per användning. Med reserverad kapacitet gör du ett åtagande för SQL Database och/eller SQL-hanterad instans används under en period av en eller tre år för att få en betydande rabatt på beräknings kostnaderna. Om du vill köpa reserverad kapacitet måste du ange Azure-regionen, distributions typen, prestanda nivån och perioden.

Du behöver inte tilldela reservationen till en speciell databas eller en hanterad instans. Att matcha befintliga distributioner som redan körs eller som nyligen distribueras får förmånen automatiskt. Genom att köpa en reservation ska du allokera till användning för beräknings kostnaderna under en period på en eller tre år. Så snart du köper en reservation debiteras inte längre de beräknings kostnader som matchar reservations-attributen enligt priserna för betala per användning. 

En reservation gäller både primära och fakturerbara sekundär beräknings repliker, men omfattar inte program vara, nätverk eller lagrings kostnader som är associerade med tjänsten. I slutet av reservations perioden upphör fakturerings förmånen och databasen eller den hanterade instansen debiteras enligt priset betala per användning. Reservationer förnyas inte automatiskt. Information om priser finns i det [reserverade kapacitets erbjudandet](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Du kan köpa reserverad kapacitet i [Azure Portal](https://portal.azure.com). Betala för reservationen [i förväg eller via månadsbetalningar](../../cost-management-billing/reservations/prepare-buy-reservation.md). Så här köper du reserverad kapacitet:

- Du måste ha ägar rollen för minst ett företag eller en enskild prenumeration med priser enligt principen betala per användning.
- För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com). Eller, om inställningen är inaktive rad, måste du vara en EA-administratör i prenumerationen. Reserverad kapacitet.

Mer information om hur företags kunder och kunder som betalar per användning debiteras för reservations köp finns i [förstå Azure reservation-användning för företagets registrering](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) och [förstå Azure reservation-användning för din prenumeration enligt principen betala per](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)användning.

> [!NOTE]
> Köp reserverad kapacitet tar inte förallokera eller reservera vissa infrastruktur resurser (virtuella datorer eller noder) för användning.

## <a name="determine-correct-size-before-purchase"></a>Fastställ rätt storlek innan köpet

Reservationens storlek bör baseras på den totala mängd data som används av den befintliga eller närmast distribuerade databasen eller den hanterade instansen i en angiven region och använder samma prestanda nivå och maskin varu generation.

Anta till exempel att du kör ett allmänt syfte, Gen5 – 16 vCore elastisk pool och två affärs kritiska Gen5 – 4 vCore-databaser. Vi vill också att du planerar att distribuera inom en och samma månad ytterligare ett allmänt Gen5 – 16 vCore elastisk pool och en affärs kritisk Gen5 – 32 vCore elastisk pool. Vi antar också att du vet att du behöver dessa resurser i minst 1 år. I det här fallet bör du köpa en 32 (2x16) virtuella kärnor 1-års reservation för en enskild databas/elastisk pool generell användning – Gen5 och en 40 (2x4 + 32) vCore 1-års reservation för en enskild databas/elastisk pool affärs kritiskt-Gen5.

## <a name="buy-reserved-capacity"></a>Köp reserverad kapacitet

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** > **Reservationer**.
3. Välj **Lägg till** och sedan i fönstret **köp reservationer** väljer du **SQL Database** för att köpa en ny reservation för SQL Database.
4. Fyll i de obligatoriska fälten. Befintliga databaser i SQL Database-och SQL-hanterad instans som matchar de attribut som du väljer kvalificera för att hämta rabatten för reserverad kapacitet. Det faktiska antalet databaser eller hanterade instanser som erhåller rabatten beror på omfattning och vald kvantitet.

    ![Skärm bild innan du skickar det reserverade kapacitets köpet](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    I följande tabell beskrivs obligatoriska fält.
    
    | Fält      | Beskrivning|
    |------------|--------------|
    |Prenumeration|Prenumerationen som används för att betala för kapacitets reservationen. Betalnings metoden för prenumerationen debiteras för reservationens start kostnader. Prenumerations typen måste vara ett Enterprise Agreement (erbjudande nummer MS-AZR-0017P eller MS-AZR-0148P) eller ett enskilt avtal med priser enligt principen betala per användning (erbjudande nummer MS-AZR-0003P eller MS-AZR-0023P). För företagsprenumerationer dras avgifterna från registreringens saldo för Azure-förskottsbetalning (kallades tidigare ekonomiskt åtagande) eller debiteras som överförbrukning. För en enskild prenumeration med priset betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.|
    |Omfång       |VCore-reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer  <br/><br/>**Delat** används reservations rabatten för vCore på databasen eller den hanterade instans som körs i alla prenumerationer i din fakturerings kontext. För företags kunder är det delade omfånget registreringen och innehåller alla prenumerationer i registreringen. För Betala per användning-kunder gäller den delade omfattningen samtliga Betala per användning-prenumerationer som kontoadministratören har skapat.<br/><br/>**Enskild prenumeration** tillämpas reservations rabatten för vCore på databaserna eller hanterade instanser i den här prenumerationen. <br/><br/>**En enda resurs grupp**, reservations rabatten tillämpas på instanserna av databaser eller hanterade instanser i den valda prenumerationen och den valda resurs gruppen i den prenumerationen.|
    |Region      |Den Azure-region som omfattas av kapacitets reservationen.|
    |Distributions typ|Den SQL-resurs typ som du vill köpa reservationen för.|
    |Prestanda nivå|Tjänst nivån för databaserna eller hanterade instanser. |
    |Period        |Ett år eller tre år.|
    |Kvantitet    |Mängden data bearbetnings resurser som köps i kapacitets reservationen. Antalet är ett antal virtuella kärnor i den valda Azure-regionen och prestanda nivån som reserveras och kommer att få fakturerings rabatten. Om du till exempel kör eller planerar att köra flera databaser med den totala beräknings kapaciteten för Gen5 16 virtuella kärnor i regionen USA, östra, anger du kvantiteten som 16 för att maximera förmånen för alla databaser. |

1. Granska kostnaden för kapacitets reservationen i avsnittet **kostnader** .
1. Välj **Köp**.
1. Välj **Visa den här reservationen** för att se inköpets status.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>flexibilitet för vCore-storlek

vCore storleks flexibilitet hjälper dig att skala upp eller ned i en prestanda nivå och region, utan att förlora den reserverade kapacitets förmånen. Reserverad kapacitet ger också flexibiliteten att tillfälligt flytta dina aktiva databaser till och från elastiska pooler (inom samma region och prestanda nivå) som en del av din normala verksamhet utan att förlora reserverade kapacitets förmåner. Genom att upprätthålla en borttagen buffert i din reservation kan du effektivt hantera prestanda toppar utan att överskrida budgeten.

## <a name="limitation"></a>Begränsning

Det går inte att reservera DTU-baserade (Basic-, standard-eller Premium) databaser i SQL Database.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Reservations rabatten för vCore tillämpas automatiskt på antalet databaser eller hanterade instanser som matchar omfattningen och attributen för kapacitets reservationen. Du kan uppdatera omfånget för kapacitets reservationen via [Azure Portal](https://portal.azure.com), PowerShell, Azure CLI eller API: et.

Information om hur du hanterar kapacitets reservationen finns i [Hantera reserverad kapacitet](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Mer information om Azure-reservationer finns i följande artiklar:

- [Vad är Azure-reservationer?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Hantera Azure Reservations](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Förstå reservationsrabatter i Azure](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](/partner-center/azure-reservations)