---
title: Konfigurera routningsmetoden för prestanda trafik med Azure Traffic Manager | Microsoft Docs
description: Den här artikeln förklarar hur du konfigurerar Traffic Manager för att dirigera trafik till slut punkten med lägsta latens
services: traffic-manager
manager: twooley
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: duau
ms.openlocfilehash: 46d71a38b79f449084f7353527f2dfb05d5b92a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994918"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Konfigurera cirkulations metoden för prestanda trafik

Med routningsmetoden för prestanda trafik kan du dirigera trafik till slut punkten med den lägsta svars tiden från klientens nätverk. Normalt är data centret med lägsta latens närmast det geografiska avståndet. Den här routningsmetoden för trafik kan inte ta hänsyn till förändringar i real tid i nätverks konfigurationen eller belastningen.

##  <a name="to-configure-performance-routing-method"></a>Konfigurera routningsmetod för prestanda

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/). 
2. I portalens sökfält söker du efter **Traffic Manager profiler** och klickar sedan på det profil namn som du vill konfigurera routningsmetod för.
3. På bladet **Traffic Manager profil** kontrollerar du att både de moln tjänster och webbplatser som du vill inkludera i konfigurationen finns.
4. I avsnittet **Inställningar** klickar du på **konfiguration** och slutför i **konfigurations** bladet på följande sätt:
    1. För routningsmetoden för **trafikroutning** **väljer du** **prestanda**.
    2. Ange **Inställningar för slut punkts övervakaren** identisk för alla slut punkter i den här profilen enligt följande:
        1. Välj lämpligt **protokoll** och ange **port** numret. 
        2. För **sökväg** anger du ett snedstreck */* . Om du vill övervaka slut punkter måste du ange en sökväg och ett fil namn. Ett snedstreck "/" är en giltig post för den relativa sökvägen och innebär att filen finns i rot katalogen (standard).
        3. Klicka på **Spara** längst upp på sidan.
5.  Testa ändringarna i konfigurationen på följande sätt:
    1.  Sök efter namnet på Traffic Manager profilen i portalens sökfält och klicka på Traffic Manager profilen i resultaten som visas.
    2.  I bladet **Traffic Manager** profil klickar du på **Översikt**.
    3.  Bladet **Traffic Manager profil** visar DNS-namnet för din nyligen skapade Traffic Manager-profil. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) för att dirigeras till den högra slut punkten som fastställs av typen av routning. I det här fallet dirigeras alla begär anden till slut punkten med den lägsta svars tiden från klientens nätverk.
6. När din Traffic Manager-profil fungerar redigerar du DNS-posten på den auktoritativa DNS-servern för att peka ditt företags domän namn till Traffic Manager domän namnet.

![Konfigurera routningsmetoden för prestanda trafik med hjälp av Traffic Manager][1]

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [routningsmetoden för viktad trafik](traffic-manager-configure-weighted-routing-method.md).
- Lär dig mer om [prioritetsroutningsmetoden](traffic-manager-configure-priority-routing-method.md).
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
- Lär dig hur du [testar Traffic Manager inställningar](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png