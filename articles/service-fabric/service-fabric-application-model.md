---
title: Program modell för Azure Service Fabric
description: Hur du modellerar och beskriver program och tjänster i Azure Service Fabric med hjälp av MANIFEST filer för program och tjänster.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 84e6b2309fdb206771d4ea01aa03c7f355d6ff19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85963741"
---
# <a name="model-an-application-in-service-fabric"></a>Modellera ett program i Service Fabric
Den här artikeln innehåller en översikt över Azure Service Fabric program modellen och hur du definierar ett program och en tjänst via MANIFEST filer.

## <a name="understand-the-application-model"></a>Förstå program modellen
Ett program är en samling komponent tjänster som utför en viss funktion eller funktion. En tjänst utför en fullständig och fristående funktion och kan starta och köras oberoende av andra tjänster.  En tjänst består av kod, konfiguration och data. För varje tjänst består kod av binärfilerna för körbara filer, konfigurationen består av tjänst inställningar som kan läsas in vid körning och data består av valfria statiska data som ska användas av tjänsten. Varje komponent i den här hierarkiska program modellen kan vara versioner och uppgraderas separat.

![Service Fabric program modell][appmodel-diagram]

En program typ är en kategorisering av ett program och består av ett paket med tjänst typer. En tjänst typ är en kategorisering av en tjänst. Kategoriseringen kan ha olika inställningar och konfigurationer, men huvud funktionerna förblir desamma. Instanserna av en tjänst är de olika skillnaderna för tjänst konfiguration av samma tjänst typ.  

Klasser (eller "typer") av program och tjänster beskrivs via XML-filer (applikations manifest och tjänst manifest).  Manifesten beskriver program och tjänster och är de mallar som program kan instansieras från klustrets avbildnings arkiv.  Manifest beskrivs i detalj i [program-och tjänst manifest](service-fabric-application-and-service-manifests.md). Schema definitionen för ServiceManifest.xml-och ApplicationManifest.xml-filen installeras med Service Fabric SDK och verktyg för *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. XML-schemat dokumenteras i [dokumentationen för ServiceFabricServiceModel. XSD-schemat](service-fabric-service-model-schema.md).

Koden för olika program instanser körs som separata processer, även om den finns på samma Service Fabric-nod. Dessutom kan livs cykeln för varje program instans hanteras (till exempel uppgraderas) oberoende av varandra. Följande diagram visar hur program typer består av tjänst typer, vilket i sin tur består av kod, konfiguration och data paket. För att förenkla diagrammet visas endast kod/config/data-paket för `ServiceType4` , även om varje tjänst typ innehåller några eller alla paket typer.

![Service Fabric program typer och tjänst typer][cluster-imagestore-apptypes]

Det kan finnas en eller flera instanser av en aktiv tjänst typ i klustret. Till exempel tillstånds känsliga tjänst instanser eller repliker, uppnå hög tillförlitlighet genom att replikera status mellan repliker som finns på olika noder i klustret. Replikering tillhandahåller i princip redundans för att tjänsten ska vara tillgänglig även om en nod i ett kluster Miss lyckas. En [partitionerad tjänst](service-fabric-concepts-partitioning.md) delar ytterligare sitt tillstånd (och åtkomst mönster till detta tillstånd) över noder i klustret.

I följande diagram visas relationen mellan program och tjänst instanser, partitioner och repliker.

![Partitioner och repliker inom en tjänst][cluster-application-instances]

> [!TIP]
> Du kan visa layouten för program i ett kluster med hjälp av verktyget Service Fabric Explorer som finns på http:// &lt; yourclusteraddress &gt; : 19080/Explorer. Mer information finns i [visualisera klustret med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [program skalbarhet](service-fabric-concepts-scalability.md).
- Läs om tjänst [tillstånd](service-fabric-concepts-state.md), [partitionering](service-fabric-concepts-partitioning.md)och [tillgänglighet](service-fabric-availability-services.md).
- Läs om hur program och tjänster definieras i [program-och tjänst manifest](service-fabric-application-and-service-manifests.md).
- [Program värd modeller](service-fabric-hosting-model.md) beskriver förhållandet mellan repliker (eller instanser) av en distribuerad tjänst och en tjänst värd process.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


