---
title: Azure Application Insights-agent – komma igång | Microsoft Docs
description: En snabb starts guide för Application Insights agenten. Övervaka webbplatsens prestanda utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 01/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f340b4fdf7fef6daecf8801d16d19aee9b89e4d5
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746466"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Kom igång med Azure Monitor Application Insights agent för lokala servrar

Den här artikeln innehåller snabb start kommandon som förväntas fungera i de flesta miljöer.
Anvisningarna är beroende av PowerShell-galleriet för att distribuera uppdateringar.
Dessa kommandon stöder PowerShell- `-Proxy` parametern.

En förklaring av dessa kommandon, anpassnings instruktioner och information om fel sökning finns i [detaljerade instruktioner](status-monitor-v2-detailed-instructions.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="download-and-install-via-powershell-gallery"></a>Hämta och installera via PowerShell-galleriet

### <a name="install-prerequisites"></a>Installationskrav

> [!NOTE]
> Från och med april 2020 har PowerShell-galleriet föråldrat TLS 1,1 och 1,0.
>
> Additionnal-krav som du kan behöva finns i [POWERSHELL-galleriet TLS-stöd](https://devblogs.microsoft.com/powershell/powershell-gallery-tls-support).
>

Kör PowerShell som administratör.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Stäng PowerShell.

### <a name="install-application-insights-agent"></a>Installera Application Insights agent
Kör PowerShell som administratör.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Aktivera övervakning
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -ConnectionString xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Ladda ned och installera manuellt (offline-alternativ)
### <a name="download-the-module"></a>Hämta modulen
Hämta den senaste versionen av modulen manuellt från [PowerShell-galleriet](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-application-insights-agent"></a>Packa upp och installera Application Insights agent
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Aktivera övervakning
```powershell
Enable-ApplicationInsightsMonitoring -ConnectionString xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>Nästa steg

 Visa telemetrin:

- [Utforska mått](../platform/metrics-charts.md) för att övervaka prestanda och användning.
- [Sök efter händelser och loggar](./diagnostic-search.md) för att diagnostisera problem.
- [Använd Analytics](../log-query/log-query-overview.md) för mer avancerade frågor.
- [Skapa instrument paneler](./overview-dashboard.md).

 Lägg till mer telemetri:

- [Skapa webbtester](monitor-web-app-availability.md) så att du är säker på att webbplatsen är aktiv.
- [Lägg till telemetri för webb klienter](./javascript.md) om du vill visa undantag från webb sidans kod och aktivera spårnings anrop.
- [Lägg till Application Insights SDK till din kod](./asp-net.md) så att du kan infoga spårnings-och logg anrop.

Gör mer med Application Insights agent:

- Läs igenom de [detaljerade anvisningarna](status-monitor-v2-detailed-instructions.md) för en förklaring av de kommandon som finns här.
- Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights-agenten.

