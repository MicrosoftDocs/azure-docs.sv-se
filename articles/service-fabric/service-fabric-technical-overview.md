---
title: Lär dig mer om Azure Service Fabric terminologi
description: Lär dig mer om viktiga Service Fabric terminologi och begrepp som används i resten av dokumentationen.
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.openlocfilehash: 2ac4b81a284ed8c38bc9cefccd08db5afa51d600
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575949"
---
# <a name="service-fabric-terminology-overview"></a>Översikt över Service Fabric terminologi

Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster.  Du kan vara [värd för Service Fabric kluster var som helst](service-fabric-deploy-anywhere.md): Azure, i ett lokalt Data Center eller på någon annan moln leverantör.  Service Fabric är Orchestrator som driver [Azure Service Fabric-nät](../service-fabric-mesh/index.yml). Du kan använda valfritt ramverk för att skriva dina tjänster och välja var du vill köra programmet från flera miljö val. Den här artikeln beskriver den terminologi som används av Service Fabric för att förstå de termer som används i dokumentationen.

## <a name="infrastructure-concepts"></a>Infrastruktur koncept

**Kluster**: en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras i.  Kluster kan skalas upp till tusentals datorer.

**Nod**: en dator eller en virtuell dator som ingår i ett kluster kallas för en *nod*. Varje nod tilldelas ett nodnamn (sträng). Noder har egenskaper, till exempel placerings egenskaper. Varje dator eller virtuell dator har en Windows-tjänst som startar automatiskt, `FabricHost.exe` som börjar köras vid start och sedan startar två körbara filer: `Fabric.exe` och `FabricGateway.exe` . Dessa två körbara filer utgör noden. För testnings scenarier kan du vara värd för flera noder på en enskild dator eller virtuell dator genom att köra flera instanser av `Fabric.exe` och `FabricGateway.exe` .

## <a name="application-and-service-concepts"></a>Program-och tjänst koncept

**Service Fabric nätprogram**: Service Fabric nätprogram beskrivs av resurs modellen (yaml och JSON-resursfiler) och kan distribueras till alla miljöer där Service Fabric körs.

**Service Fabric internt program**: Service Fabric inbyggda program beskrivs av den interna program modellen (XML-baserade program-och tjänst manifest).  Service Fabric inbyggda program kan inte köras i Service Fabric nät.

### <a name="service-fabric-mesh-application-concepts"></a>Service Fabric nät-programkoncept

**Program**: ett program är distributions enheten, versions hantering och livs längd för ett nät program. Livs cykeln för varje program instans kan hanteras separat.  Program består av ett eller flera service kod paket och inställningar. Ett program definieras med hjälp av Azure Resource Model-schemat (RM).  Tjänster beskrivs som egenskaper för program resursen i en RM-mall.  Nätverk och volymer som används av programmet refereras av programmet.  När du skapar ett program, modelleras program, tjänster, nätverk och volym (er) med hjälp av Service Fabric resurs modell.

**Tjänst**: en tjänst i ett program representerar en mikrotjänst och utför en fullständig och fristående funktion. Varje tjänst består av ett eller flera kod paket som beskriver allt som behövs för att köra behållar avbildningen som är associerad med kod paketet.  Antalet tjänster i ett program kan skalas upp och ned.

**Nätverk**: en nätverks resurs skapar ett privat nätverk för dina program och är oberoende av de program eller tjänster som kan referera till det. Flera tjänster från olika program kan vara en del av samma nätverk. Nätverk är distributions bara resurser som refereras till av program.

**Kod paket**: kod paket beskriver allt som behövs för att köra behållar avbildningen som är associerad med kod paketet, inklusive följande:

* Behållarens namn, version och register
* PROCESSOR-och minnes resurser som krävs för varje behållare
* Nätverks slut punkter
* Volymer som ska monteras i behållaren och som refererar till en separat volym resurs.

Alla kod paket som definieras som en del av en program resurs distribueras och aktive ras tillsammans som en grupp.

**Volym**: volymer är kataloger som monteras i dina behållar instanser som du kan använda för att spara tillstånd. Azure Files volym driv rutinen monterar en Azure Files resurs till en behållare och ger tillförlitlig data lagring via alla API: er som stöder fil lagring. Volymer är distributions bara resurser som refereras till av program.

### <a name="service-fabric-native-application-concepts"></a>Service Fabric-koncept för interna program

**Program**: ett program är en samling av komponent tjänster som utför en viss funktion eller funktion. Livs cykeln för varje program instans kan hanteras separat.

