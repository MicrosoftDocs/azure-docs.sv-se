---
title: Skapa en SQL-hanterad instans med Kubernetes-verktyg
description: Skapa en SQL-hanterad instans med Kubernetes-verktyg
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: d23df80a3f80ed96779297bac12ef0ed8d2927d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687931"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>Skapa en hanterad Azure SQL-instans med Kubernetes-verktyg

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Du bör redan ha skapat en [data styrenhet för Azure-bågen](./create-data-controller.md).

Om du vill skapa en SQL-hanterad instans med Kubernetes-verktyg måste du ha Kubernetes-verktygen installerade.  Exemplen i den här artikeln kommer att använda `kubectl` , men liknande metoder kan användas med andra Kubernetes-verktyg som Kubernetes-instrumentpanelen, `oc` eller `helm` om du är van vid dessa verktyg och Kubernetes yaml/JSON.

[Installera kubectl-verktyget](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Översikt

Om du vill skapa en SQL-hanterad instans måste du skapa en Kubernetes-hemlighet för att lagra system administratörens inloggning och lösen ord på ett säkert sätt och en anpassad SQL-hanterad instans resurs baserat på den anpassade resurs definitionen för sqlmanagedinstance.

## <a name="create-a-yaml-file"></a>Skapa en yaml-fil

Du kan använda [mallens yaml](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/sqlmi.yaml) -fil som start punkt för att skapa en egen anpassad SQL-hanterad instans yaml-fil.  Ladda ned den här filen till den lokala datorn och öppna den i en text redigerare.  Det är praktiskt att använda en text redigerare, t. ex. [vs-kod](https://code.visualstudio.com/download) som stöder markering av syntax och luddfri för yaml-filer.

Detta är ett exempel på en yaml-fil:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded user name. 'sa' is not allowed>
kind: Secret
metadata:
  name: sql1-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
  name: sql1
spec:
  limits:
    memory: 4Gi
    vcores: "4"
  requests:
    memory: 2Gi
    vcores: "1"
  service:
    type: LoadBalancer
  storage:
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>Anpassa inloggning och lösen ord

En Kubernetes-hemlighet lagras som en Base64-kodad sträng – en för användar namnet och en för lösen ordet.  Du måste base64 koda en system Administratörs inloggning och lösen ord och placera dem på plats hållaren på `data.password` och `data.username` .  Ta inte med `<` och `>` symbolerna som anges i mallen.

> [!NOTE]
> För optimal säkerhet är det inte tillåtet att använda värdet sa för inloggningen.
> Följ [principen för lösen ords komplexitet](/sql/relational-databases/security/password-policy#password-complexity).

Du kan använda ett online-verktyg för att koda ditt önskade användar namn och lösen ord, eller så kan du använda inbyggda CLI-verktyg beroende på din plattform.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

### <a name="customizing-the-name"></a>Anpassa namnet

Mallen har värdet ' SQL1 ' för attributet name.  Du kan ändra detta, men det måste vara tecken som följer DNS-namngivnings standarderna.  Du måste också ändra namnet på den hemlighet som ska matchas.  Om du till exempel ändrar namnet på den SQL-hanterade instansen till "sql2" måste du ändra namnet på hemligheten från "SQL1-login-Secret" till "sql2-login-Secret"

### <a name="customizing-the-resource-requirements"></a>Anpassa resurs kraven

Du kan ändra resurs kraven – RAM-och kärn gränser och-begär Anden – efter behov.  

> [!NOTE]
> Du kan lära dig mer om [Kubernetes resurs styrning](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Krav för resurs gränser och begär Anden:
- Gräns värdet för kärnor **krävs** för fakturerings syfte.
- Resten av resurs förfrågningarna och gränserna är valfria.
- Kärnornas gräns och begär Ande måste vara ett positivt heltal, om det har angetts.
- Minst 2 kärnor krävs för kärnor, om det anges.
- Minnes värdes formatet följer Kubernetes-notationen.  
- Minst 2Gi krävs för minnes förfrågan, om det anges.
- Som en allmän rikt linje bör du ha 4 GB RAM-minne för varje 1 kärna för produktions användnings fall.

### <a name="customizing-service-type"></a>Anpassa tjänst typ

Du kan ändra tjänst typen till NodePort om du vill.  Ett slumpmässigt port nummer kommer att tilldelas.

### <a name="customizing-storage"></a>Anpassa lagring

Du kan anpassa lagrings klasserna för lagring så att de överensstämmer med din miljö.  Om du inte är säker på vilka lagrings klasser som är tillgängliga kan du köra kommandot `kubectl get storageclass` för att visa dem.  Mallen har standardvärdet default.  Det innebär att det finns en lagrings klass _med namnet_ default, men det finns ingen lagrings klass som _är_ standard.  Du kan också ändra storleken på lagrings utrymmet.  Du kan läsa mer om [lagrings konfiguration](./storage-configuration.md).

## <a name="creating-the-sql-managed-instance"></a>Skapar SQL-hanterad instans

Nu när du har anpassat SQL Managed instance yaml-filen kan du skapa SQL-hanterad instans genom att köra följande kommando:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```


## <a name="monitoring-the-creation-status"></a>Övervaka skapande status

Det tar några minuter att slutföra skapandet av SQL-hanterad instans. Du kan övervaka förloppet i ett annat terminalfönster med följande kommandon:

> [!NOTE]
>  Exempel kommandona nedan förutsätter att du har skapat en SQL-hanterad instans med namnet "SQL1" och Kubernetes-namnrymden med namnet "Arc".  Om du har använt ett annat namn på namnrymd/SQL-hanterad instans kan du ersätta "Arc" och "sqlmi" med dina namn.

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Du kan också kontrol lera skapande status för alla specifika Pod genom att köra ett kommando som nedan.  Detta är särskilt användbart vid fel sökning av problem.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Fel sökning av problem med skapande

Om du stöter på problem med att skapa kan du läsa [fel söknings guiden](troubleshoot-guide.md).

## <a name="next-steps"></a>Nästa steg

[Ansluta till Azure Arc-aktiverad SQL-hanterad instans](connect-managed-instance.md)
