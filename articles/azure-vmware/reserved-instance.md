---
title: Reserverade instanser av Azure VMware-lösning
description: Lär dig hur du köper en reserverad instans för Azure VMware-lösningen. Den reserverade instansen täcker bara beräknings delen av din användning och innehåller kostnader för program varu licenser.
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: 6f181d4b6ee7e2098130633fef0eb8558add0308
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210032"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Spara kostnader med Azure VMware-lösningen

När du genomför en reserverad instans av [Azure VMware-lösningen](introduction.md)sparar du pengar.  Reservations rabatten tillämpas automatiskt på de Azure VMware-lösningar som körs och som matchar reservations omfånget och attributen. Ett inköp med reserverad instans täcker bara beräknings delen av din användning och innehåller kostnader för program varu licenser. 

## <a name="purchase-restriction-considerations"></a>Överväganden vid köp av begränsningar

Reserverade instanser är tillgängliga med vissa undantag.

-   **Moln** – reservationer är bara tillgängliga i de regioner som anges på sidan [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Otillräcklig kvot** – en reservation som är begränsad till en enda/delad prenumeration måste ha värd kvoten tillgänglig i prenumerationen för den nya reserverade instansen. Du kan [skapa en begäran om kvot ökning](enable-azure-vmware-solution.md) för att lösa problemet.

-   **Erbjudande för erbjudandet**– du behöver ett [Azure-Enterprise-avtal (EA)](../cost-management-billing/manage/ea-portal-agreements.md) med Microsoft.

-   **Kapacitets begränsningar** – i sällsynta fall begränsar Azure inköpet av nya reservationer för Azure VMware-lösningen värd-SKU: er på grund av låg kapacitet i en region.

## <a name="buy-a-reservation"></a>Köpa en reservation

Du kan köpa en reserverad instans av en Azure VMware-lösning värd instans i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Du kan betala för reservationen [fram eller med månads betalningar](../cost-management-billing/reservations/prepare-buy-reservation.md).

Dessa krav gäller för att köpa en reserverad dedikerad värd instans:

-   Du måste ha *ägar* rollen för minst en EA-prenumeration eller en prenumeration med en taxa enligt principen betala per användning.

-   För EA-prenumerationer måste du aktivera alternativet **Lägg till reserverade instanser** i [EA-portalen](https://ea.azure.com/). Om inaktive rad måste du vara en EA-administratör för prenumerationen för att aktivera den.

-   För prenumerationer under en Azure-prenumeration (Cloud Solution Provider) måste partnern köpa de reserverade instanserna i Azure Portal för kunden. 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>Köp reserverade instanser för en EA-prenumeration

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **Alla tjänster** > **Reservationer**.

3. Välj **Köp nu** och välj sedan **Azure VMware-lösning**.

4. Ange de obligatoriska fälten. De valda attributen som matchar Azure VMware-lösningens värd är kvalificerade för reservations rabatten.  Exempel på attribut är SKU, regioner (om tillämpligt) och omfång. Reservationsomfånget bestämmer var reservationsbesparingarna ska gälla.

   Om du har ett EA-avtal kan du använda **alternativet Lägg till fler** för att snabbt lägga till instanser. Alternativet är inte tillgängligt för andra prenumerations typer.

   | Fält        |  Beskrivning |
   | ------------ | ------------ |
   | Prenumeration | Prenumerationen som används för att betala för reservationen. Reservationen debiteras via prenumerationens betalningsmetod. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P), Microsofts kund avtal eller en enskild prenumeration med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). Avgifterna dras från saldot för Azure-förskottsbetalningen (kallades tidigare ekonomiskt åtagande) om det finns ett sådant eller debiteras som överförbrukning. För en prenumeration med priser enligt principen betala per användning debiteras avgifterna till prenumerationens kredit kort eller en faktura betalnings metod. |
   | Omfång        | Reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer:<br><ul><li><b>Enskilt resurs grupps omfång</b> – tillämpar reservations rabatten på de matchande resurserna i den valda resurs gruppen.</li><li><b>Enda prenumerations omfång</b> – tillämpar reservations rabatten på de matchande resurserna i den valda prenumerationen.</li><li><b>Delat omfång</b> – använder reservations rabatten för att matcha resurser i berättigade prenumerationer som finns i fakturerings kontexten. För EA-kunder är fakturerings kontexten registreringen. För enskilda prenumerationer med betalning per användning är faktureringsomfånget alla berättigade prenumerationer som kontoadministratören har skapat.</li></ul>       |
   | Region       | Den Azure-region som omfattas av reservationen.   |
   | Värd storlek    | AV36    |
   | Period         | Ett år eller tre år.  |
   | Kvantitet     | Antalet instanser som ska köpas i reservationen. Antalet är antalet Azure VMware-lösningar som kan få fakturerings rabatt.    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>Köp reserverade instanser för en CSP-prenumeration

