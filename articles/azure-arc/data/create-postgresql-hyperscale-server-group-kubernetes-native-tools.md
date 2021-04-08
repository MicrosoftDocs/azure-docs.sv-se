---
title: Skapa en PostgreSQL-Server grupp med Kubernetes-verktyg
description: Skapa en PostgreSQL-Server grupp med Kubernetes-verktyg
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: fd1b74d33793c06e586a92cc8b2e8d2d36f4827a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519970"
---
# <a name="create-a-postgresql-hyperscale-server-group-using-kubernetes-tools"></a>Skapa en PostgreSQL-Server grupp med Kubernetes-verktyg

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Du bör redan ha skapat en [data styrenhet för Azure-bågen](./create-data-controller.md).

Om du vill skapa en PostgreSQL Server grupp med Kubernetes-verktyg måste du ha installerat Kubernetes-verktygen.  Exemplen i den här artikeln kommer att använda `kubectl` , men liknande metoder kan användas med andra Kubernetes-verktyg som Kubernetes-instrumentpanelen, `oc` eller `helm` om du är van vid dessa verktyg och Kubernetes yaml/JSON.

[Installera kubectl-verktyget](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Översikt

Om du vill skapa en PostgreSQL-Server grupp måste du skapa en Kubernetes-hemlighet för att lagra din postgres-Administratörs inloggning och lösen ord på ett säkert sätt och en PostgreSQL Server grupp för Server grupper baserat på anpassade resurs definitioner för postgresql-12 eller postgresql-11.

## <a name="create-a-yaml-file"></a>Skapa en yaml-fil

Du kan använda [mallens yaml](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/postgresql.yaml) -fil som start punkt för att skapa en egen anpassad postgresql yaml-fil för Server gruppen.  Ladda ned den här filen till den lokala datorn och öppna den i en text redigerare.  Det är praktiskt att använda en text redigerare, t. ex. [vs-kod](https://code.visualstudio.com/download) som stöder markering av syntax och luddfri för yaml-filer.

Detta är ett exempel på en yaml-fil:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
kind: Secret
metadata:
  name: pg1-login-secret
type: Opaque
---
apiVersion: arcdata.microsoft.com/v1alpha1
kind: postgresql-12
metadata:
  generation: 1
  name: pg1
spec:
  engine:
    extensions:
    - name: citus
  scale:
    shards: 3
  scheduling:
    default:
      resources:
        limits:
          cpu: "4"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>Anpassa inloggning och lösen ord.
En Kubernetes-hemlighet lagras som en Base64-kodad sträng – en för användar namnet och en för lösen ordet.  Du måste base64 koda en Administratörs inloggning och lösen ord och placera dem på plats hållaren på `data.password` och `data.username` .  Ta inte med `<` och `>` symbolerna som anges i mallen.

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

### <a name="customizing-the-name"></a>Anpassa namnet

Mallen har värdet ' SG1 ' för attributet name.  Du kan ändra detta, men det måste vara tecken som följer DNS-namngivnings standarderna.  Du måste också ändra namnet på den hemlighet som ska matchas.  Om du till exempel ändrar namnet på PostgreSQL-Server gruppen till "SG2" måste du ändra namnet på hemligheten från "SG1-login-Secret" till "SG2-login-Secret"

### <a name="customizing-the-engine-version"></a>Anpassa motor versionen

Du kan ändra motor versionen till antingen postgresql-11 eller postgresql-12 genom att redigera `kind` attributet.

### <a name="customizing-the-resource-requirements"></a>Anpassa resurs kraven

Du kan ändra resurs kraven – RAM-och kärn gränser och-begär Anden – efter behov.  

> [!NOTE]
> Du kan lära dig mer om [Kubernetes resurs styrning](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Krav för resurs gränser och begär Anden:
- Gräns värdet för kärnor **krävs** för fakturerings syfte.
- Resten av resurs förfrågningarna och gränserna är valfria.
- Kärnornas gräns och begär Ande måste vara ett positivt heltal, om det har angetts.
- Minst 1 kärna krävs för kärnor för begäran, om det anges.
- Minnes värdes formatet följer Kubernetes-notationen.  

### <a name="customizing-service-type"></a>Anpassa tjänst typ

Du kan ändra tjänst typen till NodePort om du vill.  Ett slumpmässigt port nummer kommer att tilldelas.

### <a name="customizing-storage"></a>Anpassa lagring

Du kan anpassa lagrings klasserna för lagring så att de överensstämmer med din miljö.  Om du inte är säker på vilka lagrings klasser som är tillgängliga kan du köra kommandot `kubectl get storageclass` för att visa dem.  Mallen har standardvärdet default.  Det innebär att det finns en lagrings klass _med namnet_ default, men det finns ingen lagrings klass som _är_ standard.  Du kan också ändra storleken på lagrings utrymmet.  Du kan läsa mer om [lagrings konfiguration](./storage-configuration.md).

## <a name="creating-the-postgresql-hyperscale-server-group"></a>Skapa server gruppen PostgreSQL-storskalig

Nu när du har anpassat PostgreSQL yaml-filen för kan du skapa PostgreSQL för den storskaliga Server gruppen genom att köra följande kommando:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\postgres.yaml
```


## <a name="monitoring-the-creation-status"></a>Övervaka skapande status

Det tar några minuter att skapa PostgreSQL-Server gruppen. Du kan övervaka förloppet i ett annat terminalfönster med följande kommandon:

> [!NOTE]
>  Exempel kommandona nedan förutsätter att du har skapat en PostgreSQL-Server grupp med namnet "SG1" och Kubernetes namespace med namnet "Arc".  Om du har använt ett annat namn på en namnrymd/PostgreSQL-Server grupp kan du ersätta "Arc" och "SG1" med dina namn.

```console
kubectl get postgresql-12/pg1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Du kan också kontrol lera skapande status för alla specifika Pod genom att köra ett kommando som nedan.  Detta är särskilt användbart vid fel sökning av problem.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/pg1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Fel sökning av problem med skapande

Om du stöter på problem med att skapa kan du läsa [fel söknings guiden](troubleshoot-guide.md).