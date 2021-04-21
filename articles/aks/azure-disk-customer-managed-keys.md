---
title: Använda en kund hanterad nyckel för att kryptera Azure-diskar Azure Kubernetes Service (AKS)
description: ByOK (Bring Your Own Keys) för att kryptera OS- och datadiskar i AKS.
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: c5c555d7eb5142f5f41f65b24f754c65450a2713
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776199"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>BYOK (Bring Your Own Keys) med Azure-diskar i Azure Kubernetes Service (AKS)

Azure Storage krypterar alla data i ett vilokonto för lagring. Som standard krypteras data med Microsoft-hanterade nycklar. För ytterligare kontroll över krypteringsnycklar kan du tillhandahålla kund hanterade nycklar som ska användas för kryptering i vila för både operativsystemet och datadiskarna för dina AKS-kluster. Läs mer om kund hanterade nycklar i [Linux][customer-managed-keys-linux] och [Windows.][customer-managed-keys-windows]

## <a name="limitations"></a>Begränsningar
* Stöd för datadiskkryptering är begränsat till AKS-kluster som kör Kubernetes version 1.17 och senare.
* Kryptering av operativsystem och datadisk med kund hanterade nycklar kan bara aktiveras när du skapar ett AKS-kluster.

## <a name="prerequisites"></a>Förutsättningar
* Du måste aktivera mjuk borttagning och rensningsskydd för *Azure Key Vault* när du använder Key Vault för att kryptera hanterade diskar.
* Du behöver Azure CLI version 2.11.1 eller senare.

## <a name="create-an-azure-key-vault-instance"></a>Skapa en Azure Key Vault instans

Använd en Azure Key Vault instans för att lagra dina nycklar.  Du kan också använda Azure Portal för [att konfigurera kund hanterade nycklar med Azure Key Vault][byok-azure-portal]

Skapa en ny *resursgrupp och* skapa sedan en *Key Vault* instans och aktivera mjuk borttagning och rensningsskydd.  Se till att du använder samma region- och resursgruppnamn för varje kommando.

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

Ersätt *myKeyVaultName* med namnet på ditt nyckelvalv.  Du behöver också en nyckel *som* lagras i Azure Key Vault för att slutföra följande steg.  Lagra antingen din befintliga nyckel i Key Vault som du [][key-vault-generate] skapade i föregående steg eller generera en ny nyckel och ersätt *myKeyName* nedan med namnet på din nyckel.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Ge DiskEncryptionSet åtkomst till nyckelvalvet

Använd DiskEncryptionSet och resursgrupper som du skapade i föregående steg och ge DiskEncryptionSet-resursen åtkomst till Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Skapa ett nytt AKS-kluster och kryptera OS-disken

Skapa en **ny resursgrupp och** ett AKS-kluster och använd sedan nyckeln för att kryptera OS-disken. Kund hanterade nycklar stöds endast i Kubernetes-versioner som är större än 1.17. 

> [!IMPORTANT]
> Se till att du skapar en ny sauce-grupp för ditt AKS-kluster

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

När nya nodpooler läggs till i klustret som skapades ovan, används den kund-hanterade nyckeln som angavs under create för att kryptera OS-disken.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>Kryptera din AKS-klusterdatadisk (valfritt)
OS-diskkrypteringsnyckeln används för att kryptera datadisken om nyckeln inte har angetts för datadisken från v1.17.2 och du kan också kryptera AKS-datadiskar med dina andra nycklar.

> [!IMPORTANT]
> Kontrollera att du har rätt AKS-autentiseringsuppgifter. Den hanterade identiteten måste ha deltagaråtkomst till resursgruppen där diskencryptionset distribueras. Annars visas ett fel som tyder på att den hanterade identiteten inte har behörighet.

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

Skapa en fil med **namnet byok-azure-disk.yaml** som innehåller följande information.  Ersätt myAzureSubscriptionId, myResourceGroup och myDiskEncrptionSetName med dina värden och tillämpa yaml.  Se till att använda resursgruppen där DiskEncryptionSet har distribuerats.  Om du använder Azure Cloud Shell kan den här filen skapas med vi eller nano som om du arbetar i ett virtuellt eller fysiskt system:

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

Granska [metodtips för AKS-klustersäkerhet][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions