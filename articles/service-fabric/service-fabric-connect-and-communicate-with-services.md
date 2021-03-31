---
title: Anslut och kommunicera med tjänster i Azure Service Fabric
description: Lär dig hur du löser, ansluter och kommunicerar med tjänster i Service Fabric.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 11f525eba89dc963deee0ba9a86566361ef644de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576306"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Anslut och kommunicera med tjänster i Service Fabric
I Service Fabric körs en tjänst någonstans i ett Service Fabric kluster, vanligt vis distribuerat över flera virtuella datorer. Den kan flyttas från en plats till en annan, antingen av tjänstens ägare eller automatiskt av Service Fabric. Tjänsterna är inte statiskt bundna till en viss dator eller adress.

Ett Service Fabric program består vanligt vis av många olika tjänster, där varje tjänst utför en specialiserad uppgift. Dessa tjänster kan kommunicera med varandra för att forma en fullständig funktion, till exempel åter givning av olika delar av ett webb program. Det finns också klient program som ansluter till och kommunicerar med-tjänster. I det här dokumentet beskrivs hur du konfigurerar kommunikation med och mellan dina tjänster i Service Fabric.

## <a name="bring-your-own-protocol"></a>Ta med ditt eget protokoll
Service Fabric hjälper till att hantera livs cykeln för dina tjänster, men det fattar inga beslut om vad dina tjänster gör. Detta inkluderar kommunikation. När din tjänst öppnas av Service Fabric är det tjänstens möjlighet att skapa en slut punkt för inkommande begär Anden med hjälp av vilken protokoll-eller kommunikations stack du vill. Tjänsten lyssnar på en vanlig **IP-adress: port** adress med valfritt adresserings schema, till exempel en URI. Flera tjänst instanser eller repliker kan dela en värd process, vilket innebär att de antingen behöver använda olika portar eller använda en metod för att dela en port, t. ex. http.sys kernel-drivrutin i Windows. I båda fallen måste varje tjänst instans eller replik i en värd process vara unikt adresser bara.

![tjänst slut punkter][1]

## <a name="service-discovery-and-resolution"></a>Tjänst identifiering och-upplösning
I ett distribuerat system kan tjänster flyttas från en dator till en annan med tiden. Detta kan inträffa av olika orsaker, inklusive resurs utjämning, uppgraderingar, redundans eller skalbarhet. Det innebär att tjänstens slut punkts adresser ändras när tjänsten flyttas till noder med olika IP-adresser och kan öppnas på olika portar om tjänsten använder en dynamiskt vald port.

![Distribution av tjänster][7]

Service Fabric tillhandahåller en identifierings-och matchnings tjänst som kallas för Naming Service. Naming Service underhåller en tabell som mappar namngivna tjänst instanser till slut punkts adresserna de lyssnar på. Alla namngivna tjänst instanser i Service Fabric har unika namn som representeras som URI: er, till exempel `"fabric:/MyApplication/MyService"` . Namnet på tjänsten ändras inte under tjänstens livs längd, det är bara slut punkts adresser som kan ändras när tjänsterna flyttas. Detta är detsamma som webbplatser med konstanta URL: er, men där IP-adressen kan ändras. Och liknar DNS på webben, som matchar webbplats-URL: er till IP-adresser, Service Fabric har en registrator som mappar tjänst namn till deras slut punkts adress.

![Diagram som visar att Service Fabric har en registrator som mappar tjänst namn till deras slut punkts adress.][2]

Att lösa och ansluta till tjänster innebär att följande steg körs i en slinga:

* **Lös**: Hämta slut punkten som en tjänst har publicerat från Naming Service.
* **Anslut**: Anslut till tjänsten över det protokoll som den använder på den slut punkten.
* **Försök igen**: ett anslutnings försök kan Miss lyckas av flera orsaker, till exempel om tjänsten har flyttats sedan den senaste gången slut punkts adressen matchades. I så fall måste föregående steg för att lösa och ansluta göras om, och den här cykeln upprepas tills anslutningen lyckas.

## <a name="connecting-to-other-services"></a>Ansluta till andra tjänster
Tjänster som ansluter till varandra i ett kluster kan i allmänhet direkt komma åt slut punkterna för andra tjänster eftersom noderna i ett kluster finns i samma lokala nätverk. För att göra det enklare att ansluta mellan tjänster Service Fabric tillhandahåller ytterligare tjänster som använder Naming Service. En DNS-tjänst och en omvänd proxy-tjänst.


