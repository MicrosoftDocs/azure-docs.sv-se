---
title: Självstudie – använda krets brytarens instrument panel med Azure våren Cloud
description: Lär dig hur du använder krets brytar-instrumentpanelen med Azure våren Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 9fbd137f8fa36a7b0526b25d664fceac795ecd81
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879209"
---
# <a name="tutorial-use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>Självstudie: använda krets brytar-instrumentpanelen med Azure våren Cloud

**Den här artikeln gäller för:** ✔️ Java

Våren [Cloud Netflix turbin](https://github.com/Netflix/Turbine) används ofta för att aggregera flera [hystrix](https://github.com/Netflix/Hystrix) -dataströmmar så att strömmar kan övervakas i en enda vy med hjälp av hystrix-instrumentpanelen. Den här självstudien visar hur du använder dem i Azure våren Cloud.
> [!NOTE]
> Netflix hystrix används ofta i många befintliga våren-molnappar, men är inte längre i aktiv utveckling. Om du utvecklar ett nytt projekt ska du använda i stället våren-implementeringar av moln krets brytare som [resilience4j](https://github.com/resilience4j/resilience4j). Annorlunda än den turbin som visas i den här självstudien, kombinerar det nya molnet för moln Kretss ramverk till micrometer. Vi arbetar fortfarande med stöd för micrometer i Azure våren-molnet och kommer därför inte att omfattas av den här självstudien.

## <a name="prepare-your-sample-applications"></a>Förbereda dina exempel program
Exemplet är förgrenat från den här [lagrings platsen](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine).

Klona exempel lagrings platsen till utveckla-miljön:
```
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

Bygg de tre programmen som ska användas i den här självstudien:
* User-Service: en enkel REST-tjänst som har en enda slut punkt av/Personalized/{ID}
* rekommendation – tjänst: en enkel REST-tjänst som har en enda slut punkt av/recommendations, som kommer att anropas av användar tjänsten.
* hystrix-turbin: en hystrix-instrumentpanel som visar hystrix-strömmar och en turbin-tjänst som aggregerar hystrix-dataströmmar från andra tjänster.
```
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```
## <a name="provision-your-azure-spring-cloud-instance"></a>Etablera din Azure våren Cloud-instans
Följ proceduren och [etablera en tjänst instans i Azure CLI](./spring-cloud-quickstart.md#provision-an-instance-of-azure-spring-cloud).

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>Distribuera dina program till Azure våren Cloud
Dessa appar använder inte **konfigurations servern**, så du behöver inte konfigurera **konfigurations servern** för Azure våren Cloud.  Skapa och distribuera på följande sätt:
```azurecli
az spring-cloud app create -n user-service --assign-endpoint
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --assign-endpoint

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```
## <a name="verify-your-apps"></a>Verifiera dina appar
När alla appar är igång och kan identifieras kan du komma åt `user-service` med sökvägen https:// <username> -User-Service.azuremicroservices.io/Personalized/1 från din webbläsare. Om användar tjänsten har åtkomst kan `recommendation-service` du få följande utdata. Uppdatera webb sidan några få gånger om den inte fungerar.
```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```
## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>Få åtkomst till din hystrix-instrumentpanel och mått ström
Verifiera användningen av offentliga slut punkter eller privata test slut punkter.

### <a name="using-public-endpoints"></a>Använda offentliga slut punkter
Få åtkomst till hystrix-turbin med sökvägen `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/hystrix` från din webbläsare.  Följande bild visar hystrix-instrumentpanelen som körs i den här appen.

![Hystrix-instrumentpanel](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

Kopiera den turbin Stream-URL: en `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default` till text rutan och klicka på **övervaka data ström**.  Då visas instrument panelen. Om inget visas i visnings programmet, tryck på `user-service` slut punkterna för att generera strömmar.

![Hystrix Stream ](media/spring-cloud-circuit-breaker/hystrix-stream.png) nu kan du experimentera med krets brytar instrument panelen.
> [!NOTE] 
> I produktion ska hystrix-instrumentpanelen och mått data strömmen inte exponeras för Internet.

### <a name="using-private-test-endpoints"></a>Använda privata test slut punkter
Hystrixs mått strömmar är också tillgängliga från `test-endpoint` . Som en backend-tjänst har vi inte tilldelat en offentlig slut punkt för `recommendation-service` , men vi kan visa sina mått med test-Endpoint på `https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream`

![Hystrix-test – slut punkts data ström](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

Hystrix-instrumentpanelen bör fungera på som en webbapp `test-endpoint` . Om den inte fungerar som den ska kan det finnas två anledningar: första, med `test-endpoint` ändrade bas-URL: en från `/ to /<APP-NAME>/<DEPLOYMENT-NAME>` , eller andra, webbappen använder en absolut sökväg för statisk resurs. Om du vill att det ska fungera kan `test-endpoint` du behöva redigera <base> i klientens filer manuellt.

## <a name="next-steps"></a>Nästa steg
* [Etablera en tjänst instans på Azure CLI](spring-cloud-quickstart.md#provision-an-instance-of-azure-spring-cloud)
* [Förbereda ett Java våren-program för distribution i Azure våren Cloud](how-to-prepare-app-deployment.md)

