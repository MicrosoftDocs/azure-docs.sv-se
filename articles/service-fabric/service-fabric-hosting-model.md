---
title: Värd modell för Azure Service Fabric
description: Beskriver relationen mellan repliker (eller instanser) av en distribuerad Service Fabric tjänst och tjänst värd processen.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.custom: devx-track-csharp
ms.openlocfilehash: 9932c11332a616928d59c213d4f4806feb81cfe2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791653"
---
# <a name="azure-service-fabric-hosting-model"></a>Värd modell för Azure Service Fabric
Den här artikeln innehåller en översikt över program värd modeller som tillhandahålls av Azure Service Fabric och beskriver skillnaderna mellan den **delade processen** och **exklusiva process** modeller. Den beskriver hur ett distribuerat program ser ut på en Service Fabric-nod och förhållandet mellan repliker (eller instanser) av tjänsten och Service-Host-processen.

Innan du fortsätter bör du se till att du förstår de olika begreppen och förhållandena som beskrivs i [modellera ett program i Service Fabric][a1]. 

> [!NOTE]
> I den här artikeln, om inget annat anges som betyder något annat:
>
> - *Repliken* refererar både till en replik av en tillstånds känslig tjänst och en instans av en tillstånds lös tjänst.
> - *CodePackage* behandlas som likvärdigt med en *ServiceHost* -process som registrerar en *ServiceType* och är värd för repliker av tjänster för denna *ServiceType*.
>

För att förstå värd modellen ska vi gå igenom ett exempel. Anta att vi har en *ApplicationType* ' MyAppType ', som har en *ServiceType* ' MyServiceType '. "MyServiceType" tillhandahålls av *servicepack* ",", som har *CodePackage* "MyCodePackage". "MyCodePackage" registrerar *ServiceType* "MyServiceType" när den körs.

Anta att vi har ett kluster med tre noder och vi skapar en *program* **infrastruktur:/APP1** av typen ' MyAppType '. I den här program **infrastrukturen:/APP1** skapar vi en service **Fabric:/APP1/servicer** av typen ' MyServiceType '. Den här tjänsten har två partitioner (till exempel **P1** och **P2**) och tre repliker per partition. Följande diagram visar vyn över det här programmet när det slutar att distribueras på en nod.


![Diagram som visar visningen av det här programmet när den slutar att distribueras på en nod.][node-view-one]


Service Fabric aktiverat ' MyCodePackage ', som startade ' ', som är värd för repliker från båda partitionerna. Alla noder i klustret har samma vy, eftersom vi väljer antalet repliker per partition som ska vara lika med antalet noder i klustret. Nu ska vi skapa en annan tjänst, **Fabric:/APP1/ServiceB**, i Application **Fabric:/APP1**. Den här tjänsten har en partition (till exempel **P3**) och tre repliker per partition. Följande diagram visar den nya vyn på noden:


![Diagram som visar den nya vyn på noden.][node-view-two]


Service Fabric placerat den nya repliken för partition **P3** of service **Fabric:/APP1/ServiceB** i den befintliga aktiveringen av "". Vidare. nu ska vi skapa en annan program **infrastruktur:/APP2** av typen ' MyAppType '. I **infrastruktur resurser:/APP2**, skapar du en service **Fabric:/APP2/servicer**. Den här tjänsten har två partitioner (**P4** och **P5**) och tre repliker per partition. Följande diagram visar den nya vyn node:


![Diagram som visar den nya vyn Node.][node-view-three]


Service Fabric aktiverar en ny kopia av "MyCodePackage" som startar en ny kopia av "". Repliker från båda partitionerna för service **Fabric:/APP2/servicea** (**P4** och **P5**) placeras i den nya kopian "MyCodePackage".

## <a name="shared-process-model"></a>Delad process modell
I föregående avsnitt beskrivs den standard värd modell som tillhandahålls av Service Fabric, som kallas för den delade process modellen. I den här modellen för ett angivet program aktive ras endast en kopia av ett angivet *servicepack* på en nod (som startar alla *CodePackages* som ingår i det). Alla repliker av alla tjänster i en specifik *ServiceType* placeras i *CodePackage* som registrerar denna *ServiceType*. Med andra ord delar alla repliker av alla tjänster på en nod i en särskild *ServiceType* samma process.