**Tjänst**: en tjänst utför en komplett och fristående funktion och kan starta och köras oberoende av andra tjänster. En tjänst består av kod, konfiguration och data. För varje tjänst består kod av binärfilerna för körbara filer, konfigurationen består av tjänst inställningar som kan läsas in vid körning och data består av valfria statiska data som ska användas av tjänsten.

**Program typ**: namn/version som tilldelas till en samling tjänst typer. Den definieras i en `ApplicationManifest.xml` fil och bäddas in i en Programpakets katalog. Katalogen kopieras sedan till Service Fabric klustrets avbildnings arkiv. Du kan sedan skapa ett namngivet program från den här program typen i klustret.

Mer information finns i artikeln om [program modellen](service-fabric-application-model.md) .

**Programpaket**: en disk katalog som innehåller program typens `ApplicationManifest.xml` fil. Refererar till tjänst paketen för varje tjänst typ som utgör program typen. Filerna i programpaketets katalog kopieras till Service Fabric klustrets avbildnings arkiv. Ett programpaket för en e-postprograms typ kan till exempel innehålla referenser till ett kö-service-paket, ett klient dels tjänst paket och ett databas tjänst paket.

**Namngivet program**: när du har kopierat ett programpaket till avbildnings arkivet skapar du en instans av programmet i klustret. Du skapar en instans när du anger programpaketets program typ, genom att använda dess namn eller version. Varje program typ instans tilldelas ett URI-namn (Uniform Resource Identifier) som ser ut så här: `"fabric:/MyNamedApp"` . I ett kluster kan du skapa flera namngivna program från en enda program typ. Du kan också skapa namngivna program från olika program typer. Varje namngivet program hanteras och versioner sker oberoende av varandra.

**Tjänst typ**: namnet/versionen som tilldelats en tjänsts kod paket, data paket och konfigurations paket. Tjänst typen definieras i `ServiceManifest.xml` filen och bäddas in i en katalog för tjänst paket. Katalogen för tjänst paketet refereras sedan till av en Programpakets `ApplicationManifest.xml` fil. När du har skapat ett namngivet program i klustret kan du skapa en namngiven tjänst från en av program typens tjänst typer. Tjänst typens `ServiceManifest.xml` fil beskriver tjänsten.

Mer information finns i artikeln om [program modellen](service-fabric-application-model.md) .

Det finns två typer av tjänster:

* **Tillstånds lös**: Använd en tillstånds lös tjänst när tjänstens beständiga tillstånd lagras i en extern lagrings tjänst, till exempel Azure Storage, Azure SQL Database eller Azure Cosmos dB. Använd en tillstånds lös tjänst när tjänsten inte har någon beständig lagring. Till exempel, för en kalkylator tjänst där värden skickas till tjänsten, utförs en beräkning som använder dessa värden och ett resultat returneras.
* **Tillstånds känslig**: Använd en tillstånds känslig tjänst när du vill Service Fabric hantera tjänstens tillstånd via dess pålitliga samlingar eller Reliable Actors programmerings modeller. När du skapar en namngiven tjänst anger du hur många partitioner som du vill sprida ditt tillstånd över för skalbarhet. Ange också hur många gånger du vill replikera ditt tillstånd mellan noder, för tillförlitlighet. Varje namngiven tjänst har en enda primär replik och flera sekundära repliker. Du ändrar tillstånd för den namngivna tjänsten när du skriver till den primära repliken. Service Fabric replikerar sedan det här läget till alla sekundära repliker för att hålla ditt tillstånd synkroniserat. Service Fabric identifierar automatiskt när en primär replik Miss lyckas och höjer en befintlig sekundär replik till en primär replik. Service Fabric skapar sedan en ny sekundär replik.  

**Repliker eller instanser** hänvisar till kod (och tillstånd för tillstånds känsliga tjänster) för en tjänst som distribueras och körs. Se [repliker och instanser](service-fabric-concepts-replica-lifecycle.md).

**Omkonfigurationen** avser processen för alla ändringar i tjänstens replik uppsättning. Se [omkonfiguration](service-fabric-concepts-reconfiguration.md).

**Tjänst paket**: en disk katalog som innehåller tjänst typens `ServiceManifest.xml` fil. Den här filen refererar till koden, statiska data och konfigurations paketen för tjänst typen. Filerna i tjänst paket katalogen refereras till av program typens `ApplicationManifest.xml` fil. Ett tjänst paket kan till exempel referera till kod, statiska data och konfigurations paket som utgör en databas tjänst.

**Namngiven tjänst**: när du har skapat ett namngivet program kan du skapa en instans av en av tjänst typerna i klustret. Du anger tjänst typen genom att använda dess namn/version. Varje instans av tjänst typ har tilldelats ett URI-namn som omfattas av dess URI för det namngivna programmet. Om du t. ex. skapar en "databas" med namnet "databas" i ett "MyNamedApp"-namngivet program, ser URI: n ut så här: `"fabric:/MyNamedApp/MyDatabase"` . I ett namngivet program kan du skapa flera namngivna tjänster. Varje namngiven tjänst kan ha ett eget partitionsschema och instans-eller replik antal.

**Kod paket**: en disk katalog som innehåller tjänst typens körbara filer, vanligt vis exe/dll-filer. Filerna i kod paket katalogen refereras till av tjänst typens `ServiceManifest.xml` fil. När du skapar en namngiven tjänst kopieras kod paketet till den nod eller de noder som har valts för att köra den namngivna tjänsten. Sedan börjar koden att köras. Det finns två typer av kod paket körbara filer:

* **Körbara gäst program**: körbara filer som körs som-finns på värd operativ systemet (Windows eller Linux). Dessa körbara filer länkar inte till eller refererar till Service Fabric runtime-filer och använder därför inte några Service Fabric programmerings modeller. Dessa körbara filer kan inte använda vissa Service Fabric funktioner, till exempel namngivnings tjänsten för slut punkts identifiering. Körbara gäst program kan inte rapportera inläsnings mått som är speciella för varje tjänst instans.
* **Körbara tjänst värden**: körbara filer som använder Service Fabric programmerings modeller genom att länka till Service Fabric runtime-filer, vilket aktiverar Service Fabric funktioner. Till exempel kan en namngiven tjänst instans registrera slut punkter med Service Fabric Naming Service och kan också rapportera inläsnings mått.

**Data paket**: en disk katalog som innehåller tjänst typens statiska skrivskyddade datafiler, vanligt vis foto-, ljud-och videofiler. Filerna i data paket katalogen refereras till av tjänst typens `ServiceManifest.xml` fil. När du skapar en namngiven tjänst kopieras data paketet till den nod eller de noder som har valts för att köra den namngivna tjänsten. Koden börjar köras och kan nu komma åt datafilerna.

**Konfigurations paket**: en disk katalog som innehåller tjänst typens statiska, skrivskyddade konfigurationsfiler, vanligt vis textfiler. Filerna i konfigurations paket katalogen refereras till av tjänst typens `ServiceManifest.xml` fil. När du skapar en namngiven tjänst kopieras filerna i konfigurations paketet till en eller flera noder som har valts för att köra den namngivna tjänsten. Sedan börjar koden att köras och kan nu komma åt konfigurationsfilerna.

**Behållare**: som standard distribuerar Service Fabric och aktiverar tjänster som processer. Service Fabric kan även distribuera tjänster i behållar avbildningar. Behållare är en virtualiseringsteknik som sammanfattar det underliggande operativ systemet från program. Ett program och dess körning, beroenden och system bibliotek körs i en behållare. Behållaren har fullständig, privat åtkomst till behållarens egna isolerade vy av operativ systemets konstruktioner. Service Fabric stöder Windows Server-behållare och Docker-behållare i Linux. Mer information finns i [Service Fabric och behållare](service-fabric-containers-overview.md).

**Partitionsschema**: när du skapar en namngiven tjänst anger du ett partitionsschema. Tjänster med stor delmängd för att dela data mellan partitioner, vilket sprider tillstånd över klustrets noder. Genom att dela data mellan partitioner kan din namngivna tjänsts tillstånd skala. Inom en partition har tillstånds lösa namngivna tjänster instanser, medan tillstånds känsliga namngivna tjänster har repliker. Vanligt vis har tillstånds lösa namngivna tjänster bara en partition, eftersom de inte har något internt tillstånd. Partition instanserna tillhandahåller tillgänglighet. Om en instans Miss lyckas fortsätter andra instanser att fungera normalt, och sedan skapar Service Fabric en ny instans. Tillstånds känsliga namngivna tjänster upprätthåller sitt tillstånd inom repliker och varje partition har en egen replik uppsättning så att statusen hålls synkroniserad. Om en replik skulle krascha skapar Service Fabric en ny replik från befintliga repliker.

Mer information finns i artikeln [Partition Service Fabric Reliable Services](service-fabric-concepts-partitioning.md) .

## <a name="system-services"></a>System tjänster

