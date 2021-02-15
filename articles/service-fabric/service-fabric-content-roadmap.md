---
title: Läs mer om Azure Service Fabric
description: Lär dig mer om grundläggande begrepp och viktiga områden i Azure Service Fabric. Ger en utökad översikt över Service Fabric och hur du skapar mikrotjänster.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 011ddf5db1555e83a1a61a349cc19ed791ab900b
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526774"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Vill du veta mer om Service Fabric?
Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster.  Service Fabric har ett stort ytdiagram, men det är mycket att lära sig.  Den här artikeln innehåller en sammanfattning av Service Fabric och beskriver viktiga begrepp, programmerings modeller, program livs cykel, testning, kluster och hälso övervakning. Läs [översikten](service-fabric-overview.md) och [Vad är mikrotjänster?](service-fabric-overview-microservices.md) för en introduktion och hur Service Fabric kan användas för att skapa mikrotjänster. Den här artikeln innehåller ingen omfattande innehålls lista, men länkar till översikt och att komma igång-artiklar för varje Service Fabric. 

## <a name="core-concepts"></a>Huvudkoncept
[Service Fabric terminologi](service-fabric-technical-overview.md), [program modell](service-fabric-application-model.md)och [programmerings modeller som stöds](service-fabric-choose-framework.md) innehåller fler begrepp och beskrivningar, men här är grunderna.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Design tid: tjänst typ, service paket och manifest, program typ, programpaket och manifest
En tjänst typ är namnet/versionen som tilldelats en tjänsts kod paket, data paket och konfigurations paket. Detta definieras i en ServiceManifest.xml-fil. Tjänst typen består av körbar kod och tjänst konfigurations inställningar, som läses in vid körning och statiska data som används av tjänsten.

Ett tjänst paket är en disk katalog som innehåller tjänst typens ServiceManifest.xml fil, som refererar till koden, statiska data och konfigurations paketen för tjänst typen. Ett tjänst paket kan till exempel referera till kod, statiska data och konfigurations paket som utgör en databas tjänst.

En program typ är namnet/versionen som tilldelas till en samling tjänst typer. Detta definieras i en ApplicationManifest.xml-fil.

![Service Fabric program typer och tjänst typer][cluster-imagestore-apptypes]

Programpaketet är en disk katalog som innehåller programmets typ ApplicationManifest.xml fil, som refererar till tjänst paketen för varje tjänst typ som utgör program typen. Ett programpaket för en e-postprograms typ kan till exempel innehålla referenser till ett Queue Service-paket, ett klient dels tjänst paket och ett databas tjänst paket.  

