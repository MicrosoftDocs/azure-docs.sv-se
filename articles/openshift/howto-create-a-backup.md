---
title: Skapa en Azure Red Hat OpenShift 4-kluster program säkerhets kopiering med Velero
description: Lär dig hur du skapar en säkerhets kopia av dina Azure Red Hat OpenShift-kluster program med Velero
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: Aro, OpenShift, AZ Aro, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: 42ad24e4421b75a3942879bcac8f99b3c8b8138a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180963"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>Skapa ett säkerhets kopierings program för Azure Red Hat OpenShift 4-kluster

I den här artikeln förbereder du din miljö för att skapa en säkerhets kopia av kluster program för Azure Red Hat OpenShift 4. Du lär dig följande:

> [!div class="checklist"]
> * Konfigurera förutsättningarna och installera nödvändiga verktyg
> * Skapa en Azure Red Hat OpenShift 4 program säkerhets kopiering

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI-version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Innan du börjar

### <a name="install-velero"></a>Installera Velero

Om du vill [Installera](https://velero.io/docs/main/basic-install/) Velero på systemet följer du den rekommenderade processen för ditt operativ system.

### <a name="set-up-azure-storage-account-and-blob-container"></a>Konfigurera Azure Storage-konto och blob-behållare

I det här steget skapas en resurs grupp utanför ARO-klustrets resurs grupp.  Den här resurs gruppen tillåter att säkerhets kopiorna bevaras och kan återställa program till nya kluster.

```bash
AZURE_BACKUP_RESOURCE_GROUP=Velero_Backups
az group create -n $AZURE_BACKUP_RESOURCE_GROUP --location eastus

AZURE_STORAGE_ACCOUNT_ID="velero$(uuidgen | cut -d '-' -f5 | tr '[A-Z]' '[a-z]')"
az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_ID \
    --resource-group $AZURE_BACKUP_RESOURCE_GROUP \
    --sku Standard_GRS \
    --encryption-services blob \
    --https-only true \
    --kind BlobStorage \
    --access-tier Hot

BLOB_CONTAINER=velero
az storage container create -n $BLOB_CONTAINER --public-access off --account-name $AZURE_STORAGE_ACCOUNT_ID
```

## <a name="set-permissions-for-velero"></a>Ange behörigheter för Velero

### <a name="create-service-principal"></a>Skapa tjänstens huvudnamn

Velero måste ha behörighet för att säkerhetskopiera och återställa. När du skapar ett huvud namn för tjänsten ger du Velero behörighet att komma åt den resurs grupp som du definierar i föregående steg. I det här steget hämtas resurs gruppen för klustret:

```bash
export AZURE_RESOURCE_GROUP=$(az aro show --name <name of cluster> --resource-group <name of resource group> | jq -r .clusterProfile.resourceGroupId | cut -d '/' -f 5,5)
```


```bash
AZURE_SUBSCRIPTION_ID=$(az account list --query '[?isDefault].id' -o tsv)

AZURE_TENANT_ID=$(az account list --query '[?isDefault].tenantId' -o tsv)
```

```bash
AZURE_CLIENT_SECRET=$(az ad sp create-for-rbac --name "velero" --role "Contributor" --query 'password' -o tsv \
--scopes  /subscriptions/$AZURE_SUBSCRIPTION_ID)
AZURE_CLIENT_ID=$(az ad sp list --display-name "velero" --query '[0].appId' -o tsv)

```

```bash
cat << EOF  > ./credentials-velero.yaml
AZURE_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
AZURE_TENANT_ID=${AZURE_TENANT_ID}
AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET}
AZURE_RESOURCE_GROUP=${AZURE_RESOURCE_GROUP}
AZURE_CLOUD_NAME=AzurePublicCloud
EOF
```

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>Installera Velero på Azure Red Hat OpenShift 4-kluster

Det här steget installerar Velero i det egna projektet och de [anpassade resurs definitioner](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) som krävs för att säkerhetskopiera och återställa med Velero. Kontrol lera att du har loggat in i ett Azure Red Hat OpenShift v4-kluster.


```bash
velero install \
--provider azure \
--plugins velero/velero-plugin-for-microsoft-azure:v1.1.0 \
--bucket $BLOB_CONTAINER \
--secret-file ~/path/to/credentials-velero.yaml \
--backup-location-config resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,storageAccount=$AZURE_STORAGE_ACCOUNT_ID \
--snapshot-location-config apiTimeout=15m \
--velero-pod-cpu-limit="0" --velero-pod-mem-limit="0" \
--velero-pod-mem-request="0" --velero-pod-cpu-request="0"
```

## <a name="create-a-backup-with-velero"></a>Skapa en säkerhets kopia med Velero

Om du vill skapa en säkerhets kopia av programmet med Velero måste du inkludera det namn område som programmet finns i.  Om du har ett `nginx-example` namn område och vill inkludera alla resurser i namn området i säkerhets kopian kör du följande kommando i terminalen:

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
Du kan kontrol lera status för säkerhets kopieringen genom att köra:

```bash
oc get backups -n velero <name of backup> -o yaml
```

En lyckad säkerhets kopiering kommer att matas ut `phase:Completed` och objekten är aktiva i behållaren i lagrings kontot.

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>Skapa en säkerhets kopia med Velero för att inkludera ögonblicks bilder

Om du vill skapa en säkerhets kopia av programmet med Velero för att inkludera de beständiga volymerna i ditt program måste du inkludera det namn område som programmet finns i samt inkludera `snapshot-volumes=true` flaggan när du skapar säkerhets kopian

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

Du kan kontrol lera status för säkerhets kopieringen genom att köra:

```bash
oc get backups -n velero <name of backup> -o yaml
```

En lyckad säkerhets kopiering med utdata `phase:Completed` och objekten är Live i behållaren i lagrings kontot.

Mer information om hur du skapar säkerhets kopior och återställer med hjälp av Velero finns i [OpenShift-resurser för säkerhets kopiering på det inbyggda sättet](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>Nästa steg

I den här artikeln säkerhetskopierades ett Azure Red Hat OpenShift 4-kluster program. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en OpenShift v4-kluster program säkerhets kopiering med Velero
> * Skapa en OpenShift v4-kluster program säkerhets kopiering med ögonblicks bilder med hjälp av Velero


Gå vidare till nästa artikel om du vill lära dig hur du skapar en Azure Red Hat OpenShift 4-kluster program återställning.

* [Skapa en Azure Red Hat OpenShift 4-kluster program återställning](howto-create-a-restore.md)
* [Skapa en Azure Red Hat OpenShift 4-kluster program återställning inklusive ögonblicks bilder](howto-create-a-restore.md)
