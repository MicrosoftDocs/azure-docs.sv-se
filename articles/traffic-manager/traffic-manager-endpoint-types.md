---
title: Traffic Manager slut punkts typer | Microsoft Docs
description: I den här artikeln beskrivs olika typer av slut punkter som kan användas med Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: 7686f2f97da0113704216dcab741c063a80d3136
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051235"
---
# <a name="traffic-manager-endpoints"></a>Traffic Manager-slutpunkter

Med Microsoft Azure Traffic Manager kan du styra hur nätverks trafiken distribueras till program distributioner som körs i olika data Center. Du konfigurerar varje programdistribution som en ”slutpunkt” i Traffic Manager. När Traffic Manager tar emot en DNS-begäran väljer den en tillgänglig slutpunkt att returnera i DNS-svaret. Traffic Manager baserar valet för aktuell slut punkts status och Traffic routing-metoden. Mer information finns i [hur Traffic Manager fungerar](traffic-manager-how-it-works.md).

Det finns tre typer av slutpunkter som stöds av Traffic Manager:

* **Azure-slutpunkter** används för tjänster som värdhanteras i Azure.
* **Externa slut punkter** används för IPv4/IPv6-adresser, FQDN eller för tjänster som ligger utanför Azure. De här Services kan antingen vara lokalt eller med en annan värd leverantör.
* **Kapslade slutpunkter** används till att kombinera Traffic Manager-profiler för att skapa mer flexibla trafikdirigeringsscheman som stöd för större, mer komplexa distributioner.

Det finns ingen begränsning för hur slut punkter av olika typer kombineras i en enda Traffic Manager profil. Varje profil kan innehålla valfri blandning av slutpunktstyper.

I följande avsnitt beskrivs varje typ av slut punkt i större djup.

## <a name="azure-endpoints"></a>Azure-slutpunkter

Azure-slutpunkter används för Azure-baserade tjänster i Traffic Manager. Följande Azure-resurs typer stöds:

* PaaS Cloud Services.
* Web Apps
* Web App-platser
* PublicIPAddress-resurser (som kan anslutas till virtuella datorer antingen direkt eller via en Azure Load Balancer). PublicIpAddress måste ha ett DNS-namn tilldelat för att kunna användas i en Traffic Manager-profil.

PublicIPAddress-resurser är Azure Resource Manager resurser. De finns inte i den klassiska distributions modellen och stöds bara i Traffic Manager Azure Resource Manager upplevelser. Andra slut punkts typer stöds via både Resource Manager och den klassiska distributions modellen.