Det finns system tjänster som skapas i alla kluster som tillhandahåller plattforms funktionerna i Service Fabric.

**Naming Service**: varje Service Fabric-kluster har en Naming Service som matchar tjänst namn till en plats i klustret. Du hanterar tjänst namn och egenskaper, t. ex. en Internet Domain Name System (DNS) för klustret. Klienter kommunicerar säkert med valfri nod i klustret med hjälp av Naming Service för att matcha ett tjänst namn och dess plats. Program flyttas i klustret. Detta kan till exempel bero på fel, resurs utjämning eller storleks ändring av klustret. Du kan utveckla tjänster och klienter som matchar den aktuella nätverks platsen. Klienterna hämtar den faktiska datorns IP-adress och port där den körs för närvarande.

Läs [kommunicera med tjänster](service-fabric-connect-and-communicate-with-services.md) för mer information om API: er för klient-och tjänst kommunikation som fungerar med Naming Service.

**Avbildningsarkiv tjänst**: varje Service Fabric-kluster har en avbildningsarkiv tjänst där distribuerade, versioner av programpaket sparas. Kopiera ett programpaket till Avbildningsarkiv och registrera sedan den program typ som ingår i programpaketet. När program typen har tillhandahållits skapar du ett namngivet program från det. Du kan avregistrera en program typ från tjänsten Avbildningsarkiv när alla namngivna program har tagits bort.

Läs [förstå ImageStoreConnectionString-inställningen](service-fabric-image-store-connection-string.md) för mer information om avbildningsarkiv-tjänsten.

Läs artikeln [distribuera en program](service-fabric-deploy-remove-applications.md) -artikel om du vill ha mer information om hur du distribuerar program till avbildningsarkiv-tjänsten.

**Redundanshanteraren tjänst**: varje Service Fabric-kluster har en Redundanshanteraren tjänst som ansvarar för följande åtgärder:

 - Utför funktioner relaterade till hög tillgänglighet och konsekvens för tjänster.
 - Dirigerar program-och kluster uppgraderingar.
 - Interagerar med andra system komponenter.

