---
title: Snabb start – Azure Key Vault python-klient bibliotek – hantera certifikat
description: Lär dig hur du skapar, hämtar och tar bort certifikat från ett Azure Key Vault med hjälp av python-klient biblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 2026b1721dbe05af1ecc52324d1dd0ebf2ec3d08
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802666"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>Snabb start: Azure Key Vault certifikat klient bibliotek för python

Kom igång med klient biblioteket för Azure Key Vault-certifikat för python. Följ stegen nedan för att installera paketet och prova exempel koden för grundläggande uppgifter. Genom att använda Key Vault för att lagra certifikat undviker du att lagra certifikat i din kod, vilket ökar säkerheten för din app.

[API-referens dokumentation](/python/api/overview/azure/keyvault-certificates-readme)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)  |  [Paket (python-paket index)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 + eller 3.6 +](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

Den här snabb starten förutsätter att du kör [Azure CLI](/cli/azure/install-azure-cli) i ett Linux-terminalfönster.

## <a name="set-up-your-local-environment"></a>Konfigurera din lokala miljö

Den här snabb starten använder Azure Identity Library med Azure CLI för att autentisera användare till Azure-tjänster. Utvecklare kan också använda Visual Studio eller Visual Studio Code för att autentisera sina anrop. mer information finns i [autentisera klienten med klient biblioteket för Azure Identity](/python/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Logga in på Azure

1. Kör kommandot `login`.

    ```azurecli-interactive
    az login
    ```

    Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida.

    Annars öppnar du en webb sida på [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och anger den auktoriseringskod som visas i din terminal.

2. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

### <a name="install-the-packages"></a>Installera paketen

1. I en terminal-eller kommando tolk skapar du en lämplig projektmapp och skapar och aktiverar sedan en virtuell python-miljö enligt beskrivningen i [använda python-virtuella miljöer](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Installera Azure Active Directory identitets bibliotek:

    ```terminal
    pip install azure.identity
    ```


1. Installera klient biblioteket för Key Vault certifikat:

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resurs grupp och ett nyckel valv

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckel valv

Skapa en åtkomst princip för nyckel valvet som ger certifikat behörighet till ditt användar konto

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create
```

#### <a name="set-environment-variables"></a>Ange miljövariabler

Det här programmet använder Key Vault-namnet som en miljö variabel som kallas `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS eller Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Skapa exempel koden

Med klient biblioteket för Azure Key Vault certifikat för python kan du hantera certifikat. Följande kod exempel visar hur du skapar en-klient, ställer in ett certifikat, hämtar ett certifikat och tar bort ett certifikat.

Skapa en fil med namnet *kv_certificates. py* som innehåller den här koden.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Kör koden

Kontrol lera att koden i föregående avsnitt finns i en fil med namnet *kv_certificates. py*. Kör sedan koden med följande kommando:

```terminal
python kv_certificates.py
```

- Om du får problem med behörigheten kontrollerar du att du körde [ `az keyvault set-policy` kommandot](#grant-access-to-your-key-vault).
- Att köra koden igen med samma nyckel namn kan orsaka felet, "(konflikt) certifikatet <name> är för närvarande i ett borttaget, men går inte att återställa." Använd ett annat nyckel namn.

## <a name="code-details"></a>Kod information

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabb starten används inloggad användare för att autentisera till nyckel valv, vilket är önskad metod för lokal utveckling. För program som distribueras till Azure ska hanterad identitet tilldelas App Service eller virtuell dator. mer information finns i [Översikt över hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

I det här exemplet expanderas namnet på nyckel valvet till Key Vault-URI: n i formatet "https:// \<your-key-vault-name\> . Vault.Azure.net". Det här exemplet använder klassen  ["DefaultAzureCredential ()"](/python/api/azure-identity/azure.identity.defaultazurecredential) , som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identitet. Mer information finns i [Azure Credential Authentication](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Spara ett certifikat

När du har fått klient objekt för nyckel valvet kan du skapa ett certifikat med hjälp av metoden [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-) : 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Här kräver certifikatet en princip som erhållits med metoden [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--) .

Anropa en `begin_create_certificate` metod genererar ett asynkront anrop till Azure-REST API för nyckel valvet. Det asynkrona anropet returnerar ett avsöknings objekt. Anropa avsöknings metoden för att vänta på resultatet av åtgärden `result` .

Vid hantering av begäran autentiserar Azure anroparens identitet (tjänstens huvud namn) med hjälp av det Credential-objekt som du angav för klienten.


### <a name="retrieve-a-certificate"></a>Hämta ett certifikat

Om du vill läsa ett certifikat från Key Vault använder du metoden [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-) :

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Du kan också kontrol lera att certifikatet har angetts med Azure CLI-kommandot [AZ för certifikat visning](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show).

### <a name="delete-a-certificate"></a>Ta bort ett certifikat

Om du vill ta bort ett certifikat använder du metoden [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-) :

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

`begin_delete_certificate`Metoden är asynkron och returnerar ett avsöknings objekt. Att anropa avsöknings `result` metoden väntar på att slutföras.

Du kan kontrol lera att certifikatet har tagits bort med Azure CLI-kommandot [AZ för certifikat visning](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show).

När du har tagit bort ett certifikat finns det kvar i ett borttaget, men ett återställnings Bart tillstånd för en tid. Om du kör koden igen ska du använda ett annat certifikat namn.

## <a name="clean-up-resources"></a>Rensa resurser

Om du också vill experimentera med [hemligheter](../secrets/quick-create-python.md) och [nycklar](../keys/quick-create-python.md)kan du återanvända Key Vault som skapats i den här artikeln.

Annars, när du är klar med resurserna som du skapade i den här artikeln, använder du följande kommando för att ta bort resurs gruppen och alla resurser som finns i den:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Key Vault](../general/overview.md)
- [Säker åtkomst till ett nyckelvalv](../general/secure-your-key-vault.md)
- [Guide för Azure Key Vault utvecklare](../general/developers-guide.md)
- [Översikt över Key Vault säkerhet](../general/security-overview.md)
- [Autentisera med Key Vault](../general/authentication.md)
