---
title: Metod tips för Azure Service Fabric program design
description: Bästa praxis och design överväganden för att utveckla program och tjänster med hjälp av Azure Service Fabric.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: ddf846e9e3ac6add7cf3f584b702de5accfb22af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91538506"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Metod tips för Azure Service Fabric program design

Den här artikeln innehåller metod tips för att skapa program och tjänster på Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Bekanta dig med Service Fabric
* Läs [mer om Service Fabric?](service-fabric-content-roadmap.md) -artikeln.
* Läs om [Service Fabric program scenarier](service-fabric-application-scenarios.md).
* Förstå alternativen för programmerings modell genom att läsa [Översikt över Service Fabric programmerings modellen](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Vägledning för program design
Bekanta dig med den [allmänna arkitekturen](/azure/architecture/reference-architectures/microservices/service-fabric) i Service Fabric program och deras [design överväganden](/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Välj en API-Gateway
Använd en API Gateway-tjänst som kommunicerar med Server dels tjänster som sedan kan skalas ut. De vanligaste API Gateway-tjänsterna som används är:

- [Azure API Management](./service-fabric-api-management-overview.md), som är [integrerat med Service Fabric](./service-fabric-tutorial-deploy-api-management.md).
- [Azure IoT Hub](../iot-hub/index.yml) eller [Azure-Event Hubs](../event-hubs/index.yml), med hjälp av [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) för att läsa från Event Hub-partitioner.
- [Træfik omvänd proxy](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)med [Azure Service Fabric-providern](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Azure Application Gateway](../application-gateway/index.yml).

   > [!NOTE] 
   > Azure Application Gateway är inte direkt integrerad med Service Fabric. Azure API Management är vanligt vis det bästa valet.
- Din egen anpassade [ASP.net Core](./service-fabric-reliable-services-communication-aspnetcore.md) Web Application Gateway.

### <a name="stateless-services"></a>Tillstånds lösa tjänster
Vi rekommenderar att du alltid börjar med att skapa tillstånds lösa tjänster genom att använda [Reliable Services](./service-fabric-reliable-services-introduction.md) och lagra tillstånd i en Azure-databas, Azure Cosmos DB eller Azure Storage. Externt läge är den mer välbekanta metoden för de flesta utvecklare. Med den här metoden kan du också dra nytta av fråge funktioner i butiken.  

### <a name="when-to-use-stateful-services"></a>När du ska använda tillstånds känsliga tjänster
Överväg tillstånds känsliga tjänster när du har ett scenario för låg latens och måste hålla data nära beräkningen. Några exempel scenarier är IoT Digital-enheter, spel status, sessionstillstånd, cachelagring av data från en databas och långvariga arbets flöden som spårar anrop till andra tjänster.

Bestäm tids perioden för datakvarhållning:

- **Cachelagrade data**. Använd cachelagring när svars tid till externa butiker är ett problem. Använd en tillstånds känslig tjänst som din egen datacache eller Överväg att använda [SoCreate för öppen källkod Service Fabric distribuerat cacheminne](https://github.com/SoCreate/service-fabric-distributed-cache). I det här scenariot behöver du inte oroa dig om du förlorar alla data i cacheminnet.
- **Tidsbegränsade data**. I det här scenariot måste du hålla data nära beräkning under en viss tids period, men du får råd att förlora data i en *katastrof*. I många IoT-lösningar måste till exempel data ligga nära beräkning, till exempel när genomsnitts temperaturen under de senaste dagarna beräknas, men om dessa data går förlorade är de data punkter som registreras inte viktiga. I det här scenariot är det också viktigt att du inte säkerhetskopierar de enskilda data punkterna. Du säkerhetskopierar bara beräknade medelvärden som regelbundet skrivs till extern lagring.  
- **Långsiktiga data**. Tillförlitliga samlingar kan lagra dina data permanent. Men i det här fallet måste du [förbereda för haveri beredskap](./service-fabric-disaster-recovery.md), inklusive [Konfigurera principer för regelbunden säkerhets kopiering](./service-fabric-backuprestoreservice-configure-periodic-backup.md) för klustren. I praktiken konfigurerar du vad som händer om klustret förstörs i en katastrof, där du skulle behöva skapa ett nytt kluster och hur du distribuerar nya program instanser och återställer från den senaste säkerhets kopian.

Spara kostnader och förbättra tillgängligheten:
- Du kan minska kostnaderna genom att använda tillstånds känsliga tjänster eftersom du inte debiteras kostnader för data åtkomst och transaktioner från fjärrarkivet, och eftersom du inte behöver använda någon annan tjänst, t. ex. Azure cache för Redis.
- Att använda tillstånds känsliga tjänster främst för lagring och inte för beräkning är kostsamt, och vi rekommenderar inte det. Tänk på tillstånds känsliga tjänster som beräkning med billig lokal lagring.
- Genom att ta bort beroenden för andra tjänster kan du förbättra tjänstens tillgänglighet. Genom att hantera tillstånd med HA i klustret isoleras du från andra avbrott i tjänsten eller svars tider.

## <a name="how-to-work-with-reliable-services"></a>Så här arbetar du med Reliable Services
Med Service Fabric Reliable Services kan du enkelt skapa tillstånds lösa och tillstånds känsliga tjänster. Mer information finns i [Introduktion till Reliable Services](./service-fabric-reliable-services-introduction.md).
- Följ alltid [token för avbrott](./service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps) i `RunAsync()` metoden för tillstånds lösa och tillstånds känsliga tjänster och `ChangeRole()` metoden för tillstånds känsliga tjänster. Om du inte gör det vet Service Fabric inte om tjänsten kan stängas. Om du till exempel inte följer den token för avbrytande token kan mycket längre program uppgraderings tider uppstå.
-    Öppna och Stäng [kommunikations lyssnare](./service-fabric-reliable-services-communication.md) i tid och följ de token för avbrutna.
-    Blanda aldrig synkronisera kod med asynkron kod. Använd till exempel inte `.GetAwaiter().GetResult()` i dina asynkrona anrop. Använd asynkront *hela vägen* via anrops stacken.

## <a name="how-to-work-with-reliable-actors"></a>Så här arbetar du med Reliable Actors
Med Service Fabric Reliable Actors kan du enkelt skapa tillstånds känsliga, virtuella aktörer. Mer information finns i [Introduktion till Reliable Actors](./service-fabric-reliable-actors-introduction.md).

- Vi rekommenderar att du använder pub/sub-meddelanden mellan dina aktörer för att skala ditt program. Verktyg som tillhandahåller den här tjänsten omfattar [SoCreate med öppen källkod Service Fabric pub/sub](https://service-fabric-pub-sub.socreate.it/) och [Azure Service Bus](/azure/service-bus/).
- Gör aktörens tillstånd så [detaljerat som möjligt](./service-fabric-reliable-actors-state-management.md#best-practices).
- Hantera [aktörens livs cykel](./service-fabric-reliable-actors-state-management.md#best-practices). Ta bort aktörer om du inte kommer att använda dem igen. Att ta bort onödiga aktörer är särskilt viktigt när du använder den [temporära tillstånds leverantören](./service-fabric-reliable-actors-state-management.md#state-persistence-and-replication), eftersom all status är lagrad i minnet.
- På grund av deras egna [samtidiga samtidighet](./service-fabric-reliable-actors-introduction.md#concurrency)används aktörerna bäst som oberoende objekt. Skapa inte diagram av multi-skådespelare, synkrona metod anrop (var och en av de mest sannolika nätverks anropen) eller skapa förfrågningar om cirkulär aktör. Dessa påverkar avsevärt prestanda och skalning.
- Blanda inte synkronisera kod med asynkron kod. Använd asynkront för att förhindra prestanda problem.
- Gör inte långvariga anrop i aktörer. Med tids krävande anrop blockeras andra anrop till samma aktör, på grund av den omkopplade samtidigheten.
- Om du kommunicerar med andra tjänster genom att använda [Service Fabric fjärr kommunikation](./service-fabric-reliable-services-communication-remoting.md) och du skapar en `ServiceProxyFactory` skapar du fabriken på [aktörs service](./service-fabric-reliable-actors-using.md) nivå och *inte* på aktörs nivån.


## <a name="application-diagnostics"></a>Programdiagnostik
Var noggrann med att lägga till [program loggning](./service-fabric-diagnostics-event-generation-app.md) i tjänst anrop. Det hjälper dig att diagnostisera scenarier där tjänster kallar varandra. Om t. ex. ett anrop B anropar C-anrop, kan anropet gå sönder var som helst. Om du inte har tillräckligt med loggning är fel svårt att diagnostisera. Om tjänsterna loggar för mycket på grund av anrops volymer, se till att minst Logga fel och varningar.

## <a name="iot-and-messaging-applications"></a>IoT-och meddelande program
När du läser meddelanden från [azure IoT Hub](../iot-hub/index.yml) eller [Azure Event Hubs](../event-hubs/index.yml)använder du  [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor). ServiceFabricProcessor integreras med Service Fabric Reliable Services för att upprätthålla läsnings tillstånd från händelsehubben och push-överför nya meddelanden till dina tjänster via `IEventProcessor::ProcessEventsAsync()` metoden.


## <a name="design-guidance-on-azure"></a>Design rikt linjer för Azure
* Besök [Azure Architecture Center](/azure/architecture/microservices/) för utformnings vägledning om hur du [skapar mikrotjänster på Azure](/azure/architecture/microservices/).

* Besök [komma igång med Azure för att få](/gaming/azure/) [hjälp med att använda Service Fabric i spel tjänster](/gaming/azure/reference-architectures/multiplayer-synchronous-sf).