## <a name="exclusive-process-model"></a>Exklusiv process modell
Den andra värd modellen som tillhandahålls av Service Fabric är den exklusiva process modellen. I den här modellen, på en specifik nod, finns varje replik i en egen dedikerad process. Service Fabric aktiverar en ny kopia av *servicepack* (som startar alla *CodePackages* som ingår i det). Repliker placeras i *CodePackage* som registrerade *ServiceType* för tjänsten som repliken tillhör. 

Om du använder Service Fabric version 5,6 eller senare kan du välja den exklusiva process modellen vid den tidpunkt då du skapar en tjänst (med hjälp av [PowerShell][p1], [rest][r1]eller [FabricClient][c1]). Ange **ServicePackageActivationMode** som "ExclusiveProcess".

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Om du har en standard tjänst i applikations manifestet kan du välja den exklusiva process modellen genom att ange attributet **ServicePackageActivationMode** :

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Nu ska vi skapa en annan tjänst, **Fabric:/APP1/ServiceC**, i Application **Fabric:/APP1**. Den här tjänsten har två partitioner (till exempel **P6** och **P7**) och tre repliker per partition. Du anger **ServicePackageActivationMode** till ' ExclusiveProcess '. Följande diagram visar en ny vy på noden:


![Diagram över vyn Node för det distribuerade programmet][node-view-four]


Som du kan se Service Fabric aktiverade två nya kopior av "P6" (ett för varje replik från partition  och **P7**). Service Fabric placera varje replik i sin dedikerade kopia av *CodePackage*. När du använder den exklusiva process modellen för ett angivet program kan flera kopior av en specifik *servicepack* vara aktiva på en nod. I föregående exempel är tre kopior av "min ServicePack" aktiva för **Fabric:/APP1**. Var och en av dessa aktiva kopior av "ServicePackageActivationId" har en associerad  . Detta ID identifierar kopian i Application **Fabric:/APP1**.

När du bara använder den delade process modellen för ett program finns det bara en aktiv kopia av *servicepack* på en nod. **ServicePackageActivationId** för den här aktiveringen av *servicepack* är en tom sträng. Detta är fallet, till exempel med **Fabric:/APP2**.

> [!NOTE]
>- Den delade processens värd modell motsvarar **ServicePackageActivationMode** är lika med **SharedProcess**. Detta är standard värd modellen och **ServicePackageActivationMode** behöver inte anges när tjänsten skapas.
>
>- Den exklusiva processens värd modell motsvarar **ServicePackageActivationMode** är lika med **ExclusiveProcess**. Om du vill använda den här inställningen bör du ange den explicit vid tidpunkten för att skapa tjänsten. 
>
>- Om du vill visa värd modellen för en tjänst frågar du [tjänst beskrivningen][p2]och tittar på värdet för **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Arbeta med ett distribuerat tjänst paket
En aktiv kopia av ett *servicepack* på en nod kallas för ett [distribuerat tjänst paket][p3]. När du använder den exklusiva process modellen för att skapa tjänster för ett angivet program kan det finnas flera distribuerade tjänst paket för samma *servicepack*. Om du utför åtgärder som är speciella för ett distribuerat tjänst paket bör du ange **ServicePackageActivationId** för att identifiera ett speciellt distribuerat tjänst paket. Ange till exempel ID om du [rapporterar hälsan för ett distribuerat tjänst paket][p4] eller om du vill [starta om kod paketet för ett distribuerat tjänst paket][p5].

Du kan ta reda på **ServicePackageActivationId** för ett distribuerat tjänst paket genom att fråga listan över [distribuerade tjänst paket][p3] på en nod. När du frågar efter [distribuerade tjänst typer][p6], [distribuerade repliker][p7]och [distribuerade kod paket][p8] på en nod innehåller frågeresultatet även **ServicePackageActivationId** för det överordnade distribuerade tjänst paketet.

