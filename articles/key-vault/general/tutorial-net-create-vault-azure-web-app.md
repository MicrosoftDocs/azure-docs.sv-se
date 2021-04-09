---
title: Självstudie – använda Azure Key Vault med en Azure-webbapp i .NET
description: I den här självstudien konfigurerar du en Azure-webbapp i ett ASP.NET Core program för att läsa en hemlighet från ditt nyckel valv.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 2960726cf687908e8e4aed9333fce490dd7ff006
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788746"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>Självstudie: Använd en hanterad identitet för att ansluta Key Vault till en Azure-webbapp i .NET

[Azure Key Vault](./overview.md) är ett sätt att lagra autentiseringsuppgifter och andra hemligheter med ökad säkerhet. Men din kod måste autentiseras för att Key Vault ska kunna hämta dem. [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md) hjälper till att lösa det här problemet genom att ge Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva Visa autentiseringsuppgifter i din kod.

I den här självstudien får du skapa och Distribuera Azure-webbprogram till [Azure App Service](../../app-service/overview.md). Du använder en hanterad identitet för att autentisera din Azure-webbapp med ett Azure Key Vault med hjälp av [Azure Key Vault hemliga klient biblioteket för .net](/dotnet/api/overview/azure/key-vault) och [Azure CLI](/cli/azure/get-started-with-azure-cli). Samma grundläggande principer gäller när du använder det utvecklings språk du väljer, Azure PowerShell och/eller Azure Portal.

