---
title: Kryptera beständiga volym anspråk med en kundhanterad nyckel (CMK) på Azure Red Hat OpenShift (ARO)
description: Ta med din egen nyckel (BYOK)/kundhanterad nyckel (CMK) distribuera instruktioner för Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: kryptering, BYOK, Aro, OpenShift, Red Hat
ms.openlocfilehash: 09e1f92a967c7b77d3bb8e27769f4cafd4fd4f53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055006"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Kryptera beständiga volym anspråk med en kundhanterad nyckel (CMK) på Azure Red Hat OpenShift (ARO) (för hands version)

Azure Storage krypterar alla data i ett lagrings konto i vila. Som standard krypteras data med Microsoft Platform-hanterade nycklar som innehåller operativ system och data diskar. Om du vill ha mer kontroll över krypterings nycklarna kan du ange Kundhanterade nycklar för att kryptera data i dina Azure Red Hat OpenShift-kluster.

> [!NOTE]
> I det här skedet finns det bara stöd för att kryptera ARO beständiga volymer med Kundhanterade nycklar. Den här funktionen är inte tillgänglig för operativ system diskar.

> [!IMPORTANT]
> ARO för hands versions funktioner är tillgängliga på en självbetjänings-och deltagande nivå. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och de är undantagna från service avtals avtal och begränsad garanti. ARO för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion.

## <a name="before-you-begin"></a>Innan du börjar
Den här artikeln förutsätter att:

* Du har ett redan befintligt ARO-kluster i OpenShift version 4,4 (eller senare).

* Du har kommando rads verktyget ' oc ' OpenShift, base64 (del av Core utils) och ' AZ '. Azure CLI installerat.

* Du är inloggad på ditt ARO-kluster med hjälp av *oc* som global kluster administratörs användare (kubeadmin).

* Du är inloggad på Azure CLI med hjälp av *AZ* med ett konto som har behörighet att bevilja "deltagar"-åtkomst i samma prenumeration som Aro-klustret.

## <a name="limitations"></a>Begränsningar

* Tillgänglighet för kundhanterad nyckel kryptering är landsspecifika. Om du vill se status för en angiven Azure-region kontrollerar du [Azure-regioner][supported-regions].
* Om du använder Ultra disks aktiverar du Ultra disks i din prenumeration innan du börjar.

## <a name="create-an-azure-key-vault-instance"></a>Skapa en Azure Key Vault-instans
En Azure Key Vault instans måste användas för att lagra dina nycklar. Skapa en ny Key Vault med rensnings skyddet och mjuk borttagning aktive rad. Skapa sedan en ny nyckel i valvet för att lagra din egen anpassade nyckel:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table
# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Skapa en Azure Disk Encryption-uppsättning

Azure Disk Encryption-uppsättningen används som referens punkt för diskar i ARO. Den är ansluten till Azure Key Vault vi skapade i föregående steg och kommer att hämta Kundhanterade nycklar från den platsen.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $myRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Ge disk krypterings behörigheten åtkomst till Key Vault
Använd disk krypterings uppsättningen som vi skapade i föregående steg och ge disk krypterings uppsättningen åtkomst till Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's AppId value.
desIdentity="$(az disk-encryption-set show -n $desName -g $myRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $myRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the disk encryption set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Hämta andra ID: n som krävs för roll tilldelningar
Vi måste tillåta att ARO-klustret använder disk krypterings uppsättningen för att kryptera PVC-anspråk (persistent Volume claims) i ARO-klustret. För att göra detta skapar vi en ny Hanterad tjänstidentitet (MSI). Vi anger också andra behörigheter för ARO-MSI och för disk krypterings uppsättningen.
```
# First, get the application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $myRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity application ID.
aroMSIAppId="$(az identity show -n $msiName -g $myRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
myRGResourceId="$(az group show -n $myRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-byokcmk-encryption"></a>Implementera andra roll tilldelningar som krävs för BYOK/CMK-kryptering
Tillämpa de nödvändiga roll tilldelningarna med de variabler som hämtades i föregående steg:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $myRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $myRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks-optional"></a>Skapa en K8s lagrings klass för krypterade Premium & Ultra disks (valfritt)
Skapa lagrings klasser som ska användas för BYOK/CMK för Premium_LRS och UltraSSD_LRS diskar:
```
# Premium Disks
cat > managed-premium-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```
### <a name="set-up-your-storage-class-configuration"></a>Konfigurera lagrings klass konfigurationen
Ersätt variablerna som är unika för ditt ARO-kluster i de två konfigurationsfilerna för lagrings klassen:
```
# Insert your current active subscription ID into the configuration
sed -i "s/subId/$subId/g" managed-premium-encrypted-byok.yaml
sed -i "s/subId/$subId/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the Resource Group which contains the disk encryption set and Key Vault
sed -i "s/myRG/$myRG/g" managed-premium-encrypted-byok.yaml
sed -i "s/myRG/$myRG/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the disk encryption set
sed -i "s/desName/$desName/g" managed-premium-encrypted-byok.yaml
sed -i "s/desName/$desName/g" managed-ultra-encrypted-byok.yaml
```
Kör sedan den här distributionen i ditt ARO-kluster för att använda lagrings klass konfigurationen:
```
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-byok.yaml
oc apply -f managed-ultra-encrypted-byok.yaml
```
## <a name="test-encryption-with-customer-managed-keys"></a>Testa kryptering med Kundhanterade nycklar
För att kontrol lera om klustret använder en kundhanterad nyckel för PVC-kryptering skapar vi ett beständigt volym anspråk med lämplig lagrings klass. Kodfragmentet nedan skapar en POD och monterar ett beständigt volym anspråk med standard diskar
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-byok-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-byok
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-byok-encryption
spec:
  containers:
  - name: mypod-with-byok-encryption
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
        claimName: mypod-with-byok-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file"></a>Tillämpa konfigurations filen för test Pod
Kör kommandona nedan för att tillämpa test Pod-konfigurationen och returnera UID för det nya beständiga volym anspråket. UID kommer att användas för att kontrol lera att disken är krypterad med BYOK/CMK.
```
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o json | jq -r '.items[0].metadata.uid')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o json |jq -r '.spec.azureDisk.diskName')"
```
### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey"></a>Verifiera att PVC-disken har kon figurer ATS med "EncryptionAtRestWithCustomerKey" 
Pod ska skapa ett beständigt volym anspråk som refererar till lagrings klassen BYOK/CMK. Genom att köra följande kommando verifierar du att PVC: n har distribuerats som förväntat:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $myRG -o json --query [encryption]
```

## <a name="next-steps"></a>Nästa steg

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions

