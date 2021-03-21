---
title: Aktiverar end to end-TLS på Azure Application Gateway
description: Den här artikeln är en översikt över Application Gateway slut punkt till slut punkt för TLS-stöd.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: victorh
ms.openlocfilehash: c39401289ffc6f27c292168adaa15c5163a3967b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001294"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Översikt över TLS-terminering och slut punkt till slut punkt för TLS med Application Gateway

Transport Layer Security (TLS), som tidigare kallades Secure Sockets Layer (SSL), är standard säkerhets teknik för att upprätta en krypterad länk mellan en webb server och en webbläsare. Den här länken ser till att alla data som skickas mellan webb servern och webbläsare förblir privata och krypterade. Application Gateway stöder både TLS-avslutning på gatewayen samt end to end TLS Encryption.

## <a name="tls-termination"></a>TLS-terminering

Application Gateway stöder TLS-avslutning på gatewayen, efter vilken trafiken vanligt vis flödar okrypterad till backend-servrarna. Det finns ett antal fördelar med att utföra TLS-avslutning på Application Gateway:

- **Bättre prestanda** – den största prestanda träffen när TLS-dekryptering är den första hand skakningen. För att förbättra prestandan gör servern kryptering av cacheminnet TLS-sessions-ID: n och hanterar TLS-sessionsbiljetter. Om detta görs på Application Gateway kan alla begär Anden från samma klient använda de cachelagrade värdena. Om den är utförd på backend-servrarna måste klienten autentiseras på nytt varje gång som klientens begär Anden går till en annan server. Användningen av TLS-biljetter kan hjälpa dig att undvika det här problemet, men det stöds inte av alla klienter och kan vara svårt att konfigurera och hantera.
- **Bättre användning av backend-servrar** – SSL/TLS-bearbetning är mycket processor intensiv och blir mer intensiv eftersom nyckel storlekar ökar. Genom att ta bort det här arbetet från backend-servrarna kan de fokusera på vad de är mest effektiva vid och leverera innehåll.
- **Intelligent routning** – genom att dekryptera trafiken har programgatewayen åtkomst till innehållet i begäran, till exempel SIDHUVUD, URI och så vidare, och kan använda dessa data för att dirigera begär Anden.
- **Certifikat hantering** – certifikat behöver bara köpas och installeras på programgatewayen och inte på alla backend-servrar. Detta sparar både tid och pengar.

Om du vill konfigurera TLS-terminering måste ett TLS/SSL-certifikat läggas till i lyssnaren för att Application Gateway ska kunna härleda en symmetrisk nyckel enligt specifikationen TLS/SSL-protokoll. Den symmetriska nyckeln används sedan för att kryptera och dekryptera trafiken som skickas till gatewayen. TLS/SSL-certifikatet måste vara i PFX-format (personal information Exchange). Med det här fil formatet kan du exportera den privata nyckeln som krävs av programgatewayen för att utföra kryptering och dekryptering av trafik.

> [!IMPORTANT] 
> Certifikatet på lyssnaren kräver att hela certifikat kedjan laddas upp (rot certifikatet från certifikat utfärdaren, mellanliggande och löv certifikatet) för att upprätta en förtroende kedja. 


> [!NOTE] 
>
> Application Gateway tillhandahåller inte någon funktion för att skapa ett nytt certifikat eller skicka en certifikatbegäran till en certifikat utfärdare.

För att TLS-anslutningen ska fungera måste du se till att TLS/SSL-certifikatet uppfyller följande villkor:

