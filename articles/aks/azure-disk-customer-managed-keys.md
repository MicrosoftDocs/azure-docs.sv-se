---
title: Använd en kundhanterad nyckel för att kryptera Azure-diskar i Azure Kubernetes service (AKS)
description: Ta med dina egna nycklar (BYOK) för att kryptera AKS OS-och data diskar.
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 4b1c311132cc812ccb2bbbc95c4b7414b108008c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499211"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Ta med dina egna nycklar (BYOK) med Azure-diskar i Azure Kubernetes service (AKS)

Azure Storage krypterar alla data i ett lagrings konto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypterings nycklar kan du ange Kundhanterade nycklar som ska användas för kryptering i vila för både operativ systemet och data diskarna för dina AKS-kluster. Lär dig mer om Kundhanterade nycklar i [Linux][customer-managed-keys-linux] och [Windows][customer-managed-keys-windows].

## <a name="limitations"></a>Begränsningar
* Stöd för data disk kryptering är begränsat till AKS-kluster som kör Kubernetes version 1,17 och senare.
* Det går bara att aktivera kryptering av operativ system och data diskar med Kundhanterade nycklar när du skapar ett AKS-kluster.

## <a name="prerequisites"></a>Förutsättningar
* Du måste aktivera mjuk borttagning och tömning av skydd för *Azure Key Vault* när du använder Key Vault för att kryptera hanterade diskar.
* Du behöver Azure CLI-version 2.11.1 eller senare.

## <a name="create-an-azure-key-vault-instance"></a>Skapa en Azure Key Vault-instans

Använd en Azure Key Vault-instans för att lagra dina nycklar.  Du kan också använda Azure Portal för att [Konfigurera Kundhanterade nycklar med Azure Key Vault][byok-azure-portal]

Skapa en ny *resurs grupp* och skapa sedan en ny *Key Vault* -instans och aktivera mjuk borttagning och tömning av skydd.  Se till att du använder samma region och resurs grupp namn för varje kommando.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Skapa en instans av en DiskEncryptionSet

Ersätt *myKeyVaultName* med namnet på ditt nyckel valv.  Du behöver också en *nyckel* som lagras i Azure Key Vault för att utföra följande steg.  Lagra antingen den befintliga nyckeln i Key Vault du skapade i föregående steg, eller [Generera en ny nyckel][key-vault-generate] och Ersätt *myKeyName* nedan med namnet på din nyckel.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Bevilja DiskEncryptionSet åtkomst till nyckel valvet

Använd de DiskEncryptionSet och resurs grupper som du skapade i föregående steg och ge DiskEncryptionSet resurs åtkomst till Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Skapa ett nytt AKS-kluster och kryptera OS-disken

Skapa en **Ny resurs grupp** och ett AKS-kluster och Använd sedan din nyckel för att kryptera operativ system disken. Kundhanterade nycklar stöds bara i Kubernetes-versioner som är större än 1,17. 

> [!IMPORTANT]
> Se till att du skapar en ny resurs-grupp för ditt AKS-kluster

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

När nya noder läggs till i klustret som skapats ovan används den Kundhanterade nyckeln som angavs under skapandet för att kryptera operativ system disken.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>Kryptera din AKS-kluster data disk (valfritt)
Krypterings nyckeln för operativ system disken används för att kryptera data disken om nyckeln inte tillhandahålls för datadisk från v-1.17.2 och du kan också kryptera AKS-datadiskarna med dina andra nycklar.

> [!IMPORTANT]
> Se till att du har rätt AKS-autentiseringsuppgifter. Den hanterade identiteten måste ha deltagar åtkomst till den resurs grupp där diskencryptionset har distribuerats. Annars visas ett fel som tyder på att den hanterade identiteten inte har behörighet.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Skapa en fil med namnet **BYOK-Azure-disk. yaml** som innehåller följande information.  Ersätt myAzureSubscriptionId, myResourceGroup och myDiskEncrptionSetName med värdena och Använd yaml.  Se till att använda resurs gruppen där din DiskEncryptionSet har distribuerats.  Om du använder Azure Cloud Shell kan filen skapas med hjälp av vi eller nano som om du arbetar med ett virtuellt eller fysiskt system:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Kör sedan den här distributionen i ditt AKS-kluster:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>Nästa steg

Granska [metod tips för AKS kluster säkerhet][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions