---
title: Självstudie – Använda Azure Key Vault med en virtuell dator i .NET-| Microsoft Docs
description: I den här självstudien konfigurerar du en virtuell dator ASP.NET ett kärnprogram för att läsa en hemlighet från ditt nyckelvalv.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: c08d0c210e992cba5bca2695fda0bcf08c4689dc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772099"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>Självstudie: Använda Azure Key Vault med en virtuell dator i .NET

Azure Key Vault hjälper dig att skydda hemligheter, till exempel API-nycklar, databasanslutningssträngar som du behöver för att komma åt dina program, tjänster och IT-resurser.

I den här självstudien får du lära dig hur du får ett konsolprogram att läsa information från Azure Key Vault. Programmet använder en hanterad identitet för virtuella datorer för att autentisera Key Vault. 

Självstudien visar hur du:

> [!div class="checklist"]
> * Skapa en resursgrupp.
> * Skapa ett nyckelvalv.
> * Lägg till en hemlighet i nyckelvalvet.
> * Hämta en hemlighet från nyckelvalvet.
> * Skapa en virtuell dator i Azure.
> * Aktivera en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) för den virtuella datorn.
> * Tilldela behörigheter till den virtuella datorns identitet.

Innan du börjar bör du [läsa Key Vault grundläggande begreppen](basic-concepts.md). 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Förutsättningar

För Windows, Mac och Linux:
  * [Git](https://git-scm.com/downloads)
  * [.NET Core 3.1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core/3.1).
  * [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="create-resources-and-assign-permissions"></a>Skapa resurser och tilldela behörigheter

Innan du börjar koda måste du skapa några resurser, placera en hemlighet i nyckelvalvet och tilldela behörigheter.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure med följande kommando:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az login
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Connect-AzAccount
```
---

## <a name="create-a-resource-group-and-key-vault"></a>Skapa en resursgrupp och ett nyckelvalv

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Fylla i ditt nyckelvalv med en hemlighet

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Skapa en virtuell Windows- eller Linux-dator med någon av följande metoder:

| Windows | Linux |
|--|--|
| [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) | [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Azure-portalen](../../virtual-machines/windows/quick-create-portal.md) | [Azure-portalen](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>Tilldela en identitet till den virtuella datorn
Skapa en system tilldelad identitet för den virtuella datorn med följande exempel:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observera den system tilldelade identiteten som visas i följande kod. Utdata från föregående kommando är:

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$vm = Get-AzVM -Name <NameOfYourVirtualMachine>
Update-AzVM -ResourceGroupName <YourResourceGroupName> -VM $vm -IdentityType SystemAssigned
```

Observera PrincipalId som visas i följande kod. Utdata från föregående kommando är: 


```output
PrincipalId          TenantId             Type             UserAssignedIdentities
-----------          --------             ----             ----------------------
xxxxxxxx-xx-xxxxxx   xxxxxxxx-xxxx-xxxx   SystemAssigned
```
---

## <a name="assign-permissions-to-the-vm-identity"></a>Tilldela behörigheter till den virtuella datorns identitet
Tilldela de tidigare skapade identitetsbehörigheterna till nyckelvalvet med [kommandot az keyvault set-policy:](/cli/azure/keyvault#az_keyvault_set_policy)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions  get list set delete
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -ResourceGroupName <YourResourceGroupName> -VaultName '<your-unique-key-vault-name>' -ObjectId '<VMSystemAssignedIdentity>' -PermissionsToSecrets  get,list,set,delete
```
---

## <a name="sign-in-to-the-virtual-machine"></a>Logga in på den virtuella datorn

Om du vill logga in på den virtuella datorn följer du anvisningarna i Ansluta och logga in på en virtuell [Azure Windows-dator](../../virtual-machines/windows/connect-logon.md) eller Anslut och logga in på en virtuell [Azure Linux-dator.](../../virtual-machines/linux/login-using-aad.md)

## <a name="set-up-the-console-app"></a>Konfigurera konsolappen

Skapa en konsolapp och installera de nödvändiga paketen med hjälp av `dotnet` kommandot .

### <a name="install-net-core"></a>Installera .NET Core

Om du vill installera .NET Core går du till [sidan för .NET-nedladdningar.](https://www.microsoft.com/net/download)

### <a name="create-and-run-a-sample-net-app"></a>Skapa och köra en .NET-exempelapp

Öppna en kommandotolk.

Du kan skriva ut Hello World till konsolen genom att köra följande kommandon:

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>Installera paketet

Från konsolfönstret installerar du klientbiblioteket Azure Key Vault Secrets för .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

För den här snabbstarten måste du installera följande identitetspaket för att autentisera till Azure Key Vault:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>Redigera konsolappen

Öppna filen *Program.cs* och lägg till följande paket:

```csharp
using System;
using Azure.Core;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Lägg till dessa rader och uppdatera URI:en så att den `vaultUri` återspeglar för ditt nyckelvalv. Koden nedan använder  ["DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential) för autentisering till nyckelvalvet, som använder token från program hanterad identitet för att autentisera. Den använder också exponentiell backoff för återförsök om nyckelvalvet begränsas.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";
            string keyVaultName = "<your-key-vault-name>";
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

Ta bort den virtuella datorn och nyckelvalvet när de inte längre behövs.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API för Azure Key Vault](/rest/api/keyvault/)
