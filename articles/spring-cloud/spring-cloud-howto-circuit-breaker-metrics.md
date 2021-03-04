---
title: Samla in våren Cloud Resilience4J krets separator mått
description: Så här samlar du in mått för våren Cloud Resilience4Js krets brytare.
author: MikeDodaro
ms.author: brendanm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.openlocfilehash: 153f99d100ea1a456f960293f3806a739fe1d593
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038738"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-preview"></a>Samla in våren Cloud Resilience4J krets separator mått (för hands version)

I det här dokumentet beskrivs hur du samlar in moln Resilience4js krets avbrotts mått med Application Insights Java in process-agenten.  Med den här funktionen kan du övervaka mått för resilience4j krets brytare från Application Insights.

Vi använder lösningen [våren-Cloud-krets-Break-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) för att visa hur det fungerar.

## <a name="prerequisites"></a>Förutsättningar

* Aktivera Java In-Process agent från [Java-In-Process agent för Application Insights guide](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights). 

* Aktivera dimensions insamling för resilience4j-mått från [Application Insightss guiden](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

* Installera git, Maven och Java, om det inte redan används av utvecklings datorn.

## <a name="build-and-deploy-apps"></a>Skapa och distribuera appar

Följande procedur skapar och distribuerar appar.

1. Klona och bygga demo lagret.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. Skapa program med slut punkter

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. Distribuera program.

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * Inkludera det nödvändiga beroendet för Resilience4j:
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
> * Kund koden måste använda API: t för `CircuitBreakerFactory` , som implementeras `bean` automatiskt när du tar med ett fjäder moln krets avbrotts start. Mer information finns i [vår moln krets brytare](https://spring.io/projects/spring-cloud-circuitbreaker#overview).
>
> * Följande 2-beroenden har konflikter med resilient4j-paket ovan.  Se till att kunden inte inkluderar dem.
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> Navigera till den URL som tillhandahålls av Gateway-program och få åtkomst till slut punkten från [våren-Cloud-kretsen-demo-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) på följande sätt:
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Hitta Resilence4j-mått från portalen

1. Välj bladet **Application Insights** från Azure våren Cloud Portal och klicka på **Application Insights**.

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. Välj **mått** från **Application Insights** sidan.  Välj **Azure. applicationinsights** från **namn området mått**.  Välj också **resilience4j_circuitbreaker_buffered_calls** mått med **genomsnitt**.

   [![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. Välj **resilience4j_circuitbreaker_calls** mått och **genomsnitt**.

   [![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. Välj **resilience4j_circuitbreaker_calls**  mått och **genomsnitt**.  Klicka på **Lägg till filter** och välj sedan namn som **createNewAccount**.

   [![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. Välj **resilience4j_circuitbreaker_calls**  mått och **genomsnitt**.  Klicka sedan på **tillämpa delning** och välj **typ**.

   [![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. Välj **resilience4j_circuitbreaker_calls**,**resilience4j_circuitbreaker_buffered_calls** och **resilience4j_circuitbreaker_slow_calls** mått med **genomsnitt**.

   [![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Se även

* [Application Insights](./spring-cloud-howto-application-insights.md)
* [Distribuerad spårning](spring-cloud-tutorial-distributed-tracing.md)
* [Krets brytarens instrument panel](spring-cloud-tutorial-circuit-breaker.md)