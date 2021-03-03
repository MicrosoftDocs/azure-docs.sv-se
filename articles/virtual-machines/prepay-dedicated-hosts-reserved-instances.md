---
title: Förskottsbetal för Azure-dedikerade värdar att spara pengar
description: Lär dig att köpa reserverade instanser av Azure dedicerade värdar för att spara pengar på dina beräknings kostnader.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 1450f03ae72e79daca5a5ae2c04506e6bee5180b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672272"
---
# <a name="save-costs-with-azure-dedicated-host-reservations"></a>Spara kostnader med Azures dedikerade värd reservationer

När du genomför en reserverad instans av Azure-dedikerade värdar kan du spara pengar. Reservations rabatten tillämpas automatiskt på antalet aktiva dedikerade värdar som matchar reservations omfånget och attributen. Du behöver inte tilldela en reservation till en dedikerad värd för att hämta rabatterna. En reserverad instans köps bara täcker beräknings delen av din användning och inkluderar kostnader för program varu licenser. Se [Översikt över Azures dedikerade värdar för virtuella datorer](./dedicated-hosts.md).

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Ta reda på rätt dedikerad värd-SKU innan du köper


Innan du köper en reservation bör du bestämma vilken dedikerad värd du behöver. En SKU definieras för en dedikerad värd som representerar VM-serien och-typen. 

Börja med att gå över de storlekar som stöds för [virtuella Windows-datorer](./sizes.md) eller [Linux](./sizes.md) för att identifiera VM-serien.

Kontrol lera sedan om det stöds på Azure-dedikerade värdar. [Pris sidan för Azure-dedikerade värdar](https://aka.ms/ADHPricing) har den fullständiga listan över dedikerade värdar SKU: er, deras processor information och olika pris alternativ (inklusive reserverade instanser).

Du kan hitta flera SKU: er som stöder VM-serien (med olika typer). Identifiera den bästa SKU: n genom att jämföra värdens kapacitet (antal virtuella processorer). Observera att du kan tillämpa reservation på flera dedikerade värdar SKU: er som stöder samma VM-serie (till exempel DSv3_Type1 och DSv3_Type2), men inte mellan olika VM-serier (t. ex. DSv3 och ESv3).



## <a name="purchase-restriction-considerations"></a>Överväganden vid köp av begränsningar

Reserverade instanser är tillgängliga för de flesta dedikerade värd storlekar, med vissa undantag.

Reservations rabatter gäller inte för följande:

- **Moln** – reservationer är inte tillgängliga för köp i Tyskland eller regioner i Kina.

- **Otillräcklig kvot** – en reservation som är begränsad till en enskild prenumeration måste ha vCPU-kvot tillgänglig i prenumerationen för den nya reserverade instansen. Om mål prenumerationen till exempel har en kvot gräns på 10 virtuella processorer för DSv3-serien kan du inte köpa en dedikerade dedikerade värdar som stöder den här serien. Kvot kontrollen för reservationer omfattar de virtuella datorer och dedikerade värdar som redan har distribuerats i prenumerationen. Du kan [skapa en begäran om kvot ökning](../azure-portal/supportability/resource-manager-core-quotas-request.md) för att lösa problemet.

- **Kapacitets begränsningar** – i sällsynta fall begränsar Azure inköpet av nya reservationer för en delmängd av dedikerade värd-SKU: er, på grund av låg kapacitet i en region.

## <a name="buy-a-reservation"></a>Köpa en reservation

Du kan köpa en reserverad instans av en Azure-dedikerad värd instans i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Betala för reservationen [i förväg eller via månadsbetalningar](../cost-management-billing/reservations/prepare-buy-reservation.md). Dessa krav gäller för att köpa en reserverad dedikerad värd instans:

- Du måste ha ägar rollen för minst en EA-prenumeration eller en prenumeration med en taxa enligt principen betala per användning.

- För EA-prenumerationer måste alternativet **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Om den inställningen är inaktiverad måste du vara EA-administratör för prenumerationen.

- För ett CSP-program (Cloud Solution Provider) kan endast administratörs agenter eller försäljnings agenter köpa reservationer.

Så här köper du en instans:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **Alla tjänster** \> **Reservationer**.

3. Välj **Lägg till** för att köpa en ny reservation och klicka sedan på **dedikerade värdar**.

4. Fyll i obligatoriska fält. Kör dedikerade värd instanser som matchar de attribut du väljer kvalificera för att hämta reservations rabatten. Det faktiska antalet dedikerade värd instanser som erhåller rabatten beror på omfattning och kvantitet som väljs.

Om du har ett EA-avtal kan du använda **alternativet Lägg till fler** för att snabbt lägga till fler instanser. Alternativet är inte tillgängligt för andra prenumerations typer.

| **Fält**           | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Prenumeration        | Prenumerationen som används för att betala för reservationen. Reservationen debiteras via prenumerationens betalningsmetod. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller Microsoft-kundavtal eller en enskild prenumeration med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). Avgifterna dras från saldot för Azure-förskottsbetalningen (kallades tidigare ekonomiskt åtagande) om det finns ett sådant eller debiteras som överförbrukning. För en prenumeration med priser enligt principen betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen. |
| Omfång               | Reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Region              | Den Azure-region som omfattas av reservationen.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Dedikerad värd storlek | Storleken på de dedikerade värd instanserna.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Period                | Ett år eller tre år.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Kvantitet            | Antalet instanser som köps i reservationen. Antalet är antalet aktiva dedikerade värd instanser som kan hämta fakturerings rabatten.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **Omfång för enskild resursgrupp** – reservationsrabatten tillämpas endast på matchande resurser i den valda resursgruppen.

