---
title: Förbereda ett program för distribution i Azure våren Cloud
description: Lär dig hur du förbereder ett program för distribution till Azure våren Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 4e9c84efe7b96cf61a69c54e3f5ecbc469ac7d8d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98874150"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Förbereda ett program för distribution i Azure våren Cloud

::: zone pivot="programming-language-csharp"
Azure våren Cloud tillhandahåller robusta tjänster för att vara värd för, övervaka, skala och uppdatera en Steeltoe-app. Den här artikeln visar hur du förbereder ett befintligt Steeltoe-program för distribution till Azure våren Cloud. 

I den här artikeln beskrivs beroenden, konfigurationen och koden som krävs för att köra en .NET Core Steeltoe-app i Azure våren Cloud. Information om hur du distribuerar ett program till Azure våren Cloud finns i [distribuera ditt första Azure våren Cloud-program](spring-cloud-quickstart.md).

>[!Note]
> Steeltoe support för Azure våren Cloud erbjuds för närvarande som en offentlig för hands version. Med den offentliga för hands versionen kan kunder experimentera med nya funktioner före den officiella versionen.  Funktioner och tjänster för offentliga för hands versioner är inte avsedda för användning i produktion.  Mer information om stöd för för hands versionerna finns i [vanliga frågor och svar](https://azure.microsoft.com/support/faq/) eller filen [supportbegäran](../azure-portal/supportability/how-to-create-azure-support-request.md).

##  <a name="supported-versions"></a>Versioner som stöds

Azure våren Cloud stöder:

* .NET Core 3.1
* Steeltoe 2,4 och 3,0

## <a name="dependencies"></a>Beroenden

För Steeltoe 2,4 lägger du till det senaste paketet [Microsoft. Azure. SpringCloud. client 1. x. x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) i projekt filen:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

För Steeltoe 3,0 lägger du till det senaste paketet [Microsoft. Azure. SpringCloud. client 2. x.](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) x i projekt filen:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>Uppdatera program. CS

`Program.Main`Anropa metoden i-metoden `UseAzureSpringCloudService` .

För Steeltoe-2.4.4 anropar du `UseAzureSpringCloudService` efter `ConfigureWebHostDefaults` och efter `AddConfigServer` om det kallas:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer()
        .UseAzureSpringCloudService();
```

För Steeltoe-3.0.0 anropar du `UseAzureSpringCloudService` före `ConfigureWebHostDefaults` och före en Steeltoe-konfigurations kod:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .UseAzureSpringCloudService()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer();
```

## <a name="enable-eureka-server-service-discovery"></a>Aktivera identifiering av Eureka-Server

I konfigurations källan som ska användas när appen körs i Azure våren-molnet, anger `spring.application.name` du samma namn som Azure våren Cloud-appen som projektet ska distribueras till.

Om du till exempel distribuerar ett .NET-projekt `EurekaDataProvider` som heter en Azure våren Cloud-app som heter `planet-weather-provider` *appSettings.jspå* -filen, ska du inkludera följande JSON:

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>Använda tjänst identifiering

Om du vill anropa en tjänst med hjälp av identifiering av Eureka Server-tjänsten gör du HTTP-begäranden till `http://<app_name>` där `app_name` är värdet för `spring.application.name` mål programmet. Till exempel anropar följande kod `planet-weather-provider` tjänsten:

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```
::: zone-end

::: zone pivot="programming-language-java"
Det här avsnittet visar hur du förbereder ett befintligt Java våren-program för distribution till Azure våren Cloud. Om Azure våren Cloud har kon figurer ATS korrekt tillhandahåller robusta tjänster för att övervaka, skala och uppdatera ditt Java våren Cloud-program.

Innan du kör det här exemplet kan du prova den [grundläggande snabb](spring-cloud-quickstart.md)starten.

Andra exempel beskriver hur du distribuerar ett program till Azure våren Cloud när POM-filen har kon figurer ATS. 
* [Starta din första app](spring-cloud-quickstart.md)
* [Skapa och köra mikrotjänster](spring-cloud-quickstart-sample-app-introduction.md)

I den här artikeln beskrivs de nödvändiga beroendena och hur du lägger till dem i POM-filen.

## <a name="java-runtime-version"></a>Java Runtime-version

Endast våren/Java-program kan köras i Azure våren-molnet.

Azure våren Cloud stöder både Java 8 och Java 11. Värd miljön innehåller den senaste versionen av Azul Zulu OpenJDK för Azure. Mer information om Azul Zulu OpenJDK för Azure finns i [installera JDK](/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Moln versioner för våren start och våren

För att förbereda ett befintligt våren Boot-program för distribution till Azure våren-molnet ingår våren-start-och våren-moln beroenden i program POM-filen som visas i följande avsnitt.

Azure våren Cloud stöder bara våren Boot-appar, antingen våren Boot version 2,1 eller version 2,2. I följande tabell visas de kombinationer som stöds av våren-start och våren:

Start version för våren | Våren Cloud-version
---|---
2.2 | Hoxton.SR8
2.3 | Hoxton.SR8
2.4.1 + | 2020.0.0

> [!NOTE]
> Vi har identifierat ett problem med vår 2.4.0 på TLS-autentisering mellan dina appar och Eureka, Använd 2.4.1 eller senare. Läs våra [vanliga frågor och svar](./spring-cloud-faq.md?pivots=programming-language-java#development) om du insistera med 2.4.0.

### <a name="dependencies-for-spring-boot-version-2223"></a>Beroenden för våren Boot version 2.2/2.3

För våren Boot version 2,2 lägger du till följande beroenden i programmets POM-fil.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-24"></a>Beroenden för vår start version 2,4

För våren Boot version 2,2 lägger du till följande beroenden i programmets POM-fil.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.1.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>2020.0.0</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

> [!WARNING]
> Ange inte `server.port` i konfigurationen. Azure våren Cloud kommer att Overide den här inställningen till ett fast port nummer. Respektera även den här inställningen och ange inte Server porten i koden.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Andra rekommenderade beroenden för att aktivera moln funktioner i Azure våren

Om du vill aktivera de inbyggda funktionerna i Azure våren Cloud från tjänst registret till distribuerad spårning måste du även inkludera följande beroenden i ditt program. Du kan ta bort vissa av dessa beroenden om du inte behöver motsvarande funktioner för de olika apparna.

### <a name="service-registry"></a>Tjänst register

Om du vill använda den hanterade tjänsten för Azure-tjänsten ska du inkludera `spring-cloud-starter-netflix-eureka-client` beroendet i pom.xml-filen som visas här:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Slut punkten för tjänst registrerings servern matas automatiskt in som miljövariabler med din app. Program kan registrera sig själva med tjänstens register Server och identifiera andra beroende mikrotjänster.


#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient-anteckning

Lägg till följande anteckning i program käll koden.
```java
@EnableDiscoveryClient
```
Se till exempel programmet piggymetrics från tidigare exempel:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>Distribuerad konfiguration

Om du vill aktivera distribuerad konfiguration inkluderar du följande beroende `spring-cloud-config-client` i avsnittet beroenden i pom.xml-filen:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Ange inte `spring.cloud.config.enabled=false` i Start konfigurationen. Annars slutar programmet att fungera med konfigurations servern.

### <a name="metrics"></a>Mått

Inkludera `spring-boot-starter-actuator` beroendet i avsnittet beroenden i pom.xml-filen som visas här:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Måtten hämtas regelbundet från JMX-slutpunkterna. Du kan visualisera måtten med hjälp av Azure Portal.

 > [!WARNING]
 > Ange `spring.jmx.enabled=true` i konfigurations egenskapen. Annars kan inte måtten visualiseras i Azure Portal.

### <a name="distributed-tracing"></a>Distribuerad spårning

Du måste också aktivera en Azure Application insikts-instans för att arbeta med din Azure våren Cloud Service-instans. Information om hur du använder Application Insights med Azure våren Cloud finns i [dokumentationen om distribuerad spårning](spring-cloud-tutorial-distributed-tracing.md).

#### <a name="spring-boot-2223"></a>Våren boot 2.2/2.3
Inkludera följande `spring-cloud-starter-sleuth` och `spring-cloud-starter-zipkin` beroenden i avsnittet beroenden i pom.xml-filen:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

#### <a name="spring-boot-24"></a>Våren boot 2,4
Ta med följande beroende `spring-cloud-sleuth-zipkin` i avsnittet beroenden i pom.xml-filen:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

## <a name="see-also"></a>Se även
* [Analysera program loggar och mått](./diagnostic-services.md)
* [Konfigurera konfigurationsservern](./spring-cloud-tutorial-config-server.md)
* [Använd distribuerad spårning med Azure våren Cloud](./spring-cloud-tutorial-distributed-tracing.md)
* [Guide för våren-snabb start](https://spring.io/quickstart)
* [Dokumentation om våren Boot](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Nästa steg

I det här avsnittet har du lärt dig hur du konfigurerar ditt Java våren-program för distribution till Azure våren Cloud. Information om hur du konfigurerar en konfigurations Server instans finns i [Konfigurera en konfigurations Server instans](spring-cloud-tutorial-config-server.md).

Fler exempel finns på GitHub: [Azure våren Cloud-exempel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end