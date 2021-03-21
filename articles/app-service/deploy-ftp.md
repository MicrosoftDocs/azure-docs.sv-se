---
title: Distribuera innehåll med FTP/S
description: Lär dig hur du distribuerar din app för att Azure App Service med FTP eller FTPS. Förbättra webbplats säkerheten genom att inaktivera okrypterad FTP.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c7427a1f8f528fdf405b22c4e91941ea7a915ffa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045810"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Distribuera din app till Azure App Service med FTP/S

Den här artikeln visar hur du använder FTP eller FTPS för att distribuera webbappen, den mobila appens Server del eller API-appen till [Azure App Service](./overview.md).

FTP/S-slutpunkten för din app är redan aktiv. Ingen konfiguration krävs för att aktivera FTP/S-distribution.

> [!NOTE]
> **Utvecklings Center sidan (klassisk)** i Azure Portal, vilket är den gamla distributions miljön, kommer att bli inaktuell i mars 2021. Den här ändringen påverkar inte några befintliga distributions inställningar i appen och du kan fortsätta att hantera distribution av appar på sidan **distributions Center** .

## <a name="get-deployment-credentials"></a>Hämta autentiseringsuppgifter för distribution

1. Följ anvisningarna i [Konfigurera autentiseringsuppgifter för distribution för Azure App Service](deploy-configure-credentials.md) för att kopiera programmets autentiseringsuppgifter eller ange autentiseringsuppgifter för användarens omfång. Du kan ansluta till FTP/S-slutpunkten för din app med hjälp av antingen autentiseringsuppgifterna.

1. Dela FTP-användarnamnet i följande format, beroende på ditt val av referens omfång:

    | Program-omfattning | Användar omfång |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    I App Service delas FTP/S-slutpunkten mellan appar. Eftersom autentiseringsuppgifterna för användar gränssnittet inte är länkade till en speciell resurs måste du lägga användar omfånget med namnet på appen som visas ovan.

## <a name="get-ftps-endpoint"></a>Hämta FTP/S-slutpunkt
    
# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

På samma hanterings sida för appen dit du kopierade autentiseringsuppgifterna för distribution (  >  **FTP-autentiseringsuppgifter** för distributions Center) kopierar du **FTPS-slutpunkten**.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Kör kommandot [AZ webapp Deployment List-Publishing-profils](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) . I följande exempel används en [JMES-sökväg](https://jmespath.org/) för att extrahera FTP/S-slutpunkterna från utdata.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

Varje app har två FTP/S-slutpunkter, en är Read-Write, medan den andra är skrivskyddad ( `profileName` innehåller `ReadOnly` ) och är för data återställnings scenarier. Om du vill distribuera filer med FTP kopierar du URL: en till Read-Write-slutpunkten.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Kör kommandot [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) . I följande exempel extraheras FTP/S-slutpunkten från XML-utdata.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Distribuera filer till Azure

1. Från FTP-klienten (till exempel [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/)eller [WinSCP](https://winscp.net/index.php)) använder du den anslutnings information som du har samlat in för att ansluta till din app.
2. Kopiera dina filer och deras respektive katalog struktur till [ **/Site/wwwroot** -katalogen](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) i Azure (eller **/Site/wwwroot/App_Data/Jobs/** Directory för WebJobs).
3. Bläddra till appens URL för att kontrol lera att appen fungerar som den ska. 

> [!NOTE] 
> Till skillnad från [git-baserade distributioner](deploy-local-git.md) och [zip-distribution](deploy-zip.md)stöder FTP-distribution inte bygg automatisering, till exempel: 
>
> - beroende återställningar (till exempel NuGet-, NPM-, PIP-och kompositör-Automation)
> - kompilering av .NET-binärfiler
> - generering av web.config (här är ett [Node.js exempel](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Generera de nödvändiga filerna manuellt på den lokala datorn och distribuera dem sedan tillsammans med din app.
>

## <a name="enforce-ftps"></a>Framtvinga FTPS

För ökad säkerhet bör du endast tillåta FTP över TLS/SSL. Du kan också inaktivera både FTP-och FTPS om du inte använder FTP-distribution.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. På din apps resurs sida i [Azure Portal](https://portal.azure.com)väljer du **konfiguration**  >  av **allmänna inställningar** i det vänstra navigerings fältet.

2. Om du vill inaktivera okrypterad FTP väljer du **FTPS endast** i **FTP-tillstånd**. Om du vill inaktivera både FTP-och FTPS helt väljer du **inaktive rad**. När det är klart klickar du på **Spara**. Om du **bara använder FTPS** måste du framtvinga TLS 1,2 eller högre genom att gå till bladet **TLS/SSL-inställningar** i din webbapp. TLS 1,0 och 1,1 stöds inte med **FTPS**.

    ![Inaktivera FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Kör kommandot [AZ webapp config set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) med `--ftps-state` argumentet.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

Möjliga värden för `--ftps-state` är `AllAllowed` (FTP och FTPs aktiverat), `Disabled` (FTP och FTPs inaktive rad) och `FtpsOnly` (endast FTPS).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Kör kommandot [set-AzWebApp](/powershell/module/az.websites/set-azwebapp) med `-FtpsState` parametern.

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

Möjliga värden för `--ftps-state` är `AllAllowed` (FTP och FTPs aktiverat), `Disabled` (FTP och FTPs inaktive rad) och `FtpsOnly` (endast FTPS).

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Felsöka FTP-distribution

- [Hur kan jag felsöka FTP-distribution?](#how-can-i-troubleshoot-ftp-deployment)
- [Jag kan inte FTP och publicera min kod. Hur kan jag lösa problemet?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hur kan jag ansluta till FTP i Azure App Service via passivt läge?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hur kan jag felsöka FTP-distribution?

Det första steget för fel sökning av FTP-distribution är att isolera ett distributions problem från ett körnings program problem.

Ett distributions problem innebär vanligt vis att inga filer eller fel filer har distribuerats till din app. Du kan felsöka genom att undersöka FTP-distributionen eller välja en annan distributions Sök väg (till exempel käll kontroll).

Ett program problem i körnings miljön resulterar vanligt vis i rätt uppsättning filer som distribuerats till din app, men som är felaktiga i appens beteende. Du kan felsöka genom att fokusera på kod beteende vid körning och undersöka vissa fel sökvägar.

Information om distributions-eller körnings problem finns i [distribution vs. Runtime-problem](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Jag kan inte använda FTP och publicera min kod. Hur kan jag lösa problemet?
Kontrol lera att du har angett rätt [värdnamn](#get-ftps-endpoint) och [autentiseringsuppgifter](#get-deployment-credentials). Kontrol lera också att följande FTP-portar på datorn inte blockeras av en brand vägg:

- Anslutnings port för FTP-kontroll: 21, 990
- Anslutnings port för FTP-data: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hur kan jag ansluta till FTP i Azure App Service via passivt läge?
Azure App Service stöder anslutning via både aktivt och passivt läge. Passivt läge är att föredra eftersom dina distributions datorer vanligt vis ligger bakom en brand vägg (i operativ systemet eller som en del av ett hem-eller företags nätverk). Se ett [exempel från WinSCP-dokumentationen](https://winscp.net/docs/ui_login_connection). 

## <a name="more-resources"></a>Fler resurser

* [Lokal Git-distribution till Azure App Service](deploy-local-git.md)
* [Autentiseringsuppgifter för Azure App Service distribution](deploy-configure-credentials.md)
* [Exempel: skapa en webbapp och distribuera filer med FTP (Azure CLI)](./scripts/cli-deploy-ftp.md).
* [Exempel: Ladda upp filer till en webbapp med FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).
