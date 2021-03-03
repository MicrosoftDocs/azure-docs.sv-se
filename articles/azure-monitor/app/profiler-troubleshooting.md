---
title: Felsöka problem med Azure Application Insights profiler
description: Den här artikeln innehåller fel söknings steg och information som hjälper utvecklare att aktivera och använda Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 2ab719b47245f3adc2fba610f9c0473868889a7e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711458"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Felsöka problem med att aktivera eller Visa Application Insights Profiler

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Allmän fel sökning

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profiler laddas bara upp om det finns begär anden till programmet medan profiler körs

Azure Application Insights profiler samlar in data i två minuter per timme. Du kan också samla in data när du väljer knappen **profil nu** i fönstret **Konfigurera Application Insights profiler** .

> [!NOTE]
> Profilerings data laddas bara upp när den kan kopplas till en begäran som har inträffat medan profiler kördes. 

Profiler skriver spårnings meddelanden och anpassade händelser till din Application Insights-resurs. Du kan använda dessa händelser för att se hur profiler körs:

1. Sök efter spårnings meddelanden och anpassade händelser som skickats av profileraren till din Application Insights-resurs. Du kan använda den här Sök strängen för att hitta relevanta data:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Följande bild visar två exempel på sökningar från två AI-resurser: 
    
   * Till vänster tar programmet inte emot förfrågningar när profiler körs. Meddelandet förklarar att uppladdningen avbröts på grund av ingen aktivitet. 

   * Till höger startade profileraren och skickade anpassade händelser när den identifierade begär Anden som uppstod när profiler kördes. Om den `ServiceProfilerSample` anpassade händelsen visas innebär det att en profil har fångats och att den är tillgänglig i fönstret **Application Insights prestanda** .

     Om inga poster visas körs inte profiler. Fel sökning finns i avsnittet fel söknings avsnitt för din speciella app-typ senare i den här artikeln.  

     ![Sök profiler-telemetri][profiler-search-telemetry]

### <a name="other-things-to-check"></a>Andra saker att kontrol lera
* Kontrol lera att din app körs på .NET Framework 4,6.
* Om din webbapp är ett ASP.NET Core program måste den köra minst ASP.NET Core 2,0.
* Om de data du försöker visa är äldre än några veckor kan du försöka begränsa ditt tids filter och försöka igen. Spårningar tas bort efter sju dagar.
* Kontrol lera att proxyservrar eller en brand vägg inte har blockerat åtkomst till https://gateway.azureserviceprofiler.net .
* Profiler stöds inte på kostnads fria eller delade App Service-planer. Om du använder någon av dessa planer kan du prova att skala upp till en av de grundläggande planerna och profileraren ska börja fungera.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Dubbel inventering i parallella trådar

I vissa fall är total tids måttet i stack läsaren mer än varaktigheten för begäran.

Den här situationen kan uppstå när två eller fler parallella trådar är associerade med en begäran. I så fall är den totala tråd tiden mer än den tid som förflutit.

En tråd kan vänta på att den andra ska slutföras. Visnings programmet försöker identifiera den här situationen och utelämnar den spännande vänte tiden. När du gör det synkroniseringsfel det på sidan om att visa för mycket information i stället för att utesluta vad som kan vara viktig information.

När du ser parallella trådar i spåren ska du bestämma vilka trådar som väntar, så att du kan identifiera den aktiva sökvägen för begäran.

Den tråd som snabbt övergår i vänte läge väntar vanligt vis på de andra trådarna. Koncentrera dig på de andra trådarna och ignorera tiden i väntande trådar.

### <a name="error-report-in-the-profile-viewer"></a>Fel rapport i profil visaren
Skicka in ett support ärende i portalen. Se till att ta med korrelations-ID: t från fel meddelandet.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Felsöka profileraren på Azure App Service

För att profiler ska fungera korrekt:
* Web App Service-planen måste vara Basic-nivå eller högre.
* Din webbapp måste ha Application Insights aktive rad.
* Din webbapp måste ha följande app-inställningar:

    |Programinställning    | Värde    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey för din Application Insights-resurs    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |


* Webbjobbet **ApplicationInsightsProfiler3** måste köras. Så här kontrollerar du webb jobbet:
   1. Gå till [kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret).
   1. På menyn **verktyg** väljer du **instrument panel för WebJobs**.  
      Fönstret **WebJobs** öppnas. 
   
      ![Skärm bild som visar fönstret WebJobs som visar namn, status och senaste körnings tid för jobb.][profiler-webjob]   
   
   1. Om du vill visa information om webb jobbet, inklusive loggen, väljer du länken **ApplicationInsightsProfiler3** .  
     Informations fönstret för **kontinuerliga webbjobb** öppnas.

      ![Skärm bild som visar informations fönstret för kontinuerliga webbjobb.][profiler-webjob-log]

Om profiler inte fungerar för dig kan du hämta loggen och skicka den till vårt team för att få hjälp serviceprofilerhelp@microsoft.com .

### <a name="check-the-diagnostic-services-site-extension-status-page"></a>Kontrol lera status sidan för diagnostiska tjänst webbplats tillägg
Om profiler har Aktiver ATS via [Application Insightss fönstret](profiler.md) i portalen aktiverades det av tillägget för diagnostik Services-webbplatsen.

> [!NOTE]
> Kod installation av Application Insights Profiler som följer support policyn för .NET Core.
> Mer information om körningar som stöds finns i [.net Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Du kan kontrol lera status sidan för det här tillägget genom att gå till följande URL: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> Domänen för status sidans länk kan variera beroende på molnet.
Den här domänen är samma som hanterings platsen för kudu för App Service.

Den här status sidan visar installations tillståndet för profileraren och Snapshot Collector agenter. Om det uppstod ett oväntat fel visas det och du kan se hur det kan åtgärdas.

Du kan använda hanterings platsen för kudu för App Service för att hämta bas-URL: en för den här status sidan:
1. Öppna ditt App Service-program i Azure Portal.
2. Välj **Avancerade verktyg** eller Sök efter **kudu**.
3. Välj **gå** till.
4. När du är på kudu hanterings plats lägger du till följande i URL: en **`/DiagnosticServices` och trycker på RETUR**.
 Det kommer att sluta så här: `https://<kudu-url>/DiagnosticServices`

En status sida visas ungefär som på sidan nedan: ![ status sida för diagnostiska tjänster](./media/diagnostic-services-site-extension/status-page.png)
    
### <a name="manual-installation"></a>Manuell installation

När du konfigurerar profiler görs uppdateringar av webbappens inställningar. Om din miljö kräver det kan du tillämpa uppdateringarna manuellt. Ett exempel kan vara att ditt program körs i en Web Appss miljö för PowerApps. Så här installerar du uppdateringar manuellt:

1. I **kontroll fönstret webbapp** öppnar du **Inställningar**.

1. Ange **.NET Framework-version** till **v 4.6**.

1. Ställ in **Always on** **på**.
1. Skapa följande app-inställningar:

    |Programinställning    | Värde    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey för din Application Insights-resurs    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |

### <a name="too-many-active-profiling-sessions"></a>För många aktiva profilerings-sessioner

Du kan aktivera profiler på högst fyra Web Apps som körs i samma tjänste plan. Om du har fler än fyra kan profileraren utlösa en *Microsoft. ServiceProfiler. Exceptions. TooManyETWSessionException*. Lös det genom att flytta några webbappar till en annan tjänst plan.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Distributions fel: katalogen är inte tom: \\ Start \\ platsens \\ wwwroot \\ App_Data \\ jobb

Om du omdistribuerar din webbapp till en Web Apps-resurs där profileraren är aktive rad kan du se följande meddelande:

*Katalogen är inte tom: \\ Start \\ platsens \\ wwwroot \\ App_Data \\ jobb*

Det här felet uppstår om du kör webb distribution från skript eller Azure-pipeliner. Lösningen är att lägga till följande distributions parametrar till webb distributions uppgiften:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Dessa parametrar tar bort mappen som används av Application Insights Profiler och avblockerar omdistributions processen. De påverkar inte den profilerade instans som körs för tillfället.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hur gör jag för att avgöra om Application Insights Profiler körs?

Profiler körs som ett kontinuerligt webb jobb i webbappen. Du kan öppna Web App-resursen i [Azure Portal](https://portal.azure.com). I fönstret **WebJobs** kontrollerar du status för **ApplicationInsightsProfiler**. Om den inte körs öppnar du **loggar** för att få mer information.

## <a name="troubleshoot-vms-and-cloud-services"></a>Felsöka virtuella datorer och Cloud Services

>**Felet i profileraren som levereras i WAD för Cloud Services har åtgärd ATS.** Den senaste versionen av WAD (1.12.2.0) för Cloud Services fungerar med alla nya versioner av App Insights SDK. Moln tjänst värdar kommer att uppgradera WAD automatiskt, men det är inte omedelbart. Om du vill framtvinga en uppgradering kan du distribuera om tjänsten eller starta om noden.

Följ stegen nedan om du vill se om profiler har kon figurer ATS korrekt i Azure-diagnostik: 
1. Kontrol lera att innehållet i den distribuerade Azure-diagnostik-konfigurationen är det du förväntar dig. 

1. För det andra kontrollerar du att Azure-diagnostik skickar rätt iKey på profilerings kommando raden. 

1. För det tredje kontrollerar du logg filen för profilering för att se om profiler kördes men returnerade ett fel. 

Kontrol lera inställningarna som användes för att konfigurera Azure-diagnostik:

1. Logga in på den virtuella datorn (VM) och öppna sedan logg filen på den här platsen. Plugin-versionen kan vara nyare på din dator.
    
    För virtuella datorer:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    För Cloud Services:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. I filen kan du söka efter strängen **WadCfg** för att hitta de inställningar som skickades till den virtuella datorn för att konfigurera Azure-diagnostik. Du kan kontrol lera om iKey som används av profilens Sink är korrekt.

1. Kontrol lera kommando raden som används för att starta profiler. Argumenten som används för att starta profiler finns i följande fil. (Enheten kan vara c: eller d: och katalogen kan vara dold.)

    För virtuella datorer:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    för Cloud Services:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Kontrol lera att iKey på profilens kommando rad är korrekt. 

1. Använd sökvägen som påträffades i föregående *config.jspå* filen, kontrol lera logg filen för profilering, som heter **bootstrapd. log**. Den felsöknings information som anger vilka inställningar som profiler använder visas. Den visar också status och fel meddelanden från profiler.  

    För virtuella datorer är filen här:
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    För Cloud Services:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Om profiler körs medan programmet tar emot begär Anden visas följande meddelande: *aktivitet som identifieras från iKey*. 

    När spårningen laddas upp visas följande meddelande: *börja ladda upp spårning*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Redigera nätverks proxy-eller brand Väggs regler

Om ditt program ansluter till Internet via en proxy eller en brand vägg, kan du behöva uppdatera reglerna för att kommunicera med profilerings tjänsten.

De IP-adresser som används av Application Insights Profiler ingår i Azure Monitor Service tag. Mer information finns i [dokumentationen om service tag](../../virtual-network/service-tags-overview.md).


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png