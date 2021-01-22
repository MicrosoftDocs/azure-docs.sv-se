---
title: Spara pengar på Azure Synapse Analytics-avgifter med reserverad kapacitet i Azure
description: Läs om hur du kan sänka dina kostnader för Azure Synapse Analytics med reserverad kapacitet.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: bd43b668c318b825c5c5b6f36fc1da1055863bed
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599104"
---
# <a name="save-costs-for-azure-synapse-analytics-charges-with-reserved-capacity"></a>Spara pengar på Azure Synapse Analytics-avgifter med reserverad kapacitet

Du kan spara pengar med Azure Synapse Analytics genom att köpa en reservation för din cDWU-användning under ett eller tre år. Om du vill köpa reserverad kapacitet för Azure Synapse Analytics måste du välja Azure-region och period. Sedan lägger du SKU:n för Azure Synapse Analytics i kundvagnen och väljer hur många cDWU-enheter du vill köpa.

När du köper en reservation används inte längre Betala per användning-priser för den Azure Synapse Analytics-användning som matchar reservationsattributen.

En reservation omfattar inte lagrings- eller nätverksavgifter som är kopplade till Azure Synapse Analytics-användningen.

När den reserverade kapaciteten löper ut fortsätter Azure Synapse Analytics-instanserna att köras men debiteras då enligt Betala per användning-priser. Reservationer förnyas inte automatiskt.

Du hittar prisinformation i [Erbjudanden om reserverad kapacitet för Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

Du kan köpa reserverad kapacitet för Azure Synapse Analytics i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Betala för reservationen [i förväg eller via månadsbetalningar](./prepare-buy-reservation.md). Så här köper du reserverad kapacitet:

- Du måste ha rollen ägare för minst en Enterprise-prenumeration eller Betala per användning-prenumeration.
- För Enterprise-prenumerationer måste alternativet **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Om inställningen är inaktiverad måste du vara EA-administratör.
- I CSP-programmet (Cloud Solution Provider) är det bara administratörsagenter och säljagenter som kan köpa reserverad kapacitet för Azure Synapse Analytics.

Mer information om hur Enterprise-kunder och Betala per användning-kunder debiteras för reservationsköp finns i [Förstå hur Azure-reservationer tillämpas för din Enterprise-registrering](understand-reserved-instance-usage-ea.md) och [Förstå hur Azure-reservationer tillämpas för din Betala per användning-prenumeration](understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Välj rätt storlek innan köpet

Storleken på Azure Synapse Analytics-reservationen bör baseras på den totala mängden cDWU-enheter du förbrukar. Inköp görs i steg om 100 cDWU.

Anta till exempel att din totala förbrukning av Azure Synapse Analytics är DW3000c. Du vill köpa reserverad kapacitet för hela mängden. Då bör du köpa 30 enheter av reserverad cDWU-kapacitet.

## <a name="buy-azure-synapse-analytics-reserved-capacity"></a>Köp reserverad kapacitet för Azure Synapse Analytics

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Alla tjänster** > **Reservationer**.
3. Välj en prenumeration. Använd prenumerationslistan och välj den prenumeration som används till att betala för den reserverade kapaciteten. Den reserverade kapaciteten debiteras via betalningsmetoden för prenumerationen. Prenumerationstypen måste vara EA (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P), betala per användning (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P).
   - För företagsprenumerationer dras avgifterna från registreringens saldo för Azure-förskottsbetalning (kallades tidigare ekonomiskt åtagande) eller debiteras som överförbrukning.
   - Får en Betala per användning-prenumeration faktureras avgifterna från kreditkortet eller enligt fakturabetalningsmetoden.
4. Välj ett omfång. Välj ett prenumerationsomfång i omfångslistan.
   - **Omfång för enskild resursgrupp** – reservationsrabatten tillämpas endast på matchande resurser i den valda resursgruppen.
   - **Omfång för enskild prenumeration** – reservationsrabatten tillämpas på matchande resurser i den valda prenumerationen.
   - **Delat omfång** – reservationsrabatten tillämpas på matchande resurser i berättigade prenumerationer i faktureringskontexten. För EA-kunder är registreringen faktureringskontext. För enskilda prenumerationer med betalning per användning är faktureringsomfånget alla berättigade prenumerationer som kontoadministratören har skapat.
   - För Enterprise-kunder är EA-registreringen faktureringskontext.
   - För Betala per användning-kunder gäller den delade omfattningen samtliga Betala per användning-prenumerationer som kontoadministratören har skapat.
5. Välj en region som den reserverade kapaciteten ska täcka.
6. Välj en kvantitet. Ange hur många 100 cDWU-enheter du vill köpa.    
   Kvantiteten 30 skulle till exempel ge dig 3 000 cDWU reserverad kapacitet i timmen.
7. Du kan se kostnaderna för reserverad kapacitet för Azure Synapse Analytics i avsnittet **Kostnader**.
8. Välj **Köp**.
9. Välj **Visa den här reservationen** om du vill se inköpsstatusen.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](exchange-and-refund-azure-reservations.md).

Reservationsrabatter tillämpas automatiskt för de Azure Synapse Analytics-instanser som matchar den reserverade Azure Synapse Analytics-kapacitetens omfång och region. Du kan uppdatera den reserverade Azure Synapse Analytics-kapacitetens omfång i [Azure-portalen](https://portal.azure.com/), med PowerShell, CLI eller via API:et.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/).

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om hur reservationsrabatter tillämpas för Azure Synapse Analytics kan du läsa [Så tillämpas reservationsrabatter för Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md).

- Du kan läsa mer om Azure-reservationer i följande artiklar:
  - [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
  - [Hantera Azure Reservations](manage-reserved-vm-instance.md)
  - [Förstå reservationsrabatter i Azure](understand-reservation-charges.md)
  - [Förstå reservationsanvändning för din Betala per användning-prenumeration](understand-reserved-instance-usage.md)
  - [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)