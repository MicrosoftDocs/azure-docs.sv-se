---
title: Hög tillgänglighet med Media Services video på begäran
description: Den här artikeln är en översikt över de Azure-tjänster som du kan använda för att under lätta hög tillgänglighet för VOD-programmet.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: conceptual
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 2b0158c3b1bbee37fdb10c8fc0131be580ad6fc0
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562621"
---
# <a name="high-availability-with-media-services-and-video-on-demand-vod"></a>Hög tillgänglighet med Media Services och video på begäran (VOD)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="high-availability-for-vod"></a>Hög tillgänglighet för VOD

Design mönstret med hög tillgänglighet kallas [Geodes](/azure/architecture/patterns/geodes) i dokumentationen för Azure-arkitekturen. Den beskriver hur duplicerade resurser distribueras till olika geografiska regioner för att tillhandahålla skalbarhet och återhämtning.  Du kan använda Azure-tjänster för att skapa en sådan arkitektur som beskriver många design överväganden för hög tillgänglighet, till exempel redundans, hälso övervakning, belastnings utjämning och säkerhets kopiering och återställning av data.  En sådan arkitektur beskrivs nedan med information om varje tjänst som används i lösningen samt hur enskilda tjänster kan användas för att skapa en arkitektur med hög tillgänglighet för ditt VOD-program.

### <a name="sample"></a>Exempel

Det finns ett exempel som kan användas för att bekanta dig med hög tillgänglighet med Media Services och video på begäran (VOD). Det går också till mer information om hur tjänsterna används för ett VOD-scenario.  Exemplet är inte avsett att användas i produktion i det aktuella formuläret.  Läs noggrant igenom exempel koden och README, i synnerhet avsnittet om [fellägen](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming) innan du integrerar det i ett produktions program.  En produktions implementering av hög tillgänglighet för video på begäran (VOD) bör också noggrant granska sin Content Delivery Networks strategi (CDN).  Kolla in [koden på GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming).

## <a name="overview-of-services"></a>Översikt över tjänster

Tjänsterna som används i den här exempel arkitekturen är:

| Ikon | Name | Beskrivning |
| :--: | ---- | ----------- |
|![Detta är ikonen för Media Services-kontot.](media/media-services-high-availability-encoding/azure-media-services.svg)| Media Services-konto | **Beskrivning:**<br>Ett Media Services konto är start punkten för att hantera, kryptera, koda, analysera och strömma medie innehåll i Azure. Den är kopplad till en Azure Storage konto resurs. Kontot och alla tillhör ande lagrings enheter måste finnas i samma Azure-prenumeration.<br><br>**VOD användning:**<br>Det här är de tjänster som du använder för att koda och leverera dina video-och ljud till gångar.  För hög tillgänglighet skulle du ställa in minst två Media Services konton, var och en i en annan region. [Läs mer om Azure Media Services](media-services-overview.md). |
|![Det här är lagrings konto ikonen.](media/media-services-high-availability-encoding/storage-account.svg)| Lagringskonto | **Beskrivning:**<br>Ett Azure Storage-konto innehåller alla dina Azure Storage data objekt: blobbar, filer, köer, tabeller och diskar. Data är tillgängliga från var som helst i världen via HTTP eller HTTPS.<br><br>Varje Media Services konto, i varje region, har ett lagrings konto i samma region.<br><br>**VOD användning:**<br>Du kan lagra indata och utdata för bearbetning och strömning av VOD. [Läs mer om Azure Storage](../../storage/common/storage-introduction.md). |
|![Detta är Azure Storage Queue-ikonen.](media/media-services-high-availability-encoding/storage-account-queue.svg)| Azure Storage-kö | **Beskrivning:**<br>Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS.<br><br>**VOD användning:**<br>Köer kan användas för att skicka och ta emot meddelanden för att koordinera aktiviteter mellan olika moduler. Exemplet använder en Azure Storage kö, men Azure tillhandahåller andra typer av köer, till exempel Service Bus och Service Fabric Reliable-köer som kan passa dina behov bättre. [Läs mer om Azure Queue](../../storage/queues/storage-queues-introduction.md). |
|![Detta är Azure Cosmos DB ikonen.](media/media-services-high-availability-encoding/azure-cosmos-db.svg)| Azure Cosmos DB  | **Beskrivning:**<br>Azure Cosmos DB är Microsofts globalt distribuerade databas tjänst för flera modeller som skalar data flöde och lagrings utrymme oberoende av alla Azure-regioner över hela världen.<br><br>**VOD användning:**<br>Tabeller kan användas för att lagra status poster för jobb utdata och för att spåra hälso tillståndet för varje Media Services instans. Du kan också spåra/registrera status för varje anrop till Media Services-API: et. [Läs mer om Azure Cosmos DB](../../cosmos-db/introduction.md).  |
|![Detta är den hanterade identitets ikonen.](media/media-services-high-availability-encoding/managed-identity.svg)| Hanterad identitet | **Beskrivning:**<br>Hanterad identitet är en funktion i Azure AD som tillhandahåller en automatiskt hanterad identitet i Azure AD. Den kan användas för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault utan att lagra autentiseringsuppgifter i kod.<br><br>**VOD användning:**<br>Azure Functions kan använda hanterad identitet för att autentisera till Media Services instanser för att ansluta till Key Vault. [Läs mer om hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md). |
|![Detta är Key Vault ikonen.](media/media-services-high-availability-encoding/key-vault.svg)| Key Vault | **Beskrivning:**<br>Azure Key Vault kan användas för säker lagring och strikt kontroll av åtkomst till token, lösen ord, certifikat, API-nycklar och andra hemligheter. Den kan också användas som en nyckel hanterings lösning. Med Azure Key Vault är det enkelt att skapa och kontrollera de krypteringsnycklar som används för att kryptera dina data. Det kan enkelt etablera, hantera och distribuera offentliga och privata Transport Layer Security/Secure Sockets Layer-certifikat (TLS/SSL) för användning med Azure och interna anslutna resurser. Hemligheter och nycklar kan skyddas antingen av program vara eller FIPS 140-2 nivå 2-verifierade HSM: er.<br><br>**VOD användning:**<br>Key Vault kan användas för att ställa in åtkomst principer för tjänstens huvud namn för ditt program.  Den kan användas för att lagra anslutnings strängen till lagrings konton. Vi använder Key Vault för att lagra anslutnings strängar till lagrings konton och Cosmos dB. Du kan också använda Key Vault för att lagra den övergripande kluster konfigurationen. För varje medie tjänst instans kan du lagra prenumerations-ID, resurs grupps namn och konto namn. Mer information finns i hur det används i exemplet. [Läs mer om Key Vault](../../key-vault/general/overview.md). |
|![Detta är Azure Functions ikonen.](media/media-services-high-availability-encoding/function-app.svg)| Azure Functions | **Beskrivning:**<br>Kör små delar av kod (kallas "funktioner") utan att bekymra dig om program infrastrukturen med Azure Functions. [Läs mer om Azure Functions](../../azure-functions/functions-overview.md).<br><br>**VOD användning:**<br>Azure Functions kan användas för att lagra värd för modulerna i ditt VOD-program.  Moduler för ett VOD program kan innehålla:<br><br>**Modulen jobb schemaläggning**<br>Modulen schemaläggning av jobb skulle vara att skicka nya jobb till ett Media Services kluster (två eller fler instanser i olika regioner). Den spårar hälso status för varje Media Services instans och skickar ett nytt jobb till nästa felfria instans.<br><br>**Modul för jobb status**<br>Modulen jobb status lyssnar på händelser för jobbets utgående status från Azure Event Grid-tjänsten. Den lagrar händelser i händelse lagret för att minimera antalet anrop till Media Services API: er i resten av modulerna.<br><br>**Instans hälso modul**<br>Den här modulen spårar skickade jobb och fastställer hälso status för varje Media Services instans. Det spårar avslutade jobb, misslyckade jobb och jobb som aldrig har slutförts.<br><br>**Etablerings modul**<br>Den här modulen etablerar bearbetade till gångar. Den kopierar till gångs data till alla Media Services instanser och konfigurerar Azure-tjänsten för att se till att till gångar kan strömmas även om vissa Media Services instanser inte var tillgängliga. Det skulle också konfigureras för strömmande positionerare.<br><br>**Modul för jobb verifiering**<br>Den här modulen spårar varje skickat jobb, skickar om misslyckade jobb och utför jobb data rensning när ett jobb har slutförts.  |
|![Detta är App Service ikonen.](media/media-services-high-availability-encoding/application-service.svg)| App Service (och plan)  | **Beskrivning:**<br>Azure App Service är en HTTP-baserad tjänst som är värd för webb program, REST API: er och mobila Server delar. Det stöder .NET, .NET Core, Java, ruby, Node.js, PHP eller python. Program körs och skalar i både Windows-och Linux-baserade miljöer.<br><br>**VOD användning:**<br>Varje modul får en App Services värd. [Läs mer om App Service](../../app-service/overview.md). |
|![Det här är Azures främre dörr ikon.](media/media-services-high-availability-encoding/azure-front-door.svg)| Azure Front Door | **Beskrivning:**<br>Azures front dörr används för att definiera, hantera och övervaka den globala dirigeringen av webb trafik genom att optimera för bästa möjliga prestanda och snabb global redundans för hög tillgänglighet.<br><br>**VOD användning:**<br>Azures front dörr kan användas för att dirigera trafik till slut punkter för direkt uppspelning. [Lär dig mer om Azures front dörr](../../frontdoor/front-door-overview.md).  |
|![Detta är Azure Event Grid ikonen.](media/media-services-high-availability-encoding/event-grid-subscription.svg)| Azure Event Grid | **Beskrivning:**<br>Event Grid har skapats för händelsebaserade arkitekturer, har inbyggt stöd för händelser som kommer från Azure-tjänster, t. ex. lagrings-blobbar och resurs grupper. Den har också stöd för anpassade ämnes händelser. Filter kan användas för att dirigera vissa händelser till olika slut punkter, multicast till flera slut punkter och för att säkerställa att händelser levereras på ett tillförlitligt sätt. Den maximerar tillgängligheten genom att sprida sig internt över flera fel domäner i varje region, och över tillgänglighets zoner.<br><br>**VOD användning:**<br>Event Grid kan användas för att spåra alla program händelser och lagra dem för att spara jobb status. [Läs mer om Azure Event Grid](../../event-grid/overview.md). |
|![Detta är Application Insights ikonen.](media/media-services-high-availability-encoding/application-insights.svg)| Application Insights | **Beskrivning:** <br>Funktionen Application Insights i Azure Monitor är en utökningsbar APM-tjänst (Application Performance Management) för utvecklare och DevOps-utvecklare. Den används för att övervaka Live-program. Den identifierar prestanda avvikelser och innehåller analys verktyg för att diagnostisera problem och förstå vad användarna gör med en app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.<br><br>**VOD användning:**<br>Alla loggar kan skickas till Application Insights. Det skulle vara möjligt att se vilken instans som bearbetas varje jobb genom att söka efter korrekt skapade jobb meddelanden. Den kan innehålla alla skickade metadata för metadata, inklusive information om unika identifierare och instans namn. [Läs mer om Application Insights](../../azure-monitor/app/app-insights-overview.md). |
## <a name="architecture"></a>Arkitektur

Det här diagrammet på hög nivå visar arkitekturen för det exempel som tillhandahålls för att komma igång med hög tillgänglighet och medie tjänster.

[![Video på begäran (VOD) arkitektur diagram ](media/media-services-high-availability-encoding/high-availability-architecture.svg) på hög nivå](media/media-services-high-availability-encoding/high-availability-architecture.svg#lightbox)

## <a name="best-practices"></a>Bästa praxis

### <a name="regions"></a>Regioner

* [Skapa](./create-account-howto.md) två (eller fler) Azure Media Services-konton. De två kontona måste finnas i olika regioner. Mer information finns i [regioner där Azure Media Servicess tjänsten har distribuerats](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).
* Ladda upp mediet till samma region som du planerar att skicka jobbet från. Mer information om hur du startar kodning finns i [skapa ett jobb indata från en HTTPS-URL](./job-input-from-http-how-to.md) eller [skapa ett jobb indata från en lokal fil](./job-input-from-local-file-how-to.md).
* Om du sedan behöver skicka [jobbet](./transforms-jobs-concept.md) igen till en annan region kan du använda `JobInputHttp` eller använda `Copy-Blob` för att kopiera data från käll till gångs behållaren till en till gångs behållare i den alternativa regionen.

### <a name="monitoring"></a>Övervakning

* Prenumerera på `JobStateChange` meddelanden i varje konto via Azure Event Grid.
    * [Registrera dig för händelser](./reacting-to-media-services-events.md) via Azure Portal eller CLI (du kan också göra det med event Grid Management SDK)
    * Använd [Microsoft. Azure. EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (som stöder Media Services-händelser internt).
    * Du kan också använda Event Grid händelser via Azure Functions.

    Mer information:

    * Se [ljud analys exemplet](./transforms-jobs-concept.md) som visar hur du övervakar ett jobb med Azure Event Grid, inklusive hur du lägger till en återställning om Azure Event Grid meddelanden fördröjs av någon anledning.
    * Ta en titt på [Azure Event Grid scheman för Media Services händelser](./media-services-event-schemas.md).

* När du skapar ett [jobb](./transforms-jobs-concept.md):
    * Välj slumpmässigt ett konto i listan över konton som används för närvarande (den här listan innehåller vanligt vis båda kontona, men om problem upptäcks kan det bara innehålla ett konto). Om listan är tom kan du generera en avisering så att en operatör kan undersöka den.
    * Skapa en post för att hålla reda på varje synlighetssekvensnummer-jobb och regionen/kontot som används.
* När din `JobStateChange` hanterare får ett meddelande om att ett jobb har nått det schemalagda läget, registrerar du den tid det anger det schemalagda läget och regionen/kontot som används.
* När din `JobStateChange` hanterare får ett meddelande om att ett jobb har nått bearbetnings statusen markerar du posten för jobbet som bearbetning och registrerar den tid det anger bearbetnings läget.
* När din `JobStateChange` hanterare får ett meddelande om att ett jobb har nått slut för ande tillstånd (slutfört/avbrutet) markerar du posten för jobbet.
* Ha en separat process som regelbundet tittar på dina poster i jobben
    * Om du har jobb med schemalagt tillstånd som inte har avancerat till bearbetnings tillstånd under en bestämd tid för en specifik region, tar du bort den regionen från listan över konton som används för närvarande. Beroende på dina affärs behov kan du välja att avbryta dessa jobb omedelbart och skicka dem igen till den andra regionen. Eller så kan du ge dem mer tid att gå vidare till nästa steg.
    * Om en region har tagits bort från konto listan övervakar du den för återställning innan du lägger tillbaka den i listan. Det regionala hälso tillståndet kan övervakas via de befintliga jobben i regionen (om de inte har avbrutits och skickats igen), genom att lägga till kontot i listan efter en viss tids period och av operatörer som övervakar Azure Communications om avbrott som kan påverka Azure Media Services.

## <a name="next-steps"></a>Nästa steg

* Kolla av [kod exempel](/samples/browse/?products=azure-media-services)