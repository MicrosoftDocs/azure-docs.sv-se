---
title: Självstudie – Ta bort Azure Red Hat OpenShift kluster
description: I den här självstudien lär du dig hur du tar Azure Red Hat OpenShift ett kluster med Hjälp av Azure CLI
author: sakthi-vetrivel
ms.custom: fasttrack-edit, devx-track-azurecli
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 04/24/2020
ms.openlocfilehash: 65bb27f1f85b7a26e35074da84cfc27b2a5761a1
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484769"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Självstudie: Ta bort ett Azure Red Hat OpenShift 4-kluster

I den här självstudien, som är del tre av tre, tas Azure Red Hat OpenShift kluster som kör OpenShift 4 bort. Lär dig att:

> [!div class="checklist"]
> * Ta bort ett Azure Red Hat OpenShift-kluster


## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier skapades Azure Red Hat OpenShift ett kluster och anslöts till med hjälp av OpenShift-webbkonsolen. Om du inte har utfört de här stegen och vill följa med börjar du med [Självstudie 1 – Skapa ett Azure Red Hat Openshift 4-kluster.](tutorial-create-cluster.md)

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du kör Azure CLI lokalt kör du för `az login` att logga in på Azure.

```bash
az login
```

Om du har åtkomst till flera prenumerationer kör `az account set -s {subscription ID}` du ersätter `{subscription ID}` med den prenumeration som du vill använda.

## <a name="delete-the-cluster"></a>Ta bort klustret

I tidigare självstudier har följande variabler angetts.

```bash
CLUSTER=yourclustername
RESOURCEGROUP=yourresourcegroup
```

Ta bort klustret med hjälp av dessa värden:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

Du uppmanas sedan att bekräfta om du vill ta bort klustret. När du har bekräftat `y` med tar det flera minuter att ta bort klustret. När kommandot är klart tas hela resursgruppen och alla resurser i den bort, inklusive clusterâ).".

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:
> [!div class="checklist"]
> * Ta bort ett Azure Red Hat OpenShift 4-kluster

Läs mer om hur du använder OpenShift med den officiella [dokumentationen för Red Hat OpenShift](https://docs.openshift.com/container-platform/4.6/welcome/index.html)
