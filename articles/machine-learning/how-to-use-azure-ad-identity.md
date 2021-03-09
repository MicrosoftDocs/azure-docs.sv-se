---
title: Använd Azure AD-identitet med din webb tjänst
titleSuffix: Azure Machine Learning
description: Använd Azure AD-identitet med din webb tjänst i Azure Kubernetes-tjänsten för att få åtkomst till moln resurser under poängsättningen.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b6e6288f125da2a29a8eff56b64f327914f90cb4
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520480"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Använda Azure AD-identitet med din Machine Learning-webbtjänst i Azure Kubernetes-tjänsten

I den här instruktionen får du lära dig hur du tilldelar en Azure Active Directory (Azure AD)-identitet till din distribuerade maskin inlärnings modell i Azure Kubernetes-tjänsten. Med [Azure AD Pod Identity](https://github.com/Azure/aad-pod-identity) Project kan program få åtkomst till moln resurser på ett säkert sätt med hjälp av en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) och Kubernetes-primitiver. Detta gör att webb tjänsten kan komma åt dina Azure-resurser på ett säkert sätt utan att behöva bädda in autentiseringsuppgifter eller hantera tokens direkt inuti `score.py` skriptet. I den här artikeln beskrivs stegen för att skapa och installera en Azure-identitet i Azure Kubernetes service-klustret och tilldela identiteten till den distribuerade webb tjänsten.

## <a name="prerequisites"></a>Förutsättningar

- [Azure CLI-tillägget för Machine Learning-tjänsten](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/intro)eller [Azure Machine Learning Visual Studio Code-tillägget](tutorial-setup-vscode-extension.md).

- Åtkomst till ditt AKS-kluster med hjälp av `kubectl` kommandot. Mer information finns i [ansluta till klustret](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)

- En Azure Machine Learning-webbtjänst som distribueras till ditt AKS-kluster.

## <a name="create-and-install-an-azure-identity"></a>Skapa och installera en Azure-identitet

1. Använd följande kommando för att avgöra om ditt AKS-kluster är Kubernetes RBAC aktiverat:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Det här kommandot returnerar värdet `true` om KUBERNETES RBAC är aktiverat. Det här värdet anger kommandot som ska användas i nästa steg.

1. Installera [Azure AD Pod-identitet](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/) i ditt AKS-kluster.

1. [Skapa en identitet på Azure](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#2-create-an-identity-on-azure) genom att följa stegen som visas på sidan Azure AD Pod Identity Project.

1. [Distribuera AzureIdentity](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#3-deploy-azureidentity) enligt stegen som visas på sidan Azure AD Pod Identity Project.

1. [Distribuera AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding) enligt stegen som visas på sidan Azure AD Pod Identity Project.

1. Om Azure-identiteten som skapades i föregående steg inte finns i samma resurs grupp för AKS för ditt-kluster, följer du [Roll tilldelnings](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/#user-assigned-identities-that-are-not-within-the-node-resource-group) stegen som visas på sidan för Azure AD Pod-identitets projekt.

## <a name="assign-azure-identity-to-web-service"></a>Tilldela Azure Identity till Web Service

Följande steg använder den Azure-identitet som skapades i föregående avsnitt och tilldelar den till AKS-webbtjänsten via en **väljar-etikett**.

Börja med att identifiera namnet och namn området för distributionen i ditt AKS-kluster som du vill tilldela Azure-identiteten. Du kan hämta den här informationen genom att köra följande kommando. Namn områdena ska vara Azure Machine Learning arbets ytans namn och distributions namnet ska vara ditt slut punkts namn som det visas i portalen.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Lägg till etiketten för Azure Identity Selector i distributionen genom att redigera distributions specifikationen. Väljar värdet ska vara det som du definierade i steg 5 i [distribuera AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Redigera distributionen för att lägga till etiketten för Azure Identity Selector. Gå till följande avsnitt under `/spec/template/metadata/labels` . Du bör se värden som `isazuremlapp: “true”` . Lägg till etiketten AAD-Pod-Identity som visas nedan.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Kontrol lera att etiketten har lagts till korrekt genom att köra följande kommando. Du bör också se status för de nyligen skapade poddar.

```azurecli-interactive
   kubectl get pod -n azureml-<name of workspace> --show-labels
```

När poddar är igång kommer webb tjänsterna för den här distributionen nu att kunna komma åt Azure-resurser via din Azure-identitet utan att behöva bädda in autentiseringsuppgifterna i din kod.

## <a name="assign-roles-to-your-azure-identity"></a>Tilldela roller till din Azure-identitet

[Tilldela din Azure-hanterade identitet med lämpliga roller](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) för att få åtkomst till andra Azure-resurser. Se till att de roller som du tilldelar har rätt **data åtgärder**. Till exempel har [rollen Storage BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) Läs behörighet till lagrings-bloben medan den allmänna [läsar rollen](../role-based-access-control/built-in-roles.md#reader) kanske inte är det.

## <a name="use-azure-identity-with-your-web-service"></a>Använd Azure Identity med din webb tjänst

Distribuera en modell till ditt AKS-kluster. `score.py`Skriptet kan innehålla åtgärder som pekar på de Azure-resurser som din Azure-identitet har åtkomst till. Kontrol lera att du har installerat de klient biblioteks beroenden som krävs för resursen som du försöker få åtkomst till. Nedan visas några exempel på hur du kan använda din Azure-identitet för att få åtkomst till olika Azure-resurser från din tjänst.

### <a name="access-key-vault-from-your-web-service"></a>Åtkomst Key Vault från din webb tjänst

Om du har fått Läs behörighet för Azure Identity till en hemlighet i en **Key Vault** kan du `score.py` komma åt den med följande kod.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name)
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

> [!IMPORTANT]
> I det här exemplet används DefaultAzureCredential. Om du vill ge din identitet åtkomst med hjälp av en speciell åtkomst princip läser du [tilldela en Key Vault åtkomst princip med hjälp av Azure CLI](../key-vault/general/assign-access-policy-cli.md).

### <a name="access-blob-from-your-web-service"></a>Få åtkomst till BLOB från webb tjänsten

Om du har fått Läs åtkomst till data i en **lagrings-BLOB** med Azure Identity `score.py` kan du komma åt den med följande kod.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du använder python-klient biblioteket för Azure Identity finns i [lagrings platsen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) på GitHub.
* En detaljerad guide om hur du distribuerar modeller till Azure Kubernetes service-kluster finns i [instruktionen How-to](how-to-deploy-azure-kubernetes-service.md).