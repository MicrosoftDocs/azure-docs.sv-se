---
title: Översikt över Service Fabric-programmeringsmodell
description: 'Service Fabric erbjuder två ramverk för att skapa tjänster: aktörs ramverket och tjänst ramverket. De erbjuder distinkta kompromisser i enkelhet och kontroll.'
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 523c9e0064d8b78698f1a0dd3544bda58436800c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575728"
---
# <a name="service-fabric-programming-model-overview"></a>Översikt över Service Fabric-programmeringsmodell

Service Fabric erbjuder flera olika sätt att skriva och hantera dina tjänster. Tjänster kan välja att använda Service Fabric-API: er för att dra full nytta av plattformens funktioner och program ramverk. Tjänster kan också vara alla kompilerade körbara program skrivna på valfritt språk eller kod som körs i en behållare som finns på ett Service Fabric-kluster.

## <a name="guest-executables"></a>Körbara gästfiler

En [körbar gäst](service-fabric-guest-executables-introduction.md) är en befintlig, godtycklig körbar fil (som skrivs på valfritt språk) som kan köras som en tjänst i ditt program. Körbara gäst program anropar inte Service Fabric SDK-API: er direkt. De har dock fortfarande nytta av de funktioner som plattformen erbjuder, till exempel tjänstens identifierings möjlighet, anpassad hälsa och belastnings rapportering genom att anropa REST API: er som exponeras av Service Fabric. De har också fullständig support för program livs cykeln.

Kom igång med körbara gäst program genom att distribuera ditt första [gäst körbara program](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Containers

Som standard distribuerar Service Fabric och aktiverar tjänster som processer. Service Fabric kan även distribuera tjänster i [behållare](service-fabric-containers-overview.md). Service Fabric stöder distribution av Linux-behållare och Windows-behållare på Windows Server 2016 och senare. Behållar avbildningar kan hämtas från alla behållar lagrings platser och distribueras till datorn. Du kan distribuera befintliga program som körbara gäst program, Service Fabric tillstånds lösa eller tillstånds känsliga Reliable Services eller Reliable Actors i behållare, och du kan blanda tjänster i processer och tjänster i behållare i samma program.

[Lär dig mer om att behålla dina tjänster i Windows eller Linux](./service-fabric-get-started-containers.md)

## <a name="reliable-services"></a>Reliable Services

Reliable Services är ett lätt vikt ramverk för att skriva tjänster som integreras med Service Fabric plattform och som drar nytta av den fullständiga uppsättningen plattforms funktioner. Reliable Services tillhandahålla en minimal uppsättning API: er som gör det möjligt för Service Fabric runtime att hantera livs cykeln för dina tjänster och som gör att dina tjänster kan interagera med körningen. Application Framework är minimalt, vilket ger dig fullständig kontroll över design-och implementerings alternativ och kan användas som värd för alla andra program ramverk, till exempel ASP.NET Core.

Reliable Services kan vara tillstånds lösa, liknande de flesta plattformar, t. ex. webb servrar, där varje instans av tjänsten skapas lika och tillstånd sparas i en extern lösning, till exempel Azure DB eller Azure Table Storage.

Exklusivt att Service Fabric kan Reliable Services också vara tillstånds känslig, där tillstånd är sparat direkt i själva tjänsten med hjälp av pålitliga samlingar. Tillstånd görs med hög tillgänglighet via replikering och distribueras genom partitionering, allt hanteras automatiskt av Service Fabric.

[Läs mer om Reliable Services](service-fabric-reliable-services-introduction.md) eller kom igång genom [att skriva din första pålitliga tjänst](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core är en plattforms oberoende ramverk med öppen källkod för att bygga moderna, molnbaserade Internet-baserade program, till exempel webbappar, IoT-appar och mobila Server delar. Service Fabric integreras med ASP.NET Core så att du kan skriva både tillstånds lösa och tillstånds känsliga ASP.NET Core program som utnyttjar pålitliga samlingar och Service Fabric avancerade Orchestration-funktioner.

[Läs mer om ASP.net core i Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) eller kom igång genom [att skriva ditt första ASP.net Core Service Fabric program](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors

Det pålitliga aktörs ramverket bygger på Reliable Services och är ett program ramverk som implementerar mönstret för [virtuell aktör](https://research.microsoft.com/en-us/projects/orleans/) , baserat på den beräknings [aktörs modell](https://en.wikipedia.org/wiki/Actor_model). I det pålitliga aktörs ramverket används oberoende beräknings enheter med en enkel tråd körning som kallas *aktörer*. Det pålitliga aktörs ramverket innehåller inbyggd kommunikation för aktörer och förkonfigurerade tillstånds beständiga och skalbara konfigurationer.

Eftersom Reliable Actors är ett program ramverk som bygger på Reliable Services, är det helt integrerat med Service Fabric-plattformen och fördelarna med den fullständiga uppsättningen funktioner som erbjuds av plattformen.

[Läs mer om Reliable Actors](service-fabric-reliable-actors-introduction.md) eller kom igång genom [att skriva din första pålitliga aktörs tjänst](service-fabric-reliable-actors-get-started.md)

[Bygg en klient dels tjänst med ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Nästa steg

[Översikt över Service Fabric och behållare](service-fabric-containers-overview.md)

[Översikt över Reliable Services](service-fabric-reliable-services-introduction.md)

[Översikt över Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Service Fabric och ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
