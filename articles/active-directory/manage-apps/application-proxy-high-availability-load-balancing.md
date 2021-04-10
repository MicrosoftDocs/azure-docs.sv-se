---
title: Hög tillgänglighet och belastnings utjämning – Azure AD-programproxy
description: Hur trafik distribution fungerar med din Application Proxy-distribution. Innehåller tips för hur du optimerar anslutnings prestanda och använder belastnings utjämning för backend-servrar.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fda858b0811eb6308b8e5588eaeae9bff5a1730
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259397"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Hög tillgänglighet och belastnings utjämning för dina Application Proxy-kopplingar och-program

Den här artikeln förklarar hur trafik distribution fungerar med din programproxy-distribution. Vi diskuterar:

- Hur trafiken fördelas mellan användare och anslutningar, tillsammans med tips för att optimera anslutnings prestanda

- Hur trafiken flödar mellan anslutningar och backend-AppData, med rekommendationer för belastnings utjämning mellan flera backend-servrar

## <a name="traffic-distribution-across-connectors"></a>Trafik distribution över anslutningar

Anslutningar upprättar sina anslutningar baserat på principer för hög tillgänglighet. Det finns ingen garanti för att trafiken alltid fördelas jämnt mellan kopplingarna och att det inte finns någon sessions tillhörighet. Användningen varierar dock och begär Anden skickas slumpmässigt till Application Proxy Service-instanser. Det innebär att trafiken vanligt vis fördelas nästan jämnt över anslutningarna. Diagrammet och stegen nedan visar hur anslutningar upprättas mellan användare och anslutningar.

![Diagram över anslutningar mellan användare och kopplingar](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. En användare på en klient enhet försöker komma åt ett lokalt program som publicerats via programproxy.
2. Begäran går via en Azure Load Balancer för att avgöra vilken Application Proxy-tjänstinstans som ska ta begäran. Per region finns det flera instanser av tillgängliga instanser att acceptera begäran. Den här metoden hjälper till att jämnt distribuera trafiken över tjänst instanserna.
3. Begäran skickas till [Service Bus](../../service-bus-messaging/index.yml).
4. Service Bus signaler till en tillgänglig anslutning. Anslutningen hämtar sedan begäran från Service Bus.
   - I steg 2 går förfrågningar till olika Application Proxy Service-instanser, så det är mer sannolikt att anslutningar görs med olika anslutningar. Därför är kopplingarna nästan jämnt använda i gruppen.
5. Anslutningen skickar begäran till programmets backend-server. Sedan skickar programmet tillbaka svaret till anslutnings tjänsten.
6. Anslutningen Slutför svaret genom att öppna en utgående anslutning till tjänst instansen varifrån begäran kom. Sedan stängs anslutningen omedelbart. Som standard är varje koppling begränsad till 200 samtidiga utgående anslutningar.
7. Svaret skickas sedan tillbaka till klienten från tjänst instansen.
8. Efterföljande förfrågningar från samma anslutning Upprepa stegen ovan.

Ett program har ofta många resurser och öppnar flera anslutningar när de läses in. Varje anslutning går igenom stegen ovan för att bli allokerad till en tjänst instans, Välj en ny tillgänglig anslutning om anslutningen ännu inte har kopplats till en anslutning.


## <a name="best-practices-for-high-availability-of-connectors"></a>Metod tips för hög tillgänglighet för anslutningar

- På grund av hur trafiken fördelas mellan anslutningar för hög tillgänglighet är det viktigt att alltid ha minst två kopplingar i en kopplings grupp. Tre kopplingar föredras för att ge ytterligare buffert mellan kopplingar. Om du vill fastställa rätt antal anslutningar som du behöver följer du dokumentation om kapacitets planering.

- Placera anslutningar på olika utgående anslutningar för att undvika en enskild felpunkt. Om anslutningar använder samma utgående anslutning kan ett nätverks problem med anslutningen påverka alla anslutningar som använder den.

- Undvik att tvinga anslutningar att starta om när du är ansluten till produktions program. Om du gör det kan det påverka spridningen av trafik mellan kopplingarna. Att starta om anslutningar gör att fler anslutningar blir otillgängliga och tvingar fram anslutningar till den återstående tillgängliga anslutningen. Resultatet är en ojämn användning av anslutningarna från början.

- Undvik alla former av infogad kontroll på utgående TLS-kommunikation mellan anslutningar och Azure. Den här typen av inskriven granskning orsakar försämring av kommunikations flödet.

- Se till att behålla automatiska uppdateringar som körs för dina anslutningar. Om programproxyn Connector Updater tjänsten körs uppdateras dina anslutningar automatiskt och tar emot den senaste uppgraderingen. Om du inte ser Connector Updater-tjänsten på servern måste du installera om anslutningen för att få några uppdateringar.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Trafikflöde mellan anslutningar och Server dels program servrar

En annan viktig yta där hög tillgänglighet är en faktor är anslutningen mellan anslutningar och backend-servrar. När ett program publiceras via Azure AD-programproxy flödar trafiken från användarna till programmen genom tre hopp:

1. Användaren ansluter till den offentliga Azure AD-programproxy-tjänstens offentliga slut punkt på Azure. Anslutningen upprättas mellan klientens ursprungliga IP-adress (offentlig) och IP-adressen för programproxyns slut punkt.
2. Application Proxy Connector hämtar HTTP-begäran för klienten från Application Proxy-tjänsten.
3. Application Proxy Connector ansluter till mål programmet. Anslutnings tjänsten använder sin egen IP-adress för att upprätta anslutningen.

![Diagram över användare som ansluter till ett program via programproxy](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-vidarebefordrad – för rubrik fälts överväganden
I vissa situationer (t. ex. granskning, belastnings utjämning osv.) är det ett krav att dela den externa klientens ursprungliga IP-adress med den lokala miljön. För att lösa kravet lägger Azure AD-programproxy Connector till det X-vidarebefordrade – för huvud fältet med den ursprungliga klientens IP-adress (offentlig) till HTTP-begäran. Lämplig nätverks enhet (belastningsutjämnare, brand vägg) eller webb server eller Server dels program kan sedan läsa och använda informationen.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Metod tips för belastnings utjämning mellan flera App-servrar
När kopplings gruppen som är tilldelad till Application Proxy-programmet har två eller fler anslutningar och du kör backend-webbprogrammet på flera servrar (Server grupp) krävs en bra belastnings Utjämnings strategi. En lämplig strategi garanterar att servrar hämtar klient begär Anden jämnt och förhindrar över-eller under användningen av servrar i Server gruppen.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Scenario 1: backend-appen kräver inte sessions-persistence
Det enklaste scenariot är att Server delens webb program inte kräver session varaktighet (persistence). Alla begär Anden från användaren kan hanteras av alla program instanser i Server gruppen. Du kan använda en Layer 4-belastningsutjämnare och konfigurera den utan tillhörighet. Vissa alternativ är Microsoft NLB (utjämning av nätverks belastning) och Azure Load Balancer eller en belastningsutjämnare från en annan leverantör. Alternativt kan du konfigurera resursallokering med DNS.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Scenario 2: backend-program kräver överpersists av sessionen
I det här scenariot kräver backend-webbprogrammet session varaktighet (persistence) under den autentiserade sessionen. Alla begär Anden från användaren måste hanteras av Server dels program instansen som körs på samma server i Server gruppen.
Det här scenariot kan vara mer komplicerat eftersom klienten vanligt vis upprättar flera anslutningar till Application Proxy-tjänsten. Förfrågningar över olika anslutningar kan komma från olika anslutningar och servrar i Server gruppen. Eftersom varje anslutning använder sin egen IP-adress för den här kommunikationen kan belastningsutjämnaren inte garantera varaktighet baserat på anslutningarnas IP-adress. Källans IP-tillhörighet kan inte användas.
Här följer några alternativ för scenario 2:

- Alternativ 1: basera sessionen på en sessions-cookie som angetts av belastningsutjämnaren. Det här alternativet rekommenderas eftersom belastningen kan spridas jämnt mellan backend-servrarna. Den kräver en Layer 7-belastningsutjämnare med den här funktionen och kan hantera HTTP-trafiken och avsluta TLS-anslutningen. Du kan använda Azure Application Gateway (tillhörighet mellan sessioner) eller en belastningsutjämnare från en annan leverantör.

- Alternativ 2: basera sessionen på det X-vidarebefordrade – för huvud fältet. Det här alternativet kräver en Layer 7-belastningsutjämnare med den här funktionen och som kan hantera HTTP-trafiken och avsluta TLS-anslutningen.  

- Alternativ 3: Konfigurera backend-programmet så att det inte kräver sessionens persistence.

Läs program varu leverantörens dokumentation för att förstå belastnings Utjämnings kraven för backend-programmet.

## <a name="next-steps"></a>Nästa steg

- [Aktivera programproxy](application-proxy-add-on-premises-application.md)
- [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md)
- [Aktivera villkorlig åtkomst](application-proxy-integrate-with-sharepoint-server.md)
- [Felsöka problem med Application Proxy](application-proxy-troubleshoot.md)
- [Lär dig hur Azure AD-arkitekturen stöder hög tillgänglighet](../fundamentals/active-directory-architecture.md)