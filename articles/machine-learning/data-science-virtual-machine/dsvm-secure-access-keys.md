---
title: Lagra autentiseringsuppgifter på ett säkert sätt
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig att lagra autentiseringsuppgifter på ett säkert sätt på Data Science Virtual Machine. Du lär dig hur du använder hanterade tjänst identiteter och Azure Key Vault för att lagra autentiseringsuppgifter för åtkomst.
keywords: djup inlärning, AI, data vetenskaps verktyg, virtuell data vetenskaps dator, Geospatial analys, team data vetenskaps process
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: adc66fc794ac6cce3fc0f8fd1552f701a4968860
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519685"
---
# <a name="store-access-credentials-securely-on-an-azure-data-science-virtual-machine"></a>Lagra autentiseringsuppgifter på ett säkert sätt på en Azure-Data Science Virtual Machine

Det är vanligt att koden i moln program innehåller autentiseringsuppgifter för autentisering till moln tjänster. Hur du hanterar och skyddar dessa autentiseringsuppgifter är en välkänd utmaning i att skapa moln program. Vi rekommenderar att autentiseringsuppgifterna aldrig visas på Developer-arbetsstationer eller är incheckade i käll kontrollen.

Funktionen [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md) gör det enklare att lösa det här problemet genom att ge Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan att behöva ha några autentiseringsuppgifter i koden.

Ett sätt att skydda autentiseringsuppgifter är att använda Windows Installer (MSI) i kombination med [Azure Key Vault](../../key-vault/index.yml), en hanterad Azure-tjänst för att lagra hemligheter och kryptografiska nycklar på ett säkert sätt. Du kan komma åt ett nyckel valv med hjälp av den hanterade identiteten och sedan hämta de auktoriserade hemligheterna och krypterings nycklarna från nyckel valvet.

Dokumentationen om hanterade identiteter för Azure-resurser och Key Vault omfattar en omfattande resurs för detaljerad information om dessa tjänster. Resten av den här artikeln vägleder dig genom den grundläggande användningen av MSI och Key Vault på Data Science Virtual Machine (DSVM) för att få åtkomst till Azure-resurser. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Skapa en hanterad identitet på DSVM

```azurecli-interactive
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```

## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>Tilldela Key Vault åtkomst behörighet till en VM-huvudobjekt

```azurecli-interactive
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>Få åtkomst till en hemlighet i nyckel valvet från DSVM

```bash
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>Åtkomst till lagrings nycklar från DSVM

```bash
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```

## <a name="access-the-key-vault-from-python"></a>Komma åt nyckel valvet från python

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
    credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
    key_vault_uri,  # Your key vault URL.
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Komma åt nyckel valvet från Azure CLI

```azurecli-interactive
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```