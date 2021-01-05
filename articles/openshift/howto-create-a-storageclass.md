---
title: Skapa en Azure Files StorageClass på Azure Red Hat OpenShift 4
description: Lär dig hur du skapar en Azure Files StorageClass på Azure Red Hat OpenShift
ms.service: container-service
ms.topic: article
ms.date: 10/16/2020
author: grantomation
ms.author: b-grodel
keywords: Aro, OpenShift, AZ Aro, Red Hat, CLI, Azure-fil
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fe80698b71ae0ba808991d79b423d49abfacdf7c
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825908"
---
# <a name="create-an-azure-files-storageclass-on-azure-red-hat-openshift-4"></a>Skapa en Azure Files StorageClass på Azure Red Hat OpenShift 4

I den här artikeln skapar du en StorageClass för Azure Red Hat OpenShift 4 som dynamiskt etablerar ReadWriteMany-lagring (RWX) med Azure Files. Du lär dig följande:

> [!div class="checklist"]
> * Konfigurera förutsättningarna och installera nödvändiga verktyg
> * Skapa en Azure Red Hat OpenShift 4-StorageClass med Azures fil etablering

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI-version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Innan du börjar

Distribuera ett Azure Red Hat OpenShift 4-kluster i din prenumeration, se [skapa ett Azure Red Hat OpenShift 4-kluster](tutorial-create-cluster.md)


### <a name="set-up-azure-storage-account"></a>Konfigurera Azure Storage-konto

I det här steget skapas en resurs grupp utanför Azure Red Hat OpenShift-ARO-klustrets resurs grupp. Den här resurs gruppen innehåller Azure Files resurser som skapas av Azure Red Hat openshifts dynamiska provisioor.

```bash
AZURE_FILES_RESOURCE_GROUP=aro_azure_files
LOCATION=eastus

az group create -l $LOCATION -n $AZURE_FILES_RESOURCE_GROUP

AZURE_STORAGE_ACCOUNT_NAME=aroazurefilessa

az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_NAME \
    --resource-group $AZURE_FILES_RESOURCE_GROUP \
    --kind StorageV2 \
    --sku Standard_LRS
```

## <a name="set-permissions"></a>Ställa in behörigheter
### <a name="set-resource-group-permissions"></a>Ange resurs grupps behörigheter

ARO-tjänstens huvud namn kräver "Listnycklar"-behörighet för den nya resurs gruppen för Azure Storage-kontot. Tilldela rollen deltagare för att uppnå detta.

```bash
ARO_RESOURCE_GROUP=aro-rg
CLUSTER=cluster
ARO_SERVICE_PRINCIPAL_ID=$(az aro show -g $ARO_RESOURCE_GROUP -n $CLUSTER --query servicePrincipalProfile.clientId -o tsv)

az role assignment create --role Contributor -–assignee $ARO_SERVICE_PRINCIPAL_ID -g $AZURE_FILES_RESOURCE_GROUP
```

### <a name="set-aro-cluster-permissions"></a>Ange ARO-kluster behörigheter

Kontot för beständig volym bindning i OpenShift kräver möjligheten att läsa hemligheter. Skapa och tilldela en kluster roll med OpenShift för att uppnå detta.
```bash
ARO_API_SERVER=$(az aro list --query "[?contains(name,'$CLUSTER')].[apiserverProfile.url]" -o tsv)

oc login -u kubeadmin -p $(az aro list-credentials -g $ARO_RESOURCE_GROUP -n $CLUSTER --query=kubeadminPassword -o tsv) $APISERVER

oc create clusterrole azure-secret-reader \
    --verb=create,get \
    --resource=secrets

oc adm policy add-cluster-role-to-user azure-secret-reader system:serviceaccount:kube-system:persistent-volume-binder
```

## <a name="create-storageclass-with-azure-files-provisioner"></a>Skapa StorageClass med Azure Files-etablering

I det här steget skapas en StorageClass med en Azure Files-etablering. I StorageClass-manifestet krävs information om lagrings kontot så att ARO-klustret vet att det går att titta på ett lagrings konto utanför den aktuella resurs gruppen.

```bash
cat << EOF >> azure-storageclass-azure-file.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azure-file
provisioner: kubernetes.io/azure-file
parameters:
  location: $LOCATION
  skuName: Standard_LRS
  storageAccount: $AZURE_STORAGE_ACCOUNT_NAME
  resourceGroup: $AZURE_FILES_RESOURCE_GROUP
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF

oc create -f azure-storageclass-azure-file.yaml
```

## <a name="change-the-default-storageclass-optional"></a>Ändra standard-StorageClass (valfritt)

Standard StorageClass på ARO kallas hanterad-Premium och använder Azure-disk-etableringen. Ändra detta genom att utfärda korrigerings kommandon mot StorageClass-manifest.

```bash
oc patch storageclass managed-premium -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

oc patch storageclass azure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## <a name="verify-azure-file-storageclass-optional"></a>Verifiera Azure File StorageClass (valfritt)

Skapa ett nytt program och tilldela det lagrings utrymme.

```bash
oc new-project azfiletest
oc new-app –template httpd-example

#Wait for the pod to become Ready
curl $(oc get route httpd-example -n azfiletest -o jsonpath={.spec.host})

oc set volume dc/httpd-example --add --name=v1 -t pvc --claim-size=1G -m /data

#Wait for the new deployment to rollout
export POD=$(oc get pods --field-selector=status.phase==Running -o jsonpath={.items[].metadata.name})
oc exec $POD -- bash -c "mkdir ./data"
oc exec $POD -- bash -c "echo 'azure file storage' >> /data/test.txt"

oc exec $POD -- bash -c "cat /data/test.txt"
azure file storage
```
test.txt-filen visas också via Storage Explorer i Azure Portal.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat dynamisk beständig lagring med Microsoft Azure filer och Azure Red Hat OpenShift 4. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett lagringskonto
> * Konfigurera en StorageClass i ett Azure Red Hat OpenShift 4-kluster med hjälp av Azure Files-etableringen

Gå vidare till nästa artikel om du vill lära dig mer om resurser som stöds i Azure Red Hat OpenShift 4.

* [Support policy för Azure Red Hat OpenShift](support-policies-v4.md)
