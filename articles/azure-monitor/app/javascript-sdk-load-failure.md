---
title: Felsöka SDK-inläsnings fel för JavaScript-webbprogram – Azure Application Insights
description: Felsöka SDK-inläsnings fel för JavaScript-webbprogram
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6295a56abbf3466c68b968c935936dbc10e22fb5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711424"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>Felsöka SDK-inläsnings fel för JavaScript-webbappar

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

Undantags felet för SDK-inläsningen skapas och rapporteras av JavaScript-kodfragmentet (v3 eller senare) när SDK-skriptet inte kunde laddas ned eller initieras. Simplistically-klienten (webbläsare) kunde inte ladda ned Application Insights SDK eller initiera från den identifierade värd sidan och därför kommer ingen telemetri eller händelser att rapporteras.

![Översikt över Azure Portal webbläsarens haveriation](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> Detta undantag stöds i alla större webbläsare som har stöd för Fetch () API eller XMLHttpRequest. Detta omfattar inte IE 8 och nedan, så du får inte den här typen av undantag som rapporteras från dessa webbläsare (om inte din miljö innehåller en Fetch-polyfyllning).

![information om undantag i webbläsare](./media/javascript-sdk-load-failure/exception.png)

Stack informationen innehåller grundläggande information med URL: erna som används av slutanvändaren.

| Name                      | Beskrivning                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;CDN- &nbsp; slutpunkt&gt; | Den URL som användes (och misslyckades) för att hämta SDK: n.                                                      |
| &lt;Hjälp &nbsp; länk&gt;    | En URL som länkar till fel söknings dokumentation (den här sidan).                                              |
| &lt;Värd- &nbsp; URL&gt;     | Den fullständiga URL: en för sidan som slutanvändaren använde.                                                    |
| &lt;Slut punkts- &nbsp; URL&gt; | Den URL som användes för att rapportera undantaget kan vara till hjälp för att identifiera om värd sidan har öppnats från det offentliga Internet eller ett privat moln.

De vanligaste orsakerna till detta undantag:

- Tillfälligt nätverks anslutnings problem.
- Application Insights CDN-avbrott.
- Det gick inte att initiera SDK efter inläsning av skriptet.
- Application Insights JavaScript-CDN har blockerats.

[Tillfälliga nätverks anslutnings fel](#intermittent-network-connectivity-failure) är den vanligaste orsaken till det här undantaget, särskilt i scenarier med mobil nätverks växling där användarna förlorar nätverks anslutningen tillfälligt.

I följande avsnitt beskrivs hur du felsöker varje möjlig rotor saken till det här felet.

> [!NOTE]
> Flera av de här fel söknings stegen förutsätter att ditt program har direkt kontroll över kodfragmentets &lt; skript/ &gt; tagg och dess konfiguration som returneras som en del av HTML-sidan för värd. Om du inte gör det gäller inte de identifierade stegen för ditt scenario.

## <a name="intermittent-network-connectivity-failure"></a>Tillfälligt nätverks anslutnings problem

**Om det uppstår tillfälliga nätverks anslutnings problem finns det färre möjliga lösningar och de kommer vanligt vis att lösa sig själva under en kort tids period.** Om användaren till exempel laddar om webbplatsen (uppdaterar sidan) kommer filerna att hämtas och cachelagras lokalt tills en uppdaterad version släpps.

> [!NOTE]
> Om det här undantaget är beständigt och inträffar över många av dina användare (som diagnostiseras av en snabb och varaktig nivå av detta undantag som rapporteras) tillsammans med en minskning av normal klient telemetri, är det _inte troligt_ att problem med nätverks anslutningen är orsaken till problemet och du bör fortsätta diagnostisera med de andra kända problemen.

I den här situationen är det osannolikt att tillhandahålla eller minska antalet förekomster av det här undantaget, eftersom ditt eget CDN kommer att påverkas av samma problem.

Samma sak gäller även när du använder SDK via NPM-paket-lösningen. Från slutanvändarna när detta inträffar kan det dock hända att hela programmet inte kan läsas in/initieras (inte _bara_ för TELEMETRI-SDK – som de inte ser visuellt), så att de förmodligen uppdaterar webbplatsen tills den läses in helt.

Du kan också försöka att använda [NPM-paket](#use-npm-packages-to-embed-the-application-insight-sdk) för att bädda in Application Insights SDK.

För att minimera tillfälliga problem med nätverks anslutningen har vi implementerat Cache-Control rubriker på alla CDN-filer så att när slutanvändarens webbläsare har laddat ned den aktuella versionen av SDK behöver den inte laddas ned igen och webbläsaren återanvänder den tidigare hämtade kopian (se [hur cachelagring fungerar](../../cdn/cdn-how-caching-works.md)). Om cachelagringen Miss lyckas eller om det finns en ny version, måste användarens webbläsare ladda ned den uppdaterade versionen. Så du kan se bakgrunds nivån _"brus"_ i scenariot för att kontrol lera fel eller en tillfällig insamling när en ny version sker och görs allmänt tillgänglig (distribueras till CDN).
 
## <a name="application-insights-cdn-outage"></a>Application Insights CDN-avbrott

Du kan kontrol lera om det finns ett Application Insights CDN-avbrott genom att försöka komma åt CDN-slutpunkten direkt från webbläsaren (till exempel https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js eller https://js.monitor.azure.com/scripts/b/ai.2.min.js) från en annan plats än dina slutanvändare är förmodligen från din egen utvecklings dator (förutsatt att din organisation inte har blockerat den här domänen).

Om du bekräftar att det finns ett avbrott kan du [skapa ett nytt support ärende](https://azure.microsoft.com/support/create-ticket/) eller prova att ändra den URL som används för att hämta SDK: n.

### <a name="change-the-cdn-endpoint"></a>Ändra CDN-slutpunkten
  
När kodfragmentet och dess konfiguration returneras av programmet som en del av varje genererad sida kan du ändra kodfragment- `src` konfigurationen så att den använder en annan URL för SDK: n. Genom att använda den här metoden kan du kringgå det blockerade CDN-problemet eftersom den nya URL: en inte ska blockeras.

Nuvarande Application Insights JavaScript SDK CDN-slutpunkter
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> `https://js.monitor.azure.com/`Slut punkten är ett alias som gör att vi kan växla mellan CDN-leverantörer inom cirka 5 minuter, utan att du behöver ändra någon konfiguration. Detta gör att vi kan åtgärda identifierade CDN-relaterade problem snabbare om en CDN-provider har regionala eller globala problem utan att alla behöver ändra sina inställningar.

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>Det gick inte att initiera SDK efter inläsning av skriptet

När SDK: n inte kan initieras har &lt; skriptet/ &gt; laddats ned från CDN, men det går inte att initiera. Detta kan bero på saknade eller ogiltiga beroenden eller någon form av JavaScript-undantag.

Det första du bör kontrol lera är om SDK: n har laddats ned, om skriptet inte har hämtats, är det här scenariot __inte__ orsaken till undantags felet för SDK-inläsning.

Snabb kontroll: Använd en webbläsare som stöder utvecklarverktyg (F12), verifiera på fliken nätverk att skriptet som definierats i ```src``` kodfragment konfigurationen hämtades med svars koden 200 (lyckades) eller en 304 (inte ändrad). Du kan också använda ett verktyg som Fiddler för att granska nätverks trafiken.

I avsnitten nedan finns olika rapporterings alternativ, det rekommenderar att du antingen skapar ett support ärende eller höjer ett problem på GitHub.

 Grundläggande rapporterings regler:

- Om problemet endast påverkar ett litet antal användare och en specifik eller delmängd av webb läsar versioner (se informationen om det rapporterade undantaget), är det sannolikt en slut användar-eller miljö som bara kräver att ditt program tillhandahåller ytterligare `polyfill` implementeringar. I dessa fall kan du [skicka en fil till GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).
- Om det här problemet påverkar hela programmet och alla dina användare påverkas, är det förmodligen ett release-relaterat problem och därför bör du [skapa ett nytt support ärende](https://azure.microsoft.com/support/create-ticket/).

### <a name="javascript-exceptions"></a>JavaScript-undantag

Först kan du söka efter JavaScript-undantag, använda en webbläsare som stöder utvecklarverktyg (F12) läsa in sidan och granska om några undantag har uppstått.

Om det finns undantag som rapporteras i SDK-skriptet (till exempel ai.2.min.js), kan det betyda att konfigurationen som skickas till SDK: n innehåller oväntad eller saknad konfiguration eller att en felaktig version har distribuerats till CDN.

Du kan kontrol lera om konfigurationen är felaktig genom att ändra konfigurationen som skickas till kodfragmentet (om det inte redan finns) så att den bara innehåller din Instrumentation-nyckel som ett sträng värde.

> src: " https://js.monitor.azure.com/scripts/b/ai.2.min.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY"<br />
> }});<br />

Om du fortfarande ser ett JavaScript-undantag i SDK-skriptet när du använder den här minimala konfigurationen, [skapar du ett nytt support ärende](https://azure.microsoft.com/support/create-ticket/) eftersom det kräver att den felaktiga versionen återställs på grund av ett problem med en nyligen distribuerad version.

Om undantaget försvinner orsakas problemet troligt vis av ett typ matchnings fel eller ett oväntat värde. Börja lägga till dina konfigurations alternativ tillbaka en i taget och testa tills undantaget inträffar igen. Kontrol lera sedan dokumentationen för objektet som orsakar problemet. Om dokumentationen är oklar eller om du behöver hjälp kan du ange [ett problem på GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).

Om konfigurationen tidigare har distribuerats och fungerat, men precis börjat rapportera detta undantag, kan det vara ett problem med en nyligen distribuerad version, kontrol lera om den påverkar endast en liten uppsättning användare/webbläsare och antingen fil-eller [GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues) eller  [skapa ett nytt support ärende](https://azure.microsoft.com/support/create-ticket/).

### <a name="enable-console-debugging"></a>Aktivera fel sökning av konsol

Om det inte finns några undantag som genereras nästa steg är att aktivera fel sökning av konsol genom att lägga till `loggingLevelConsole` inställningen i konfigurationen. Detta skickar alla initierings fel och varningar till webbläsarens konsol (normalt tillgängligt via utvecklarverktyg). Eventuella rapporterade fel bör vara själv för klar ande och om du behöver ytterligare hjälp [filen ett problem med GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).

> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> loggingLevelConsole: 2<br />
> }});<br />

> [!NOTE]
> Under initieringen utför SDK några grundläggande kontroller för kända viktiga beroenden. Om dessa inte tillhandahålls av den aktuella körnings miljön rapporterar det att fel uppstår som varnings meddelanden till-konsolen, men endast om `loggingLevelConsole` är större än noll.

Om det fortfarande inte går att initiera kan du försöka aktivera ```enableDebug``` konfigurations inställningen. Detta innebär att alla interna fel genereras som ett undantag (vilket leder till telemetri). Eftersom det här är en utvecklare som bara konfigurerar den kommer det förmodligen att få störningar med undantag som visas som en del av vissa interna kontroller, så du måste granska varje undantag för att avgöra vilket problem som gör att SDK: n Miss fungerar. Använd den icke-minified versionen av skriptet (Observera tillägget under det är ". js" och inte ".min.js"), annars blir undantagen oläsliga.

> [!WARNING]
> Detta är endast en utvecklare-inställning och ska aldrig aktive ras i en fullständig produktions miljö eftersom du förlorar telemetri.

> src: " https://js.monitor.azure.com/scripts/b/ai.2.min.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> enableDebug: sant<br />
> }});<br />