- **Omfång för enskild prenumeration** – reservationsrabatten tillämpas på matchande resurser i den valda prenumerationen.

- **Delat omfång** – reservationsrabatten tillämpas på matchande resurser i berättigade prenumerationer i faktureringskontexten. För EA-kunder är fakturerings kontexten registreringen. För enskilda prenumerationer med betalning per användning är faktureringsomfånget alla berättigade prenumerationer som kontoadministratören har skapat.

## <a name="usage-data-and-reservation-utilization"></a>Användnings data och reservations användning

Dina användningsdata har ett effektivt pris på noll för användning som täcks av reservationen. Du kan se vilken VM-instans som har tagit emot reservations rabatten för varje reservation.

Mer information om hur reservations rabatter visas i användnings data finns i [förstå Azure reservation-användning för företagets registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) om du är en EA-kund. Om du har en enskild prenumeration kan du läsa mer i avsnittet om [hur du betalar per användning för din prenumeration enligt principen betala per användning](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Ändra en reservation efter köpet

Du kan göra följande typer av ändringar i en reservation efter köpet:

- Uppdatera reservationsomfång

- Flexibilitet för instans storlek (om tillämpligt)

- Ägarskap

Du kan också dela upp en reservation i mindre segment och slå samman redan delade reservationer. Ingen av ändringarna orsakar en ny kommersiell transaktion eller ändrar slut datumet för reservationen.

Du kan inte göra följande typer av ändringar efter köpet, direkt:

- En befintlig reservations region

- SKU

- Kvantitet

- Varaktighet

Du kan dock *byta* ut en reservation om du vill göra ändringar.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Information om hur du hanterar en reservation finns i [Hantera Azure-reservationer](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Mer information om Azure-reservationer finns i följande artiklar:

- [Vad är Azure-reservationer?](../cost-management-billing/reservations/save-compute-costs-reservations.md)

- [Använda Azure Dedicated Hosts](./dedicated-hosts.md)

- [Prissättning för reserverade värdar](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Hantera reservationer i Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

- [Förstå hur reservationsrabatten tillämpas](../cost-management-billing/manage/understand-vm-reservation-charges.md)

- [Förstå reservationsanvändning för en prenumeration med Betala per användning-priser](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

- [Förstå reservationsanvändning för din Enterprise-registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

- [Kostnader för Windows-programvara ingår inte i reservationer](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

- [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](/partner-center/azure-reservations)