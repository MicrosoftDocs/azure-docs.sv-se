---
title: Auktorisera åtkomst till data med en hanterad identitet
titleSuffix: Azure Storage
description: Använd hanterade identiteter för Azure-resurser för att auktorisera blob- och ködataåtkomst från program som körs på virtuella Azure-datorer, funktionsappar med mera.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 80df7b85ec1ad9e273081f9a6a96b9a9d7ec8cd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791201"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Auktorisera åtkomst till blob- och ködata med hanterade identiteter för Azure-resurser

Azure Blob och Queue Storage stöder Azure Active Directory -autentisering (Azure AD) med [hanterade identiteter för Azure-resurser.](../../active-directory/managed-identities-azure-resources/overview.md) Hanterade identiteter för Azure-resurser kan ge åtkomst till blob- och ködata med hjälp av Azure AD-autentiseringsuppgifter från program som körs på virtuella Azure-datorer (VM), funktionsappar, VM-skalningsuppsättningar och andra tjänster. Genom att använda hanterade identiteter för Azure-resurser tillsammans med Azure AD-autentisering kan du undvika att lagra autentiseringsuppgifter med dina program som körs i molnet.  

Den här artikeln visar hur du ger åtkomst till blob- eller ködata från en virtuell Azure-dator med hjälp av hanterade identiteter för Azure-resurser. Den beskriver också hur du testar din kod i utvecklingsmiljön.

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator

