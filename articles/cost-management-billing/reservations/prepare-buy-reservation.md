---
title: Köp en Azure-reservation
description: Lär dig mer om viktiga aspekter som hjälp när du ska köpa en Azure-reservation.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: a70d434b06fd19071f3576f9333579e0bd2b85e7
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574982"
---
# <a name="buy-a-reservation"></a>Köpa en reservation

Med Azure Reservations kan du spara pengar genom att registrera dig för en 1- eller 3-årsplan för flera Azure-resurser. Innan du ingår ett åtagande om att köpa en reservation bör du läsa följande avsnitt för att förbereda dig för köpet.

## <a name="who-can-buy-a-reservation"></a>Vem kan köpa en reservation

För att kunna köpa en reservation måste du ha rollen som ägare eller reservationsansvarig för en Azure-prenumeration av typen Enterprise (MS-AZR-0017P eller MS-AZR-0148P), Betala per användning (MS-AZR-0003P eller MS-AZR-0023P) eller Microsoft-kundavtal. Molnlösningsleverantörer kan använda Azure-portalen eller  [Partnercenter](/partner-center/azure-reservations) för att köpa Azure-reservationer.

Enterprise-avtalskunder (EA) kan begränsa köp till EA-administratörer genom att inaktivera alternativet **Lägg till reserverade instanser** i EA-portalen. EA-administratörer måste ha rollen som ägare eller reservationsansvarig för minst en EA-prenumeration för att kunna köpa en reservation. Alternativet är användbart för företag som vill att reservationer ska köpas av ett centralt team.

En reservationsrabatt gäller bara för resurser som är kopplade till prenumerationer som köpts via Enterprise-, CSP-planer (Cloud Solution Provider), Microsoft-kundavtal eller individuella planer med Betala per användning-priser.

## <a name="scope-reservations"></a>Omfångsanpassa reservationer

Du kan omfångsanpassa en reservation till en prenumeration eller till resursgrupper. Valet av omfång för en reservation medför att det ställe där reservationsbesparingen ska gälla väljs. När du omfångsanpassar reservationen till en resursgrupp gäller reservationsrabatten endast för resursgruppen, inte för hela prenumerationen.

### <a name="reservation-scoping-options"></a>Alternativ för att anpassa omfång för reservation

Du kan välja mellan tre reservationsomfång, beroende på dina behov:

- **Omfång för enskild resursgrupp** – reservationsrabatten tillämpas endast på matchande resurser i den valda resursgruppen.
- **Omfång för enskild prenumeration** – reservationsrabatten tillämpas på matchande resurser i den valda prenumerationen.
- **Delat omfång** – reservationsrabatten tillämpas på matchande resurser i berättigade prenumerationer i faktureringskontexten.
    - För Enterprise-avtalskunder är det registreringen som är faktureringskontext. Det delade reservationsomfånget innehåller flera Active Directory-klientorganisationer i en registrering.
    - För kunder med Microsoft-kundavtal är faktureringsomfånget faktureringsprofilen.
    - För enskilda prenumerationer med betalning per användning är faktureringsomfånget alla berättigade prenumerationer som kontoadministratören har skapat.

När Azure tillämpar reservationsrabatter för din användning bearbetas reservationen i följande ordning:

1. Reservationer som är omfångsanpassade till en resursgrupp
2. Reservationer med enskilt omfång
3. Reservationer med delat omfång

