---
title: Kluster resurs hanterare – hanterings integrering
description: En översikt över integrerings platserna mellan kluster resurs hanteraren och Service Fabric hantering.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ae80ac5833e90164fc4ff92010fd1830ae932cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92174039"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Kluster resurs hanteraren-integrering med Service Fabric kluster hantering
Service Fabric Cluster Resource Manager Driver inte uppgraderingar i Service Fabric, men det ingår. Det första sättet som kluster resurs hanteraren hjälper till med hantering är att spåra det önskade läget för klustret och tjänsterna i det. Kluster resurs hanteraren skickar ut hälso rapporter när det inte går att placera klustret i önskad konfiguration. Om det till exempel finns otillräcklig kapacitet i kluster resurs hanteraren skickas hälso varningar och fel som indikerar problemet. En annan integrerings komponent måste göra med hur uppgraderingar fungerar. Kluster resurs hanteraren ändrar sitt beteende något under uppgraderingar.  

## <a name="health-integration"></a>Hälso integrering
Kluster resurs hanteraren spårar kontinuerligt de regler som du har definierat för att placera dina tjänster. Det spårar också den återstående kapaciteten för varje mått på noderna och i klustret och i klustret som helhet. Om den inte kan uppfylla reglerna eller om det inte finns tillräckligt med kapacitet genereras hälso varningar och fel. Om en nod till exempel har överkapacitet och kluster resurs hanteraren kommer att försöka åtgärda problemet genom att flytta tjänster. Om det inte går att korrigera situationen uppstår en hälso varning som anger vilken nod som överskrider kapaciteten och för vilka mått.

Ett annat exempel på resurs hanterarens hälso varningar är överträdelser av placerings begränsningar. Om du till exempel har definierat en placerings begränsning (till exempel `“NodeColor == Blue”` ) och resurs hanteraren identifierar en överträdelse av denna begränsning, genererar den en hälso varning. Detta gäller för anpassade begränsningar och standard begränsningar (t. ex. fel domän och uppgradering av domän begränsningar).

Här är ett exempel på en sådan hälso rapport. I det här fallet är hälso rapporten för en av system tjänstens partitioner. Hälso meddelandet anger att replikerna i partitionen är tillfälligt förpackade i för få uppgraderings domäner.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Det här hälso meddelandet talar om för oss att:

1. Alla själva replikerna är felfria: var och en har `AggregatedHealthState : Ok`
2. Distributions begränsningen för uppgraderings domänen håller på att överskridas. Det innebär att en viss uppgraderings domän har fler repliker från den här partitionen än den borde.
3. Vilken nod innehåller den replik som orsakar överträdelsen. I det här fallet är det noden med name- *noden. 8*
4. Om en uppgradering för närvarande sker för den här partitionen ("uppgraderas för närvarande--false")
5. Distributions principen för den här tjänsten: "distributions princip--packning". Detta styrs av `RequireDomainDistribution` [placerings principen](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). *Packning* indikerar att i det här fallet DomainDistribution _inte_ var obligatoriskt, så vi vet att ingen placerings princip har angetts för den här tjänsten. 
6. När rapporten skedde – 8/10/2015 7:13:02 PM

Information som detta ger aviseringar som utlöses i produktion så att du kan se att något har gått fel och även används för att identifiera och stoppa Felaktiga uppgraderingar. I det här fallet skulle vi vilja se om vi kan ta reda på varför Resource Manager tvungen att packa replikerna i uppgraderings domänen. Normalt är packningen tillfällig eftersom noderna i de andra uppgraderings domänerna var inaktiverade, till exempel.

Anta att kluster resurs hanteraren försöker placera vissa tjänster, men att det inte finns några lösningar som fungerar. När det inte går att placera tjänster är det vanligt vis någon av följande orsaker:

1. Vissa tillfälliga villkor har gjort det omöjligt att placera tjänst instansen eller repliken korrekt
2. Tjänstens placerings krav uppfyller kraven.

