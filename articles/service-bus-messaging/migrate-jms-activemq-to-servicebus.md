---
title: Migrera JMS-program (Java Message Service) från Apache ActiveMQ till Azure Service Bus | Microsoft Docs
description: Den här artikeln förklarar hur du migrerar befintliga JMS-program som interagerar med Apache ActiveMQ för att interagera med Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.custom: devx-track-java
ms.openlocfilehash: 4160a9ab4edbac8584eab2d4e5b9bf1ba11a9aec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568791"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-apache-activemq-to-azure-service-bus"></a>Migrera befintliga Java Message Service (JMS) 2,0-program från Apache ActiveMQ till Azure Service Bus

Den här artikeln beskriver hur du ändrar ett befintligt JMS 2,0-program (Java Message Service) som interagerar med en JMS-Broker för att interagera med Azure Service Bus i stället. Artikeln handlar särskilt om att migrera från Apache ActiveMQ eller Amazon MQ.

Azure Service Bus stöder Java 2 Platform-, Enterprise Edition-och våren-arbetsbelastningar som använder sig av JMS 2,0 API över Advanced Message Queueing Protocol (AMQP).

## <a name="before-you-start"></a>Innan du börjar

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Skillnader mellan Azure Service Bus-och Apache-ActiveMQ

Azure Service Bus-och Apache-ActiveMQ är både meddelande hanterare, som fungerar som JMS-leverantörer för klient program för att skicka meddelanden till och ta emot meddelanden från. De aktiverar både punkt-till-punkt-semantik med köer och publicerar-prenumerera-semantik med ämnen och prenumerationer. 

Även om det finns några skillnader mellan de två, som följande tabell visar:

| Kategori | ActiveMQ | Azure Service Bus |
| --- | --- | --- |
| Program skiktning | Klustrad monolit | Två nivåer <br> (Gateway + server del) |
| Protokollstöd | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| Etableringsläge | <ul> <li> Infrastruktur som en tjänst (IaaS), lokalt </li> <li> Amazon MQ (hanterad plattform som en tjänst) </li> | Hanterad plattform som en tjänst (PaaS) |
| Meddelandestorlek | Kunde konfigureras | 1 MB (Premium-nivå) |
| Hög tillgänglighet | Kund hantering | Plattform hanterad |
| Haveriberedskap | Kund hantering | Plattform hanterad | 

### <a name="current-supported-and-unsupported-features"></a>Aktuella funktioner som stöds och som inte stöds

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="considerations"></a>Överväganden

Azure Service Bus med två nivåer i ger olika funktioner för affärs kontinuitet (hög tillgänglighet och haveri beredskap). Det finns dock några saker att tänka på när du använder JMS-funktioner.

#### <a name="service-upgrades"></a>Tjänst uppgraderingar

Om du uppgraderar och startar om Service Bus tas temporära köer eller ämnen bort. Om ditt program är känsligt för data förlust i tillfälliga köer eller ämnen ska du inte använda tillfälliga köer eller ämnen. Använd varaktiga köer, ämnen och prenumerationer i stället.

#### <a name="data-migration"></a>Datamigrering

Som en del i migrering och ändring av klient program för att interagera med Azure Service Bus migreras inte data som finns i ActiveMQ till Service Bus. Du kan behöva ett anpassat program för att tömma ActiveMQ-köer, ämnen och prenumerationer och sedan spela upp meddelandena till köer, ämnen och prenumerationer av Service Bus.

#### <a name="authentication-and-authorization"></a>Autentisering och auktorisering

Rollbaserad åtkomst kontroll i Azure (Azure RBAC), som stöds av Azure Active Directory, är den föredragna autentiseringsmetoden för Service Bus. Eftersom Azure RBAC eller anspråksbaserad autentisering inte stöds av Apache QPID-JMS, kan du dock använda SAS-nycklar för autentisering.

## <a name="pre-migration"></a>Före migrering

### <a name="version-check"></a>Versions kontroll

Du använder följande komponenter och versioner medan du skriver JMS-programmen: 

| Komponent | Version |
|---|---|
| API för Java-meddelande tjänst (JMS) | 1,1 eller senare |
| AMQP-protokoll | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Se till att AMQP-portarna är öppna

Service Bus stöder kommunikation via AMQP-protokollet. För det här ändamålet aktiverar du kommunikation över portarna 5671 (AMQP) och 443 (TCP). Beroende på var klient programmen finns kan du behöva ett support ärende för att tillåta kommunikation över dessa portar.

> [!IMPORTANT]
> Service Bus stöder endast AMQP 1,0-protokoll.

### <a name="set-up-enterprise-configurations"></a>Konfigurera Enterprise-konfigurationer

