---
title: Terminologi för Azure Service Fabric-nät
description: Den här artikeln beskriver den terminologi som används av Azure Service Fabric-nätet för att hjälpa dig att förstå de termer som används i dokumentationen.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 1fda83ec00bed0f0c67d45e1e1af61baa5de5e78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626764"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologi för Service Fabric nät

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk. Den här artikeln beskriver den terminologi som används av Azure Service Fabric-nätet för att hjälpa dig att förstå de termer som används i dokumentationen.

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](../service-fabric/index.yml) är en plattform för distribuerade system med öppen källkod som gör det enkelt att paketera, distribuera och hantera skalbara och pålitliga mikrotjänster. Service Fabric är den Orchestrator som ger Service Fabric nät. Service Fabric innehåller alternativ för hur du kan skapa och köra dina mikrotjänster-program. Du kan använda valfritt ramverk för att skriva dina tjänster och välja var du vill köra programmet från flera miljö val.

## <a name="application-and-service-concepts"></a>Program-och tjänst koncept

**Service Fabric nätprogram**: Service Fabric nätprogram beskrivs av [resurs modellen](./service-fabric-mesh-service-fabric-resources.md) (yaml och JSON-resursfiler) och kan distribueras till alla miljöer där Service Fabric körs.

**Service Fabric internt program**: Service Fabric inbyggda program beskrivs av den [interna program modellen](../service-fabric/service-fabric-application-model.md) (XML-baserade program-och tjänst manifest).  Service Fabric inbyggda program kan inte köras i Service Fabric nät.

**Program**: ett Service Fabric nätprogram är enhets distribution, versions hantering och livs längd för ett nät program. Livs cykeln för varje program instans kan hanteras separat.  Program består av ett eller flera service kod paket och inställningar. Ett program definieras med hjälp av Azure Resource Model-schemat (RM).  Tjänster beskrivs som egenskaper för program resursen i en RM-mall.  Nätverk och volymer som används av programmet refereras av programmet.  När du skapar ett program, modelleras program, tjänster, nätverk och volym (er) med hjälp av Service Fabric resurs modell.

**Tjänst**: en tjänst i ett program representerar en mikrotjänst och utför en fullständig och fristående funktion. Varje tjänst består av ett eller flera kod paket som beskriver allt som behövs för att köra behållar avbildningen som är associerad med kod paketet.  Antalet tjänst repliker i ett program kan skalas in och ut.

**Kod paket**: kod paket beskriver allt som behövs för att köra behållar avbildningen som är associerad med kod paketet, inklusive följande:

* Behållarens namn, version och register
* PROCESSOR-och minnes resurser som krävs för varje behållare
* Nätverks slut punkter
* Volymer som ska monteras i behållaren och som refererar till en separat volym resurs.

## <a name="deployment-and-application-models"></a>Distributions-och program modeller 

Om du vill distribuera dina tjänster måste du beskriva hur de ska köras. Service Fabric stöder tre olika distributions modeller:

### <a name="resource-model"></a>Resursmodell
Service Fabric resurser är allt som kan distribueras individuellt för att Service Fabric; inklusive program, tjänster, nätverk och volymer. Resurser definieras med hjälp av en JSON-fil som kan distribueras till en kluster slut punkt.  För Service Fabric nät används Azures resurs modell schema. Ett YAML-filschema kan också användas för att enklare utforma definitionsfiler. Resurser kan distribueras var som helst Service Fabric körs. Resurs modellen är det enklaste sättet att beskriva Service Fabric-program. Huvud fokus är vid enkel distribution och hantering av behållare tjänster. Läs mer i [Introduktion till Service Fabric resurs modell](./service-fabric-mesh-service-fabric-resources.md).

### <a name="native-model"></a>Inbyggd modell
Den inbyggda program modellen ger dina program fullständig åtkomst till Service Fabric. Program och tjänster definieras som registrerade typer i XML-MANIFEST filer.

Den inbyggda modellen stöder Reliable Services Framework, som ger åtkomst till API: erna för Service Fabric körning och kluster hantering i C# och Java. Den inbyggda modellen stöder också godtyckliga behållare och körbara filer.

Den inbyggda modellen stöds inte i Service Fabric nät miljön.  Mer information finns i [Översikt över programmerings modellen](../service-fabric/service-fabric-choose-framework.md).

### <a name="docker-compose"></a>Docker-sammanställning 
[Docker Compose](https://docs.docker.com/compose/) är en del av Docker-projektet. Service Fabric ger begränsat stöd för distribution av program med hjälp av Docker-modellen.

## <a name="environments"></a>Miljöer

Service Fabric är en plattforms teknik med öppen källkod som flera olika tjänster och produkter baseras på. Microsoft tillhandahåller följande alternativ:

 - **Service Fabric nät**: en fullständigt hanterad tjänst för att köra Service Fabric program i Microsoft Azure.
 - **Azure-Service Fabric**: det värd erbjudande som Azure hosted Service Fabric. Den ger integration mellan Service Fabric och Azure-infrastrukturen, tillsammans med uppgradering och konfigurations hantering av Service Fabric-kluster.
 - **Fristående Service Fabric**: en uppsättning installations-och konfigurations verktyg för att [distribuera Service Fabric kluster var som helst](../service-fabric/service-fabric-deploy-anywhere.md) (lokalt eller i valfri moln leverantör). Hanteras inte av Azure.
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

Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).

Få svar på [vanliga frågor](service-fabric-mesh-faq.md).
