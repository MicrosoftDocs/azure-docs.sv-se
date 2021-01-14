---
title: Hantera slutpunkter i Azure Traffic Manager | Microsoft Docs
description: Den här artikeln beskriver hur du lägger till, tar bort, aktiverar och inaktiverar slutpunkter från Azure Traffic Manager.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: duau
ms.openlocfilehash: 3d5a888bf132725ff64c2a128b77f0257f435585
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184262"
---
# <a name="add-disable-enable-or-delete-endpoints"></a>Lägga till, inaktivera, aktivera eller ta bort slutpunkter

Funktionen Web Apps i Azure App Service tillhandahåller redan trafikroutningsfunktioner för redundans och resursallokering för webbplatser inom ett datacenter, oavsett webbplatsläge. Med Azure Traffic Manager kan du konfigurera trafikroutning för redundans och resursallokering för webbplatser och molntjänster i olika datacenter. Det första steget om du vill att den här funktionen ska vara tillgänglig är att lägga till molntjänstens eller webbplatsens slutpunkt i Traffic Manager.

Du kan också inaktivera enskilda slutpunkter som ingår i en Traffic Manager-profil. Om du inaktiverar en slutpunkt är den fortfarande en del av profilen, men profilen fungerar som om slutpunkten inte ingår i den. Den här åtgärden är användbar för att tillfälligt ta bort en slutpunkt som är i underhållsläge eller som omdistribueras. När slutpunkten är igång igen kan den aktiveras.

> [!NOTE]
> Inaktiveringen av en slutpunkt har inget att göra med dess distributionsstatus i Azure. En felfri slutpunkt förblir aktiv och kan ta emot trafik även om den är inaktiverad i Traffic Manager. Dessutom påverkar inte inaktiveringen av en slutpunkt i en profil dess status i en annan profil.

## <a name="to-add-a-cloud-service-or-an-app-service-endpoint-to-a-traffic-manager-profile"></a>För att lägga till en molntjänst eller en apptjänstslutpunkt till en Traffic Manager-profil

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare.
2. Leta efter namnet på **Traffic Manager-profilen** som du vill modifiera och klicka sedan på Traffic Manager-profilen i resultaten som visas.
3. Klicka på **Slutpunkter** på bladet **Traffic Manager-profil** i avsnittet **Inställningar**.
4. Klicka på **Lägg till** på bladet **Slutpunkter** som visas.
5. Ange enligt anvisningarna på bladet **Lägg till slutpunkt**:
    1. Klicka på **Azure-slutpunkt** för **Typ**.
    2. Ange ett **Namn** som du vill använda för att identifiera den här slutpunkten.
    3. För **Målresurstyp** väljer du en lämplig resurstyp från listrutan.
    4. För **Målresurs** klickar du på **Välj...** för att visa resurser i samma prenumeration på **bladet Resurser**. På bladet för **Resurser** som visas väljer du den tjänst som du vill lägga till som den första slutpunkten.
    5. För **Prioritet** väljer du **1**. Detta resulterar i all trafik går igenom den här slutpunkten, förutsatt att den är felfri.
    6. Behåll **Lägg till som inaktiverad** som avmarkerat.
    7. Klicka på **OK**
6.  Upprepa steg 4 och 5 för att lägga till nästa Azure-slutpunkt. Se till att lägga till den med dess **Prioritet**-värde angivet som **2**.
7.  När båda slutpunkterna har lagts till visas de på bladet **Traffic Manager-profil** tillsammans med dess övervakningsstatus som är **Online**.

> [!NOTE]
> När du lägger till eller tar bort en slutpunkt från en profil med trafikroutningsmetoden *Redundans* är det möjligt att prioritetslistan för redundans inte längre är i den ordning du önskar. Du kan justera ordningen för prioritetslistan för redundans från konfigurationssidan. Mer information finns i [Konfigurera trafikroutning för redundans](./traffic-manager-configure-priority-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Så här inaktiverar du en slutpunkt

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare.
2. Leta efter namnet på **Traffic Manager-profilen** som du vill modifiera och klicka sedan på Traffic Manager-profilen i resultaten som visas.
3. Klicka på **Slutpunkter** på bladet **Traffic Manager-profil** i avsnittet **Inställningar**. 
4. Klicka på den slut punkt som du vill inaktivera.
5. På bladet **Slutpunkt** ändrar du statusen för slutpunkten till **Inaktiverad** och sedan klickar du på **Spara**.
6. Klienter fortsätter att skicka trafik till slutpunkten under hela livstiden (TTL). Du kan ändra TTL-värdet från konfigurationssidan för Traffic Manager-profilen.

## <a name="to-enable-an-endpoint"></a>Så här aktiverar du en slutpunkt

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare.
2. Leta efter namnet på **Traffic Manager-profilen** som du vill modifiera och klicka sedan på Traffic Manager-profilen i resultaten som visas.
3. Klicka på **Slutpunkter** på bladet **Traffic Manager-profil** i avsnittet **Inställningar**. 
4. Klicka på den slut punkt som du vill aktivera.
5. På bladet **Slutpunkt** ändrar du statusen för slutpunkten till **Aktiverad** och sedan klickar du på **Spara**.
6. Klienter fortsätter att skicka trafik till slutpunkten under hela livstiden (TTL). Du kan ändra TTL-värdet från konfigurationssidan för Traffic Manager-profilen.

## <a name="to-delete-an-endpoint"></a>Ta bort en slutpunkt

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare.
2. Leta efter namnet på **Traffic Manager-profilen** som du vill modifiera och klicka sedan på Traffic Manager-profilen i resultaten som visas.
3. Klicka på **Slutpunkter** på bladet **Traffic Manager-profil** i avsnittet **Inställningar**. 
4. Klicka på den slut punkt som du vill ta bort.
5. Klicka på **ta bort** i **slut punkts** bladet


## <a name="next-steps"></a>Nästa steg

* [Hantera Traffic Manager-profiler](traffic-manager-manage-profiles.md)
* [Konfigurera routningsmetoder](./traffic-manager-configure-priority-routing-method.md)
* [Felsöka degraderat Traffic Manager-tillstånd](traffic-manager-troubleshooting-degraded.md)
* [Prestandaöverväganden för Traffic Manager](traffic-manager-performance-considerations.md)
* [Åtgärder i Traffic Manager (REST-API-referens)](/previous-versions/azure/reference/hh758255(v=azure.100))