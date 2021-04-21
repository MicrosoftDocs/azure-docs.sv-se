---
title: Självstudie – Webbappen får åtkomst till lagring med hjälp av hanterade identiteter | Azure
description: I den här självstudien lär du dig att komma Azure Storage för en app med hjälp av hanterade identiteter.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a37b189ae98332b2d6c557b6bdfad98266002e9e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833912"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>Självstudie: Azure Storage från en webbapp

Lär dig hur du Azure Storage för en webbapp (inte en inloggad användare) som körs på Azure App Service med hjälp av hanterade identiteter.

:::image type="content" alt-text="Diagram som visar hur du kommer åt lagring." source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

Du vill lägga till åtkomst till Azure-dataplanet (Azure Storage, Azure SQL Database, Azure Key Vault eller andra tjänster) från webbappen. Du kan använda en delad nyckel, men sedan måste du bekymra dig om driftssäkerhet för vem som kan skapa, distribuera och hantera hemligheten. Det är också möjligt att nyckeln kan checkas in på GitHub, som hackare kan söka efter. Ett säkrare sätt att ge webbappen åtkomst till data är att använda [hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md).

En hanterad identitet från Azure Active Directory (Azure AD) gör att App Service kan komma åt resurser via rollbaserad åtkomstkontroll (RBAC), utan att appautentiseringsuppgifter krävs. När du har tilldelar en hanterad identitet till din webbapp tar Azure hand om skapandet och distributionen av ett certifikat. Personer behöver inte bekymra sig om att hantera hemligheter eller autentiseringsuppgifter för appar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa en system tilldelad hanterad identitet på en webbapp.
> * Skapa ett lagringskonto och en Azure Blob Storage container.
> * Få åtkomst till lagring från en webbapp med hjälp av hanterade identiteter.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Ett webbprogram som körs på Azure App Service som har [App Service autentiserings-/auktoriseringsmodulen aktiverad.](scenario-secure-app-authentication-app-service.md)

## <a name="enable-managed-identity-on-an-app"></a>Aktivera hanterad identitet i en app

Om du skapar och publicerar webbappen via Visual Studio har den hanterade identiteten aktiverats på din app åt dig. I apptjänsten väljer du **Identitet i** den vänstra rutan och sedan **System tilldelad**. Kontrollera att **Status är** inställt på **På**. Om inte väljer du **Spara och** sedan Ja för **att aktivera** den system tilldelade hanterade identiteten. När den hanterade identiteten är aktiverad anges statusen till **På** och objekt-ID:t är tillgängligt.

:::image type="content" alt-text="Skärmbild som visar alternativet System tilldelad identitet." source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

Det här steget skapar ett nytt objekt-ID som skiljer sig från app-ID:t som skapades i **fönstret Autentisering/auktorisering.** Kopiera objekt-ID:t för den system tilldelade hanterade identiteten. Du behöver det senare.

## <a name="create-a-storage-account-and-blob-storage-container"></a>Skapa ett lagringskonto och en Blob Storage container

Nu är du redo att skapa ett lagringskonto och Blob Storage container.

Varje lagringskonto måste tillhöra en Azure-resursgrupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp. Den här artikeln visar hur du skapar en ny resursgrupp.

Ett v2-lagringskonto för generell användning ger åtkomst till alla Azure Storage-tjänster: blobar, filer, köer, tabeller och diskar. Stegen som beskrivs här skapar ett v2-lagringskonto för generell användning, men stegen för att skapa alla typer av lagringskonto liknar varandra.

Blobar i Azure Storage är ordnade i containrar. Innan du kan ladda upp en blob senare i den här självstudien måste du först skapa en container.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill skapa ett v2-lagringskonto Azure Portal generell användning i lagringskontot.

1. Välj **Alla tjänster** på menyn i Azure-portalen. I listan över resurser anger du **Lagringskonton**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Lagringskonton.**

1. I fönstret **Lagringskonton** som visas väljer du Lägg **till**.

1. Välj den prenumeration där du vill skapa lagringskontot.

1. Under fältet **Resursgrupp** väljer du den resursgrupp som innehåller webbappen från den nedrullningsna menyn.

1. Ange sedan ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.

1. Välj en plats för ditt lagringskonto eller använd standardplatsen.

1. Lämna dessa fält med respektive standardvärde:

    |Fält|Värde|
    |--|--|
    |Distributionsmodell|Resource Manager|
    |Prestanda|Standard|
    |Typ av konto|StorageV2 (generell användning v2)|
    |Replikering|Geo-redundant lagring med läsbehörighet (RA-GRS)|
    |Åtkomstnivå|Frekvent|

1. Välj **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.

1. Välj **Skapa**.

Följ dessa steg om Blob Storage skapa en Azure Storage i en container.

1. Gå till ditt nya lagringskonto i Azure Portal.

1. I den vänstra menyn för lagringskontot bläddrar du till **avsnittet Blob Service** och väljer sedan **Containrar.**

1. Välj knappen **+ Container**.

1. Ange ett namn för den nya containern. Containernamnet får bara innehålla gemener, måste börja med en bokstav eller siffra och får bara innehålla bokstäver, siffror och bindestreck (-).

1. Ställ in nivån för allmän åtkomst till containern. Standardnivån är **Privat (ingen anonym åtkomst)**.

1. Klicka på **OK** för att skapa containern.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Kör följande skript för att skapa ett v2-lagringskonto Blob Storage generell användning. Ange namnet på den resursgrupp som innehåller webbappen. Ange ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.

Ange platsen för ditt lagringskonto. Om du vill se en lista över platser som är giltiga för din prenumeration kör du ```Get-AzLocation | select Location``` . Containernamnet får bara innehålla gemener, måste börja med en bokstav eller siffra och får bara innehålla bokstäver, siffror och bindestreck (-).

