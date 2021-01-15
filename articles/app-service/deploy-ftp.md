---
title: Distribuera innehåll med FTP/S
description: Lär dig hur du distribuerar din app för att Azure App Service med FTP eller FTPS. Förbättra webbplats säkerheten genom att inaktivera okrypterad FTP.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: cfec5ec5f14afc8c4eba5c21c5904687c9b187cc
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209261"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Distribuera din app till Azure App Service med FTP/S

Den här artikeln visar hur du använder FTP eller FTPS för att distribuera webbappen, den mobila appens Server del eller API-appen till [Azure App Service](./overview.md).

FTP/S-slutpunkten för din app är redan aktiv. Ingen konfiguration krävs för att aktivera FTP/S-distribution.

## <a name="open-ftp-dashboard"></a>Öppna FTP-instrumentpanel

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **app Services**.

    ![Sök efter app Services.](media/app-service-continuous-deployment/search-for-app-services.png)

2. Välj den webbapp som du vill distribuera.

    ![Välj din app.](media/app-service-continuous-deployment/select-your-app.png)

3. Välj   >  **FTP-**  >  **instrumentpanel** för distributions Center.

    ![Öppna FTP-instrumentpanel](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Hämta information om FTP-anslutning

I FTP-instrumentpanelen väljer du **Kopiera** för att kopiera FTPS slut punkt och autentiseringsuppgifter för appen.

![Kopiera FTP-information](./media/app-service-deploy-ftp/ftp-dashboard.png)

Vi rekommenderar att du använder **autentiseringsuppgifter för appen** för att distribuera till din app eftersom den är unik för varje app. Om du klickar på användarautentiseringsuppgifter kan du dock ange **autentiseringsuppgifter på användar** nivå som du kan använda för FTP/S-inloggning till alla App Service appar i din prenumeration.

> [!NOTE]
> Autentisering till en FTP/FTPS-slutpunkt med autentiseringsuppgifter på användar nivå kräver ett användar namn i följande format: 
>
>`<app-name>\<user-name>`
>
> Eftersom autentiseringsuppgifter på användar nivå är länkade till användaren och inte en speciell resurs, måste användar namnet vara i det här formatet för att dirigera inloggnings åtgärden till rätt app-slutpunkt.
>

## <a name="deploy-files-to-azure"></a>Distribuera filer till Azure

1. Från FTP-klienten (till exempel [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/)eller [WinSCP](https://winscp.net/index.php)) använder du den anslutnings information som du har samlat in för att ansluta till din app.
2. Kopiera dina filer och deras respektive katalog struktur till [ **/Site/wwwroot** -katalogen](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) i Azure (eller **/Site/wwwroot/App_Data/Jobs/** Directory för WebJobs).
3. Bläddra till appens URL för att kontrol lera att appen fungerar som den ska. 

> [!NOTE] 
> Till skillnad från [git-baserade distributioner](deploy-local-git.md)stöder FTP-distribution inte följande distributions automatiseringar: 
>
> - beroende återställningar (till exempel NuGet-, NPM-, PIP-och kompositör-Automation)
> - kompilering av .NET-binärfiler
> - generering av web.config (här är ett [Node.js exempel](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Generera de nödvändiga filerna manuellt på den lokala datorn och distribuera dem sedan tillsammans med din app.
>

## <a name="enforce-ftps"></a>Framtvinga FTPS

För ökad säkerhet bör du endast tillåta FTP över TLS/SSL. Du kan också inaktivera både FTP-och FTPS om du inte använder FTP-distribution.

På din apps resurs sida i [Azure Portal](https://portal.azure.com)väljer du **konfiguration**  >  av **allmänna inställningar** i det vänstra navigerings fältet.

Om du vill inaktivera okrypterad FTP väljer du **FTPS endast** i **FTP-tillstånd**. Om du vill inaktivera både FTP-och FTPS helt väljer du **inaktive rad**. När det är klart klickar du på **Spara**. Om du **bara använder FTPS** måste du framtvinga TLS 1,2 eller högre genom att gå till bladet **TLS/SSL-inställningar** i din webbapp. TLS 1,0 och 1,1 stöds inte med **FTPS**.

![Inaktivera FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatisera med skript

För FTP-distribution med [Azure CLI](/cli/azure), se [skapa en webbapp och distribuera filer med FTP (Azure CLI)](./scripts/cli-deploy-ftp.md).

För FTP-distribution med hjälp av [Azure PowerShell](/cli/azure), se [Ladda upp filer till en webbapp med FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Felsöka FTP-distribution

- [Distribuera din app till Azure App Service med FTP/S](#deploy-your-app-to-azure-app-service-using-ftps)
  - [Öppna FTP-instrumentpanel](#open-ftp-dashboard)
  - [Hämta information om FTP-anslutning](#get-ftp-connection-information)
  - [Distribuera filer till Azure](#deploy-files-to-azure)
  - [Framtvinga FTPS](#enforce-ftps)
  - [Automatisera med skript](#automate-with-scripts)
  - [Felsöka FTP-distribution](#troubleshoot-ftp-deployment)
    - [Hur kan jag felsöka FTP-distribution?](#how-can-i-troubleshoot-ftp-deployment)
    - [Jag kan inte FTP och publicera min kod. Hur kan jag lösa problemet?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
    - [Hur kan jag ansluta till FTP i Azure App Service via passivt läge?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)
  - [Nästa steg](#next-steps)
  - [Fler resurser](#more-resources)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hur kan jag felsöka FTP-distribution?

Det första steget för fel sökning av FTP-distribution är att isolera ett distributions problem från ett körnings program problem.

Ett distributions problem innebär vanligt vis att inga filer eller fel filer har distribuerats till din app. Du kan felsöka genom att undersöka FTP-distributionen eller välja en annan distributions Sök väg (till exempel käll kontroll).

Ett program problem i körnings miljön resulterar vanligt vis i rätt uppsättning filer som distribuerats till din app, men som är felaktiga i appens beteende. Du kan felsöka genom att fokusera på kod beteende vid körning och undersöka vissa fel sökvägar.

Information om distributions-eller körnings problem finns i [distribution vs. Runtime-problem](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Jag kan inte använda FTP och publicera min kod. Hur kan jag lösa problemet?
Kontrol lera att du har angett rätt värdnamn och [autentiseringsuppgifter](#open-ftp-dashboard). Kontrol lera också att följande FTP-portar på datorn inte blockeras av en brand vägg:

- Anslutnings port för FTP-kontroll: 21, 990
- Anslutnings port för FTP-data: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hur kan jag ansluta till FTP i Azure App Service via passivt läge?
Azure App Service stöder anslutning via både aktivt och passivt läge. Passivt läge är att föredra eftersom dina distributions datorer vanligt vis ligger bakom en brand vägg (i operativ systemet eller som en del av ett hem-eller företags nätverk). Se ett [exempel från WinSCP-dokumentationen](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Nästa steg

Försök [att distribuera till Azure med git](deploy-local-git.md)för mer avancerade distributions scenarier. Git-baserad distribution till Azure möjliggör versions kontroll, paket återställning, MSBuild med mera.

## <a name="more-resources"></a>Fler resurser

* [Autentiseringsuppgifter för Azure App Service distribution](deploy-configure-credentials.md)