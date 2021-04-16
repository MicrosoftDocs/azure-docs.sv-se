---
title: Skapa en Azure Red Hat OpenShift 4-klusterprogramsäkerhetskopiering med Velero
description: Lär dig hur du skapar en säkerhetskopia av dina Azure Red Hat OpenShift-klusterprogram med Hjälp av Velero
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c8bf722bd77372cd89e7c64757347b5fd07eb1ed
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481369"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>Skapa en programsäkerhetskopia Azure Red Hat OpenShift 4-kluster

I den här artikeln förbereder du din miljö för att skapa en Azure Red Hat OpenShift 4 klusterprogramsäkerhetskopiering. Du lär dig följande:

> [!div class="checklist"]
> * Konfigurera förutsättningarna och installera nödvändiga verktyg
> * Skapa en Azure Red Hat OpenShift 4-programsäkerhetskopia

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Innan du börjar

### <a name="install-velero"></a>Installera Velero

Om [du](https://velero.io/docs/main/basic-install/) vill installera Velero på datorn följer du den rekommenderade processen för ditt operativsystem.

### <a name="set-up-azure-storage-account-and-blob-container"></a>Konfigurera Azure Storage-konto och blobcontainer

Det här steget skapar en resursgrupp utanför ARO-klustrets resursgrupp.  Med den här resursgruppen kan säkerhetskopiorna bevaras och program kan återställas till nya kluster.

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

Velero behöver behörighet för att göra säkerhetskopieringar och återställningar. När du skapar ett huvudnamn för tjänsten ger du Velero behörighet att komma åt resursgruppen som du definierar i föregående steg. Det här steget hämtar klustrets resursgrupp:

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

Det här steget installerar Velero i [](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) sitt eget projekt och de anpassade resursdefinitioner som krävs för att göra säkerhetskopieringar och återställningar med Velero. Kontrollera att du har loggat in på ett Azure Red Hat OpenShift v4-kluster.


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

## <a name="create-a-backup-with-velero"></a>Skapa en säkerhetskopia med Velero

Om du vill skapa en programsäkerhetskopia med Velero måste du inkludera det namnområde som programmet finns i.  Om du har ett namnområde och vill inkludera alla resurser i namnområdet i säkerhetskopian kör du `nginx-example` följande kommando i terminalen:

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
Du kan kontrollera säkerhetskopieringsstatusen genom att köra:

```bash
oc get backups -n velero <name of backup> -o yaml
```

En lyckad säkerhetskopiering matar `phase:Completed` ut och objekten finns i containern på lagringskontot.

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>Skapa en säkerhetskopia med Velero för att inkludera ögonblicksbilder

Om du vill skapa en programsäkerhetskopia med Velero för att inkludera de beständiga volymerna i ditt program måste du inkludera det namnområde som programmet finns i samt inkludera flaggan när du skapar `snapshot-volumes=true` säkerhetskopian

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

Du kan kontrollera säkerhetskopieringsstatusen genom att köra:

```bash
oc get backups -n velero <name of backup> -o yaml
```

En lyckad säkerhetskopiering med `phase:Completed` utdata och objekten finns i containern på lagringskontot.

Mer information om hur du skapar säkerhetskopior och återställningar med Hjälp av Velero finns i [Backup OpenShift-resurser på det inbyggda sättet](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>Nästa steg

I den här artikeln säkerhetskopierades Azure Red Hat OpenShift 4-klusterprogram. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en säkerhetskopiering av OpenShift v4-klusterprogram med Velero
> * Skapa en säkerhetskopiering av OpenShift v4-klusterprogram med ögonblicksbilder med Hjälp av Velero


Gå vidare till nästa artikel om du vill lära dig hur du skapar en Azure Red Hat OpenShift 4-klusterprogramåterställning.

* [Skapa en Azure Red Hat OpenShift 4-klusterprogramåterställning](howto-create-a-restore.md)
* [Skapa en Azure Red Hat OpenShift 4-klusterprogramåterställning inklusive ögonblicksbilder](howto-create-a-restore.md)
