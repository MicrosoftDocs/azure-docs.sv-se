---
title: Överväganden för nätverkstopologier för Azure Active Directory-programproxy
description: Behandlar nätverks sto pol faktorer när du använder Azure Active Directory-programproxy.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 2873bd9668bfba887ad9add061e68f36a747d5b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492327"
---
# <a name="optimize-traffic-flow-with-azure-active-directory-application-proxy"></a>Optimera trafikflöde med Azure Active Directory-programproxy

Den här artikeln förklarar hur du optimerar faktorer för trafikflöde och nätverkstopologier när du använder Azure Active Directory (Azure AD) Application Proxy för att publicera och komma åt dina program via en fjärr anslutning.

## <a name="traffic-flow"></a>Trafikflöde

När ett program publiceras via Azure AD-programproxy flödar trafik från användarna till programmen genom tre anslutningar:

1. Användaren ansluter till den offentliga Azure AD-programproxy-tjänstens offentliga slut punkt på Azure
1. Tjänsten Application Proxy ansluter till Application Proxy Connector
1. Application Proxy Connector ansluter till mål programmet

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-three-hops.png" alt-text="Diagram som visar trafikflödet från användare till mål programmet." lightbox="./media/application-proxy-network-topology/application-proxy-three-hops.png":::

## <a name="optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview"></a>Optimera kopplings grupper så att de använder närmaste moln tjänst i Application Proxy (för hands version)

När du registrerar dig för en Azure AD-klient bestäms regionen för din klient organisation av landet/regionen som du anger. När du aktiverar programproxyn väljs **standard** instanserna för moln tjänsten i Azure för din klient organisation i samma region som din Azure AD-klient eller den närmaste regionen.

Om till exempel din Azure AD-klients land eller region är Storbritannien, kommer alla programproxy-kopplingar som **standard** att tilldelas till att använda tjänst instanser i Europeiska Data Center. När dina användare kommer åt publicerade program, går deras trafik genom programproxyns moln tjänst instanser på den här platsen.

Om du har anslutningar installerade i regioner som skiljer sig från din standard region, kan det vara bra att ändra vilken region som anslutnings gruppen är optimerad för för att förbättra prestanda vid åtkomst till dessa program. När en region har angetts för en kopplings grupp ansluter den till Application Proxy Cloud Services i den angivna regionen.

För att optimera trafikflöde och minska svars tiden till en kopplings grupp tilldelar du anslutnings gruppen till den närmaste regionen. Så här tilldelar du en region:

> [!IMPORTANT]
> Anslutningarna måste använda minst version 1.5.1975.0 för att kunna använda den här funktionen.

