---
title: Optimera kostnader för Azure-disklagring med reservationer
description: Lär dig mer om att köpa Azure-disklagring reservationer för att spara kostnader på Premium SSD-hanterade diskar.
author: roygara
ms.author: rogarana
ms.date: 01/30/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: db6575894904e6ced2d4be48fec5961f5b8b8a54
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602636"
---
# <a name="reduce-costs-with-azure-disks-reservation"></a>Minska kostnader med Azures diskreservation

Spara på Azure-disklagring-användning med reserverad kapacitet. Azure-disklagring reservationer kombinerat med Azure Reserved Virtual Machine Instances kan du sänka dina totala kostnader för virtuella datorer. Reservations rabatten tillämpas automatiskt på de matchande diskarna i det valda reservations omfånget. På grund av det här automatiska programmet behöver du inte tilldela en reservation till en hanterad disk för att få rabatterna.

Rabatter används varje timme beroende på disk användning. Oanvänd reserverad kapacitet överför inte. Azure-disklagring reservations rabatter gäller inte för ohanterade diskar, Ultra disks eller Page BLOB-förbrukning.

## <a name="determine-your-storage-needs"></a>Fastställ dina lagrings behov

Innan du köper en reservation bör du fastställa dina lagrings behov. Azure-disklagring-reservationer är för närvarande endast tillgängliga för utvalda Azure Premium SSD-SKU: er. SKU: n för en Premium SSD fastställer diskens storlek och prestanda.

När du fastställer dina lagrings behov bör du inte tänka på diskar baserat på just-kapacitet. Du kan till exempel inte ha en reservation för en P40-disk och använda den för att betala för två mindre P30-diskar. När du köper en reservation köper du bara en reservation för det totala antalet diskar per SKU.

En disk reservation görs per disk-SKU. Därför baseras reservations förbrukningen på enheten för disk-SKU: er i stället för den angivna storleken.

Anta till exempel att du reserverar en P40-disk med 2 TiB med etablerad lagrings kapacitet. Anta också att du bara tilldelar två P30-diskar. P40-reservationen i detta fall är inte ett konto för P30-förbrukning och du betalar priset för betala per användning på P30-diskarna.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Att tänka på innan du köper

Vi rekommenderar följande metoder när du överväger disk reservations köp:

- Analysera användnings informationen för att avgöra vilka reservationer du bör köpa. Se till att du spårar användningen i disk-SKU: er i stället för att ha disk kapaciteten etablerad eller används.
- Undersök disk reservationen tillsammans med din VM-reservation. Vi rekommenderar starkt att du gör reservationer för både VM-användning och disk användning för maximala besparingar. Du kan börja med att fastställa rätt VM-reservation och sedan utvärdera disk reservationen. I allmänhet har du en standard konfiguration för var och en av dina arbets belastningar. En SQL Server-Server kan till exempel ha två P40-datadiskar och en P30-operativsystems disk.
  
  Den här typen av mönster kan hjälpa dig att avgöra det reserverade belopp som du kan köpa. Den här metoden kan förenkla utvärderings processen och se till att du har en plan för både VM och diskar. Planen innehåller överväganden som prenumerationer eller regioner.

## <a name="purchase-restrictions"></a>Köp begränsningar

Reservations rabatter är för närvarande inte tillgängliga för följande:

- Ohanterade diskar eller sid-blobar.
- Standard SSD eller standard hård diskar (HDD).
- Premium SSD SKU: er som är mindre än P30: P1, P2, P3, P4, P6, P10, p15 och P20 SSD SKU: er.
- Diskar i Azure Government, Azure Germany eller Azure Kina-regioner.

I sällsynta fall begränsar Azure köpet av nya reservationer till en delmängd disk SKU: er på grund av låg kapacitet i en region.

## <a name="buy-a-disk-reservation"></a>Köp en disk reservation

