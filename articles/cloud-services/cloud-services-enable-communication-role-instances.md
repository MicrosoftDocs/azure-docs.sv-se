---
title: Kommunikation för roller i Cloud Services (klassisk) | Microsoft Docs
description: Roll instanser i Cloud Services kan ha slut punkter (http, https, TCP, UDP) definierade för dem som kommunicerar med utanför eller mellan andra roll instanser.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 82aa1579a1f7feb36732153341e1eacf266a7218
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743040"
---
# <a name="enable-communication-for-role-instances-in-azure-cloud-services-classic"></a>Aktivera kommunikation för roll instanser i Azure Cloud Services (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Moln tjänst roller kommunicerar via interna och externa anslutningar. Externa anslutningar kallas för **ingångs slut punkter** medan interna anslutningar kallas **interna slut punkter**. I det här avsnittet beskrivs hur du ändrar [tjänst definitionen](cloud-services-model-and-package.md#csdef) för att skapa slut punkter.

## <a name="input-endpoint"></a>Slut punkt för indatakälla
Slut punkten för indatakälla används när du vill exponera en port för utsidan. Du anger protokoll typ och port för slut punkten som sedan gäller för båda de externa och interna portarna för slut punkten. Om du vill kan du ange en annan intern port för slut punkten med attributet [localPort](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) .

Slut punkten för indatamängden kan använda följande protokoll: **http, https, TCP, UDP**.

Om du vill skapa en ingångs slut punkt lägger du till det underordnade elementet **InputEndpoint** i **slut punkts** elementet för en webb-eller arbets roll.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Slut punkt för instans ingång
Instans ingångs slut punkter liknar ingångs slut punkter, men gör att du kan mappa specifika offentliga portar för varje enskild roll instans genom att använda vidarebefordrad port på belastningsutjämnaren. Du kan ange en enskild offentlig port eller ett port intervall.

Slut punkten för instans indatamängden kan endast använda **TCP** eller **UDP** som protokoll.

Om du vill skapa en slut punkt för instans ineffekt lägger du till det underordnade elementet **InstanceInputEndpoint** i **slut punkts** elementet för en webb-eller arbets roll.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Intern slut punkt
Interna slut punkter är tillgängliga för kommunikation mellan instans och instans. Porten är valfri och om den utelämnas tilldelas den en dynamisk port till slut punkten. Ett port intervall kan användas. Det finns en gräns på fem interna slut punkter per roll.

Den interna slut punkten kan använda följande protokoll: **http, TCP, UDP, any**.

Om du vill skapa en intern ingångs slut punkt lägger du till det underordnade elementet **InternalEndpoint** i **slut punkts** elementet för en webb-eller arbets roll.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Du kan också använda ett port intervall.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Arbets roller eller webb roller
Det finns en mindre skillnad med slut punkter när du arbetar med både Worker och webb roller. Webb rollen måste ha minst en slut punkt för en ingång med **http-** protokollet.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Använda .NET SDK för att få åtkomst till en slut punkt
Det hanterade Azure-biblioteket innehåller metoder för roll instanser som ska kommunicera vid körning. Från kod som körs i en roll instans kan du hämta information om förekomsten av andra roll instanser och deras slut punkter, samt information om den aktuella roll instansen.

> [!NOTE]
> Du kan bara hämta information om roll instanser som körs i din moln tjänst och som definierar minst en intern slut punkt. Du kan inte hämta data om roll instanser som körs i en annan tjänst.
> 
> 

Du kan använda egenskapen [instances](/previous-versions/azure/reference/ee741904(v=azure.100)) för att hämta instanser av en roll. Använd först [CurrentRoleInstance](/previous-versions/azure/reference/ee741907(v=azure.100)) för att returnera en referens till den aktuella roll instansen och Använd sedan egenskapen [Role](/previous-versions/azure/reference/ee741918(v=azure.100)) för att returnera en referens till själva rollen.

