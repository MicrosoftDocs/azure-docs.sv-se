---
title: Distribuera innehåll med FTP/S
description: Lär dig hur du distribuerar din app Azure App Service via FTP eller FTPS. Förbättra webbplatssäkerheten genom att inaktivera okrypterad FTP.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: fd856ee47c1100292f7558bb0fa2a1772951a3cb
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832377"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Distribuera din app till Azure App Service FTP/S

Den här artikeln visar hur du använder FTP eller FTPS för att distribuera din webbapp, serverdel för mobilappar eller API-app för [att Azure App Service](./overview.md).

FTP/S-slutpunkten för din app är redan aktiv. Ingen konfiguration krävs för att aktivera FTP/S-distribution.

> [!NOTE]
> Sidan **Development Center (klassisk)** i Azure Portal, som är den gamla distributionsupplevelsen, kommer att bli inaktuell i mars 2021. Den här ändringen påverkar inte befintliga distributionsinställningar i din app och du kan fortsätta att hantera appdistributionen på **sidan Distributionscenter.**

## <a name="get-deployment-credentials"></a>Hämta autentiseringsuppgifter för distribution

1. Följ anvisningarna i Konfigurera [autentiseringsuppgifter för Azure App Service](deploy-configure-credentials.md) att kopiera autentiseringsuppgifterna för programomfånget eller ange autentiseringsuppgifter för användaromfång. Du kan ansluta till FTP/S-slutpunkten för din app med båda autentiseringsuppgifterna.

1. Skapa FTP-användarnamnet i följande format, beroende på ditt val av omfång för autentiseringsuppgifter:

    | Programomfång | Användaromfång |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    I App Service delas FTP/S-slutpunkten mellan appar. Eftersom autentiseringsuppgifterna för användaromfång inte är länkade till en specifik resurs måste du lägga till användarnamnet för användaromfånget med appnamnet som visas ovan.

## <a name="get-ftps-endpoint"></a>Hämta FTP/S-slutpunkt
    
# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