> [!NOTE]
>- Under den delade processens värd modell, på en specifik nod, för ett givet program, aktive ras endast en kopia av *servicepack* . Det har en **ServicePackageActivationId** som är lika med *tom sträng* och behöver inte anges när du utför åtgärder som är relaterade till det distribuerade tjänst paketet. 
>
> - Under exklusivt process värd modell, på en specifik nod, för ett givet program, kan en eller flera kopior av en *servicepack* vara aktiva. Varje aktivering har en *icke-tom* **ServicePackageActivationId** som anges när du utför åtgärder som är relaterade till det distribuerade tjänst paketet. 
>
> - Om **ServicePackageActivationId** utelämnas används en *tom sträng* som standard. Om ett distribuerat tjänst paket som har Aktiver ATS under den delade process modellen finns, utförs åtgärden på den. Annars misslyckas åtgärden.
>
> - Fråga inte en gång och cachelagra **ServicePackageActivationId**. ID: t genereras dynamiskt och kan ändras av olika orsaker. Innan du utför en åtgärd som kräver **ServicePackageActivationId** bör du först fråga listan över [distribuerade tjänst paket][p3] på en nod. Använd sedan **ServicePackageActivationId** från frågeresultatet för att utföra den ursprungliga åtgärden.
>
>

## <a name="guest-executable-and-container-applications"></a>Körbara gäst program och behållar program
Service Fabric behandlar [körbara gäst][a2] program och [behållar][a3] program som fristående tjänster, som finns fristående. Det finns ingen Service Fabric körning i *ServiceHost* (en process eller container). Eftersom dessa tjänster är fristående är antalet repliker per *ServiceHost* inte tillämpligt för de här tjänsterna. Den vanligaste konfigurationen som används med dessa tjänster är Single-partition, med [InstanceCount][c2] lika med-1 (en kopia av Service koden som körs på varje nod i klustret). 

Standard- **ServicePackageActivationMode** för de här tjänsterna är **SharedProcess**, i vilket fall Service Fabric bara aktiverar en kopia av *servicepack* på en nod för ett angivet program.  Det innebär bara att en kopia av Service koden kör en nod. Om du vill att flera kopior av Service koden ska köras på en nod anger du **ServicePackageActivationMode** som **ExclusiveProcess** när du skapar tjänsten. Du kan till exempel göra detta när du skapar flera tjänster (*Service1* till *tjänst*) för *ServiceType* (anges i *ServiceManifest*) eller när din tjänst har flera partitioner. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Ändra värd modell för en befintlig tjänst
Vid tillfället kan du inte ändra värd modellen för en befintlig tjänst från en delad process till en exklusiv process (eller vice versa).

## <a name="choose-between-the-hosting-models"></a>Välj mellan värd modellerna
Du bör utvärdera vilken värd modell som passar bäst för dina behov. Den delade process modellen använder operativ system resurser bättre eftersom färre processer har skapats och flera repliker i samma process kan dela portar. Men om en av replikerna har ett fel där tjänsten måste ta ned tjänst värden, påverkar den alla andra repliker i samma process.

 Den exklusiva process modellen ger bättre isolering, med varje replik i sin egen process. Om ett fel uppstår i en av replikerna påverkas inte andra repliker. Den här modellen är användbar för fall där port delning inte stöds av kommunikations protokollet. Det underlättar möjligheten att tillämpa resurs styrning på replik nivå. Den exklusiva processen förbrukar dock fler operativ system resurser, eftersom den skapar en process för varje replik på noden.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Överväganden för exklusivt process modell och program modell
För de flesta program kan du modellera ditt program i Service Fabric genom att behålla en *ServiceType* per *servicepack*. 

I vissa fall kan Service Fabric också tillåta mer än en *ServiceType* per *servicepack* (och en *CodePackage* kan registrera fler än en *ServiceType*). Här följer några scenarier där dessa konfigurationer kan vara användbara:

- Du vill optimera resursutnyttjande genom att skapa färre processer och ha högre replik täthet per process.
- Repliker från olika *ServiceTypes* måste dela några vanliga data som har en hög initierings-eller minnes kostnad.
- Du har ett kostnads fritt tjänst erbjudande och du vill placera en gräns för resursutnyttjande genom att placera alla repliker av tjänsten i samma process.

Den exklusiva process värd modellen är inte konsekvent med en program modell som har flera *ServiceTypes* per *servicepack*. Detta beror på att flera *ServiceTypes* per *servicepack* har utformats för att uppnå högre resurs delning mellan repliker och möjliggör högre replik täthet per process. Den exklusiva process modellen är utformad för att uppnå olika resultat.

