---
title: Ge åtkomst till data med en hanterad identitet
titleSuffix: Azure Storage
description: Använd hanterade identiteter för Azure-resurser för att auktorisera blob-och Queue data-åtkomst från program som körs i virtuella Azure-datorer, funktions program och andra.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 552d2587f35ed391b470c6d5b1693b79fd57306b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879586"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Ge åtkomst till blob-och Queue-data med hanterade identiteter för Azure-resurser

Azure blob-och Queue Storage-stöd Azure Active Directory (Azure AD)-autentisering med [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md). Hanterade identiteter för Azure-resurser kan ge åtkomst till blob-och Queue-data med hjälp av Azure AD-autentiseringsuppgifter från program som körs i virtuella Azure-datorer, Function-appar, skalnings uppsättningar för virtuella datorer och andra tjänster. Genom att använda hanterade identiteter för Azure-resurser tillsammans med Azure AD-autentisering kan du undvika att lagra autentiseringsuppgifter med dina program som körs i molnet.  

Den här artikeln visar hur du auktoriserar åtkomst till BLOB-eller Queue-data från en virtuell Azure-dator med hanterade identiteter för Azure-resurser. Det beskriver också hur du testar din kod i utvecklings miljön.

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator

Innan du kan använda hanterade identiteter för Azure-resurser för att ge åtkomst till blobbar och köer från din virtuella dator måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager klient bibliotek](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Mer information om hanterade identiteter finns i [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library"></a>Autentisera med Azure Identity Library

Klient biblioteket för Azure Identity ger Azure Azure AD token Authentication-stöd för [Azure SDK](https://github.com/Azure/azure-sdk). De senaste versionerna av Azure Storage klient bibliotek för .NET, Java, python och Java Script integreras med Azure Identity Library för att tillhandahålla ett enkelt och säkert sätt att skaffa en OAuth 2,0-token för auktorisering av Azure Storage begär Anden.

En fördel med klient biblioteket för Azure Identity är att du kan använda samma kod för att autentisera om ditt program körs i utvecklings miljön eller i Azure. Klient biblioteket för Azure Identity för .NET autentiserar ett säkerhets objekt. När din kod körs i Azure är säkerhets objekt en hanterad identitet för Azure-resurser. I utvecklings miljön finns inte den hanterade identiteten, så klient biblioteket autentiserar antingen användaren eller tjänstens huvud namn i test syfte.

Efter autentiseringen får klient biblioteket för Azure Identity ett token-autentiseringsuppgifter. Den här token-autentiseringsuppgiften kapslas sedan i det tjänst klient objekt som du skapar för att utföra åtgärder mot Azure Storage. Biblioteket hanterar detta för dig sömlöst genom att hämta rätt token-autentiseringsuppgifter.

Mer information om klient biblioteket för Azure Identity för .NET finns i [klient biblioteket för Azure Identity för .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity). Referens dokumentation för klient biblioteket för Azure Identity finns i [namn området för Azure. Identity](/dotnet/api/azure.identity).

### <a name="assign-azure-roles-for-access-to-data"></a>Tilldela Azure-roller för åtkomst till data

När ett Azure AD-säkerhetsobjekt försöker komma åt BLOB-eller Queue data, måste säkerhets objektets behörigheter ha behörighet till resursen. Om säkerhetsobjektet är en hanterad identitet i Azure eller ett Azure AD-användarkonto som kör kod i utvecklings miljön, måste säkerhets objekt tilldelas en Azure-roll som ger åtkomst till BLOB-eller Queue-data i Azure Storage. Information om hur du tilldelar behörigheter via Azure RBAC finns i avsnittet **tilldela Azure-roller för åtkomst behörighet** i [auktorisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

> [!NOTE]
> När du skapar ett Azure Storage-konto tilldelas du inte automatiskt behörigheter för åtkomst till data via Azure AD. Du måste uttryckligen tilldela dig själv en Azure-roll för Azure Storage. Du kan tilldela den på nivån för din prenumeration, resurs grupp, lagrings konto eller behållare eller kö.
>
> Innan du tilldelar dig själv en roll för data åtkomst kommer du att kunna komma åt data i ditt lagrings konto via Azure Portal eftersom Azure Portal även kan använda konto nyckeln för data åtkomst. Mer information finns i [Välj hur du godkänner åtkomst till BLOB-data i Azure Portal](../blobs/authorize-data-operations-portal.md).

### <a name="authenticate-the-user-in-the-development-environment"></a>Autentisera användaren i utvecklings miljön

När din kod körs i utvecklings miljön kan autentiseringen hanteras automatiskt, eller så kan det krävas en webb läsar inloggning, beroende på vilka verktyg du använder. Microsoft Visual Studio stöder till exempel enkel inloggning (SSO), så att det aktiva Azure AD-användarkontot används automatiskt för autentisering. Mer information om SSO finns i [enkel inloggning till program](../../active-directory/manage-apps/what-is-single-sign-on.md).

Andra utvecklingsverktyg kan bli ombedd att logga in via en webbläsare.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Autentisera ett huvud namn för tjänsten i utvecklings miljön

Om utvecklings miljön inte stöder enkel inloggning eller inloggning via en webbläsare kan du använda ett huvud namn för tjänsten för att autentisera från utvecklings miljön.

#### <a name="create-the-service-principal"></a>Skapa huvudnamn för tjänsten

Om du vill skapa ett huvud namn för tjänsten med Azure CLI och tilldela en Azure-roll, anropar du kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) . Ange en Azure Storage data åtkomst roll som ska tilldelas det nya huvud namnet för tjänsten. Ange dessutom omfånget för roll tilldelningen. Mer information om de inbyggda roller som finns för Azure Storage finns i [inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

Om du inte har tillräcklig behörighet för att tilldela en roll till tjänstens huvud namn kan du behöva be kontots ägare eller administratör att utföra roll tilldelningen.

I följande exempel används Azure CLI för att skapa ett nytt huvud namn för tjänsten och tilldela rollen **Storage BLOB data Reader** till den med konto omfånget

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

`az ad sp create-for-rbac`Kommandot returnerar en lista över egenskaper för tjänstens huvud namn i JSON-format. Kopiera värdena så att du kan använda dem för att skapa de miljövariabler som krävs i nästa steg.

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
> Det kan ta några minuter att sprida Azures roll tilldelningar.

#### <a name="set-environment-variables"></a>Ange miljövariabler

Klient biblioteket för Azure Identity läser värden från tre miljövariabler vid körning för att autentisera tjänstens huvud namn. I följande tabell beskrivs det värde som ska anges för varje miljö variabel.

|Miljövariabel|Värde
|-|-
|`AZURE_CLIENT_ID`|App-ID för tjänstens huvud namn
|`AZURE_TENANT_ID`|Tjänstens huvud namn för Azure AD-klient
|`AZURE_CLIENT_SECRET`|Lösen ordet som genereras för tjänstens huvud namn

> [!IMPORTANT]
> När du har ställt in miljövariablerna stänger du och öppnar konsol fönstret igen. Om du använder Visual Studio eller en annan utvecklings miljö kan du behöva starta om utvecklings miljön för att den ska kunna registrera nya miljövariabler.

Mer information finns i [skapa identitet för Azure-appen i portalen](../../active-directory/develop/howto-create-service-principal-portal.md).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET-kod exempel: skapa en Block-Blob

Lägg till följande `using` direktiv i koden för att använda Azure-identiteten och Azure Storage klient biblioteken.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Skapa en instans av klassen [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) om du vill hämta en token-autentiseringsuppgifter som din kod kan använda för att auktorisera begär anden till Azure Storage. I följande kod exempel visas hur du hämtar autentiseringsuppgifter för autentiserade token och använder den för att skapa ett tjänst klient objekt. Använd sedan tjänst klienten för att ladda upp en ny BLOB:

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
> Om du vill auktorisera begär Anden mot BLOB-eller Queue data med Azure AD måste du använda HTTPS för dessa begär Anden.

## <a name="next-steps"></a>Nästa steg

- [Hantera åtkomst behörigheter till lagrings data med Azure RBAC](./storage-auth-aad-rbac-portal.md).
- [Använd Azure AD med lagrings program](storage-auth-aad-app.md).
- [Kör PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-data](../blobs/authorize-data-operations-powershell.md)
- [Självstudie: åtkomst till lagring från App Service med hanterade identiteter](../../app-service/scenario-secure-app-access-storage.md)