---
title: Optimera kostnader för blobblagring med reserverad kapacitet
titleSuffix: Azure Storage
description: Läs mer om att köpa Azure Storage reserverad kapacitet för att spara kostnader för Block-Blob och Azure Data Lake Storage Gen2 resurser.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6fd791495602846b95e2dd3e99423db5505f5307
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600911"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Optimera kostnader för blobblagring med reserverad kapacitet

Du kan spara pengar på lagrings kostnader för BLOB-data med Azure Storage reserverad kapacitet. Azure Storage reserverad kapacitet ger dig rabatt på kapacitet för block-blobbar och för Azure Data Lake Storage Gen2 data i standard lagrings konton när du genomför en reservation för ett år eller tre år. En reservation tillhandahåller en fast mängd lagrings kapacitet för reservationens period.

Azure Storage reserverad kapacitet kan avsevärt minska kapacitets kostnaderna för block-blobbar och Azure Data Lake Storage Gen2 data. De kostnads besparingar som uppnås beror på hur länge din reservation är, den totala kapaciteten du väljer att reservera och åtkomst nivån och typen av redundans som du har valt för ditt lagrings konto. Reserverad kapacitet ger en fakturerings rabatt som inte påverkar Azure Storage resursernas tillstånd.

Information om Azure Storage-reservationspriser finns i [Prissättning för blockblobar](https://azure.microsoft.com/pricing/details/storage/blobs/) och [Azure Data Lake Storage Gen 2-prissättning](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Reservations villkor för Azure Storage

I följande avsnitt beskrivs villkoren i en Azure Storage reservation.

### <a name="reservation-capacity"></a>Reservations kapacitet

Du kan köpa Azure Storage reserverad kapacitet i enheter om 100 TiB och 1 PiB per månad för ett år eller tre års period.

### <a name="reservation-scope"></a>Reservations omfång

Azure Storage reserverad kapacitet är tillgänglig för en enskild prenumeration eller för flera prenumerationer (delad omfattning). När den är begränsad till en enda prenumeration tillämpas reservations rabatten endast på den valda prenumerationen. När det gäller flera prenumerationer delas reservations rabatten mellan dessa prenumerationer i kundens fakturerings kontext.

När du köper Azure Storage reserverad kapacitet kan du använda din reservation för både Block-Blob och Azure Data Lake Storage Gen2 data. En reservation tillämpas på din användning inom det köpta omfånget och kan inte begränsas till ett visst lagrings konto, behållare eller objekt i prenumerationen.

En Azure Storage-reservation täcker bara den mängd data som lagras i en prenumeration eller delad resurs grupp. Avgifter för tidig borttagning, åtgärder, bandbredd och data överföring ingår inte i reservationen. Så snart du köper en reservation debiteras de kapacitets kostnader som matchar reservations-attributen enligt rabatten i stället för enligt priserna för betala per användning. Mer information om Azure-reservationer finns i [Vad är Azure reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md).

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Konto typer, nivåer och redundans alternativ som stöds

Azure Storage reserverad kapacitet är tillgänglig för resurser i standard lagrings konton, inklusive General-Purpose v2 (GPv2) och Blob Storage-konton.

Alla åtkomst nivåer (frekvent, låg frekvent och arkivering) stöds för reservationer. Mer information om åtkomst nivåer finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](storage-blob-storage-tiers.md)lag rings nivåer.

Alla typer av redundans stöds för reservationer. Mer information om redundans alternativ finns [Azure Storage redundans](../common/storage-redundancy.md).

> [!NOTE]
> Azure Storage reserverad kapacitet är inte tillgänglig för Premium Storage-konton, General-Purpose v1-lagring (GPv1), Azure Data Lake Storage Gen1, Page blobbar, Azure Queue Storage, Azure Table Storage eller Azure Files.  

### <a name="security-requirements-for-purchase"></a>Säkerhets krav för köp

Så här köper du reserverad kapacitet:

