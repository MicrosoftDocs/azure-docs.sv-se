---
title: Spara i Azure App Service med reserverad kapacitet
description: Lär dig hur du kan spara pengar på isolerade stämpelavgifter i Azure App Service med reserverad kapacitet.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: fb463d80e8cb980505df825d6f22d75f8a5c5f5a
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599203"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Spara pengar på isolerade stämpelavgifter i Azure App Service med reserverad kapacitet

Du kan spara pengar på isolerade stämpelavgifter i Azure App Service genom att åta dig en reservation för stämpelanvändningen under tre år. Om du vill köpa reserverad kapacitet för isolerade stämpelavgifter måste du välja i vilken region stämpeln ska distribueras och hur många stämplar du vill köpa.

När du köper en reservation debiteras inte längre isolerade stämpelavgifter som matchar reservationsattributen enligt användningsbaserade priser. Reservationen används automatiskt för det antal isolerade stämplar som matchar den reserverade kapacitetens omfattning och region. Du behöver inte tilldela en reservation till en viss isolerad stämpel. Reservationen gäller inte för webbarbeten, och därför debiteras andra resurser som är kopplade till stämpeln separat.

När den reserverade kapaciteten löper ut fortsätter de isolerade stämplarna att köras men debiteras då enligt användningsbaserade priser. Reservationer förnyas inte automatiskt.

## <a name="determine-the-right-reservation-to-purchase"></a>Avgöra vilken reservation du ska köpa

När du köper en reservation binder du dig till en viss användning av reserverade kvantiteter under de kommande tre åren. Granska dina användningsdata så att du ser hur många isolerade stämplar för App Service du använder som regel och kommer att behöva i framtiden.

Se även till att du förstår hur den isolerade stämpeln sänder ut Linux- och Windows-mätare.

- Som standard sänder en tom isolerad stämpel ut Windows-stämpelmätaren. Det här gäller till exempel om inga webbarbeten är distribuerade. Den fortsätter att sända ut den här mätaren om Windows-webbarbeten distribueras till stämpeln.
- Mätaren ändras till en Linux-stämpelmätare om du distribuerar ett Linux-webbarbete.
- När både Linux- och Windows-webbarbeten distribueras så sänder stämpeln ut Windows-mätaren.

Stämpelmätaren kan alltså ändras mellan Windows och Linux under stämpelns livslängd.

Köp stämpelreservationer för Windows om du har ett eller flera Windows-webbarbeten för stämpeln. Den enda gång du bör köpa en Linux-stämpelreservation är om du planerar att _bara_ använda Linux-webbarbeten med stämpeln.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Köpa reserverad kapacitet för isolerade stämplar

Du kan köpa reserverad kapacitet för isolerade stämplar i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Betala för reservationen [i förväg eller via månadsbetalningar](./prepare-buy-reservation.md). Om du vill köpa reserverad kapacitet måste du ha en ägarroll för minst en Enterprise-prenumeration eller en enskild prenumeration med betalning per användning.

- För Enterprise-prenumerationer måste alternativet **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Om inställningen är inaktiverad måste du vara EA-administratör.
- I CSP-programmet (Cloud Solution Provider) är det bara administratörsagenter och säljagenter som kan köpa reserverad kapacitet för Azure Synapse Analytics.

**Så här köper du:**

1. Gå till [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Välj en prenumeration. Välj den prenumeration som ska betala för den reserverade kapaciteten i listan **Prenumeration**. Den reserverade kapaciteten debiteras via betalningsmetoden för prenumerationen. Prenumerationstypen måste vara EA (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P), betala per användning (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P) eller en CSP-prenumeration.
    - För företagsprenumerationer dras avgifterna från registreringens saldo för Azure-förskottsbetalning (kallades tidigare ekonomiskt åtagande) eller debiteras som överförbrukning.
    - Får en Betala per användning-prenumeration faktureras avgifterna från kreditkortet eller enligt fakturabetalningsmetoden.
1. Välj en **Omfattning** för prenumerationen.
    - **Omfång för enskild resursgrupp** – reservationsrabatten tillämpas endast på matchande resurser i den valda resursgruppen.
    - **Omfång för enskild prenumeration** – reservationsrabatten tillämpas på matchande resurser i den valda prenumerationen.
    - **Delat omfång** – reservationsrabatten tillämpas på matchande resurser i berättigade prenumerationer i faktureringskontexten. För EA-kunder är registreringen faktureringskontext. För enskilda prenumerationer med betalning per användning är faktureringsomfånget alla berättigade prenumerationer som kontoadministratören har skapat.
1. Välj en **Region** som den reserverade kapaciteten ska täcka och lägg till reservationen i kundvagnen.
1. Välj en typ av isolerad plan och klicka på **Välj**.  
    ![Exempel ](./media/prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Ange antalet isolerade stämplar för App Service som ska reserveras. Om du väljer tre får du till exempel tre reserverade stämplar i en region. Klicka på **Nästa: Granska och köp**.
1. Granska och klicka på **Köp nu**.

Efter köpet går du till [Reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) där du när som helst kan se status för köpet.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](exchange-and-refund-azure-reservations.md).

## <a name="discount-application-shown-in-usage-data"></a>Visning av rabatter i användningsdata

Dina användningsdata har ett effektivt pris på noll för användning som täcks av reservationen. Du ser reservationsrabatten i användningsdata för varje stämpelinstans i varje reservation.

Du kan läsa mer om visning av reservationsrabatten i användningsdata i [Hämta information om kostnader och användning för reservationer i Enterprise-avtal](understand-reserved-instance-usage-ea.md) om du är EA-kund. Annars kan du läsa i [Förstå användning av Azure-reservationer för din enskilda prenumeration med Betala per användning-priser](understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Nästa steg

- Du kan läsa mer om Azure-reservationer i följande artiklar:
  - [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
  - [Förstå hur en reservationsrabatt för en isolerad stämpel för Azure App Service tillämpas](reservation-discount-app-service-isolated-stamp.md)
  - [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)