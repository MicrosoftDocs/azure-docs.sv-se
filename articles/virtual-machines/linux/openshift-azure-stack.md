---
title: Distribuera OpenShift i Azure Stack
description: Distribuera OpenShift i Azure Stack.
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: decc66d2d5abd16c084aa19443dbac54e87a9560
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667231"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Distribuera OpenShift container Platform eller OKD i Azure Stack

OpenShift kan distribueras i Azure Stack. Det finns vissa viktiga skillnader mellan Azure och Azure Stack så att distributionen skiljer sig något och några av funktionerna kan skilja sig något.

Azure Cloud-providern fungerar för närvarande inte i Azure Stack. Därför kan du inte använda disk koppling för beständigt lagrings utrymme i Azure Stack. I stället kan du konfigurera andra lagrings alternativ som NFS, iSCSI, GlusterFS osv. Alternativt kan du aktivera CNS och använda GlusterFS för beständig lagring. Om CNS är aktive rad kommer tre ytterligare noder att distribueras med ytterligare lagrings utrymme för GlusterFS-användning.

Du kan använda en av flera metoder för att distribuera OpenShift container Platform eller OKD i Azure Stack:

- Du kan distribuera de nödvändiga komponenterna i Azure-infrastrukturen manuellt och sedan följa [dokumentationen för OpenShift container Platform](https://docs.openshift.com/container-platform) eller [OKD-dokumentationen](https://docs.okd.io).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-container-platform/) som fören klar distributionen av OpenShift container Platform-klustret.
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-origin) som fören klar distributionen av OKD-klustret.

Om du använder Resource Manager-mallen väljer du rätt gren (azurestack-release-3. x). Mallarna för Azure fungerar inte eftersom API-versionerna skiljer sig mellan Azure och Azure Stack. Avbildnings referensen RHEL är för närvarande hårdkodad som en variabel i azuredeploy.jspå filen och måste ändras för att matcha din avbildning.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

För alla alternativ krävs en Red Hat-prenumeration. Under distributionen registreras Red Hat Enterprise Linux-instansen till Red Hat-prenumerationen och kopplas till det pool-ID som innehåller rättigheterna för OpenShift container Platform.
Se till att du har ett giltigt användar namn, lösen ord och pool-ID för Red Hat Subscription Manager (RHSM). Alternativt kan du använda en aktiverings nyckel, ett org-ID och ett pool-ID.  Du kan kontrol lera den här informationen genom att logga in på https://access.redhat.com .

## <a name="azure-stack-prerequisites"></a>Azure Stack förutsättningar

En RHEL-avbildning (OpenShift container Platform) eller CentOS Image (OKD) måste läggas till i din Azure Stack-miljö för att distribuera ett OpenShift-kluster. Kontakta Azure Stack-administratören om du vill lägga till avbildningarna. Du hittar anvisningar här:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Distribuera med hjälp av OpenShift container Platform eller OKD Resource Manager-mallen

Om du vill distribuera med hjälp av Resource Manager-mallen använder du en parameter fil för att ange indataparametrarna. Om du vill anpassa distributionen ytterligare delar du GitHub-lagrings platsen och ändrar lämpliga objekt.

Några vanliga anpassnings alternativ är, men är inte begränsade till:

- Skydds VM-storlek (variabel i azuredeploy.jspå)
- Namngivnings konventioner (variabler i azuredeploy.jspå)
- OpenShift-kluster, information, modifierad via hosts-filen (deployOpenShift.sh)
- RHEL bild referens (variabel i azuredeploy.jspå)

Om du vill distribuera med hjälp av Azure CLI följer du lämpligt avsnitt i avsnittet [OpenShift container Platform](./openshift-container-platform-3x.md) eller avsnittet [OKD](./openshift-okd.md) .

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-container-platform-3x-post-deployment.md)
- [Felsöka OpenShift-distribution i Azure](./openshift-container-platform-3x-troubleshooting.md)