- Du måste ha **ägar** rollen för minst ett företag eller en enskild prenumeration med priser enligt principen betala per användning.
- För företags prenumerationer måste **Lägg till reserverade instanser** vara aktiverade i EA-portalen. Eller, om inställningen är inaktive rad, måste du vara en EA-administratör i prenumerationen.
- För ett CSP-program (Cloud Solution Provider) kan endast administratörs agenter eller försäljnings agenter köpa Azure Blob Storage reserverad kapacitet.

## <a name="determine-required-capacity-before-purchase"></a>Fastställ nödvändig kapacitet innan köpet

När du köper en Azure Storage reservation måste du välja region, åtkomst nivå och alternativ för redundans för reservationen. Din reservation är bara giltig för data som lagras i den regionen, åtkomst nivån och redundans nivån. Anta till exempel att du köper en reservation för data i västra USA för frekvent nivå med hjälp av Zone-redundant lagring (ZRS). Du kan inte använda samma reservation för data i östra USA, data på Arkiv nivå eller data i Geo-redundant lagring (GRS). Du kan dock köpa ytterligare en reservation för dina ytterligare behov.  

Reservationer är tillgängliga idag för 100 TiB-eller 1 PiB-block, med högre rabatter för 1 PiB-block. När du köper en reservation i Azure Portal kan Microsoft ge dig rekommendationer baserat på din tidigare användning för att avgöra vilken reservation du bör köpa.

## <a name="purchase-azure-storage-reserved-capacity"></a>Köp Azure Storage reserverad kapacitet

Du kan köpa Azure Storage reserverad kapacitet genom [Azure Portal](https://portal.azure.com). Betala för reservationen i förväg eller via månadsbetalningar. Mer information om hur du köper med månads betalningar finns i [köpa Azure-reservationer med fram-eller månads betalningar](../../cost-management-billing/reservations/prepare-buy-reservation.md).

Information om hur du identifierar de reservations villkor som passar ditt scenario finns i [förstå Azure Storage reserverad kapacitet rabatt](../../cost-management-billing/reservations/understand-storage-charges.md).

Följ dessa steg för att köpa reserverad kapacitet:

1. Gå till fönstret [köp reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) i Azure Portal.  
1. Välj **Azure-Blob Storage** för att köpa en ny reservation.  
1. Fyll i de obligatoriska fälten enligt beskrivningen i följande tabell:

    ![Skärm bild som visar hur du köper reserverad kapacitet](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Fält  |Beskrivning  |
   |---------|---------|
   |**Omfång**   |  Anger hur många prenumerationer som kan använda den fakturerings förmån som är kopplad till reservationen. Den styr också hur reservationen tillämpas på vissa prenumerationer. <br/><br/> Om du väljer **delad** tillämpas reservations rabatten på Azure Storage kapacitet i en prenumeration i din fakturerings kontext. Fakturerings kontexten baseras på hur du registrerade dig för Azure. För företags kunder är det delade omfånget registreringen och innehåller alla prenumerationer i registreringen. För kunder som betalar per användning inkluderar det delade omfånget alla enskilda prenumerationer med priser enligt principen betala per användning som har skapats av konto administratören.  <br/><br/>  Om du väljer **enskild prenumeration** tillämpas reservations rabatten på Azure Storage kapacitet i den valda prenumerationen. <br/><br/> Om du väljer **enskild resurs grupp** tillämpas reservations rabatten på Azure Storage kapacitet i den valda prenumerationen och den valda resurs gruppen i den prenumerationen. <br/><br/> Du kan ändra reservations omfånget när du har köpt reservationen.  |
   |**Prenumeration**  | Den prenumeration som används för att betala för Azure Storage reservationen. Betalnings metoden för den valda prenumerationen används för att debitera kostnaderna. Prenumerationen måste vara någon av följande typer: <br/><br/>  Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P): för en företags prenumeration dras avgifterna från registreringens förskotts betalning i Azure (tidigare kallat betalnings åtagande) eller debiteras som överanvändning. <br/><br/> Individuell prenumeration med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P): för en enskild prenumeration med taxan betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.    |
   | **Region** | Den region där reservationen gäller. |
   | **Åtkomstnivå** | Åtkomst nivån där reservationen gäller. Alternativen är *frekvent*, *låg frekvent* eller *Arkiv* lag ring. Mer information om åtkomst nivåer finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](storage-blob-storage-tiers.md)lag rings nivåer. |
   | **Redundans** | Alternativet redundans för reservationen. Alternativen inkluderar *LRS*, *ZRS*, *GRS*, *GZRS*, *RA-GRS* och *ra-GZRS*. Mer information om redundans alternativ finns [Azure Storage redundans](../common/storage-redundancy.md). |
   | **Fakturerings frekvens** | Anger hur ofta kontot debiteras för reservationen. Alternativen omfattar *varje månad* eller i *förskott*. |
   | **Storlek** | Mängden kapacitet som ska reserveras. |
   |**Period**  | Ett år eller tre år.   |

