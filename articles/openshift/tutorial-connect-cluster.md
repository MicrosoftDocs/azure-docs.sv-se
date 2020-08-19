---
title: Självstudie – ansluta till ett Azure Red Hat OpenShift 4-kluster
description: Lär dig hur du ansluter ett Microsoft Azure Red Hat OpenShift-kluster
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 226cf29b1a94b4508a9d68f02b7400a18eba4bc2
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587862"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>Självstudie: ansluta till ett Azure Red Hat OpenShift 4-kluster

I den här självstudien, som är del två av tre, kommer du att ansluta till ett Azure Red Hat OpenShift-kluster som kör OpenShift 4 som kubeadmin-användare via webb konsolen OpenShift. Lär dig att:
> [!div class="checklist"]
> * Hämta `kubeadmin` autentiseringsuppgifter för klustret
> * Installera OpenShift CLI
> * Ansluta till ett Azure Red Hat OpenShift-kluster med OpenShift CLI

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier skapades ett kluster med en Azure Red Hat OpenShift. Om du inte har gjort dessa steg och vill följa med, börjar du med [självstudie 1 – Skapa ett Azure Red Hat OpenShift 4-kluster.](tutorial-create-cluster.md)

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI-version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Du kan logga in på klustret med hjälp av `kubeadmin` användaren.  Kör följande kommando för att hitta lösen ordet för `kubeadmin` användaren.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

Följande exempel på utdata visar att lösen ordet är i `kubeadminPassword` .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Du kan hitta kluster konsolens URL genom att köra följande kommando, som ser ut så här: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

Starta konsol-URL: en i en webbläsare och logga in med `kubeadmin` autentiseringsuppgifterna.

![Inloggnings skärm för Azure Red Hat OpenShift](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Installera OpenShift CLI

När du är inloggad i OpenShift-webbkonsolen klickar du på **?** längst upp till höger och sedan på **kommando rads verktyg**. Ladda ned den version som är lämplig för din dator.

![Inloggnings skärm för Azure Red Hat OpenShift](media/aro4-download-cli.png)

Du kan också hämta den senaste versionen av CLI-versionen som är lämplig för din dator från <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> .

Om du kör kommandona på Azure Cloud Shell laddar du ned den senaste OpenShift 4 CLI för Linux.

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>Anslut med OpenShift CLI

Hämta API-serverns adress.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

Logga in på OpenShift-klustrets API-server med hjälp av följande kommando. Ersätt **\<kubeadmin password>** med det lösen ord som du nyss hämtade.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:
> [!div class="checklist"]
> * Hämta `kubeadmin` autentiseringsuppgifter för klustret
> * Installera OpenShift CLI
> * Ansluta till ett Azure Red Hat OpenShift-kluster med OpenShift CLI

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Ta bort ett Azure Red Hat OpenShift-kluster](tutorial-delete-cluster.md)
