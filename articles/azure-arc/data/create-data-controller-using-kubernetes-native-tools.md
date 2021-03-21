---
title: Skapa en datakontrollant med Kubernetes-verktyg
description: Skapa en datakontrollant med Kubernetes-verktyg
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 500587dc6564aa55eb430365eb67bb958bbd2482
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519987"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Skapa en Azure båg-datakontrollant med Kubernetes-verktyg

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Läs avsnittet [skapa data styrenheten för Azure båg](create-data-controller.md) för översikts information.

Om du vill skapa data styrenheten för Azure-bågen med Kubernetes-verktyg måste du ha Kubernetes-verktygen installerade.  Exemplen i den här artikeln kommer att använda `kubectl` , men liknande metoder kan användas med andra Kubernetes-verktyg som Kubernetes-instrumentpanelen, `oc` eller `helm` om du är van vid dessa verktyg och Kubernetes yaml/JSON.

[Installera kubectl-verktyget](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> Några av stegen för att skapa en Azure Arc-datakontrollant som anges nedan kräver Kubernetes-kluster administratörs behörighet.  Om du inte är en Kubernetes kluster administratör måste du ha Kubernetes-klustrets administratör för att utföra dessa steg för din räkning.

### <a name="cleanup-from-past-installations"></a>Rensa från tidigare installationer

Om du har installerat Azure Arc data Controller tidigare, i samma kluster och tog bort Azure Arc-datastyrenheten med `azdata arc dc delete` kommandot, kan det finnas vissa kluster nivå objekt som fortfarande måste tas bort. Kör följande kommandon för att ta bort kluster nivå objekt för Azure Arc-datakontrollanten:

```console
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
```

## <a name="overview"></a>Översikt

Att skapa data styrenheten för Azure-bågen har följande steg på hög nivå:
1. Skapa anpassade resurs definitioner för data styrenheten båge, Azure SQL-hanterad instans och PostgreSQL-skalning. **[Kräver Kubernetes-kluster administratörs behörighet]**
2. Skapa ett namn område där data styrenheten ska skapas. **[Kräver Kubernetes-kluster administratörs behörighet]**
3. Skapa start program-tjänsten inklusive replik uppsättningen, tjänst kontot, rollen och roll bindningen.
4. Skapa en hemlighet för data enhetens administratörs användar namn och lösen ord.
5. Skapa datakontrollanten.
   
## <a name="create-the-custom-resource-definitions"></a>Skapa anpassade resurs definitioner

Kör följande kommando för att skapa anpassade resurs definitioner.  **[Kräver Kubernetes-kluster administratörs behörighet]**

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>Skapa ett namn område där data styrenheten ska skapas

Kör ett kommando som liknar följande om du vill skapa ett nytt, dedikerat namn område där data styrenheten ska skapas.  I det här exemplet och resten av exemplen i den här artikeln används ett namn områdes namn för `arc` . Om du väljer att använda ett annat namn använder du samma namn i hela.

```console
kubectl create namespace arc
```

Om andra personer kommer att använda det här namn området som inte är kluster administratörer rekommenderar vi att du skapar en namn områdes administratörs roll och beviljar rollen till dessa användare via en roll bindning.  Namn områdes administratören ska ha fullständig behörighet för namn området.  Mer information kommer att tillhandahållas senare om hur du ger mer detaljerad rollbaserad åtkomst till användare.

## <a name="create-the-bootstrapper-service"></a>Skapa start program-tjänsten

Start program-tjänsten hanterar inkommande begär Anden för att skapa, redigera och ta bort anpassade resurser, till exempel en datakontrollant, SQL-hanterad instans eller PostgreSQL för storskalig Server grupp.

Kör följande kommando för att skapa en start program-tjänst, ett tjänst konto för start program-tjänsten och en roll-och roll bindning för start program Service-kontot.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/bootstrapper.yaml
```

Kontrol lera att start program-Pod körs med hjälp av följande kommando.  Du kan behöva köra det ett par gånger tills statusen ändras till `Running` .

```console
kubectl get pod --namespace arc
```

Mallfilen start program. yaml används som standard för att hämta start program behållar avbildningen från Microsoft Container Registry (MCR).  Om din miljö inte har åtkomst direkt till Microsoft Container Registry kan du göra följande:
- Följ stegen för att [Hämta behållar avbildningarna från Microsoft container Registry och skicka dem till ett privat behållar register](offline-deployment.md).
- [Skapa en bild-pull-hemlighet](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin) för ditt privata behållar register.
- Lägg till en bild-pull-hemlighet i Start program-behållaren. Se exemplet nedan.
- Ändra avbildningens plats för start program-avbildningen. Se exemplet nedan.

I exemplet nedan förutsätter vi att du har skapat ett namn på en avbildnings-pull `regcred` som anges i Kubernetes-dokumentationen.

```yaml
#just showing only the relevant part of the bootstrapper.yaml template file here
containers:
      - env:
        - name: ACCEPT_EULA
          value: "Y"
        #image: mcr.microsoft.com/arcdata/arc-bootstrapper:public-preview-dec-2020  <-- template value to change
        image: <your registry DNS name or IP address>/<your repo>/arc-bootstrapper:<your tag>
        imagePullPolicy: IfNotPresent
        name: bootstrapper
        resources: {}
        securityContext:
          runAsUser: 21006
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: regcred
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: sa-mssql-controller
      serviceAccountName: sa-mssql-controller
      terminationGracePeriodSeconds: 30

```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>Skapa en hemlighet för data styrenhets administratören

Användar namn och lösen ord för data kontroll enheten används för att autentisera till datakontrollantens API för att utföra administrativa funktioner.  Välj ett säkert lösen ord och dela det med bara de som behöver ha kluster administratörs behörighet.

En Kubernetes-hemlighet lagras som en Base64-kodad sträng – en för användar namnet och en för lösen ordet.

Du kan använda ett online-verktyg för att koda ditt önskade användar namn och lösen ord, eller så kan du använda inbyggda CLI-verktyg beroende på din plattform.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

När du har kodat användar namn och lösen ord kan du skapa en fil baserat på [mallfilen](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/controller-login-secret.yaml) och ersätta värdena för användar namn och lösen ord med dina egna.

Kör sedan följande kommando för att skapa hemligheten.

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>Skapa data kontrollen

Nu är du redo att skapa själva datakontrollanten.

Börja med att skapa en kopia av [mallfilen](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/data-controller.yaml) lokalt på datorn så att du kan ändra några av inställningarna.

Redigera följande efter behov:

**KUNNA**
- **plats**: ändra till den Azure-plats där _metadata_ om data styrenheten ska lagras.  Du kan se en lista över tillgängliga Azure-platser i artikeln [skapa datacontroller-översikt](create-data-controller.md) .
- **resourceGroup**: Azure-resurs gruppen där du vill skapa Azure-resursen data controller i Azure Resource Manager.  Normalt bör den här resurs gruppen redan finnas, men den är inte obligatorisk förrän den tid som du överför data till Azure.
- **prenumeration**: GUID för Azure-prenumerationen för den prenumeration som du vill skapa Azure-resurserna i.

**REKOMMENDERAS FÖR ATT GRANSKA OCH EVENTUELLT ÄNDRA STANDARDVÄRDEN**
- **lagring.. className**: den lagrings klass som ska användas för datakontrollantens data och loggfiler.  Om du är osäker på tillgängliga lagrings klasser i Kubernetes-klustret kan du köra följande kommando: `kubectl get storageclass` .  Standardvärdet är att `default` det finns en lagrings klass som finns och som har ett namn som `default` inte är en lagrings klass som _är_ standard.  Obs! det finns två className-inställningar som ska anges till önskad lagrings klass – en för data och en för loggar.
- **ServiceType**: ändra tjänst typen till `NodePort` om du inte använder en Loadbalancer.  Obs: det finns två serviceType-inställningar som måste ändras.
- På Azure Red Hat OpenShift eller Red Hat OpenShift container Platform måste du tillämpa begränsningen för säkerhets kontext innan du skapar data kontrollen. Följ anvisningarna i [tillämpa en säkerhets kontext begränsning för Azure Arc-aktiverade data tjänster i OpenShift](how-to-apply-security-context-constraint.md).
- **Säkerhet** Ersätt `security:` inställningarna med följande värden i data Controller yaml-filen för Azure Red Hat OpenShift eller Red Hat OpenShift container Platform. 

```yml
  security:
    allowDumps: true
    allowNodeMetricsCollection: false
    allowPodMetricsCollection: false
    allowRunAsRoot: false
```

**VALFRITT**
- **namn**: standard namnet för datakontrollanten är `arc` , men du kan ändra den om du vill.
- **DisplayName**: Ange värdet till samma värde som attributet name överst i filen.
- **register**: Microsoft container Registry är standard.  Om du hämtar bilderna från Microsoft Container Registry och skickar [dem till ett privat behållar register](offline-deployment.md), anger du IP-adressen eller DNS-namnet för ditt register här.
- **dockerRegistry**: den image-pull-hemlighet som ska användas för att hämta avbildningar från ett privat behållar register om det behövs.
- **databas**: standard lagrings platsen på Microsoft container Registry är `arcdata` .  Om du använder ett privat behållar register anger du sökvägen till den mapp/lagrings plats som innehåller de avbildningar av Azure Arc-aktiverade data Services-behållare.
- **imageTag**: den aktuella senaste version-taggen är standard i mallen, men du kan ändra den om du vill använda en äldre version.

I följande exempel visas en slutförd datakontrollants yaml-fil. Uppdatera exemplet för din miljö baserat på dina krav och informationen ovan.

```yaml
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    #dockerRegistry: arc-private-registry - optional if you are using a private container registry that requires authentication using an image pull secret
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: public-preview-dec-2020 
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: Indirect
      location: eastus
      resourceGroup: myresourcegroup
      subscription: c82c901a-129a-435d-86e4-cc6b294590ae
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default
      size: 10Gi
```

Spara den redigerade filen på den lokala datorn och kör följande kommando för att skapa data kontrollen:

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>Övervaka skapande status

Det tar några minuter att slutföra skapandet av kontrollanten. Du kan övervaka förloppet i ett annat terminalfönster med följande kommandon:

> [!NOTE]
>  Exempel kommandona nedan förutsätter att du har skapat en datakontrollant och ett Kubernetes-namnområde med namnet `arc` .  Om du har använt ett annat namn på namn område/data enhet kan du ersätta `arc` med ditt namn.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Du kan också kontrol lera skapande status för alla specifika Pod genom att köra ett kommando som nedan.  Detta är särskilt användbart vid fel sökning av problem.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

Azure Arc-tillägget för Azure Data Studio ger en bärbar dator som vägleder dig genom hur du konfigurerar Azure Arc-aktiverade Kubernetes och konfigurerar den för att övervaka en git-lagringsplats som innehåller en exempel fil med SQL-hanterad instans yaml. När allt är anslutet distribueras en ny SQL-hanterad instans till ditt Kubernetes-kluster.

Se **distribuera en SQL-hanterad instans med Azure Arc-aktiverade Kubernetes och flödes** antecknings böcker i Azure Arc-tillägget för Azure Data Studio.

## <a name="troubleshooting-creation-problems"></a>Fel sökning av problem med skapande

Om du stöter på problem med att skapa kan du läsa [fel söknings guiden](troubleshoot-guide.md).

## <a name="next-steps"></a>Nästa steg

- [Skapa en SQL-hanterad instans med Kubernetes-inbyggda verktyg](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Skapa en PostgreSQL-Server grupp med Kubernetes-inbyggda verktyg](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
