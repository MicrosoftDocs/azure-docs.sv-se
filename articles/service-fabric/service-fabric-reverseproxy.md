---
title: Omvänd proxy för Azure Service Fabric
description: Använd Service Fabric Invertera proxy för kommunikation till mikrotjänster inifrån och utanför klustret.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: 012a49762596adee39988614ed0c1020cd8bc104
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791112"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Omvänd proxy i Azure Service Fabric
Omvänd proxy inbyggd i Azure Service Fabric hjälper mikrotjänster som körs i ett Service Fabric kluster att identifiera och kommunicera med andra tjänster som har http-slutpunkter.

## <a name="microservices-communication-model"></a>Kommunikation modell för mikrotjänster
Mikrotjänster i Service Fabric köras på en delmängd noder i klustret och kan migrera mellan noderna av olika orsaker. Därför kan slut punkterna för mikrotjänster ändras dynamiskt. För att kunna identifiera och kommunicera med andra tjänster i klustret måste mikrotjänster gå igenom följande steg:

1. Lös tjänst platsen via namngivnings tjänsten.
2. Anslut till tjänsten.
3. Lägg till föregående steg i en slinga som implementerar tjänst upplösning och försök att använda principer för anslutnings fel

Mer information finns i [ansluta och kommunicera med tjänster](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Kommunicera med hjälp av den omvända proxyn
Omvänd proxy är en tjänst som körs på varje nod och hanterar slut punkts upplösning, automatiskt återförsök och andra anslutnings fel för klient tjänsternas räkning. Omvänd proxy kan konfigureras att tillämpa olika principer när den hanterar begär Anden från klient tjänster. Med hjälp av en omvänd proxy kan klient tjänsten använda alla HTTP-kommunikations bibliotek på klient sidan och inte kräva särskild upplösning och omprövnings logik i tjänsten. 

Omvänd proxy exponerar en eller flera slut punkter på en lokal nod för klient tjänster som ska användas för att skicka begär anden till andra tjänster.

![Intern kommunikation][1]

> [!NOTE]
> **Plattformar som stöds**
>
> Omvänd proxy i Service Fabric stöder för närvarande följande plattformar
> * *Windows-kluster*: Windows 8 och senare eller Windows Server 2012 och senare
> * *Linux-kluster*: omvänd proxy är för närvarande inte tillgängligt för Linux-kluster
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Nå mikrotjänster utanför klustret
Standard modellen för externa kommunikationer för mikrotjänster är en valbar modell där varje tjänst inte kan nås direkt från externa klienter. [Azure Load Balancer](../load-balancer/load-balancer-overview.md), som är en nätverks gränser mellan mikrotjänster och externa klienter, utför Network Address Translation och vidarebefordrar externa begär anden till intern IP: port slut punkter. Om du vill göra en mikrotjänsts slut punkt direkt tillgänglig för externa klienter måste du först konfigurera Load Balancer att vidarebefordra trafik till varje port som tjänsten använder i klustret. Dessutom är de flesta mikrotjänster, särskilt tillstånds känsliga mikrotjänster, inte aktiva på alla noder i klustret. Mikrotjänster kan flyttas mellan noder vid redundans. I sådana fall kan Load Balancer inte effektivt bestämma platsen för målnoden för de repliker som den ska vidarebefordra trafiken till.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Nå mikrotjänster via den omvända proxyn utanför klustret
I stället för att konfigurera en enskild tjänsts port i Load Balancer kan du konfigurera bara porten för omvänd proxy i Load Balancer. Med den här konfigurationen kan klienter utanför klustret uppnå tjänster i klustret med hjälp av den omvända proxyn utan ytterligare konfiguration.

![Extern kommunikation][0]

> [!WARNING]
> När du konfigurerar porten för omvänd proxy i Load Balancer adresseras alla mikrotjänster i klustret som exponerar en HTTP-slutpunkt utanför klustret. Det innebär att mikrotjänster som är avsedda att vara interna kan identifieras av en bestämd skadlig användare. Detta potentiellt visar allvarliga sårbarheter som kan utnyttjas. exempel:
>
> * En obehörig användare kan starta en denial of service-attack genom att upprepade gånger anropa en intern tjänst som inte har en tillräckligt härdad attack yta.
> * En obehörig användare kan leverera felaktiga paket till en intern tjänst, vilket resulterar i oavsiktligt beteende.
> * En tjänst som är avsedd att vara intern kan returnera privat eller känslig information som inte är avsedd att exponeras för tjänster utanför klustret, vilket innebär att denna känsliga information exponeras för en obehörig användare. 
>
> Se till att du fullständigt förstår och minimerar potentiella säkerhets aspekterna för ditt kluster och appar som körs på den, innan du gör den omvända proxy-porten offentlig. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>URI-format för adresserings tjänster med den omvända proxyn
Den omvända proxyn använder ett angivet URI-format (Uniform Resource Identifier) för att identifiera den diskpartition som den inkommande begäran ska vidarebefordras till:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** Den omvända proxyn kan konfigureras för att godkänna HTTP-eller HTTPS-trafik. För HTTPS-vidarebefordran, se [ansluta till en säker tjänst med den omvända proxyn](service-fabric-reverseproxy-configure-secure-communication.md) när du har omvänt proxy-installationen för att lyssna på https.
* **Fullständigt kvalificerat domän namn (FQDN) | intern IP:** För externa klienter kan du konfigurera den omvända proxyn så att den går att komma åt via kluster domänen, till exempel mycluster.eastus.cloudapp.azure.com. Som standard körs den omvända proxyn på varje nod. För intern trafik kan den omvända proxyn nås på localhost eller på valfri intern nod-IP, till exempel 10.0.0.1.
* **Port:** Detta är porten, till exempel 19081, som har angetts för den omvända proxyn.
* **ServiceInstanceName:** Detta är det fullständigt kvalificerade namnet på den distribuerade tjänst instans som du försöker uppnå utan "Fabric:/" schema. Om du till exempel vill komma åt *Fabric:/MyApp/tjänsten/* tjänsten använder du *Mittprog/fjärrtjänsten*.

    Tjänst instansens namn är Skift läges känsligt. Om du använder ett annat Skift läge för namnet på tjänst instansen i URL: en kan det leda till att begäran Miss söker med 404 (hittades inte).
* **Sökväg till suffix:** Detta är den faktiska URL-sökvägen, till exempel *myapi/Values/Add/3*, för den tjänst som du vill ansluta till.
* **PartitionKey:** För en partitionerad tjänst är detta den beräknade partitionsnyckel för den partition som du vill uppnå. Observera att detta *inte* är PARTITIONS-ID GUID. Den här parametern krävs inte för tjänster som använder sig av singleton-partitionsschema.
* **PartitionKind:** Detta är tjänstens partitions schema. Detta kan vara "Int64Range" eller "name". Den här parametern krävs inte för tjänster som använder sig av singleton-partitionsschema.
* **ListenerName** Slut punkterna från tjänsten har formatet {"slut punkter": {"Listener1": "Endpoint1", "Listener2": "Endpoint2"...}}. När tjänsten exponerar flera slut punkter, identifierar detta den slut punkt som klient förfrågan ska vidarebefordras till. Detta kan utelämnas om tjänsten bara har en lyssnare.
* **TargetReplicaSelector** Detta anger hur mål repliken eller instansen ska väljas.
  * När mål tjänsten är tillstånds känslig kan TargetReplicaSelector vara något av följande: "PrimaryReplica", "RandomSecondaryReplica" eller "RandomReplica". När den här parametern inte anges är standardvärdet ' PrimaryReplica '.
  * När mål tjänsten är tillstånds lös, använder omvänd proxy en slumpmässig instans av tjänstepartitionen för att vidarebefordra begäran till.
* **Tids gräns:**  Detta anger tids gränsen för HTTP-begäran som skapats av den omvända proxyn till tjänsten å klientens vägnar. Standardvärdet är 120 sekunder. Detta är en valfri parameter.

### <a name="example-usage"></a>Exempel på användning
Vi kan till exempel ta tjänsten *Fabric:/MyApp/unservice* som öppnar en http-lyssnare på följande URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Följande är resurserna för tjänsten:

* `/index.html`
* `/api/users/<userId>`

Om tjänsten använder singleton partitionerings schema krävs inte parametrarna för *PartitionKey* och *PartitionKind* , och tjänsten kan nås med hjälp av gatewayen som:

* Externt `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Arbeta `http://localhost:19081/MyApp/MyService`

Om tjänsten använder ett enhetligt Int64-partitionerings schema måste parametrarna *PartitionKey* och *PartitionKind* användas för att uppnå en partition av tjänsten:

* Externt `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Arbeta `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

För att komma åt de resurser som tjänsten exponerar placerar du bara resurs Sök vägen efter tjänst namnet i URL: en:

* Externt `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Arbeta `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Gatewayen vidarebefordrar sedan dessa förfrågningar till tjänstens URL:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Särskild hantering för port delnings tjänster
Service Fabric reverse proxy försöker matcha en tjänst adress igen och försöker utföra begäran igen när det inte går att nå en tjänst. När en tjänst inte kan nås har tjänst instansen eller repliken i allmänhet flyttats till en annan nod som en del av dess normala livs cykel. När detta inträffar kan den omvända proxyn ta emot ett nätverks anslutnings fel som anger att en slut punkt inte längre är öppen på den ursprungliga matchade adressen.

Repliker eller tjänst instanser kan dock dela en värd process och kan också dela en port som finns på en http.sys-baserad webb server, inklusive:

* [System .net. HttpListener](/dotnet/api/system.net.httplistener)
* [ASP.NET Core weblyssnare](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

I den här situationen är det troligt att webb servern är tillgänglig i värd processen och att den svarar på begär Anden, men den matchade tjänst instansen eller repliken är inte längre tillgänglig på värden. I det här fallet kommer gatewayen att få ett HTTP 404-svar från webb servern. Ett HTTP 404-svar kan därför ha två olika betydelser:

- Fall #1: tjänst adressen är korrekt, men den resurs som användaren begärde finns inte.
- Ärende #2: tjänst adressen är felaktig och resursen som användaren begärde kan finnas på en annan nod.

Det första fallet är en vanlig HTTP 404 som betraktas som ett användar fel. Men i det andra fallet har användaren begärt en resurs som finns. Det gick inte att hitta den omvända proxyn eftersom själva tjänsten har flyttats. Den omvända proxyn måste matcha adressen igen och försöka utföra begäran igen.

Den omvända proxyn behöver därför ett sätt att skilja mellan dessa två fall. En ledtråd från-servern krävs för att göra den skillnaden.

* Som standard antar den omvända proxyn fall #2 och försöker lösa och utfärda begäran igen.
* För att indikera fall #1 till den omvända proxyn ska tjänsten returnera följande HTTP-svars huvud:

  `X-ServiceFabric : ResourceNotFound`

Detta HTTP-svarshuvuden visar en normal HTTP 404-situation där den begärda resursen inte finns, och den omvända proxyn kommer inte att försöka matcha tjänst adressen igen.

## <a name="special-handling-for-services-running-in-containers"></a>Särskild hantering för tjänster som körs i behållare

För tjänster som körs i behållare kan du använda miljövariabeln `Fabric_NodeIPOrFQDN` för att skapa [URL: en för omvänd proxy](#uri-format-for-addressing-services-by-using-the-reverse-proxy) som i följande kod:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
För det lokala klustret `Fabric_NodeIPOrFQDN` anges "localhost" som standard. Starta det lokala klustret med `-UseMachineName` parametern för att se till att behållare kan komma åt omvänd proxy som körs på noden. Mer information finns i [Konfigurera din utvecklings miljö för att felsöka behållare](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Service Fabric tjänster som körs i Docker-behållare kräver särskilda filen Docker. yml- *portar* http: eller https: konfiguration. Mer information finns i [Docker skapa distributions stöd i Azure Service Fabric](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Nästa steg
* Konfigurera [och konfigurera omvänd proxy i ett kluster](service-fabric-reverseproxy-setup.md).
* [Konfigurera vidarebefordran till säker HTTP-tjänst med den omvända proxyn](service-fabric-reverseproxy-configure-secure-communication.md)
* [Diagnostisera omvända proxy-händelser](service-fabric-reverse-proxy-diagnostics.md)
* Se ett exempel på HTTP-kommunikation mellan tjänster i ett [exempel projekt på GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Fjärran rop med Reliable Services fjärr kommunikation](service-fabric-reliable-services-communication-remoting.md)
* [Webb-API som använder OWIN i Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [WCF-kommunikation med hjälp av Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
