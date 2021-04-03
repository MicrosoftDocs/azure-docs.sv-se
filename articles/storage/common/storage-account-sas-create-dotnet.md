---
title: Skapa ett konto säkerhets associationer med .NET
titleSuffix: Azure Storage
description: Lär dig hur du skapar en signatur för delad åtkomst för kontot (SAS) med hjälp av .NET-klient biblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/12/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 2918b845430a6fc6dc59eca7041c114fc9d06515
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97092218"
---
# <a name="create-an-account-sas-with-net"></a>Skapa ett konto säkerhets associationer med .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Den här artikeln visar hur du använder lagrings konto nyckeln för att skapa en konto säkerhets Association med [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage).

## <a name="create-an-account-sas"></a>Skapa en konto-SAS

### <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

En konto säkerhets Association är signerad med konto åtkomst nyckeln. Använd [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) -klassen för att skapa de autentiseringsuppgifter som används för att signera SAS. Skapa sedan ett nytt [AccountSasBuilder](/dotnet/api/azure.storage.sas.accountsasbuilder) -objekt och anropa [ToSasQueryParameters](/dotnet/api/azure.storage.sas.accountsasbuilder.tosasqueryparameters) för att hämta SAS-token-strängen.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetAccountSASToken":::

### <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

Om du vill skapa en konto säkerhets Association för en behållare anropar du metoden [CloudStorageAccount. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) .

I följande kod exempel skapas en konto säkerhets Association som är giltig för blob-och fil tjänsterna och ger klienten behörigheterna läsa, skriva och lista för att få åtkomst till API: er på tjänst nivå. Kontots SAS begränsar protokollet till HTTPS, så begäran måste göras med HTTPS. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

---

## <a name="use-an-account-sas-from-a-client"></a>Använda en konto säkerhets Association från en klient

Om du vill använda kontots SAS för att få åtkomst till API: er för den Blob Service skapar du ett Blob Service klient objekt med SAS-och Blob Storage-slutpunkten för ditt lagrings konto.

### <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_UseAccountSAS":::

### <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

I det här kodfragmentet ersätter du `<storage-account>` plats hållaren med namnet på ditt lagrings konto.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

---

## <a name="next-steps"></a>Nästa steg

- [Bevilja begränsad åtkomst till Azure Storage resurser med signaturer för delad åtkomst (SAS)](storage-sas-overview.md)
- [Skapa en konto-SAS](/rest/api/storageservices/create-account-sas)
