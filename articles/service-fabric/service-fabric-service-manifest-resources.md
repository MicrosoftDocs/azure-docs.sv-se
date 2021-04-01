---
title: Ange Service Fabric tjänst slut punkter
description: Så här beskriver du slut punkts resurser i ett tjänst manifest, inklusive hur du konfigurerar HTTPS-slutpunkter
ms.topic: conceptual
ms.date: 09/16/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 0ed5a4aa8993f52d42b97288cd143e6114ff36ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033314"
---
# <a name="specify-resources-in-a-service-manifest"></a>Ange resurser i ett tjänst manifest
## <a name="overview"></a>Översikt
Service Fabric program och tjänster definieras och versions hantering med hjälp av MANIFEST-filer. En mer övergripande översikt över ServiceManifest.xml och ApplicationManifest.xml finns i [Service Fabric program-och tjänst manifest](service-fabric-application-and-service-manifests.md).

Tjänst manifestet gör det möjligt för resurser som används av tjänsten att deklareras eller ändras, utan att den kompilerade koden ändras. Service Fabric stöder konfiguration av slut punkts resurser för tjänsten. Åtkomst till de resurser som anges i tjänst manifestet kan styras via SecurityGroup i applikations manifestet. Resurs deklarationen gör att dessa resurser kan ändras vid distributions tillfället, vilket innebär att tjänsten inte behöver införa en ny konfigurations funktion. Schema definitionen för ServiceManifest.xml-filen installeras med Service Fabric SDK och verktyg för att *C:\Program\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd* och dokumenteras i [ServiceFabricServiceModel. xsd schema-dokumentation](service-fabric-service-model-schema.md).

## <a name="endpoints"></a>Slutpunkter
När en slut punkts resurs definieras i tjänst manifestet tilldelar Service Fabric portar från det reserverade programmets port intervall när en port inte anges explicit. Titta till exempel på slut punkts *ServiceEndpoint1* som anges i manifestet som anges efter detta stycke. Dessutom kan tjänster även begära en speciell port i en resurs. Tjänst repliker som körs på olika klusternoder kan tilldelas olika port nummer, medan repliker av en tjänst som körs på samma nod delar porten. Tjänste replikerna kan sedan använda dessa portar vid behov för replikering och lyssna efter klient begär Anden.

Vid aktivering av en tjänst som anger en HTTPS-slutpunkt anger Service Fabric åtkomst kontroll posten för porten, binder det angivna Server certifikatet till porten och ger också identiteten att tjänsten körs som behörighet till certifikatets privata nyckel. Aktiverings flödet anropas varje gång Service Fabric startas, eller när certifikat deklarationen för programmet ändras via en uppgradering. Slut punkts certifikatet kommer också att övervakas för ändringar/förnyelser, och behörigheter kommer att tillämpas regelbundet vid behov.

När tjänsten avslutas kommer Service Fabric att rensa slut punkts åtkomst kontroll posten och ta bort certifikat bindningen. Behörigheter som tillämpas på certifikatets privata nyckel kommer dock inte att rensas.

