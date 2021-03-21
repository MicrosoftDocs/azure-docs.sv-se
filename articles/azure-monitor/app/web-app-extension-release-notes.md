---
title: Viktig information för Azure Web App-tillägg – Application Insights
description: Viktig information för Azure Web Apps-tillägget för runtime Instrumentation med Application Insights.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 07ba61f630b849a377f1c7ba881f95518eb73606
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042614"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>Viktig information för Azure Web App-tillägg för Application Insights

Den här artikeln innehåller information om uppdateringar för Azure Web Apps-tillägget för runtime Instrumentation med Application Insights. Detta gäller endast för förinstallerade tillägg.

[Läs](azure-web-apps.md) mer om Azure Web App-tillägget för Application Insights.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

- Hur ser du vilken version av tillägget jag är på?
    - Gå till `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights`. Besök [den stegvisa fel söknings guiden för tillägg/agent-baserad övervakning](./azure-web-apps.md?tabs=net#troubleshooting) för mer information.

- Vad händer om jag använder privata tillägg?
    - Avinstallera privata webbplats tillägg eftersom det inte längre stöds.

## <a name="release-notes"></a>Viktig information

### <a name="2838"></a>2.8.38

- JAVA-tillägg: uppgraderat till [Java agent 3.0.2 (ga)](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.0.2) från 2.5.1.
- Node.js-tillägg: uppdaterade AI SDK till [1.8.8](https://github.com/microsoft/ApplicationInsights-node.js/releases/tag/1.8.8) från 1.8.7.
- .NET Core: borttagna versioner som inte stöds (2,0, 2,2, 3,0). Versioner som stöds är 2,1 och 3,1.

### <a name="2837"></a>2.8.37

- AppSvc Windows-tillägg: genomförd .net Core-åtgärd med valfri version av System.Diagnostics.DiagnosticSource.dll.

### <a name="2836"></a>2.8.36

- AppSvc Windows-tillägg: aktive rad mellan-op med AI SDK i .NET Core.

### <a name="2835"></a>2.8.35

- AppSvc Windows-tillägg: stöd för .NET Core 3,1 har lagts till.

### <a name="2833"></a>2.8.33

- .NET, .NET Core, Java och Node.js agenter och Windows-tillägget: stöd för suveräna moln. Anslutnings strängar kan användas för att skicka data till suveräna moln.

### <a name="2831"></a>2.8.31

- ASP.NET Core agent: åtgärdat problem som rör en av de uppdaterade Application Insights SDK: s referenser (se kända problem för 2.8.26). Om den felaktiga versionen av `System.Diagnostics.DiagnosticSource.dll` redan har lästs in av Runtime kraschar inte kod tillägget för programmet och säkerhets kopieringar sker inte. För kunder som drabbas av det här problemet rekommenderar vi att du tar bort `System.Diagnostics.DiagnosticSource.dll` från bin-mappen eller använder den äldre versionen av tillägget genom att ange "ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24". annars aktive ras inte program övervakning.

### <a name="2826"></a>2.8.26

- ASP.NET Core agent: åtgärdat problem som rör uppdaterad Application Insights SDK. Agenten försöker inte läsa in `AiHostingStartup` om ApplicationInsights.dll redan finns i bin-mappen. Detta löser problem som rör reflektion via sammansättning \<AiHostingStartup\> . GetTypes().
- Kända problem: undantag `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` kan uppstå om en annan version av `DiagnosticSource` DLL läses in. Detta kan inträffa, till exempel om det finns `System.Diagnostics.DiagnosticSource.dll` i mappen publicera. Som en minskning använder du den tidigare versionen av tillägget genom att ställa in appinställningar i app Services: ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24.

### <a name="2824"></a>2.8.24

- Paketerat om version av 2.8.21.

### <a name="2823"></a>2.8.23

- ASP.NET Core 3,0-stöd för övervakning av kod har lagts till.
- Uppdaterade ASP.NET Core SDK till [2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) för körnings versionerna 2,1, 2,2 och 3,0. Appar som riktar sig mot .NET Core 2,0 fortsätter att använda 2.1.1 av SDK.

### <a name="2814"></a>2.8.14

- Uppdaterad ASP.NET Core SDK-version från 2.3.0 till den senaste (2.6.1) för appar som riktar sig mot .NET Core 2,1, 2,2. Appar som riktar sig mot .NET Core 2,0 fortsätter att använda 2.1.1 av SDK.

### <a name="2812"></a>2.8.12

- Stöd för ASP.NET Core 2,2-appar.
- Ett fel i ASP.NET Core-tillägget har åtgärdat ett fel i tillägg till SDK även om programmet redan har instrumenterats med SDK: n. För 2,1-och 2,2-appar kommer förekomsten av ApplicationInsights.dll i programmappen att leda till att tillägget stängs av. För 2,0-appar återställs tillägget endast om ApplicationInsights har Aktiver ATS med ett `UseApplicationInsights()` anrop.

- Permanent korrigering för ofullständigt HTML-svar för ASP.NET Core appar. Den här korrigeringen har nu utökats för att fungera för .NET Core 2,2-appar.

- Stöd har lagts till för att inaktivera JavaScript-inmatning för ASP.NET Core-appar ( `APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting` ). För ASP.NET Core är JavaScript-injekteringen i läget "opt-out" som standard, om det inte uttryckligen stängs av. (Standardinställningen är att behålla det aktuella beteendet.)

- Åtgärdat fel i ASP.NET Cores tillägg som orsakade insprutning även om iKey inte fanns.
- Ett fel har åtgärd ATS i prefix logiken för SDK-version som orsakade en felaktig SDK-version i telemetri.

- Har lagt till prefix för SDK-version för ASP.NET Core appar för att identifiera hur telemetri samlades in.
- Den fasta SCM-ApplicationInsights-sidan visar versionen för det förinstallerade tillägget korrekt.

### <a name="2810"></a>2.8.10

- Korrigering för ofullständigt HTML-svar för ASP.NET Core appar.

## <a name="next-steps"></a>Nästa steg

- Besök [Azure App Service-dokumentationen](azure-web-apps.md) för mer information om hur du konfigurerar övervakning för Azure App-tjänster. 
