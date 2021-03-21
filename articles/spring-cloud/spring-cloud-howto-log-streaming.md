---
title: Strömma apploggar för Azure Spring Cloud i realtid
description: Använda logg strömning för att visa program loggar direkt
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1eeb291c7a058efd8905e95ebf1ea14fed046691
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98680526"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Strömma apploggar för Azure Spring Cloud i realtid

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Azure våren Cloud gör det möjligt att logga strömning i Azure CLI för att få real tids program konsol loggar för fel sökning. Du kan också [Analysera loggar och mått med diagnostikinställningar](./diagnostic-services.md).

## <a name="prerequisites"></a>Förutsättningar

* Installera [Azure CLI-tillägget](/cli/azure/install-azure-cli) för våren Cloud, lägsta version 0.2.0.
* En instans av **Azure våren Cloud** med ett program som körs, till exempel [våren Cloud App](./spring-cloud-quickstart.md).

> [!NOTE]
>  ASC CLI-tillägget har uppdaterats från version 0.2.0 till 0.2.1. Den här ändringen påverkar syntaxen för kommandot för logg strömning: `az spring-cloud app log tail` , som ersätts av: `az spring-cloud app logs` . Kommandot: `az spring-cloud app log tail` kommer att bli inaktuellt i en framtida version. Om du har använt version 0.2.0 kan du uppgradera till 0.2.1. Ta först bort den gamla versionen med kommandot: `az extension remove -n spring-cloud` .  Installera sedan 0.2.1 med kommandot: `az extension add -n spring-cloud` .

## <a name="use-cli-to-tail-logs"></a>Använd CLI för att logga loggar

Om du vill undvika att flera gånger specificera resurs gruppen och tjänst instans namnet anger du standard resurs grupps namn och kluster namn.
```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
I följande exempel kommer resurs gruppen och tjänst namnet att utelämnas i kommandona.

### <a name="tail-log-for-app-with-single-instance"></a>Pilslut för app med en enda instans
Om en app som heter auth-service bara har en instans, kan du visa loggen för App-instansen med följande kommando:
```azurecli
az spring-cloud app logs -n auth-service
```
Då returneras loggar:
```output
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Pilslut för app med flera instanser
Om det finns flera instanser för appen med namnet `auth-service` kan du Visa instans loggen med hjälp av `-i/--instance` alternativet. 

Först kan du hämta instans namnen för appen med följande kommando.

```azurecli
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Med resultat:

```output
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Sedan kan du strömma loggar för en app-instans med alternativet alternativ `-i/--instance` :

```azurecli
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Du kan också få information om App-instanser från Azure Portal.  När du har valt **appar** i det vänstra navigerings fönstret i moln tjänsten Azure våren väljer du **App-instanser**.

### <a name="continuously-stream-new-logs"></a>Strömma nya loggar kontinuerligt
Som standard `az spring-cloud ap log tail` skriver ut endast befintliga loggar som strömmas till app-konsolen och sedan avslutas. Om du vill strömma nya loggar lägger du till-f (--följ):  

```azurecli
az spring-cloud app logs -n auth-service -f
``` 
Så här kontrollerar du vilka loggnings alternativ som stöds:
```azurecli
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>Nästa steg
* [Snabb start: övervaka Azure våren Cloud-appar med loggar, mått och spårning](spring-cloud-quickstart-logs-metrics-tracing.md)
* [Analysera loggar och mått med diagnostikinställningar](./diagnostic-services.md)

