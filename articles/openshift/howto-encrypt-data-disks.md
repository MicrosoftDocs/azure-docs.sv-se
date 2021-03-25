---
title: Kryptera beständiga volym anspråk med en kundhanterad nyckel (CMK) på Azure Red Hat OpenShift (ARO)
description: Ta med din egen nyckel (BYOK)/kundhanterad nyckel (CMK) distribuera instruktioner för Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: kryptering, BYOK, Aro, CMK, OpenShift, Red Hat
ms.openlocfilehash: cf028456cc8971678373d36214885c3f79df8e82
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047073"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Kryptera beständiga volym anspråk med en kundhanterad nyckel (CMK) på Azure Red Hat OpenShift (ARO) (för hands version)

Azure Storage använder SSE (Server Side Encryption) för att automatiskt [kryptera](../storage/common/storage-service-encryption.md) dina data när de sparas i molnet. Som standard krypteras data med Microsoft-plattforms hanterade nycklar. Om du vill ha ytterligare kontroll över krypterings nycklarna kan du ange egna Kundhanterade nycklar för att kryptera data i dina Azure Red Hat OpenShift-kluster.

> [!NOTE]
> I det här skedet finns det bara stöd för att kryptera ARO beständiga volymer med Kundhanterade nycklar. Den här funktionen är för närvarande inte tillgänglig för operativ system diskar för Master-eller Worker-noder.

> [!IMPORTANT]
> ARO för hands versions funktioner är tillgängliga på en självbetjänings-och deltagande nivå. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och de är undantagna från service avtals avtal och begränsad garanti. ARO för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion.

## <a name="before-you-begin"></a>Innan du börjar
Den här artikeln förutsätter att:

* Du har ett redan befintligt ARO-kluster i OpenShift version 4,4 (eller senare).

* Du har kommando rads verktyget **oc** OpenShift, base64 (del av coreutils) och **AZ** Azure CLI installerat.

* Du är inloggad på ditt ARO-kluster med hjälp av **oc** som global kluster administratörs användare (kubeadmin).

* Du är inloggad på Azure CLI med hjälp av **AZ** med ett konto som har behörighet att bevilja "deltagar"-åtkomst i samma prenumeration som Aro-klustret.

## <a name="limitations"></a>Begränsningar

* Tillgänglighet för kundhanterad nyckel kryptering är landsspecifika. Om du vill se status för en angiven Azure-region kontrollerar du [Azure-regioner][supported-regions].
* Om du vill använda Ultra disks måste du först aktivera dem i din prenumeration innan du börjar.

## <a name="declare-cluster--encryption-variables"></a>Deklarera variabler för kluster & kryptering
Du bör konfigurera variablerna nedan till det som kan vara lämpligt för ditt ARO-kluster där du vill aktivera Kundhanterade krypterings nycklar:
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>Hämta ditt prenumerations-ID
Ditt Azure-prenumerations-ID används flera gånger i konfigurationen av CMK. Hämta och lagra den som en variabel:
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Skapa en Azure Key Vault-instans
En Azure Key Vault instans måste användas för att lagra dina nycklar. Skapa en ny Key Vault med rensnings skyddet aktiverat. Skapa sedan en ny nyckel i valvet för att lagra din egen anpassade nyckel:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Skapa en Azure Disk Encryption-uppsättning

Azure Disk Encryptions uppsättningen används som referens punkt för diskar i ARO. Den är ansluten till Azure Key Vault vi skapade i föregående steg och kommer att hämta Kundhanterade nycklar från den platsen.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Ge disk krypterings behörigheten åtkomst till Key Vault
Använd disk krypterings uppsättningen som vi skapade i föregående steg och ge disk krypterings uppsättningen åtkomst till Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Hämta andra ID: n som krävs för roll tilldelningar
Vi måste tillåta att ARO-klustret använder disk krypterings uppsättningen för att kryptera PVC-anspråk (persistent Volume claims) i ARO-klustret. För att göra detta skapar vi en ny Hanterad tjänstidentitet (MSI). Vi kommer också att ange andra behörigheter för ARO-MSI och för disk krypterings uppsättningen.

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

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>Implementera andra roll tilldelningar som krävs för CMK-kryptering
Tillämpa de nödvändiga roll tilldelningarna med de variabler som hämtades i föregående steg:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>Skapa en K8s lagrings klass för krypterade Premium & Ultra disks
Skapa lagrings klasser som ska användas för CMK för Premium_LRS och UltraSSD_LRS diskar:

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

Kör sedan den här distributionen i ditt ARO-kluster för att använda lagrings klass konfigurationen:

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>Testa kryptering med Kundhanterade nycklar (valfritt)
För att kontrol lera om klustret använder en kundhanterad nyckel för PVC-kryptering skapar vi ett beständigt volym anspråk med den nya lagrings klassen. Kodfragmentet nedan skapar en POD och monterar ett beständigt volym anspråk med hjälp av Premium diskar.
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
### <a name="apply-the-test-pod-configuration-file-optional"></a>Tillämpa konfigurations filen för test Pod (valfritt)
Kör kommandona nedan för att tillämpa test Pod-konfigurationen och returnera UID för det nya beständiga volym anspråket. UID kommer att användas för att kontrol lera att disken är krypterad med CMK.
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> Ibland kan det uppstå en liten fördröjning när roll tilldelningar tillämpas i Azure Active Directory. Beroende på hur snabbt de här instruktionerna körs kanske kommandot för att fastställa det fullständiga namnet på Azure-disken inte lyckas. Om detta inträffar granskar du utmatningen av **oc och beskriver PVC-myPod – med-CMK-Encryption-PVC** för att säkerställa att disken har allokerats. Om spridningen av roll tilldelningen inte har slutförts kan du behöva *ta bort* och *tillämpa* Pod & PVC-yaml igen.

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>Verifiera att PVC-disken har kon figurer ATS med "EncryptionAtRestWithCustomerKey" (valfritt)
Pod ska skapa ett beständigt volym anspråk som refererar till lagrings klassen CMK. Genom att köra följande kommando verifierar du att PVC: n har distribuerats som förväntat:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ../aks/operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions