---
title: Självstudie – använda Azure Key Vault med en virtuell dator i .NET | Microsoft Docs
description: I den här självstudien konfigurerar du en virtuell dator ett ASP.NET Core-program för att läsa en hemlighet från ditt nyckel valv.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: a56c08e5bf6054d24af3ade571ec625969286a77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455652"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>Självstudie: använda Azure Key Vault med en virtuell dator i .NET

Azure Key Vault hjälper dig att skydda hemligheter, t. ex. API-nycklar, databas anslutnings strängarna som du behöver för att få åtkomst till dina program, tjänster och IT-resurser.

I den här självstudien får du lära dig hur du hämtar ett konsol program för att läsa information från Azure Key Vault. Programmet använder den hanterade identiteten för den virtuella datorn för att autentisera till Key Vault. 

Självstudien visar hur du:

> [!div class="checklist"]
> * Skapa en resursgrupp.
> * Skapa ett nyckelvalv.
> * Lägg till en hemlighet i nyckelvalvet.
> * Hämta en hemlighet från nyckelvalvet.
> * Skapa en virtuell dator i Azure.
> * Aktivera en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) för den virtuella datorn.
> * Tilldela behörigheter till den virtuella datorns identitet.

Läs [Key Vault grundläggande koncept](basic-concepts.md)innan du börjar. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Förutsättningar

För Windows, Mac och Linux:
  * [Git](https://git-scm.com/downloads)
  * [.Net Core 3,1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core/3.1).
  * [Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-resources-and-assign-permissions"></a>Skapa resurser och tilldela behörigheter

Innan du börjar koda måste du skapa vissa resurser, placera en hemlighet i nyckel valvet och tilldela behörigheter.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Skapa en resurs grupp och ett nyckel valv

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Fyll i nyckel valvet med en hemlighet

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Skapa en virtuell Windows-eller Linux-dator med någon av följande metoder:

| Windows | Linux |
|--|--|
| [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) | [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Azure-portalen](../../virtual-machines/windows/quick-create-portal.md) | [Azure-portalen](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>Tilldela en identitet till den virtuella datorn
Skapa en systemtilldelad identitet för den virtuella datorn med kommandot [AZ VM Identity Assign](/cli/azure/vm/identity#az-vm-identity-assign) :

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observera den systemtilldelade identiteten som visas i följande kod. Utdata från föregående kommando skulle vara: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Tilldela behörigheter till VM-identiteten
Tilldela de tidigare skapade identitets behörigheterna till ditt nyckel valv med kommandot [AZ-nyckel valv set-princip](/cli/azure/keyvault#az-keyvault-set-policy) :

```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>Logga in på den virtuella datorn

Om du vill logga in på den virtuella datorn följer du anvisningarna i [Anslut och logga in på en virtuell Azure Windows-dator](../../virtual-machines/windows/connect-logon.md) eller [ansluter och loggar in på en virtuell Azure Linux-dator](../../virtual-machines/linux/login-using-aad.md).

## <a name="set-up-the-console-app"></a>Konfigurera konsol programmet

Skapa en konsol app och installera de nödvändiga paketen med hjälp av `dotnet` kommandot.

### <a name="install-net-core"></a>Installera .NET Core

Om du vill installera .NET Core går du till sidan med [.net-nedladdningar](https://www.microsoft.com/net/download) .

### <a name="create-and-run-a-sample-net-app"></a>Skapa och kör en exempel-.NET-app

Öppna en kommandotolk.

Du kan skriva ut "Hello World" till konsolen genom att köra följande kommandon:

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>Installera paketet

I konsol fönstret installerar du klient biblioteket Azure Key Vault hemligheter för .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

I den här snabb starten måste du installera följande identitets paket för att autentisera till Azure Key Vault:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>Redigera konsol programmet

Öppna filen *program. cs* och Lägg till följande paket:

```csharp
using System;
using Azure.Core;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Lägg till dessa rader och uppdatera URI: n för att avspegla `vaultUri` ditt nyckel valv. I koden nedan används  ["DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential) för autentisering till Key Vault, som använder token från Programhanterad identitet för att autentisera. Den använder också exponentiell backoff för återförsök om nyckel valvet begränsas.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
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

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort den virtuella datorn och nyckel valvet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API för Azure Key Vault](/rest/api/keyvault/)