Service Bus aktiverar olika funktioner för företags säkerhet och hög tillgänglighet. Mer information finns i: 

  * [Tjänstslutpunkter för virtuellt nätverk](service-bus-service-endpoints.md)
  * [Brandvägg](service-bus-ip-filtering.md)
  * [Kryptering på tjänst sidan med kundhanterad nyckel (BYOK)](configure-customer-managed-key.md)
  * [Privata slut punkter](private-link-service.md)
  * [Autentisering och auktorisering](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Övervakning, aviseringar och spårning

För varje Service Bus-namnrymd publicerar du mått på Azure Monitor. Du kan använda dessa mått för avisering och dynamisk skalning av resurser som har allokerats till namn området.

Mer information om de olika måtten och hur du konfigurerar aviseringar finns i [Service Bus mått i Azure Monitor](service-bus-metrics-azure-monitor.md). Du kan också få mer information om [spårning på klient sidan för data åtgärder](service-bus-end-to-end-tracing.md) och [Operational/Diagnostic-loggning för hanterings åtgärder](service-bus-diagnostic-logs.md).

### <a name="metrics---new-relic"></a>Mått – nya Relic

Du kan korrelera vilka mått från ActiveMQ mappar till vilka mått i Azure Service Bus. Se följande från den nya Relic-webbplatsen:

  * [ActiveMQ/Amazon MQ nya Relic-mått](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure Service Bus nya Relic-mått](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> För närvarande har nya Relic inte direkt, sömlös integrering med ActiveMQ, men de har mått som är tillgängliga för Amazon MQ. Eftersom Amazon MQ härleds från ActiveMQ, mappar följande tabell de nya Relic-måtten från Amazon MQ till Azure Service Bus.
>

|Mått grupperingen| Amazon MQ/ActiveMQ-mått | Azure Service Bus mått |
|------------|---------------------------|--------------------------|
|Utjämning|`CpuUtilization`|`CPUXNS`|
|Utjämning|`MemoryUsage`|`WSXNS`|
|Utjämning|`CurrentConnectionsCount`|`activeConnections`|
|Utjämning|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Utjämning|`InactiveDurableTopicSubscribersCount`|Använd prenumerations mått|
|Utjämning|`TotalMessageCount`|Använd kö/ämne/prenumerations nivå `activeMessages`|
|Kö/ämne|`EnqueueCount`|`incomingMessages`|
|Kö/ämne|`DequeueCount`|`outgoingMessages`|
|Kö|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migrering

Om du vill migrera ditt befintliga JMS 2,0-program för att interagera med Service Bus följer du stegen i följande avsnitt.

### <a name="export-the-topology-from-activemq-and-create-the-entities-in-service-bus-optional"></a>Exportera topologin från ActiveMQ och skapa entiteterna i Service Bus (valfritt)

För att säkerställa att klient program kan ansluta till Service Bus kan du migrera topologin (inklusive köer, ämnen och prenumerationer) från Apache ActiveMQ till Service Bus.

> [!NOTE]
> För JMS-program skapar du köer, ämnen och prenumerationer som en körnings åtgärd. De flesta JMS-providers (meddelande utjämnare) ger dig möjlighet att skapa dessa vid körning. Det är därför som det här export steget betraktas som valfritt. För att säkerställa att ditt program har behörighet att skapa topologin vid körning använder du anslutnings strängen med SAS- `Manage` behörigheter.

Gör så här:

1. Använd [kommando rads verktyget ActiveMQ](https://activemq.apache.org/activemq-command-line-tools-reference) för att exportera topologin.
1. Återskapa samma topologi med hjälp av en Azure Resource Manager- [mall](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
1. Kör Azure Resource Manager-mallen.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Importera maven-beroendet för Service Bus JMS-implementering

För att säkerställa sömlös anslutning med Service Bus lägger du till `azure-servicebus-jms` paketet som ett beroende till filen maven på `pom.xml` följande sätt:

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Konfigurations ändringar för program Server

Den här delen är anpassad till den program server som är värd för dina klient program som ansluter till ActiveMQ.

#### <a name="spring-applications"></a>Våren-program

##### <a name="update-the-applicationproperties-file"></a>Uppdatera `application.properties` filen

Om du använder ett våren Boot-program för att ansluta till ActiveMQ vill du ta bort de ActiveMQ egenskaperna från `application.properties` filen.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Lägg sedan till de Service Bus-/regionsspecifika egenskaperna i `application.properties` filen.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Ersätt `ActiveMQConnectionFactory` med `ServiceBusJmsConnectionFactory`

Nästa steg är att ersätta instansen av `ActiveMQConnectionFactory` med `ServiceBusJmsConnectionFactory` .

> [!NOTE] 
> De faktiska kod ändringarna är specifika för programmet och hur beroenden hanteras, men följande exempel ger vägledning om vad som bör ändras.
>

Tidigare kan du ha instansierat ett objekt av `ActiveMQConnectionFactory` , enligt följande:

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Nu ändrar du detta för att instansiera ett objekt av `ServiceBusJmsConnectionFactory` , enligt följande:

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Efter migreringen

Nu när du har ändrat programmet för att börja skicka och ta emot meddelanden från Service Bus bör du kontrol lera att det fungerar som förväntat. När det är färdigt kan du fortsätta med att förfina och modernisera din programs tack.

## <a name="next-steps"></a>Nästa steg

Använd [Start Start programmet våren för Azure Service Bus JMS](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) för sömlös integrering med Service Bus.

Mer information om Service Bus meddelande-och JMS finns i:

* [Service Bus JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
