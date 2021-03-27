---
title: Felsök med systemhälsorapporter
description: Beskriver de hälso rapporter som skickas av Azure Service Fabric-komponenter och deras användning för fel sökning av kluster eller program problem
ms.topic: conceptual
ms.date: 2/28/2018
ms.openlocfilehash: 483483746b2cce66588e9481bca7e0de391070b8
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625899"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Felsök med hjälp av systemhälsorapporter
Azure Service Fabric-komponenter tillhandahåller system hälso rapporter på alla entiteter i klustret direkt. [Hälso arkivet](service-fabric-health-introduction.md#health-store) skapar och tar bort entiteter baserat på system rapporter. Den organiserar också dem i en hierarki som fångar interaktionen mellan entiteter.

> [!NOTE]
> Läs mer i [Service Fabric hälso modell](service-fabric-health-introduction.md)för att förstå hälsorelaterade begrepp.
> 
> 

System hälso rapporter ger insyn i kluster-och program funktioner och flaggar problem. För program och tjänster kontrollerar system hälso rapporter att entiteter har implementerats och fungerar korrekt från Service Fabric perspektivet. Rapporterna ger ingen hälso övervakning av tjänstens affärs logik eller identifiering av processer som inte svarar. Användar tjänsterna kan förbättra hälso data med information som är speciell för deras logik.

> [!NOTE]
> Hälso rapporter som skickas av användar övervaknings enheter visas bara *när* system komponenterna har skapat en entitet. När en entitet tas bort tar hälso arkivet automatiskt bort alla hälso rapporter som är associerade med den. Detsamma gäller när en ny instans av entiteten skapas. Ett exempel är när en ny tillstånds känslig tjänst replik instans skapas. Alla rapporter som är associerade med den gamla instansen tas bort och rensas från Store.
> 
> 

System komponent rapporter identifieras av källan, som börjar med "**systemet".** . Övervaknings enheter kan inte använda samma prefix för sina källor, eftersom rapporter med ogiltiga parametrar avvisas.

Nu ska vi titta på några system rapporter för att förstå vad som utlöser dem och för att lära dig hur du korrigerar de potentiella problem som de representerar.

> [!NOTE]
> Service Fabric fortsätter att lägga till rapporter om villkor för intresse som ger bättre insyn i vad som händer i klustret och programmen. Befintliga rapporter kan förbättras med mer information som hjälper dig att felsöka problemet snabbare.
> 
> 

## <a name="cluster-system-health-reports"></a>Hälso rapporter för kluster system
Entiteten kluster hälsa skapas automatiskt i Health Store. Om allt fungerar som det ska visas ingen system rapport.

### <a name="neighborhood-loss"></a>Förlorat
**System. Federation** rapporterar ett fel när den identifierar en förlust. Rapporten är från enskilda noder och Node-ID: t ingår i egenskaps namnet. Om något går förlorat i hela Service Fabric ringen, kan du normalt förväntar dig två händelser som representerar båda sidorna i avstånds rapporten. Om fler platser går förlorade finns det fler händelser.

Rapporten anger den globala låne tids gränsen som TTL (Time-to-Live). Rapporten skickas varje halvår i TTL-tiden så länge villkoret förblir aktivt. Händelsen tas bort automatiskt när den upphör att gälla. Ta bort-när-utgångs beteende säkerställer att rapporten rensas från hälso lagret på rätt sätt, även om noden rapportering är nere.

* **SourceId**: system. Federation
* **Egenskap**: **börjar med och** innehåller Node-information.
* **Nästa steg**: ta reda på varför området förloras. Kontrol lera till exempel kommunikationen mellan klusternoder.

### <a name="rebuild"></a>Återskapa

Tjänsten Redundanshanteraren (FM) hanterar information om klusternoderna. När FM förlorar sina data och går över till data förlust, kan den inte garantera att den har den senaste uppdaterade informationen om klusternoderna. I det här fallet går systemet igenom en återuppbyggnad och System.FM samlar in data från alla noder i klustret för att återskapa sitt tillstånd. Ibland kan återskapas eller stoppas på grund av problem med nätverks-eller nodfel. Samma sak kan inträffa med Redundanshanteraren master-tjänsten (FMM). FMM är en tillstånds lös system tjänst som håller reda på var alla FMs finns i klustret. FMM primär är alltid noden med det ID som är närmast 0. Om den noden släpps utlöses en återuppbyggnad.
När ett av de föregående villkoren inträffar flaggar **system.fm** eller **system. FMM** den via en fel rapport. Återuppbyggnaden kan ha fastnat i en av två faser:

* **Väntar på sändning**: FM/FMM väntar på sändnings meddelandets svar från de andra noderna.

  * **Nästa steg**: Undersök om det är problem med nätverks anslutningen mellan noderna.
* **Väntar på noder**: FM/FMM har redan tagit emot ett broadcast-svar från de andra noderna och väntar på ett svar från vissa noder. Hälso rapporten listar de noder för vilka FM/FMM väntar på ett svar.
   * **Nästa steg**: Undersök nätverks anslutningen mellan FM/FMM och noderna i listan. Undersök varje listad nod för andra möjliga problem.

* **SourceId**: system.fm eller system. FMM
* **Egenskap**: återskapa.
* **Nästa steg**: Undersök nätverks anslutningen mellan noderna samt tillståndet för eventuella noder som visas i beskrivningen av hälso rapporten.

### <a name="seed-node-status"></a>Status för Dirigerings nod
**System.fm** rapporterar en varning på kluster nivå om vissa Seed-noder inte är felfria. Startnoder är de noder som upprätthåller tillgängligheten för det underliggande klustret. Dessa noder bidrar till att säkerställa att klustret förblir igång genom att upprätta lån med andra noder och betjäna som tiebreakers under vissa typer av nätverks fel. Om en majoritet av startnoderna är nere i klustret och de inte tas tillbaka, stängs klustret automatiskt av. 

En Seed-nod är ohälsosam om nodens status är nere, borttagen eller okänd.
Varnings rapporten för status för Dirigerings nod visar en lista över alla felaktiga Seed-noder med detaljerad information.

* **SourceId**: system.fm
* **Egenskap**: SeedNodeStatus
* **Nästa steg**: om den här varningen visas i klustret följer du anvisningarna nedan för att åtgärda det: för kluster som kör Service Fabric version 6,5 eller högre: för Service Fabric kluster i Azure försöker Service Fabric ändra den till en nod som inte är en Seed-nod automatiskt. Detta gör du genom att kontrol lera att antalet icke-startnoder i den primära nodtypen är större eller lika med antalet startnoder. Om det behövs kan du lägga till fler noder till den primära nodtypen för att uppnå detta.
Beroende på kluster status kan det ta lite tid att åtgärda problemet. När detta är klart rensas varnings rapporten automatiskt.

För att Service Fabric fristående kluster för att rensa varnings rapporten måste alla startnoder bli felfria. Beroende på varför dirigeringsrouters inte är felfria, måste olika åtgärder vidtas: om noden Seed är nere måste användarna ta med den noden. om noden Seed tas bort eller är okänd måste den här Dirigerings noden tas [bort från klustret](./service-fabric-cluster-windows-server-add-remove-nodes.md).
Varnings rapporten rensas automatiskt när alla startnoder blir felfria.

För kluster som kör Service Fabric version som är äldre än 6,5: i det här fallet måste varnings rapporten rensas manuellt. **Användarna bör se till att alla startnoder blir felfria innan de rensar rapporten**: om noden Seed är nere måste användarna ta den noden. om noden Seed tas bort eller är okänd måste den noden tas bort från klustret.
När alla startnoderna blivit felfria använder du följande kommando från PowerShell för att [Rensa varnings rapporten](/powershell/module/servicefabric/send-servicefabricclusterhealthreport):

```powershell
PS C:\> Send-ServiceFabricClusterHealthReport -SourceId "System.FM" -HealthProperty "SeedNodeStatus" -HealthState OK

## Node system health reports
System.FM, which represents the Failover Manager service, is the authority that manages information about cluster nodes. Each node should have one report from System.FM showing its state. The node entities are removed when the node state is removed. For more information, see [RemoveNodeStateAsync](/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### Node up/down
System.FM reports as OK when the node joins the ring (it's up and running). It reports an error when the node departs the ring (it's down, either for upgrading or simply because it has failed). The health hierarchy built by the health store acts on deployed entities in correlation with System.FM node reports. It considers the node a virtual parent of all deployed entities. The deployed entities on that node are exposed through queries if the node is reported as up by System.FM, with the same instance as the instance associated with the entities. When System.FM reports that the node is down or restarted, as a new instance, the health store automatically cleans up the deployed entities that can exist only on the down node or on the previous instance of the node.

* **SourceId**: System.FM
* **Property**: State.
* **Next steps**: If the node is down for an upgrade, it should come back up after it's been upgraded. In this case, the health state should switch back to OK. If the node doesn't come back or it fails, the problem needs more investigation.

The following example shows the System.FM event with a health state of OK for node up:

```powershell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Certifikatets förfallo datum
**System. FabricNode** rapporterar en varning när certifikat som används av noden snart upphör att gälla. Det finns tre certifikat per nod: **Certificate_cluster**, **Certificate_server** och **Certificate_default_client**. När giltighets tiden är minst två veckor är rapportens hälso tillstånd OK. När förfallo tiden är inom två veckor är rapport typen en varning. TTL för dessa händelser är oändligt och tas bort när en nod lämnar klustret.

* **SourceId**: system. FabricNode
* **Egenskap**: börjar med **certifikat** och innehåller mer information om certifikat typen.
* **Nästa steg**: uppdatera certifikaten om de snart upphör att gälla.

### <a name="load-capacity-violation"></a>Inläsnings kapacitets överträdelse
Den Service Fabric Load Balancer rapporterar en varning när en felaktig nod överskrids.

* **SourceId**: system. PLB
* **Egenskap**: börjar med **kapacitet**.
* **Nästa steg**: kontrol lera de angivna måtten och visa den aktuella kapaciteten på noden.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Nodens kapacitets matchnings fel för resurs styrnings mått
System. hosting rapporterar en varning om den definierade nodens kapacitet i kluster manifestet är större än den verkliga nodens kapacitet för resurs styrnings mått (minne och processor kärnor). En hälso rapport visas när det första tjänst paketet som använder [resurs styrning](service-fabric-resource-governance.md) registreras på en angiven nod.

* **SourceId**: system. hosting
* **Egenskap**: **ResourceGovernance**.
* **Nästa steg**: det här problemet kan vara ett problem eftersom hantering av tjänst paket inte används som förväntat och [resurs styrning](service-fabric-resource-governance.md) inte fungerar korrekt. Uppdatera kluster manifestet med rätt nod kapacitet för dessa mått, eller ange dem inte och låt Service Fabric automatiskt identifiera tillgängliga resurser.

## <a name="application-system-health-reports"></a>Hälso rapporter för program system
System.CM, som representerar kluster hanterarens tjänst, är den myndighet som hanterar information om ett program.

### <a name="state"></a>Tillstånd
System.CM rapporter som OK när programmet har skapats eller uppdaterats. Det informerar hälso lagret när programmet tas bort så att det kan tas bort från Store.

* **SourceId**: system.cm
* **Egenskap**: status.
* **Nästa steg**: om programmet har skapats eller uppdaterats bör det omfatta hälso rapporten i kluster hanteraren. Annars kontrollerar du programmets tillstånd genom att utfärda en fråga. Använd till exempel PowerShell-cmdleten **Get-ServiceFabricApplication-ApplicationName** *ApplicationName*.

I följande exempel visas tillstånds händelsen i programmet **Fabric:/WORDCOUNT** :

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Hälso rapporter för tjänst system
System.FM, som representerar den Redundanshanteraren tjänsten, är den myndighet som hanterar information om tjänster.

### <a name="state"></a>Tillstånd
System.FM rapporter som OK när tjänsten har skapats. Enheten tas bort från hälso arkivet när tjänsten tas bort.

* **SourceId**: system.fm
* **Egenskap**: status.

I följande exempel visas tillstånds händelsen för service **Fabric:/WORDCOUNT/WordCountWebService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Tjänst korrelations fel
**System. PLB** rapporterar ett fel när den identifierar att uppdatering av en tjänst är korrelerad med en annan tjänst som skapar en tillhörighets kedja. Rapporten rensas när en lyckad uppdatering sker.

* **SourceId**: system. PLB
* **Egenskap**: **ServiceDescription**.
* **Nästa steg**: kontrol lera de korrelerade tjänst beskrivningarna.

## <a name="partition-system-health-reports"></a>Partitionera system hälso rapporter
System.FM, som representerar tjänsten Redundanshanteraren, är den myndighet som hanterar information om diskpartitioner.

### <a name="state"></a>Tillstånd
System.FM rapporterar som OK när partitionen har skapats och är felfri. Enheten tas bort från hälso arkivet när partitionen tas bort.

Om partitionen är lägre än det lägsta antalet repliker, rapporteras ett fel. Om partitionen inte är lägre än det lägsta antalet repliker, men den är lägre än antalet mål repliker, rapporterar den en varning. Om partitionen förlorar kvorum rapporterar System.FM ett fel.

Andra viktiga händelser är en varning när omkonfigurationen tar längre tid än förväntat och när versionen tar längre tid än förväntat. De förväntade tiderna för generering och omkonfiguration kan konfigureras baserat på tjänst scenarier. Om en tjänst till exempel har ett terabyte-tillstånd, till exempel Azure SQL Database, tar det längre tid än för en tjänst med en liten mängd tillstånd.

* **SourceId**: system.fm
* **Egenskap**: status.
* **Nästa steg**: om hälso tillståndet inte är OK är det möjligt att vissa repliker inte har skapats, öppnats eller upphöjts till primär eller sekundär. 

Om beskrivningen beskriver kvorum förlust, och sedan undersöker den detaljerade hälso rapporten för repliker som är nere och där de ska säkerhets kopie ras, hjälper du att ta tillbaka partitionen online.

Om beskrivningen beskriver en partition som fastnar i [omkonfigurationen](service-fabric-concepts-reconfiguration.md)ger hälso rapporten på den primära repliken ytterligare information.

För andra System.FM hälso rapporter skulle det finnas rapporter om replikerna eller partitionen eller tjänsten från andra system komponenter. 

I följande exempel beskrivs några av de här rapporterna. 

I följande exempel visas en felfritt partition:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

I följande exempel visas hälsan för en partition som är lägre än mål replik antalet. Nästa steg är att hämta beskrivningen av partitionen, som visar hur den är konfigurerad: **MinReplicaSetSize** är tre och **TargetReplicaSetSize** är sju. Hämta sedan antalet noder i klustret, vilket i det här fallet är fem. I det här fallet kan två repliker inte placeras, eftersom mål antalet repliker är högre än antalet tillgängliga noder.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

I följande exempel visas hälsan för en partition som fastnar i omkonfigurationen på grund av att användaren inte har en token för uppsägning i **RunAsync** -metoden. Att undersöka hälso rapporten för en replik som marker ATS som primär (P) kan hjälpa till att öka detalj nivån på problemet.

```powershell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Hälso rapporten visar tillståndet för replikerna för partitionen som omkonfigureras: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Hälso rapporten innehåller för varje replik:
- Tidigare konfigurations roll
- Aktuell konfigurations roll
- [Replik status](service-fabric-concepts-replica-lifecycle.md)
- Nod som repliken körs på
- Replik-ID

I ett fall som till exempel krävs ytterligare undersökning. Undersök hälso tillståndet för varje enskild replik som börjar med replikerna markerade som `Primary` och `Secondary` (131482789658160654 och 131482789688598467) i föregående exempel.

### <a name="replica-constraint-violation"></a>Överträdelse av replik begränsning
**System. PLB** rapporterar en varning om en fel upptäcktes för en replik begränsning och det inte går att placera alla partitions repliker. I rapport detaljerna visas vilka begränsningar och egenskaper som förhindrar replik placeringen.

* **SourceId**: system. PLB
* **Egenskap**: börjar med **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Hälso rapporter för replik system
**System. ra**, som representerar komponenten för omkonfiguration av agent, är utfärdare för replik status.

### <a name="state"></a>Tillstånd
System. RA-rapporter är OK när repliken har skapats.

* **SourceId**: system. ra
* **Egenskap**: status.

I följande exempel visas en felfri replik:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Den här egenskapen används för att Visa varningar eller fel vid försök att öppna en replik, stänga en replik eller överföra en replik från en roll till en annan. Mer information finns i [replik livs cykel](service-fabric-concepts-replica-lifecycle.md). Fel kan vara undantag utlösta av API-anrop eller krascher i tjänst värd processen under den här tiden. För fel på grund av API-anrop från C#-kod lägger Service Fabric till undantag och stack spårning i hälso rapporten.

Dessa hälso varningar utlöses efter att du har försökt att utföra åtgärden lokalt under ett visst antal gånger (beroende på princip). Service Fabric försöker utföra åtgärden upp till ett maximalt tröskelvärde. När max gränsen har nåtts kan det försöka åtgärda problemet. Det här försöket kan orsaka att de här varningarna rensas när de visas på den här noden. Om en replik till exempel inte kan öppnas på en nod, Service Fabric genererar en hälso varning. Om repliken fortfarande inte kan öppnas Service Fabric agerar själv reparation. Den här åtgärden kan innebära att du försöker samma åtgärd på en annan nod. Det här försöket gör att den här repliken rensas. 

* **SourceId**: system. ra
* **Property**: **ReplicaOpenStatus**, **ReplicaCloseStatus** och **ReplicaChangeRoleStatus**.
* **Nästa steg**: Undersök tjänst koden eller krasch dum par för att identifiera varför åtgärden misslyckades.

I följande exempel visas hälso tillståndet för en replik som kastas `TargetInvocationException` från dess öppna-metod. Beskrivningen innehåller fel punkten, **IStatefulServiceReplica. Open**, undantags typen **TargetInvocationException** och stack spårningen.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

I följande exempel visas en replik som ständigt kraschar under stängning:

```powershell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Omkonfiguration
Den här egenskapen används för att ange när en replik som utför en [omkonfiguration](service-fabric-concepts-reconfiguration.md) upptäcker att omkonfigurationen har stoppats eller fastnat. Den här hälso rapporten kan finnas på den replik vars aktuella roll är primär, förutom i fall med en växlings primär omkonfiguration, där det kan finnas på repliken som degraderas från primär till aktiv sekundär.

Omkonfigurationen kan fastna av någon av följande orsaker:

- En åtgärd på den lokala repliken, samma replik som den som utför omkonfigurationen, slutförs inte. I detta fall kan det vara mer information att undersöka hälso rapporter för den här repliken från andra komponenter, system. RAP eller System.RE.

- En åtgärd slutförs inte på en fjärran sluten replik. Repliker för vilka åtgärder väntar visas i hälso rapporten. Ytterligare undersökningar bör utföras på hälso rapporter för dessa fjär repliker. Det kan också finnas kommunikations problem mellan den här noden och fjärrnoden.

I sällsynta fall kan omkonfigurationen fastna på grund av kommunikation eller andra problem mellan den här noden och tjänsten Redundanshanteraren.

* **SourceId**: system. ra
* **Egenskap**: omkonfiguration.
* **Nästa steg**: Undersök lokala eller fjärranslutna repliker beroende på hälso rapportens beskrivning.

I följande exempel visas en hälso rapport där en omkonfiguration fastnar på den lokala repliken. I det här exemplet beror det på att en tjänst inte följer token för uppsägning.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

I följande exempel visas en hälso rapport där en omkonfiguration fastnar i väntan på ett svar från två fjärranslutna repliker. I det här exemplet finns det tre repliker i partitionen, inklusive den aktuella primära. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Den här hälso rapporten visar att omkonfigurationen fastnar i väntan på ett svar från två repliker: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

För varje replik anges följande information:
- Tidigare konfigurations roll
- Aktuell konfigurations roll
- [Replik status](service-fabric-concepts-replica-lifecycle.md)
- Nod-ID
- Replik-ID

Så här avblockerar du omkonfigurationen:
- **Nedåt** -replikerna bör hämtas. 
- **Inbygga** repliker bör slutföra bygget och över gången till klar.

### <a name="slow-service-api-call"></a>Långsamma API-anrop
**System. Rap** och **system. Replicator** rapporterar en varning om ett anrop till användar tjänst koden tar längre tid än den konfigurerade tiden. Varningen rensas när anropet har slutförts.

* **SourceId**: system. Rap eller system. Replicator
* **Egenskap**: namnet på det långsamma API: t. Beskrivningen innehåller mer information om tiden som API: et har väntande.
* **Nästa steg**: ta reda på varför anropet tar längre tid än förväntat.

I följande exempel visas en hälso händelse från system. RAP för en tillförlitlig tjänst som inte bevisar en token för uppsägning i **RunAsync**:

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

Egenskapen och texten visar vilket API som har fastnat. Nästa steg som ska vidtas för olika låsta API: er skiljer sig åt. Alla API: er på *IStatefulServiceReplica* eller *IStatelessServiceInstance* är vanligt vis ett fel i tjänst koden. I följande avsnitt beskrivs hur dessa översätts till [Reliable Services modell](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica. Open**: den här varningen anger att ett anrop till `CreateServiceInstanceListeners` , `ICommunicationListener.OpenAsync` eller om det har åsidosatts, `OnOpenAsync` fastnar.

- **IStatefulServiceReplica. Close** och **IStatefulServiceReplica. abort**: det vanligaste fallet är att en tjänst inte följer den token för uppsägning som skickades till `RunAsync` . Det kan också vara att `ICommunicationListener.CloseAsync` , eller om det åsidosätts, `OnCloseAsync` fastnat.

- **IStatefulServiceReplica. ChangeRole (S)** och **IStatefulServiceReplica. ChangeRole (N)**: det vanligaste fallet är att en tjänst inte följer den token för uppsägning som skickades till `RunAsync` . I det här scenariot är den bästa lösningen att starta om repliken.

- **IStatefulServiceReplica. ChangeRole (P)**: det vanligaste fallet är att tjänsten inte har returnerat någon uppgift från `RunAsync` .

Andra API-anrop som kan fastna finns i **IReplicator** -gränssnittet. Exempel:

- **IReplicator. CatchupReplicaSet**: den här varningen anger ett av två saker. Det finns inte tillräckligt med repliker. Se om detta är fallet genom att titta på replikernas replik status i partitionen eller System.FM hälso rapport för en fastnad omkonfiguration. Eller också är replikerna inte åtgärdade. PowerShell-cmdleten `Get-ServiceFabricDeployedReplicaDetail` kan användas för att bestämma förloppet för alla repliker. Problemet beror på repliker vars `LastAppliedReplicationSequenceNumber` värde ligger bakom det primära `CommittedSequenceNumber` värdet.

- **IReplicator. BuildReplica ( \<Remote ReplicaId> )**: den här varningen anger ett problem i skapande processen. Mer information finns i [replik livs cykel](service-fabric-concepts-replica-lifecycle.md). Det kan bero på en felaktig konfiguration av den replikerade adressen. Mer information finns i [Konfigurera tillstånds känslig Reliable Services](service-fabric-reliable-services-configuration.md) och [Ange resurser i ett tjänst manifest](service-fabric-service-manifest-resources.md). Det kan också vara ett problem på fjärrnoden.

### <a name="replicator-system-health-reports"></a>System hälso rapporter för Replicator
**Replikerings kön är full:** 
 **System. Replicator** rapporterar en varning när målkön är full. I den primära replik kön blir vanligt vis fullständig eftersom en eller flera sekundära repliker är långsamma att bekräfta. På den sekundära inträffar detta vanligt vis när tjänsten är långsam att tillämpa åtgärderna. Varningen rensas när kön inte längre är full.

* **SourceId**: system. Replicator
* **Property**: **PrimaryReplicationQueueStatus** eller **SecondaryReplicationQueueStatus**, beroende på replik rollen.
* **Nästa steg**: om rapporten finns på den primära kontrollerar du anslutningen mellan noderna i klustret. Om alla anslutningar är felfria kan det finnas minst en långsam sekundär disk med en hög disk fördröjning för att tillämpa åtgärder. Om rapporten finns på den sekundära kontrollerar du disk användning och prestanda på noden först. Kontrol lera sedan den utgående anslutningen från den långsamma noden till den primära.

**RemoteReplicatorConnectionStatus:** 
 **System. Replicator** på den primära repliken rapporterar en varning när anslutningen till en sekundär (Remote) replikerare inte är felfri. Den fjärranslutna replikeringens adress visas i rapportens meddelande, vilket gör det enklare att identifiera om fel konfiguration skickades eller om det finns nätverks problem mellan replikerarna.

* **SourceId**: system. Replicator
* **Egenskap**: **RemoteReplicatorConnectionStatus**.
* **Nästa steg**: kontrol lera fel meddelandet och se till att den fjärranslutna replikerings adressen är korrekt konfigurerad. Om t. ex. fjärrreplikeraren är öppen med lyssnar adressen "localhost", går den inte att komma åt från utsidan. Om adressen ser korrekt ut kontrollerar du anslutningen mellan den primära noden och fjär adressen för att hitta eventuella nätverks problem.

### <a name="replication-queue-full"></a>Replikerings kön är full
**System. Replicator** rapporterar en varning när målkön är full. I den primära replik kön blir vanligt vis fullständig eftersom en eller flera sekundära repliker är långsamma att bekräfta. På den sekundära inträffar detta vanligt vis när tjänsten är långsam att tillämpa åtgärderna. Varningen rensas när kön inte längre är full.

* **SourceId**: system. Replicator
* **Property**: **PrimaryReplicationQueueStatus** eller **SecondaryReplicationQueueStatus**, beroende på replik rollen.

### <a name="slow-naming-operations"></a>Långsamma namngivnings åtgärder
**System. NamingService** rapporterar hälso tillståndet för den primära repliken när en namngivnings åtgärd tar längre tid än acceptabelt. Exempel på namngivnings åtgärder är [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) eller [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Fler metoder hittar du under FabricClient. De kan till exempel hittas under [Service Management-metoder](/dotnet/api/system.fabric.fabricclient.servicemanagementclient) eller [egenskaps hanterings metoder](/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Namngivnings tjänsten matchar tjänst namn till en plats i klustret. Användare kan använda den för att hantera tjänst namn och egenskaper. Det är en Service Fabric partitionerad, beständig tjänst. En av partitionerna representerar *auktoritets ägaren*, som innehåller metadata om alla Service Fabric namn och tjänster. Service Fabric namn mappas till olika partitioner, så kallade *namn på ägarnoder* , så att tjänsten är utöknings bar. Läs mer om [namngivnings tjänsten](service-fabric-architecture.md).
> 
> 

När det tar längre tid än förväntat att utföra en namngivnings åtgärd flaggas åtgärden med en varnings rapport om den primära repliken av namngivnings tjänstens partition som hanterar åtgärden. Om åtgärden slutförs raderas varningen. Om åtgärden slutförs med ett fel innehåller hälso rapporten information om felet.

* **SourceId**: system. NamingService
* **Property**: börjar med prefixet "**Duration_**" och identifierar den långsamma åtgärden och det Service Fabric namn som åtgärden tillämpas på. Om t. ex. skapa tjänst vid namn **infrastruktur:/MyApp/** t tar för lång tid, är egenskapen **Duration_AOCreateService. Fabric:/MyApp/unservice**. "AO" pekar på den namngivnings partitionens roll för det här namnet och åtgärden.
* **Nästa steg**: kontrol lera varför namngivnings åtgärden Miss lyckas. Varje åtgärd kan ha olika rotor orsaker. Till exempel kan borttagnings tjänsten fastna. Tjänsten kan ha fastnat eftersom program värden håller på att krascha på en nod på grund av en användar fel i tjänst koden.

I följande exempel visas en tjänst åtgärd för att skapa. Åtgärden tog längre tid än den konfigurerade varaktigheten. "AO"-försök och skickar arbete till "nej". "Nej" slutförde den senaste åtgärden med TIMEOUT. I det här fallet är samma replik primär för rollerna "AO" och "NO".

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication system hälso rapporter
**System. hosting** är utfärdaren på distribuerade entiteter.

### <a name="activation"></a>Aktivering
System. hosting-rapporter som OK när ett program har Aktiver ATS på noden. Annars rapporteras ett fel.

* **SourceId**: system. hosting
* **Egenskap**: **aktivering**, inklusive utgivnings versionen.
* **Nästa steg**: om programmet inte är felfritt kan du undersöka varför aktiveringen misslyckades.

I följande exempel visas en lyckad aktivering:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Ladda ned
System. hosting rapporterar ett fel om hämtningen av ett program paket Miss lyckas.

* **SourceId**: system. hosting
* **Egenskap**: **Ladda ned**, inklusive utgivnings versionen.
* **Nästa steg**: ta reda på varför hämtningen misslyckades på noden.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage system hälso rapporter
**System. hosting** är utfärdaren på distribuerade entiteter.

### <a name="service-package-activation"></a>Aktivering av tjänst paket
System. hosting-rapporter som OK om aktiveringen av tjänst paketet på noden lyckas. Annars rapporteras ett fel.

* **SourceId**: system. hosting
* **Egenskap**: aktivering.
* **Nästa steg**: Undersök varför aktiveringen misslyckades.

### <a name="code-package-activation"></a>Aktivering av kod paket
System. hosting-rapporter som OK för varje kod paket om aktiveringen lyckas. Om aktiveringen Miss lyckas rapporterar den en varning som har kon figurer ATS. Om **CodePackage** inte kan aktive ras eller avslutas med ett fel som är större än den konfigurerade **CodePackageHealthErrorThreshold**, kan du rapportera ett fel. Om ett tjänst paket innehåller flera kod paket genereras en aktiverings rapport för var och en.

* **SourceId**: system. hosting
* **Egenskap**: använder prefixet **CodePackageActivation** och innehåller namnet på kod paketet och start punkten som *CodePackageActivation: CodePackageName: SetupEntryPoint/EntryPoint*. Till exempel **CodePackageActivation: code: SetupEntryPoint**.

### <a name="service-type-registration"></a>Registrering av tjänst typ
System. hosting-rapporter som OK om tjänst typen har registrerats. Det rapporterar ett fel om registreringen inte skett i tid enligt konfigurationen med hjälp av **ServiceTypeRegistrationTimeout**. Om körningen har stängts avregistreras tjänst typen från noden och är värd för rapporter om en varning.

* **SourceId**: system. hosting
* **Egenskap**: använder prefixet **ServiceTypeRegistration** och innehåller namnet på tjänst typen. Till exempel **ServiceTypeRegistration: FileStoreServiceType**.

I följande exempel visas ett felfritt distribuerat tjänst paket:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Ladda ned
System. hosting rapporterar ett fel om hämtningen av ett tjänst paket Miss lyckas.

* **SourceId**: system. hosting
* **Egenskap**: **Ladda ned**, inklusive utgivnings versionen.
* **Nästa steg**: ta reda på varför hämtningen misslyckades på noden.

### <a name="upgrade-validation"></a>Uppgraderings validering
System. hosting rapporterar ett fel om verifieringen under uppgraderingen Miss lyckas eller om uppgraderingen Miss lyckas på noden.

* **SourceId**: system. hosting
* **Egenskap**: använder prefixet **FabricUpgradeValidation** och innehåller uppgraderings versionen.
* **Beskrivning**: pekar på det fel som påträffades.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Odefinierad Node-kapacitet för resurs styrnings mått
System. hosting rapporterar en varning om nodens kapacitet inte har definierats i kluster manifestet och konfigurationen för automatisk identifiering är avstängd. Service Fabric genererar en hälso varning när tjänst paketet som använder [resurs styrning](service-fabric-resource-governance.md) registrerar på en angiven nod.

* **SourceId**: system. hosting
* **Egenskap**: **ResourceGovernance**.
* **Nästa steg**: det bästa sättet att lösa det här problemet är att ändra kluster manifestet för att aktivera automatisk identifiering av tillgängliga resurser. Ett annat sätt är att uppdatera kluster manifestet med korrekt angiven nods kapacitet för dessa mått.

## <a name="next-steps"></a>Nästa steg
* [Visa Service Fabric hälso rapporter](service-fabric-view-entities-aggregated-health.md)

* [Rapportera och kontrol lera tjänstens hälsa](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Service Fabric program uppgradering](service-fabric-application-upgrade.md)
