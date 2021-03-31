---
title: 'Program uppgradering: Dataserialisering'
description: Metod tips för Dataserialisering och hur det påverkar rullande program uppgraderingar.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 9474a0bf8041ac541389f782f60bf1220d690cde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575643"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Hur Dataserialisering påverkar en program uppgradering
I en [uppgradering av rullande program](service-fabric-application-upgrade.md)tillämpas uppgraderingen på en delmängd noder, en uppgraderings domän i taget. Under den här processen finns vissa uppgraderings domäner i den nyare versionen av programmet och vissa uppgraderings domäner finns på den äldre versionen av programmet. Under distributionen måste den nya versionen av programmet kunna läsa den gamla versionen av dina data och den gamla versionen av programmet måste kunna läsa den nya versionen av dina data. Om data formatet inte vidarebefordras och är bakåtkompatibelt, kan uppgraderingen Miss lyckas eller vara sämre, data kan gå förlorade eller skadas. Den här artikeln beskriver vad som utgör ditt data format och ger bästa möjliga praxis för att se till att dina data är vanliga och bakåtkompatibla.

## <a name="what-makes-up-your-data-format"></a>Vad utgör ditt data format?
I Azure Service Fabric kommer de data som sparas och replikeras från dina C#-klasser. För program som använder [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)är dessa data objekt i de tillförlitliga ord listorna och köerna. För program som använder [Reliable Actors](service-fabric-reliable-actors-introduction.md), det vill säga upp-läget för aktören. Dessa C#-klasser måste serialiseras för att vara bestående och replikeras. Därför definieras data formatet av de fält och egenskaper som är serialiserade, samt hur de serialiseras. I en data serie till exempel en serialiserad enhet `IReliableDictionary<int, MyClass>` `int` `MyClass` .

### <a name="code-changes-that-result-in-a-data-format-change"></a>Kod ändringar som resulterar i en ändring av data format
Eftersom data formatet bestäms av C#-klasser kan ändringar i klasser orsaka ett data format. Det måste vara viktigt att se till att en rullande uppgradering kan hantera data formats ändringen. Exempel som kan orsaka data format ändringar:

* Lägga till eller ta bort fält eller egenskaper
* Byta namn på fält eller egenskaper
* Ändra typer av fält eller egenskaper
* Ändra klass namnet eller namn området

### <a name="data-contract-as-the-default-serializer"></a>Data kontrakt som standard serialiserare
Serialiseraren ansvarar generellt för att läsa data och avserialisera den till den aktuella versionen, även om data finns i en äldre eller *nyare* version. Standard serialiseraren är [data kontrakts serialiseringen](/dotnet/framework/wcf/feature-details/using-data-contracts), som har väldefinierade versions regler. Tillförlitliga samlingar tillåter att serialiseraren åsidosätts, men Reliable Actors för närvarande inte. Data serialiseraren spelar en viktig roll när det är möjligt att aktivera löpande uppgraderingar. DataContract-serialiseringen är den seriella som vi rekommenderar för Service Fabric program.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Hur data formatet påverkar en löpande uppgradering
Under en rullande uppgradering finns det två huvud scenarier där serialiseraren kan stöta på en äldre eller *nyare* version av dina data:

1. När en nod har uppgraderats och börjar säkerhets kopie ras, kommer den nya serialiseraren att läsa in de data som sparades på disk av den gamla versionen.
2. Under den löpande uppgraderingen kommer klustret att innehålla en blandning av den gamla och nya versionen av koden. Eftersom repliker kan placeras i olika uppgraderings domäner och repliker skickar data till varandra kan den nya och/eller gamla versionen av dina data påträffas av den nya och/eller tidigare versionen av din serialiserare.

> [!NOTE]
> Den "nya versionen" och "gamla versionen" refererar till den version av koden som körs. "Ny serialiserare" syftar på den seriella kod som körs i den nya versionen av programmet. "Nya data" syftar på den serialiserade C#-klassen från den nya versionen av programmet.
> 
> 

De två versionerna av kod och data format måste både vara framåtriktade och bakåtkompatibla. Om de inte är kompatibla kan den rullande uppgraderingen Miss lyckas eller så kan data gå förlorade. Den rullande uppgraderingen kan Miss lyckas eftersom koden eller serialiseraren kan utlösa undantag eller ett fel när den påträffar den andra versionen. Data kan gå förlorade om du till exempel har lagt till en ny egenskap men den gamla serialiseraren ignorerar den under deserialiseringen.

Data kontrakt är den rekommenderade lösningen för att säkerställa att dina data är kompatibla. Den har väldefinierade versions regler för att lägga till, ta bort och ändra fält. Den har också stöd för att hantera okända fält, ansluta till serialiserings-och avserialiserings processen och hantera klass arv. Mer information finns i [använda data kontrakt](/dotnet/framework/wcf/feature-details/using-data-contracts).

## <a name="next-steps"></a>Nästa steg
Genom [att uppgradera programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md) går du igenom en program uppgradering med Visual Studio.

Genom [att uppgradera ditt program med hjälp av PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) får du en program uppgradering med PowerShell.

Styr hur programmet uppgraderas med hjälp av [uppgraderings parametrar](service-fabric-application-upgrade-parameters.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att titta på [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i program uppgraderingar genom att följa stegen i [Felsöka program uppgraderingar](service-fabric-application-upgrade-troubleshooting.md).