1. När du har valt parametrar för din reservation, visar Azure Portal kostnaden. Portalen visar också rabatt procenten för betalning per användning.

1. I fönstret **inköps reservationer** granskar du den totala kostnaden för reservationen. Du kan också ange ett namn på reservationen.

    ![Skärm bild som visar hur du köper en reservation](media/storage-blob-reserved-capacity/purchase-reservations.png)

När du har köpt en reservation tillämpas den automatiskt på alla befintliga Azure Storage Block-Blob-eller Azure Data Lake Storage Gen2 resurser som matchar villkoren i reservationen. Om du inte har skapat några Azure Storage resurser än, kommer reservationen att gälla när du skapar en resurs som matchar villkoren i reservationen. I båda fallen börjar reservations perioden omedelbart efter ett lyckat köp.

## <a name="exchange-or-refund-a-reservation"></a>Exchange eller återbetala en reservation

Du kan byta ut eller återbetala en reservation med vissa begränsningar. Dessa begränsningar beskrivs i följande avsnitt.

Om du vill byta ut eller återbetala en reservation navigerar du till reservations informationen i Azure Portal. Välj **Exchange** eller **åter betalning** och följ anvisningarna för att skicka en support förfrågan. När begäran har bearbetats skickar Microsoft ett e-postmeddelande till dig för att bekräfta att begäran har slutförts.

Mer information om Azure Reservations-principer finns i [självbetjänings utbyte och åter betalningar för Azure reservations](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Utbyte av en reservation

Genom att byta en reservation kan du få en proportionell åter betalning som baseras på den outnyttjade delen av reservationen. Du kan sedan tillämpa åter betalningen på inköps priset för en ny Azure Storage reservation.

Det finns ingen gräns för hur många byten du kan göra. Det finns dessutom ingen avgift kopplad till ett utbyte. Den nya reservationen som du köper måste vara lika med eller större än den proportionella krediten från den ursprungliga reservationen. En Azure Storage reservation kan bara bytas ut för en annan Azure Storage reservation och inte för en reservation för någon annan Azure-tjänst.

### <a name="refund-a-reservation"></a>Återbetala en reservation

Du kan avbryta en Azure Storage reservation när som helst. När du avbryter får du en proportionell åter betalning som baseras på den återstående reservations perioden, minus en avgift för tidig uppsägning på 12 procent. Den högsta bidrags satsen per år är $50 000.

Om du avbryter en reservation avslutas omedelbart reservationen och resten av månaderna returneras till Microsoft. Det återstående proportionella saldot minus avgiften återbetalas till din ursprungliga köp form.

## <a name="expiration-of-a-reservation"></a>Förfallo datum för en reservation

När en reservation går ut debiteras Azure Storage kapacitet som du använder under den reservationen enligt priset betala per användning. Reservationer förnyas inte automatiskt.

Du får ett e-postmeddelande 30 dagar innan reservationen upphör att gälla och sedan på förfallo datumet. Om du vill fortsätta att dra nytta av de kostnads besparingar som en reservation ger, förnyar du den inte senare än förfallo datumet.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure-reservationer?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Förstå hur reservationsrabatten tillämpas för Azure Storage](../../cost-management-billing/reservations/understand-storage-charges.md)