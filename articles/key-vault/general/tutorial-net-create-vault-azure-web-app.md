---
title: Självstudie – Använda Azure Key Vault med en Azure-webbapp i .NET
description: I den här självstudien konfigurerar du en Azure-webbapp i ett ASP.NET Core-program för att läsa en hemlighet från ditt nyckelvalv.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 91467d1de3a1543736115ef7a25281733c8eb85d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819222"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>Självstudie: Använda en hanterad identitet för att Key Vault till en Azure-webbapp i .NET

[Azure Key Vault](./overview.md) ett sätt att lagra autentiseringsuppgifter och andra hemligheter med ökad säkerhet. Men din kod måste autentiseras för att Key Vault hämta dem. [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md) hjälper till att lösa det här problemet genom att ge Azure-tjänsterna en automatiskt hanterad identitet Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, Key Vault, utan att behöva visa autentiseringsuppgifter i koden.

I den här självstudien skapar och distribuerar du Azure-webbprogram till [Azure App Service](../../app-service/overview.md). Du använder en hanterad identitet för att autentisera din Azure-webbapp med ett Azure-nyckelvalv med hjälp Azure Key Vault ett hemligt [klientbibliotek](/dotnet/api/overview/azure/key-vault) för .NET och [Azure CLI.](/cli/azure/get-started-with-azure-cli) Samma grundläggande principer gäller när du använder val av utvecklingsspråk, Azure PowerShell och/eller Azure Portal.

