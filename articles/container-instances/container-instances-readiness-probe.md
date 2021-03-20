---
title: Konfigurera readiness PROBE på behållar instansen
description: Lär dig hur du konfigurerar en avsökning för att se till att behållare i Azure Container Instances ta emot begär Anden endast när de är klara
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 3e89086d66f284df35e36dc8f1d68bb09264843f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86169671"
---
# <a name="configure-readiness-probes"></a>Konfigurera beredskapsavsökning

För behållar program som hanterar trafik, kanske du vill kontrol lera att din behållare är redo att hantera inkommande begär Anden. Azure Container Instances stöder beredskaps avsökningar för att inkludera konfigurationer så att din behållare inte kan nås under vissa förhållanden. Beredskaps avsökningen fungerar som en [Kubernetes readiness PROBE](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/). Till exempel kan en container app behöva läsa in en stor data uppsättning under starten och du vill inte ta emot begär Anden under den här tiden.

Den här artikeln beskriver hur du distribuerar en behållar grupp som innehåller en beredskaps avsökning, så att en behållare endast tar emot trafik när avsökningen lyckas.

Azure Container Instances också stöd för [direktmigreringar](container-instances-liveness-probe.md), som du kan konfigurera för att orsaka att en behållare som inte är felfria startar om automatiskt.

> [!NOTE]
> För närvarande kan du inte använda en beredskaps avsökning i en behållar grupp som distribueras till ett virtuellt nätverk.

## <a name="yaml-configuration"></a>YAML-konfiguration

Skapa till exempel en `readiness-probe.yaml` fil med följande kodfragment som innehåller en beredskaps avsökning. Den här filen definierar en behållar grupp som består av en behållare som kör en liten webbapp. Appen distribueras från den offentliga `mcr.microsoft.com/azuredocs/aci-helloworld` avbildningen. Den här appen med behållare visas också i [distribuera en behållar instans i Azure med hjälp av Azure CLI](container-instances-quickstart.md) och andra snabb starter.

```yaml
apiVersion: 2019-12-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Start kommando

Distributionen innehåller en `command` egenskap som definierar ett start kommando som körs när behållaren först börjar köras. Den här egenskapen accepterar en sträng mat ris. Det här kommandot simulerar en tidpunkt då webbappen körs men behållaren inte är klar. 

Först startar den en Shell-session och kör ett `node` -kommando för att starta webbappen. Det startar också ett kommando till vilo läge i 240 sekunder, och sedan skapas en fil som heter `ready` i `/tmp` katalogen:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Beredskaps kommando

Den här YAML-filen definierar en `readinessProbe` som stöder ett `exec` beredskaps kommando som fungerar som beredskaps kontroll. Det här exempel kommandot testar för att det finns en `ready` fil i `/tmp` katalogen.

När `ready` filen inte finns, avslutas beredskaps kommandot med ett värde som inte är noll, behållaren fortsätter att köras men går inte att komma åt. När kommandot avslutas med slut koden 0, är behållaren klar att nås. 

`periodSeconds`Egenskapen anger att beredskaps kommandot ska köras var 5: e sekund. Beredskaps avsökningen körs för behållar gruppens livs längd.

## <a name="example-deployment"></a>Exempel distribution

Kör följande kommando för att distribuera en behållar grupp med föregående YAML-konfiguration:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Visa beredskaps kontroller

I det här exemplet, under de första 240 sekunderna, fungerar inte kommandot readiness när det söker efter `ready` filens existens. Status koden returnerade signaler om att behållaren inte är klar.

Dessa händelser kan visas från Azure Portal eller Azure CLI. Portalen visar t. ex. händelser av typen `Unhealthy` utlöses när beredskaps kommandot inte fungerar. 

![Felaktig händelse för portalen][portal-unhealthy]

## <a name="verify-container-readiness"></a>Verifiera behållar beredskap

När du har startat behållaren kan du kontrol lera att den inte är tillgänglig från början. Hämta IP-adressen för behållar gruppen efter etableringen:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Försök att komma åt webbplatsen medan beredskaps avsökningen Miss lyckas:

```bash
wget <ipAddress>
```

Utdata visar att webbplatsen inte går att komma åt från början:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

Efter 240 sekunder slutförs beredskaps kommandot och signalerar att behållaren är klar. När du kör `wget` kommandot slutförs nu:

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

När behållaren är klar kan du också komma åt webbappen genom att bläddra till IP-adressen med hjälp av en webbläsare.

> [!NOTE]
> Beredskaps avsökningen fortsätter att köras under behållar gruppens livs längd. Om kommandot beredskapen Miss lyckas vid ett senare tillfälle blir behållaren återigen otillgänglig. 
> 

## <a name="next-steps"></a>Nästa steg

En beredskaps avsökning kan vara användbar i scenarier som omfattar grupper med flera behållare som består av beroende behållare. Mer information om scenarier med flera behållare finns [i behållar grupper i Azure Container instances](container-instances-container-groups.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