Kryptografiproviders som vill köpa reserverade instanser för sina kunder måste använda administrate-proceduren ( **admin på uppdrag av** ) i [partner Center-dokumentationen](/partner-center/azure-plan-manage). Om du vill ha mer information kan du Visa [admin på uppdrag av (administrate)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) video.

1. Logga in på [partner Center](https://partner.microsoft.com).

2. Välj **CSP** för att få åtkomst till avsnittet **kunder** .

3. Expandera kund information och välj **Microsoft Azure-hanteringsportal**. 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Microsoft Partner Center-kunder, region" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. I Azure Portal väljer du **alla tjänster**  >  **reservationer**.

5. Välj **Köp nu** och välj sedan **Azure VMware-lösning**.

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="Microsoft Azure-portalen reservationer" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. Ange de obligatoriska fälten. De valda attributen som matchar Azure VMware-lösningens värd är kvalificerade för reservations rabatten.  Exempel på attribut är SKU, regioner (om tillämpligt) och omfång. Reservationsomfånget bestämmer var reservationsbesparingarna ska gälla.

   | Fält        |  Beskrivning |
   | ------------ | ------------ |
   | Prenumeration | Prenumerationen som används för att betala för reservationen. Reservationen debiteras via prenumerationens betalningsmetod. Prenumerations typen måste vara en berättigad, vilket i det här fallet är en CSP-prenumeration|
   | Omfång        | Reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer:<br><ul><li><b>Enskilt resurs grupps omfång</b> – tillämpar reservations rabatten på de matchande resurserna i den valda resurs gruppen.</li><li><b>Enda prenumerations omfång</b> – tillämpar reservations rabatten på de matchande resurserna i den valda prenumerationen.</li><li><b>Delat omfång</b> – använder reservations rabatten för att matcha resurser i berättigade prenumerationer som finns i fakturerings kontexten. För EA-kunder är fakturerings kontexten registreringen. För enskilda prenumerationer med betalning per användning är faktureringsomfånget alla berättigade prenumerationer som kontoadministratören har skapat.</li></ul>       |
   | Region       | Den Azure-region som omfattas av reservationen.   |
   | Värd storlek    | AV36    |
   | Period         | Ett år eller tre år.  |
   | Kvantitet     | Antalet instanser som ska köpas i reservationen. Antalet är antalet Azure VMware-lösningar som kan få fakturerings rabatt.     |

Mer information om hur du visar de inköpta reservationerna för din kund finns i [Visa Azure-reservationer som en Azure Solution Provider-artikel (CSP)](../cost-management-billing/reservations/how-to-view-csp-reservations.md) .

## <a name="usage-data-and-reservation-usage"></a>Användnings data och reservations användning

Din användning som erhåller en reservations rabatt har ett effektivt pris på noll. Du kan se vilken Azure VMware Solution-instans som har tagit emot reservations rabatten för varje reservation.

Mer information om hur reservations rabatter visas i användnings data:

- För EA-kunder, se [förstå Azure reservation-användning för företagets registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- För enskilda prenumerationer, se [förstå Azure reservation-användning för din prenumeration enligt principen betala per](../cost-management-billing/reservations/understand-reserved-instance-usage.md) användning

## <a name="change-a-reservation-after-purchase"></a>Ändra en reservation efter köpet

Du kan göra dessa ändringar i en reservation efter köpet:

-   Uppdatera reservationsomfång

-   Flexibilitet för instans storlek (om tillämpligt)

-   Ägarskap

Du kan också dela upp en reservation i mindre segment eller slå samman reservationer. Ingen av ändringarna orsakar en ny kommersiell transaktion eller ändrar slut datumet för reservationen.

Mer information om CSP-hanterade reservationer finns i [sälja Microsoft Azure reservationer till kunder med hjälp av Partner Center, Azure Portal eller API: er](/partner-center/azure-reservations).



>[!NOTE]
>När du har köpt din reservation kan du inte göra dessa typer av ändringar direkt:
>
> - En befintlig reservations region
> - SKU
> - Kvantitet
> - Varaktighet
>
>Du kan dock *byta* ut en reservation om du vill göra ändringar.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

CSP: er kan avbryta, byta ut eller återbetala reservationer, med vissa begränsningar som köps för kunden. Mer information finns i [Hantera, avbryta, byta ut eller återbetala Microsoft Azure reservationer för kunder](/partner-center/azure-reservations-manage).

## <a name="next-steps"></a>Nästa steg

Nu när du har täckt en reserverad instans av Azure VMware-lösningen kanske du vill lära dig mer om:

- [Skapa en Azure VMware-lösning](../migrate/how-to-create-azure-vmware-solution-assessment.md)för att utvärdera.
- [Hantera DHCP för Azure VMware-lösningen](manage-dhcp.md).
- [Livs cykel hantering av virtuella datorer i Azure VMware-lösningen](lifecycle-management-of-azure-vmware-solution-vms.md).
 