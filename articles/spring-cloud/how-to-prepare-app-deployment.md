---
title: Förbereda ett program för distribution i Azure Spring Cloud
description: Lär dig hur du förbereder ett program för distribution till Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: cabc4784dfb19f569212f4d0cb93e6838473e559
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714496"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Förbereda ett program för distribution i Azure Spring Cloud

::: zone pivot="programming-language-csharp"
Azure Spring Cloud tillhandahåller robusta tjänster för att vara värd för, övervaka, skala och uppdatera en Steeltoe-app. Den här artikeln visar hur du förbereder ett befintligt Azure Spring Cloud. 

I den här artikeln beskrivs de beroenden, konfiguration och kod som krävs för att köra en .NET Core Configurationtoe-app i Azure Spring Cloud. Information om hur du distribuerar ett program till Azure Spring Cloud finns i [Distribuera ditt Azure Spring Cloud program](spring-cloud-quickstart.md).

>[!Note]
> Stöd för olika tjänster Azure Spring Cloud för närvarande som en offentlig förhandsversion. Med offentliga förhandsversioner kan kunder experimentera med nya funktioner innan de släpps officiellt.  Funktioner och tjänster i den offentliga förhandsversionen är inte avsedda för produktionsanvändning.  Mer information om support under förhandsversioner finns i Vanliga frågor [och svar](https://azure.microsoft.com/support/faq/) eller skapa en [Supportbegäran](../azure-portal/supportability/how-to-create-azure-support-request.md).

##  <a name="supported-versions"></a>Versioner som stöds

Azure Spring Cloud stöder:

* .NET Core 3.1
* Metalltoe 2.4 och 3.0

## <a name="dependencies"></a>Beroenden

För Steeltoe 2.4 lägger du till det senaste [Microsoft.Azure.SpringCloud.Client 1.x.x-paketet](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) i projektfilen:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

För Steeltoe 3.0 lägger du till det senaste [Microsoft.Azure.SpringCloud.Client 2.x.x-paketet](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) i projektfilen:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>Uppdatera Program.cs

I metoden `Program.Main` anropar du `UseAzureSpringCloudService` metoden .

För Metalltoe 2.4.4 `UseAzureSpringCloudService` anropar du efter `ConfigureWebHostDefaults` och efter om den `AddConfigServer` anropas:

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

För Metalltoe 3.0.0 anropar du `UseAzureSpringCloudService` före och före någon `ConfigureWebHostDefaults` Configurationtoe-konfigurationskod:

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

## <a name="enable-eureka-server-service-discovery"></a>Aktivera identifiering av Tjänsten Eureka Server

I konfigurationskällan som ska användas när appen körs i Azure Spring Cloud anger du till samma namn som den Azure Spring Cloud som projektet ska `spring.application.name` distribueras till.

Om du till exempel distribuerar ett .NET-projekt `EurekaDataProvider` med namnet till en Azure Spring Cloud-app med namnetappSettings.js`planet-weather-provider` *på* filen ska innehålla följande JSON:

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>Använda tjänstidentifiering

Om du vill anropa en tjänst med hjälp av tjänstidentifiering för Eureka Server, gör du HTTP-begäranden till där `http://<app_name>` `app_name` är värdet för `spring.application.name` målappen. Följande kod anropar till exempel `planet-weather-provider` tjänsten:

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
Det här avsnittet visar hur du förbereder ett befintligt Java Spring-program för distribution till Azure Spring Cloud. Om den är korrekt konfigurerad Azure Spring Cloud robusta tjänster för att övervaka, skala och uppdatera Java Spring Cloud program.

Innan du kör det här exemplet kan du prova den [grundläggande snabbstarten](spring-cloud-quickstart.md).

Andra exempel förklarar hur du distribuerar ett program Azure Spring Cloud när POM-filen har konfigurerats. 
* [Starta din första app](spring-cloud-quickstart.md)
* [Skapa och köra mikrotjänster](spring-cloud-quickstart-sample-app-introduction.md)

Den här artikeln förklarar de nödvändiga beroendena och hur du lägger till dem i POM-filen.

## <a name="java-runtime-version"></a>Java Runtime-version

Endast Spring-/Java-program kan köras Azure Spring Cloud.

Azure Spring Cloud stöder både Java 8 och Java 11. Värdmiljön innehåller den senaste versionen av Azul Zulu OpenJDK för Azure. Mer information om Azul Zulu OpenJDK för Azure finns [i Installera JDK.](/azure/developer/java/fundamentals/java-jdk-install)

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot och Spring Cloud versioner

För att förbereda ett Spring Boot-program för distribution till Azure Spring Cloud inkluderar du Spring Boot- och Spring Cloud-beroenden i POM-filen för programmet enligt följande avsnitt.

Azure Spring Cloud stöder Spring Boot version 2.2, 2.3, 2.4. I följande tabell visas de kombinationer Spring Boot och Spring Cloud stöds:

Spring Boot version | Spring Cloud version
---|---
2.2 | Hoxton.SR8
2.3 | Hoxton.SR8
2.4.1+ | 2020.0.0

> [!NOTE]
> Vi har identifierat ett problem med Spring Boot 2.4.0 vid TLS-autentisering mellan dina appar och Eureka. Använd 2.4.1 eller högre. I våra vanliga frågor [och svar](./spring-cloud-faq.md?pivots=programming-language-java#development) hittar du en lösning om du vill använda 2.4.0.

### <a name="dependencies-for-spring-boot-version-2223"></a>Beroenden för Spring Boot version 2.2/2.3

För Spring Boot version 2.2 lägger du till följande beroenden i POM-filen för programmet.

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

### <a name="dependencies-for-spring-boot-version-24"></a>Beroenden för Spring Boot version 2.4

För Spring Boot version 2.2 lägger du till följande beroenden i POM-filen för programmet.

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
> Ange inte `server.port` i konfigurationen. Azure Spring Cloud över den här inställningen till ett fast portnummer. Respektera även den här inställningen och ange inte serverporten i koden.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Andra rekommenderade beroenden för att Azure Spring Cloud funktioner

Om du vill aktivera de inbyggda funktionerna i Azure Spring Cloud från tjänstregistret till distribuerad spårning måste du även inkludera följande beroenden i ditt program. Du kan ta bort vissa av dessa beroenden om du inte behöver motsvarande funktioner för de specifika apparna.

### <a name="service-registry"></a>Service Registry

Om du vill använda den hanterade Azure Service Registry-tjänsten `spring-cloud-starter-netflix-eureka-client` inkluderar du beroendet i pom.xml som visas här:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Slutpunkten för Service Registry-servern matas automatiskt in som miljövariabler med din app. Program kan registrera sig själva med Service Registry-servern och identifiera andra beroende mikrotjänster.


#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient-anteckning

Lägg till följande anteckning i programmets källkod.
```java
@EnableDiscoveryClient
```
Du kan till exempel se programmet symmetrymetrics från tidigare exempel:
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

Om du vill aktivera distribuerad konfiguration inkluderar du `spring-cloud-config-client` följande beroende i avsnittet beroenden i din pom.xml fil:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Ange inte i `spring.cloud.config.enabled=false` din bootstrap-konfiguration. Annars slutar programmet att fungera med Config Server.

### <a name="metrics"></a>Mått

Inkludera `spring-boot-starter-actuator` beroendet i avsnittet beroenden i din pom.xml som visas här:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Mått hämtas regelbundet från JMX-slutpunkterna. Du kan visualisera måtten med hjälp av Azure Portal.

 > [!WARNING]
 > Ange i `spring.jmx.enabled=true` konfigurationsegenskapen. Annars kan mått inte visualiseras i Azure Portal.

### <a name="distributed-tracing"></a>Distribuerad spårning

Du måste också aktivera en Azure Application Insights-instans så att den fungerar med Azure Spring Cloud-tjänstinstansen. Information om hur du använder Application Insights med Azure Spring Cloud finns i [dokumentationen om distribuerad spårning](spring-cloud-tutorial-distributed-tracing.md).

#### <a name="spring-boot-2223"></a>Spring Boot 2.2/2.3
Inkludera följande `spring-cloud-starter-sleuth` och `spring-cloud-starter-zipkin` beroenden i avsnittet beroenden i din pom.xml fil:

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

#### <a name="spring-boot-24"></a>Spring Boot 2.4
Inkludera följande `spring-cloud-sleuth-zipkin` beroende i avsnittet beroenden i din pom.xml fil:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

## <a name="see-also"></a>Se även
* [Analysera programloggar och mått](./diagnostic-services.md)
* [Konfigurera konfigurationsservern](spring-cloud-tutorial-config-server.md)
* [Använda distribuerad spårning med Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md)
* [Snabbstartsguide för Spring](https://spring.io/quickstart)
* [Spring Boot dokumentation](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Nästa steg

I det här avsnittet har du lärt dig hur du konfigurerar ditt Java Spring-program för distribution till Azure Spring Cloud. Information om hur du ställer in en Config Server-instans finns [i Konfigurera en Config Server instans](spring-cloud-tutorial-config-server.md).

Fler exempel finns på GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end