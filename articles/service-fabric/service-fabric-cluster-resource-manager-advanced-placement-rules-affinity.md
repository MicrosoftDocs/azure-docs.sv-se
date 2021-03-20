---
title: Service Fabric Cluster Resource Manager-tillhörighet
description: Översikt över tjänst tillhörighet för Azure Service Fabric Services och vägledning för konfiguration av tjänst tillhörighet.
services: service-fabric
documentationcenter: .net
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c141cd96877fd140b858d0aaed9197f2de80eca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89005742"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurera och använda tjänst tillhörighet i Service Fabric
Tillhörighet är en kontroll som främst tillhandahålls för att under lätta över gången av större monolitisk-program till molnet och mikrotjänsterna. Den används också som en optimering för att förbättra prestandan för tjänster, även om detta kan ha sido effekter.

Anta att du skapar en större app eller en som precis inte har utformats med mikrotjänster i åtanke, för att Service Fabric (eller någon annan distribuerad miljö). Den här typen av över gång är vanlig. Du börjar genom att lyfta hela appen till miljön, paketera den och se till att den körs smidigt. Sedan börjar du dela upp den i olika mindre tjänster som alla pratar med varandra.

Slutligen kanske du upptäcker att programmet har problem. Problemen infaller vanligt vis i en av följande kategorier:

1. En del komponent X i monolitisk-appen hade ett avdokumenterat beroende av komponenten Y och du precis aktiverade komponenterna i separata tjänster. Eftersom dessa tjänster nu körs på olika noder i klustret är de brutna.
2. Dessa komponenter kommunicerar via (lokala namngivna pipes | delade minne | filer på disk) och de måste verkligen kunna skriva till en delad lokal resurs av prestanda skäl just nu. Det hårda beroendet tas bort senare, kanske.
3. Allt är bra, men det visar att dessa två komponenter faktiskt är av typen samtals känsliga/prestanda känsliga. När de flyttade dem till separata tjänster, ökar den totala program prestandan eller svars tiden. Detta innebär att det övergripande programmet inte uppfyller förväntningarna.

I dessa fall vill vi inte förlora vårt omstrukturerings arbete och vill inte gå tillbaka till monolit. Det senaste villkoret kan till och med vara önskvärt som en vanlig optimering. Men för att vi ska kunna utforma om komponenterna för att arbeta naturligt som tjänster (eller tills vi kan lösa prestanda förväntningarna på något annat sätt) behöver vi en viss uppfattning om plats.

Vad bör jag göra? Du kan också försöka att aktivera tillhörighet.

## <a name="how-to-configure-affinity"></a>Konfigurera tillhörighet
Om du vill konfigurera tillhörighet definierar du en tillhörighets relation mellan två olika tjänster. Du kan tänka på tillhörighet som "peka" en tjänst på en annan och säga att den här tjänsten bara kan köras där tjänsten körs. " Ibland hänvisar vi till tillhörighet som en överordnad/underordnad relation (där du pekar på det underordnade objektet). Tillhörighet garanterar att repliker eller instanser av en tjänst placeras på samma noder som en annan tjänst.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> En underordnad tjänst kan bara delta i en enskild tillhörighets relation. Om du vill att det underordnade ska vara tillhör till två överordnade tjänster samtidigt kan du välja:
> - Ändra relationerna (har parentService1 och parentService2 punkt i den aktuella underordnade tjänsten) eller
> - Ange en av de överordnade föräldrarna som hubb av en konvention och har alla tjänst platser i den tjänsten. 
>
> Det resulterande placerings beteendet i klustret ska vara detsamma.
>

## <a name="different-affinity-options"></a>Olika tillhörighets alternativ
Tillhörigheten visas via ett av flera korrelations scheman och har två olika lägen. Det vanligaste tillhörighets läget är vad vi kallar NonAlignedAffinity. I NonAlignedAffinity placeras replikerna eller instanserna av de olika tjänsterna på samma noder. Det andra läget är AlignedAffinity. Anpassad tillhörighet är bara användbar med tillstånds känsliga tjänster. Att konfigurera två tillstånds känsliga tjänster med en anpassad tillhörighet säkerställer att presidentval för dessa tjänster placeras på samma noder som varandra. Det gör också att varje par med sekundär nyckel för tjänsterna placeras på samma noder. Det är också möjligt (men mindre vanligt) att konfigurera NonAlignedAffinity för tillstånds känsliga tjänster. För NonAlignedAffinity skulle de olika replikerna av de två tillstånds känsliga tjänsterna köras på samma noder, men deras presidentval kan sluta på olika noder.

