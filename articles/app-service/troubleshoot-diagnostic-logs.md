---
title: Aktivera diagnostikloggning
description: Lär dig hur du aktiverar diagnostisk loggning och lägger till instrumentation i ditt program, samt hur du kommer åt den information som loggas av Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: ffff4215ddbe3f01da927cb47fb4e06f4946a207
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833858"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Aktivera diagnostikloggning för appar i Azure App Service
## <a name="overview"></a>Översikt
Azure tillhandahåller inbyggd diagnostik som hjälper dig att felsöka en App Service [app](overview.md). I den här artikeln får du lära dig hur du aktiverar diagnostisk loggning och lägger till instrumentation i ditt program, samt hur du kommer åt den information som loggas av Azure.

I den här artikeln används [Azure Portal](https://portal.azure.com) och Azure CLI för att arbeta med diagnostikloggar. Information om hur du arbetar med diagnostikloggar med hjälp Visual Studio finns [i Felsöka Azure i Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Förutom loggningsanvisningarna i den här artikeln finns det en ny integrerad loggningsfunktion med Azure Monitoring. Du hittar mer information om den här funktionen i avsnittet [Skicka loggar till Azure Monitor (förhandsversion).](#send-logs-to-azure-monitor-preview) 
>
>

|Typ|Plattform|Location|Description|
|-|-|-|-|
| Programloggning | Windows, Linux | App Service filsystem och/eller Azure Storage blobar | Loggar meddelanden som genereras av programkoden. Meddelandena kan genereras av det webbramverk du väljer, eller från programkoden direkt med hjälp av standardloggningsmönstret för ditt språk. Varje meddelande tilldelas någon av följande kategorier: **Kritisk,** **Fel,** **Varning,** **Information,** **Felsökning** och **Spårning.** Du kan välja hur utförlig du vill att loggning ska vara genom att ange allvarlighetsgraden när du aktiverar programloggning.|
| Webbserverloggning| Windows | App Service eller Azure Storage blobar| RÅ HTTP-begärandedata i [det utökade W3C-loggfilsformatet](/windows/desktop/Http/w3c-logging). Varje loggmeddelande innehåller data som HTTP-metoden, resurs-URI, klient-IP, klientport, användaragent, svarskod och så vidare. |
| Detaljerade felmeddelanden| Windows | App Service filsystem | Kopior av *.htm-felsidorna* som skulle ha skickats till klientwebbläsaren. Av säkerhetsskäl bör detaljerade felsidor inte skickas till klienter i produktion, men App Service kan spara felsidan varje gång ett programfel uppstår som har HTTP-kod 400 eller högre. Sidan kan innehålla information som kan hjälpa dig att avgöra varför servern returnerar felkoden. |
| Spårning av misslyckade förfrågningar | Windows | App Service filsystem | Detaljerad spårningsinformation om misslyckade begäranden, inklusive en spårning av de IIS-komponenter som används för att bearbeta begäran och den tid det tar för varje komponent. Det är användbart om du vill förbättra platsprestanda eller isolera ett specifikt HTTP-fel. En mapp genereras för varje misslyckad begäran, som innehåller XML-loggfilen och XSL-formatmallen att visa loggfilen med. |
| Distributionsloggning | Windows, Linux | App Service filsystem | Loggar för när du publicerar innehåll till en app. Distributionsloggning sker automatiskt och det finns inga konfigurerbara inställningar för distributionsloggning. Det hjälper dig att avgöra varför en distribution misslyckades. Om du till exempel använder ett anpassat [distributionsskript kan](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)du använda distributionsloggning för att avgöra varför skriptet misslyckas. |

> [!NOTE]
> App Service ett dedikerat, interaktivt diagnostikverktyg som hjälper dig att felsöka ditt program. Mer information finns i Azure App Service [diagnostiköversikt.](overview-diagnostics.md)
>
> Dessutom kan du använda andra Azure-tjänster för att förbättra loggnings- och övervakningsfunktioner i din app, till [exempel Azure Monitor](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Aktivera programloggning (Windows)

> [!NOTE]
> Programloggning för Blob Storage kan bara använda lagringskonton i samma region som App Service

Om du vill aktivera programloggning för [Windows-Azure Portal](https://portal.azure.com)går du till appen och väljer **App Service loggar**.

Välj **På** för **antingen Application Logging (filsystem)** **eller Application Logging (blob)** eller båda. 

Alternativet **Filsystem** är för tillfällig felsökning och stängs av om 12 timmar. Alternativet **Blob** är för långsiktig loggning och behöver en bloblagringscontainer för att skriva loggar till.  **Alternativet Blob** innehåller också ytterligare information i loggmeddelandena, till exempel ID:t för loggmeddelandets ursprungliga VM-instans ( ), tråd-ID ( ) och en mer `InstanceId` detaljerad `Tid` tidsstämpel ( [`EventTickCount`](/dotnet/api/system.datetime.ticks) ).

> [!NOTE]
> För närvarande kan endast .NET-programloggar skrivas till bloblagringen. Java, PHP, Node.js, Python-programloggar kan bara lagras App Service filsystemet (utan kodändringar för att skriva loggar till extern lagring).
>
> Om du [återskapar åtkomstnycklarna för lagringskontot](../storage/common/storage-account-create.md)måste du återställa respektive loggningskonfiguration så att de uppdaterade åtkomstnycklarna används. Gör så här:
>
> 1. På fliken **Konfigurera** anger du respektive loggningsfunktion till **Av**. Spara inställningen.
> 2. Aktivera loggning till lagringskontots blob igen. Spara inställningen.
>
>

Välj **nivå**, eller den detaljnivå som ska loggas. I följande tabell visas de loggkategorier som ingår i varje nivå:

| Nivå | Inkluderade kategorier |
|-|-|
|**Disabled** (Inaktiverat) | Inget |
|**Fel** | Fel, kritisk |
|**Varning** | Varning, Fel, Kritisk|
|**Information** | Info, Warning, Error, Critical|
|**Verbose** | Trace, Debug, Info, Warning, Error, Critical (alla kategorier) |

När du är klar väljer du **Spara**.

## <a name="enable-application-logging-linuxcontainer"></a>Aktivera programloggning (Linux/container)

Om du vill aktivera programloggning för Linux-appar eller anpassade containerappar [i Azure Portal](https://portal.azure.com)navigerar du till din app och väljer **App Service loggar**.

I **Programloggning** väljer du **Filsystem**.

I **Kvot (MB)** anger du diskkvoten för programloggarna. I **Kvarhållningsperiod (dagar)** anger du hur många dagar loggarna ska behållas.

När du är klar väljer du **Spara**.

## <a name="enable-web-server-logging"></a>Aktivera webbserverloggning

Om du vill aktivera webbserverloggning för [Windows-appar i Azure Portal](https://portal.azure.com)går du till appen och väljer App Service **loggar**.

För **Webbserverloggning väljer** du **Lagring för** att  lagra loggar på bloblagring eller Filsystem för att lagra loggar App Service filsystemet. 

I **Kvarhållningsperiod (dagar)** anger du hur många dagar loggarna ska behållas.

> [!NOTE]
> Om du [återskapar lagringskontots åtkomstnycklar måste](../storage/common/storage-account-create.md)du återställa respektive loggningskonfiguration för att använda de uppdaterade nycklarna. Gör så här:
>
> 1. På fliken **Konfigurera** anger du respektive loggningsfunktion till **Av**. Spara inställningen.
> 2. Aktivera loggning till lagringskontots blob igen. Spara inställningen.
>
>

När du är klar väljer du **Spara**.

## <a name="log-detailed-errors"></a>Logga detaljerade fel

Om du vill spara felsidan eller spårningen av misslyckade förfrågningar [för Windows-appar i Azure Portal](https://portal.azure.com)du till din app och väljer App Service **loggar**.

Under **Detaljerad felloggning eller** Spårning av misslyckade **förfrågningar** väljer **du På** och sedan **Spara.**

Båda typerna av loggar lagras i det App Service filsystemet. Upp till 50 fel (filer/mappar) bevaras. När antalet HTML-filer överstiger 50 tas de äldsta 26 felen bort automatiskt.

## <a name="add-log-messages-in-code"></a>Lägga till loggmeddelanden i kod

I programkoden använder du de vanliga loggningsaktiviteterna för att skicka loggmeddelanden till programloggarna. Exempel:

- ASP.NET program kan använda klassen [System.Diagnostics.Trace för](/dotnet/api/system.diagnostics.trace) att logga information i programdiagnostikloggen. Exempel:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Som standard använder ASP.NET Core [loggningsprovidern Microsoft.Extensions.Logging.AzureAppServices.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Mer information finns i ASP.NET [Core-loggning i Azure](/aspnet/core/fundamentals/logging/). Information om WebJobs SDK-loggning finns [i Kom igång med Azure WebJobs SDK](./webjobs-sdk-get-started.md#enable-console-logging)

## <a name="stream-logs"></a>Strömningsloggar

Innan du strömmar loggar i realtid aktiverar du den loggtyp som du vill använda. All information som skrivs till filer som slutar på .txt, .log eller .htm som lagras i *katalogen /LogFiles* (d:/home/logfiles) strömmas av App Service.

> [!NOTE]
> Vissa typer av loggningsbuffertskrivningar till loggfilen, vilket kan resultera i händelser i fel ordning i dataströmmen. Till exempel kan en programloggpost som inträffar när en användare besöker en sida visas i dataströmmen före motsvarande HTTP-loggpost för sidbegäran.
>

### <a name="in-azure-portal"></a>I Azure Portal

Om du vill strömma loggar [Azure Portal](https://portal.azure.com)navigerar du till din app och väljer **Logga ström.** 

### <a name="in-cloud-shell"></a>I Cloud Shell

Om du vill strömma loggar [live Cloud Shell](../cloud-shell/overview.md)använder du följande kommando:

> [!IMPORTANT]
> Det här kommandot kanske inte fungerar med webbappar som finns i en Linux App Service-plan.

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Om du vill filtrera specifika loggtyper, till exempel HTTP, använder du parametern **--Provider.** Exempel:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --provider http
```

### <a name="in-local-terminal"></a>I den lokala terminalen

Om du vill strömma loggar i den lokala konsolen [installerar du Azure CLI](/cli/azure/install-azure-cli) och loggar in på ditt [konto.](/cli/azure/authenticate-azure-cli) När du har loggat in följer [du instruktionerna för Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Komma åt loggfiler

Om du konfigurerar Azure Storage för en loggtyp behöver du ett klientverktyg som fungerar med Azure Storage. Mer information finns i [Azure Storage Client Tools](../storage/common/storage-explorers.md).

För loggar som lagras App Service filsystemet är det enklaste sättet att ladda ned ZIP-filen i webbläsaren på:

- Linux-/containerappar: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows-appar: `https://<app-name>.scm.azurewebsites.net/api/dump`

För Linux-/containerappar innehåller ZIP-filen konsolutdataloggar för både Docker-värden och Docker-containern. För en utskalade app innehåller ZIP-filen en uppsättning loggar för varje instans. I App Service-filsystemet är dessa loggfiler innehållet i katalogen */home/LogFiles.*

För Windows-appar innehåller ZIP-filen innehållet i *katalogen D:\Home\LogFiles* i App Service filsystemet. Den har följande struktur:

| Loggtyp | Katalog | Description |
|-|-|-|
| **Programloggar** |*/LogFiles/Application/* | Innehåller en eller flera textfiler. Formatet på loggmeddelandena beror på vilken loggningsprovider du använder. |
| **Spårningar av misslyckade förfrågningar** | */LogFiles/W3SVC##########/* | Innehåller XML-filer och en XSL-fil. Du kan visa de formaterade XML-filerna i webbläsaren. |
| **Detaljerade felloggar** | */LogFiles/DetailedErrors/* | Innehåller HTM-felfiler. Du kan visa HTM-filerna i webbläsaren.<br/>Ett annat sätt att visa spårningar av misslyckade förfrågningar är att gå till appsidan i portalen. I den vänstra menyn väljer du **Diagnostisera** och lösa problem. Sök sedan efter Spårningsloggar för misslyckade förfrågningar **och** klicka sedan på ikonen för att bläddra och visa den spårning du vill ha. |
| **Webbserverloggar** | */LogFiles/http/RawLogs/* | Innehåller textfiler som formaterats med [det utökade loggfilsformatet W3C.](/windows/desktop/Http/w3c-logging) Den här informationen kan läsas med hjälp av en textredigerare eller ett verktyg som [Log Parser](https://www.iis.net/downloads/community/2010/04/log-parser-22).<br/>App Service stöder inte fälten `s-computername` `s-ip` , eller `cs-version` . |
| **Distributionsloggar** | */LogFiles/Git/* och */deployments/* | Innehåller loggar som genereras av de interna distributionsprocesserna, samt loggar för Git-distributioner. |

## <a name="send-logs-to-azure-monitor-preview"></a>Skicka loggar till Azure Monitor (förhandsversion)

Med den nya Azure Monitor kan du skapa diagnostikinställningar [(förhandsversion)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) för att skicka loggar till [lagringskonton,](https://aka.ms/appsvcblog-azmon)Event Hubs och Log Analytics. 

> [!div class="mx-imgBorder"]
> ![Diagnostikinställningar (förhandsversion)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Loggtyper som stöds

I följande tabell visas de loggtyper och beskrivningar som stöds: 

| Loggtyp | Windows | Windows-container | Linux | Linux-container | Description |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & Tomcat | Ja | Ja | Ja | Standardutdata och standardfel |
| AppServiceHTTPLogs | Ja | Ja | Ja | Ja | Webbserverloggar |
| AppServiceEnvironmentPlatformLogs | Yes | Saknas | Ja | Ja | App Service-miljön: skalning, konfigurationsändringar och statusloggar|
| AppServiceAuditLogs | Ja | Ja | Ja | Ja | Inloggningsaktivitet via FTP och Kudu |
| AppServiceFileAuditLogs | Ja | Ja | Tba | Tba | Filändringar som görs i webbplatsinnehållet; **endast tillgängligt för Premium-nivån och högre** |
| AppServiceAppLogs | ASP .NET & Java Tomcat <sup>1</sup> | ASP .NET & Java Tomcat <sup>1</sup> | Java SE & Tomcat 2 <sup></sup> | Java SE & Tomcat 2 <sup></sup> | Programloggar |
| AppServiceIPSecAuditLogs  | Ja | Ja | Ja | Ja | Begäranden från IP-regler |
| AppServicePlatformLogs  | Tba | Ja | Ja | Ja | Containeråtgärdsloggar |
| AppServiceAntivirusScanAuditLogs | Ja | Ja | Ja | Ja | [Virusgenomsökningsloggar](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html) med Microsoft Defender; **endast tillgängligt för Premium-nivå** | 

<sup>1</sup> För Java Tomcat-appar lägger du till "TOMCAT_USE_STARTUP_BAT" i appinställningarna och ställer in den på falskt eller 0. Måste ha den  senaste Tomcat-versionen och använda *java.util.logging*.

<sup>2</sup> För Java SE-appar lägger du till "$WEBSITE_AZMON_PREVIEW_ENABLED" i appinställningarna och anger det till true eller till 1.

## <a name="next-steps"></a><a name="nextsteps"></a> Nästa steg
* [Köra frågor mot loggar med Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Övervaka Azure App Service](web-sites-monitor.md)
* [Felsöka Azure App Service i Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analysera apploggar i HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