Filerna i program paket katalogen kopieras till Service Fabric klustrets avbildnings arkiv. Du kan sedan skapa ett namngivet program från den här program typen, som sedan körs i klustret. När du har skapat ett namngivet program kan du skapa en namngiven tjänst från en av program typens tjänst typer. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Kör tid: kluster och noder, namngivna program, namngivna tjänster, partitioner och repliker
Ett [Service Fabric kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras i. Kluster kan skalas upp till tusentals datorer.

En dator eller virtuell dator som ingår i ett kluster kallas för en nod. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel placeringsegenskaper. Varje dator eller virtuell dator har en Windows-tjänst som startar automatiskt, `FabricHost.exe` som börjar köras vid start och sedan startar två körbara filer: `Fabric.exe` och `FabricGateway.exe` . Dessa två körbara filer utgör noden. I utvecklings-eller testnings scenarier kan du vara värd för flera noder på en dator eller en virtuell dator genom att köra flera instanser av `Fabric.exe` och `FabricGateway.exe` .

Ett namngivet program är en samling med namngivna tjänster som utför en viss funktion eller funktion. En tjänst utför en fullständig och fristående funktion (den kan starta och köra oberoende av andra tjänster) och består av kod, konfiguration och data. När ett programpaket har kopierats till avbildnings arkivet skapar du en instans av programmet i klustret genom att ange programpaketets program typ (med hjälp av namn/version). Varje program typ instans tilldelas ett URI-namn som ser ut som *Fabric:/MyNamedApp*. I ett kluster kan du skapa flera namngivna program från en enda program typ. Du kan också skapa namngivna program från olika program typer. Varje namngivet program hanteras och versioner sker oberoende av varandra.

När du har skapat ett namngivet program kan du skapa en instans av en av tjänst typerna (en namngiven tjänst) i klustret genom att ange tjänst typen (med namnet/versionen). Varje instans av tjänst typ har tilldelats ett URI-namn som omfattas av dess URI för det namngivna programmet. Om du t. ex. skapar en "databas" med namnet "databas" i ett "MyNamedApp"-namngivet program, ser URI: n ut så här: *Fabric:/MyNamedApp/min databas*. I ett namngivet program kan du skapa en eller flera namngivna tjänster. Varje namngiven tjänst kan ha ett eget partitionsschema och instans-/replik antal. 

Det finns två typer av tjänster: tillstånds lösa och tillstånds känsliga. Tillstånds lösa tjänster lagrar inte tillstånd i tjänsten. Tillstånds lösa tjänster har ingen beständig lagring på alla eller lagrar beständigt tillstånd i en extern lagrings tjänst, till exempel Azure Storage, Azure SQL Database eller Azure Cosmos DB. En tillstånds känslig tjänst lagrar tillstånd i tjänsten och använder pålitliga samlingar eller Reliable Actors programmerings modeller för att hantera tillstånd. 

När du skapar en namngiven tjänst anger du ett partition schema. Tjänster med stora mängder tillstånd som delar data mellan partitioner. Varje partition ansvarar för en del av tjänstens fullständiga status, som är spridd över klustrets noder.  

I följande diagram visas relationen mellan program och tjänst instanser, partitioner och repliker.

![Partitioner och repliker inom en tjänst][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Partitionering, skalning och tillgänglighet
[Partitionering](service-fabric-concepts-partitioning.md) är inte unikt för Service Fabric. En väl känd form av partitionering är data partitionering eller horisontell partitionering. Tillstånds känsliga tjänster med stor delmängd för att dela data mellan partitioner. Varje partition ansvarar för en del av tjänstens fullständiga status. 

Replikerna för varje partition sprids över klustrets noder, vilket gör att den namngivna tjänstens tillstånd kan [skalas](service-fabric-concepts-scalability.md). När data behöver växa, ökar partitionerna och Service Fabric balansering av partitioner mellan noder för att effektivt kunna använda maskin varu resurser. Om du lägger till nya noder i klustret kommer Service Fabric att ombalansera partitionens repliker över det ökade antalet noder. Övergripande program prestanda förbättras och konkurrens för åtkomst till minnes minskningar. Om noderna i klustret inte används effektivt kan du minska antalet noder i klustret. Service Fabric åter balanserar om partitionens repliker över det minskade antalet noder för att bättre kunna använda maskin varan på varje nod.

Inom en partition har tillstånds lösa namngivna tjänster instanser och tillstånds känsliga namngivna tjänster har repliker. Vanligt vis har tillstånds lösa namngivna tjänster bara en enda partition eftersom de inte har något internt tillstånd, även om [det finns undantag](./service-fabric-concepts-partitioning.md#partition-service-fabric-stateless-services). Partition instanserna tillhandahåller [tillgänglighet](service-fabric-availability-services.md). Om en instans Miss lyckas fortsätter andra instanser att fungera normalt och sedan Service Fabric skapar en ny instans. Tillstånds känsliga namngivna tjänster upprätthåller sin status inom repliker och varje partition har en egen replik uppsättning. Läs-och skriv åtgärder utförs på en replik (kallas primär). Ändringar av tillstånd från Skriv åtgärder replikeras till flera andra repliker (kallas aktiva sekundära). Om en replik skulle krascha skapar Service Fabric en ny replik från befintliga repliker.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Tillståndslösa och tillståndskänsliga mikrotjänster för Service Fabric
Med Service Fabric kan du skapa program som består av mikrotjänster eller containrar. Tillståndslösa mikrotjänster (till exempel protokollgatewayar och webbproxyservrar) har inte ett föränderligt tillstånd utanför en begäran och svaret från tjänsten. Arbetsroller i Azure Cloud Services är ett exempel på en tillståndslös tjänst. Tillståndskänsliga mikrotjänster (till exempel användarkonton, databaser, enheter, kundvagnar och köer) har ett föränderligt och auktoritärt tillstånd bortom begäran och svaret. Dagens program med Internetskala består av en kombination av tillståndslösa och tillståndskänsliga mikrotjänster. 

En viktig skillnad med Service Fabric är starkt inriktad på att skapa tillstånds känsliga tjänster, antingen med de [inbyggda programmerings modellerna](service-fabric-choose-framework.md) eller med behållar tillstånds känsliga tjänster. [Programscenarier](service-fabric-application-scenarios.md) beskriver scenarier där tillståndskänsliga tjänster används.

Varför har tillstånds känsliga mikrotjänster tillsammans med tillstånds lösa? De två huvudsakliga orsakerna är:

* Du kan bygga av OLTP-tjänster med höga data flöden, låg latens, feltolerant Online Transaction Processing (OLTP) genom att hålla kod och data nära på samma dator. Några exempel är interaktiva butiker-, Sök-, Sakernas Internet-system (IoT), handels system, kredit korts bearbetning och bedrägeri identifierings system samt hantering av personliga poster.
* Du kan förenkla program designen. Tillstånds känsliga mikrotjänster tar bort behovet av ytterligare köer och cacheminnen som traditionellt krävs för att hantera tillgänglighets-och latens kraven för ett rent tillstånds löst program. Tillstånds känsliga tjänster är naturlig hög tillgänglighet och låg latens, vilket minskar antalet rörliga delar som ska hanteras i ditt program som helhet.

## <a name="supported-programming-models"></a>Programmeringsmodeller som stöds
Service Fabric erbjuder flera olika sätt att skriva och hantera dina tjänster. Tjänster kan använda Service Fabric API: er för att dra full nytta av plattformens funktioner och program ramverk. Tjänster kan också vara alla kompilerade körbara program som skrivits på valfritt språk och som finns på ett Service Fabric-kluster. Mer information finns i [programmerings modeller som stöds](service-fabric-choose-framework.md).

### <a name="containers"></a>Containers
Som standard distribuerar Service Fabric och aktiverar tjänster som processer. Service Fabric kan även distribuera tjänster i [behållare](service-fabric-containers-overview.md). Det är viktigt att du blandar tjänster i processer och tjänster i behållare i samma program. Service Fabric stöder distribution av Linux-behållare och Windows-behållare på Windows Server 2016. Du kan distribuera befintliga program, tillstånds lösa tjänster eller tillstånds känsliga tjänster i behållare. 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) är ett lätt ramverk för att skriva tjänster som integreras med Service Fabric plattform och som drar nytta av den fullständiga uppsättningen plattforms funktioner. Reliable Services kan vara tillstånds lösa (liknar de flesta tjänst plattformarna, till exempel webb servrar eller arbets roller i Azure Cloud Services), där tillstånd är sparat i en extern lösning, till exempel Azure DB eller Azure Table Storage. Reliable Services kan också vara tillstånds känslig, där tillstånd är sparat direkt i själva tjänsten med hjälp av pålitliga samlingar. Tillstånd har gjorts med [hög tillgänglighet](service-fabric-availability-services.md) via replikering och distribueras genom [partitionering](service-fabric-concepts-partitioning.md), allt hanteras automatiskt av Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Det [pålitliga aktörs](service-fabric-reliable-actors-introduction.md) ramverket byggt ovanpå Reliable Services är ett program ramverk som implementerar mönstret för virtuell aktör, baserat på aktörens design mönster. I det pålitliga aktörs ramverket används oberoende beräknings enheter med en enkel tråd körning som kallas aktörer. Det pålitliga aktörs ramverket innehåller inbyggd kommunikation för aktörer och förkonfigurerade tillstånds beständiga och skalbara konfigurationer.

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric integreras med [ASP.net Core](service-fabric-reliable-services-communication-aspnetcore.md) som en första klass programmerings modell för att skapa webb-och API-program.  ASP.NET Core kan användas på två olika sätt i Service Fabric:

- Är värd för en körbar gäst. Detta används främst för att köra befintliga ASP.NET Core-program på Service Fabric utan kod ändringar.
- Körs inuti en tillförlitlig tjänst. Detta möjliggör bättre integrering med Service Fabric Runtime och tillåter tillstånds känsliga ASP.NET Core-tjänster.

### <a name="guest-executables"></a>Körbara gästfiler
En [körbar gäst](service-fabric-guest-executables-introduction.md) är en befintlig, godtycklig körbar fil (skrivet på valfritt språk) som finns på ett Service Fabric-kluster tillsammans med andra tjänster. Körbara gäst program integreras inte direkt med Service Fabric-API: er. De har dock fortfarande nytta av de funktioner som plattform erbjuder, till exempel anpassad hälso tillstånd och belastnings rapportering och tjänst identifiering, genom att anropa REST-API: er. De har också fullständig support för program livs cykeln. 

## <a name="application-lifecycle"></a>Programlivscykel
Precis som med andra plattformar går ett program på Service Fabric vanligt vis igenom följande faser: design, utveckling, testning, distribution, uppgradering, underhåll och borttagning. Service Fabric ger förstklassig support för hela program livs cykeln för moln program, från utveckling genom distribution, daglig hantering och underhåll av eventuell inaktive ring. Tjänste modellen gör det möjligt för flera olika roller att delta oberoende av programmets livs cykel. [Service Fabric-programmets livs cykel](service-fabric-application-lifecycle.md) ger en översikt över API: erna och hur de används av de olika rollerna under faserna i Service Fabric programmets livs cykel. 

Hela appens livs cykel kan hanteras med [PowerShell-cmdletar](/powershell/module/ServiceFabric/New-ServiceFabricService), [CLI-kommandon](service-fabric-sfctl.md), [C# API: er](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [Java API](/java/api/overview/azure/servicefabric): er och [REST API: er](/rest/api/servicefabric/). Du kan också ställa in kontinuerlig integrering/kontinuerliga distributions pipeline med verktyg som [Azure-pipeliner](./service-fabric-tutorial-deploy-app-with-cicd-vsts.md) eller [Jenkins](/azure/developer/jenkins/deploy-to-service-fabric-cluster).

## <a name="test-applications-and-services"></a>Testa program och tjänster
För att skapa verkliga molnbaserade tjänster är det viktigt att kontrol lera att dina program och tjänster kan motstå verkliga problem. Tjänsten fel analys är utformad för att testa tjänster som bygger på Service Fabric. Med [fel analys tjänsten](service-fabric-testability-overview.md)kan du orsaka meningsfulla fel och köra fullständiga test scenarier mot dina program. Dessa fel och scenarier använder och validerar de många tillstånd och över gångar som en tjänst kommer att uppleva under hela sin livs längd, allt på ett kontrollerat, säkert och konsekvent sätt.

[Åtgärder](service-fabric-testability-actions.md) riktar en tjänst för testning med enskilda fel. En tjänst utvecklare kan använda dessa som bygg stenar för att skriva komplicerade scenarier. Exempel på simulerade fel är:

* Starta om en nod för att simulera ett antal situationer där en dator eller virtuell dator startas om.
* Flytta en replik av en tillstånds känslig tjänst för att simulera belastnings utjämning, redundans eller program uppgradering.
* Anropa kvorum i en tillstånds känslig tjänst för att skapa en situation där Skriv åtgärder inte kan fortsätta eftersom det inte finns tillräckligt med säkerhets kopior eller sekundära repliker för att acceptera nya data.
* Anropa data förlust i en tillstånds känslig tjänst för att skapa en situation där alla minnes minnes status är helt rensade.

[Scenarier](service-fabric-testability-scenarios.md) är komplexa åtgärder som består av en eller flera åtgärder. Fel analys tjänsten innehåller två inbyggda fullständiga scenarier:

* [Kaos-scenario](service-fabric-controlled-chaos.md)– simulerar kontinuerliga, överlagrade fel (både korrekt och oäkta) i hela klustret under en längre tid.
* [Failover-scenario](service-fabric-testability-scenarios.md#failover-test)– en version av kaos-testscenariot som är riktad till en speciell tjänstmall samtidigt som andra tjänster inte påverkas.

## <a name="clusters"></a>Kluster
Ett [Service Fabric kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras i. Kluster kan skalas upp till tusentals datorer. En dator eller en virtuell dator som ingår i ett kluster kallas för en klusternod. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel placeringsegenskaper. Varje dator eller virtuell dator har en tjänst för automatisk start `FabricHost.exe` som börjar köras vid start och sedan startar två körbara filer: Fabric.exe och FabricGateway.exe. Dessa två körbara filer utgör noden. För testnings scenarier kan du vara värd för flera noder på en enskild dator eller virtuell dator genom att köra flera instanser av `Fabric.exe` och `FabricGateway.exe` .

Service Fabric kluster kan skapas på virtuella eller fysiska datorer som kör Windows Server eller Linux. Du kan distribuera och köra Service Fabric program i valfri miljö där du har en uppsättning Windows Server-eller Linux-datorer som är sammankopplade: lokalt, på Microsoft Azure eller på någon annan moln leverantör.

### <a name="clusters-on-azure"></a>Kluster i Azure
Om du kör Service Fabric kluster i Azure kan du integrera med andra Azure-funktioner och-tjänster, vilket gör det enklare och mer tillförlitligt att utföra driften och hanteringen av klustret. Ett kluster är en Azure Resource Manager resurs, så du kan modellera kluster som andra resurser i Azure. Resource Manager ger också enkel hantering av alla resurser som används av klustret som en enda enhet. Kluster i Azure är integrerade med Azure Diagnostics och Azure Monitor loggar. Typer av klusternoder är [skalnings uppsättningar för virtuella datorer](../virtual-machine-scale-sets/index.yml), så att funktionen för automatisk skalning är inbyggd.

Du kan skapa ett kluster på Azure via [Azure Portal](service-fabric-cluster-creation-via-portal.md), från en [mall](service-fabric-cluster-creation-via-arm.md)eller från [Visual Studio](./service-fabric-cluster-creation-via-arm.md).

Med Service Fabric på Linux kan du bygga, distribuera och hantera hög tillgängliga, skalbara program på Linux precis som i Windows. Service Fabric Framework (Reliable Services och Reliable Actors) är tillgängliga i Java på Linux, förutom C# (.NET Core). Du kan också bygga [gästbaserade körbara tjänster](service-fabric-guest-executables-introduction.md) med valfritt språk eller ramverk. Dirigering av Docker-behållare stöds också. Docker-behållare kan köra körbara gäst program eller interna Service Fabric tjänster, som använder Service Fabric Framework. Mer information finns [i om Service Fabric på Linux](service-fabric-deploy-anywhere.md).

Det finns vissa funktioner som stöds i Windows, men inte i Linux. Läs mer i [skillnaderna mellan Service Fabric i Linux och Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Fristående kluster
Service Fabric innehåller ett installations paket som du kan använda för att skapa fristående Service Fabric-kluster lokalt eller i valfri moln leverantör. Fristående kluster ger dig friheten att vara värd för ett kluster oavsett var du vill. Om dina data omfattas av efterlevnad eller reglerings begränsningar, eller om du vill behålla dina data lokalt, kan du vara värd för ditt eget kluster och dina program. Service Fabric program kan köras i flera värd miljöer utan ändringar, så att dina kunskaper om att skapa program går över från en värd miljö till en annan. 

[Skapa ditt första fristående Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md)

Fristående Linux-kluster stöds inte ännu.

### <a name="cluster-security"></a>Klustersäkerhet
Kluster måste skyddas för att förhindra att obehöriga användare ansluter till klustret, särskilt när produktions arbets belastningar som körs på den. Även om det är möjligt att skapa ett oskyddat kluster gör det möjligt för anonyma användare att ansluta till det om hanterings slut punkter exponeras för det offentliga Internet. Det går inte att senare Aktivera säkerhet på ett oskyddat kluster: kluster säkerhet aktive ras när klustret skapas.

Kluster säkerhets scenarierna är:
* Säkerhet från nod till nod
* Säkerhet från klient till nod
* Service Fabric rollbaserad åtkomst kontroll

Mer information finns i [skydda ett kluster](service-fabric-cluster-security.md).

### <a name="scaling"></a>Skalning
Om du lägger till nya noder i klustret ombalanserar Service Fabric partitionens repliker och instanser över det ökade antalet noder. Övergripande program prestanda förbättras och konkurrens för åtkomst till minnes minskningar. Om noderna i klustret inte används effektivt kan du minska antalet noder i klustret. Service Fabric åter balanserar partitionens repliker och instanser över det minskade antalet noder för att bättre kunna använda maskin varan på varje nod. Du kan skala kluster på Azure antingen [manuellt](service-fabric-cluster-scale-in-out.md) eller [via programmering](service-fabric-cluster-programmatic-scaling.md). Fristående kluster kan skalas [manuellt](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Kluster uppgraderingar
Med jämna mellanrum släpps nya versioner av Service Fabric Runtime. Utföra körning eller infrastruktur resurser, uppgraderingar i klustret så att du alltid kör en version som [stöds](service-fabric-support.md). Förutom Fabric-uppgraderingar kan du också uppdatera kluster konfigurationen, till exempel certifikat eller program portar.

Ett Service Fabric-kluster är en resurs som du äger, men som hanteras delvis av Microsoft. Microsoft ansvarar för att korrigera det underliggande operativ systemet och utföra infrastruktur uppdateringar i klustret. Du kan ange att klustret ska ta emot automatiska Fabric-uppgraderingar, när Microsoft släpper en ny version eller väljer att välja en infrastruktur version som stöds som du vill använda. Uppgraderingar av infrastruktur resurser och konfigurationer kan ställas in via Azure Portal eller via Resource Manager. Mer information finns i [uppgradera ett Service Fabric-kluster](service-fabric-cluster-upgrade.md). 

Ett fristående kluster är en resurs som du helt äger. Du ansvarar för korrigering av underliggande operativ system och att initiera Fabric-uppgraderingar. Om klustret kan ansluta till [https://www.microsoft.com/download](https://www.microsoft.com/download) kan du ange att klustret automatiskt ska ladda ned och etablera det nya Service Fabric Runtime-paketet. Sedan initierar du uppgraderingen. Om klustret inte kan komma åt [https://www.microsoft.com/download](https://www.microsoft.com/download) kan du hämta det nya Runtime-paketet manuellt från en ansluten Internet-dator och sedan starta uppgraderingen. Om du vill ha mer information kan du läsa [uppgradera ett fristående Service Fabric kluster](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Hälsoövervakning
Service Fabric introducerar en [hälso modell](service-fabric-health-introduction.md) som har utformats för att flagga kluster och program villkor i fel tillstånd för specifika entiteter (till exempel klusternoder och tjänst repliker). Hälso modellen använder hälso rapporter (system komponenter och övervaknings rapporter). Målet är enkelt och snabbt att diagnostisera och reparera. Service Writers måste tänka på både hälso tillstånd och hur man [utformar hälso rapporter](service-fabric-report-health.md#design-health-reporting). Alla villkor som kan påverka hälsan bör rapporteras, särskilt om det kan hjälpa till att flagga problem nära roten. Hälso informationen kan spara tid och ansträngning för fel sökning och undersökning när tjänsten är igång och körs i produktions skala.

Service Fabric rapporteraren övervakar identifierade villkor för intresse. De rapporterar om dessa villkor baserat på deras lokala vy. [Hälso arkivet](service-fabric-health-introduction.md#health-store) sammanställer hälso data som skickas av alla rapporter för att avgöra om entiteterna är globalt felfria. Modellen är avsedd att vara omfattande, flexibel och lätt att använda. Hälso rapportens kvalitet bestämmer noggrannheten för klustrets hälso tillstånd. Falska positiva identifieringar som felaktigt visar fel som inte är felfria kan påverka uppgraderingar eller andra tjänster som använder hälso data. Exempel på sådana tjänster är reparations tjänster och aviserings metoder. Därför behövs det en del tanke på att ge rapporter som upprättar förutsättningar på bästa möjliga sätt.

Rapportering kan göras från:
* Den övervakade Service Fabric tjänst repliken eller-instansen.
* Interna övervaknings regler distribueras som en Service Fabric tjänst (till exempel en Service Fabric tillstånds lös tjänst som övervakar villkor och ärende rapporter). Övervaknings enheten kan distribueras på alla noder eller kan tillhör till den övervakade tjänsten.
* Interna övervaknings grupper som körs på Service Fabric noder, men som inte implementeras som Service Fabric-tjänster.
* Externa övervaknings grupper som avsöker resursen från en plats utanför Service Fabrics klustret (till exempel övervaknings tjänsten som Gomez).

Service Fabric-komponenter rapporterar hälsa på alla entiteter i klustret. [System hälso rapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) ger insyn i kluster-och programfunktioner och flaggar problem genom hälso tillstånd. För program och tjänster kontrollerar system hälso rapporter att entiteter har implementerats och fungerar korrekt från Service Fabric körnings perspektivet. Rapporterna ger ingen hälso övervakning av tjänstens affärs logik eller identifierar processer som har slutat svara. För att lägga till hälso information som är unik för din tjänsts logik, [implementera anpassad hälso rapportering](service-fabric-report-health.md) i dina tjänster.

Service Fabric tillhandahåller flera olika sätt att [Visa hälso rapporter](service-fabric-view-entities-aggregated-health.md) sammanställda i hälso lagret:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) eller andra visualiserings verktyg.
* Hälso frågor (via [PowerShell](/powershell/module/ServiceFabric/New-ServiceFabricService), [CLI](service-fabric-sfctl.md), [C# FabricClient API: er](/dotnet/api/system.fabric.fabricclient.healthclient) och [Java FabricClient-API: er](/java/api/system.fabric)eller rest- [API](/rest/api/servicefabric): er).
* Allmänna frågor som returnerar en lista över entiteter som har hälso tillstånd som en av egenskaperna (via PowerShell, CLI, API: er eller REST).

## <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik
[Övervakning och diagnostik](service-fabric-diagnostics-overview.md) är avgörande för att utveckla, testa och distribuera program och tjänster i valfri miljö. Service Fabric lösningar fungerar bäst när du planerar och implementerar övervakning och diagnostik som hjälper till att säkerställa att program och tjänster fungerar som förväntat i en lokal utvecklings miljö eller i produktion.

Huvud målen för övervakning och diagnostik är att:

- Identifiera och diagnostisera problem med maskin vara och infrastruktur
- Identifiera problem med program vara och appar, minska avbrott i tjänsten
- Förstå beslut om resursanvändning och hjälp enhets åtgärder
- Optimera prestanda för program, tjänster och infrastruktur
- Generera affärs insikter och identifiera förbättrings områden
 
Det totala arbets flödet för övervakning och diagnostik består av tre steg:

1. Händelse generering: Detta inkluderar händelser (loggar, spårning, anpassade händelser) på infrastruktur (kluster), plattform och program-/tjänst nivå
2. Händelse agg regering: genererade händelser måste samlas in och aggregeras innan de kan visas
3. Analys: händelser behöver visualiseras och vara tillgängliga i viss form för att tillåta analys och Visa vid behov

Det finns flera produkter som beskriver dessa tre områden och du kan välja olika tekniker för var och en. Mer information finns i avsnittet [om övervakning och diagnostik för Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du skapar ett [kluster i Azure](service-fabric-cluster-creation-via-portal.md) eller ett [fristående kluster i Windows](service-fabric-cluster-creation-for-windows-server.md).
* Prova att skapa en tjänst med hjälp av programmeringsmodellerna [Reliable Services](service-fabric-reliable-services-quick-start.md) eller [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Lär dig hur du [migrerar från Cloud Services](service-fabric-cloud-services-migration-differences.md).
* Lär dig att [övervaka och diagnostisera tjänster](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Lär dig att [testa dina appar och tjänster](service-fabric-testability-overview.md).
* Lär dig att [Hantera och dirigera kluster resurser](service-fabric-cluster-resource-manager-introduction.md).
* Titta på [Service Fabric exemplen](/samples/browse/?products=azure).
* Läs mer om [Service Fabric support alternativ](service-fabric-support.md).
* Läs [Team bloggen](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) för artiklar och meddelanden.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
