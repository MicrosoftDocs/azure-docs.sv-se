---
title: Förskottsbetala för program varu planer – Azure Reservations
description: Lär dig hur du kan betala för program varu planer för att spara pengar på dina kostnader för betala per användning.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: 5a4f7de0cacb17087e346123f53dad45ae1de357
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602615"
---
# <a name="prepay-for-azure-software-plans"></a>Förskottsbetala för programvaruplaner för Azure

När du förskottsbetaler för din program varu användning med SUSE och RedHat i Azure kan du spara pengar över dina kostnader enligt principen betala per användning. Rabatterna gäller endast SUSE-och RedHat-mätare och inte för användning av virtuella datorer. Du kan köpa reservationer för virtuella datorer separat för ytterligare besparingar.

Du kan köpa SUSE och RedHat Software-planer i Azure Portal. Så här köper du en plan:

- Du måste ha ägar rollen för minst ett företag eller en enskild prenumeration med priset betala per användning.
- För Enterprise-prenumerationer måste alternativet **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Om inställningen är inaktive rad måste du vara en EA-administratör för prenumerationen.
- För ett CSP-program (Cloud Solution Provider) kan administratörs agenter eller försäljnings agenter köpa program varu planerna.

## <a name="buy-a-software-plan"></a>Köp en program varu plan

1. Logga in på Azure Portal och gå till [reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Klicka på **Lägg till** och välj sedan den program varu plan som du vill köpa.
Fyll i de obligatoriska fälten. Alla virtuella SUSE Linux-datorer eller virtuella RedHat-datorer som matchar attributen för det du köper får rabatten. Det faktiska antalet distributioner som erhåller rabatten beror på vald omfattning och kvantitet.
3. Välj en prenumeration. Den används för att betala för planen.
Betalnings metoden för prenumerationen debiteras de direkta kostnaderna för reservationen. Prenumerations typen måste vara en Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller ett enskilt avtal med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P).
    - För en företags prenumeration dras avgifterna från registreringen för förskotts betalning i Azure (tidigare kallat betalnings åtagande) eller debiteras som överanvändning.
    - För en enskild prenumeration med priset betala per användning debiteras avgifterna på prenumerationens kredit kort eller faktura betalnings metod.
4. Välj ett omfång. Omfattningen kan omfatta en prenumeration eller flera prenumerationer (delad omfattning).
    - Enskild prenumeration – plan rabatten används för att matcha användning i prenumerationen.
    - Delad – plan rabatten tillämpas på matchande instanser i en prenumeration i din fakturerings kontext. För företags kunder är fakturerings kontexten registreringen och innehåller alla prenumerationer i registreringen. Fakturerings kontexten är alla enskilda planer med pris prenumerationer enligt principen betala per användning för enskilda abonnemang med pris uppgifter enligt principen betala per användning, som skapas av konto administratören.
    - Enskild resurs grupp – tillämpar reservations rabatten på de matchande resurserna enbart i den valda resurs gruppen.
5. Välj en produkt för att välja storlek på den virtuella datorn och avbildnings typen. Rabatten gäller endast för den valda virtuella dator storleken.
6. Välj ett år eller tre års period.
7. Välj en kvantitet, vilket är antalet förbetalda VM-instanser som kan få fakturerings rabatt.
8. Lägg till produkten i vagnen, granska och köp.

Reservations rabatten tillämpas automatiskt på den program varu mätare som du förbetalr för. Beräknings avgifter för virtuella datorer omfattas inte av planen. Du kan köpa VM-reservationer separat.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Rabatten gäller för olika virtuella SUSE VM-storlekar

Som reserverade VM-instanser erbjuder SUSE Linux-planer flexibilitet för instans storlek. Rabatten gäller även när du distribuerar en virtuell dator som är en annan storlek än den SUSE-plan som du köpte. Mer information finns i [förstå hur rabatten för program planen tillämpas](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>RedHat plan rabatt

Planer är bara tillgängliga för Red Hat Enterprise Linux virtuella datorer. Rabatten gäller inte för virtuella RedHat Enterprise Linux SAP HANA VM eller RedHat Enterprise Linux SAP Business Apps.

RedHat plan rabatter gäller endast för den VM-storlek som du väljer vid tidpunkten för köpet. RHEL-planer kan inte återbetalas eller bytas ut efter köpet.


## <a name="cancellation-and-exchanges-not-allowed"></a>Annullering och byten tillåts inte

Du kan inte avbryta eller byta ut en SUSE eller RedHat-plan som du har köpt. Kontrollera din användning för att försäkra dig om att du köper rätt plan. Information om hur du identifierar vad som ska köpas finns i [förstå hur rabatten för program planen tillämpas](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Information om hur du hanterar en reservation finns i [Hantera Azure-reservationer](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Mer information finns i följande artiklar:

- [Vad är Azure-reservationer?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Hantera reservationer i Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Förstå hur boknings rabatten för SUSE används](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