Innan du kan använda hanterade identiteter för Azure-resurser för att ge åtkomst till blobar och köer från den virtuella datorn måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager klientbibliotek](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Mer information om hanterade identiteter finns i [Hanterade identiteter för Azure-resurser.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="authenticate-with-the-azure-identity-library"></a>Autentisera med Azure Identity-biblioteket

Azure Identity-klientbiblioteket har stöd för Azure AD-tokenautentisering för [Azure SDK.](https://github.com/Azure/azure-sdk) De senaste versionerna av Azure Storage-klientbiblioteken för .NET, Java, Python och JavaScript integreras med Azure Identity-biblioteket för att tillhandahålla ett enkelt och säkert sätt att hämta en OAuth 2.0-token för auktorisering av Azure Storage-begäranden.

En fördel med Azure Identity-klientbiblioteket är att du kan använda samma kod för att autentisera om programmet körs i utvecklingsmiljön eller i Azure. Azure Identity-klientbiblioteket för .NET autentiserar ett säkerhetsobjekt. När koden körs i Azure är säkerhetsobjekt en hanterad identitet för Azure-resurser. I utvecklingsmiljön finns inte den hanterade identiteten, så klientbiblioteket autentiserar antingen användaren eller tjänstens huvudnamn i testsyfte.

Efter autentiseringen får Azure Identity-klientbiblioteket en token-autentiseringstoken. Den här token-autentiseringstoken kapslas sedan in i tjänstklientobjektet som du skapar för att utföra åtgärder mot Azure Storage. Biblioteket hanterar detta sömlöst genom att hämta lämpliga token-autentiseringsuppgifter.

Mer information om Azure Identity-klientbiblioteket för .NET finns i [Azure Identity-klientbiblioteket för .NET.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) Referensdokumentation för Azure Identity-klientbiblioteket finns i [Azure.Identity Namespace](/dotnet/api/azure.identity).

### <a name="assign-azure-roles-for-access-to-data"></a>Tilldela Azure-roller för åtkomst till data

När ett Azure AD-säkerhetsobjekt försöker komma åt blob- eller ködata måste säkerhetsobjekt ha behörighet till resursen. Oavsett om säkerhetsobjekt är en hanterad identitet i Azure eller ett Azure AD-användarkonto som kör kod i utvecklingsmiljön måste säkerhetsobjekt tilldelas en Azure-roll som ger åtkomst till blob- eller ködata i Azure Storage. Information om hur du tilldelar behörigheter via Azure RBAC finns i avsnittet Tilldela **Azure-roller** för åtkomsträttigheter i Auktorisera åtkomst till [Azure-blobar](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)och köer med hjälp av Azure Active Directory .

> [!NOTE]
> När du skapar ett Azure Storage-konto tilldelas du inte automatiskt behörighet att komma åt data via Azure AD. Du måste uttryckligen tilldela dig själv en Azure-roll för Azure Storage. Du kan tilldela den på nivån för din prenumeration, resursgrupp, lagringskonto eller container eller kö.
>
> Innan du tilldelar dig själv en roll för dataåtkomst kommer du att kunna komma åt data i ditt lagringskonto via Azure Portal eftersom Azure Portal också kan använda kontonyckeln för dataåtkomst. Mer information finns i [Välj hur du vill tillåta åtkomst till blobdata i Azure Portal](../blobs/authorize-data-operations-portal.md).

### <a name="authenticate-the-user-in-the-development-environment"></a>Autentisera användaren i utvecklingsmiljön

När koden körs i utvecklingsmiljön kan autentiseringen hanteras automatiskt eller kräva en webbläsarinloggning, beroende på vilka verktyg du använder. Microsoft Visual Studio till exempel stöd för enkel inloggning (SSO) så att det aktiva Azure AD-användarkontot används automatiskt för autentisering. Mer information om enkel inloggning finns [i Enkel inloggning till program.](../../active-directory/manage-apps/what-is-single-sign-on.md)

Andra utvecklingsverktyg kan uppmana dig att logga in via en webbläsare.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Autentisera ett huvudnamn för tjänsten i utvecklingsmiljön

Om utvecklingsmiljön inte stöder enkel inloggning eller inloggning via en webbläsare kan du använda tjänstens huvudnamn för att autentisera från utvecklingsmiljön.

#### <a name="create-the-service-principal"></a>Skapa huvudnamn för tjänsten

Om du vill skapa ett huvudnamn för tjänsten med Azure CLI och tilldela en Azure-roll anropar du kommandot [az ad sp create-for-rbac.](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) Ange en Azure Storage dataåtkomstroll som ska tilldelas till det nya tjänstens huvudnamn. Ange även omfånget för rolltilldelningen. Mer information om de inbyggda rollerna som finns för Azure Storage finns i [Inbyggda Roller i Azure.](../../role-based-access-control/built-in-roles.md)

Om du inte har tillräcklig behörighet för att tilldela en roll till tjänstens huvudnamn kan du behöva be kontoägaren eller administratören att utföra rolltilldelningen.

I följande exempel används Azure CLI för att skapa ett nytt huvudnamn för tjänsten och tilldela rollen **Storage Blob Data Reader** till den med kontoomfång

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Kommandot `az ad sp create-for-rbac` returnerar en lista över egenskaper för tjänstens huvudnamn i JSON-format. Kopiera dessa värden så att du kan använda dem för att skapa de nödvändiga miljövariablerna i nästa steg.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Det kan ta några minuter att sprida Rolltilldelningar i Azure.

#### <a name="set-environment-variables"></a>Ange miljövariabler

Azure Identity-klientbiblioteket läser värden från tre miljövariabler vid körning för att autentisera tjänstens huvudnamn. I följande tabell beskrivs värdet som ska anges för varje miljövariabel.

|Miljövariabel|Värde
|-|-
|`AZURE_CLIENT_ID`|App-ID för tjänstens huvudnamn
|`AZURE_TENANT_ID`|Azure AD-klientorganisations-ID:t för tjänstens huvudnamn
|`AZURE_CLIENT_SECRET`|Lösenordet som genererades för tjänstens huvudnamn

> [!IMPORTANT]
> När du har angett miljövariablerna stänger du och öppnar konsolfönstret igen. Om du använder Visual Studio eller en annan utvecklingsmiljö kan du behöva starta om utvecklingsmiljön för att den ska kunna registrera de nya miljövariablerna.

Mer information finns i Skapa [identitet för Azure-appen i portalen.](../../active-directory/develop/howto-create-service-principal-portal.md)

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET-kodexempel: Skapa en blockblob

Lägg till följande `using` -direktiv i koden för att använda Azure Identity Azure Storage klientbibliotek.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Skapa en instans av [klassen DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) för att få en token-autentiseringsbehörighet som din kod kan använda för att auktorisera begäranden till Azure Storage att skapa en instans av klassen DefaultAzureCredential. Följande kodexempel visar hur du hämtar autentiseringsuppgifter för autentiserad token och använder den för att skapa ett tjänstklientobjekt och sedan använder tjänstklienten för att ladda upp en ny blob:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
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
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Om du vill auktorisera begäranden mot blob- eller ködata med Azure AD måste du använda HTTPS för dessa begäranden.

## <a name="next-steps"></a>Nästa steg

- [Hantera åtkomstbehörigheter till lagringsdata med Azure RBAC](./storage-auth-aad-rbac-portal.md).
- [Använd Azure AD med lagringsprogram](storage-auth-aad-app.md).
- [Kör PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att få åtkomst till blobdata](../blobs/authorize-data-operations-powershell.md)
- [Självstudie: Få åtkomst till lagring App Service med hjälp av hanterade identiteter](../../app-service/scenario-secure-app-access-storage.md)