<center>

![Tillhörighets lägen och deras effekter][Image1]
</center>

### <a name="best-effort-desired-state"></a>Önskat tillstånd för bästa ansträngning
En tillhörighets relation är bästa prestanda. Det ger inte samma garantier för samplacering eller tillförlitlighet som körs i samma körbara process. Tjänsterna i en tillhörighets relation är fundamentalt olika entiteter som kan gå sönder och flyttas oberoende av varandra. En tillhörighets relation kan också brytas, även om dessa avbrott är tillfälliga. Kapacitets begränsningar kan till exempel innebära att bara vissa av tjänst objekten i tillhörighets relationen får plats på en viss nod. I dessa fall, även om det finns en tillhörighets relation på plats, kan den inte tillämpas på grund av de andra begränsningarna. Om det är möjligt korrigeras överträdelsen automatiskt senare.

### <a name="chains-vs-stars"></a>Kedjor eller stjärnor
I dag kan inte kluster resurs hanteraren modellera kedjor av tillhörighets relationer. Det innebär att en tjänst som är underordnad en tillhörighets relation inte får vara överordnad i en annan tillhörighets relation. Om du vill modellera den här typen av relation måste du effektivt modellera den som en stjärna i stället för en kedja. Om du vill flytta från en kedja till en stjärna är den understa underordnade överordnad till det första underordnade objekt i stället. Beroende på tjänsternas placering kan du behöva göra detta flera gånger. Om det inte finns någon fysisk överordnad tjänst kan du behöva skapa en som fungerar som plats hållare. Beroende på dina krav kanske du också vill titta på [program grupper](service-fabric-cluster-resource-manager-application-groups.md).

<center>

![Kedjor jämfört med stjärnor i kontexten för tillhörighets relationer][Image2]
</center>

En annan sak att notera om tillhörighets relationer idag är att de är riktade mot standard. Det innebär att tillhörighets regeln endast tvingar fram att den underordnade placeras med överordnad. Det garanterar inte att den överordnade är placerad med det underordnade objektet. Därför, om det uppstår en tillhörighets överträdelse och korrigera överträdelsen av någon anledning, är det inte möjligt att flytta det underordnade objektet till den överordnade noden, och även om den överordnade noden till den underordnade noden har korrigerat överträdelsen, kommer den överordnade inte att flyttas till den underordnade noden. Om du ställer in config- [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) på True tas riktningen bort. Det är också viktigt att Observera att tillhörighets relationen inte kan vara perfekt eller tillämpas direkt eftersom olika tjänster har olika livscykler och kan gå sönder och flyttas oberoende av varandra. Anta till exempel att den överordnade plötsligt växlar över till en annan nod eftersom den kraschade. Kluster resurs hanteraren och Redundanshanteraren hanterar redundansväxlingen först, eftersom tjänsterna är konsekventa och tillgängliga är prioriteten. När redundansväxlingen är klar bryts tillhörighets relationen, men kluster resurs hanteraren tycker att allt är bra tills det ser att det underordnade inte är placerat i den överordnade. Dessa sorters kontroller utförs regelbundet. Mer information om hur kluster resurs hanteraren utvärderar begränsningar finns i [den här artikeln](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), och [den här](service-fabric-cluster-resource-manager-balancing.md) informationen pratar mer om hur du konfigurerar takt som de här begränsningarna utvärderas på.   


### <a name="partitioning-support"></a>Partitionerings stöd
Det slutliga meddelandet om tillhörighet är att tillhörighets relationer inte stöds där den överordnade partitionen är partitionerad. Partitionerade överordnade tjänster kan komma att stödjas på ett eventuellt sätt, men i de flesta fall är de inte tillåtna.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster finns i [så här konfigurerar du tjänster](service-fabric-cluster-resource-manager-configure-services.md)
- Om du vill begränsa tjänster till en liten uppsättning datorer eller aggregera tjänsternas belastning använder du [program grupper](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png