När du ansluter till en roll instans via programmering via .NET SDK är det relativt enkelt att komma åt slut punkts informationen. När du till exempel redan har anslutit till en speciell roll miljö kan du hämta porten för en speciell slut punkt med den här koden:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

Egenskapen **instances** returnerar en samling **RoleInstance** -objekt. Den här samlingen innehåller alltid den aktuella instansen. Om rollen inte definierar en intern slut punkt innehåller samlingen den aktuella instansen men inga andra instanser. Antalet roll instanser i samlingen är alltid 1 i det fall där ingen intern slut punkt har definierats för rollen. Om rollen definierar en intern slut punkt går dess instanser att identifiera vid körning och antalet instanser i samlingen motsvarar antalet instanser som anges för rollen i tjänst konfigurations filen.

> [!NOTE]
> Det hanterade Azure-biblioteket ger inte möjlighet att fastställa hälso tillståndet för andra roll instanser, men du kan implementera sådana hälso bedömningar själv om tjänsten behöver den här funktionen. Du kan använda [Azure-diagnostik](cloud-services-dotnet-diagnostics.md) för att få information om att köra roll instanser.
> 
> 

Om du vill fastställa port numret för en intern slut punkt på en roll instans kan du använda [`InstanceEndpoints`](/previous-versions/azure/reference/ee741917(v=azure.100)) egenskapen för att returnera ett Dictionary-objekt som innehåller slut punkts namn och deras motsvarande IP-adresser och portar. [`IPEndpoint`](/previous-versions/azure/reference/ee741919(v=azure.100))Egenskapen returnerar IP-adressen och porten för en angiven slut punkt. `PublicIPEndpoint`Egenskapen returnerar porten för en belastningsutjämnad slut punkt. IP-adress delen av `PublicIPEndpoint` egenskapen används inte.

Här är ett exempel som itererar roll instanser.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Här är ett exempel på en arbets roll som hämtar den slut punkt som exponeras genom tjänst definitionen och börjar lyssna efter anslutningar.

> [!WARNING]
> Den här koden fungerar bara för en distribuerad tjänst. När du kör i Azure Compute-emulatorn ignoreras tjänst konfigurations element som skapar direkt port slut punkter (**InstanceInputEndpoint** -element).
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Regler för nätverks trafik för att kontrol lera roll kommunikation
När du har definierat interna slut punkter kan du lägga till regler för nätverks trafik (baserat på de slut punkter som du har skapat) för att styra hur roll instanser kan kommunicera med varandra. Följande diagram visar några vanliga scenarier för att kontrol lera roll kommunikation:

![Scenarier för nätverks trafik regler](./media/cloud-services-enable-communication-role-instances/scenarios.png "Scenarier för nätverks trafik regler")

I följande kod exempel visas roll definitioner för de roller som visas i föregående diagram. Varje roll definition innehåller minst en definierad intern slut punkt:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> Begränsning av kommunikation mellan roller kan ske med interna slut punkter för båda fasta och automatiskt tilldelade portar.
> 
> 

Som standard när en intern slut punkt har definierats kan kommunikationen flöda från vilken roll som helst till den interna slut punkten för en roll utan några begränsningar. Om du vill begränsa kommunikationen måste du lägga till ett **NetworkTrafficRules** -element i **service definition** -elementet i tjänst definitions filen.

### <a name="scenario-1"></a>Scenario 1
Tillåt endast nätverks trafik från **WebRole1** till **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Scenario 2
Tillåter endast nätverks trafik från **WebRole1** till **WorkerRole1** och **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Scenario 3
Tillåter endast nätverks trafik från **WebRole1** till **WorkerRole1**, och **WorkerRole1** till **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Scenario 4
Tillåter endast nätverks trafik från **WebRole1** till **WorkerRole1**, **WebRole1** till **WorkerRole2** och **WorkerRole1** till **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Du hittar en referens till XML-schemat för elementen som används [ovan.](/previous-versions/azure/reference/gg557551(v=azure.100))

## <a name="next-steps"></a>Nästa steg
Läs mer om moln tjänst [modellen](cloud-services-model-and-package.md).




