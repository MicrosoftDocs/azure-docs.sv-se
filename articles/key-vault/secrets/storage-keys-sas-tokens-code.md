---
title: Hämta token för signaturer för delad åtkomst i kod | Azure Key Vault
description: Funktionen hanterat lagrings konto ger en sömlös integrering mellan Azure Key Vault och ett Azure Storage-konto.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 6530434e36f7c9a9a60a9782bcf2dce7ba447dab
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584921"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Hämta token för signaturer för delad åtkomst i kod

Du kan hantera ditt lagrings konto med SAS-token (signatur för delad åtkomst) som lagras i ditt nyckel valv. Mer information finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SAS](../../storage/common/storage-sas-overview.md).

Den här artikeln innehåller exempel på .NET-kod som hämtar en SAS-token och utför åtgärder med den. Information om hur du skapar och lagrar SAS-token finns i [Hantera lagrings konto nycklar med Key Vault och Azure CLI](overview-storage-keys.md) eller [Hantera lagrings konto nycklar med Key Vault och Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Kodexempel

I det här exemplet hämtar koden en SAS-token från ditt nyckel valv, använder den för att skapa ett nytt lagrings konto och skapar en ny Blob Service-klient.

```cs
// The shared access signature is stored as a secret in keyvault. 
// After you get a security token, create a new SecretClient with vault credentials and the key vault URI.
// The format for the key vault URI (kvuri) is https://<YourKeyVaultName>.vault.azure.net

var kv = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

// Now retrive your storage SAS token from Key Vault using the name of the secret (secretName).

KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;

// Create new storage credentials using the SAS token.
StorageCredentials accountSAS = new StorageCredentials(sasToken);

// Use these credentials and your storage account name to create a Blob service client.
CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();
```

Om signaturens token för delad åtkomst håller på att gå ut kan du hämta signatur-token för delad åtkomst från ditt nyckel valv och uppdatera koden.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [ger begränsad åtkomst till Azure Storage-resurser med hjälp av SAS](../../storage/common/storage-sas-overview.md).
- Lär dig hur du [hanterar lagrings konto nycklar med Key Vault och Azure CLI](overview-storage-keys.md) eller [Azure PowerShell](overview-storage-keys-powershell.md).
- Se [nyckel exempel för hanterade lagrings konton](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
