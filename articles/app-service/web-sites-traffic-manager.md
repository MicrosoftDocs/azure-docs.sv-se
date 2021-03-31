---
title: Styra trafik med Traffic Manager
description: Hitta metod tips för att konfigurera Azure Traffic Manager när du integrerar det med Azure App Service.
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.topic: article
ms.date: 02/25/2016
ms.custom: seodec18
ms.openlocfilehash: 040f84288c66f4506919e775b9ea41324b617cfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80437899"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Styra Azure App Service trafik med Azure Traffic Manager
> [!NOTE]
> Den här artikeln innehåller sammanfattnings information för Microsoft Azure Traffic Manager som är relaterad till Azure App Service. Mer information om Azure Traffic Manager hittar du genom att besöka länkarna i slutet av den här artikeln.
> 
> 

## <a name="introduction"></a>Introduktion
Du kan använda Azure Traffic Manager för att styra hur begäranden från webbklienter distribueras till appar i Azure App Service. När App Service-slutpunkter läggs till i en Azure Traffic Manager-profil kan Azure Traffic Manager hålla reda på status för dina App Service-appar (som de körs, stoppas eller har tagits bort) så att den kan bestämma vilka av slutpunkterna som ska ta emot trafik.

## <a name="routing-methods"></a>Routningsmetoder
Azure Traffic Manager använder fyra olika metoder för routning. Dessa metoder beskrivs i följande lista som de gäller för Azure App Service.

* **[Prioritet](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method):** Använd en primär app för all trafik och ange säkerhets kopior om den primära appen eller säkerhets kopierings programmen inte är tillgängliga.
* **[Viktat](../traffic-manager/traffic-manager-routing-methods.md#weighted):** distribuera trafik över en uppsättning appar, antingen jämnt eller enligt de vikter som du definierar.
* **[Prestanda](../traffic-manager/traffic-manager-routing-methods.md#performance):** när du har appar på olika geografiska platser använder du den "närmast" appen med avseende på den lägsta nätverks fördröjningen.
* **[Geografisk](../traffic-manager/traffic-manager-routing-methods.md#geographic):** dirigera användare till specifika appar baserat på vilken geografisk plats deras DNS-fråga kommer från. 

Mer information finns i [Traffic Manager metoder för routning](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>App Service och Traffic Manager profiler
Om du vill konfigurera kontrollen av App Service app-trafik skapar du en profil i Azure Traffic Manager som använder någon av de fyra belastnings Utjämnings metoder som beskrivs ovan och lägger sedan till slut punkterna (i det här fallet App Service) som du vill styra trafiken till profilen för. Din app-status (körs, stoppad eller borttagen) förmedlas regelbundet till profilen så att Azure Traffic Manager kan dirigera trafiken i enlighet med detta.

Tänk på följande när du använder Azure Traffic Manager med Azure:

* För appar som bara distribueras inom samma region tillhandahåller App Service redan funktioner för redundans och resursallokering utan hänsyn till app-läge.
* För distributioner i samma region som använder App Service tillsammans med en annan Azure-moln tjänst kan du kombinera båda typerna av slut punkter för att aktivera hybrid scenarier.
* Du kan bara ange en App Service slut punkt per region i en profil. När du väljer en app som en slut punkt för en region blir de återstående apparna i den regionen otillgängliga för val av profilen.
* De App Service slut punkter som du anger i en Azure Traffic Manager-profil visas under avsnittet **domän namn** på sidan Konfigurera för appen i profilen, men det går inte att konfigurera där.
* När du har lagt till en app i en profil **visas URL:** en för den anpassade domänen i appens Portal sida om du har konfigurerat en. Annars visas URL: en för Traffic Manager profil (till exempel `contoso.trafficmanager.net` ). Både det direkta domän namnet för appen och Traffic Manager URL visas på appens konfigurations sida under avsnittet **domän namn** .
* Dina anpassade domän namn fungerar som förväntat, men förutom att lägga till dem i dina appar måste du även konfigurera DNS-mappningen så att den pekar på Traffic Manager-URL: en. Information om hur du konfigurerar en anpassad domän för en App Service-app finns i [Konfigurera ett anpassat domän namn i Azure App Service med Traffic Manager-integrering](configure-domain-traffic-manager.md).
* Du kan bara lägga till appar som är i standard-eller Premium-läge till en Azure Traffic Manager-profil.
* Om du lägger till en app i en Traffic Manager-profil startas appen om.

## <a name="next-steps"></a>Nästa steg
En konceptuell och teknisk översikt över Azure Traffic Manager finns i [Traffic Manager översikt](../traffic-manager/traffic-manager-overview.md).


