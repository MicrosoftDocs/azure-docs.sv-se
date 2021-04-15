---
title: Snabbstart – Azure Key Vault Python-klientbibliotek – hantera certifikat
description: Lär dig hur du skapar, hämtar och tar bort certifikat från ett Azure-nyckelvalv med hjälp av Python-klientbiblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b8f36d7490e24247f1f8482c9dece86b68015d73
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376111"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>Snabbstart: Azure Key Vault ett certifikatklientbibliotek för Python

Kom igång med Azure Key Vault-certifikatklientbiblioteket för Python. Följ stegen nedan för att installera paketet och prova exempelkoden för grundläggande uppgifter. Genom att Key Vault lagra certifikat undviker du att lagra certifikat i koden, vilket ökar säkerheten för din app.

[API-referensdokumentation](/python/api/overview/azure/keyvault-certificates-readme)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)  |  [Paket (Python-paketindex)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration [– skapa en utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python 2.7+ eller 3.6+](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

Den här snabbstarten förutsätter att du [kör Azure CLI](/cli/azure/install-azure-cli) i ett Linux-terminalfönster.

## <a name="set-up-your-local-environment"></a>Konfigurera din lokala miljö

Den här snabbstarten använder Azure Identity Library med Azure CLI för att autentisera användare till Azure-tjänster. Utvecklare kan också använda Visual Studio eller Visual Studio Code för att autentisera sina anrop. Mer information finns i Autentisera klienten med [Azure Identity-klientbiblioteket](/python/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Logga in på Azure

1. Kör kommandot `login`.

    ```azurecli-interactive
    az login
    ```

    Om CLI kan öppna din standardwebbläsare gör den det och läser in en Inloggningssida för Azure.

    Annars öppnar du en webbläsarsida på och [https://aka.ms/devicelogin](https://aka.ms/devicelogin) anger auktoriseringskoden som visas i terminalen.

2. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

### <a name="install-the-packages"></a>Installera paketen

1. Skapa en lämplig projektmapp i en terminal eller kommandotolk och skapa och aktivera sedan en virtuell Python-miljö enligt beskrivningen i [Använda virtuella Python-miljöer](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Installera Azure Active Directory identitetsbiblioteket:

    ```terminal
    pip install azure.identity
    ```


1. Installera klientbiblioteket Key Vault certifikat:

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resursgrupp och ett nyckelvalv

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckelvalv

Skapa en åtkomstprincip för ditt nyckelvalv som ger certifikatbehörighet till ditt användarkonto

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create
```

#### <a name="set-environment-variables"></a>Ange miljövariabler

Det här programmet använder nyckelvalvsnamnet som en miljövariabel med namnet `KEY_VAULT_NAME` .

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

## <a name="create-the-sample-code"></a>Skapa exempelkoden

Med Azure Key Vault klientbiblioteket för Python kan du hantera certifikat. Följande kodexempel visar hur du skapar en klient, anger ett certifikat, hämtar ett certifikat och tar bort ett certifikat.

Skapa en fil med *namnet kv_certificates.py* som innehåller den här koden.

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

Kontrollera att koden i föregående avsnitt finns i en fil med namnet *kv_certificates.py*. Kör sedan koden med följande kommando:

```terminal
python kv_certificates.py
```

- Om du stöter på behörighetsfel kontrollerar du att du har kört [ `az keyvault set-policy` kommandot](#grant-access-to-your-key-vault).
- Om koden med samma nyckelnamn körs igen kan felet "(Conflict) Certificate is currently in a deleted but recoverable state((Conflict) Certificate is currently in a deleted but recoverable state" ((Konflikt) Certificate is currently in a deleted but recoverable state ((Konflikt) Certificate is currently in a deleted but recoverable state((Konflikt) Certificate is currently in <name> a deleted but recoverable state((Konflikt) Certificate is currently in a deleted but Använd ett annat nyckelnamn.

## <a name="code-details"></a>Kodinformation

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabbstarten används den inloggade användaren för att autentisera till nyckelvalvet, vilket är den bästa metoden för lokal utveckling. För program som distribueras till Azure ska hanterad identitet tilldelas till App Service eller virtuell dator. Mer information finns i Översikt över [hanterad identitet.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

I exemplet nedan expanderas namnet på ditt nyckelvalv till nyckelvalvs-URI i formatet "https:// \<your-key-vault-name\> .vault.azure.net". Det här exemplet använder  [klassen "DefaultAzureCredential()"](/python/api/azure-identity/azure.identity.defaultazurecredential) som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identitet. Mer information finns i [Standardautentisering med Azure-autentiseringsuppgifter.](https://docs.microsoft.com/python/api/overview/azure/identity-readme) 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Spara ett certifikat

När du har fått klientobjektet för nyckelvalvet kan du skapa ett certifikat med hjälp begin_create_certificate [metod:](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-) 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Här kräver certifikatet en princip [](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--) som erhållits med CertificatePolicy.get_default-metoden.

När en `begin_create_certificate` metod anropas genereras ett asynkront anrop till Azure REST API för nyckelvalvet. Det asynkrona anropet returnerar ett avsökningsobjekt. Om du vill vänta på resultatet av åtgärden anropar du `result` avsökningsmetoden.

När du hanterar begäran autentiserar Azure anroparens identitet (tjänstens huvudnamn) med hjälp av autentiseringsobjektet som du angav för klienten.


### <a name="retrieve-a-certificate"></a>Hämta ett certifikat

Om du vill läsa ett certifikat [](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-) Key Vault du använda get_certificate-metoden:

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Du kan också kontrollera att certifikatet har angetts med Azure CLI-kommandot [az keyvault certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show).

### <a name="delete-a-certificate"></a>Ta bort ett certifikat

Om du vill ta bort ett certifikat [använder begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-) metod:

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

Metoden `begin_delete_certificate` är asynkron och returnerar ett avsökningsobjekt. När avsökningsmetoden `result` anropas väntar den på att den slutförs.

Du kan kontrollera att certifikatet har tagits bort med Azure CLI-kommandot [az keyvault certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show).

När det har tagits bort förblir ett certifikat i ett borttagna men återställningsbart tillstånd under en tid. Om du kör koden igen använder du ett annat certifikatnamn.

## <a name="clean-up-resources"></a>Rensa resurser

Om du även vill experimentera med hemligheter [och](../secrets/quick-create-python.md) nycklar [kan](../keys/quick-create-python.md)du återanvända de Key Vault som skapades i den här artikeln.

När du är klar med resurserna som skapades i den här artikeln använder du annars följande kommando för att ta bort resursgruppen och alla dess resurser:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Key Vault](../general/overview.md)
- [Säker åtkomst till ett nyckelvalv](../general/secure-your-key-vault.md)
- [Azure Key Vault utvecklarguide](../general/developers-guide.md)
- [Key Vault säkerhetsöversikt](../general/security-overview.md)
- [Autentisera med Key Vault](../general/authentication.md)