> [!WARNING] 
> Genom att definiera statiska portar ska inte överlappa med det applikations port intervall som anges i ClusterManifest. Om du anger en statisk port tilldelar du den utanför program port intervallet, annars leder det till port konflikter. Med version 6.5 CU2 kommer vi att utfärda en **hälso varning** när vi identifierar en sådan konflikt men låter distributionen fortsätta att synkroniseras med den levererade 6,5 beteendet. Vi kan dock förhindra program distributionen från nästa större versioner.
>
> Med version 7,0 kommer vi att utfärda en **hälso varning** när vi identifierar användningen av användnings port intervallet går utöver HostingConfig:: ApplicationPortExhaustThresholdPercentage (standard 80%).
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Om det finns flera kod paket i ett enda tjänst paket måste kod paketet också refereras i avsnittet **slut punkter** .  Om till exempel **ServiceEndpoint2a** och **ServiceEndpoint2b** är slut punkter från samma tjänst paket som refererar till olika kod paket, så klargörs det kod paket som motsvarar varje slut punkt enligt följande:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Läs mer om att [Konfigurera tillstånds känsliga Reliable Services](service-fabric-reliable-services-configuration.md) för att läsa mer om att referera till slut punkter från konfigurations filen för konfigurations paket (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exempel: Ange en HTTP-slutpunkt för din tjänst
Följande tjänst manifest definierar en resurs för TCP-slutpunkten och två HTTP-slutpunkts resurser i &lt; resurs- &gt; elementet.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
      <Endpoint Name="ServiceEndpoint4" Protocol="https" Port="14023"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exempel: Ange en HTTPS-slutpunkt för din tjänst
HTTPS-protokollet tillhandahåller serverautentisering och används även för kryptering av klient-server-kommunikation. Om du vill aktivera HTTPS på din Service Fabric-tjänst anger du protokollet i avsnittet *resurser-> slut punkter-> slut* punkt i tjänst manifestet, som du ser tidigare för slut punkten *ServiceEndpoint3*.

> [!NOTE]
> Det går inte att ändra tjänstens protokoll under program uppgraderingen. Om den ändras under uppgraderingen, är det en större ändring.
> 

> [!WARNING] 
> När du använder HTTPS ska du inte använda samma port och certifikat för olika tjänst instanser (oberoende av programmet) som distribueras till samma nod. Uppgradering av två olika tjänster med samma port i olika program instanser leder till ett uppgraderings haveri. Mer information finns i [uppgradera flera program med https-slutpunkter ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Här är ett exempel på en ApplicationManifest som demonstrerar konfigurationen som krävs för en HTTPS-slutpunkt. Server-eller slut punkt certifikatet kan ha deklarerats med tumavtryck eller subjektets nätverks namn och ett värde måste anges. EndpointRef är en referens till EndpointResource i ServiceManifest och vars protokoll måste ha angetts till HTTPS-protokollet. Du kan lägga till fler än en EndpointCertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint3"/>
      <EndpointBindingPolicy CertificateRef="SslCertByCN" EndpointRef="ServiceEndpoint4"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="SslCertByTP" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
    <EndpointCertificate Name="SslCertByCN" X509FindType="FindBySubjectName" X509FindValue="ServiceFabric-EndpointCertificateBinding-Test" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

För Linux-kluster är **mitt** Arkiv standardvärdet för mappen **/var/lib/sfcerts**.

Ett exempel på ett fullständigt program som använder en HTTPS-slutpunkt finns i [lägga till en HTTPS-slutpunkt till en ASP.net Core webb-API-frontend-tjänst med hjälp av Kestrel](./service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest).

## <a name="port-acling-for-http-endpoints"></a>Port ACLing för HTTP-slutpunkter
Service Fabric kommer automatiskt att ange HTTP-slutpunkter för ACL: er som anges som standard. Den kommer **inte** att utföra automatiska ACLing om en slut punkt inte har någon kopplad [SecurityAccessPolicy](service-fabric-assign-policy-to-endpoint.md) och Service Fabric har kon figurer ATS för att köras med ett konto med administratörs behörighet.

## <a name="overriding-endpoints-in-servicemanifestxml"></a>Åsidosätt slut punkter i ServiceManifest.xml

I ApplicationManifest lägger du till en ResourceOverrides-sektion, som kommer att vara en syskon till ConfigOverrides-avsnittet. I det här avsnittet kan du ange åsidosättningen för avsnittet slut punkter i avsnittet resurser som anges i tjänst manifestet. Det finns stöd för att åsidosätta slut punkter i runtime 5.7.217/SDK 2.7.217 och högre.

För att åsidosätta slut punkten i ServiceManifest med hjälp av ApplicationParameters ändrar du ApplicationManifest så här:

I avsnittet service manifest import lägger du till ett nytt avsnitt "ResourceOverrides".

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

I parametrarna lägger du till nedan:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

När du distribuerar programmet kan du skicka dessa värden som ApplicationParameters.  Exempel:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Obs! Om värdet som angetts för en angiven ApplicationParameter är tomt går vi tillbaka till det standardvärde som anges i ServiceManifest för motsvarande EndPointName.

Exempel:

Om du har angett i ServiceManifest

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Antag att värdet PORT1 och Protocol1 för program parametrarna är null eller tomt. Porten kommer att bestämmas av ServiceFabric och protokollet kommer att vara TCP.

Anta att du anger ett felaktigt värde. Anta att du har angett ett sträng värde "foo" i stället för en int.  New-ServiceFabricApplication kommandot Miss fungerar med ett fel: `The override parameter with name 'ServiceEndpoint1' attribute 'Port1' in section 'ResourceOverrides' is invalid. The value specified is 'Foo' and required is 'int'.`

## <a name="next-steps"></a>Nästa steg

I den här artikeln förklaras hur du definierar slut punkter i Service Fabric tjänst manifest. Mer detaljerade exempel finns i:

> [!div class="nextstepaction"]
> [Exempel på program och tjänstmanifest](service-fabric-manifest-examples.md)

En genom gång av paketering och distribution av ett befintligt program på ett Service Fabric-kluster finns i:

> [!div class="nextstepaction"]
> [Paketera och distribuera en befintlig körbar fil till Service Fabric](service-fabric-deploy-existing-app.md)