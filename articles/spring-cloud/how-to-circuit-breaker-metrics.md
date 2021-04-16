---
title: Samla Spring Cloud för Resilience4J-kretsbrytare med Micrometer
description: Så här samlar Spring Cloud in mått för Resilience4J-kretsbrytare med Micrometer i Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: fedebd9182c168b9b7c455d5f6726e66720e0a8b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479167"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-with-micrometer-preview"></a>Samla Spring Cloud Resilience4J-kretsbrytarmått med Micrometer (förhandsversion)

Det här dokumentet beskriver hur du samlar in Spring Cloud Resilience4j Circuit Breaker Metrics med Application Insights java-agenten i processen. Med den här funktionen kan du övervaka mått för resilience4j-kretsbrytaren från Application Insights med Micrometer.

Vi använder [spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) för att visa hur det fungerar.

## <a name="prerequisites"></a>Förutsättningar

* Aktivera Java In-Process-agenten från [Java In-Process Agent för Application Insights guide](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights). 

* Aktivera dimensionsinsamling för resilience4j-mått från [Application Insights guiden](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

* Installera git, Maven och Java, om det inte redan används av utvecklingsdatorn.

## <a name="build-and-deploy-apps"></a>Skapa och distribuera appar

Följande procedur skapar och distribuerar appar.

1. Klona och skapa demodatabasen.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. Skapa program med slutpunkter

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
> * Kundkoden måste använda API:et för , som implementeras som en automatiskt skapad när du inkluderar `CircuitBreakerFactory` `bean` ett Spring Cloud Circuit Breaker Starter. Mer information finns [Spring Cloud kretsbrytaren](https://spring.io/projects/spring-cloud-circuitbreaker#overview).
>
> * Följande två beroenden har konflikter med resilient4j-paket ovan.  Se till att kunden inte inkluderar dem.
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
> Gå till den URL som tillhandahålls av gatewayprogram och gå till slutpunkten [från spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) på följande sätt:
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Leta upp Resilence4j-mått från portalen

1. Välj **bladet Application Insights** från Azure Spring Cloud portalen och klicka **på Application Insights**.

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. Välj **Mått** på **Application Insights** sidan.  Välj **azure.applicationinsights** från **Metrics Namespace**.  Välj även **resilience4j_circuitbreaker_buffered_calls** mått med **Medelvärde.**

   [![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. Välj **resilience4j_circuitbreaker_calls** mått och **Medelvärde.**

   [![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. Välj **resilience4j_circuitbreaker_calls** mått och **Medelvärde.**  Klicka **på Lägg** till filter och välj sedan namn som **createNewAccount**.

   [![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. Välj **resilience4j_circuitbreaker_calls** mått och **Medelvärde.**  Klicka sedan **på Tillämpa delning** och välj **typ**.

   [![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. Välj **resilience4j_circuitbreaker_calls,**"**resilience4j_circuitbreaker_buffered_calls** och **resilience4j_circuitbreaker_slow_calls** mått med **Genomsnitt**.

   [![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Se även

* [Application Insights](spring-cloud-howto-application-insights.md)
* [Distribuerad spårning](spring-cloud-howto-distributed-tracing.md)
* [Instrumentpanel för kretsbrytare](spring-cloud-tutorial-circuit-breaker.md)