### <a name="dns-service"></a>DNS-tjänst
Eftersom många tjänster, särskilt behållar tjänster, kan ha ett befintligt URL-namn, kan det vara bra att lösa dessa med hjälp av standard-DNS-protokollet (i stället för Naming Service-protokollet), särskilt i program för att lyfta och växla. Detta är exakt vad DNS-tjänsten gör. Du kan mappa DNS-namn till ett tjänst namn och därmed matcha slut punktens IP-adresser. 

Som du ser i följande diagram mappar DNS-tjänsten som körs i Service Fabric-klustret DNS-namn till tjänst namn som sedan matchas av Naming Service för att returnera slut punkts adresserna som ska anslutas till. DNS-namnet för tjänsten tillhandahålls vid tidpunkten för skapandet. 

![Diagram som visar hur DNS-tjänsten, när den körs i Service Fabric-klustret, mappar DNS-namn till tjänst namn som sedan löses av Naming Service för att returnera slut punkts adresserna som ska anslutas till.][9]

Mer information om hur du använder DNS-tjänsten finns i artikeln om [DNS-tjänsten i Azure Service Fabric](service-fabric-dnsservice.md) .

### <a name="reverse-proxy-service"></a>Tjänsten omvänd proxy
Den omvända proxyn adresser tjänster i klustret som exponerar HTTP-slutpunkter inklusive HTTPS. Den omvända proxyn fören klar att anropa andra tjänster och deras metoder genom att ha ett särskilt URI-format och hanterar de åtgärder för att lösa, ansluta och försök som krävs för att en tjänst ska kunna kommunicera med en annan med hjälp av Naming Service. Med andra ord döljer den Naming Service från dig när du anropar andra tjänster genom att göra det så enkelt som att anropa en URL.

![Diagram som visar hur omvänd proxy adresser tjänster i klustret som exponerar HTTP-slutpunkter inklusive HTTPS.][10]

Mer information om hur du använder tjänsten reverse proxy finns [i artikeln om omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy.md) .

## <a name="connections-from-external-clients"></a>Anslutningar från externa klienter
Tjänster som ansluter till varandra i ett kluster kan i allmänhet direkt komma åt slut punkterna för andra tjänster eftersom noderna i ett kluster finns i samma lokala nätverk. I vissa miljöer kan det dock finnas ett kluster bakom en belastningsutjämnare som dirigerar inkommande trafik via en begränsad uppsättning portar. I dessa fall kan tjänsterna fortfarande kommunicera med varandra och lösa adresser med hjälp av Naming Service, men extra steg måste vidtas för att tillåta att externa klienter ansluter till tjänster.

## <a name="service-fabric-in-azure"></a>Service Fabric i Azure
Ett Service Fabric kluster i Azure placeras bakom ett Azure Load Balancer. All extern trafik till klustret måste passera belastnings utjämningen. Belastningsutjämnaren vidarebefordrar automatiskt inkommande trafik på en specifik port till en slumpmässig *nod* som har samma port öppen. Azure Load Balancer bara känner till om portarna är öppna på *noderna*, vet inte om portar som är öppna för enskilda *tjänster*.

![Azure Load Balancer-och Service Fabric-topologi][3]

För att till exempel kunna ta emot extern trafik på port **80** måste följande saker konfigureras:

1. Skriv en tjänst som lyssnar på port 80. Konfigurera port 80 i tjänstens ServiceManifest.xml och öppna en lyssnare i tjänsten, till exempel en webb server med egen värd.

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. Skapa ett Service Fabric kluster i Azure och ange port **80** som en anpassad slut punkts port för nodtypen som ska vara värd för tjänsten. Om du har fler än en nodtyp kan du konfigurera en *placerings begränsning* för tjänsten för att säkerställa att den bara körs på nodtypen som har den anpassade slut punkts porten öppen.

    ![Öppna en port på en nodtyp][4]
