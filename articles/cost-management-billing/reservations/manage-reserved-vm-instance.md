---
title: Hantera Azure-reservationer
description: Lär dig hur du hanterar Azure-reservationer. Se anvisningar om hur du ändrar reservationens omfång, delar upp en reservation och optimerar reservationsanvändningen.
ms.service: cost-management-billing
ms.subservice: reservations
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 12/08/2020
ms.author: banders
ms.openlocfilehash: 2cd0611d5701f5ca407afd6d4e3b1b0ae22b6c12
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562981"
---
# <a name="manage-reservations-for-azure-resources"></a>Hantera reservationer för Azure-resurser

När du har köpt en Azure-reservation kan du behöva använda reservationen för en annan prenumeration, ändra vem som får hantera reservationen eller ändra reservationens omfattning. Du kan också dela upp en reservation i två reservationer och använda några av instanserna du har köpt för en annan prenumeration.

Om du har köpt Azure Reserved Virtual Machine Instances kan du ändra reservationens optimeringsinställning. Reservationsrabatten kan användas för virtuella datorer i samma serie, eller så kan du reservera datacenterkapacitet för en viss VM-storlek. Du bör också försöka optimera dina reservationer så att de används fullt ut.

*Den behörighet som krävs för att hantera en reservation skiljer sig från prenumerationsbehörigheten.*

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Reservationsbeställningar och reservationer

När du köper en reservation skapas två objekt: En **reservationsbeställning** och en **reservation**.

Vid inköpstillfället har en reservationsbeställning en underordnad reservation. Med åtgärder som att dela, koppla, delåterbetala eller byta reservationer så skapar du nya reservationer under **reservationsbeställningen**.

Du kan visa en reservationsbeställning genom att gå till **Reservationer**, välja reservationen och sedan **Reservationsbeställnings-ID**.

![Exempel på information om en reservationsbeställning med reservationsbeställnings-id:t ](./media/manage-reserved-vm-instance/reservation-order-details.png)

En reservation ärver sina behörigheter från reservationsbeställningen.

## <a name="change-the-reservation-scope"></a>Ändra reservationens omfång

 Reservationsrabatten gäller för virtuella datorer, SQL-databaser, Azure Cosmos DB eller andra resurser som matchar din reservation och körs i reservationsomfånget. Faktureringskontexten är beroende av den prenumeration som används till att köpa reservationen.

Så här uppdaterar du reservationsomfånget:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** > **Reservationer**.
3. Markera reservationen.
4. Välj **Inställningar** > **Konfiguration**.
5. Ändra omfånget.

Om du ändrar från delat till enskilt omfång kan du bara välja prenumerationer du äger. Du kan bara välja prenumerationer i samma faktureringskontext som reservationen.

Omfånget gäller endast enskilda prenumerationer där du betalar per användning (erbjudandet MS-AZR-0003P eller MS-AZR-0023P), företagserbjudandet MS-AZR-0017P eller MS-AZR-0148P, eller CSP-prenumerationer.

## <a name="who-can-manage-a-reservation-by-default"></a>Vem kan hantera en reservation som standard?

Som standard kan följande användare visa och hantera reservationer:

- Den person som köper en reservation och kontoadministratören för den faktureringsprenumeration som används för att köpa reservationen läggs till i reservationsbeställningen.
- Faktureringsadministratörer för Enterprise-avtal och Microsoft-kundavtal.

Du kan välja mellan två alternativ för att låta andra hantera reservationer:

- Delegera åtkomsthantering för en enskild reservationsbeställning:
    1. Logga in på [Azure-portalen](https://portal.azure.com).
    1. Välj **Alla tjänster** > **Reservationer** så visas de reservationer du har åtkomst till.
    1. Markera den reservation som du vill ge andra användare åtkomst till.
    1. Välj reservationsbeställningen i informationen om reservationer.
    1. Välj **Åtkomstkontroll (IAM)** .
    1. Välj **Lägg till rolltilldelning** > **Roll** > **Ägare**. Du kan välja en annan roll om du vill ge begränsad åtkomst.
    1. Ange e-postadressen för den användare som du vill lägga till som ägare.
    1. Välj användaren och sedan **Spara**.

- Lägg till en användare som faktureringsadministratör för ett Enterprise-avtal eller ett Microsoft-kundavtal:
    - Om du har ett Enterprise-avtal lägger du till användare med rollen _Företagsadministratör_ så att de kan visa och hantera reservationsbeställningar under Enterprise-avtalet. Användare med rollen _Enterprise-administratör (skrivskyddad)_ kan bara visa reservationen. Avdelningsadministratörer och kontoägare kan inte visa reservationer _om de inte_ uttryckligen läggs till i dem via åtkomstkontroll (IAM). Mer information finns i [Hantera Azure Enterprise-roller](../manage/understand-ea-roles.md).

        _Enterprise-administratörer kan bli ägare till en reservationsbeställning och kan lägga till andra användare till en reservation via åtkomstkontroll (IAM)._
    - Med ett Microsoft-kundavtal kan användare med rollen som faktureringsprofilägare eller faktureringsprofildeltagare hantera alla reservationsköp som görs via faktureringsprofilen. Debiteringsprofilläsare och fakturahanterare kan visa alla reservationer som betalas via faktureringsprofilen. Däremot kan de inte göra ändringar i reservationer.
    Mer information finns i [Roller och uppgifter för faktureringsprofiler](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="how-billing-administrators-view-or-manage-reservations"></a>Så här kan faktureringsadministratörer visa och hantera reservationer

1. Gå till **Cost Management + Billing** och välj **Reservationstransaktioner** till vänster på sidan.
2. Om du har nödvändig faktureringsbehörighet kan du visa och hantera reservationer. Om du inte ser några reservationer kontrollerar du att du är inloggad med den Azure AD-klientorganisation som reservationerna skapades i.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dela upp en reservation i två reservationer

 När du har köpt fler än en resursinstans i en reservation kanske du vill tilldela instanserna i reservationen till olika prenumerationer. Som standard har alla instanser ett omfång – antingen en enskild prenumeration, resursgrupp eller delad. Anta att du har köpt en reservation för tio VM-instanser och angett omfånget som prenumeration A. Nu vill du ändra omfånget för sju VM-instanser till prenumeration A och de återstående tre till prenumeration B. Det kan du göra genom att dela upp en reservation. När du har delat en reservation annulleras det ursprungliga ReservationID och två nya reservationer skapas. Delning påverkar inte reservationsordningen – delningen leder inte till en ny kommersiell transaktion och de nya reservationerna har samma slutdatum som den som delades.

 Du kan dela upp en reservation i två reservationer via PowerShell, CLI eller API:et.

### <a name="split-a-reservation-by-using-powershell"></a>Dela upp en reservation med hjälp av PowerShell

1. Hämta reservationsbeställnings-id:t med följande kommando:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Hämta information om en reservation:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Dela upp reservationen i två och fördela instanserna:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Du kan uppdatera omfattningen med följande kommando:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](exchange-and-refund-azure-reservations.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Ändra optimeringsinställningen för reserverade VM-instanser

 När du köper en reserverad VM-instans väljer du mellan att prioritera instansens kapacitet eller flexibilitet gällande storleken. Om du väljer att prioritera flexibilitet gällande storleken använder du reservationsrabatten för andra virtuella datorer i samma [VM-storleksgrupp](../../virtual-machines/reserved-vm-instance-size-flexibility.md). Kapacitetsprioritet prioriterar datacenterkapaciteten i dina distributioner. Det här alternativet gör det säkrare att du kan starta VM-instanserna när du behöver dem.

När reservationens omfång är delat prioriteras som standard flexibilitet i storleken. Datacenterkapaciteten prioriteras inte för VM-distributioner.

För reservationer med enskilt omfång kan du optimera reservationen för kapacitet snarare än flexibilitet gällande instansstorleken.

Så här uppdaterar du optimeringsinställningen för en reservation:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** > **Reservationer**.
3. Välj reservationen.
4. Välj **Inställningar** > **Konfiguration**.
  ![Exempel som visar konfigurationsobjektet](./media/manage-reserved-vm-instance/add-product03.png)
5. Ändra inställningen **Optimera för**.
  ![Exempel som visar inställningen Optimera](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>Optimera reservationsanvändningen

Du sparar bara pengar med Azure-reservationer om du har hög användningsgrad för resurserna. När du köper en reservation betalar du i praktiken för en möjlig resursanvändning på 100 % under ett eller tre år. Försök att maximera reservationen så att du får mesta möjliga användning och besparingar. I följande avsnitt beskrivs hur du övervakar en reservation och optimerar användningen.

### <a name="view-reservation-use-in-the-azure-portal"></a>Visa reservationsanvändning i Azure-portalen

Ett sätt att visa användningen av en reservation är i Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Alla tjänster** > [**Reservationer**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) och läs av värdet för **Utnyttjande (%)** för reservationen.  
  ![Bild som visar listan med reservationer](./media/manage-reserved-vm-instance/reservation-list.png)
3. Välj en reservation.
4. Granska trenden i reservationsanvändningen över tid.
  ![Bild som visar reservationsanvändning ](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Visa reservationsanvändning med API:et

Om du är en EA-kund (Enterprise Agreement) kan du programmatiskt visa hur organisationens reservationer används. Du ser oanvända reservationer via användningsdata. När du granskar reservationskostnaderna ska du ha i åtanke att informationen är uppdelad mellan faktiska kostnader och periodiserade kostnader. Den faktiska kostnaden hjälper dig att stämma av din månadsfaktura. I den ingår även kostnader för reservationsköp och information om hur reservationen används. Periodiserad kostnad är som faktiska kostnader förutom att det effektiva priset för reservationsanvändningen beräknas proportionellt. Oanvända reservationstimmar visas i den periodiserade kostnadsinformationen. Mer information om användningsinformation för EA-kunder finns i [Hämta kostnader för och användning av EA-reservationer](understand-reserved-instance-usage-ea.md).

För andra prenumerationstyper använder du API:et [Reservationsöversikter – visa per reservationsbeställning och reservation](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

### <a name="optimize-your-reservation"></a>Optimera din reservation

Gör så här om du märker att organisationens reservationer underutnyttjas:

- Se till att de virtuella datorer som organisationen skapar matchar reservationens VM-storlek.
- Se till att flexibilitet gällande instansernas storlek är aktiverad. Mer information finns i [Hantera reservationer – ändra optimeringsinställningen för reserverade VM-instanser](#change-optimize-setting-for-reserved-vm-instances).
- Ändra omfånget för reservationen till _delad_ så att den kan användas bredare. Mer information finns i [Ändra omfånget för en reservation](#change-the-reservation-scope).
- Överväg att byta in den mängd som inte används. Mer information finns i [Uppsägningar och utbyten](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om Azure-reservationer i följande artiklar:

- [Vad är Azure-reservationer?](save-compute-costs-reservations.md)

Köpa en tjänstplan:
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Betala i förväg för Azure Cosmos DB-resurser med reserverad Azure Cosmos DB-kapacitet](../../cosmos-db/cosmos-db-reserved-capacity.md)

Köpa en programvaruplan:
- [Förskottsbetala för Red Hat-programvaruplaner från Azure Reservations](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Förskottsbetala för SUSE-programvaruplaner från Azure Reservations](../../virtual-machines/linux/prepay-suse-software-charges.md)

Förstå rabatter och användning:
- [Förstå hur VM-reservationsrabatten tillämpas](../manage/understand-vm-reservation-charges.md)
- [Förstå hur rabatten i programvaruplanen för Red Hat Enterprise Linux tillämpas](understand-rhel-reservation-charges.md)
- [Förstå hur rabatten i programvaruplanen för SUSE Linux Enterprise tillämpas](understand-suse-reservation-charges.md)
- [Förstå hur andra reservationsrabatter tillämpas](understand-reservation-charges.md)
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
- [Kostnader för Windows-programvara som inte ingår i reservationer](reserved-instance-windows-software-costs.md)
