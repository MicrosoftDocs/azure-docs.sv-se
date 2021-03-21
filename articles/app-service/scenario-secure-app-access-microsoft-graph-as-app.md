---
title: Självstudie – webbappen får åtkomst till Microsoft Graph som appen | Azure
description: I den här självstudien får du lära dig hur du kommer åt data i Microsoft Graph med hjälp av hanterade identiteter.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 06837ab0f4685787f8d2615e81d0405fdb8ec711
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99062567"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>Självstudie: åtkomst Microsoft Graph från en säker app som appen

Lär dig hur du kommer åt Microsoft Graph från en webbapp som körs på Azure App Service.

:::image type="content" alt-text="Diagram som visar åtkomst Microsoft Graph." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Du vill anropa Microsoft Graph för webb programmet. Ett säkert sätt att ge din webbapp åtkomst till data är att använda en [systemtilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md). En hanterad identitet från Azure Active Directory ger App Service åtkomst till resurser via RBAC (rollbaserad åtkomst kontroll), utan att kräva autentiseringsuppgifter för appen. När du har tilldelat en hanterad identitet till din webbapp tar Azure hand om skapandet och distributionen av ett certifikat. Du behöver inte bekymra dig om att hantera autentiseringsuppgifter för hemligheter eller appar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa en systemtilldelad hanterad identitet i en webbapp.
> * Lägg till Microsoft Graph API-behörigheter till en hanterad identitet.
> * Anropa Microsoft Graph från en webbapp med hjälp av hanterade identiteter.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Ett webb program som körs på Azure App Service som har [modulen App Service autentisering/auktorisering aktive rad](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-app"></a>Aktivera hanterad identitet i appen

Om du skapar och publicerar din webbapp via Visual Studio har den hanterade identiteten Aktiver ATS i din app. I App Service väljer du **identitet** i den vänstra rutan och sedan **tilldelat system**. Kontrol lera att **status** har angetts till **på**. Om inte väljer du **Spara** och väljer sedan **Ja** för att aktivera den hanterade identiteten som tilldelats av systemet. När den hanterade identiteten är aktive rad anges status till **på** och objekt-ID: t är tillgängligt.

Anteckna värdet för **objekt-ID** , som du behöver i nästa steg.

:::image type="content" alt-text="Skärm bild som visar den systemtilldelade identiteten." source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Bevilja åtkomst till Microsoft Graph

Vid åtkomst till Microsoft Graph måste den hanterade identiteten ha rätt behörigheter för den åtgärd som den vill utföra. För närvarande finns det inget alternativ för att tilldela sådana behörigheter via Azure Portal. Följande skript lägger till de begärda Microsoft Graph API-behörigheterna för det hanterade identitets tjänstens huvud objekt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD.

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$TenantID="<tenant-id>"
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp-20201102125811"

# Get the ID of the managed identity for the web app.
$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid

# Check the Microsoft Graph documentation for the permission you need for the operation.
$PermissionName = "User.Read.All"

Connect-AzureAD -TenantId $TenantID

# Get the service principal for Microsoft Graph.
# First result should be AppId 00000003-0000-0000-c000-000000000000
$GraphServicePrincipal = Get-AzureADServicePrincipal -SearchString "Microsoft Graph" | Select-Object -first 1

# Assign permissions to the managed identity service principal.
$AppRole = $GraphServicePrincipal.AppRoles | `
Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $spID -PrincipalId $spID `
-ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az login

webAppName="SecureWebApp-20201106120003"

spId=$(az resource list -n $webAppName --query [*].identity.principalId --out tsv)

graphResourceId=$(az ad sp list --display-name "Microsoft Graph" --query [0].objectId --out tsv)

appRoleId=$(az ad sp list --display-name "Microsoft Graph" --query "[0].appRoles[?value=='User.Read.All' && contains(allowedMemberTypes, 'Application')].id" --output tsv)

uri=https://graph.microsoft.com/v1.0/servicePrincipals/$spId/appRoleAssignments

body="{'principalId':'$spId','resourceId':'$graphResourceId','appRoleId':'$appRoleId'}"

az rest --method post --uri $uri --body $body --headers "Content-Type=application/json"
```

---

När skriptet har körts kan du kontrol lera i [Azure Portal](https://portal.azure.com) att de begärda API-behörigheterna har tilldelats till den hanterade identiteten.

Gå till **Azure Active Directory** och välj **företags program**. I det här fönstret visas alla tjänst huvud namn i din klient organisation. I **alla program** väljer du tjänstens huvud namn för den hanterade identiteten. 

Om du följer den här självstudien finns det två tjänst huvud namn med samma visnings namn (till exempel SecureWebApp2020094113531). Tjänstens huvud namn som har en **Start sidas-URL** representerar webbappen i din klient organisation. Tjänstens huvud namn utan **Start sidans URL** representerar den systemtilldelade hanterade identiteten för din webbapp. **Objekt-ID-** värdet för den hanterade identiteten matchar objekt-ID: t för den hanterade identitet som du skapade tidigare.

Välj tjänstens huvud namn för den hanterade identiteten.

:::image type="content" alt-text="Skärm bild som visar alternativet alla program." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

I **Översikt** väljer du **behörigheter** och du ser de tillagda behörigheterna för Microsoft Graph.

:::image type="content" alt-text="Skärm bild som visar fönstret behörigheter." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>Anrops Microsoft Graph (.NET)

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) -klassen används för att hämta en token-autentiseringsuppgifter för din kod för att godkänna begär anden till Microsoft Graph. Skapa en instans av klassen [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) , som använder den hanterade identiteten för att hämta tokens och bifoga dem till tjänst klienten. I följande kod exempel hämtas autentiseringsuppgifter för autentiserad token och används för att skapa ett tjänst klient objekt, som hämtar användarna i gruppen.

Om du vill se den här koden som en del av ett exempel program, se [exemplet på GitHub](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/3-WebApp-graphapi-managed-identity).

### <a name="install-the-microsoftidentitywebmicrosoftgraph-client-library-package"></a>Installera klient biblioteks paketet Microsoft. Identity. Web. MicrosoftGraph

Installera [Microsoft. Identity. Web. MicrosoftGraph NuGet-paketet](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) i ditt projekt med hjälp av kommando rads gränssnittet .net Core eller Package Manager-konsolen i Visual Studio.

# <a name="command-line"></a>[Kommandorad](#tab/command-line)

Öppna en kommando rad och växla till den katalog som innehåller projekt filen.

Kör installations kommandona.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph
```

# <a name="package-manager"></a>[Paket hanterare](#tab/package-manager)

Öppna konsolen projekt/lösning i Visual Studio och öppna konsolen med hjälp av **verktyg**  >  **NuGet Package Manager**  >  **Package Manager Console** .

Kör installations kommandona.
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph
```

---

### <a name="example"></a>Exempel

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Azure.Identity;
using Microsoft.Graph.Core;
using System.Net.Http.Headers;

...

public IList<MSGraphUser> Users { get; set; }

public async Task OnGetAsync()
{
    // Create the Microsoft Graph service client with a DefaultAzureCredential class, which gets an access token by using the available Managed Identity.
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
        new Azure.Core.TokenRequestContext(
            new[] { "https://graph.microsoft.com/.default" }));

    var accessToken = token.Token;
    var graphServiceClient = new GraphServiceClient(
        new DelegateAuthenticationProvider((requestMessage) =>
        {
            requestMessage
            .Headers
            .Authorization = new AuthenticationHeaderValue("bearer", accessToken);

            return Task.CompletedTask;
        }));

    List<MSGraphUser> msGraphUsers = new List<MSGraphUser>();
    try
    {
        var users =await graphServiceClient.Users.Request().GetAsync();
        foreach(var u in users)
        {
            MSGraphUser user = new MSGraphUser();
            user.userPrincipalName = u.UserPrincipalName;
            user.displayName = u.DisplayName;
            user.mail = u.Mail;
            user.jobTitle = u.JobTitle;

            msGraphUsers.Add(user);
        }
    }
    catch(Exception ex)
    {
        string msg = ex.Message;
    }

    Users = msGraphUsers;
}
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med den här själv studie kursen och inte längre behöver webbappen eller tillhör ande resurser [rensar du de resurser som du har skapat](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Skapa en systemtilldelad hanterad identitet i en webbapp.
> * Lägg till Microsoft Graph API-behörigheter till en hanterad identitet.
> * Anropa Microsoft Graph från en webbapp med hjälp av hanterade identiteter.

Lär dig hur du ansluter en [.net Core-app](tutorial-dotnetcore-sqldb-app.md), [python-app](tutorial-python-postgresql-app.md), [java-app](tutorial-java-spring-cosmosdb.md)eller [Node.js app](tutorial-nodejs-mongodb-app.md) till en databas.