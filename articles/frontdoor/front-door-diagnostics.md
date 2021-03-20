---
title: Övervaka mått och loggar i Azures front dörr | Microsoft Docs
description: I den här artikeln beskrivs de olika mått och åtkomst loggar som stöds av Azures front dörr
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: 58db217ca173acbe0356596de916216c4ab7f241
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715555"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Övervaka mått och loggar i Azures front dörr

Genom att använda Azures front dörr kan du övervaka resurser på följande sätt:

- **Mått**. Azures front dörr har för närvarande åtta mått för att visa prestanda räknare.
- **Loggar**. Med aktivitets-och diagnostikloggar kan prestanda, åtkomst och annan information sparas eller förbrukas från en resurs i övervaknings syfte.

##  <a name="metrics"></a><a name="metrics"></a>Mått

Mått är en funktion för vissa Azure-resurser som gör att du kan visa prestanda räknare i portalen. Följande är tillgängliga mått på front dörren:

| Metric | Mått visnings namn | Enhet | Dimensioner | Beskrivning |
| --- | --- | --- | --- | --- |
| RequestCount | Antal begäranden | Antal | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antalet klient förfrågningar som hanteras av frontend-dörren.  |
| RequestSize | Begär ande storlek | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antalet byte som har skickats som begär Anden från klienter till front dörren. |
| ResponseSize | Svars storlek | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antalet byte som har skickats som svar från Front dörren till klienter. |
| TotalLatency | Total svars tid | Millisekunder | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Den totala tiden från klient förfrågan som mottagits av front dörren tills de sista svars byte som skickades från AFD till klienten. |
| BackendRequestCount | Antal Server dels begär Anden | Antal | HttpStatus</br>HttpStatusGroup</br>Serverdel | Antalet förfrågningar som skickats från Front dörren till Server delar. |
| BackendRequestLatency | Svars tid för Server del | Millisekunder | Serverdel | Tiden räknat från när begäran skickades av frontend till Server delen tills front dörren fick den senaste svars byten från Server delen. |
| BackendHealthPercentage | Server delens hälso procent | Procent | Serverdel</br>BackendPool | Procent andelen lyckade hälso avsökningar från Front dörren till Server delar. |
| WebApplicationFirewallRequestCount | Antal begär Anden om webb programs brand vägg | Antal | PolicyName</br>RuleName</br>Action | Antalet klient begär Anden som bearbetats av program nivå säkerheten för front dörren. |

## <a name="activity-logs"></a><a name="activity-log"></a>Aktivitets loggar

Aktivitets loggar innehåller information om de åtgärder som utförs på front dörren. De avgör också vad, vem och när det gäller Skriv-, post-eller borttagnings åtgärder som utförs på front dörren.

>[!NOTE]
>Aktivitets loggar innehåller inte Läs åtgärder (get). De omfattar inte heller åtgärder som du utför med hjälp av antingen Azure Portal eller ursprungligt hanterings-API.

Få åtkomst till aktivitets loggar i din front dörr eller alla loggar för dina Azure-resurser i Azure Monitor. Så här visar du aktivitetsloggar:

1. Välj din instans av frontend-dörren.
2. Välj **aktivitets logg**.

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="Aktivitetslogg":::

3. Välj en filtrerings omfattning och välj sedan **Använd**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnostikloggar
Diagnostikloggar ger omfattande information om åtgärder och fel som är viktiga för granskning och fel sökning. Diagnostikloggar skiljer sig från aktivitets loggar.

Aktivitets loggar ger insikter om de åtgärder som utförs på Azure-resurser. Diagnostikloggar ger insikter om åtgärder som din resurs har utfört. Mer information finns i [Azure Monitor diagnostikloggar](../azure-monitor/essentials/platform-logs-overview.md).

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="Diagnostikloggar":::

Så här konfigurerar du diagnostikloggar för din frontend-dörr:

1. Välj din Azure-front dörr.

2. Välj **diagnostikinställningar**.

3. Välj **Aktivera diagnostik**. Arkivera diagnostikloggar tillsammans med mått till ett lagrings konto, strömma dem till en händelsehubben eller skicka dem till Azure Monitor loggar.

Front dörren innehåller för närvarande diagnostikloggar (batch-varje timme). Diagnostikloggar tillhandahåller enskilda API-begäranden med varje post med följande schema:

| Egenskap  | Beskrivning |
| ------------- | ------------- |
| BackendHostname | Om begäran vidarebefordrades till en server del representerar Server delens värdnamn. Det här fältet är tomt om begäran omdirigeras eller vidarebefordras till ett regionalt cacheminne (när cachelagring är aktiverat för regeln). |
| CacheStatus | För cachelagring av scenarier definierar det här fältet cache-träff/missar på POP |
| ClientIp | IP-adressen för klienten som gjorde begäran. Om det fanns ett X-vidarebefordrat-för-huvud i begäran plockas klientens IP-adress från samma. |
| ClientPort | IP-porten för den klient som gjorde begäran. |
| HttpMethod | HTTP-metod som används av begäran. |
| HttpStatusCode | HTTP-statuskod som returnerades från proxyservern. |
| HttpStatusDetails | Resulterande status för begäran. Innebörd av detta sträng värde finns i en status referens tabell. |
| HttpVersion | Typ av begäran eller anslutning. |
| POP | Kort namn på den kant där begäran landats. |
| RequestBytes | Storleken på HTTP Request-meddelandet i byte, inklusive begärandehuvuden och begär ande texten. |
| RequestUri | URI för den mottagna begäran. |
| ResponseBytes | Byte som skickats av backend-servern som svar.  |
| RoutingRuleName | Namnet på den routningsregler som begäran matchade. |
| RulesEngineMatchNames | Namnen på de regler som begäran matchade. |
| SecurityProtocol | TLS/SSL-protokollets version som används av begäran eller null om ingen kryptering. |
| SentToOriginShield </br> (inaktuell) * **se kommentarer om utfasning i följande avsnitt.**| Om värdet är true innebär det att begäran besvarades från ursprungs sköldens cacheminne i stället för Edge-pop. Ursprungs sköld är ett överordnat cacheminne som används för att förbättra förhållandet mellan cacheträffar. |
| isReceivedFromClient | Om värdet är true innebär det att begäran kom från klienten. Om det här värdet är falskt, är begäran en brist i kanten (underordnad POP) och är besvarad från ursprungs skölden (överordnad POP). |
| TimeTaken | Tiden från första byte av begäran till front dörren till sista mottagna byte, i sekunder. |
| TrackingReference | Den unika referens strängen som identifierar en begäran som betjänas av en front dörr, som också skickas som X-Azure-ref-huvud till klienten. Krävs för att söka efter information i åtkomst loggarna för en speciell begäran. |
| UserAgent | Webbläsarens typ som används av klienten. |
| Mängden | Det här fältet innehåller den speciella typen av fel för ytterligare fel sökning. </br> Möjliga värden är: </br> **Noerror**: indikerar att inget fel hittades. </br> **CertificateError**: allmänt SSL-certifikat fel.</br> **CertificateNameCheckFailed**: värd namnet i SSL-certifikatet är ogiltigt eller matchar inte. </br> **ClientDisconnected**: det gick inte att utföra begäran på grund av klient nätverks anslutning. </br> **UnspecifiedClientError**: allmänt klient fel. </br> **InvalidRequest**: ogiltig begäran. Det kan bero på en felaktig rubrik, brödtext och URL. </br> **DNSFailure**: DNS-haveri. </br> **DNSNameNotResolved**: det gick inte att matcha Server namnet eller adressen. </br> **OriginConnectionAborted**: anslutningen med ursprunget stoppades plötsligt. </br> **OriginConnectionError**: fel i allmän ursprungs anslutning. </br> **OriginConnectionRefused**: det gick inte att upprätta anslutningen till ursprunget. </br> **OriginError**: allmänt ursprungs fel. </br> **OriginInvalidResponse**: ursprung returnerade ett ogiltigt eller okänt svar. </br> **OriginTimeout**: tids gränsen för ursprungs förfrågan har gått ut. </br> **ResponseHeaderTooBig**: ursprunget returnerade för stort av ett svars huvud. </br> **RestrictedIP**: begäran blockerades på grund av en begränsad IP-adress. </br> **SSLHandshakeError**: det gick inte att upprätta en anslutning till sitt ursprung på grund av ett SYNKRONISERINGSFEL vid SSL-skakning. </br> **UnspecifiedError**: ett fel inträffade som inte fick plats i något av felen i tabellen. |

### <a name="sent-to-origin-shield-deprecation"></a>Skickas till ursprungs sköldens utfasning
Den råa logg egenskapen **isSentToOriginShield** har ersatts och ersatts av ett nytt fält **isReceivedFromClient**. Använd det nya fältet om du redan använder det inaktuella fältet. 

Obehandlade loggar innehåller loggar som skapats från både CDN-Edge (underordnad POP) och ursprungs sköld. Ursprungs sköld syftar på överordnade noder som är strategiskt placerade över hela världen. Dessa noder kommunicerar med ursprungs servrar och minskar belastningen på belastningen på ursprunget. 

För varje begäran som går till ursprungs sköld finns det två logg poster:

* En för Edge-noder
* En för ursprungs sköld. 

Om du vill särskilja utgången eller svaren från Edge-noderna eller ursprungs skölden kan du använda fältet **isReceivedFromClient** för att hämta rätt data. 

Om värdet är false innebär det att begäran besvaras från ursprungs sköld till Edge-noder. Den här metoden är effektiv för att jämföra obehandlade loggar med fakturerings data. Avgifter debiteras inte för utgående från ursprungs sköld till Edge-noderna. Avgifter debiteras för utgående från Edge-noderna till klienter. 

**Kusto Query-exempel för att utesluta loggar som skapats på ursprungs sköld i Log Analytics.**

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> För olika konfigurationer av Routning och trafik, kan vissa av fälten som backendHostname, cacheStatus, isReceivedFromClient och POP-fält svara med olika värden. I tabellen nedan beskrivs de olika värdena som fälten har för olika scenarier:

| Scenarier | Antal logg poster | POP | BackendHostname | isReceivedFromClient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Routningsregler utan cachelagring aktiverat | 1 | Kant-POP-kod | Backend där begäran vidarebefordrades | Sant | CONFIG_NOCACHE |
| Routningsregler med cachelagring aktiverat. Cacheträff i Edge-popup | 1 | Kant-POP-kod | Tom | Sant | TRÄFFA |
| Routningsregler med cachelagring aktiverat. Missar i cacheminnet på Edge-POP, men cache träff vid överordnat cache-fönster | 2 | 1. Edge POP-kod</br>2. POP-kod för överordnad cache | 1. POP-värdnamn för överordnad cache</br>2. Tom | 1. True</br>2. false | 1. SAKNAT</br>2. TRÄFF |
| Routningsregler med cachelagring aktiverat. Cacheminnet missar i Edge-POP, men delvis cache träff i överordnat cache-fönster | 2 | 1. Edge POP-kod</br>2. POP-kod för överordnad cache | 1. POP-värdnamn för överordnad cache</br>2. Server del som hjälper till att fylla i cache | 1. True</br>2. false | 1. SAKNAT</br>2. PARTIAL_HIT |
| Routningsregler med cachelagring aktiverat. Cachelagra PARTIAL_HIT i Edge-POP, men cache träff på överordnad cache-POP | 2 | 1. Edge POP-kod</br>2. POP-kod för överordnad cache | 1. Edge POP-kod</br>2. POP-kod för överordnad cache | 1. True</br>2. false | 1. PARTIAL_HIT</br>2. TRÄFF |
| Routningsregler med cachelagring aktiverat. Missar i cacheminnet på både Edge och överordnad cache | 2 | 1. Edge POP-kod</br>2. POP-kod för överordnad cache | 1. Edge POP-kod</br>2. POP-kod för överordnad cache | 1. True</br>2. false | 1. SAKNAT</br>2. SAKNAT |

> [!NOTE]
> För cache-scenarier är värdet för cache-status partial_hit när några av bytena för en begäran erhålls från den främre dörr kanten eller ursprungs skärms-cacheminnet, medan några av de byte som hämtas från ursprunget för stora objekt.

Front dörren använder en teknik som kallas objekt segment. När en stor fil begärs hämtar front dörren mindre delar av filen från ursprunget. När POP-servern på den främre dörren tar emot en fullständig eller byte-intervall av den begärda filen begär servern från ursprunget i segment om 8 MB.

När segmentet anländer till den främre dörren, cachelagras det och betjänas direkt för användaren. Den främre dörren för hämtar sedan nästa segment parallellt. Med den här för hämtningen ser du till att innehållet förblir ett segment framför användaren, vilket minskar svars tiden. Den här processen fortsätter tills hela filen laddas ned (om så krävs), alla byte-intervall är tillgängliga (om de begärs) eller när klienten stänger anslutningen. Mer information om byte Range-begäran finns i RFC 7233. Den främre dörren cachelagrar alla segment när de tas emot. Hela filen behöver inte cachelagras i cachen med frontend-dörren. Kommande begär Anden om fil-eller byte-intervallen hanteras från cachen med frontend-dörren. Om inte alla segment cachelagras på den främre dörren används för hämtning för att begära segment från ursprunget. Den här optimeringen är beroende av möjligheten för ursprungs servern att stödja byte intervall begär Anden. Om ursprungs servern inte stöder byte intervall begär Anden är den här optimeringen inte effektiv.

## <a name="next-steps"></a>Nästa steg

- [Skapa en profil för en front dörr](quickstart-create-front-door.md)
- [Så här fungerar en front dörr](front-door-routing-architecture.md)
