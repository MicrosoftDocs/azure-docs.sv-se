---
title: Operativ system-och körnings uppdatering takt
description: Lär dig hur Azure App Service uppdaterar operativ systemet och körnings miljön, vilka körningar och korrigerings nivåer dina appar har och hur du kan få uppdaterings meddelanden.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 8b876760ee2bafc855345878a28c38ec3b35daad
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047889"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Uppdatering av operativ system och körning i Azure App Service

Den här artikeln visar hur du hämtar viss versions information om operativ systemet eller programmet i [App Service](overview.md). 

App Service är en plattform som en tjänst, vilket innebär att operativ systemet och program stacken hanteras av Azure. du hanterar bara ditt program och dess data. Mer kontroll över operativ systemet och program stacken är tillgängligt i [Azure Virtual Machines](../virtual-machines/index.yml). Med detta i åtanke är det dock bra att använda som App Service användare för att få mer information, till exempel:

-   Hur och när tillämpas OS-uppdateringar?
-   Hur korrigeras App Service mot betydande sårbarheter (till exempel noll dagar)?
-   Vilka operativ system och körnings versioner kör dina appar?

Av säkerhets skäl publiceras inte vissa specifika säkerhets uppgifter. Artikeln syftar dock till att undvika problem genom att maximera genomskinligheten i processen och hur du kan hålla dig uppdaterad om säkerhetsrelaterade meddelanden eller körnings uppdateringar.

## <a name="how-and-when-are-os-updates-applied"></a>Hur och när tillämpas OS-uppdateringar?

Azure hanterar OS-korrigeringar på två nivåer, de fysiska servrarna och de virtuella gäst datorerna (virtuella datorer) som kör App Service-resurserna. Båda uppdateras varje månad, som anpassas efter månads [uppdateringens](/security-updates/) månads schema. De här uppdateringarna tillämpas automatiskt, på ett sätt som garanterar service avtalet med hög tillgänglighet för Azure-tjänster. 

För detaljerad information om hur uppdateringar tillämpas, se [avmystifiera Magic bakom App Service OS-uppdateringar](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Hur hanterar Azure viktiga sårbarheter?

När allvarliga säkerhets risker kräver omedelbar uppdatering, t. ex. [noll dagars sårbarheter](https://wikipedia.org/wiki/Zero-day_(computing)), hanteras hög prioritets uppdateringar från fall till fall.

Håll dig uppdaterad med viktiga säkerhets meddelanden i Azure genom att gå till [Azures säkerhets blogg](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>När är stödda språk körningar uppdaterade, tillagda eller inaktuella?

Nya, stabila versioner av språk körningar som stöds (huvud, del eller korrigering) läggs regelbundet till App Service instanser. Vissa uppdateringar skriver över den befintliga installationen, medan andra installeras sida vid sida med befintliga versioner. En överskrivnings installation innebär att appen körs automatiskt på den uppdaterade körningen. En sida vid sida-installation innebär att du måste migrera appen manuellt för att kunna dra nytta av en ny körnings version. Mer information finns i en av underavsnitten.

Körnings uppdateringar och utfasningar meddelas här:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Informationen här gäller för språk körningar som är inbyggda i en App Service-app. En anpassad körning som du överför till App Service, till exempel, förblir oförändrad om du inte uppgraderar den manuellt.
>
>

### <a name="new-patch-updates"></a>Nya uppdaterings uppdateringar

Uppdaterings uppdateringar av .NET, PHP, Java SDK eller Tomcat-versionen tillämpas automatiskt genom att den befintliga installationen skrivs över med den senaste versionen. Node.js korrigerings uppdateringar installeras sida vid sida med befintliga versioner (liknar de högre och lägre versionerna i nästa avsnitt). Nya python-korrigeringsfiler kan installeras manuellt via [webbplats tillägg](https://azure.microsoft.com/blog/azure-web-sites-extensions/), sida vid sida med inbyggda python-installationer.

### <a name="new-major-and-minor-versions"></a>Nya högre och lägre versioner

När en ny större eller lägre version läggs till installeras den sida vid sida med befintliga versioner. Du kan uppgradera appen manuellt till den nya versionen. Om du har konfigurerat kör tids versionen i en konfigurations fil (till exempel `web.config` och `package.json` ), måste du uppgradera med samma metod. Om du använde en App Service inställning för att konfigurera kör tids versionen kan du ändra den i [Azure Portal](https://portal.azure.com) eller genom att köra ett [Azure CLI](/cli/azure/get-started-with-azure-cli) -kommando i [Cloud Shell](../cloud-shell/overview.md), som du ser i följande exempel:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.8 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Föråldrade versioner  

När en äldre version är föråldrad meddelas borttagnings datumet så att du kan planera din uppgradering av runtime-versionen enligt detta. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Hur kan jag fråga om status för operativ system och körnings uppdateringar på mina instanser?  

Även om viktig information om operativ systemet är låst från Access (se [operativ systemets funktioner på Azure App Service](operating-system-functionality.md)) kan du använda [kudu-konsolen](https://github.com/projectkudu/kudu/wiki/Kudu-console) för att fråga din app service-instans om operativ systemets version och körnings versioner. 

Följande tabell visar hur du använder Windows-versioner och språk körnings miljön som kör dina appar:

| Information | Var du hittar den | 
|-|-|
| Windows-version | Se `https://<appname>.scm.azurewebsites.net/Env.cshtml` (under system information) |
| .NET-version | `https://<appname>.scm.azurewebsites.net/DebugConsole`Kör följande kommando i kommando tolken i: <br>`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full"` |
| .NET Core-version | `https://<appname>.scm.azurewebsites.net/DebugConsole`Kör följande kommando i kommando tolken i: <br> `dotnet --version` |
| PHP-version | `https://<appname>.scm.azurewebsites.net/DebugConsole`Kör följande kommando i kommando tolken i: <br> `php --version` |
| Standard Node.js version | Kör följande kommando i [Cloud Shell](../cloud-shell/overview.md): <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python-version | `https://<appname>.scm.azurewebsites.net/DebugConsole`Kör följande kommando i kommando tolken i: <br> `python --version` |  
| Java-version | `https://<appname>.scm.azurewebsites.net/DebugConsole`Kör följande kommando i kommando tolken i: <br> `java -version` |  

> [!NOTE]  
> Åtkomst till register platsen `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages` , där information om [uppdateringar av "KB"](/security-updates/SecurityBulletins/securitybulletins) lagras, är låst.
>
>

## <a name="more-resources"></a>Fler resurser

[Säkerhets Center: säkerhet](https://www.microsoft.com/en-us/trustcenter/security)  
[64 bitars ASP.NET Core på Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
