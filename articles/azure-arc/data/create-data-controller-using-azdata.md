---
title: Skapa datakontrollant med Azure Data CLI (azdata)
description: Skapa en Azure Arc-datakontrollant på ett typiskt Kubernetes-kluster med flera noder som du redan har skapat med hjälp av Azure Data CLI (azdata).
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 19451fb09919238a04ac953c9c38fc70b4744d16
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955305"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>Skapa en Azure Arc-dataenhet med hjälp av [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Krav

Läs avsnittet [skapa data styrenheten för Azure båg](create-data-controller.md) för översikts information.

Om du vill skapa data styrenheten för Azure-bågen med hjälp av [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] måste du ha [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] installerat.

   [Installera [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

Oavsett vilken mål plattform du väljer måste du ange följande miljövariabler innan du skapar användaren för administratörs användaren för datakontrollanten. Du kan ange autentiseringsuppgifterna till andra personer som måste ha administratörs behörighet till datakontrollanten vid behov.

**AZDATA_USERNAME** ett användar namn som du väljer för administratörs användaren för data kontrollanten. Exempel: `arcadmin`

**AZDATA_PASSWORD** – ett valfritt lösen ord för användaren som administrerar data kontrollanten. Lösen ordet måste innehålla minst åtta tecken och innehålla tecken från tre av följande fyra uppsättningar: versaler, gemena bokstäver, siffror och symboler.

### <a name="linux-or-macos"></a>Linux eller macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

Du måste ansluta och autentisera till ett Kubernetes-kluster och ha en befintlig Kubernetes-kontext markerad innan du påbörjar skapandet av data styrenheten för Azure-bågen. Hur du ansluter till ett Kubernetes-kluster eller en tjänst varierar. Se dokumentationen för den Kubernetes-distribution eller tjänst som du använder för att ansluta till Kubernetes-API-servern.

Du kan kontrol lera att du har en aktuell Kubernetes-anslutning och bekräfta din aktuella kontext med följande kommandon.

```console
kubectl get namespace
kubectl config current-context
```

### <a name="connectivity-modes"></a>Anslutningslägen

Som det beskrivs i [anslutnings lägen och-krav](https://docs.microsoft.com/azure/azure-arc/data/connectivity)kan Azure Arc-datakontrollanten distribueras antingen med antingen `direct` eller `indirect` anslutnings läget. Med `direct` anslutnings läge skickas användnings data automatiskt och skickas kontinuerligt till Azure. I den här artikeln anger exemplen `direct` anslutnings läget enligt följande:

   ```console
   --connectivity-mode direct
   ```

   Om du vill skapa styrenheten med `indirect` anslutnings läget uppdaterar du skripten i exemplet enligt nedan:

   ```console
   --connectivity-mode indirect
   ```

#### <a name="create-service-principal"></a>Skapa tjänstens huvudnamn

Om du distribuerar Azure Arc-dataenheten med `direct` anslutnings läge krävs autentiseringsuppgifter för tjänstens huvud namn för Azure-anslutningen. Tjänstens huvud namn används för att överföra användnings-och mät data. 

Följ dessa kommandon för att skapa mått för att ladda upp tjänstens huvud namn:

> [!NOTE]
> Att skapa ett huvud namn [för tjänsten kräver vissa behörigheter i Azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Uppdatera följande exempel om du vill skapa ett huvud namn för tjänsten. Ersätt `<ServicePrincipalName>` med namnet på tjänstens huvud namn och kör kommandot:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

Om du skapade tjänstens huvud namn tidigare och behöver bara hämta de aktuella autentiseringsuppgifterna, kör du följande kommando för att återställa autentiseringsuppgifterna.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Om du till exempel vill skapa ett huvud namn för tjänsten `azure-arc-metrics` kör du följande kommando

```console
az ad sp create-for-rbac --name azure-arc-metrics
```

Exempel på utdata:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Spara `appId` värdena, `password` och `tenant` i en miljö variabel för senare användning. 

#### <a name="save-environment-variables-in-windows"></a>Spara miljövariabler i Windows

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

#### <a name="save-environment-variables-in-linux-or-macos"></a>Spara miljövariabler i Linux eller macOS

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

#### <a name="save-environment-variables-in-powershell"></a>Spara miljövariabler i PowerShell

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
$Env:SPN_AUTHORITY="https://login.microsoftonline.com"
```

När du har skapat tjänstens huvud namn tilldelar du tjänstens huvud namn till rätt roll. 

### <a name="assign-roles-to-the-service-principal"></a>Tilldela roller till tjänstens huvud namn

Kör det här kommandot för att tilldela tjänstens huvud namn till `Monitoring Metrics Publisher` rollen i prenumerationen där dina databas instans resurser finns:

#### <a name="run-the-command-on-windows"></a>Kör kommandot i Windows

> [!NOTE]
> Du måste använda dubbla citat tecken för roll namn när du kör från en Windows-miljö.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```

#### <a name="run-the-command-on-linux-or-macos"></a>Kör kommandot på Linux eller macOS

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

#### <a name="run-the-command-in-powershell"></a>Kör kommandot i PowerShell

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

Med tjänstens huvud namn tilldelat till en lämplig roll, och miljövariablerna har angetts, kan du fortsätta att skapa data kontrollen 

## <a name="create-the-azure-arc-data-controller"></a>Skapa data styrenheten för Azure-bågen

> [!NOTE]
> Du kan använda ett annat värde för `--namespace` parametern för kommandot azdata Arc DC Create i exemplen nedan, men se till att använda namn områdets namn för `--namespace parameter` alla andra kommandon nedan.

- [Skapa på Azure Kubernetes service (AKS)](#create-on-azure-kubernetes-service-aks)
- [Skapa på AKS-motorn på Azure Stack Hub](#create-on-aks-engine-on-azure-stack-hub)
- [Skapa på AKS på Azure Stack HCI](#create-on-aks-on-azure-stack-hci)
- [Skapa på Azure Red Hat OpenShift (ARO)](#create-on-azure-red-hat-openshift-aro)
- [Skapa i Red Hat OpenShift container Platform (OCP)](#create-on-red-hat-openshift-container-platform-ocp)
- [Skapa vid öppen källkod, överordnad Kubernetes (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)
- [Skapa på AWS Elastic Kubernetes service (EKS)](#create-on-aws-elastic-kubernetes-service-eks)
- [Skapa på Google Cloud Kubernetes Engine service (GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Skapa på Azure Kubernetes service (AKS)

Som standard använder AKS-distributions profilen `managed-premium` lagrings klassen. `managed-premium`Lagrings klassen fungerar bara om du har virtuella datorer som har distribuerats med hjälp av VM-avbildningar som har Premium diskar.

Om du ska använda `managed-premium` som lagrings klass kan du köra följande kommando för att skapa data kontrollen. Ersätt plats hållarna i kommandot med resurs gruppens namn, prenumerations-ID och Azure-plats.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Om du inte är säker på vilken lagrings klass som ska användas bör du använda `default` lagrings klassen som stöds oavsett vilken VM-typ som du använder. Det ger bara den snabbaste prestandan.

Om du vill använda `default` lagrings klassen kan du köra det här kommandot:

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Skapa på AKS-motorn på Azure Stack Hub

Som standard använder distributions profilen `managed-premium` lagrings klassen. `managed-premium`Lagrings klassen fungerar bara om du har distribuerade virtuella datorer som har distribuerats med hjälp av virtuella dator avbildningar som har Premium diskar på Azure Stack Hub.

Du kan köra följande kommando för att skapa datakontrollanten med hjälp av lagrings klassen Managed-Premium:

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Om du inte är säker på vilken lagrings klass som ska användas bör du använda `default` lagrings klassen som stöds oavsett vilken VM-typ som du använder. I Azure Stack hubb backas Premium disks och standard diskar av samma lagrings infrastruktur. Därför förväntas de ge samma allmänna prestanda, men med olika IOPS-gränser.

Om du vill använda `default` lagrings klassen kan du köra det här kommandot.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).

### <a name="create-on-aks-on-azure-stack-hci"></a>Skapa på AKS på Azure Stack HCI

Som standard använder distributions profilen en lagrings klass med namnet `default` och tjänst typen `LoadBalancer` .

Du kan köra följande kommando för att skapa data styrenheten med hjälp av `default` lagrings klassen och tjänst typen `LoadBalancer` .

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).


### <a name="create-on-azure-red-hat-openshift-aro"></a>Skapa på Azure Red Hat OpenShift (ARO)

#### <a name="apply-the-scc"></a>Tillämpa SCC

Innan du skapar datakontrollanten på en Red Hat OpenShift måste du tillämpa vissa säkerhets kontext begränsningar (SCC). För för hands versionen gör dessa säkerhets begränsningar begränsade. Framtida versioner kommer att tillhandahålla uppdaterad SCC.

1. Hämta den anpassade säkerhets kontext begränsningen (SCC). Använd något av följande: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([RAW](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` Följande kommando hämtar Arc-data-SCC. yaml:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Skapa SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Tillämpa SCC på tjänst kontot.

   > [!NOTE]
   > Använd samma namnrymd här och i `azdata arc dc create` kommandot nedan. Exempel är `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```


#### <a name="create-custom-deployment-profile"></a>Skapa anpassad distributions profil

Använd profilen `azure-arc-azure-openshift` för Azure RedHat Open Shift.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>Skapa datakontrollant

Du kan köra följande kommando för att skapa data styrenheten:

> [!NOTE]
> Använd samma namnrymd här och i `oc adm policy add-scc-to-user` kommandona ovan. Exempel är `arc` .

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Skapa i Red Hat OpenShift container Platform (OCP)

> [!NOTE]
> Om du använder Red Hat OpenShift container Platform på Azure rekommenderar vi att du använder den senaste tillgängliga versionen.

#### <a name="apply-the-scc"></a>Tillämpa SCC

Innan du skapar datakontrollanten på Red Hat-OCP måste du tillämpa vissa säkerhets kontext begränsningar (SCC). För för hands versionen gör dessa säkerhets begränsningar begränsade. Framtida versioner kommer att tillhandahålla uppdaterad SCC.

1. Hämta den anpassade säkerhets kontext begränsningen (SCC). Använd något av följande: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([RAW](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` Följande kommando hämtar Arc-data-SCC. yaml:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Skapa SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Tillämpa SCC på tjänst kontot.

   > [!NOTE]
   > Använd samma namnrymd här och i `azdata arc dc create` kommandot nedan. Exempel är `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```

#### <a name="determine-storage-class"></a>Bestäm lagrings klass

Du måste också bestämma vilken lagrings klass som ska användas genom att köra följande kommando.

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>Skapa anpassad distributions profil

Skapa en ny anpassad distributions profil fil baserat på `azure-arc-openshift` distributions profilen genom att köra följande kommando. Det här kommandot skapar en katalog `custom` i din aktuella arbets katalog och en anpassad distributions profil fil `control.json` i den katalogen.

Använd profilen `azure-arc-openshift` för OpenShift container Platform.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>Ange lagrings klass 

Nu ska du ange önskad lagrings klass genom att ersätta `<storageclassname>` i kommandot nedan med namnet på den lagrings klass som du vill använda som fastställdes genom att köra `kubectl get storageclass` kommandot ovan.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>Ange LoadBalancer (valfritt)

Som standard `azure-arc-openshift` används distributions profilen `NodePort` som tjänst typ. Om du använder ett OpenShift-kluster som är integrerat med en belastningsutjämnare kan du ändra konfigurationen så att den använder `LoadBalancer` tjänst typen med hjälp av följande kommando:

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="verify-security-policies"></a>Verifiera säkerhets principer

När du använder OpenShift kanske du vill köra med standard säkerhets principerna i OpenShift eller om du ofta vill låsa miljön mer än vanligt. Du kan välja att inaktivera vissa funktioner för att minimera de behörigheter som krävs vid distributions tiden och vid körning genom att köra följande kommandon.

Det här kommandot inaktiverar mått samlingar om poddar. Du kommer inte att kunna se mått för poddar i Grafana-instrumentpaneler om du inaktiverar den här funktionen. Standardvärdet är true.

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

Det här kommandot inaktiverar mått samlingar om noder. Du kommer inte att kunna se mått för noder i Grafana-instrumentpaneler om du inaktiverar den här funktionen. Standardvärdet är true.

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

Detta kommando inaktiverar möjligheten att ta minnes dum par i fel söknings syfte.
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

#### <a name="create-data-controller"></a>Skapa datakontrollant

Nu är du redo att skapa datakontrollanten med hjälp av följande kommando.

> [!NOTE]
>   Använd samma namnrymd här och i `oc adm policy add-scc-to-user` kommandona ovan. Exempel är `arc` .

> [!NOTE]
>   `--path`Parametern ska peka på _katalogen_ som innehåller control.jspå filen som inte är control.jssjälva filen.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Skapa vid öppen källkod, överordnad Kubernetes (kubeadm)

Som standard använder kubeadm-distributions profilen en lagrings klass med namnet `local-storage` och tjänst typen `NodePort` . Om detta är acceptabelt kan du hoppa över anvisningarna nedan som anger önskad lagrings klass och tjänst typ och kör `azdata arc dc create` kommandot direkt nedan.

Om du vill anpassa din distributions profil för att ange en angiven lagrings klass och/eller tjänst typ, börjar du med att skapa en ny anpassad distributions profil fil baserat på kubeadm distributions profil genom att köra följande kommando. Det här kommandot skapar en katalog `custom` i din aktuella arbets katalog och en anpassad distributions profil fil `control.json` i den katalogen.

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

Du kan leta upp tillgängliga lagrings klasser genom att köra följande kommando.

```console
kubectl get storageclass
```

Nu ska du ange önskad lagrings klass genom att ersätta `<storageclassname>` i kommandot nedan med namnet på den lagrings klass som du vill använda som fastställdes genom att köra `kubectl get storageclass` kommandot ovan.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Som standard används kubeadm distributions profil `NodePort` som tjänst typ. Om du använder ett Kubernetes-kluster som är integrerat med en belastningsutjämnare kan du ändra konfigurationen med hjälp av följande kommando.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

Nu är du redo att skapa datakontrollanten med hjälp av följande kommando.

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Skapa på AWS Elastic Kubernetes service (EKS)

Som standard är lagrings klassen EKS `gp2` och tjänst typen `LoadBalancer` .

Kör följande kommando för att skapa datakontrollanten med den angivna EKS-distributions profilen.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Skapa på Google Cloud Kubernetes Engine service (GKE)

Som standard är lagrings klassen GKE `standard` och tjänst typen `LoadBalancer` .

Kör följande kommando för att skapa datakontrollanten med den angivna GKE-distributions profilen.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).

## <a name="monitoring-the-creation-status"></a>Övervaka skapande status

Det tar några minuter att slutföra skapandet av kontrollanten. Du kan övervaka förloppet i ett annat terminalfönster med följande kommandon:

> [!NOTE]
>  Exempel kommandona nedan förutsätter att du har skapat en datakontrollant och ett Kubernetes-namnområde med namnet `arc` . Om du har använt ett annat namn på namn område/data enhet kan du ersätta `arc` med ditt namn.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Du kan också kontrol lera skapande status för alla specifika Pod genom att köra ett kommando som nedan. Detta är särskilt användbart vid fel sökning av problem.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Fel sökning av problem med skapande

Om du stöter på problem med att skapa kan du läsa [fel söknings guiden](troubleshoot-guide.md).
