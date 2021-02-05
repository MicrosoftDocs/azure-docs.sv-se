---
title: Aktivera diagnostikloggning
description: Lär dig hur du aktiverar diagnostikloggning och lägger till instrumentering i programmet, samt hur du får åtkomst till den information som loggas av Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 3b7f8f5a0f48bf0cdca7f72e5fb364ea0a5d1315
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576607"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Aktivera diagnostikloggning för appar i Azure App Service
## <a name="overview"></a>Översikt
Azure har inbyggd diagnostik som hjälper till med fel sökning av en [App Service app](overview.md). I den här artikeln får du lära dig hur du aktiverar diagnostikloggning och lägger till instrumentering i programmet, samt hur du får åtkomst till den information som loggas av Azure.

I den här artikeln används [Azure Portal](https://portal.azure.com) och Azure CLI för att arbeta med diagnostikloggar. Information om hur du arbetar med diagnostiska loggar med Visual Studio finns i [Felsöka Azure i Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Förutom loggnings anvisningarna i den här artikeln finns det en ny integrerad loggnings funktion med Azure-övervakning. Du hittar mer information om den här funktionen i avsnittet [skicka loggar till Azure Monitor (för hands version)](#send-logs-to-azure-monitor-preview) . 
>
>

|Typ|Plattform|Location|Description|
|-|-|-|-|
| Programloggning | Windows, Linux | App Service fil system och/eller Azure Storage blobbar | Loggar meddelanden som genereras av din program kod. Meddelandena kan genereras av det webb ramverk du väljer eller från din program kod direkt med hjälp av standard loggnings mönstret för ditt språk. Varje meddelande tilldelas en av följande kategorier: **kritisk**, **fel**, **Varning**, **information**, **fel sökning** och **spårning**. Du kan välja hur utförlig loggning ska ske genom att ange allvarlighets grad när du aktiverar program loggning.|
| Webb Server loggning| Windows | App Service fil system eller Azure Storage blobbar| Rå data för HTTP-begäran i [utökat logg fils format för W3C](/windows/desktop/Http/w3c-logging). Varje logg meddelande innehåller data, till exempel HTTP-metoden, resurs-URI, klient-IP, klient port, användar agent, svars kod och så vidare. |
| Detaljerade fel meddelanden| Windows | App Service fil system | Kopior av *. htm* -felsidor som skulle ha skickats till klient webbläsaren. Av säkerhets skäl bör detaljerade felsidor inte skickas till klienter i produktion, men App Service kan spara felsidan varje gången ett program fel uppstår som har HTTP-kod 400 eller senare. Sidan kan innehålla information som kan hjälpa dig att avgöra varför servern returnerar felkoden. |
| Spårning av misslyckade begär Anden | Windows | App Service fil system | Detaljerad spårnings information om misslyckade förfrågningar, inklusive spårning av IIS-komponenter som används för att bearbeta begäran och den tid som tagits i varje komponent. Det är användbart om du vill förbättra platsens prestanda eller isolera ett särskilt HTTP-fel. En mapp skapas för varje misslyckad begäran, som innehåller XML-loggfilen och XSL-formatmallen för att Visa logg filen med. |
| Distributions loggning | Windows, Linux | App Service fil system | Loggar för när du publicerar innehåll till en app. Distributions loggning sker automatiskt och det finns inga konfigurerbara inställningar för distributions loggning. Det hjälper dig att avgöra varför en distribution misslyckades. Om du till exempel använder ett [anpassat distributions skript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)kan du använda distributions loggning för att avgöra varför skriptet Miss söker. |

> [!NOTE]
> App Service tillhandahåller ett dedikerat, interaktivt diagnos verktyg som hjälper dig att felsöka ditt program. Mer information finns i [Översikt över Azure App Service-diagnostik](overview-diagnostics.md).
>
> Dessutom kan du använda andra Azure-tjänster för att förbättra funktionerna för loggning och övervakning av din app, till exempel [Azure Monitor](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Aktivera program loggning (Windows)

> [!NOTE]
> Program loggning för Blob Storage kan bara använda lagrings konton i samma region som App Service

Om du vill aktivera program loggning för Windows-appar i [Azure Portal](https://portal.azure.com)navigerar du till din app och väljer **App Service loggar**.

Välj **antingen för** **program loggning (fil system)** eller **program loggning (BLOB)** eller båda. 

Alternativet **fil system** är för tillfälliga fel söknings syfte och inaktive ras i 12 timmar. **BLOB** -alternativet är för långsiktig loggning och behöver en Blob Storage-behållare för att skriva loggar till.  **BLOB** -alternativet innehåller också ytterligare information i logg meddelandena, till exempel ID: t för den ursprungliga virtuella dator instansen av logg meddelandet ( `InstanceId` ), tråd-ID ( `Tid` ) och en mer detaljerad tidsstämpel ( [`EventTickCount`](/dotnet/api/system.datetime.ticks) ).

> [!NOTE]
> För närvarande kan endast .NET-program loggar skrivas till blob-lagringen. Java-, PHP-, Node.js-och python-programloggarna kan bara lagras i det App Service fil systemet (utan kod ändringar för att skriva loggar till extern lagring).
>
> Om du [återskapar åtkomst nycklarna för lagrings kontot](../storage/common/storage-account-create.md)måste du också återställa respektive loggnings konfiguration för att använda de uppdaterade åtkomst nycklarna. Gör så här:
>
> 1. På fliken **Konfigurera** ställer du in respektive loggnings funktion på **av**. Spara inställningen.
> 2. Aktivera loggning till lagrings kontots BLOB igen. Spara inställningen.
>
>

Välj **nivå** eller den informations nivå som ska loggas. Följande tabell visar de logg kategorier som ingår på varje nivå:

| Nivå | Inkluderade kategorier |
|-|-|
|**Disabled** (Inaktiverat) | Inget |
|**Fel** | Fel, kritiskt |
|**Varning** | Varning, fel, kritiskt|
|**Information** | Information, varning, fel, kritiskt|
|**Verbose** | Spåra, felsöka, info, varning, fel, kritiskt (alla kategorier) |

När du är färdig väljer du **Spara**.

## <a name="enable-application-logging-linuxcontainer"></a>Aktivera program loggning (Linux/container)

Om du vill aktivera program loggning för Linux-appar eller anpassade behållar program i [Azure Portal](https://portal.azure.com)navigerar du till din app och väljer **App Service loggar**.

I **program loggning** väljer du **fil system**.

I **kvot (MB)** anger du disk kvoten för program loggarna. I **kvarhållningsperiod (dagar)** anger du antalet dagar som loggarna ska behållas.

När du är färdig väljer du **Spara**.

## <a name="enable-web-server-logging"></a>Aktivera webb Server loggning

Om du vill aktivera loggning av webb server för Windows-appar i [Azure Portal](https://portal.azure.com)navigerar du till din app och väljer **App Service loggar**.

För **webb Server loggning** väljer du **lagring** för att lagra loggar på Blob Storage eller **fil system** för att lagra loggar i App Service fil system. 

I **kvarhållningsperiod (dagar)** anger du antalet dagar som loggarna ska behållas.

> [!NOTE]
> Om du [återskapar åtkomst nycklarna för lagrings kontot](../storage/common/storage-account-create.md)måste du återställa respektive loggnings konfiguration för att använda de uppdaterade nycklarna. Gör så här:
>
> 1. På fliken **Konfigurera** ställer du in respektive loggnings funktion på **av**. Spara inställningen.
> 2. Aktivera loggning till lagrings kontots BLOB igen. Spara inställningen.
>
>

När du är färdig väljer du **Spara**.

## <a name="log-detailed-errors"></a>Logga detaljerade fel

Om du vill spara felsidan eller spåra misslyckade begär Anden för Windows-appar i [Azure Portal](https://portal.azure.com)navigerar du till din app och väljer **App Service loggar**.

Under **detaljerad fel loggning** eller **spårning av misslyckade begär Anden** väljer du **på** och väljer sedan **Spara**.

Båda typerna av loggar lagras i App Service fil system. Upp till 50 fel (filer/mappar) bevaras. När antalet HTML-filer överstiger 50 tas de äldsta 26 felen bort automatiskt.

## <a name="add-log-messages-in-code"></a>Lägg till logg meddelanden i kod

I program koden använder du vanliga loggnings funktioner för att skicka logg meddelanden till program loggarna. Exempel:

- ASP.NET-program kan använda klassen [system. Diagnostics. trace](/dotnet/api/system.diagnostics.trace) för att logga information i Application Diagnostics-loggen. Exempel:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Som standard använder ASP.NET Core loggnings leverantören [Microsoft. Extensions. logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) . Mer information finns i [ASP.net Core loggning i Azure](/aspnet/core/fundamentals/logging/).

## <a name="stream-logs"></a>Strömningsloggar

Innan du strömmar loggar i real tid aktiverar du den loggtyp som du vill använda. All information som skrivs till filer som slutar på. txt,. log eller. htm som lagras i katalogen */LogFiles* (d:/Home/logfile) strömmas av App Service.

> [!NOTE]
> Vissa typer av loggnings buffert skriver till logg filen, vilket kan leda till att det inte finns några händelser i data strömmen. Till exempel kan en program logg post som inträffar när en användare besöker en sida visas i data strömmen före motsvarande HTTP-loggpost för sid förfrågan.
>

### <a name="in-azure-portal"></a>I Azure Portal

Om du vill strömma loggar i [Azure Portal](https://portal.azure.com)navigerar du till din app och väljer **logg ström**. 

### <a name="in-cloud-shell"></a>I Cloud Shell

Om du vill strömma loggar Live i [Cloud Shell](../cloud-shell/overview.md)använder du följande kommando:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Om du vill filtrera vissa händelser, till exempel fel, använder du parametern **--filter** . Exempel:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
Använd parametern **--Path** för att filtrera vissa logg typer, till exempel http. Exempel:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>I lokal Terminal

Om du vill strömma loggar i den lokala konsolen [installerar du Azure CLI](/cli/azure/install-azure-cli) och [loggar in på ditt konto](/cli/azure/authenticate-azure-cli). När du har loggat in följer du [anvisningarna för Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Åtkomst till loggfiler

Om du konfigurerar alternativet Azure Storage blobbar för en loggtyp behöver du ett klient verktyg som fungerar med Azure Storage. Mer information finns i [Azure Storage klient verktyg](../storage/common/storage-explorers.md).

För loggar som lagras i App Service fil systemet är det enklaste sättet att ladda ned ZIP-filen i webbläsaren på:

- Linux/container-appar: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows-appar: `https://<app-name>.scm.azurewebsites.net/api/dump`

För Linux/container-appar innehåller ZIP-filen konsolens utgående loggar för både Docker-värden och Docker-behållaren. För en utskalad app innehåller ZIP-filen en uppsättning loggar för varje instans. I det App Service fil systemet, är dessa loggfiler innehållet i */Home/LogFiles* -katalogen.

För Windows-appar innehåller ZIP-filen innehållet i *D:\Home\LogFiles* -katalogen i App Service fil system. Den har följande struktur:

| Loggtyp | Katalog | Description |
|-|-|-|
| **Program loggar** |*/LogFiles/Application/* | Innehåller en eller flera textfiler. Formatet på logg meddelandena beror på vilken loggnings leverantör du använder. |
| **Spårning av misslyckade begär Anden** | */LogFiles/W3SVC#########/* | Innehåller XML-filer och en XSL-fil. Du kan visa de formaterade XML-filerna i webbläsaren. |
| **Detaljerade fel loggar** | */LogFiles/DetailedErrors/* | Innehåller HTM-felfiler. Du kan visa HTM-filerna i webbläsaren.<br/>Ett annat sätt att Visa spårningen av misslyckade begär Anden är att navigera till din app-sida i portalen. På den vänstra menyn väljer du **diagnostisera och löser problem**, sedan söker du efter **spårnings loggar för misslyckade begär Anden** och klickar sedan på ikonen för att bläddra och visa den spårning som du vill använda. |
| **Webb server loggar** | */LogFiles/http/RawLogs/* | Innehåller textfiler som formaterats med [utökat logg fils format för W3C](/windows/desktop/Http/w3c-logging). Den här informationen kan läsas med en text redigerare eller ett verktyg som [log parser](https://www.iis.net/downloads/community/2010/04/log-parser-22).<br/>App Service stöder inte `s-computername` fälten, `s-ip` , eller `cs-version` . |
| **Distributions loggar** | */LogFiles/git/* och */Deployments/* | Innehåller loggar som genereras av interna distributions processer samt loggar för git-distributioner. |

## <a name="send-logs-to-azure-monitor-preview"></a>Skicka loggar till Azure Monitor (förhands granskning)

Med den nya [Azure Monitor-integreringen](https://aka.ms/appsvcblog-azmon)kan du [skapa diagnostikinställningar (för hands version)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) för att skicka loggar till lagrings konton, Event Hubs och Log Analytics. 

> [!div class="mx-imgBorder"]
> ![Diagnostikinställningar (förhands granskning)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Logg typer som stöds

I följande tabell visas de logg typer och beskrivningar som stöds: 

| Loggtyp | Windows | Windows-behållare | Linux | Linux-behållare | Description |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & Tomcat | Ja | Ja | Ja | Standardutdata och standard fel |
| AppServiceHTTPLogs | Ja | Ja | Ja | Ja | Webb server loggar |
| AppServiceEnvironmentPlatformLogs | Yes | Saknas | Ja | Ja | App Service-miljön: skalning, konfigurations ändringar och status loggar|
| AppServiceAuditLogs | Ja | Ja | Ja | Ja | Inloggnings aktivitet via FTP och kudu |
| AppServiceFileAuditLogs | Ja | Ja | TBA | TBA | Fil ändringar som har gjorts i webbplatsens innehåll. endast tillgängligt för Premium-nivån och över |
| AppServiceAppLogs | ASP .NET | ASP .NET | Java SE & Tomcat välsignat-bilder <sup>1</sup> | Java SE & Tomcat välsignat-bilder <sup>1</sup> | Program loggar |
| AppServiceIPSecAuditLogs  | Ja | Ja | Ja | Ja | Begär Anden från IP-regler |
| AppServicePlatformLogs  | TBA | Ja | Ja | Ja | Åtgärds loggar för behållare |

<sup>1</sup> för Java se appar lägger du till "$Website _AZMON_PREVIEW_ENABLED" i appinställningar och anger den till 1 eller True.

## <a name="next-steps"></a><a name="nextsteps"></a> Nästa steg
* [Fråga efter loggar med Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Övervaka Azure App Service](web-sites-monitor.md)
* [Felsöka Azure App Service i Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analysera app-loggar i HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
