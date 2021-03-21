---
title: Ansluta till ett peer-nätverk i Azure Lab Services | Microsoft Docs
description: Lär dig hur du ansluter ditt labb nätverk till ett annat nätverk som en peer. Du kan till exempel ansluta ditt lokala organisations-/universitets nätverk med Labbets virtuella nätverk i Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 99ea72f11c8f389a15171a1fe3b376646494903a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434251"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Anslut ditt labb nätverk till ett peer-virtuellt nätverk i Azure Lab Services

Den här artikeln innehåller information om peering The Labs Network med ett annat nätverk.

## <a name="overview"></a>Översikt

Med peering för virtuella nätverk kan du sömlöst ansluta virtuella Azure-nätverk. När de virtuella nätverken har peer-kopplats visas de som ett nätverk för anslutningsändamål. Trafiken mellan virtuella datorer i peer-kopplat virtuella nätverk dirigeras via Microsoft stamnät-infrastrukturen, ungefär som trafik dirigeras mellan virtuella datorer i samma virtuella nätverk, via privata IP-adresser. Mer information finns i [peering för virtuella nätverk](../virtual-network/virtual-network-peering-overview.md).

Du kan behöva ansluta ditt labbs nätverk till ett peer-virtuellt nätverk i vissa scenarier, inklusive följande:

- De virtuella datorerna i labbet har program vara som ansluter till lokala licens servrar för att erhålla licens.
- De virtuella datorerna i labbet behöver åtkomst till data uppsättningar (eller andra filer) på Universitys nätverks resurser.

Vissa lokala nätverk är anslutna till Azure Virtual Network antingen via [ExpressRoute](../expressroute/expressroute-introduction.md) eller [Virtual Network Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). Dessa tjänster måste konfigureras utanför Azure Lab Services. Mer information om hur du ansluter ett lokalt nätverk till Azure med hjälp av ExpressRoute finns i [Översikt över ExpressRoute](../expressroute/expressroute-introduction.md). För lokal anslutning med en Virtual Network Gateway måste gatewayen, det angivna virtuella nätverket och labb kontot vara i samma region.

> [!NOTE]
> När du skapar en Azure-Virtual Network som ska peer-kopplas med ett labb konto är det viktigt att du förstår hur det virtuella nätverkets region påverkar hur labb skapas.  Mer information finns i administratörs guidens avsnitt på [regions\locations](./administrator-guide.md#regionslocations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurera när labb kontot skapas

När du skapar ett nytt [labb konto](tutorial-setup-lab-account.md)kan du välja ett befintligt virtuellt nätverk som visas i list rutan **peer Virtual Network** på fliken **Avancerat** .  I listan visas endast virtuella nätverk i samma region som labb kontot. Det valda virtuella nätverket är anslutet (peer-kopplat) till labb som skapats under labb kontot.  Alla virtuella datorer i labb som skapas efter den här ändringen kommer att ha åtkomst till resurserna i det peer-virtuella nätverket.

![Välj VNet till peer](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Adressintervall

Det finns också ett alternativ för att tillhandahålla **adress intervall** för de virtuella datorerna i labbet.  Egenskapen för **adress intervall** gäller endast om ett **virtuellt dator nätverk** är aktiverat för labbet. Om adress intervallet anges skapas alla virtuella datorer i labben under labb kontot i det adress intervallet. Adress intervallet ska vara i CIDR-notation (till exempel 10.20.0.0/20) och inte överlappa några befintliga adress intervall.  När du tillhandahåller ett adress intervall är det viktigt att tänka på hur många *labb* som ska skapas och tillhandahålla ett adress intervall för att hantera det. Labb tjänster förutsätter högst 512 virtuella datorer per labb.  Ett IP-intervall med "/23" kan till exempel endast skapa ett labb.  Ett intervall med "/21" gör det möjligt att skapa fyra labb.

Om **adress intervallet** inte anges använder Lab-tjänsterna det standard adress intervall som ges av Azure när det virtuella nätverket skapas för peer-kopplas till det virtuella nätverket.  Intervallet är ofta något som 10. x. 0,0/16.  Detta kan leda till överlappande IP-intervall, så se till att antingen ange ett adress intervall i labb inställningarna eller kontrol lera adress intervallet för det virtuella nätverket som peer-kopplas.

> [!NOTE]
> Det går inte att skapa labb om Lab-kontot är peer-kopplat till ett virtuellt nätverk, men har för många IP-adressintervall. Du kan ta slut på utrymme i adress intervallet om det finns för många labb i labb kontot (varje labb använder 512-adresser). 
> 
> Om det inte går att skapa labbet kontaktar du ditt labb kontos ägare/-administratör och begär att adress intervallet ska ökas. Administratören kan öka adress intervallet med hjälp av stegen som beskrivs i avsnittet [Ange ett adress intervall för virtuella datorer i ett labb konto](#specify-an-address-range-for-vms-in-the-lab-account) . 

## <a name="configure-after-the-lab-account-is-created"></a>Konfigurera när labb kontot har skapats

Du kan aktivera samma egenskap på fliken labb **konfiguration** på sidan **labb konto** om du inte har konfigurerat ett peer-nätverk när du skapade labb kontot. Ändringar som görs i den här inställningen gäller bara för de labb som skapas efter ändringen. Som du kan se i avbildningen kan du aktivera eller inaktivera ett **virtuellt dator nätverk** för labb i labb kontot.

![Aktivera eller inaktivera VNet-peering när labbet har skapats](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

När du väljer ett virtuellt nätverk för det **virtuella peer-nätverket** är alternativet **Tillåt att labb skaparen att välja labb plats** inaktiverat. Det beror på att labb i labb kontot måste finnas i samma region som labb kontot för att de ska kunna ansluta till resurser i det virtuella peer-nätverket.

> [!IMPORTANT]
> Inställningen för peer-kopplat virtuellt nätverk gäller endast labb som skapas när ändringen har gjorts, inte i de befintliga labben.


## <a name="specify-an-address-range-for-vms-in-the-lab-account"></a>Ange ett adress intervall för virtuella datorer i labb kontot
Följande procedur innehåller steg för att ange ett adress intervall för virtuella datorer i labbet. Om du uppdaterar intervallet som du har angett gäller det ändrade adress intervallet endast för virtuella datorer som skapas när ändringen har gjorts. 

Här följer några begränsningar när du anger adress intervallet som du bör ha i åtanke. 

- Prefixet måste vara mindre än eller lika med 23. 
- Om ett virtuellt nätverk är peer-kopplat till labb kontot kan det angivna adress intervallet inte överlappa med adress intervallet från det peer-kopplade virtuella nätverket.

1. På sidan **labb konto** väljer du **labb inställningar** på den vänstra menyn.
2. I fältet **adress intervall** anger du adress intervall för virtuella datorer som ska skapas i labbet. Adress intervallet bör vara i CIDR-notation (Classless Inter-Domain routing) (exempel: 10.20.0.0/23). Virtuella datorer i labbet skapas i det här adress intervallet.
3. Välj **Spara** i verktygsfältet. 

    ![Konfigurera adress intervall](./media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

- [Tillåt labbets skapare att välja plats för labbet](allow-lab-creator-pick-lab-location.md)
- [Koppla ett delat avbildnings galleri till ett labb](how-to-attach-detach-shared-image-gallery.md)
- [Lägg till en användare som labb ägare](how-to-add-user-lab-owner.md)
- [Visa brand Väggs inställningar för ett labb](how-to-configure-firewall-settings.md)
- [Konfigurera andra inställningar för ett labb](how-to-configure-lab-accounts.md)