När du använder Azure-slutpunkter identifierar Traffic Manager när en webbapp stoppas och startas. Status visas i slut punkts status. Mer information finns i [Traffic Manager slut punkts övervakning](traffic-manager-monitoring.md#endpoint-and-profile-status) . När den underliggande tjänsten stoppas kontrollerar Traffic Manager inte slut punkts hälso kontroller eller dirigera trafik till slut punkten. Inga Traffic Manager fakturerings händelser inträffar för den stoppade instansen. När tjänsten startas om fortsätter faktureringen och slut punkten är tillgänglig för att ta emot trafik. Den här identifieringen gäller inte för PublicIpAddress-slutpunkter.

## <a name="external-endpoints"></a>Externa slut punkter

Externa slut punkter används antingen för IPv4/IPv6-adresser, FQDN eller för tjänster utanför Azure. Med hjälp av IPv4/IPv6-adress slut punkter kan Traffic Manager kontrol lera hälso tillståndet för slut punkter utan att ett DNS-namn krävs för dem. Det innebär att Traffic Manager kan svara på frågor med A/AAAA-poster när den returnerar slut punkten i ett svar. Tjänster utanför Azure kan inkludera en tjänst som finns lokalt eller med en annan provider. Externa slut punkter kan användas individuellt eller kombineras med Azure-slutpunkter i samma Traffic Manager profil. Undantaget är för slut punkter som har angetts som IPv4-eller IPv6-adresser, som endast kan vara externa slut punkter. Genom att kombinera Azure-slutpunkter med externa slut punkter kan du använda olika scenarier:

* Ge ökad redundans för ett befintligt lokalt program i antingen en aktiv-aktiv eller aktiv-passiv växlings modell med hjälp av Azure. 
* Dirigera trafik till slut punkter som inte har ett DNS-namn som är associerat med dem. Minskar också den övergripande fördröjningen för DNS-sökning genom att ta bort behovet av att köra en andra DNS-fråga för att hämta en IP-adress till ett DNS-namn som returneras.
* Minska program svars tiden för användare runtom i världen, utöka ett befintligt lokalt program till andra geografiska platser i Azure. Mer information finns i [Traffic Manager "prestanda" i trafik flödet](traffic-manager-routing-methods.md#performance).
* Ge mer kapacitet för ett befintligt lokalt program, antingen kontinuerligt eller som en "burst-till-moln"-lösning för att möta en insamling i efter frågan med hjälp av Azure.

I vissa fall är det bra att använda externa slut punkter för att referera till Azure-tjänster. Se [vanliga frågor och svar](traffic-manager-faqs.md#traffic-manager-endpoints) . Hälso kontrollerna debiteras enligt priset för Azure-slutpunkter, inte för de externa slut punkterna. Till skillnad från Azure-slutpunkter, om du stoppar eller tar bort den underliggande tjänsten fortsätter hälso kontroll faktureringen. Faktureringen avslutas när du inaktiverar eller tar bort slut punkten i Traffic Manager.

## <a name="nested-endpoints"></a>Kapslade slut punkter

Kapslade slut punkter kombinerar flera Traffic Manager profiler för att skapa flexibla scheman för trafik dragning som stöd för större och komplexa distributioner. Med kapslade slut punkter läggs en "underordnad" profil till som en slut punkt till en överordnad profil. Både underordnade och överordnade profiler kan innehålla andra slut punkter av valfri typ, inklusive andra kapslade profiler. 

Mer information finns i [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web Apps som slut punkter

Några mer överväganden gäller när du konfigurerar Web Apps som slut punkter i Traffic Manager:

1. Endast Web Apps på SKU: n (standard) eller senare är berättigade att användas med Traffic Manager. Försök att lägga till en webbapp med en lägre SKU Miss lyckas. Att nedgradera SKU för en befintlig webbapp resulterar i Traffic Manager inte längre skickar trafik till webbappen. Mer information om vilka planer som stöds finns i [app Services planer](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. När en slut punkt tar emot en HTTP-begäran använder den "Host"-huvudet i begäran för att avgöra vilken webbapp som ska betjäna begäran. Värd huvudet innehåller det DNS-namn som används för att starta begäran, till exempel "contosoapp.azurewebsites.net". Om du vill använda ett annat DNS-namn med din webbapp måste DNS-namnet registreras som ett anpassat domän namn för appen. När du lägger till en Web App-slutpunkt som en Azure-slutpunkt registreras DNS-namnet för Traffic Managers profilen automatiskt för appen. Registreringen tas bort automatiskt när slut punkten tas bort.
3. Varje Traffic Manager profil får ha högst en webbapp från varje Azure-region. För att kringgå den här begränsningen kan du konfigurera en webbapp som en extern slut punkt. Mer information finns i [vanliga frågor och svar](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Aktivera och inaktivera slut punkter

Att inaktivera en slut punkt i Traffic Manager kan vara användbart för att tillfälligt ta bort trafik från en slut punkt som är i underhålls läge eller som omdistribueras. När slut punkten körs igen kan den aktive ras igen.

Du kan aktivera eller inaktivera slut punkter via Traffic Manager Portal, PowerShell, CLI eller REST API.

> [!NOTE]
> Att inaktivera en Azure-slutpunkt har inget att göra med distributions statusen i Azure. En Azure-tjänst (till exempel en virtuell dator eller webbapp körs och kan ta emot trafik även om den är inaktive rad i Traffic Manager. Trafiken kan åtgärdas direkt till tjänst instansen i stället för via Traffic Manager profilens DNS-namn. Mer information finns i [hur Traffic Manager fungerar](traffic-manager-how-it-works.md).

Den aktuella behörigheten för varje slut punkt för att ta emot trafik beror på följande faktorer:

* Profil status (aktive rad/inaktive rad)
* Slut punkts status (aktive rad/inaktive rad)
* Resultatet av hälso kontrollerna för den slut punkten

Mer information finns i [Traffic Manager slut punkts övervakning](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Eftersom Traffic Manager fungerar på DNS-nivå, kan det inte påverka befintliga anslutningar till någon slut punkt. När en slut punkt inte är tillgänglig dirigerar Traffic Manager nya anslutningar till en annan tillgänglig slut punkt. Värden bakom den inaktiverade eller felaktiga slut punkten kan dock fortsätta att ta emot trafik via befintliga anslutningar tills dessa sessioner avslutas. Program bör begränsa sessionens varaktighet så att trafik kan tömmas från befintliga anslutningar.

Om alla slut punkter i en profil inaktive ras eller om själva profilen är inaktive rad skickar Traffic Manager ett "NXDOMAIN"-svar till en ny DNS-fråga.

## <a name="faqs"></a>Vanliga frågor och svar

* [Kan jag använda Traffic Manager med slut punkter från flera prenumerationer?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Kan jag använda Traffic Manager med moln tjänstens mellanlagrings platser?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [Stöder Traffic Manager IPv6-slutpunkter?](./traffic-manager-faqs.md#does-traffic-manager-support-ipv6-endpoints)

* [Kan jag använda Traffic Manager med mer än en webbapp i samma region?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Hur gör jag för att flytta min Traffic Managers profils Azure-slutpunkter till en annan resurs grupp?](./traffic-manager-faqs.md#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur Traffic Manager fungerar](traffic-manager-how-it-works.md).
* Lär dig mer om Traffic Manager [slut punkts övervakning och automatisk redundans](traffic-manager-monitoring.md).
* Lär dig mer om att Traffic Manager [metoder för trafik cirkulation](traffic-manager-routing-methods.md).