Överväg att använda flera *ServiceTypes* per *servicepack*, med en annan *CodePackage* som registrerar varje *ServiceType*. Anta att vi har en *servicepack* av typen MultiTypeServicePackage, som har två *CodePackages*:

- ' MyCodePackageA ', som registrerar *ServiceType* ' MyServiceTypeA '.
- ' MyCodePackageB ', som registrerar *ServiceType* ' MyServiceTypeB '.

Nu ska vi säga att vi skapar ett program, **Fabric:/SpecialApp**. I **Fabric:/SpecialApp** skapar vi följande två tjänster med den exklusiva process modellen:

- Service **Fabric:/SpecialApp/service** av typ ' MyServiceTypeA ', med två partitioner (till exempel **P1** och **P2**) och tre repliker per partition.
- Service **Fabric:/SpecialApp/ServiceB** av typen ' MyServiceTypeB ', med två partitioner (**P3** och **P4**) och tre repliker per partition.

På en specifik nod har båda tjänsterna två repliker var. Eftersom vi använde den exklusiva process modellen för att skapa tjänsterna, aktiverar Service Fabric en ny kopia av ' för varje replik. Varje aktivering av "MultiTypeServicePackage" startar en kopia av "MyCodePackageA" och "MyCodePackageB". Men endast en av "MyCodePackageA" eller "MyCodePackageB" är värd för repliken där "MultiTypeServicePackage" aktiverades. Följande diagram visar vyn node:


![Diagram som visar vyn Node.][node-view-five]


Vid aktivering av "MultiTypeServicePackage" för repliken av partition **P1** av service **Fabric:/SpecialApp/service**, "MyCodePackageA" är värd för repliken. ' MyCodePackageB ' körs. På samma sätt, vid aktivering av "MultiTypeServicePackage" för repliken av partition **P3** för service **Fabric:/SpecialApp/ServiceB**, "MyCodePackageB" är värd för repliken. ' MyCodePackageA ' körs. Därför ju större antal *CodePackages* (att registrera olika *ServiceTypes*) per *servicepack*, desto högre den redundanta resursanvändningen. 
 
 Men om vi skapar tjänsterna **Fabric:/SpecialApp/servicer** och **Fabric:/SpecialApp/ServiceB** med den delade Process modellen, aktiverar Service Fabric bara en kopia av "MultiTypeServicePackage" för application **Fabric:/SpecialApp**. "MyCodePackageA" är värd för alla repliker för service **Fabric:/SpecialApp/service**. ' MyCodePackageB ' är värd för alla repliker för service **Fabric:/SpecialApp/ServiceB**. Följande diagram visar vyn Node i den här inställningen: 


![Diagram över vyn Node för det distribuerade programmet][node-view-six]


I föregående exempel kan du tänka på att om "MyCodePackageA" registrerar både "MyServiceTypeA" och "MyServiceTypeB", och det inte finns någon "MyCodePackageB", finns det ingen redundant *CodePackage* som körs. Även om detta är korrekt överensstämmer inte den här program modellen med den exklusiva process värd modellen. Om målet är att ställa in varje replik i en egen dedikerad process behöver du inte registrera båda *ServiceTypes* från samma *CodePackage*. I stället lägger du bara till varje *ServiceType* i sitt eget *servicepack*.

### <a name="reliable-services-and-actor-forking-subprocesses"></a>Under processer för Reliable Services och aktörs förgreningar

Service Fabric har inte stöd för pålitliga tjänster och därmed pålitliga aktörer som delar under processer. Ett exempel på varför det inte finns stöd för [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext) kan inte användas för att registrera en under process som inte stöds och avbrutna token skickas endast till registrerade processer. Detta resulterar i alla typer av problem, till exempel uppgraderings fel, när det inte går att stänga under processer när den överordnade processen har tagit emot en token för uppsägning.

## <a name="next-steps"></a>Nästa steg
[Paketera ett program][a4] och Förbered det för distribution.

[Distribuera och ta bort program][a5]. Den här artikeln beskriver hur du använder PowerShell för att hantera program instanser.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: /rest/api/servicefabric/sfclient-api-createservice

[c1]: /dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: /dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: /powershell/module/servicefabric/new-servicefabricservice
[p2]: /powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[p4]: /powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: /powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: /powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: /powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