Du kan alltid uppdatera omfånget efter att du har köpt en reservation. Det gör du genom att gå till reservationen, klicka på **Konfiguration** och omfångsanpassa reservationen på nytt. En ny omfångsanpassning av en reservation är inte en kommersiell transaktion. Reservationsperioden ändras inte. Mer information om hur du uppdaterar omfånget finns i avsnittet om att [uppdatera omfånget efter att du har köpt en reservation](manage-reserved-vm-instance.md#change-the-reservation-scope).

![Exempel som visar en ändring av reservationens omfång](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="discounted-subscription-and-offer-types"></a>Rabatterad prenumeration och erbjudandetyper

Reservationsrabatter gäller för följande berättigade prenumerationer och erbjudandetyper.

- Enterprise-avtal (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P)
- MCA-prenumerationer (Microsoft-kundavtal).
- Enskilda prenumerationsavtal med priser enligt principen betala per användning (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P)
- CSP-prenumerationer

Resurser som körs i en prenumeration med andra erbjudandetyper får inte reservationsrabatten.

## <a name="purchase-reservations"></a>Köpa reservationer

Du kan köpa reservationer från Azure-portalen, API:er, PowerShell och CLI. Läs den av följande artiklar som är relevant för dig när du är redo att köpa en reservation:

- [App Service](prepay-app-service.md)
- [Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](/azure/data-explorer/pricing-reserved-capacity)
- [Disklagring](../../virtual-machines/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Programvaruplaner](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure Database for PostgreSQL](../../postgresql/concept-reserved-pricing.md)
- [Azure Database for MySQL](../../mysql/concept-reserved-pricing.md)
- [Azure Database för MariaDB](../../mariadb/concept-reserved-pricing.md)
- [Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md)
- [Virtuella datorer](../../virtual-machines/prepay-reserved-vm-instances.md)

## <a name="buy-reservations-with-monthly-payments"></a>Köp reservationer via månadsbetalningar

Du kan betala för reservationer via månadsbetalningar. När du betalar per månad delar du upp totalkostnaden för reservationen mellan de avtalade månaderna snarare än att betala hela beloppet i förväg. Totalbeloppet för reservationer är detsamma oavsett om du betalar i förväg eller per månad. Det tillkommer inte några extra avgifter när du väljer månadsbetalning.

Om reservationen köps med Microsoft Customer Agreement (MCA) kan månadsbeloppet variera beroende på den aktuella månadens växelkurs i din lokala valuta.

Månadsbetalningar är inte tillgängliga för: Databricks, SUSE Linux-reservationer, Red Hat-planer och Azure Red Hat OpenShift-licenser.

### <a name="view-payments-made"></a>Visa gjorda betalningar

Du kan visa redan gjorda betalningar med hjälp av API:er, i användningsdata och i kostnadsanalyser. För reservationer som betalas varje månad visas frekvenssvärdet som **Återkommande** i användningsdata och i API:et Reservation Charges. För reservationer som betalas i förväg visas värdet som **En gång**.

I kostnadsanalyser visas månatliga inköp i standardvyn. Använd filtret **inköp** för **Debiteringstyp** och **återkommande** för **Frekvens** om du vill se alla inköp. Om du bara vill visa reservationer använder du ett filter för **Reservation**.

![Exempel som visar inköpskostnader för reservationer i en kostnadsanalys](./media/prepare-buy-reservation/cost-analysis.png)

### <a name="exchange-and-refunds"></a>Byten och återbetalning

Precis som med andra reservationer kan du återbetala eller byta ut reservationer med månatlig fakturering. 

När du byter ut en reservation som du betalar för månadsvis måste den totala livstidskostnaden för det nya köpet vara större än de överblivna betalningar som ställs in för den returnerade reservationen. Det finns inga andra gränser och tillkommer inga andra avgifter för byten. Du kan byta ut en reservation som betalats i förväg till en ny reservation som faktureras per månad. Livstidsvärdet för den nya reservationen måste dock vara större än det beräknade värdet för reservationen som returneras.

Om du annullerar en reservation som betalas månadsvis, påförs annullerade framtida betalningar mot återbetalningsgränsen på 50 000 USD.

Du kan läsa mer om byten och återbetalningar i [Självservice vid byten och återbetalningar för Azure-reservationer](exchange-and-refund-azure-reservations.md).

## <a name="reservation-notifications"></a>Reservationsmeddelanden

Beroende på hur du betalar för din Azure-prenumeration skickas reservationsmeddelanden via e-post till följande användare i din organisation. Meddelanden skickas för olika händelser, däribland: 

- Köp
- Kommande upphörande av reservation
- Upphörande
- Förnyelse
- Annullering
- Omfångsändring

För kunder med EA-prenumerationer:

- Meddelanden skickas endast till EA-meddelandekontakterna.
- Användare som läggs till i en reservation med hjälp av Azure RBAC-behörighet (IAM) får inte några e-postaviseringar.

För kunder med enskilda prenumerationer:

- Inköparen får ett inköpsmeddelande.
- Vid inköpstillfället får prenumerationens faktureringskontoägare ett inköpsmeddelande.
- Kontoägaren får alla andra meddelanden.

## <a name="next-steps"></a>Nästa steg

- [Hantera reservationer för Azure-resurser](manage-reserved-vm-instance.md)