- Att aktuellt datum och tid är inom datum intervallet "giltigt från" och "giltigt till" i certifikatet.
- Att certifikatets ”vanliga namn” (CN) matchar värdhuvudet i begäran. Exempelvis om klienten gör en begäran till `https://www.contoso.com/`, måste CN vara `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certifikat som stöds för TLS-avslutning

Application Gateway stöder följande typer av certifikat:

- Certifikat UTFÄRDAre (certifikat utfärdare): ett CA-certifikat är ett digitalt certifikat som utfärdats av en certifikat utfärdare (CA)
- EV-certifikat (utökad verifiering): ett EV-certifikat är ett certifikat som följer rikt linjerna för bransch standard certifikat. Detta kommer att aktivera webbläsarens lokaliserare grönt och även publicera företags namnet.
- Jokertecken: det här certifikatet har stöd för valfritt antal under domäner baserat på *. site.com, där din under domän skulle ersätta *. Den har dock inte stöd för site.com, så om användarna kommer åt din webbplats utan att skriva den inledande "www", kommer jokertecknet för jokertecken inte att gälla.
- Self-Signed certifikat: klient webbläsare kan inte lita på dessa certifikat och varnar användaren om att den virtuella tjänstens certifikat inte ingår i en förtroende kedja. Självsignerade certifikat är bra för testning eller miljöer där administratörer styr klienterna och kan kringgå webbläsarens säkerhets aviseringar på ett säkert sätt. Produktions arbets belastningar bör aldrig använda självsignerade certifikat.

Mer information finns i [Konfigurera TLS-avslutning med Application Gateway](./create-ssl-portal.md).

### <a name="size-of-the-certificate"></a>Certifikatets storlek
I avsnittet [Application Gateway gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits) kan du se hur den maximala storleken på TLS/SSL-certifikat stöds.

## <a name="end-to-end-tls-encryption"></a>TLS-kryptering från slut punkt till slut punkt

Du kanske inte vill ha okrypterad kommunikation till backend-servrarna. Du kan ha säkerhets krav, krav på efterlevnad eller programmet kan bara godkänna en säker anslutning. Azure Application Gateway har slut punkt till slut punkt för att stödja dessa krav.

Med end-to-end-TLS kan du kryptera och skicka känsliga data på ett säkert sätt till Server delen samtidigt som du använder Application Gateway lager-7 belastnings Utjämnings funktioner. Dessa funktioner omfattar cookie-baserad sessionsgräns, URL-baserad routning, stöd för routning baserat på platser, möjligheten att skriva om eller mata in X-forwarded-*-rubriker och så vidare.

När den är konfigurerad med kommunikation från slut punkt till slut punkt, avslutar Application Gateway TLS-sessionerna på gatewayen och dekrypterar användar trafiken. Därefter appliceras de konfigurerade reglerna för att välja en lämplig serverdels-serverpoolinstans att dirigera trafiken till. Application Gateway initierar sedan en ny TLS-anslutning till backend-servern och återkrypterar data med Server dels serverns offentliga nyckel certifikat innan begäran skickas till Server delen. Eventuella svar från webbservern genomgår samma process på väg tillbaka till användaren. TLS från slut punkt till slut punkt aktive ras genom att ställa in protokoll inställningen i [HTTP-inställningen för http](./configuration-overview.md#http-settings) till https, som sedan tillämpas på en backend-pool.

För Application Gateway-och WAF v1 SKU: n gäller TLS-principen för både frontend-och backend-trafik. På klient sidan fungerar Application Gateway som-servern och tillämpar principen. På Server delen fungerar Application Gateway som klienten och skickar protokoll/chiffrering-information som inställningen under TLS-handskakningen.

För Application Gateway-och WAF v2-SKU: n gäller TLS-principen endast för klient dels trafiken och alla chiffer erbjuds till backend-servern, som har kontroll över att välja vissa chiffer och TLS-version under hand skakningen.

Application Gateway kommunicerar endast med de Server dels servrar som antingen tillåter att de har listat sitt certifikat med den Application Gateway eller vars certifikat är signerade av välkända CA-myndigheter och certifikatets CN matchar värd namnet i inställningarna för HTTP-backend. Dessa inkluderar betrodda Azure-tjänster som Azure App Service/Web Apps och Azure API Management.

Om certifikaten för medlemmarna i backend-poolen inte har signerats av välkända CA-utfärdare, måste varje instans i backend-poolen med end to end TLS aktiverat konfigureras med ett certifikat för att tillåta säker kommunikation. Genom att lägga till certifikatet ser du till att programgatewayen bara kommunicerar med kända backend-instanser. Detta skyddar all kommunikation från slut punkt till slut punkt.

> [!NOTE] 
>
> Certifikatet som har lagts till i **Server delens HTTP-inställning** för att autentisera backend-servrarna kan vara samma som det certifikat som lagts till i **LYSSNAren** för TLS-avslutning vid Application Gateway eller annat för förbättrad säkerhet.

![TLS-scenario för slut punkt till slut punkt][1]

I det här exemplet dirigeras begär Anden som använder TLS 1.2 till backend-servrar i Pool1 med end to end-TLS.

## <a name="end-to-end-tls-and-allow-listing-of-certificates"></a>Slut punkt till slut punkt TLS och Tillåt registrering av certifikat

Application Gateway kommunicerar bara med kända Server dels instanser som har Tillåt att deras certifikat visas med programgatewayen. Det finns vissa skillnader i konfigurations processen från slut punkt till slut punkt som gäller den version av Application Gateway som används. I följande avsnitt förklaras de var för sig.

## <a name="end-to-end-tls-with-the-v1-sku"></a>End-to-end-TLS med v1 SKU

För att aktivera TLS från slut punkt till slut punkt med backend-servrar och för att Application Gateway dirigera begär anden till dem, måste hälso avsökningarna lyckas och returnera felfria svar.

För HTTPS-hälsosökningar använder Application Gateway v1 SKU en exakt matchning av autentiseringscertifikatet (offentlig nyckel för backend-serverns certifikat och inte rot certifikatet) som ska överföras till HTTP-inställningarna.

Endast anslutningar till kända och tillåtna Server delar tillåts sedan. Återstående Server delar betraktas som ohälsosama av hälso avsökningarna. Självsignerade certifikat är enbart för testningsändamål och rekommenderas inte för produktions-arbetsbelastningar. Sådana certifikat måste tillåtas i listan med programgatewayen enligt beskrivningen i föregående steg innan de kan användas.

> [!NOTE]
> Autentisering och konfiguration av betrodda rot certifikat krävs inte för betrodda Azure-tjänster som Azure App Service. De anses vara betrodda som standard.

## <a name="end-to-end-tls-with-the-v2-sku"></a>End-to-end-TLS med v2-SKU: n

Certifikat för autentisering har ersatts och ersatts av betrodda rot certifikat i Application Gateway v2-SKU: n. De fungerar på samma sätt för att autentisera certifikat med några viktiga skillnader:

- Certifikat signerade av välkända CA-myndigheter vars CN matchar värd namnet i inställningarna för HTTP-Dataservern kräver inte ytterligare steg för att avsluta TLS till att fungera. 

   Om till exempel Server dels certifikaten utfärdas av en välkänd certifikat utfärdare och har ett CN-contoso.com, och värd fältet för Server delens http-inställning också är inställt på contoso.com, krävs inga ytterligare steg. Du kan ställa in protokollet för http-filservern på HTTPS och både hälso avsökningen och data Sök vägen är TLS-aktiverade. Om du använder Azure App Service eller andra Azure-webbtjänster som Server del är dessa även implicit betrodda, och inga ytterligare åtgärder krävs för end to end-TLS.
   
> [!NOTE] 
>
> För att ett TLS/SSL-certifikat ska vara betrott måste certifikatet på backend-servern ha utfärdats av en certifikat utfärdare som är välkänd. Om certifikatet inte har utfärdats av en betrodd certifikat utfärdare, programgatewayen kommer sedan att kontrol lera om certifikatet för den utfärdande certifikat utfärdaren utfärdades av en betrodd certifikat utfärdare, och så vidare tills antingen en betrodd certifikat utfärdare hittas (där en betrodd, säker anslutning upprättas) eller så kan ingen betrodd certifikat utfärdare hittas (då kommer programgatewayen att markera Server delen som skadad). Det rekommenderas därför att Server delens Server certifikat innehåller både rot-och mellanliggande certifikat utfärdare.

- Om certifikatet är självsignerat, eller signerat av okända mellanhänder, så måste du aktivera end-to-end-TLS i v2-SKU: n. ett betrott rot certifikat måste definieras. Application Gateway kommunicerar endast med Server certifikat vars rot certifikat matchar en av listan över betrodda rot certifikat i Server delens http-inställning som är associerad med poolen.

- Förutom rot certifikat matchningen, validerar Application Gateway v2 även om värd inställningen som anges i Server delens http-inställning matchar det egna namnet (CN) som presenteras av backend-serverns TLS/SSL-certifikat. När du försöker upprätta en TLS-anslutning till Server delen anger Application Gateway v2 Servernamnindikator (SNI)-tillägget till värden som anges i Server delens http-inställning.

- Om **Välj värd namn från Server dels mål** väljs i stället för värd fältet i http-inställningen för Server delen, anges SNI-huvudet alltid till FQDN för backend-poolen och CN på backend-serverns TLS/SSL-certifikat måste matcha sitt fullständiga domän namn. Medlemmar i backend-pooler med IP-adresser stöds inte i det här scenariot.

- Rot certifikatet är ett base64-kodat rot certifikat från backend-serverns certifikat.

## <a name="sni-differences-in-the-v1-and-v2-sku"></a>SNI skillnader i v1-och v2-SKU: n

Som tidigare nämnts avslutar Application Gateway TLS-trafik från klienten vid Application Gateway-lyssnaren (vi kallar den klient delens anslutning), dekrypterar trafiken, tillämpar de nödvändiga reglerna för att fastställa backend-servern som begäran måste vidarebefordras till och upprättar en ny TLS-session med backend-servern (vi kallar Server delens anslutning).

I följande tabeller beskrivs skillnaderna i SNI mellan v1-och v2-SKU: n i termer av frontend-och backend-anslutningar.

### <a name="frontend-tls-connection-client-to-application-gateway"></a>Klient delens TLS-anslutning (klient till Programgateway)

---
Scenario | v1 | v2 |
| --- | --- | --- |
| Om klienten anger SNI-huvud och alla lyssnare för flera platser är aktiverade med flaggan "Kräv SNI" | Returnera lämpligt certifikat och om platsen inte finns (enligt server_name) återställs anslutningen. | Returnerar lämpligt certifikat om det är tillgängligt, annars returnerar certifikatet för den första HTTPS-lyssnare som kon figurer ATS (i ordningen)|
| Om klienten inte anger något SNI-huvud och om alla rubriker för flera platser är aktiverade med "Kräv SNI" | Återställer anslutningen | Returnerar certifikatet för den första HTTPS-lyssnare som kon figurer ATS (i ordningen)
| Om klienten inte anger SNI-huvud och om det finns en grundläggande lyssnare som kon figurer ATS med ett certifikat | Returnerar certifikatet som kon figurer ATS i den grundläggande lyssnaren till klienten (standard-eller fallback-certifikat) | Returnerar certifikatet för den första HTTPS-lyssnare som kon figurer ATS (i ordningen) |

### <a name="backend-tls-connection-application-gateway-to-the-backend-server"></a>Server dels TLS-anslutning (Application Gateway till backend-servern)

#### <a name="for-probe-traffic"></a>För avsöknings trafik

---
Scenario | v1 | v2 |
| --- | --- | --- |
| SNI (server_name)-huvud under TLS-handskakning som FQDN | Ange som FQDN från backend-poolen. Som enligt [RFC 6066](https://tools.ietf.org/html/rfc6066)tillåts inte textuella IPv4-och IPv6-adresser i SNI-värdnamnet. <br> **Obs:** FQDN i backend-poolen ska DNS matcha till backend-serverns IP-adress (offentlig eller privat) | SNI-huvud (server_name) anges som värd namnet från den anpassade avsökningen som är kopplad till HTTP-inställningarna (om den har kon figurer ATS), i annat fall från det värdnamn som anges i HTTP-inställningarna, annars från det fullständiga domän namn som anges i Server delen Prioritetsordningen är anpassad avsökning > HTTP-inställningar > backend-pool. <br> **Obs:** Om värd namnen som kon figurer ATS i HTTP-inställningar och anpassad avsökning skiljer sig åt, kommer SNI att anges som värdnamn från den anpassade avsökningen enligt prioritet.
| Om Server delens adresspool är en IP-adress (v1) eller om anpassad avsöknings-värdnamn har kon figurer ATS som IP-adress (v2) | SNI (server_name) anges inte. <br> **Obs:** I det här fallet ska backend-servern kunna returnera ett standard-/fallback-certifikat och detta bör tillåtas i HTTP-inställningar under autentiseringscertifikatet. Om inget standard-/fallback-certifikat har kon figurer ATS i backend-servern och SNI förväntas, kan servern återställa anslutningen och leda till avsöknings problem | I prioritetsordning som nämnts tidigare, om de har IP-adress som värdnamn, kommer SNI inte att anges enligt [RFC 6066](https://tools.ietf.org/html/rfc6066). <br> **Obs:** SNI anges inte heller i v2-avsökningar om ingen anpassad avsökning har kon figurer ATS och inget värdnamn har angetts för HTTP-inställningar eller backend-poolen |

> [!NOTE] 
> Om en anpassad avsökning inte har kon figurer ATS skickar Application Gateway en standard avsökning i formatet- \<protocol\> ://127.0.0.1: \<port\> /. För en standard-HTTPS-avsökning kommer den till exempel att skickas som https://127.0.0.1:443/ . Observera att den 127.0.0.1 som anges här bara används som HTTP-värd rubrik och enligt RFC 6066, kommer inte att användas som SNI-huvud. Mer information om hälso avsöknings fel finns i [fel söknings guide för Server dels hälsa](application-gateway-backend-health-troubleshooting.md).

#### <a name="for-live-traffic"></a>För Live-trafik

---
Scenario | v1 | v2 |
| --- | --- | --- |
| SNI (server_name)-huvud under TLS-handskakning som FQDN | Ange som FQDN från backend-poolen. Som enligt [RFC 6066](https://tools.ietf.org/html/rfc6066)tillåts inte textuella IPv4-och IPv6-adresser i SNI-värdnamnet. <br> **Obs:** FQDN i backend-poolen ska DNS matcha till backend-serverns IP-adress (offentlig eller privat) | SNI-huvud (server_name) anges som värd namn från HTTP-inställningarna, annars om alternativet *PickHostnameFromBackendAddress* har valts eller om inget värdnamn anges, anges det som FQDN i konfigurationen för backend-poolen
| Om adressen till Server delens adresspool är en IP-adress eller ett värdnamn som inte angetts i HTTP-inställningarna | SNI anges inte enligt [RFC 6066](https://tools.ietf.org/html/rfc6066) om posten för backend-poolen inte är ett fullständigt domän namn | SNI anges som värd namn från det inloggade fullständiga domän namnet från klienten och Server dels certifikatets CN måste matcha det här värd namnet.

## <a name="next-steps"></a>Nästa steg

När du har lärt dig om end to end-TLS går du till [Konfigurera end to end-TLS genom att använda Application Gateway med PowerShell](application-gateway-end-to-end-ssl-powershell.md) för att skapa en Programgateway med end to end-TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png