3. När klustret har skapats konfigurerar du Azure Load Balancer i klustrets resurs grupp för att vidarebefordra trafik på port 80. När du skapar ett kluster via Azure Portal konfigureras det automatiskt för varje anpassad slut punkts port som har kon figurer ATS.

    ![Skärm bild som visar Port fältet för Server delen under belastnings Utjämnings regler.][5]
4. Azure Load Balancer använder en avsökning för att avgöra om trafik ska skickas till en viss nod eller inte. Avsökningen kontrollerar regelbundet en slut punkt på varje nod för att avgöra om noden svarar eller inte. Om avsökningen inte kan ta emot ett svar efter ett angivet antal gånger, slutar belastningsutjämnaren att skicka trafik till den noden. När du skapar ett kluster via Azure Portal konfigureras en avsökning automatiskt för varje anpassad slut punkts port som har kon figurer ATS.

    ![Vidarebefordra trafik i Azure Load Balancer][8]

Det är viktigt att komma ihåg att Azure Load Balancer och avsökningen bara vet om *noderna*, inte de *tjänster* som körs på noderna. Azure Load Balancer skickar alltid trafik till noder som svarar på avsökningen, så var försiktig måste vidtas för att säkerställa att tjänsterna är tillgängliga på de noder som kan svara på avsökningen.

## <a name="reliable-services-built-in-communication-api-options"></a>Reliable Services: inbyggda API-alternativ för kommunikation
Reliable Services Framework levereras med flera fördefinierade kommunikations alternativ. Beslutet om vad som passar bäst för dig beror på valet av programmerings modell, kommunikations ramverk och det programmeringsspråk som dina tjänster är skrivna i.

* **Inget enskilt protokoll:**  Om du inte har något särskilt val av kommunikations ramverk, men du vill att något ska köras snabbt, så är det idealiska alternativet för dig att [betjäna fjärr kommunikation](service-fabric-reliable-services-communication-remoting.md), som tillåter starkt skrivna fjärrprocedurs anrop för Reliable Services och Reliable Actors. Detta är det enklaste och snabbaste sättet att komma igång med tjänst kommunikation. Service Remoting hanterar matchning av tjänst adresser, anslutning, återförsök och fel hantering. Detta är tillgängligt för både C#-och Java-program.
* **Http**: för oberoende kommunikation tillhandahåller http ett val av bransch standard med verktyg och http-servrar som är tillgängliga på många olika språk, vilket stöds av Service Fabric. Tjänster kan använda alla HTTP-stackar som är tillgängliga, inklusive [ASP.net Web API](./service-fabric-reliable-services-communication-aspnetcore.md) för C#-program. Klienter som är skrivna i C# kan `ICommunicationClient` `ServicePartitionClient` använda klasserna och, och för Java, använda- `CommunicationClient` och- `FabricServicePartitionClient` klasser [för tjänst upplösning, http-anslutningar och försök att](service-fabric-reliable-services-communication.md)göra om slingor.
* **WCF**: om du har en befintlig kod som använder WCF som kommunikations ramverk kan du använda `WcfCommunicationListener` för-Server sidan och `WcfCommunicationClient` och- `ServicePartitionClient` klasserna för-klienten. Detta är dock endast tillgängligt för C#-program i Windows-baserade kluster. Mer information finns i den här artikeln om [WCF-baserad implementering av kommunikations stacken](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Använda anpassade protokoll och andra ramverk för kommunikation
Tjänster kan använda alla protokoll eller ramverk för kommunikation, oavsett om det är ett anpassat binärt protokoll över TCP-socketar eller strömmande händelser via [azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) eller [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/). Service Fabric tillhandahåller kommunikations-API: er som du kan använda för att ansluta din kommunikations stack till, medan allt arbete att identifiera och ansluta är abstrakt från dig. Mer information finns i den här artikeln om den [Reliable-tjänst kommunikations modellen](service-fabric-reliable-services-communication.md) .

## <a name="next-steps"></a>Nästa steg
Lär dig mer om de begrepp och API: er som är tillgängliga i [Reliable Services kommunikations modellen](service-fabric-reliable-services-communication.md)och kom sedan igång snabbt med [tjänst-Remoting](service-fabric-reliable-services-communication-remoting.md) eller gå djupare och lär dig hur du skriver en kommunikations lyssnare med hjälp av [webb-API med OWIN Self-Host](./service-fabric-reliable-services-communication-aspnetcore.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