**Repair Manager tjänst**: det här är en valfri system tjänst som gör det möjligt att utföra reparations åtgärder på ett kluster på ett sätt som är säkert, kan automatiseras och transparent. Reparations hanteraren används i:

   - Utföra underhålls reparationer i Azure i [silver-och Gold](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) -arbetsService Fabric kluster.
   - Utföra reparations åtgärder för [programmet för uppdaterings dirigering](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Distributions-och program modeller

Om du vill distribuera dina tjänster måste du beskriva hur de ska köras. Service Fabric stöder tre olika distributions modeller:

### <a name="resource-model-preview"></a>Resurs modell (för hands version)

Service Fabric resurser är allt som kan distribueras individuellt för att Service Fabric; inklusive program, tjänster, nätverk och volymer. Resurser definieras med hjälp av en JSON-fil som kan distribueras till en kluster slut punkt.  För Service Fabric nät används Azures resurs modell schema. Ett YAML-filschema kan också användas för att enklare utforma definitionsfiler. Resurser kan distribueras var som helst Service Fabric körs. Resurs modellen är det enklaste sättet att beskriva Service Fabric-program. Huvud fokus är vid enkel distribution och hantering av behållare tjänster. Läs mer i [Introduktion till Service Fabric resurs modell](../service-fabric-mesh/service-fabric-mesh-service-fabric-resources.md).

### <a name="native-model"></a>Inbyggd modell

Den inbyggda program modellen ger dina program fullständig åtkomst till Service Fabric. Program och tjänster definieras som registrerade typer i XML-MANIFEST filer.

Den inbyggda modellen stöder Reliable Services-och Reliable Actors-ramverk, som ger åtkomst till API: erna för Service Fabric Runtime och kluster hanterings-API: er i C# och Java. Den inbyggda modellen stöder också godtyckliga behållare och körbara filer. Den inbyggda modellen stöds inte i [Service Fabric nät miljön](../service-fabric-mesh/service-fabric-mesh-overview.md).

**Reliable Services**: ett API för att bygga tillstånds lösa och tillstånds känsliga tjänster. Tillstånds känsliga tjänster lagrar sitt tillstånd i Reliable Collections, till exempel en ord lista eller en kö. Du kan också ansluta olika kommunikations stackar, till exempel Web API och Windows Communication Foundation (WCF).

**Reliable Actors**: ett API för att bygga tillstånds lösa och tillstånds känsliga objekt via programmerings modellen för Virtual aktör. Den här modellen är användbar när du har många oberoende enheter av beräkning eller tillstånd. Den här modellen använder en Turn-baserad trådad modell, så det är bäst att undvika kod som anropar andra aktörer eller tjänster eftersom en enskild aktör inte kan bearbeta andra inkommande begär Anden förrän alla utgående begär Anden har avslut ATS.

Du kan också köra dina befintliga program på Service Fabric:

**Behållare**: Service Fabric stöder distribution av Docker-behållare på Linux-och Windows Server-behållare i windows Server 2016, tillsammans med stöd för isolerings läget för Hyper-V. I Service Fabric [program modellen](service-fabric-application-model.md)representerar en behållare en program värd där flera tjänst repliker placeras. Service Fabric kan köra alla behållare, och scenariot liknar scenariot för gäst körning, där du paketerar ett befintligt program i en behållare. Dessutom kan du [köra Service Fabric tjänster i behållare](service-fabric-services-inside-containers.md) också.

**Körbara gäst program**: du kan köra vilken typ av kod som helst, till exempel Node.js, python, Java eller C++ i Azure Service Fabric som en tjänst. Service Fabric avser de här typerna av tjänster som körbara gäst program, som behandlas som tillstånds lösa tjänster. Fördelarna med att köra en körbar gäst fil i ett Service Fabric kluster är hög tillgänglighet, hälso övervakning, hantering av program livs cykel, hög densitet och identifiering.

Mer information finns i artikeln [Välj en programmerings modell för din tjänst](service-fabric-choose-framework.md) .

### <a name="docker-compose"></a>Docker-sammanställning 

[Docker Compose](https://docs.docker.com/compose/) är en del av Docker-projektet. Service Fabric ger begränsat stöd för [distribution av program med hjälp av Docker-modellen](service-fabric-docker-compose.md).

## <a name="environments"></a>Miljöer

Service Fabric är en plattforms teknik med öppen källkod som flera olika tjänster och produkter baseras på. Microsoft tillhandahåller följande alternativ:

 - **Azure Service Fabric-nät**: en fullständigt hanterad tjänst för att köra Service Fabric program i Microsoft Azure.
 - **Azure-Service Fabric**: det värd erbjudande som Azure hosted Service Fabric. Den ger integration mellan Service Fabric och Azure-infrastrukturen, tillsammans med uppgradering och konfigurations hantering av Service Fabric-kluster.
 - **Fristående Service Fabric**: en uppsättning installations-och konfigurations verktyg för att [distribuera Service Fabric kluster var som helst](./service-fabric-deploy-anywhere.md) (lokalt eller i valfri moln leverantör). Hanteras inte av Azure.
 - **Service Fabric utvecklings kluster**: tillhandahåller en lokal utvecklings miljö för Windows, Linux eller Mac för utveckling av Service Fabric program.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Support mat ris för miljö-, Ramverks-och distributions modell

Olika miljöer har olika stöd nivåer för ramverk och distributions modeller. I följande tabell beskrivs kombinationerna av ramverk och distributions modeller som stöds.

| Typ av program | Beskrivs av | Azure Service Fabric Mesh | Azure Service Fabric-kluster (valfritt OS)| Lokalt kluster | Fristående kluster |
|---|---|---|---|---|---|
| Service Fabric nätprogram | Resurs modell (YAML & JSON) | Stöds |Stöds inte | Windows – stöds, Linux och Mac – stöds inte | Windows – stöds inte |
|Service Fabric inbyggda program | Intern program modell (XML) | Stöds inte| Stöds|Stöds|Windows – stöds|

I följande tabell beskrivs de olika program modellerna och de verktyg som finns för dem mot Service Fabric.

| Typ av program | Beskrivs av | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric nätprogram | Resurs modell (YAML & JSON) | VS 2017 |Stöds inte |Stöds inte | Endast nät miljö som stöds | Stöds inte|
|Service Fabric inbyggda program | Intern program modell (XML) | VS 2017 och VS 2015| Stöds|Stöds|Stöds|Stöds|

## <a name="next-steps"></a>Nästa steg

Läs mer om Service Fabric:

* [Översikt över Service Fabric](service-fabric-overview.md)
* [Varför använda mikrotjänster för att bygga program?](service-fabric-overview-microservices.md)
* [Programscenarier](service-fabric-application-scenarios.md)

Lär dig mer om Service Fabric nät:

* [Översikt över Service Fabric nät](../service-fabric-mesh/service-fabric-mesh-overview.md)