Du kan köpa Azure-disklagring reservationer via [Azure Portal](https://portal.azure.com/). Du kan betala för reservationen antingen före eller med månads betalningar. Mer information om hur du köper med månads betalningar finns i [inköps reservationer med månads betalningar](../cost-management-billing/reservations/prepare-buy-reservation.md#buy-reservations-with-monthly-payments).

Följ dessa steg för att köpa reserverad kapacitet:

1. Gå till fönstret [köp reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) i Azure Portal.

1. Välj **Azure-Managed disks** för att köpa en reservation.

    ![Fönster för inköps reservationer](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Ange de värden som krävs enligt beskrivningen i följande tabell:

   |Element  |Beskrivning  |
   |---------|---------|
   |**Omfång**   |  Hur många prenumerationer kan använda fakturerings förmånen som är associerad med reservationen. Det här värdet anger även hur reservationen tillämpas på vissa prenumerationer. <br/><br/> Om du väljer **delad** tillämpas reservations rabatten på Azure Storage kapacitet i varje prenumeration i din fakturerings kontext. Fakturerings kontexten baseras på hur du registrerade dig för Azure. För företags kunder är det delade omfånget registreringen och innehåller alla prenumerationer i registreringen. För kunder som betalar per användning inkluderar det delade omfånget alla enskilda prenumerationer med priser enligt principen betala per användning som har skapats av konto administratören.  <br/><br/>  Om du väljer **enskild prenumeration** tillämpas reservations rabatten på Azure Storage kapacitet i den valda prenumerationen. <br/><br/> Om du väljer **enskild resurs grupp** tillämpas reservations rabatten på Azure Storage kapacitet i den valda prenumerationen och i den valda resurs gruppen för den aktuella prenumerationen. <br/><br/> Du kan ändra reservations omfånget när du har köpt reservationen.  |
   |**Prenumeration**  | Den prenumeration som du använder för att betala för Azure Storage reservationen. Betalnings metoden för den valda prenumerationen används för att debitera kostnaderna. Prenumerationen måste vara någon av följande typer:<br/><ul><li> Enterprise-avtal (erbjudande nummer MS-AZR-0017P och MS-AZR-0148P). För en företags prenumeration dras avgifterna från registreringen för förskotts betalning i Azure (tidigare kallat betalnings åtagande) eller debiteras som överanvändning.</li><br/><li>Enskild prenumeration med priser enligt principen betala per användning (erbjudande nummer MS-AZR-0003P och MS-AZR-0023P). För en enskild prenumeration med priser enligt principen betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.</li></ul>    |
   | **Diskar** | Den SKU som du vill skapa. |
   | **Region** | Den region där reservationen gäller. |
   | **Fakturerings frekvens** | Hur ofta kontot debiteras för reservationen. Alternativen omfattar **varje månad** och **längst fram**. |

    ![För att välja den produkt som du vill purchase.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. När du har angett värden för din reservation visar Azure Portal kostnaden. Portalen visar också rabatt procenten för betalning per användning. Välj **Nästa** för att fortsätta till fönstret **köp reservationer** .

1. I fönstret **inköps reservationer** kan du ge reservationen ett namn och välja det totala antalet reservationer som du vill göra. Antalet reservationer mappar till antalet diskar. Om du till exempel vill reservera en hundra diskar anger du **kvantiteten** **100**.

1. Granska den totala kostnaden för reservationen.

    ![Fönstret inköps reservationer](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

När du har köpt en reservation tillämpas den automatiskt på alla befintliga Disklagring-resurser som matchar reservations villkoren. Om du inte har skapat några Disklagring resurser än, gäller reservationen när du skapar en resurs som matchar reservations villkoren. I båda fallen börjar reservations perioden omedelbart efter ett lyckat köp.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut eller återbetala reservationer i vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="expiration-of-a-reservation"></a>Förfallo datum för en reservation

När en reservation går ut debiteras Azure-disklagring kapacitet som du använder under den reservationen enligt priset betala per användning. Reservationer förnyas inte automatiskt.

Du får ett e-postmeddelande 30 dagar innan reservationen upphör att gälla och på förfallo datumet. Om du vill fortsätta att dra nytta av de kostnads besparingar som en reservation ger, förnyar du den inte senare än förfallo datumet.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure-reservationer?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Förstå hur reservations rabatten tillämpas på Azure-disklagring](../cost-management-billing/reservations/understand-disk-reservations.md)