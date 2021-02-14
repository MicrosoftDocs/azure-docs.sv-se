---
title: Spara i Azure App Service med reserverad kapacitet
description: Lär dig hur du kan spara kostnader för Azure App Service Premium v3-reserverade instanser och isolerad frimärke.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/01/2021
ms.author: banders
ms.custom: references_regions
ms.openlocfilehash: 92a315121ad8ae6fadcadbf6d531eb3e99ae69a9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374549"
---
# <a name="save-costs-with-azure-app-service-reserved-instances"></a>Spara kostnader med Azure App Service reserverade instanser

I den här artikeln förklaras hur du kan spara med Azure App Service reserverade instanser för Premium v3-instanser och isolerade stämpla avgifter.

## <a name="save-with-premium-v3-reserved-instances"></a>Spara med Premium v3 reserverade instanser

När du genomför en Azure App Service Premium v3-reserverad instans kan du spara pengar. Reservations rabatten tillämpas automatiskt på antalet instanser som körs och som matchar reservations omfånget och attributen. Du behöver inte tilldela en reservation till en instans för att hämta rabatterna.

## <a name="determine-the-right-reserved-instance-size-before-you-buy"></a>Fastställ rätt reserverade instans storlek innan du köper

Innan du köper en reservation bör du bestämma storleken på den reserverade Premium v3-instans som du behöver. I följande avsnitt får du hjälp att avgöra rätt Premium v3-reserverade instans storlek.

### <a name="use-reservation-recommendations"></a>Använd reservations rekommendationer

Du kan använda reservations rekommendationer för att avgöra vilka reservationer du bör köpa.

- Köp rekommendationer och Rekommenderad kvantitet visas när du köper en Premium v3-reserverad instans i Azure Portal.
- Azure Advisor tillhandahåller inköps rekommendationer för enskilda prenumerationer.
- Du kan använda API: erna för att få inköps rekommendationer för både delad omfattning och enskild prenumerations omfattning. Mer information finns i [reserverade instanser köp rekommendations-API: er för företags kunder](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- För Enterprise-avtal (EA) och Microsoft Customer Agreement (MCA) är inköps rekommendationer för delade och enkla prenumerations omfattningar tillgängliga med [Azure Consumption Insights Power BI innehålls paketet](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="analyze-your-usage-information"></a>Analysera din användnings information

Analysera användnings informationen för att avgöra vilka reservationer du bör köpa. Användnings data är tillgängliga i användnings filen och API: erna. Använd dem tillsammans för att avgöra vilken reservation som ska köpas. Kontrol lera om det finns Premium v3-instanser med hög användning per dag för att fastställa antalet reservationer som ska köpas.

Din användnings fil visar dina avgifter per fakturerings period och daglig användning. Information om hur du hämtar din användnings fil finns i [Visa och ladda ned Azure-användning och-kostnader](../understand/download-azure-daily-usage.md). Sedan kan du med hjälp av informationen om användnings filen [avgöra vilken reservation som ska köpas](determine-reservation-purchase.md).


## <a name="buy-a-premium-v3-reserved-instance"></a>Köp en reserverad Premium v3-instans

Du kan köpa en reserverad Premium v3-instans i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Betala för reservationen [i förväg eller via månadsbetalningar](prepare-buy-reservation.md). Dessa krav gäller för köp av en Premium v3-reserverad instans:

- Du måste ha ägar rollen för minst en EA-prenumeration eller en prenumeration med en taxa enligt principen betala per användning.
- För EA-prenumerationer måste alternativet **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Om den inställningen är inaktiverad måste du vara EA-administratör för prenumerationen.
- För ett CSP-program (Cloud Solution Provider) kan endast administratörs agenter eller försäljnings agenter köpa reservationer.

Så här köper du en instans:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Alla tjänster** > **Reservationer**.
3. Välj **Lägg till** för att köpa en ny reservation och klicka sedan på **instans**.
4. Fyll i obligatoriska fält. Köra Premium v3-reserverade instanser som matchar de attribut du väljer kvalificera för reservations rabatten. Det faktiska antalet reserverade Premium v3-instanser som erhåller rabatten beror på omfattning och kvantitet som väljs.

Om du har ett EA-avtal kan du använda **alternativet Lägg till fler** för att snabbt lägga till fler instanser. Alternativet är inte tillgängligt för andra prenumerations typer.

| **Fält** | **Beskrivning** |
|---|---|
| Prenumeration | Prenumerationen som används för att betala för reservationen. Reservationen debiteras via prenumerationens betalningsmetod. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller Microsoft-kundavtal eller en enskild prenumeration med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). Avgifterna dras från saldot för det ekonomiska åtagandet om det finns ett sådant eller debiteras som överförbrukning. För en prenumeration med priser enligt principen betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen. |
| Omfång | Reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <ul><li>**Omfång för enskild resursgrupp** – reservationsrabatten tillämpas endast på matchande resurser i den valda resursgruppen. </li><li>**Omfång för enskild prenumeration** – reservationsrabatten tillämpas på matchande resurser i den valda prenumerationen.</li><li>**Delat omfång** – reservationsrabatten tillämpas på matchande resurser i berättigade prenumerationer i faktureringskontexten. För EA-kunder är fakturerings kontexten registreringen. För enskilda prenumerationer med betalning per användning är faktureringsomfånget alla berättigade prenumerationer som kontoadministratören har skapat.</li></ul> |
| Region | Den Azure-region som omfattas av reservationen. |
| Premium v3, reserverad instans storlek | Storleken på de reserverade Premium v3-instanserna. |
| Period | Ett år eller tre år. Det finns också en 5 års period som endast är tillgänglig för HBv2 Premium v3-reserverade instanser. |
| Kvantitet | Antalet instanser som köps i reservationen. Kvantiteten är antalet aktiva Premium v3-reserverade instanser som kan få fakturerings rabatt. Om du till exempel kör 10 standard \_ D2 Premium v3-reserverade instanser i USA, kan du ange kvantitet som 10 för att maximera fördelarna med alla reserverade instanser som körs med Premium v3. |

## <a name="save-with-isolated-stamp-fees"></a>Spara med isolerade stämpla avgifter

Du kan spara pengar på isolerade stämpelavgifter i Azure App Service genom att åta dig en reservation för stämpelanvändningen under tre år. Om du vill köpa reserverad kapacitet för isolerade stämpelavgifter måste du välja i vilken region stämpeln ska distribueras och hur många stämplar du vill köpa.

När du köper en reservation debiteras inte längre isolerade stämpelavgifter som matchar reservationsattributen enligt användningsbaserade priser. Reservationen används automatiskt för det antal isolerade stämplar som matchar den reserverade kapacitetens omfattning och region. Du behöver inte tilldela en reservation till en viss isolerad stämpel. Reservationen gäller inte för webbarbeten, och därför debiteras andra resurser som är kopplade till stämpeln separat.

När den reserverade kapaciteten löper ut fortsätter de isolerade stämplarna att köras men debiteras då enligt användningsbaserade priser. Reservationer förnyas inte automatiskt.

## <a name="determine-the-right-isolated-stamp-reservation-to-purchase"></a>Fastställ den rätta isolerade stämpeln för inköp

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
    ![Exempel ](./media/prepay-app-service/app-service-isolated-stamp-select.png)
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
  - [Förstå hur en reservationsrabatt för en isolerad stämpel för Azure App Service tillämpas](reservation-discount-app-service.md)
  - [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
