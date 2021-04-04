---
title: Självstudie – webb program åtkomst till lagring med hjälp av hanterade identiteter | Azure
description: I den här självstudien får du lära dig hur du kommer åt Azure Storage för en app med hjälp av hanterade identiteter.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 72b1d4fe864c23c0ac065e47d96ab0c78866defa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435849"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>Självstudie: åtkomst Azure Storage från en webbapp

Lär dig att komma åt Azure Storage för en webbapp (inte en inloggad användare) som körs på Azure App Service genom att använda hanterade identiteter.

:::image type="content" alt-text="Diagram som visar hur du kommer åt lagringen." source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

Du vill lägga till åtkomst till Azure-dataplanen (Azure Storage, Azure SQL Database, Azure Key Vault eller andra tjänster) från din webbapp. Du kan använda en delad nyckel, men du måste bekymra dig om drifts säkerhet för vem som kan skapa, distribuera och hantera hemligheten. Det är också möjligt att nyckeln kan kontrol leras i GitHub, vilka hackare vet hur de ska genomsökas. Ett säkrare sätt att ge din webbapp åtkomst till data är att använda [hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md).

Med en hanterad identitet från Azure Active Directory (Azure AD) kan App Service komma åt resurser via rollbaserad åtkomst kontroll (RBAC), utan att kräva autentiseringsuppgifter för appen. När du har tilldelat en hanterad identitet till din webbapp tar Azure hand om skapandet och distributionen av ett certifikat. Människor behöver inte oroa sig för att hantera hemligheter eller autentiseringsuppgifter för appen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa en systemtilldelad hanterad identitet i en webbapp.
> * Skapa ett lagrings konto och en Azure Blob Storage-behållare.
> * Åtkomst till lagring från en webbapp med hjälp av hanterade identiteter.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Ett webb program som körs på Azure App Service som har [modulen App Service autentisering/auktorisering aktive rad](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-an-app"></a>Aktivera hanterad identitet i en app

Om du skapar och publicerar din webbapp via Visual Studio har den hanterade identiteten Aktiver ATS i din app. I App Service väljer du **identitet** i den vänstra rutan och väljer sedan **tilldelat system**. Kontrol lera att **statusen** har angetts till **på**. Om inte väljer du **Spara** och väljer sedan **Ja** för att aktivera den hanterade identiteten som tilldelats av systemet. När den hanterade identiteten är aktive rad anges status till **på** och objekt-ID: t är tillgängligt.

:::image type="content" alt-text="Skärm bild som visar alternativet system tilldelad identitet." source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

Det här steget skapar ett nytt objekt-ID, ett annat än det app-ID som skapades i fönstret **autentisering/auktorisering** . Kopiera objekt-ID: t för den systemtilldelade hanterade identiteten. Du behöver det senare.

## <a name="create-a-storage-account-and-blob-storage-container"></a>Skapa ett lagrings konto och Blob Storage container

Nu är du redo att skapa ett lagrings konto och Blob Storage behållare.

Varje lagringskonto måste tillhöra en Azure-resursgrupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp. Den här artikeln visar hur du skapar en ny resurs grupp.

Ett v2-lagringskonto för generell användning ger åtkomst till alla Azure Storage-tjänster: blobar, filer, köer, tabeller och diskar. De steg som beskrivs här skapar ett allmänt-syfte v2-lagrings konto, men stegen för att skapa en typ av lagrings konto liknar varandra.

Blobbar i Azure Storage ordnas i behållare. Innan du kan ladda upp en BLOB senare i den här självstudien måste du först skapa en behållare.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill skapa ett allmänt-syfte v2-lagrings konto i Azure Portal.

1. Välj **Alla tjänster** på menyn i Azure-portalen. Ange **lagrings konton** i listan över resurser. När du börjar skriva filtreras listan baserat på det du skriver. Välj **lagrings konton**.

1. I fönstret **lagrings konton** som visas väljer du **Lägg till**.

1. Välj den prenumeration där du vill skapa lagringskontot.

1. Under fältet **resurs grupp** väljer du den resurs grupp som innehåller din webbapp från den nedrullningsbara menyn.

1. Ange sedan ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också vara mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.

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

Följ dessa steg om du vill skapa en Blob Storage behållare i Azure Storage.

1. Gå till det nya lagrings kontot i Azure Portal.

1. I den vänstra menyn för lagrings kontot, bläddrar du till avsnittet **BLOB service** och väljer sedan **behållare**.

1. Välj knappen **+ Container**.

1. Ange ett namn för den nya containern. Containernamnet får bara innehålla gemener, måste börja med en bokstav eller siffra och får bara innehålla bokstäver, siffror och bindestreck (-).

1. Ställ in nivån för allmän åtkomst till containern. Standardnivån är **Privat (ingen anonym åtkomst)**.

1. Klicka på **OK** för att skapa containern.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill skapa ett allmänt-syfte v2-lagrings konto och Blob Storage behållare kör du följande skript. Ange namnet på den resurs grupp som innehåller din webbapp. Ange ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också vara mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.

Ange platsen för ditt lagrings konto. Om du vill se en lista över platser som är giltiga för din prenumeration kör du ```Get-AzLocation | select Location``` . Containernamnet får bara innehålla gemener, måste börja med en bokstav eller siffra och får bara innehålla bokstäver, siffror och bindestreck (-).

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden.

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

Om du vill skapa ett allmänt-syfte v2-lagrings konto och Blob Storage behållare kör du följande skript. Ange namnet på den resurs grupp som innehåller din webbapp. Ange ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också vara mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener. 

Ange platsen för ditt lagrings konto. Containernamnet får bara innehålla gemener, måste börja med en bokstav eller siffra och får bara innehålla bokstäver, siffror och bindestreck (-).

I följande exempel används ditt Azure AD-konto för att auktorisera åtgärden att skapa behållaren. Innan du skapar behållaren ska du tilldela rollen Storage BLOB data Contributor till dig själv. Även om du är kontots ägare behöver du explicita behörigheter för att utföra data åtgärder mot lagrings kontot.

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden.

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

## <a name="grant-access-to-the-storage-account"></a>Bevilja åtkomst till lagrings kontot

Du måste ge din webbapp åtkomst till lagrings kontot innan du kan skapa, läsa eller ta bort blobbar. I ett föregående steg konfigurerade du webb programmet som körs på App Service med en hanterad identitet. Med Azure RBAC kan du ge hanterad identitets åtkomst till en annan resurs, precis som alla säkerhets objekt. Rollen Storage BLOB data Contributor ger webbappen (som representeras av den systemtilldelade hanterade identiteten) Läs-, skriv-och borttagnings åtkomst till BLOB-behållaren och data.

# <a name="portal"></a>[Portal](#tab/azure-portal)

I [Azure Portal](https://portal.azure.com)går du till ditt lagrings konto för att ge åtkomst till ditt webb program. Välj **åtkomst kontroll (IAM)** i den vänstra rutan och välj **roll tilldelningar**. Du ser en lista över vem som har åtkomst till lagrings kontot. Nu vill du lägga till en roll tilldelning i en robot, den app-tjänst som behöver åtkomst till lagrings kontot. Välj **Lägg till**  >  **Lägg till roll tilldelning**.

I **roll** väljer du **Storage BLOB data Contributor** för att ge din webbapp åtkomst till att läsa Storage-blobbar. I **tilldela åtkomst till** väljer du **App Service**. I **prenumeration** väljer du din prenumeration. Välj sedan den app service som du vill ge åtkomst till. Välj **Spara**.

:::image type="content" alt-text="Skärm bild som visar skärmen Lägg till roll tilldelning." source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

Din webbapp har nu åtkomst till ditt lagrings konto.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Kör följande skript för att tilldela din webbapp (som representeras av en systemtilldelad hanterad identitet) rollen Storage BLOB data Contributor på ditt lagrings konto.

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kör följande skript för att tilldela din webbapp (som representeras av en systemtilldelad hanterad identitet) rollen Storage BLOB data Contributor på ditt lagrings konto.

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Åtkomst Blob Storage (.NET)

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) -klassen används för att hämta en token-autentiseringsuppgifter för din kod för att godkänna begär anden till Azure Storage. Skapa en instans av klassen [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) , som använder den hanterade identiteten för att hämta tokens och bifoga dem till tjänst klienten. I följande kod exempel hämtas autentiseringsuppgifter för autentiserad token och används för att skapa ett tjänst klient objekt, som laddar upp en ny blob.

Om du vill se den här koden som en del av ett exempel program, se [exemplet på GitHub](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/1-WebApp-storage-managed-identity).

### <a name="install-client-library-packages"></a>Installera klient biblioteks paket

Installera [Blob Storage NuGet-paketet](https://www.nuget.org/packages/Azure.Storage.Blobs/) för att arbeta med Blob Storage och [Azure Identity client-biblioteket för .net NuGet-paketet](https://www.nuget.org/packages/Azure.Identity/) för att autentisera med Azure AD-autentiseringsuppgifter. Installera klient biblioteken med hjälp av kommando rads gränssnittet .NET Core eller Package Manager-konsolen i Visual Studio.

# <a name="command-line"></a>[Kommandorad](#tab/command-line)

Öppna en kommando rad och växla till den katalog som innehåller projekt filen.

Kör installations kommandona.

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[Paket hanterare](#tab/package-manager)

Öppna projektet eller lösningen i Visual Studio och öppna konsolen med hjälp av **verktyg**  >  **NuGet Package Manager**  >  **Package Manager Console** .

Kör installations kommandona.
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

Om du är klar med den här själv studie kursen och inte längre behöver webbappen eller tillhör ande resurser [rensar du de resurser som du har skapat](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Skapa en systemtilldelad hanterad identitet.
> * Skapa ett lagrings konto och Blob Storage behållare.
> * Åtkomst till lagring från en webbapp med hjälp av hanterade identiteter.

> [!div class="nextstepaction"]
> [App Service åtkomst till Microsoft Graph för användarens räkning](scenario-secure-app-access-microsoft-graph-as-user.md)