1. Logga in på [Azure-portalen](https://portal.azure.com/) som programadministratör för den katalog som använder programproxy. Om klientdomänen exempelvis är contoso.com ska administratören vara admin@contoso.com eller något annat administratörsalias på den domänen.
1. Välj ditt användar namn i det övre högra hörnet. Kontrol lera att du är inloggad på en katalog som använder programproxy. Om du behöver ändra kataloger väljer du **Växla katalog** och väljer en katalog som använder Application Proxy.
1. I den vänstra navigerings panelen väljer du **Azure Active Directory**.
1. Under **Hantera** väljer du **programproxy**.
1. Välj **ny anslutnings grupp**, ange ett **namn** för anslutnings gruppen.
1. Gå sedan till **Avancerade inställningar** och välj List rutan under optimera för en bestämd region och välj den region som är närmast kopplingarna.
1. Välj **Skapa**.
    
    :::image type="content" source="./media/application-proxy-network-topology/geo-routing.png" alt-text="Konfigurera en ny anslutnings grupp." lightbox="./media/application-proxy-network-topology/geo-routing.png":::

1. När den nya anslutnings gruppen har skapats kan du välja vilka kopplingar som ska tilldelas den här anslutnings gruppen. 
   - Du kan bara flytta kopplingar till anslutnings gruppen om den finns i en kopplings grupp med hjälp av standard regionen. Den bästa metoden är att alltid börja med dina anslutningar som är placerade i "standard grupp" och sedan flytta dem till rätt anslutnings grupp.
   - Du kan bara ändra region för en kopplings grupp om det **inte finns några** kopplingar tilldelade till den eller de tilldelade apparna.
1. Tilldela sedan kopplings gruppen till dina program. Vid åtkomst till apparna bör trafiken nu gå till program proxyns moln tjänst i den region som anslutnings gruppen är optimerad för.

## <a name="considerations-for-reducing-latency"></a>Att tänka på när du minskar svars tiden

Alla proxyservrar introducerar svars tid i din nätverks anslutning. Oavsett vilken proxy-eller VPN-lösning du väljer som din lösning för fjärråtkomst, innehåller den alltid en uppsättning servrar som gör det möjligt att ansluta till nätverket i företags nätverket.

Organisationer inkluderar vanligt vis Server slut punkter i perimeternätverket. Med Azure AD-programproxy kan trafiken flöda genom proxy-tjänsten i molnet medan anslutningarna finns i företagets nätverk. Inget perimeternätverk krävs.

Nästa avsnitt innehåller ytterligare förslag som hjälper dig att minska svars tiden ytterligare. 

### <a name="connector-placement"></a>Kopplings placering

Programproxyn väljer plats för instanser, baserat på din klient plats. Du kan dock välja var du vill installera anslutningen, vilket ger dig möjlighet att definiera svars tiderna för nätverks trafiken.

När du konfigurerar Application Proxy-tjänsten ställer du följande frågor:

- Var finns appen?
- Var finns de flesta användare som har åtkomst till appen?
- Var finns Application Proxy-instansen?
- Har du redan en särskild nätverks anslutning till Azure-datacenter som har kon figurer ATS, till exempel Azure ExpressRoute eller ett liknande VPN?

Anslutningen måste kommunicera med både Azure och dina program (steg 2 och 3 i trafikflödes diagrammet), så att kopplingens placering påverkar svars tiden för dessa två anslutningar. Tänk på följande när du utvärderar kopplingens placering:

- Om du vill använda Kerberos-begränsad delegering (KCD) för enkel inloggning, behöver anslutningen en rad syn för ett Data Center. Dessutom måste anslutnings servern vara domänansluten.  
- När du är tveksam installerar du anslutningen närmare programmet.

### <a name="general-approach-to-minimize-latency"></a>Allmän metod för att minimera latens

Du kan minimera svars tiden för trafik från slut punkt till slut punkt genom att optimera varje nätverks anslutning. Varje anslutning kan optimeras med:

- Minska avståndet mellan hoppens två ändar.
- Välja rätt nätverk att passera. Till exempel kan det gå snabbare att gå över ett privat nätverk i stället för det offentliga Internet, på grund av dedikerade länkar.

Om du har en särskild VPN-eller ExpressRoute-länk mellan Azure och företagets nätverk kanske du vill använda den.

## <a name="focus-your-optimization-strategy"></a>Fokusera på optimerings strategin

Det finns lite som du kan göra för att kontrol lera anslutningen mellan dina användare och tjänsten Application Proxy. Användare kan komma åt dina appar från ett hem nätverk, ett kafé eller ett annat land/en annan region. I stället kan du optimera anslutningarna från Application Proxy-tjänsten till Application Proxy-kopplingarna till apparna. Överväg att inkludera följande mönster i din miljö.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Mönster 1: sätt kopplingen nära programmet

Placera kopplingen nära mål programmet i kundens nätverk. Den här konfigurationen minimerar steg 3 i krets krets diagrammet eftersom kopplingen och programmet är nära.

Om din anslutning behöver en rad sikt till domänkontrollanten är det här mönstret fördelaktigt. De flesta av våra kunder använder det här mönstret, eftersom det fungerar bra i de flesta fall. Det här mönstret kan också kombineras med Pattern 2 för att optimera trafiken mellan tjänsten och anslutnings tjänsten.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Mönster 2: dra nytta av ExpressRoute med Microsoft-peering

Om du har ExpressRoute som har kon figurer ATS med Microsoft-peering kan du använda en snabbare ExpressRoute-anslutning för trafik mellan programproxy och anslutnings tjänsten. Anslutningen är fortfarande i nätverket, nära appen.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Mönster 3: dra nytta av ExpressRoute med privat peering

Om du har en särskild VPN-eller ExpressRoute som har kon figurer ATS med privat peering mellan Azure och företagets nätverk, har du ett annat alternativ. I den här konfigurationen anses det virtuella nätverket i Azure vanligt vis som ett tillägg till företags nätverket. Så du kan installera anslutningen i Azure-datacentret och fortfarande uppfylla kraven på låg latens för anslutnings-till-app-anslutningen.

Svars tiden komprometteras inte eftersom trafiken flödar över en dedikerad anslutning. Du får också bättre svars tid för service till koppling för programproxy eftersom anslutningen installeras i ett Azure-datacenter nära platsen för din Azure AD-klient.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-expressroute-private.png" alt-text="Diagram som visar Connector som installerats i ett Azure-datacenter" lightbox="./media/application-proxy-network-topology/application-proxy-expressroute-private.png":::

### <a name="other-approaches"></a>Andra metoder

Även om fokus för den här artikeln är kopplings placering kan du också ändra programmets placering för att få bättre svars tid.

Allt fler kan organisationer flytta sina nätverk till värdbaserade miljöer. Detta gör det möjligt för dem att placera sina appar i en värd miljö som också är en del av företagets nätverk och fortfarande finns i domänen. I det här fallet kan mönstren som beskrivs i föregående avsnitt tillämpas på den nya program platsen. Om du överväger det här alternativet, se [Azure AD Domain Services](../../active-directory-domain-services/overview.md).

Tänk också på att organisera dina anslutningar med [anslutnings grupper](application-proxy-connector-groups.md) för att rikta in appar som finns på olika platser och i nätverk.

## <a name="common-use-cases"></a>Vanliga användarsituationer

I det här avsnittet går vi igenom några vanliga scenarier. Anta att Azure AD-klienten (och därför proxy service-slutpunkten) finns i USA (US). De överväganden som beskrivs i dessa användnings fall gäller även för andra regioner runtom i världen.

I dessa scenarier kallar vi varje anslutning till "hopp" och numrera dem för enklare diskussion:

- **Hopp 1**: användare till Application Proxy-tjänsten
- **Hopp 2**: Application Proxy service till Application Proxy Connector
- **Hopp 3**: Application Proxy Connector till mål programmet 

### <a name="use-case-1"></a>Användnings fall 1

**Scenario:** Appen är i en organisations nätverk i USA, med användare i samma region. Det finns inga ExpressRoute eller VPN mellan Azure-datacenter och företags nätverket.

**Rekommendation:** Följ mönstret 1, förklaras i föregående avsnitt. Överväg att använda ExpressRoute om det behövs för bättre svars tid.

Detta är ett enkelt mönster. Du optimerar hopp 3 genom att placera kopplingen nära appen. Detta är också ett naturligt val, eftersom anslutningen vanligt vis installeras med en rad syn för appen och till data centret för att utföra KCD-åtgärder.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern1.png" alt-text="Diagram som visar användare, proxy, anslutning och app är alla i USA." lightbox="./media/application-proxy-network-topology/application-proxy-pattern1.png":::

### <a name="use-case-2"></a>Användnings fall 2

**Scenario:** Appen är i en organisations nätverk i USA, där användare sprids ut globalt. Det finns inga ExpressRoute eller VPN mellan Azure-datacenter och företags nätverket.

**Rekommendation:** Följ mönstret 1, förklaras i föregående avsnitt.

Ett vanligt mönster är att optimera hopp 3, där du placerar kopplingen nära appen. Hopp 3 är vanligt vis kostsamt, om det är i samma region. Hopp 1 kan dock vara dyrare beroende på var användaren är, eftersom användare i världen måste ha åtkomst till Application Proxy-instansen i USA. Det är värt att notera att alla proxyservrar har liknande egenskaper för användare som sprids ut globalt.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern2.png" alt-text="Användare sprids globalt, men allt annat är i USA" lightbox="./media/application-proxy-network-topology/application-proxy-pattern2.png":::

### <a name="use-case-3"></a>Användnings fall 3

**Scenario:** Appen finns i en organisations nätverk i USA. ExpressRoute med Microsoft-peering finns mellan Azure och företags nätverket.

**Rekommendation:** Följ mönster 1 och 2, förklaras i föregående avsnitt.

Börja med att placera anslutningen så nära appen som möjligt. Sedan använder systemet automatiskt ExpressRoute för hopp 2.

Om ExpressRoute-länken använder Microsoft-peering flödar trafiken mellan proxyn och anslutnings tjänsten över länken. Hopp 2 har optimerad svars tid.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern3.png" alt-text="Diagram över ExpressRoute mellan proxy och koppling" lightbox="./media/application-proxy-network-topology/application-proxy-pattern3.png":::

### <a name="use-case-4"></a>Användnings fall 4

**Scenario:** Appen finns i en organisations nätverk i USA. ExpressRoute med privat peering finns mellan Azure och företags nätverket.

**Rekommendation:** Följ mönstret 3, förklaras i föregående avsnitt.

Placera anslutningen i det Azure-datacenter som är anslutet till företags nätverket via ExpressRoute privata peering.

Anslutningen kan placeras i Azure-datacentret. Eftersom anslutnings tjänsten fortfarande har en detaljerad serie för programmet och data centret via det privata nätverket är hopp 3 optimerat. Dessutom optimeras hopp 2 ytterligare.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern4.png" alt-text="Koppling i Azure Data Center, ExpressRoute mellan koppling och app" lightbox="./media/application-proxy-network-topology/application-proxy-pattern4.png":::

### <a name="use-case-5"></a>Användnings fall 5

**Scenario:** Appen är i en organisations nätverk i Europa, som standard är vi USA, med de flesta användare i Europa.

**Rekommendation:** Placera kopplingen nära appen. Uppdatera kopplings gruppen så att den optimeras för användning av Europa Application Proxy Service-instanser. Anvisningar finns i [optimera kopplings grupper för att använda närmaste moln tjänst för tillämpningsproxy](application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview).

Eftersom Europa användare har åtkomst till en Application Proxy-instans som sker i samma region, är hopp 1 inte dyrt. Hopp 3 är optimerat. Överväg att använda ExpressRoute för att optimera hopp 2.

### <a name="use-case-6"></a>Användnings fall 6

**Scenario:** Appen är i en organisations nätverk i Europa, som standard är vi USA, med de flesta användare i USA.

**Rekommendation:** Placera kopplingen nära appen. Uppdatera kopplings gruppen så att den optimeras för användning av Europa Application Proxy Service-instanser. Anvisningar finns i [optimera kopplings grupper för att använda närmaste moln tjänst för tillämpningsproxy](application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview). Hopp 1 kan vara dyrare eftersom alla amerikanska användare måste ha åtkomst till Application Proxy-instansen i Europa.

Du kan också överväga att använda en annan variant i den här situationen. Om de flesta användare i organisationen är i USA är det mycket troligt att nätverket även utsätts för oss. Placera anslutaren i USA, Fortsätt att använda standard regionen USA för dina anslutnings grupper och Använd den dedikerade interna företags nätverks linjen till programmet i Europa. På så sätt optimeras hopp 2 och 3.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern5c.png" alt-text="Diagrammet visar användare, proxy och anslutning i USA, appen i Europa." lightbox="./media/application-proxy-network-topology/application-proxy-pattern5c.png":::

## <a name="next-steps"></a>Nästa steg

- [Aktivera programproxy](application-proxy-add-on-premises-application.md)
- [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md)
- [Aktivera villkorlig åtkomst](application-proxy-integrate-with-sharepoint-server.md)
- [Felsöka problem med Application Proxy](application-proxy-troubleshoot.md)