I dessa fall kan hälso rapporter från kluster resurs hanteraren hjälpa dig att avgöra varför det inte går att placera tjänsten. Vi kallar den här processen för villkors Eli minerings ordningen. Under den guidar systemet igenom de konfigurerade begränsningarna som påverkar tjänsten och registrerar vad de eliminerar. På så sätt kan du se vilka noder som har eliminerats och varför det inte går att placera några tjänster.

## <a name="constraint-types"></a>Villkors typer
Låt oss prata om var och en av de olika begränsningarna i dessa hälso rapporter. Du ser hälso meddelanden som är relaterade till dessa begränsningar när det inte går att placera repliker.

* **ReplicaExclusionStatic** och **ReplicaExclusionDynamic**: dessa begränsningar indikerar att en lösning avvisades eftersom två tjänst objekt från samma partition skulle behöva placeras på samma nod. Detta är inte tillåtet eftersom sedan felet i noden skulle påverka den partitionen. ReplicaExclusionStatic och ReplicaExclusionDynamic är nästan samma regel och skillnaderna spelar ingen roll. Om du ser en begränsnings Eli minerings sekvens som innehåller antingen ReplicaExclusionStatic-eller ReplicaExclusionDynamic-begränsningen, anser kluster resurs hanteraren att det inte finns tillräckligt med noder. Detta kräver att återstående lösningar använder dessa ogiltiga platser, vilket inte är tillåtet. De andra begränsningarna i sekvensen meddelar vanligt vis oss varför noder elimineras på den första platsen.
* **PlacementConstraint**: om du ser det här meddelandet innebär det att vi har eliminerat vissa noder eftersom de inte stämde överens med tjänstens placerings begränsningar. Vi går igenom de nuvarande konfigurerade placerings begränsningarna som en del av det här meddelandet. Detta är normalt om du har definierat en placerings begränsning. Men om placerings begränsningen gör att det inte finns tillräckligt många noder som kan elimineras så här.
* **NodeCapacity**: den här begränsningen innebär att kluster resurs hanteraren inte kunde placera replikerna på de angivna noderna, eftersom det skulle placera dem över kapacitet.
* **Tillhörighet**: den här begränsningen anger att det inte gick att placera repliken på de berörda noderna eftersom den skulle orsaka en överträdelse av tillhörighets begränsningen. Mer information om tillhörighet finns i [den här artikeln](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **Faulydomain** och **UpgradeDomain**: den här begränsningen eliminerar noder om du placerar repliken på de angivna noderna skulle leda till packning i ett visst fel eller en uppgraderings domän. Flera exempel som diskuterar den här begränsningen presenteras i avsnittet om [fel-och uppgraderings domän begränsningar och resulterande beteende](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: du bör normalt inte se den här begränsningen genom att ta bort noder från lösningen eftersom den körs som en optimering som standard. Den prioriterade plats begränsningen finns också vid uppgraderingar. Under uppgraderingen används den för att flytta tjänster tillbaka till den plats där uppgraderingen startade.

## <a name="blocklisting-nodes"></a>Blocklisting-noder
Ett annat hälso meddelande är att kluster resurs hanterarens rapporter är när noderna är blocklisted. Du kan betrakta Blocklisting som en tillfällig begränsning som automatiskt tillämpas för dig. Noder får blocklisted när de stöter på upprepade problem när instanser av tjänst typen startas. Noderna är blocklisted per tjänst-typ. En nod kan vara blocklisted för en tjänst typ, men inte en annan. 

Du kommer att se hur Blocklisting används ofta under utvecklingen: vissa fel leder till att tjänstens värd kraschar vid start, Service Fabric försöker skapa en tjänst värd några gånger och felet kvarstår. Efter några försök får noden blocklisted och kluster resurs hanteraren kommer att försöka skapa tjänsten någon annan stans. Om det här problemet kvarstår på flera noder, är det möjligt att alla giltiga noder i klustret är blockerade. Blocklisting kan också ta bort så många noder som inte räcker för att kunna starta tjänsten för att uppfylla den önskade skalningen. Du kan vanligt vis se ytterligare fel eller varningar från kluster resurs hanteraren som anger att tjänsten är lägre än den önskade repliken eller antalet instanser, samt hälso meddelanden som anger vad felet är och som leder till Blocklisting på den första platsen.

Blocklisting är inte ett permanent villkor. Efter några minuter tas noden bort från blockeringslistan och Service Fabric kan aktivera tjänsterna på den noden igen. Om tjänsterna fortsätter att fungera, är noden blocklisted för den tjänst typen igen. 

### <a name="constraint-priorities"></a>Begränsnings prioritet

> [!WARNING]
> Ändring av begränsnings prioriteter rekommenderas inte och kan ha betydande negativa effekter i klustret. Informationen nedan anges som referens för standard begränsnings prioriteterna och deras beteende. 
>

Med alla dessa begränsningar kan du ha funderat på "Hej – Jag tror att fel domän begränsningar är de viktigaste i systemet. För att se till att fel domän begränsningen inte bryter mot varandra är jag villig att kränka andra begränsningar. "

Begränsningar kan konfigureras med olika prioritets nivåer. Dessa är:

   - "hård" (0)
   - "mjuk" (1)
   - "optimering" (2)
   - "av" (-1). 
   
De flesta begränsningarna är konfigurerade som hårda begränsningar som standard.

Det är ovanligt att ändra prioriteten för begränsningar. Det har varit tillfällen då begränsnings prioriteter behövde ändras, vanligt vis för att undvika andra fel eller beteenden som påverkar miljön. I allmänhet fungerar flexibiliteten i infrastrukturen för begränsnings prioritet mycket bra, men det behövs inte ofta. Det mesta av tiden som allt har varit på sina standard prioriteringar. 

Prioritets nivåerna innebär inte att en specifik begränsning _kommer_ att överskridas och att det alltid kommer att uppfyllas. Begränsnings prioriteter definierar i vilken ordning begränsningar tillämpas. Prioriteter definierar kompromisserna när det är omöjligt att uppfylla alla begränsningar. Vanligt vis kan alla begränsningar vara uppfyllda om det inte finns något annat som händer i miljön. Några exempel på scenarier som leder till begränsnings överträdelser är motstridiga begränsningar, eller ett stort antal samtidiga fel.

I avancerade situationer kan du ändra begränsnings prioriteterna. Anta till exempel att du ville se till att tillhörighet alltid skulle kränkas när det behövs för att lösa problem med nodens kapacitet. För att åstadkomma detta kan du ange prioriteten för tillhörighets begränsningen till "mjuk" (1) och lämna kapacitets begränsningen inställd på "hård" (0).

Standard prioritets värden för de olika begränsningarna anges i följande konfiguration:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

via ClusterConfig.jspå för fristående distributioner eller Template.jspå för Azure-värdbaserade kluster:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Domän begränsningar för fel domän och uppgradering
Kluster resurs hanteraren vill hålla tjänsterna spridda mellan fel-och uppgraderings domäner. Den modellerar detta som en begränsning i kluster resurs hanterarens motor. Om du vill ha mer information om hur de används och deras speciella beteende kan du läsa artikeln om [kluster konfiguration](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Kluster resurs hanteraren kan behöva paketera ett par repliker i en uppgraderings domän för att hantera uppgraderingar, fel eller andra begränsnings fel. Packning i fel-eller uppgraderings domäner sker vanligt vis bara när det finns flera fel eller andra omsättnings bara i systemet som förhindrar korrekt placering. Om du vill förhindra packning även under dessa situationer kan du använda `RequireDomainDistribution` [placerings principen](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Observera att detta kan påverka tjänstens tillgänglighet och tillförlitlighet som en sido effekt, så tänk på det noggrant.

Om miljön har kon figurer ATS korrekt respekteras alla begränsningar fullständigt, även vid uppgraderingar. Nyckeln är att kluster resurs hanteraren ser ut för dina begränsningar. När ett brott upptäcks rapporterar det omedelbart och försöker åtgärda problemet.

## <a name="the-preferred-location-constraint"></a>Prioriterad plats begränsning
PreferredLocation-begränsningen är lite annorlunda, eftersom den har två olika användnings områden. En användning av den här begränsningen är vid program uppgraderingar. Kluster resurs hanteraren hanterar den här begränsningen automatiskt under uppgraderingar. Den används för att se till att när uppgraderingar är slutförda kommer replikerna att återgå till sina ursprungliga platser. Den andra användningen av PreferredLocation-begränsningen är för [ `PreferredPrimaryDomain` placerings principen](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Båda dessa är optimeringar, och därför är PreferredLocation-begränsningen den enda begränsningen inställd på "optimering" som standard.

## <a name="upgrades"></a>Uppgraderingen
Kluster resurs hanteraren hjälper även till vid program-och kluster uppgraderingar, under vilka den har två jobb:

* kontrol lera att klustrets regler inte har komprometterats
* försök att hjälpa uppgraderingen smidigt

### <a name="keep-enforcing-the-rules"></a>Fortsätt att verkställa reglerna
Det viktigaste att vara medveten om är att reglerna – strikta begränsningar som placerings begränsningar och kapaciteter – fortfarande tillämpas under uppgraderingar. Placerings begränsningar säkerställer att arbets belastningarna bara körs där de är tillåtna, även under uppgraderingar. När tjänsterna är mycket begränsade kan uppgraderingar ta längre tid. När tjänsten eller dess nod tas ned för en uppdatering kan det finnas några alternativ för var den kan gå.

### <a name="smart-replacements"></a>Smarta ersättningar
När en uppgradering startar tar Resource Manager en ögonblicks bild av klustrets aktuella ordning. När varje uppgraderings domän har slutförts försöker den returnera de tjänster som fanns i den uppgraderings domänen till sin ursprungliga ordning. På så sätt finns det högst två över gångar för en tjänst under uppgraderingen. Det finns en flytt av den berörda noden och en flytt tillbaka i. Att returnera klustret eller tjänsten till hur det var innan uppgraderingen ser till att uppgraderingen inte påverkar klustrets layout. 

### <a name="reduced-churn"></a>Reducerad omsättning
En annan sak som inträffar under uppgraderingar är att kluster resurs hanteraren stänger av balanseringen. Att förhindra balansering förhindrar onödiga reaktioner på uppgraderingen, som att flytta tjänster till noder som har tömts för uppgraderingen. Om uppgraderingen i fråga är en kluster uppgradering, bal anse ras inte hela klustret under uppgraderingen. Begränsnings kontrollerna fortsätter att vara aktiva, endast rörelse baserat på den proaktiva utjämningen av mått är inaktive rad.

### <a name="buffered-capacity--upgrade"></a>Uppgradering av buffrad kapacitet &
Vanligt vis vill du att uppgraderingen ska slutföras även om klustret är begränsat eller nästan är fullt. Att hantera klustrets kapacitet är ännu mer viktigt under uppgraderingar än vanligt. Beroende på antalet uppgraderings domäner måste mellan 5 och 20 procent av kapaciteten migreras när uppgraderingen rullar genom klustret. Detta arbete måste gå någonstans. Det är här som begreppet [buffrad kapacitet](service-fabric-cluster-resource-manager-cluster-description.md#node-buffer-and-overbooking-capacity) är användbart. Den buffrade kapaciteten respekteras under normal drift. Kluster resurs hanteraren kan fylla noderna upp till sin totala kapacitet (med bufferten) under uppgraderingarna om det behövs.

## <a name="next-steps"></a>Nästa steg
* Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