Om detta fortfarande inte ger några insikter bör du ange [ett problem på GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues) med informationen och en exempel plats om du har en. Ta reda på hur du kan identifiera problemet med webbläsarens version, operativ system och JS Framework-information.

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>Application Insights JavaScript-CDN har blockerats

Det CDN som blockeras är möjligt när en Application Insights JavaScript SDK CDN-slutpunkten har rapporter ATS och/eller identifierats som osäker. När detta inträffar kommer det att leda till att slut punkten är offentligt blockerad – listad och användare av de här listorna börjar blockera all åtkomst.

För att lösa problemet kräver det att ägaren av CDN-slutpunkten fungerar med den blockerande entiteten som har markerat slut punkten som osäker så att den kan tas bort från den aktuella listan.

Kontrol lera om CDN-slutpunkten har identifierats som osäker.
- [Google-Oh-rapport](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

Beroende på hur ofta programmet, brand väggen eller miljön uppdaterar sina lokala kopior av listorna, kan det ta lång tid och/eller kräva manuella åtgärder av slutanvändare eller företagets IT-avdelningar för att tvinga fram en uppdatering eller uttryckligen tillåta CDN-slutpunkterna att lösa problemet.

Om CDN-slutpunkten identifieras som osäker, [skapar du ett support ärende](https://azure.microsoft.com/support/create-ticket/) för att säkerställa att problemet löses så snart som möjligt.

För att *eventuellt* kringgå det här problemet snabbare kan du [ändra SDK](#change-the-cdn-endpoint): s CDN-slutpunkt.

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>Application Insights JavaScript-CDN blockeras (av slutanvändaren – blockerad av webbläsare, installerad blockering, personlig brand vägg)

Kontrol lera om slutanvändarna har:

- Installerat ett webb läsar tillägg (vanligt vis någon form av AD, skadlig kod eller blockering av popup-fönster).
- Blockerad (eller tillåts inte) Application Insights CDN-slutpunkter i webbläsare eller proxy.
- Konfigurerat en brand Väggs regel som gör att CDN-domänen för SDK: n blockeras (eller att DNS-posten inte löses).

Om de har konfigurerat något av dessa alternativ måste du arbeta med dem (eller tillhandahålla dokumentation) för att tillåta CDN-slutpunkter.

Det är möjligt att plugin-programmet som de har installerat använder sig av den offentliga blockeringslistan. Om så inte är fallet är det förmodligen någon annan manuellt konfigurerad lösning eller använder en privat domän blockeringslistan.

#### <a name="add-exceptions-for-cdn-endpoints"></a>Lägg till undantag för CDN-slutpunkter

Arbeta med dina slutanvändare eller Tillhandahåll dokumentation som informerar dem om att de ska tillåta att skript från Application Insights CDN-slutpunkter hämtas genom att inkludera dem i webbläsarens plugin-eller brand Väggs regel undantags lista (varierar beroende på användarens miljö).

Här är ett exempel på hur du konfigurerar [Chrome för att tillåta eller blockera åtkomst till webbplatser.](https://support.google.com/chrome/a/answer/7532419?hl=en)

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Application Insights CDN blockeras (av företags brand väggen)

Om slutanvändarna finns i ett företags nätverk är det förmodligen brand Väggs lösningen och att deras IT-avdelning har implementerat någon form av Internet filtrerings system. I så fall måste du arbeta med dem för att tillåta de nödvändiga reglerna för dina slutanvändare.

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>Lägg till undantag för CDN-slutpunkter för företag

  Detta liknar tillägg av [undantag för slutanvändare](#add-exceptions-for-cdn-endpoints), men du måste arbeta med företagets IT-avdelning om du vill att de ska kunna konfigurera Application Insights CDN-slutpunkter som ska laddas ned genom att inkludera (eller ta bort) dem i alla domäner som blockerar listor eller tillåter List tjänster.

  > [!WARNING]
  > Om din företags användare använder ett [privat moln](https://azure.microsoft.com/overview/what-is-a-private-cloud/) och de inte kan göra någon form av undantag för att tillhandahålla sina interna användare med offentlig åtkomst för CDN-slutpunkterna måste du använda [Application Insights NPM-paket](https://www.npmjs.com/package/applicationinsights) eller vara värd för Application Insights SDK på din egen CDN.  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>Ytterligare fel sökning för blockerad CDN

> [!NOTE]
> Om dina användare använder ett [privat moln](https://azure.microsoft.com/overview/what-is-a-private-cloud/) och de inte har åtkomst till det offentliga Internet måste du vara värd för SDK: n på din egen CDN eller använda NPM-paket.

#### <a name="host-the-sdk-on-your-own-cdn"></a>Var värd för SDK: n på din egen CDN

 I stället för att dina slutanvändare laddar ned Application Insights SDK från det offentliga CDN kan du vara värd för Application Insights SDK från din egen CDN-slutpunkt. Vi rekommenderar att du använder en speciell version (AI. 2. #. # .min.js) så att det är lättare att identifiera vilken version du använder. Du kan också regelbundet uppdatera den till den aktuella versionen (ai.2.min.js) så att du kan utnyttja eventuella fel korrigeringar och nya funktioner som blir tillgängliga.

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>Använda NPM-paket för att bädda in Application Insight SDK

I stället för att använda kodfragmentet och offentliga CDN-slutpunkter kan du använda [NPM-paketen](https://www.npmjs.com/package/applicationinsights) för att inkludera SDK som en del av dina egna JavaScript-filer. SDK: n kommer bara att bli ett annat paket inom dina egna skript.

> [!NOTE]
> När du använder NPM-paket bör du också använda någon form av [JavaScript](https://www.bing.com/search?q=javascript+bundler) -paket för att hjälpa till med kod delning och minimering.

Precis som med kodfragmentet, är det också möjligt att dina egna skript (med eller utan att använda SDK NPM-paket) kan påverkas av samma spärrnings problem som anges här, beroende på ditt program, dina användare och ditt ramverk du kanske vill överväga att implementera något som liknar logiken i kodfragmentet för att identifiera och rapportera de här problemen.


## <a name="next-steps"></a>Nästa steg 
- [Få ytterligare hjälp genom att arkivera ett problem på GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [Övervaka webb sidans användning](javascript.md)
