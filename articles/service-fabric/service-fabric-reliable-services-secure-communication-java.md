---
title: Säker kommunikation med fjärrkommunikation med Java
description: Lär dig hur du skyddar kommunikation mellan tjänster för Java Reliable Services som körs i ett Azure Service Fabric-kluster.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: 01a64fbcfef9f56abb0e1aa6cf7f5d821dd3763b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87325720"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Säker kommunikation mellan tjänster i en Java-tjänst
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java i Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Säkerhet är en av de viktigaste aspekterna av kommunikationen. Reliable Services Application Framework innehåller några inbyggda kommunikations stackar och verktyg som du kan använda för att förbättra säkerheten. Den här artikeln beskriver hur du kan förbättra säkerheten när du använder tjänst-Remoting i en Java-tjänst. Det bygger på ett befintligt [exempel](service-fabric-reliable-services-communication-remoting-java.md) som förklarar hur du konfigurerar fjärr kommunikation för pålitliga tjänster skrivna i Java. 

Följ dessa steg för att skydda en tjänst när du använder service Remoting med Java-tjänster:

1. Skapa ett gränssnitt, `HelloWorldStateless` som definierar de metoder som ska vara tillgängliga för ett fjärran rop på tjänsten. Tjänsten kommer att använda `FabricTransportServiceRemotingListener` , som deklareras i `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` paketet. Det här är en `CommunicationListener` implementering som tillhandahåller funktioner för fjärr kommunikation.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Lägg till Listener-inställningar och säkerhets uppgifter.

    Kontrol lera att det certifikat som du vill använda för att skydda tjänst kommunikationen är installerat på alla noder i klustret. För tjänster som körs på Linux måste certifikatet vara tillgängligt som en PEM-formmatted-fil. antingen en `.pem` fil som innehåller certifikatet och den privata nyckeln eller en `.crt` fil som innehåller certifikatet och en `.key` fil som innehåller den privata nyckeln. Mer information finns i [plats och format för X. 509-certifikat på Linux-noder](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Det finns två sätt som du kan ange inställningar för lyssnare och säkerhets uppgifter:

   1. Ange dem med hjälp av ett [konfigurations paket](service-fabric-application-and-service-manifests.md):

       Lägg till ett namngivet `TransportSettings` avsnitt i settings.xml-filen.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       I det här fallet `createServiceInstanceListeners` kommer metoden att se ut så här:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Om du lägger till ett `TransportSettings` avsnitt i settings.xml-filen utan något prefix, `FabricTransportListenerSettings` kommer alla inställningar från det här avsnittet att läsas in som standard.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        I det här fallet `CreateServiceInstanceListeners` kommer metoden att se ut så här:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. När du anropar metoder på en säker tjänst med hjälp av fjärrstacken, i stället för `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` att använda klassen för att skapa en Tjänstproxy, använder du `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory` .

    Om klient koden körs som en del av en tjänst kan du läsa in `FabricTransportSettings` från settings.xml-filen. Skapa ett TransportSettings-avsnitt som liknar tjänst koden, som du ser ovan. Gör följande ändringar i klient koden:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
