---
title: Kryptera beständiga volymanspråk med en kund hanterad nyckel (CMK) på Azure Red Hat OpenShift (ARO)
description: Distribuera instruktioner för Bring Your Own Key (BYOK) /Customer-managed key (CMK) för Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: encryption, byok, aro, cmk, openshift, red hat
ms.openlocfilehash: f6c80bab6f821dc7c85352bf57ebe255ae712d43
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783529"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Kryptera beständiga volymanspråk med en kund hanterad nyckel (CMK) på Azure Red Hat OpenShift (ARO) (förhandsversion)

Azure Storage använder kryptering på serversidan (SSE) [](../storage/common/storage-service-encryption.md) för att automatiskt kryptera dina data när de sparas i molnet. Som standard krypteras data med Microsofts plattform hanterade nycklar. För ytterligare kontroll över krypteringsnycklar kan du ange dina egna kund hanterade nycklar för att kryptera data i Azure Red Hat OpenShift kluster.

> [!NOTE]
> I det här skedet finns det endast stöd för kryptering av beständiga ARO-volymer med kund hanterade nycklar. Den här funktionen är för närvarande inte tillgänglig för operativsystemdiskar för huvudnoder eller arbetsnoder.

> [!IMPORTANT]
> Förhandsfunktionerna i ARO är tillgängliga via självbetjäning och avanmälning. Förhandsgranskningar tillhandahålls i "i dess omfattning" och "efter vad som är tillgängligt", och de undantas från serviceavtalen och den begränsade garantin. ARO-förhandsversioner omfattas delvis av kundsupporten efter bästa möjliga resultat. Därför är dessa funktioner inte avsedda för produktionsanvändning.

## <a name="before-you-begin"></a>Innan du börjar
Den här artikeln förutsätter att:

* Du har ett befintligt ARO-kluster i OpenShift version 4.4 (eller senare).

* Du har **kommandoradsverktyget oc** OpenShift, base64 (del av coreutils) och **az** Azure CLI installerade.

* Du är inloggad på ditt ARO-kluster med **oc** som global klusteradministratörsanvändare (kubeadmin).

* Du är inloggad på Azure CLI med **az** med ett konto som har behörighet att bevilja deltagaråtkomst i samma prenumeration som ARO-klustret.

## <a name="limitations"></a>Begränsningar

* Tillgängligheten för kund hanterad nyckelkryptering är regionsspecifik. Om du vill se status för en specifik Azure-region kontrollerar du [Azure-regioner][supported-regions].
* Om du vill använda Ultradiskar måste du först aktivera dem i din prenumeration innan du börjar.

## <a name="declare-cluster--encryption-variables"></a>Deklarera & krypteringsvariabler
Du bör konfigurera variablerna nedan till vad som än är lämpligt för ditt ARO-kluster där du vill aktivera kund hanterade krypteringsnycklar:
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>Skaffa ditt prenumerations-ID
Ditt Azure-prenumerations-ID används flera gånger i konfigurationen av CMK. Hämta den och lagra den som en variabel:
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Skapa en Azure Key Vault instans
En Azure Key Vault-instans måste användas för att lagra dina nycklar. Skapa en ny Key Vault med rensningsskydd aktiverat. Skapa sedan en ny nyckel i valvet för att lagra din egen anpassade nyckel:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Skapa en Azure-diskkrypteringsuppsättning

Den Azure Disk Encryption uppsättningen används som referenspunkt för diskar i ARO. Den är ansluten till den Azure Key Vault som vi skapade i föregående steg och hämtar kund hanterade nycklar från den platsen.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Ge diskkrypteringsuppsättningen åtkomst till Key Vault
Använd den diskkrypteringsuppsättning som vi skapade i föregående steg och ge diskkrypteringsuppsättningen åtkomst till Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Hämta andra ID:er som krävs för rolltilldelningar
Vi måste tillåta att ARO-klustret använder diskkrypteringsuppsättningen för att kryptera beständiga volymanspråk (PVC) i ARO-klustret. För att göra detta skapar vi en ny hanterad tjänstidentitet (MSI). Vi kommer också att ange andra behörigheter för ARO MSI och för diskkrypteringsuppsättningen.

```azurecli-interactive
# First, get the Azure Application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $buildRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity Azure Application ID.
aroMSIAppId="$(az identity show -n $msiName -g $buildRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
buildRGResourceId="$(az group show -n $buildRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>Implementera andra rolltilldelningar som krävs för CMK-kryptering
Tillämpa de rolltilldelningar som krävs med hjälp av variablerna som erhölls i föregående steg:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>Skapa en k8s-lagringsklass för krypterade Premium & Ultra-diskar
Generera lagringsklasser som ska användas för CMK för Premium_LRS och UltraSSD_LRS diskar:

```azurecli-interactive
# Premium Disks
cat > managed-premium-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```

Kör sedan den här distributionen i ditt ARO-kluster för att tillämpa lagringsklasskonfigurationen:

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>Testa kryptering med kund hanterade nycklar (valfritt)
För att kontrollera om klustret använder en kund hanterad nyckel för PV-kryptering skapar vi ett beständigt volymanspråk med hjälp av den nya lagringsklassen. Kodfragmentet nedan skapar en podd och monterar ett beständigt volymanspråk med hjälp av Premium-diskar.
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-cmk-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-cmk
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-cmk-encryption
spec:
  containers:
  - name: mypod-with-cmk-encryption
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-cmk-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file-optional"></a>Tillämpa testpoddens konfigurationsfil (valfritt)
Kör kommandona nedan för att tillämpa testpoddkonfigurationen och returnera UID för det nya beständiga volymanspråket. UID:t används för att verifiera att disken är krypterad med hjälp av CMK.
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> Ibland kan det uppstå en viss fördröjning när du tillämpar rolltilldelningar inom Azure Active Directory. Beroende på hur snabbt dessa instruktioner körs kan det hända att kommandot "Fastställa det fullständiga Azure Disk-namnet" inte lyckas. Om detta inträffar granskar du utdata från **oc describe pv mypod-with-cmk-encryption-pv** för att säkerställa att disken har etablerats. Om rolltilldelningsspridningen inte har slutförts  kan du behöva ta bort och tillämpa poddtilldelningen & PV YAML.

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>Kontrollera att PV-disken har konfigurerats med "EncryptionAtRestWithCustomerKey" (valfritt)
Podden bör skapa ett beständigt volymanspråk som refererar till CMK-lagringsklassen. Om du kör följande kommando verifieras att PV har distribuerats som förväntat:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ../aks/operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions