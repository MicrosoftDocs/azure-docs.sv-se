---
title: Säkerhets aspekter för Azure AD-programproxy | Microsoft Docs
description: Omfattar säkerhets överväganden för att använda Azure AD-programproxy
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
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c98bce0be2b456220815a359aae1ee697f3ca2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254973"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Säkerhets överväganden för att få åtkomst till appar via fjärr anslutning med Azure AD-programproxy

I den här artikeln beskrivs de komponenter som fungerar för att skydda dina användare och program när du använder Azure Active Directory-programproxy.

Följande diagram visar hur Azure AD ger säker fjärråtkomst till dina lokala program.

 ![Diagram över säker fjärråtkomst via Azure AD-programproxy](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Säkerhets förmåner

Azure AD-programproxy erbjuder följande säkerhets fördelar:

### <a name="authenticated-access"></a>Autentiserad åtkomst 

Om du väljer att använda Azure Active Directory förautentisering kan endast autentiserade anslutningar komma åt ditt nätverk.

Azure AD-programproxy förlitar sig på Azure AD Security Token Service (STS) för all autentisering.  Förautentisering, med sin natur, blockerar ett stort antal anonyma attacker, eftersom endast autentiserade identiteter kan komma åt backend-programmet.

Om du väljer genom strömning som metod för förautentisering får du inte den här förmånen. 

### <a name="conditional-access"></a>Villkorlig åtkomst

Använd bättre princip kontroller innan anslutningar till nätverket upprättas.

Med [villkorlig åtkomst](../conditional-access/concept-conditional-access-cloud-apps.md)kan du definiera begränsningar för hur användare får åtkomst till dina program. Du kan skapa principer som begränsar inloggningar baserat på plats, styrkan för autentisering och användar risk profil.

Du kan också använda villkorlig åtkomst för att konfigurera Multi-Factor Authentication-principer och lägga till ytterligare ett säkerhets lager till dina användarautentisering. Dessutom kan dina program dirigeras till Microsoft Cloud App Security via villkorlig åtkomst i Azure AD för att tillhandahålla övervakning och kontroller i real tid via [åtkomst](/cloud-app-security/access-policy-aad) [och arbetsköprinciper](/cloud-app-security/session-policy-aad)

### <a name="traffic-termination"></a>Trafik terminering

All trafik avbryts i molnet.

Eftersom Azure AD-programproxy är en omvänd proxy avslutas all trafik till backend-program på tjänsten. Sessionen kan bara återupprättas med backend-servern, vilket innebär att backend-servrarna inte exponeras för direkt HTTP-trafik. Den här konfigurationen innebär att du är bättre skyddad mot riktade attacker.

### <a name="all-access-is-outbound"></a>All åtkomst är utgående 

Du behöver inte öppna inkommande anslutningar till företags nätverket.

Application Proxy-kopplingar använder bara utgående anslutningar till Azure AD-programproxy-tjänsten, vilket innebär att du inte behöver öppna brand Väggs portar för inkommande anslutningar. Traditionella proxyservrar krävde ett perimeternätverk (även kallat *DMZ*, *demilitariserad Zone* eller *skärmat undernät*) och tillåten åtkomst till oautentiserade anslutningar vid nätverks kanten. Det här scenariot kräver investeringar i brand Väggs produkter för webb program för att analysera trafik och skydda miljön. Med Application Proxy behöver du inte ett perimeternätverk eftersom alla anslutningar är utgående och sker över en säker kanal.

Mer information om anslutningar finns i [förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Analys och maskin inlärning i moln skala 

Få säkerhets skydd i den senaste säkerheten.

Eftersom den är en del av Azure Active Directory kan programproxyn utnyttja [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md), med data från Microsoft Security Response Center och digital brottslighet Unit. Tillsammans identifierar vi på ett proaktivt sätt komprometterade konton och ger skydd mot högrisk inloggningar. Vi tar hänsyn till flera faktorer för att avgöra vilka inloggnings försök som är hög risk. De här faktorerna omfattar flagga infekterade enheter, maskera-nätverk och ovanlig eller platser som inte är osannolika.

Många av dessa rapporter och händelser är redan tillgängliga via ett API för integrering med dina SIEM-system (Security information and Event Management).

### <a name="remote-access-as-a-service"></a>Fjärråtkomst som en tjänst

Du behöver inte bekymra dig om att underhålla och korrigera lokala servrar.

Program vara som inte har uppdaterats har fortfarande konton för ett stort antal attacker. Azure AD-programproxy är en Internet-Scale-tjänst som Microsoft äger, så att du alltid får de senaste säkerhets korrigeringarna och-uppgraderingar.

För att förbättra säkerheten för program som publiceras av Azure AD-programproxy blockerar vi webb robot robotarna från att indexera och arkivera dina program. Varje gången en webb robot försöker hämta robotens inställningar för en publicerad app, svarar programproxyn med en robots.txt-fil som innehåller `User-agent: * Disallow: /` .

#### <a name="azure-ddos-protection-service"></a>Azure DDoS Protection-tjänst

Program som publiceras via programproxyn skyddas mot DDoS-attacker (distributed denial of Service). Det här skyddet hanteras av Microsoft och aktive ras automatiskt i alla våra data Center. Azure DDoS Protection-tjänsten tillhandahåller alltid trafik övervakning och i real tid av vanliga attacker på nätverks nivå. 

## <a name="under-the-hood"></a>Under huven

Azure AD-programproxy består av två delar:

* Den molnbaserade tjänsten: den här tjänsten körs i Azure och är den plats där den externa klienten/användar anslutningarna görs.
* [Den lokala anslutningen](application-proxy-connectors.md): en lokal komponent lyssnar Connector efter begär Anden från Azure AD-programproxy-tjänsten och hanterar anslutningar till de interna programmen. 

Ett flöde mellan anslutningen och Application Proxy-tjänsten upprättas när:

* Anslutningen konfigureras först.
* Anslutningen hämtar konfigurations information från Application Proxy-tjänsten.
* En användare får åtkomst till ett publicerat program.

>[!NOTE]
>All kommunikation sker över TLS, och de har alltid sitt ursprung vid anslutningen till Application Proxy-tjänsten. Tjänsten är endast utgående.

Anslutningen använder ett klient certifikat för att autentisera till Application Proxy-tjänsten för nästan alla anrop. Det enda undantaget till den här processen är det inledande installations steget, där klient certifikatet upprättas.

### <a name="installing-the-connector"></a>Installerar anslutningen

När anslutningen först konfigureras sker följande flödes händelser:

1. Anslutnings registreringen till tjänsten görs som en del av installationen av anslutnings programmet. Användarna uppmanas att ange sina autentiseringsuppgifter för Azure AD-administratören. Den token som har hämtats från den här autentiseringen visas sedan för Azure AD-programproxy-tjänsten.
2. Application Proxy-tjänsten utvärderar token. Den kontrollerar om användaren är global administratör i klienten. Om användaren inte är administratör avbryts processen.
3. Anslutningen genererar en begäran om klient certifikat och skickar den, tillsammans med token, till Application Proxy-tjänsten. Tjänsten i sin tur verifierar token och signerar begäran om klient certifikat.
4. Anslutnings programmet använder klient certifikatet för framtida kommunikation med Application Proxy-tjänsten.
5. Anslutningen utför en första hämtning av system konfigurations data från tjänsten med hjälp av dess klient certifikat och är nu redo att vidta begär Anden.

### <a name="updating-the-configuration-settings"></a>Uppdaterar konfigurations inställningarna

När tjänsten Application Proxy uppdaterar konfigurations inställningarna sker följande flödes händelser:

1. Anslutningen ansluter till konfigurations slut punkten i Application Proxy-tjänsten med hjälp av dess klient certifikat.
2. När klient certifikatet har verifierats returnerar Application Proxy-tjänsten konfigurations data till anslutnings tjänsten (till exempel anslutnings gruppen som anslutningen ska vara en del av).
3. Om det aktuella certifikatet är mer än 180 dagar gammalt, genererar anslutningen en ny certifikatbegäran som effektivt uppdaterar klient certifikatet var 180 dag.

### <a name="accessing-published-applications"></a>Åtkomst till publicerade program

När användare ansluter till ett publicerat program, sker följande händelser mellan Application Proxy-tjänsten och Application Proxy-anslutningen:

1. Tjänsten autentiserar användaren för appen
2. Tjänsten placerar en begäran i anslutnings kön
3. En anslutning bearbetar begäran från kön
4. Anslutningen väntar på ett svar
5. Tjänsten strömmar data till användaren

Om du vill veta mer om vad som händer i vart och ett av dessa steg fortsätter du med att läsa.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. tjänsten autentiserar användaren för appen

Om du konfigurerade appen så att den använder passthrough som sin Förautentiserings metod hoppas stegen i det här avsnittet över.

Om du konfigurerade appen för förautentisering med Azure AD omdirigeras användarna till Azure AD STS för att autentisera och följande steg utförs:

1. Programproxyn söker efter eventuella krav för villkorlig åtkomst princip för det specifika programmet. Det här steget ser till att användaren har tilldelats till programmet. Om tvåstegsverifiering krävs uppmanas användaren att ange en andra autentiseringsmetod.

2. När alla kontroller har slutförts utfärdar Azure AD STS en signerad token för programmet och omdirigerar användaren tillbaka till Application Proxy-tjänsten.

3. Application Proxy kontrollerar att token har utfärdats till rätt program. Den utför andra kontroller också, till exempel att se till att token har signerats av Azure AD och att den fortfarande finns i det giltiga fönstret.

4. Application Proxy anger en krypterad autentiserings-cookie som indikerar att autentiseringen till programmet har inträffat. Cookien innehåller en förfallo tids stämpling som baseras på token från Azure AD och andra data, t. ex. användar namnet som autentiseringen baseras på. Cookien krypteras med en privat nyckel som endast är känd för Application Proxy-tjänsten.

5. Application Proxy omdirigerar användaren tillbaka till den ursprungligen begärda URL: en.

Om någon del av förautentiseringen Miss lyckas nekas användarens begäran och användaren visas ett meddelande som anger orsaken till problemet.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. tjänsten placerar en förfrågan i anslutnings kön

Anslutningar håller en utgående anslutning öppen till Application Proxy-tjänsten. När en begäran kommer i kö, ställer tjänsten upp begäran på en av de öppna anslutningarna för att ansluta.

Begäran innehåller objekt från programmet, till exempel begärandehuvuden, data från den krypterade cookien, användaren som gör begäran och ID för begäran. Även om data från den krypterade cookien skickas med begäran, är själva autentiserings-cookien inte.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. anslutningen bearbetar begäran från kön. 

Baserat på begäran utför programproxy en av följande åtgärder:

* Om begäran är en enkel åtgärd (t. ex. om det inte finns några data i bröd texten som är med en RESTful *Get* -begäran), gör anslutningen en anslutning till den interna interna resursen och väntar sedan på ett svar.

* Om begäran har data som är kopplade till den i bröd texten (till exempel en RESTful *post* -åtgärd) gör anslutningen en utgående anslutning genom att använda klient certifikatet till Application Proxy-instansen. Den gör anslutningen till att begära data och öppna en anslutning till den interna resursen. När den tar emot begäran från anslutningen börjar Application Proxy-tjänsten att acceptera innehåll från användaren och vidarebefordra data till anslutnings tjänsten. Anslutningen vidarebefordrar i sin tur data till den interna resursen.

#### <a name="4-the-connector-waits-for-a-response"></a>4. anslutningen väntar på ett svar.

När begäran och överföring av allt innehåll till Server delen har slutförts, väntar anslutningen på ett svar.

När den får ett svar gör anslutningen en utgående anslutning till Application Proxy-tjänsten för att returnera rubrik informationen och börja strömma retur data.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. tjänsten strömmar data till användaren. 

En del bearbetning av programmet kan inträffa här. Om du konfigurerade programproxyn för att översätta sidhuvuden eller URL: er i ditt program sker bearbetningen vid behov under det här steget.


## <a name="next-steps"></a>Nästa steg

[Nätverks sto pol faktorer när du använder Azure AD-programproxy](application-proxy-network-topology.md)

[Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)