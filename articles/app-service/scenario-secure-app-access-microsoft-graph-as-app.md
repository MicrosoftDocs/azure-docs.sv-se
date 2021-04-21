---
title: Självstudie – Webbappen får Microsoft Graph som app| Azure
description: I den här självstudien lär du dig att komma åt data i Microsoft Graph med hjälp av hanterade identiteter.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1, devx-track-azurepowershell
ms.openlocfilehash: 5bb52799836b1975de9d936e04fb53987effb300
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832634"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>Självstudie: Microsoft Graph åtkomst från en skyddad app som app

Lär dig hur du kommer Microsoft Graph från en webbapp som körs på Azure App Service.

:::image type="content" alt-text="Diagram som visar åtkomst till Microsoft Graph." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Du vill anropa Microsoft Graph för webbappen. Ett säkert sätt att ge webbappen åtkomst till data är att använda en [system tilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md). En hanterad identitet Azure Active Directory kan App Service åtkomst till resurser via rollbaserad åtkomstkontroll (RBAC), utan att appautentiseringsuppgifter krävs. När du har tilldelar en hanterad identitet till din webbapp tar Azure hand om skapandet och distributionen av ett certifikat. Du behöver inte bekymra dig om att hantera hemligheter eller autentiseringsuppgifter för appar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa en system tilldelad hanterad identitet på en webbapp.
> * Lägg Microsoft Graph API-behörigheter till en hanterad identitet.
> * Anropa Microsoft Graph från en webbapp med hjälp av hanterade identiteter.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Ett webbprogram som körs på Azure App Service som har App Service [autentiserings-/auktoriseringsmodulen aktiverad.](scenario-secure-app-authentication-app-service.md)

## <a name="enable-managed-identity-on-app"></a>Aktivera hanterad identitet i appen

Om du skapar och publicerar webbappen via Visual Studio aktiverades den hanterade identiteten i din app åt dig. I apptjänsten väljer du **Identitet i** den vänstra rutan och sedan System **assigned (System tilldelad).** Kontrollera att **Status** är inställt på **På.** Om inte väljer du **Spara och** sedan Ja för **att aktivera** den system tilldelade hanterade identiteten. När den hanterade identiteten är aktiverad anges statusen till **På** och objekt-ID:t är tillgängligt.

Anteckna värdet för **Objekt-ID,** som du behöver i nästa steg.

:::image type="content" alt-text="Skärmbild som visar den system tilldelade identiteten." source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Bevilja åtkomst till Microsoft Graph

Vid åtkomst till Microsoft Graph måste den hanterade identiteten ha rätt behörigheter för den åtgärd som den vill utföra. För närvarande finns det inget alternativ för att tilldela sådana behörigheter via Azure Portal. Följande skript lägger till de begärda API Microsoft Graph behörigheterna för den hanterade identiteten för tjänstens huvudnamn.

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

När skriptet har utförts kan du kontrollera i Azure Portal [att](https://portal.azure.com) de begärda API-behörigheterna har tilldelats till den hanterade identiteten.

Gå till **Azure Active Directory** och välj sedan **Företagsprogram**. Det här fönstret visar alla tjänstens huvudnamn i din klientorganisation. I **Alla program** väljer du tjänstens huvudnamn för den hanterade identiteten. 

Om du följer den här självstudien finns det två tjänsthuvudnamn med samma visningsnamn (till exempel SecureWebApp2020094113531). Tjänstens huvudnamn som har en **url för** startsidan representerar webbappen i din klientorganisation. Tjänstens huvudnamn utan **URL:en för** startsidan representerar den system tilldelade hanterade identiteten för din webbapp. **Objekt-ID-värdet** för den hanterade identiteten matchar objekt-ID:t för den hanterade identitet som du skapade tidigare.

Välj tjänstens huvudnamn för den hanterade identiteten.

:::image type="content" alt-text="Skärmbild som visar alternativet Alla program." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

I **Översikt** väljer **du Behörigheter** så ser du de tillagda behörigheterna för Microsoft Graph.

:::image type="content" alt-text="Skärmbild som visar fönstret Behörigheter." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>Anropa Microsoft Graph (.NET)

Klassen [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) används för att hämta en token-autentiseringsbehörighet för din kod för att auktorisera begäranden till Microsoft Graph. Skapa en instans av [klassen DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) som använder den hanterade identiteten för att hämta token och koppla dem till tjänstklienten. I följande kodexempel hämtar autentiseringssuppgifter för autentiserad token och använder den för att skapa ett klientobjekt för tjänsten, som hämtar användarna i gruppen.

Om du vill se den här koden som en del av ett exempelprogram kan du [se exemplet på GitHub.](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/3-WebApp-graphapi-managed-identity)

### <a name="install-the-microsoftidentitywebmicrosoftgraph-client-library-package"></a>Installera klientbibliotekspaketet Microsoft.Identity.Web.MicrosoftGraph

Installera [NuGet-paketet Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) i projektet med hjälp av .NET Core-kommandoradsgränssnittet eller Package Manager-konsolen i Visual Studio.

# <a name="command-line"></a>[Kommandorad](#tab/command-line)

Öppna en kommandorad och växla till den katalog som innehåller projektfilen.

Kör installationskommandona.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph
```

# <a name="package-manager"></a>[Package Manager](#tab/package-manager)

Öppna projektet/lösningen i Visual Studio och öppna konsolen med hjälp av **kommandot**  >  **Tools NuGet Package Manager** Package Manager  >  **Console.**

Kör installationskommandona.
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

Om du är klar med den här självstudien och inte längre behöver webbappen eller associerade resurser [rensar du de resurser som du skapade.](scenario-secure-app-clean-up-resources.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Skapa en system tilldelad hanterad identitet på en webbapp.
> * Lägg Microsoft Graph API-behörigheter till en hanterad identitet.
> * Anropa Microsoft Graph från en webbapp med hjälp av hanterade identiteter.

Lär dig hur du ansluter [en .NET Core-app,](tutorial-dotnetcore-sqldb-app.md) [Python-app,](tutorial-python-postgresql-app.md) [Java-app](tutorial-java-spring-cosmosdb.md) [Node.js en app](tutorial-nodejs-mongodb-app.md) till en databas.
