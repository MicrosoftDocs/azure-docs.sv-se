---
title: Undantags hantering – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Läs mer om hantering av undantag i Threat Modeling Tool. Se information om minskning och Visa kod exempel.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 41532e554623c47e9728c6ccab92d99500e42021
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517435"
---
# <a name="security-frame-exception-management--mitigations"></a>Säkerhets ram: undantags hantering | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF – ta inte med serviceDebug-noden i konfigurations filen](#servicedebug)</li><li>[WCF – ta inte med serviceMetadata-noden i konfigurations filen](#servicemetadata)</li></ul> |
| **Webb-API** | <ul><li>[Kontrol lera att rätt undantags hantering görs i ASP.NET webb-API](#exception)</li></ul> |
| **Webb program** | <ul><li>[Visa inte säkerhets information i fel meddelanden](#messages)</li><li>[Implementera standard fel hanterings sida](#default)</li><li>[Ange distributions metod för åter försäljning i IIS](#deployment)</li><li>[Undantag bör inte fungera säkert](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF – ta inte med serviceDebug-noden i konfigurations filen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänt, NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [FORTIFY kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Steg** | WCF-tjänster (Windows Communication Framework) kan konfigureras för att Visa felsöknings information. Felsöknings information ska inte användas i produktions miljöer. `<serviceDebug>`Taggen definierar om funktionen för fel söknings information är aktive rad för en WCF-tjänst. Om attributet includeExceptionDetailInFaults har angetts till True returneras undantags information från programmet till klienter. Angripare kan utnyttja den ytterligare information som de får från att felsöka utdata för att montera attacker riktade mot ramverket, databasen eller andra resurser som används av programmet. |

### <a name="example"></a>Exempel
Följande konfigurations fil innehåller `<serviceDebug>` taggen: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Inaktivera fel söknings information i tjänsten. Detta kan åstadkommas genom att taggen tas bort `<serviceDebug>` från programmets konfigurations fil. 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF – ta inte med serviceMetadata-noden i konfigurations filen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Allmänt, NET Framework 3 |
| **Referenser**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [FORTIFY kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Steg** | Offentligt exponerande information om en tjänst kan ge angripare en värdefull inblick i hur de kan utnyttja tjänsten. `<serviceMetadata>`Taggen aktiverar funktionen för metadata-publicering. Metadata för tjänsten kan innehålla känslig information som inte ska vara offentligt tillgänglig. Låt minst bara tillåta betrodda användare att komma åt metadata och se till att onödig information inte visas. Ännu bättre, inaktivera möjligheten att publicera metadata. En säker WCF-konfiguration kommer inte att innehålla `<serviceMetadata>` taggen. |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>Kontrol lera att rätt undantags hantering görs i ASP.NET webb-API

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | MVC 5, MVC 6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Undantags hantering i ASP.net webb-API](https://www.asp.net/web-api/overview/error-handling/exception-handling), [modell validering i ASP.net webb-API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Steg** | Som standard översätts de mest undantagna undantagen i ASP.NET webb-API till ett HTTP-svar med status kod `500, Internal Server Error`|

### <a name="example"></a>Exempel
Om du vill kontrol lera status koden som returneras av API `HttpResponseException` : t kan du använda det som visas nedan: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>Exempel
Om du vill ha mer kontroll över undantags svaret `HttpResponseMessage` kan klassen användas på det sätt som visas nedan: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
Undantags filter kan användas för att fånga upp ohanterade undantag som inte är av typen `HttpResponseException` . Undantags filter implementerar `System.Web.Http.Filters.IExceptionFilter` gränssnittet. Det enklaste sättet att skriva ett undantags filter är att härleda från `System.Web.Http.Filters.ExceptionFilterAttribute` klassen och åsidosätta metoden OnException. 

### <a name="example"></a>Exempel
Här är ett filter som konverterar `NotImplementedException` undantag till HTTP-status kod `501, Not Implemented` : 
```csharp
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

Det finns flera sätt att registrera ett webb-API undantags filter:
- Efter åtgärd
- Efter kontrollant
- Globalt

### <a name="example"></a>Exempel
Om du vill tillämpa filtret på en speciell åtgärd lägger du till filtret som ett attribut i åtgärden: 
```csharp
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>Exempel
Om du vill tillämpa filtret på alla åtgärder i en `controller` lägger du till filtret som ett attribut i `controller` klassen: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Exempel
Om du vill tillämpa filtret globalt på alla webb-API-kontroller lägger du till en instans av filtret i `GlobalConfiguration.Configuration.Filters` samlingen. Undantags filter i den här samlingen gäller för alla åtgärder för webb-API-kontroller. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Exempel
För modell valideringen kan modell statusen skickas till CreateErrorResponse-metoden enligt nedan: 
```csharp
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

Se länkarna i avsnittet referenser om du vill ha mer information om exceptionell hantering och modell validering i ASP.NET webb-API 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>Visa inte säkerhets information i fel meddelanden

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Allmänna fel meddelanden tillhandahålls direkt till användaren utan att inkludera känsliga program data. Exempel på känsliga data är:</p><ul><li>Server namn</li><li>Anslutningssträngar</li><li>Användar namn</li><li>Lösenord</li><li>SQL-procedurer</li><li>Information om dynamiska SQL-problem</li><li>Stack spårning och kodrader</li><li>Variabler lagrade i minnet</li><li>Platser för enheter och mappar</li><li>Program installations punkter</li><li>Konfigurations inställningar för värd</li><li>Information om andra interna program</li></ul><p>Att svälla alla fel i ett program och tillhandahålla allmänna fel meddelanden, samt att aktivera anpassade fel i IIS, förhindrar att information avslöjas. SQL Server databas-och .NET-undantags hantering, bland annat fel hanterings arkitekturer, är särskilt utförligt och mycket användbart för en skadlig användare som profileringr ditt program. Visa inte direkt innehållet i en klass som härletts från .NET-undantags klassen och se till att du har rätt undantags hantering så att ett oväntat undantag inte oavsiktligt höjs direkt till användaren.</p><ul><li>Ange allmänna fel meddelanden direkt till användaren som har abstrakt information som finns direkt i undantags-eller fel meddelandet</li><li>Visa inte innehållet i en .NET-undantags klass direkt till användaren</li><li>Sväll alla fel meddelanden och meddela användaren via ett allmänt fel meddelande som skickas till program klienten</li><li>Exponera inte innehållet i undantags klassen direkt till användaren, särskilt returvärdet från `.ToString()` , eller värdena för egenskaperna för meddelandet eller stacktrace. Logga informationen på ett säkert sätt och visa ett mer innocuous-meddelande till användaren</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>Implementera standard fel hanterings sida

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Redigera dialogrutan för inställningar av ASP.NET-felsidor](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd569096(v=ws.10)) |
| **Steg** | <p>När ett ASP.NET-program Miss lyckas och orsakar ett internt HTTP/1. x 500-server fel eller en funktions konfiguration (till exempel filtrering av begär Anden) förhindrar att en sida visas, kommer ett fel meddelande att genereras. Administratörer kan välja om programmet ska visa ett eget meddelande för klienten, ett detaljerat fel meddelande till klienten eller enbart ett detaljerat fel meddelande till localhost. `<customErrors>`Taggen i web.config har tre lägen:</p><ul><li>**På:** Anger att anpassade fel har Aktiver ATS. Om inget defaultRedirect-attribut anges ser användarna ett allmänt fel. De anpassade felen visas för fjärrklienter och till den lokala värden</li><li>**Av:** Anger att anpassade fel har inaktiverats. De detaljerade ASP.NET-felen visas för fjärrklienterna och den lokala värden</li><li>**RemoteOnly:** Anger att anpassade fel endast visas för fjärrklienter och att ASP.NET-fel visas för den lokala värden. Detta är standardvärdet</li></ul><p>Öppna `web.config` filen för programmet/platsen och kontrol lera att taggen har antingen `<customErrors mode="RemoteOnly" />` eller `<customErrors mode="On" />` definierats.</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>Ange distributions metod för åter försäljning i IIS

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [distributions element (ASP.NET Settings schema)](/previous-versions/dotnet/netframework-2.0/ms228298(v=vs.80)) |
| **Steg** | <p>`<deployment retail>`Växeln är avsedd att användas av produktions-IIS-servrar. Den här växeln används för att hjälpa program att köra med bästa möjliga prestanda och minst möjliga läckage av säkerhets information genom att inaktivera programmets möjlighet att generera spårningsutdata på en sida, inaktivera möjligheten att visa detaljerade fel meddelanden för slutanvändare och inaktivera fel söknings växeln.</p><p>Ofta kan växlar och alternativ som är förfokuserade för utvecklare, till exempel spårning av misslyckade begär Anden och fel sökning, aktive ras under pågående utveckling. Vi rekommenderar att distributions metoden på alla produktions servrar anges till återförsäljarversion. öppna machine.config-filen och se `<deployment retail="true" />` till att förblir inställt på sant.</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>Undantag bör inte fungera säkert

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Rapportera ett problem](https://owasp.org/www-community/Fail_securely) |
| **Steg** | Programmet bör inte fungera på ett säkert sätt. Alla metoder som returnerar ett booleskt värde, baserat på vilka ett visst beslut fattas, bör ha ett noggrant skapat undantags block. Det finns många logiska fel på grund av vilka säkerhets problem som är krypa i när undantags blocket skrivs carelessly.|

### <a name="example"></a>Exempel
```csharp
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
Metoden ovan returnerar alltid True, om ett undantag inträffar. Om slutanvändaren tillhandahåller en felaktig URL, som webbläsaren följer, men `Uri()` konstruktorn inte, kommer detta att generera ett undantag och den skadelidande kommer att tas till den giltiga men felaktiga URL: en.