Mer information om hur du Azure App-tjänstwebbprogram och distribution som presenteras i den här självstudien finns i:
- [Översikt över App Service](../../app-service/overview.md)
- [Skapa en ASP.NET Core-webbapp i Azure App Service](../../app-service/quickstart-dotnetcore.md)
- [Lokal Git-distribution till Azure App Service](../../app-service/deploy-local-git.md)

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. [Skapa ett kostnadsfritt.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 3.1 SDK (eller senare)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* En [Git-installation](https://www.git-scm.com/downloads) av version 2.28.0 eller senare.
* [Azure CLI eller](/cli/azure/install-azure-cli) [Azure PowerShell](/powershell/azure/).
* [Azure Key Vault.](./overview.md) Du kan skapa ett nyckelvalv med [hjälp av Azure Portal,](quick-create-portal.md) [Azure CLI](quick-create-cli.md)eller [Azure PowerShell](quick-create-powershell.md).
* En Key Vault [hemlighet](../secrets/about-secrets.md). Du kan skapa en hemlighet med hjälp [Azure Portal,](../secrets/quick-create-portal.md) [PowerShell](../secrets/quick-create-powershell.md)eller [Azure CLI.](../secrets/quick-create-cli.md)

Om du redan har distribuerat webbappen i Azure App Service [](#create-and-assign-a-managed-identity) kan du gå vidare och konfigurera webbappåtkomst till ett nyckelvalv och ändra kodavsnitt [för webbprogram.](#modify-the-app-to-access-your-key-vault)

## <a name="create-a-net-core-app"></a>Skapa en .NET Core-app
I det här steget ska du konfigurera det lokala .NET Core-projektet.

I ett terminalfönster på datorn skapar du en katalog med namnet `akvwebapp` och gör den till den aktuella katalogen:

```bash
mkdir akvwebapp
cd akvwebapp
```

Skapa en .NET Core-app med hjälp av [det nya webbkommandot dotnet:](/dotnet/core/tools/dotnet-new)

```bash
dotnet new web
```

Kör programmet lokalt så att du vet hur det ska se ut när du distribuerar det till Azure:

```bash
dotnet run
```

I en webbläsare går du till appen på `http://localhost:5000` .

Du ser då meddelandet ”Hello World!” från exempelappen som visas på sidan.

Mer information om hur du skapar webbprogram för Azure finns i [Skapa en ASP.NET Core-webbapp i Azure App Service](../../app-service/quickstart-dotnetcore.md)

## <a name="deploy-the-app-to-azure"></a>distribuera appen till Azure

I det här steget distribuerar du ditt .NET Core-program till Azure App Service med hjälp av lokal Git. Mer information om hur du skapar och distribuerar program finns i [Skapa en ASP.NET Core-webbapp i Azure](../../app-service/quickstart-dotnetcore.md).

### <a name="configure-the-local-git-deployment"></a>Konfigurera den lokala Git-distributionen

I terminalfönstret väljer du **Ctrl+C** för att stänga webbservern.  Initiera en Git-lagringsplats för .NET Core-projektet:

```bash
git init --initial-branch=main
git add .
git commit -m "first commit"
```

Du kan använda FTP och lokal Git för att distribuera en Azure-webbapp med hjälp av en *distributionsanvändare*. När du har konfigurerat distributionsanvändaren kan du använda den för alla dina Azure-distributioner. Ditt användarnamn och lösenord för distribution på kontonivå skiljer sig från autentiseringsuppgifterna för din Azure-prenumeration. 

Konfigurera distributionsanvändaren genom att köra [kommandot az webapp deployment user set.](/cli/azure/webapp/deployment/user?#az_webapp_deployment_user_set) Välj ett användarnamn och lösenord som följer dessa riktlinjer: 

- Användarnamnet måste vara unikt inom Azure. För lokala Git-pushar får den inte innehålla tecknet vid tecknet (@). 
- Lösenordet måste vara minst åtta tecken långt och innehålla två av följande tre element: bokstäver, siffror och symboler. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

JSON-utdata visar lösenordet som `null` . Om du får `'Conflict'. Details: 409` ett felmeddelande ändrar du användarnamnet. Om du ser felet `'Bad Request'. Details: 400` ska du använda ett starkare lösenord. 

Registrera ditt användarnamn och lösenord så att du kan använda det för att distribuera dina webbappar.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där du distribuerar Azure-resurser och hanterar dem. Skapa en resursgrupp som ska innehålla både nyckelvalvet och webbappen med hjälp av [kommandot az group create:](/cli/azure/group?#az_group_create)

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

Skapa en [App Service plan](../../app-service/overview-hosting-plans.md) med hjälp av kommandot Azure CLI [az appservice plan create.](/cli/azure/appservice/plan) I följande exempel skapas App Service plan med `myAppServicePlan` namnet `FREE` på prisnivån:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

När App Service plan skapas visar Azure CLI information som liknar det du ser här:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

Mer information finns i [Hanera en App Service-plan i Azure](../../app-service/app-service-plan-manage.md).

### <a name="create-a-web-app"></a>Skapa en webbapp

Skapa en [Azure-webbapp](../../app-service/overview.md) i `myAppServicePlan` App Service plan. 

> [!Important]
> Precis som ett nyckelvalv måste en Azure-webbapp ha ett unikt namn. Ersätt `<your-webapp-name>` med namnet på din webbapp i följande exempel.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

När webbappen har skapats visar Azure CLI utdata som liknar det du ser här:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

URL:en för fjärransluten Git visas `deploymentLocalGitUrl` i egenskapen i formatet `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` . Spara den här URL:en. Du behöver det senare.

Konfigurera nu webbappen så att den distribueras från `main` -grenen:

```azurecli-interactive
 az webapp config appsettings set -g MyResourceGroup -name "<your-webapp-name>"--settings deployment_branch=main
```

Gå till den nya appen med hjälp av följande kommando. Ersätt `<your-webapp-name>` med namnet på din app.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Du ser standardwebbsidan för en ny Azure-webbapp.

### <a name="deploy-your-local-app"></a>Distribuera din lokala app

I det lokala terminalfönstret kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. I följande kommando ersätter du `<deploymentLocalGitUrl-from-create-step>` med URL:en för den Fjärranslutna Git-fil som du sparade i [avsnittet Skapa en webbapp.](#create-a-web-app)

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Använd följande kommando för att skicka till Azure-fjärrservern för att distribuera din app. När Git Autentiseringshanteraren dig om autentiseringsuppgifter använder du de autentiseringsuppgifter som du skapade i [avsnittet Konfigurera den lokala Git-distributionen.](#configure-the-local-git-deployment)

```bash
git push azure main
```

Det här kommandot kan ta några minuter att köra. När den körs visas information som liknar det du ser här:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  main -> main
</pre>

Gå till (eller uppdatera) det distribuerade programmet med hjälp av webbläsaren:

```bash
http://<your-webapp-name>.azurewebsites.net
```

Du ser då meddelandet ”Hello World!” som du såg tidigare när du besökte `http://localhost:5000` .

Mer information om hur du distribuerar webbprogram med Git finns i [Lokal Git-distribution till Azure App Service](../../app-service/deploy-local-git.md)
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>Konfigurera webbappen för att ansluta till Key Vault

I det här avsnittet konfigurerar du webbåtkomst till Key Vault och uppdaterar programkoden för att hämta en hemlighet från Key Vault.

### <a name="create-and-assign-a-managed-identity"></a>Skapa och tilldela en hanterad identitet

I den här självstudien använder vi [hanterad identitet för](../../active-directory/managed-identities-azure-resources/overview.md) att autentisera till Key Vault. Hanterad identitet hanterar automatiskt programautentiseringsuppgifter.

I Azure CLI skapar du identiteten för programmet genom att köra kommandot [az webapp-identity assign:](/cli/azure/webapp/identity?#az_webapp_identity_assign)

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Kommandot returnerar det här JSON-kodfragmentet:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Om du vill ge  webbappen behörighet att hämta och lista åtgärder i nyckelvalvet skickar du till kommandot Azure CLI az  `principalId` [keyvault set-policy:](/cli/azure/keyvault?#az_keyvault_set_policy)

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

Du kan också tilldela åtkomstprinciper med hjälp av [Azure Portal](./assign-access-policy-portal.md) eller [PowerShell.](./assign-access-policy-powershell.md)

### <a name="modify-the-app-to-access-your-key-vault"></a>Ändra appen för att få åtkomst till ditt nyckelvalv

I den här självstudien använder [du Azure Key Vault ett hemligt klientbibliotek](/dotnet/api/overview/azure/security.keyvault.secrets-readme) i demonstrationssyfte. Du kan också använda [Azure Key Vault certifikatklientbibliotek](/dotnet/api/overview/azure/security.keyvault.certificates-readme), eller [Azure Key Vault nyckelklientbiblioteket](/dotnet/api/overview/azure/security.keyvault.keys-readme).

#### <a name="install-the-packages"></a>Installera paketen

Installera det hemliga klientbiblioteket Azure Key Vault .NET- och Azure Identity-klientbibliotekspaket från terminalfönstret:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>Uppdatera koden

Leta upp och öppna filen Startup.cs i ditt akvwebapp-projekt. 

Lägg till följande rader i rubriken:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Lägg till följande rader före `app.UseEndpoints` anropet och uppdatera URI:en så att den `vaultUri` återspeglar för ditt nyckelvalv. Den här koden  [använder DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential) för att autentisera till Key Vault, som använder en token från en hanterad identitet för att autentisera. Mer information om hur du autentiserar Key Vault finns i [utvecklarhandboken.](./developers-guide.md#authenticate-to-key-vault-in-code) Koden använder också exponentiell backoff för återförsök om Key Vault begränsas. Mer information om Key Vault finns i [Azure Key Vault vägledningen om begränsningar.](./overview-throttling.md)

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

Uppdatera raden så `await context.Response.WriteAsync("Hello World!");` att den ser ut så här:

```csharp
await context.Response.WriteAsync(secretValue);
```

Se till att spara ändringarna innan du fortsätter till nästa steg.

#### <a name="redeploy-your-web-app"></a>Distribuera din webbapp igen

Nu när du har uppdaterat koden kan du distribuera om den till Azure med hjälp av följande Git-kommandon:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>Gå till din färdiga webbapp

```bash
http://<your-webapp-name>.azurewebsites.net
```

Där innan du såg "Hello World!", bör du nu se värdet för din hemlighet.

## <a name="next-steps"></a>Nästa steg

- [Använda Azure Key Vault med program som distribuerats till en virtuell dator i .NET](./tutorial-net-virtual-machine.md)
- Läs mer om [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md)
- Visa [utvecklarguiden](./developers-guide.md)
- [Säker åtkomst till ett nyckelvalv](./security-features.md)