Mer information om Azure App tjänst webb program och distribution som presenteras i den här självstudien finns i:
- [Översikt över App Service](../../app-service/overview.md)
- [Skapa en webbASP.NET Core-webbapp i Azure App Service](../../app-service/quickstart-dotnetcore.md)
- [Lokal Git-distribution till Azure App Service](../../app-service/deploy-local-git.md)

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. [Skapa ett kostnads fritt.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.Net Core 3,1 SDK (eller senare)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* En [git](https://www.git-scm.com/downloads) -installation.
* [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/).
* [Azure Key Vault.](./overview.md) Du kan skapa ett nyckel valv med hjälp av [Azure Portal](quick-create-portal.md), [Azure CLI](quick-create-cli.md)eller [Azure PowerShell](quick-create-powershell.md).
* En Key Vault [hemlighet](../secrets/about-secrets.md). Du kan skapa en hemlighet med hjälp av [Azure Portal](../secrets/quick-create-portal.md), [POWERSHELL](../secrets/quick-create-powershell.md)eller [Azure CLI](../secrets/quick-create-cli.md).

Om du redan har distribuerat ditt webb program i Azure App Service kan du gå vidare till [Konfigurera webb program åtkomst till ett nyckel valv](#create-and-assign-a-managed-identity) och [ändra webb program kod](#modify-the-app-to-access-your-key-vault) avsnitt.

## <a name="create-a-net-core-app"></a>Skapa en .NET Core-app
I det här steget ska du konfigurera det lokala .NET Core-projektet.

I ett terminalfönster på datorn skapar du en katalog med namnet `akvwebapp` och gör den till den aktuella katalogen:

```bash
mkdir akvwebapp
cd akvwebapp
```

Skapa en .NET Core-app med hjälp av det [nya dotNet-webb](/dotnet/core/tools/dotnet-new) kommandot:

```bash
dotnet new web
```

Kör programmet lokalt så att du vet hur det ska se ut när du distribuerar det till Azure:

```bash
dotnet run
```

I en webbläsare går du till appen på `http://localhost:5000` .

Du ser då meddelandet ”Hello World!” meddelande från den exempel app som visas på sidan.

Mer information om hur du skapar webb program för Azure finns [i skapa ett ASP.net Core-webbprogram i Azure App Service](../../app-service/quickstart-dotnetcore.md)

## <a name="deploy-the-app-to-azure"></a>distribuera appen till Azure

I det här steget ska du distribuera ditt .NET Core-program till Azure App Service med hjälp av lokal git. Mer information om hur du skapar och distribuerar program finns i [skapa ett ASP.net Core-webbprogram i Azure](../../app-service/quickstart-dotnetcore.md).

### <a name="configure-the-local-git-deployment"></a>Konfigurera lokal Git-distribution

I terminalfönstret väljer du **CTRL + C** för att stänga webb servern.  Initiera en git-lagringsplats för .NET Core-projektet:

```bash
git init
git add .
git commit -m "first commit"
```

Du kan använda FTP och lokal git för att distribuera en Azure-webbapp med hjälp av en *distributions användare*. När du har konfigurerat din distributions användare kan du använda den för alla dina Azure-distributioner. Ditt användar namn och lösen ord för distribution på konto nivå skiljer sig från dina autentiseringsuppgifter för Azure-prenumerationen. 

Konfigurera distributions användaren genom att köra kommandot [AZ webapp Deployment User set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) . Välj ett användar namn och lösen ord som följer dessa rikt linjer: 

- Användarnamnet måste vara unikt inom Azure. För lokala git-push-meddelanden får den inte innehålla snabel-a-symbolen (@). 
- Lösen ordet måste innehålla minst åtta tecken och innehålla två av följande tre element: bokstäver, siffror och symboler. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

JSON-utdata visar lösen ordet som `null` . Ändra användar namnet om du får ett `'Conflict'. Details: 409` fel meddelande. Om du ser felet `'Bad Request'. Details: 400` ska du använda ett starkare lösenord. 

Registrera ditt användar namn och lösen ord så att du kan använda det för att distribuera dina webb program.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resurs grupp är en logisk behållare dit du distribuerar Azure-resurser och hanterar dem. Skapa en resurs grupp som innehåller både ditt nyckel valv och din webbapp med hjälp av kommandot [AZ Group Create](/cli/azure/group?#az-group-create) :

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

Skapa en [App Service plan](../../app-service/overview-hosting-plans.md) med hjälp av kommandot Azure CLI [AZ AppService plan Create](/cli/azure/appservice/plan) . I följande exempel skapas ett App Service plan med namnet `myAppServicePlan` på `FREE` pris nivån:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

När App Service plan skapas visar Azure CLI information som liknar vad du ser här:

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
> Precis som ett nyckel valv måste en Azure-webbapp ha ett unikt namn. Ersätt `<your-webapp-name>` med namnet på din webbapp i följande exempel.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

När webbappen skapas visar Azure CLI utdata som liknar det du ser här:

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


URL: en för git-fjärrdatabasen visas i `deploymentLocalGitUrl` -egenskapen i formatet `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` . Spara den här URL: en. Du behöver det senare.

Gå till den nya appen med hjälp av följande kommando. Ersätt `<your-webapp-name>` med namnet på appen.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Du ser standard webb sidan för en ny Azure-webbapp.

### <a name="deploy-your-local-app"></a>Distribuera din lokala app

I det lokala terminalfönstret kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. I följande kommando ersätter `<deploymentLocalGitUrl-from-create-step>` du med URL: en för den git-fjärrdatabas som du sparade i avsnittet [skapa en webbapp](#create-a-web-app) .

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Använd följande kommando för att skicka till Azure-fjärrservern för att distribuera din app. När git Credential Manager uppmanas att ange autentiseringsuppgifter använder du de autentiseringsuppgifter som du skapade i avsnittet [Konfigurera lokal Git-distribution](#configure-the-local-git-deployment) .

```bash
git push azure main
```

Det kan ta några minuter att köra det här kommandot. När den körs visas information som liknar det du ser här:
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

Du ser då meddelandet ”Hello World!” meddelande som du såg tidigare när du besökte `http://localhost:5000` .

Mer information om hur du distribuerar webb program med git finns i [lokal Git-distribution till Azure App Service](../../app-service/deploy-local-git.md)
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>Konfigurera webb programmet för att ansluta till Key Vault

I det här avsnittet ska du konfigurera webb åtkomst till Key Vault och uppdatera din program kod för att hämta en hemlighet från Key Vault.

### <a name="create-and-assign-a-managed-identity"></a>Skapa och tilldela en hanterad identitet

I den här självstudien använder vi [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) för att autentisera till Key Vault. Hanterad identitet hanterar automatiskt programautentiseringsuppgifter.

Skapa identiteten för programmet i Azure CLI genom att köra kommandot [AZ webapp-Identity Assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) :

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Kommandot returnerar följande JSON-kodfragment:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Om du vill ge ditt webb program behörighet att utföra **Get** -och **list** -åtgärder i ditt nyckel valv skickar `principalId` du till Azure CLI [-AZ för att ange princip](/cli/azure/keyvault?#az-keyvault-set-policy) kommando:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

Du kan också tilldela åtkomst principer med hjälp av [Azure Portal](./assign-access-policy-portal.md) eller [PowerShell](./assign-access-policy-powershell.md).

### <a name="modify-the-app-to-access-your-key-vault"></a>Ändra appen för åtkomst till ditt nyckel valv

I den här självstudien använder du [Azure Key Vault hemliga klient biblioteket](/dotnet/api/overview/azure/security.keyvault.secrets-readme) i demonstrations syfte. Du kan också använda [Azure Key Vault klient bibliotek för certifikat](/dotnet/api/overview/azure/security.keyvault.certificates-readme)eller [Azure Key Vault nyckel klient bibliotek](/dotnet/api/overview/azure/security.keyvault.keys-readme).

#### <a name="install-the-packages"></a>Installera paketen

I terminalfönstret installerar du Azure Key Vault hemliga klient biblioteket för .NET-och Azure Identity-klientens biblioteks paket:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>Uppdatera koden

Leta upp och öppna filen startup. cs i ditt akvwebapp-projekt. 

Lägg till följande rader i rubriken:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Lägg till följande rader före `app.UseEndpoints` anropet och uppdatera URI: n för att avspegla `vaultUri` ditt nyckel valv. I den här koden används  [DefaultAzureCredential ()](/dotnet/api/azure.identity.defaultazurecredential) för att autentisera till Key Vault, som använder en token från hanterad identitet för att autentisera. Mer information om att autentisera till Key Vault finns i [Developer ' s guide](./developers-guide.md#authenticate-to-key-vault-in-code). Koden använder också exponentiell backoff för återförsök om Key Vault begränsas. Mer information om Key Vault transaktions gränser finns i avsnittet [Azure Key Vault begränsnings vägledning](./overview-throttling.md).

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

Uppdatera raden `await context.Response.WriteAsync("Hello World!");` så att den ser ut som den här raden:

```csharp
await context.Response.WriteAsync(secretValue);
```

Se till att spara ändringarna innan du fortsätter till nästa steg.

#### <a name="redeploy-your-web-app"></a>Distribuera din webbapp igen

Nu när du har uppdaterat din kod kan du distribuera om den till Azure med hjälp av dessa git-kommandon:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>Gå till din slutförda webbapp

```bash
http://<your-webapp-name>.azurewebsites.net
```

Där innan du såg "Hello World!" bör du nu se värdet för din hemlighet som visas.

## <a name="next-steps"></a>Nästa steg

- [Använda Azure Key Vault med program som distribueras till en virtuell dator i .NET](./tutorial-net-virtual-machine.md)
- Lär dig mer om [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md)
- Visa [Guide för utvecklare](./developers-guide.md)
- [Säker åtkomst till ett nyckelvalv](./secure-your-key-vault.md)