På samma hanteringssida för din app där du kopierade autentiseringsuppgifterna för distribution (**Deployment Center**  >  **FTP-autentiseringsuppgifter**) kopierar du **FTPS-slutpunkten**.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Kör kommandot [az webapp deployment list-publishing-profiles.](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) I följande exempel används en [JMES-sökväg för](https://jmespath.org/) att extrahera FTP/S-slutpunkterna från utdata.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

Varje app har två FTP/S-slutpunkter, en är skrivskyddade, medan den andra är skrivskyddade ( innehåller ) och är `profileName` `ReadOnly` för dataåterställningsscenarier. Om du vill distribuera filer med FTP kopierar du URL:en för slutpunkten för läsning och skrivning.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Kör kommandot [Get-AzWebAppPublishingProfile.](/powershell/module/az.websites/get-azwebapppublishingprofile) I följande exempel extraheras FTP/S-slutpunkten från XML-utdata.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Distribuera filer till Azure

1. Från FTP-klienten (till exempel [Visual Studio,](https://www.visualstudio.com/vs/community/) [Cyberdattack](https://cyberduck.io/)eller [WinSCP](https://winscp.net/index.php)) använder du anslutningsinformationen som du samlade in för att ansluta till din app.
2. Kopiera dina filer och deras respektive katalogstruktur till [ **katalogen /site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) i Azure (eller katalogen **/site/wwwroot/App_Data/Jobs/för** WebJobs).
3. Bläddra till appens URL för att kontrollera att appen körs korrekt. 

> [!NOTE] 
> Till [skillnad från Git-baserade distributioner](deploy-local-git.md) [och Zip-distribution](deploy-zip.md)stöder FTP-distribution inte byggautomatisering, till exempel: 
>
> - beroendeåterställningar (till exempel Automatiseringar av NuGet, NPM, PIP och Composer)
> - kompilering av .NET-binärfiler
> - generering av web.config (här är ett [Node.js exempel](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Generera de nödvändiga filerna manuellt på den lokala datorn och distribuera dem sedan tillsammans med din app.
>

## <a name="enforce-ftps"></a>Framtvinga FTPS

För ökad säkerhet bör du endast tillåta FTP via TLS/SSL. Du kan också inaktivera både FTP och FTPS om du inte använder FTP-distribution.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. På appens resurssida i den [Azure Portal](https://portal.azure.com)du Allmänna **konfigurationsinställningar**  >  **i** det vänstra navigeringsfönstret.

2. Om du vill inaktivera okrypterad FTP väljer **du ENDAST FTPS** i **FTP-tillstånd**. Om du vill inaktivera både FTP och FTPS helt väljer du **Inaktiverad.** När det är klart klickar du på **Spara**. Om du **använder endast FTPS** måste du framtvinga TLS 1.2 eller senare genom att navigera till **bladet TLS/SSL-inställningar** för webbappen. TLS 1.0 och 1.1 stöds inte med **endast FTPS.**

    ![Inaktivera FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Kör kommandot [az webapp config set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) med `--ftps-state` argumentet .

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

Möjliga värden för `--ftps-state` är `AllAllowed` (FTP och FTPS aktiverat), (FTP och `Disabled` FTPs inaktiverade) och `FtpsOnly` (endast FTPS).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Kör kommandot [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) med `-FtpsState` parametern .

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

Möjliga värden för `--ftps-state` är `AllAllowed` (FTP och FTPS aktiverat), (FTP och `Disabled` FTPs inaktiverade) och `FtpsOnly` (endast FTPS).

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Felsöka FTP-distribution

- [Hur felsöker jag FTP-distribution?](#how-can-i-troubleshoot-ftp-deployment)
- [Jag kan inte ANVÄNDA FTP och publicera min kod. Hur kan jag lösa problemet?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hur kan jag ansluta till FTP i Azure App Service via passivt läge?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hur felsöker jag FTP-distribution?

Det första steget vid felsökning av FTP-distribution är att isolera ett distributionsproblem från ett körningsprogramproblem.

Ett distributionsproblem resulterar vanligtvis i att inga filer eller fel filer distribueras till din app. Du kan felsöka genom att undersöka FTP-distributionen eller välja en alternativ distributionssökväg (till exempel källkontroll).

Ett körningsprogramproblem resulterar vanligtvis i rätt uppsättning filer som distribueras till din app, men felaktigt appbeteende. Du kan felsöka genom att fokusera på kodbeteende vid körning och undersöka specifika felsökvägar.

Information om hur du fastställer ett distributions- eller körningsproblem [finns i Distribution jämfört med körningsproblem.](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Jag kan inte använda FTP och publicera min kod. Hur kan jag lösa problemet?
Kontrollera att du har angett rätt [värdnamn och](#get-ftps-endpoint) [autentiseringsuppgifter.](#get-deployment-credentials) Kontrollera också att följande FTP-portar på datorn inte blockeras av en brandvägg:

- Anslutningsport för FTP-kontroll: 21, 990
- FTP-dataanslutningsport: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hur ansluter jag till FTP i Azure App Service via passivt läge?
Azure App Service stöder anslutning via både aktivt och passivt läge. Passivt läge är att föredra eftersom dina distributionsdatorer vanligtvis finns bakom en brandvägg (i operativsystemet eller som en del av ett hem- eller företagsnätverk). Se ett [exempel i WinSCP-dokumentationen.](https://winscp.net/docs/ui_login_connection) 

## <a name="more-resources"></a>Fler resurser

* [Lokal Git-distribution till Azure App Service](deploy-local-git.md)
* [Azure App Service autentiseringsuppgifter för distribution](deploy-configure-credentials.md)
* [Exempel: Skapa en webbapp och distribuera filer med FTP (Azure CLI).](./scripts/cli-deploy-ftp.md)
* [Exempel: Ladda upp filer till en webbapp med FTP (PowerShell).](./scripts/powershell-deploy-ftp.md)
