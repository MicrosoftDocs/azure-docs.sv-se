---
title: Optimera kostnader för Azure Files med reserverad kapacitet
titleSuffix: Azure Files
description: Lär dig hur du sparar kostnader för distributioner av Azure-filresurser med hjälp av Azure Files reserverad kapacitet.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 918320cdb24442e551249e4e67d65e4ba85846c8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027663"
---
# <a name="optimize-costs-for-azure-files-with-reserved-capacity"></a>Optimera kostnader för Azure Files med reserverad kapacitet
Du kan spara pengar på lagrings kostnaderna för Azure-filresurser med kapacitets reservationer. Azure Files reserverad kapacitet ger rabatt på kapacitet för lagrings kostnader när du genomför en reservation för ett år eller tre år. En reservation tillhandahåller en fast mängd lagrings kapacitet för reservationens period.

Azure Files reserverad kapacitet kan avsevärt minska dina kapacitets kostnader för lagring av data i dina Azure-filresurser. Hur mycket du sparar beror på hur länge din reservation är, vilken total kapacitet du väljer att reservera och vilka inställningar för nivå och redundans som du har valt för Azure-filresurser. Reserverad kapacitet ger en fakturerings rabatt och påverkar inte statusen för dina Azure-filresurser.

För pris information om reservations kapacitet för Azure Files, se [Azure Files prissättning](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="reservation-terms-for-azure-files"></a>Reservations villkor för Azure Files
I följande avsnitt beskrivs villkoren i en Azure Files kapacitets reservation.

### <a name="reservation-capacity"></a>Reservations kapacitet
Du kan köpa Azure Files reserverad kapacitet i enheter om 10 TiB och 100 TiB per månad för ett år eller tre års period.

### <a name="reservation-scope"></a>Reservations omfång
Azure Files reserverad kapacitet är tillgänglig för en enskild prenumeration eller för flera prenumerationer (delad omfattning). När den är begränsad till en enda prenumeration tillämpas reservations rabatten endast på den valda prenumerationen. När det gäller flera prenumerationer delas reservations rabatten mellan dessa prenumerationer i kundens fakturerings kontext. En reservation gäller din användning inom det köpta omfånget och kan inte begränsas till ett visst lagrings konto, en behållare eller ett objekt i prenumerationen.

En kapacitets reservation för Azure Files omfattar bara den mängd data som lagras i en prenumeration eller delad resurs grupp. Avgifter för transaktioner, bandbredd och data överföring ingår inte i reservationen. Så snart du köper en reservation debiteras de kapacitets avgifter som matchar reservations-attributen enligt rabatten i stället för priserna för betala per användning. Mer information om Azure-reservationer finns i [Vad är Azure reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md).

### <a name="supported-tiers-and-redundancy-options"></a>Nivåer som stöds och alternativ för redundans
Azure Files reserverad kapacitet är bara tillgänglig för standard fil resurser som distribuerats i generell användnings version 2 lagrings konton (GPv2). Reserverad kapacitet är inte tillgänglig för Azure-filresurser i Premium-eller Transaction-optimerade nivåer.

För närvarande stöder endast Azure-filresurser på frekventa och låg frekventa nivåer reservationer. Alla stöd för lagrings redundans för reservationer. Mer information om redundans alternativ finns [Azure Files redundans](storage-files-planning.md#redundancy).

### <a name="security-requirements-for-purchase"></a>Säkerhets krav för köp
Så här köper du reserverad kapacitet:

- Du måste ha **ägar** rollen för minst ett företag eller en enskild prenumeration med priser enligt principen betala per användning.
- För företags prenumerationer måste **Lägg till reserverade instanser** vara aktiverade i EA-portalen. Eller, om inställningen är inaktive rad, måste du vara en EA-administratör i prenumerationen.
- För ett CSP-program (Cloud Solution Provider) kan endast administratörs agenter eller försäljnings agenter köpa Azure Files reserverad kapacitet.

## <a name="determine-required-capacity-before-purchase"></a>Fastställ nödvändig kapacitet innan köpet
När du köper en Azure Files reservation måste du välja alternativ för region, nivå och redundans för reservationen. Din reservation är endast giltig för data som lagras i regionen, nivå och redundans. Anta till exempel att du köper en reservation för data i västra USA för frekvent nivå med hjälp av Zone-redundant lagring (ZRS). Denna reservation gäller inte för data i östra USA, data på låg frekvent nivå eller data i Geo-redundant lagring (GRS). Du kan dock köpa ytterligare en reservation för dina ytterligare behov.  

Reservationer är tillgängliga för 10 TiB-eller 100 TiB-block, med högre rabatter för 100 TiB-block. När du köper en reservation i Azure Portal kan Microsoft ge dig rekommendationer baserat på din tidigare användning för att avgöra vilken reservation du bör köpa.

## <a name="purchase-azure-files-reserved-capacity"></a>Köp Azure Files reserverad kapacitet
Du kan köpa Azure Files reserverad kapacitet genom [Azure Portal](https://portal.azure.com). Betala för reservationen i förväg eller via månadsbetalningar. Mer information om hur du köper med månads betalningar finns i [köpa Azure-reservationer med fram-eller månads betalningar](../../cost-management-billing/reservations/prepare-buy-reservation.md).

Information om hur du identifierar de reservations villkor som passar ditt scenario finns i [förstå Azure Storage reserverad kapacitets rabatt](../../cost-management-billing/reservations/understand-storage-charges.md).

Följ dessa steg för att köpa reserverad kapacitet:

1. Gå till bladet med [inköps reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) i Azure Portal.  
1. Välj **Azure Files** för att köpa en ny reservation.  
1. Fyll i de obligatoriska fälten enligt beskrivningen i följande tabell:

    ![Skärm bild som visar hur du köper reserverad kapacitet](./media/files-reserve-capacity/select-reserved-capacity.png)

   |Fält  |Beskrivning  |
   |---------|---------|
   |**Omfång**   |  Anger hur många prenumerationer som kan använda den fakturerings förmån som är kopplad till reservationen. Den styr också hur reservationen tillämpas på vissa prenumerationer. <br/><br/> Om du väljer **delad** tillämpas reservations rabatten på Azure Files kapacitet i en prenumeration i din fakturerings kontext. Fakturerings kontexten baseras på hur du registrerade dig för Azure. För företags kunder är det delade omfånget registreringen och innehåller alla prenumerationer i registreringen. För kunder som betalar per användning inkluderar det delade omfånget alla enskilda prenumerationer med priser enligt principen betala per användning som har skapats av konto administratören.  <br/><br/>  Om du väljer **enskild prenumeration** tillämpas reservations rabatten på Azure Files kapacitet i den valda prenumerationen. <br/><br/> Om du väljer **enskild resurs grupp** tillämpas reservations rabatten på Azure Files kapacitet i den valda prenumerationen och den valda resurs gruppen i den prenumerationen. <br/><br/> Du kan ändra reservations omfånget när du har köpt reservationen.  |
   |**Prenumeration**  | Den prenumeration som används för att betala för Azure Files reservationen. Betalnings metoden för den valda prenumerationen används för att debitera kostnaderna. Prenumerationen måste vara någon av följande typer: <br/><br/>  Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P): för en företags prenumeration dras avgifterna från registreringens förskotts betalning i Azure (tidigare kallat betalnings åtagande) eller debiteras som överanvändning. <br/><br/> Individuell prenumeration med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P): för en enskild prenumeration med taxan betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.    |
   | **Region** | Den region där reservationen gäller. |
   | **Nivå** | Den nivå där reservationen gäller. Alternativen är *frekvent* och *låg* frekvent. |
   | **Redundans** | Alternativet redundans för reservationen. Alternativen är *LRS*, *ZRS*, *GRS* och *GZRS*. Mer information om redundans alternativ finns [Azure Files redundans](storage-files-planning.md#redundancy). |
   | **Fakturerings frekvens** | Anger hur ofta kontot debiteras för reservationen. Alternativen omfattar *varje månad* eller i *förskott*. |
   | **Storlek** | Mängden kapacitet som ska reserveras. |
   |**Period**  | Ett år eller tre år.   |

1. När du har valt parametrar för din reservation, visar Azure Portal kostnaden. Portalen visar också rabatt procenten för betalning per användning.

1. På bladet **köp bokningar** granskar du den totala kostnaden för reservationen. Du kan också ange ett namn på reservationen.

När du har köpt en reservation tillämpas den automatiskt på alla befintliga Azure-filresurser som matchar villkoren i reservationen. Om du inte har skapat några Azure-filresurser ännu kommer reservationen att gälla när du skapar en resurs som matchar villkoren i reservationen. I båda fallen börjar reservations perioden omedelbart efter ett lyckat köp.

## <a name="exchange-or-refund-a-reservation"></a>Exchange eller återbetala en reservation
Du kan byta ut eller återbetala en reservation med vissa begränsningar. Dessa begränsningar beskrivs i följande avsnitt.

Om du vill byta ut eller återbetala en reservation navigerar du till reservations informationen i Azure Portal. Välj **Exchange** eller **åter betalning** och följ anvisningarna för att skicka en support förfrågan. När begäran har bearbetats skickar Microsoft ett e-postmeddelande till dig för att bekräfta att begäran har slutförts.

Mer information om Azure Reservations-principer finns i [självbetjänings utbyte och åter betalningar för Azure reservations](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Utbyte av en reservation
Genom att byta en reservation kan du få en proportionell åter betalning som baseras på den outnyttjade delen av reservationen. Du kan sedan tillämpa åter betalningen på inköps priset för en ny Azure Files reservation.

Det finns ingen gräns för hur många byten du kan göra. Det finns dessutom ingen avgift kopplad till ett utbyte. Den nya reservationen som du köper måste vara lika med eller större än den proportionella krediten från den ursprungliga reservationen. En Azure Files reservation kan bara bytas ut för en annan Azure Files reservation och inte för en reservation för någon annan Azure-tjänst.

### <a name="refund-a-reservation"></a>Återbetala en reservation
Du kan avbryta en Azure Files reservation när som helst. När du avbryter får du en proportionell åter betalning som baseras på den återstående reservations perioden, minus en avgift för tidig uppsägning på 12 procent. Den högsta bidrags satsen per år är $50 000.

Om du avbryter en reservation avslutas omedelbart reservationen och resten av månaderna returneras till Microsoft. Det återstående proportionella saldot minus avgiften återbetalas till din ursprungliga köp form.

## <a name="expiration-of-a-reservation"></a>Förfallo datum för en reservation
När en reservation går ut debiteras Azure Files kapacitet som du använder under den reservationen enligt priset betala per användning. Reservationer förnyas inte automatiskt.

Du får ett e-postmeddelande 30 dagar innan reservationen upphör att gälla och sedan på förfallo datumet. Om du vill fortsätta att dra nytta av de kostnads besparingar som en reservation ger, förnyar du den inte senare än förfallo datumet.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss
Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Vad är Azure-reservationer?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Förstå hur reservations rabatter tillämpas på Azure Storage-tjänster](../../cost-management-billing/reservations/understand-storage-charges.md)