Kom ihåg att ersätta platshållarvärden inom vinkelparenteser med dina egna värden.

```powershell
Connect-AzAccount

$resourceGroup = "securewebappresourcegroup"
$location = "<location>"
$storageName="securewebappstorage"
$containerName = "securewebappblobcontainer"

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kör följande skript för att skapa ett v2-lagringskonto Blob Storage generell användning. Ange namnet på den resursgrupp som innehåller webbappen. Ange ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. 

Ange platsen för ditt lagringskonto. Containernamnet får bara innehålla gemener, måste börja med en bokstav eller siffra och får bara innehålla bokstäver, siffror och bindestreck (-).

I följande exempel används ditt Azure AD-konto för att auktorisera åtgärden för att skapa containern. Innan du skapar containern tilldelar du rollen Storage Blob Data-deltagare till dig själv. Även om du är kontoägare behöver du explicit behörighet att utföra dataåtgärder mot lagringskontot.

Kom ihåg att ersätta platshållarvärden inom vinkelparenteser med dina egna värden.

```azurecli-interactive
az login

az storage account create \
    --name securewebappstorage \
    --resource-group securewebappresourcegroup \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope $storageId

az storage container create \
    --account-name securewebappstorage \
    --name securewebappblobcontainer \
    --auth-mode login
```

---

## <a name="grant-access-to-the-storage-account"></a>Bevilja åtkomst till lagringskontot

Du måste ge webbappen åtkomst till lagringskontot innan du kan skapa, läsa eller ta bort blobar. I ett tidigare steg konfigurerade du webbappen som körs på App Service med en hanterad identitet. Med Azure RBAC kan du ge den hanterade identiteten åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Rollen Storage Blob Data-deltagare ger webbappen (representeras av den system tilldelade hanterade identiteten) läs-, skriv- och borttagningsåtkomst till blobcontainern och data.

# <a name="portal"></a>[Portal](#tab/azure-portal)

I Azure Portal [du](https://portal.azure.com)till ditt lagringskonto för att ge webbappen åtkomst. Välj **Åtkomstkontroll (IAM)** i den vänstra rutan och välj sedan **Rolltilldelningar.** Du ser en lista över vem som har åtkomst till lagringskontot. Nu vill du lägga till en rolltilldelning till en robot, den apptjänst som behöver åtkomst till lagringskontot. Välj **Lägg till** lägg till  >  **rolltilldelning.**

I **Roll** väljer du **Storage Blob Data-deltagare för** att ge webbappen åtkomst till att läsa lagringsblobar. I **Tilldela åtkomst till** väljer du **App Service**. I **Prenumeration** väljer du din prenumeration. Välj sedan den apptjänst som du vill ge åtkomst till. Välj **Spara**.

:::image type="content" alt-text="Skärmbild som visar skärmen Lägg till rolltilldelning." source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

Webbappen har nu åtkomst till ditt lagringskonto.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Kör följande skript för att tilldela webbappen (representeras av en system tilldelad hanterad identitet) rollen Storage Blob Data-deltagare på ditt lagringskonto.

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kör följande skript för att tilldela webbappen (representeras av en system tilldelad hanterad identitet) rollen Storage Blob Data-deltagare på ditt lagringskonto.

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Åtkomst Blob Storage (.NET)

Klassen [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) används för att hämta en token-autentiseringsbehörighet för din kod för att auktorisera begäranden till Azure Storage. Skapa en instans av [klassen DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) som använder den hanterade identiteten för att hämta token och koppla dem till tjänstklienten. Följande kodexempel hämtar autentiseringsnamnet för autentiserad token och använder den för att skapa ett klientobjekt för tjänsten som laddar upp en ny blob.

Om du vill se den här koden som en del av ett exempelprogram kan du [se exemplet på GitHub.](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/1-WebApp-storage-managed-identity)

### <a name="install-client-library-packages"></a>Installera klientbibliotekspaket

Installera [nuget Blob Storage-paketet](https://www.nuget.org/packages/Azure.Storage.Blobs/) så att det fungerar med Blob Storage och Azure Identity-klientbiblioteket för [.NET NuGet-paketet](https://www.nuget.org/packages/Azure.Identity/) för autentisering med Azure AD-autentiseringsuppgifter. Installera klientbiblioteken med hjälp av kommandoradsgränssnittet för .NET Core eller Package Manager-konsolen i Visual Studio.

# <a name="command-line"></a>[Kommandorad](#tab/command-line)

Öppna en kommandorad och växla till den katalog som innehåller projektfilen.

Kör installationskommandona.

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[Package Manager](#tab/package-manager)

Öppna projektet eller lösningen i Visual Studio och öppna -konsolen med hjälp av **kommandot**  >  **Tools NuGet Package Manager** Package Manager  >  **Console** .

Kör installationskommandona.
```powershell
Install-Package Azure.Storage.Blobs

Install-Package Azure.Identity
```

---

### <a name="example"></a>Exempel

```csharp
using System;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;
using System.IO;
using Azure.Identity;

// Some code omitted for brevity.

static public async Task UploadBlob(string accountName, string containerName, string blobName, string blobContents)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (Exception e)
    {
        throw e;
    }
}
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med den här självstudien och inte längre behöver webbappen eller associerade resurser [rensar du de resurser som du skapade.](scenario-secure-app-clean-up-resources.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Skapa en system tilldelad hanterad identitet.
> * Skapa ett lagringskonto och Blob Storage container.
> * Få åtkomst till lagring från en webbapp med hjälp av hanterade identiteter.

> [!div class="nextstepaction"]
> [App Service åtkomst Microsoft Graph åt användaren](scenario-secure-app-access-microsoft-graph-as-user.md)
