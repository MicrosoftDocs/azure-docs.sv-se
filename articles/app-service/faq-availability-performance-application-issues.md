---
title: Vanliga frågor och svar om program prestanda
description: Få svar på vanliga frågor om tillgänglighets-, prestanda-och program problem i Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: dfaeee6a6e2a9728d7e63fb5681c487fbbd6139e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88959008"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Vanliga frågor och svar om program prestanda för Web Apps i Azure

> [!NOTE]
> Några av rikt linjerna nedan kanske bara fungerar på Windows-eller Linux-App Services. Linux App Services till exempel köras i 64-bitars läge som standard.
>

Den här artikeln innehåller svar på vanliga frågor och svar om program prestanda problem för [Web Apps funktionen i Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Varför är min app långsam?

Flera faktorer kan bidra till dåliga program prestanda. Detaljerade fel söknings steg finns i [Felsöka prestanda för långsamma webb program](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Hur gör jag för att felsöka ett scenario med hög CPU-förbrukning?

I vissa scenarier med hög CPU-förbrukning kan din app faktiskt kräva mer data bearbetnings resurser. I så fall bör du överväga att skala till en högre tjänst nivå så att programmet får alla resurser som krävs. Andra gånger kan hög processor förbrukning orsakas av en dålig loop eller en kodnings metod. Att få insikt i vad som utlöser ökad processor förbrukning är en process i två delar. Börja med att skapa en process dumpning och analysera sedan process dumpningen. Mer information finns i [avbilda och analysera en dumpfil för hög CPU-förbrukning för Web Apps](/archive/blogs/asiatech/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Hur gör jag för att felsöka ett scenario med hög minnes förbrukning?

I vissa scenarier med hög minnes användning kan din app faktiskt kräva mer data bearbetnings resurser. I så fall bör du överväga att skala till en högre tjänst nivå så att programmet får alla resurser som krävs. Andra gånger kan ett fel i koden orsaka en minnes läcka. En kodnings metod kan också öka minnes användningen. Att få insikt i vad som utlöser hög minnes användning är en process i två delar. Börja med att skapa en process dumpning och analysera sedan process dumpningen. Crash Diagnostic från Azure Site Extension-galleriet kan effektivt utföra båda dessa steg. Mer information finns i [avbilda och analysera en dumpfil för återkommande hög minnes storlek för Web Apps](/archive/blogs/asiatech/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Hur gör jag för att automatisera App Service webbappar med PowerShell?

Du kan använda PowerShell-cmdletar för att hantera och underhålla App Service webbappar. I vårt blogg inlägg [automatiserar webbappar som finns i Azure App Service med hjälp av PowerShell](/archive/blogs/puneetgupta/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way), beskriver vi hur du använder Azure Resource Manager-baserade PowerShell-cmdletar för att automatisera vanliga uppgifter. Blogg inlägget innehåller också en exempel kod för olika hanterings uppgifter för webbappar. Beskrivningar och syntax för alla App Service Web Apps-cmdlets finns i [AZ. websites](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Hur gör jag för att visa mina webbappens händelse loggar?

Så här visar du webbappens händelse loggar:

1. Logga in på din **kudu-webbplats** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. I menyn väljer du **Felsök konsol**  >  **cmd**.
3. Välj mappen **loggfiler** .
4. Om du vill visa händelse loggar väljer du Penn ikonen bredvid **eventlog.xml**.
5. Hämta loggarna genom att köra PowerShell-cmdleten `Save-AzureWebSiteLog -Name webappname` .

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Hur gör jag för att avbilda en minnes dumpning i användarläge för min webbapp?

För att avbilda en minnes dum par i användarläge för din webbapp:

1. Logga in på din **kudu-webbplats** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Välj menyn **process Utforskaren** .
3. Högerklicka på processen för **w3wp.exe** eller ditt webb jobb.
4. Välj **Ladda ned minnes dumpning**  >  **fullständig dump**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Hur gör jag för att visa information på process nivå för min webbapp?

Det finns två alternativ för att visa information på process nivå för din webbapp:

*   I Azure-portalen:
    1. Öppna **process Utforskaren** för webb programmet.
    2. Välj **w3wp.exe** processen om du vill se informationen.
*   I kudu-konsolen:
    1. Logga in på din **kudu-webbplats** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
    2. Välj menyn **process Utforskaren** .
    3. Välj **Egenskaper** för **w3wp.exes** processen.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>När jag bläddrar till min app visas "fel 403 – den här webbappen har stoppats". Hur gör jag för att lösa det?

Det här felet kan orsakas av tre villkor:

* Webbappen har nått en fakturerings gräns och din webbplats har inaktiverats.
* Webbappen har stoppats i portalen.
* Webb programmet har nått en resurs kvot gräns som kan gälla för en kostnads fri eller delad plan tjänst plan.

Om du vill se vad som orsakar felet och lösa problemet följer du stegen i [Web Apps: "fel 403 – den här webbappen har stoppats"](/archive/blogs/waws/azure-web-apps-error-403-this-web-app-is-stopped).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Var kan jag läsa mer om kvoter och begränsningar för olika App Services planer?

Information om kvoter och begränsningar finns i [App Service gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Hur gör jag för att minska svars tiden för den första begäran efter inaktivitet?

Som standard inaktive ras Web Apps om de är inaktiva under en angiven tids period. På så sätt kan systemet Spara resurser. Nack delen är att svaret på den första begäran efter att webbappen har inaktiverats är längre, så att webbappen kan läsa in och börja betjäna svar. I tjänst prenumerationerna Basic och standard kan du aktivera inställningen **Always on** för att hålla appen alltid inläst. Detta eliminerar längre inläsnings tider när appen är inaktiv. Så här ändrar du inställningen **Always on** :

1. I Azure Portal går du till din webbapp.
2. Välj **konfiguration**
3. Välj **allmänna inställningar**.
4. För **Always on** väljer du **på**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Hur gör jag för att du aktivera spårning av misslyckade förfrågningar?

Aktivera spårning av misslyckade begär Anden:

1. I Azure Portal går du till din webbapp.
3. Välj **alla inställningar**  >  **diagnostikloggar**.
4. För **spårning av misslyckade begär Anden** väljer du **på**.
5. Välj **Spara**.
6. På bladet webbapp väljer du **verktyg**.
7. Välj **Visual Studio Online**.
8. Om inställningen inte är **på** väljer du **på**.
9. Välj **gå** till.
10. Välj **Web.config**.
11. I system. webserver lägger du till den här konfigurationen (för att avbilda en viss URL):

    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. Om du vill felsöka problem med långsamma prestanda lägger du till den här konfigurationen (om begäran tar mer än 30 sekunder):
    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. Om du vill hämta spårningen av misslyckade begär Anden går du till din webbplats i [portalen](https://portal.azure.com).
15. Välj **verktyg**  >  **kudu**  >  **Go**.
18. I menyn väljer du **Felsök konsol**  >  **cmd**.
19. Välj mappen **loggfiler** och välj sedan den mapp med ett namn som börjar med **W3SVC**.
20. Om du vill se XML-filen väljer du Penn ikonen.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Jag ser meddelandet "arbets process begärde åter användning på grund av" procent minnes gräns ". Hur gör jag för att du lösa det här problemet?

Den största tillgängliga mängden minne för en 32-bitars process (även på ett 64-bitars operativ system) är 2 GB. Arbets processen är som standard inställd på 32-bitars i App Service (för kompatibilitet med äldre webb program).

Överväg att växla till 64-bitars processer så att du kan dra nytta av det ytterligare minne som är tillgängligt i din Web Worker-roll. Detta utlöser en omstart av en webbapp så att du kan schemalägga detta.

Observera också att en 64-bitars miljö kräver en Basic-eller standard service-plan. Kostnads fria och delade planer körs alltid i en 32-bitars miljö.

Mer information finns i [Konfigurera Web Apps i App Service](configure-common.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Varför nådde min förfrågan efter 230 sekunder?

Azure Load Balancer har en standardinställning för tids gräns för inaktivitet på fyra minuter. Detta är vanligt vis en rimlig svars tids gräns för en webbegäran. Om din webbapp kräver bakgrunds bearbetning rekommenderar vi att du använder Azure WebJobs. Azure-webbappen kan anropa webbjobb och meddelas när bakgrunds bearbetningen har slutförts. Du kan välja mellan flera metoder för att använda WebJobs, inklusive köer och utlösare.

WebJobs är utformat för bakgrunds bearbetning. Du kan göra så mycket bakgrunds bearbetning som du vill i ett webb jobb. Mer information om WebJobs finns i [köra bakgrunds aktiviteter med WebJobs](webjobs-create.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET Core program som finns i App Service slutar ibland att svara. Hur gör jag för att åtgärda det här problemet?

Ett känt problem med en tidigare [Kestrel-version](https://github.com/aspnet/KestrelHttpServer/issues/1182) kan orsaka att en ASP.net core 1,0-app som finns i App Service tillfälligt slutar svara. Du kan också se det här meddelandet: "det angivna CGI-programmet påträffade ett fel och servern avslutade processen."

Det här problemet har åtgärd ATS i Kestrel version 1.0.2. Den här versionen ingår i ASP.NET Core 1.0.3 Update. För att lösa det här problemet, se till att du uppdaterar dina program beroenden till att använda Kestrel 1.0.2. Du kan också använda en av de två lösningarna som beskrivs i blogg inlägget [ASP.NET Core 1,0 problem med att minska prestandan i App Service Web Apps](/archive/blogs/waws/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Jag kan inte hitta loggfilerna i fil strukturen för min webbapp. Hur kan jag hitta dem?

Om du använder den lokala cache-funktionen i App Service påverkas mappstrukturen för loggfiler och datamappar för din App Service instans. När Local cache används skapas undermappar i lagrings-loggfilerna och datamapparna. Undermapparna använder namngivnings mönstret "unik identifierare" + tidsstämpel. Varje undermapp motsvarar en VM-instans där webbappen körs eller har körts.

För att avgöra om du använder lokal cache, se fliken App Service **program inställningar** . Om lokal cache används anges appens inställning `WEBSITE_LOCAL_CACHE_OPTION` till `Always` .

Om du inte använder lokal cache och har drabbats av det här problemet skickar du en support förfrågan.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Jag ser meddelandet "ett försök gjordes att få åtkomst till en socket på ett sätt som förbjuds av dess åtkomst behörigheter." Hur gör jag för att lösa det?

Det här felet uppstår vanligt vis om utgående TCP-anslutningar på VM-instansen är förbrukade. I App Service tillämpas gränser för det högsta antalet utgående anslutningar som kan göras för varje VM-instans. Mer information finns i [numeriska gränser mellan virtuella datorer](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Det här felet kan också uppstå om du försöker komma åt en lokal adress från ditt program. Mer information finns i [lokala adress begär Anden](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Mer information om utgående anslutningar i din webbapp finns i blogg inlägget om [utgående anslutningar till Azure Websites](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Hur gör jag för att använder du Visual Studio till fjärrfelsökning min App Service-webbapp?

En detaljerad genom gång som visar hur du felsöker din webbapp med hjälp av Visual Studio finns i [fjärrfelsökning App Service-webbapp](/archive/blogs/benjaminperkins/remote-debug-your-azure-app-service-web-app).