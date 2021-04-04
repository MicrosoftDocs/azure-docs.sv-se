---
title: Skala ett Service Fabric kluster in eller ut
description: Skala ett Service Fabric kluster i eller ut för att matcha efter frågan genom att ange regler för automatisk skalning för varje nodtyp/virtuell dators skalnings uppsättning. Lägga till eller ta bort noder i ett Service Fabric-kluster
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 6ee04c73b75d6b335e450ff816c51f0a3089b918
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94409968"
---
# <a name="scale-a-cluster-in-or-out"></a>Skala in eller ut ett kluster

> [!WARNING]
> Läs det här avsnittet innan du skalar

Skala beräknings resurser till Källa din program arbets belastning kräver avsiktlig planering, kommer nästan alltid att ta längre tid än en timme att slutföra för en produktions miljö och kräver att du förstår din arbets belastning och ditt företags sammanhang. Om du aldrig har gjort den här aktiviteten tidigare rekommenderar vi att du börjar med att läsa och förstå [Service Fabric överväganden vid planering av kluster kapacitet](service-fabric-cluster-capacity.md), innan du fortsätter med resten av det här dokumentet. Den här rekommendationen är att undvika oönskade LiveSite-problem, och det rekommenderas också att du har testat de åtgärder som du bestämmer dig för att utföra mot en icke-produktions miljö. Du kan när som helst [rapportera produktions problem eller begära betald support för Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). För tekniker som allokeras för att utföra dessa åtgärder som har lämplig kontext, beskriver den här artikeln skalnings åtgärder, men du måste bestämma och förstå vilka åtgärder som är lämpliga för ditt användnings fall. till exempel vilka resurser som ska skalas (CPU, lagring, minne), vilken riktning som ska skalas (lodrätt eller vågrätt) och vilka åtgärder som ska utföras (resurs Malldistribution, Portal, PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Skala ett Service Fabric kluster i eller ut med regler för automatisk skalning eller manuellt
Skalnings uppsättningar för virtuella datorer är en Azure Compute-resurs som du kan använda för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Service Fabric kluster har kon figurer ATS som en separat skalnings uppsättning för virtuella datorer. Varje nodtyp kan sedan skalas in eller ut oberoende av varandra, ha olika port uppsättningar öppna och kan ha olika kapacitets mått. Läs mer om det i dokumentet [Service Fabric Node types](service-fabric-cluster-nodetypes.md) . Eftersom Service Fabric-nodtypen i klustret består av virtuella datorers skalnings uppsättningar på Server delen måste du konfigurera regler för automatisk skalning för varje nodtyp/virtuell dators skalnings uppsättning.

> [!NOTE]
> Prenumerationen måste ha tillräckligt många kärnor för att lägga till de nya virtuella datorerna som utgör klustret. Det finns för närvarande ingen modell validering, så du får ett distributions tids haveri, om någon av kvot gränserna uppnåddes. Dessutom kan en enda nodtyp inte bara överstiga 100 noder per VMSS. Du kan behöva lägga till VMSS för att uppnå den aktuella skalan, och automatisk skalning kan inte automatiskt lägga till VMSS. Att lägga till VMSS på plats i ett Live-kluster är en utmanings uppgift, och vanligt vis ger användare etablering av nya kluster med lämpliga nodtyper som skapas vid skapande tillfället. [Planera kluster kapaciteten](./service-fabric-cluster-capacity.md) enligt detta. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Välj nodtyp/virtuell dator skalnings uppsättning som ska skalas
För närvarande kan du inte ange regler för automatisk skalning för skalnings uppsättningar för virtuella datorer med hjälp av portalen för att skapa ett Service Fabric kluster, så låt oss använda Azure PowerShell (1.0 +) för att Visa nodtyper och sedan lägga till regler för automatisk skalning till dem.

Kör följande cmdlets om du vill hämta en lista över den skalnings uppsättning för virtuella datorer som utgör klustret:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Ange regler för automatisk skalning för den här nodtypen/skalnings uppsättningen för virtuell dator
Om klustret har flera nodtyper upprepar du detta för varje nodtyper/skalnings uppsättningar för virtuella datorer som du vill skala (in eller ut). Ta hänsyn till antalet noder som du måste ha innan du ställer in automatisk skalning. Det minsta antalet noder som du måste ha för den primära nodtypen avgörs av tillförlitlighetsnivån som du har valt. Läs mer om [Tillförlitlighets nivåer](service-fabric-cluster-capacity.md).

> [!NOTE]
> Om du skalar den primära nodtypen till mindre än det lägsta tillåtna antalet blir klustret instabilt eller till och med det. Detta kan leda till data förlust för dina program och system tjänsterna.
> 
> 

För närvarande styrs funktionen för automatisk skalning inte av de belastningar som dina program kan rapportera till Service Fabric. Så nu är den automatiska skalningen du får helt drived av de prestanda räknare som genereras av var och en av de virtuella datorernas skalnings uppsättnings instanser.  

Följ de här anvisningarna [för att ställa in automatisk skalning för varje skalnings uppsättning för virtuella datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> I en skala i scenariot, om inte nodtypen har en [hållbarhets nivå][durability] på guld eller silver, måste du anropa [Remove-ServiceFabricNodeState-cmdlet](/powershell/module/servicefabric/remove-servicefabricnodestate) : en med rätt nodnamn. För brons-hållbarhet rekommenderar vi inte att du skalar på mer än en nod i taget.
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Manuellt lägga till virtuella datorer till en nodtyp/skalnings uppsättning för virtuella datorer

När du skalar ut lägger du till fler instanser av virtuella datorer i skalningsuppsättningen. Dessa instanser blir de noder som används i Service Fabric. Service Fabric vet när fler instanser läggs till i skalningsuppsättningen (genom utskalning) och reagerar automatiskt. 

> [!NOTE]
> Det tar tid att lägga till virtuella datorer, så det är inte troligt att tilläggen tas i beaktande. Planera för att lägga till kapacitet på ett bra sätt, så att det blir mer än 10 minuter innan VM-kapaciteten är tillgänglig för repliker/tjänst instanser som ska placeras.
> 

### <a name="add-vms-using-a-template"></a>Lägg till virtuella datorer med en mall
Följ exemplet/anvisningarna i [galleriet snabb starts mal len](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) för att ändra antalet virtuella datorer i varje nodtyp. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Lägg till virtuella datorer med PowerShell-eller CLI-kommandon
Följande kod hämtar en skalningsuppsättning efter namn och ökar **kapaciteten** för skalningsuppsättningen med 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Den här koden anger värdet 6 för kapaciteten.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Ta bort virtuella datorer manuellt från en nodtyp/skalnings uppsättning för virtuella datorer
När du skalar i en nodtyp tar du bort virtuella dator instanser från skalnings uppsättningen. Om nodtypen är brons hållbarhets nivå kan Service Fabric inte identifieras vad som har hänt och rapporterar att en nod saknas. Service Fabric rapporterar ett feltillstånd för klustret. För att förhindra att det är felaktigt, måste du uttryckligen ta bort noden från klustret och ta bort nodens tillstånd.

Service Fabric system Services körs i den primära nodtypen i klustret. Vid skalning i den primära nodtypen, skala aldrig antalet instanser till mindre än vad [Tillförlitlighets nivån](service-fabric-cluster-capacity.md) garanterar. 
 
För en tillstånds känslig tjänst behöver du ett visst antal noder för att alltid vara upp till att bibehålla tillgänglighet och bevarande av tjänstens status. Du behöver minst antalet noder som motsvarar antalet mål replik uppsättningar för partition/tjänst.

### <a name="remove-the-service-fabric-node"></a>Ta bort Service Fabric-noden

Stegen för att ta bort Node-tillstånd manuellt gäller endast för nodtyper med en *brons* -hållbarhets nivå.  För *silver* -och *Gold* -hållbarhets nivån görs de här stegen automatiskt av plattformen. Mer information om hållbarhet finns i [Kapacitetsplanering för Service Fabric-kluster][durability].

>[!NOTE]
> Behåll det lägsta antalet fem noder för alla skalnings uppsättningar för virtuella datorer som har en hög grad av guld eller silver aktiverat. Klustret kommer att ange fel tillstånd om du skalar under det här tröskelvärdet och du måste rensa bort de borttagna noderna manuellt.

Om du vill hålla noderna i klustret jämnt fördelade över uppgraderings- och feldomäner och därmed möjliggöra jämn användning av dem, bör den senast skapade noden tas bort först. Med andra ord bör noderna tas bort i omvänd ordning mot hur de skapades. Den senast skapade noden är den som har den största egenskapsvärdet för `virtual machine scale set InstanceId`. Kodexemplen nedan returnerar den senast skapade noden.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Service Fabric-klustret måste informeras om att noden ska tas bort. Du måste göra tre saker:

1. Inaktivera noden så att den inte längre är en replik av data.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Stoppa noden så att Service Fabric-körningen får en ren avstängning och appen får en avslutningsbegäran.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Ta bort noden från klustret.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

När du har utfört dessa steg för noden kan du ta bort den från skalningsuppsättningen. Om du har någon annan hållbarhetsnivå än [Brons][durability] görs dessa steg åt dig när instansen av skalningsuppsättningen tas bort.

Följande kodblock hämtar den senaste skapade noden och inaktiverar, stoppar och tar bort noden från klustret.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

I **sfctl**-koden nedan används följande kommando för att hämta **node-name**-värdet för den nod som skapats senast: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Använd följande **sfctl**-frågor för att kontrollera status för varje steg
>
> **Kontrol lera status för avaktivering**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Kontrol lera stopp status**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Skala in skalningsuppsättningen

Nu när Service Fabric-noden har tagits bort från klustret kan VM-skalningsuppsättningen skalas in. I exemplet nedan minskas skalningsuppsättningskapaciteten med 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Den här koden anger värdet 5 för kapaciteten.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Beteenden som du kan studera i Service Fabric Explorer
När du skalar ut ett kluster visas Service Fabric Explorer det antal noder (skalnings uppsättnings instanser för virtuella datorer) som ingår i klustret.  När du skalar ett kluster i visas dock den borttagna noden/VM-instansen som visas i ett ohälsosamt tillstånd om du inte anropar [Remove-ServiceFabricNodeState cmd](/powershell/module/servicefabric/remove-servicefabricnodestate) med lämpligt nodnamn.   

Här är en förklaring till det här beteendet.

Noderna som anges i Service Fabric Explorer är en reflektion av vad Service Fabric system tjänster (särskilt) vet om antalet noder som klustret hade/har. När du skalar den virtuella datorns skalnings uppsättning i, har den virtuella datorn tagits bort, men FM-systemtjänsten tror fortfarande att noden (som var mappad till den virtuella datorn som togs bort) kommer tillbaka. Så Service Fabric Explorer fortsätta att visa noden (trots att hälso tillståndet kan vara fel eller okänd).

För att se till att en nod tas bort när en virtuell dator tas bort har du två alternativ:

1. Välj en hållbarhets nivå på guld eller silver för nodtyper i klustret, vilket ger dig infrastruktur integrering. Sedan tas noderna automatiskt bort från systemet med system tjänster (FM) när du skalar i.
Läs [informationen om hållbarhets nivåer här](service-fabric-cluster-capacity.md)

> [!NOTE]
> Behåll det lägsta antalet fem noder för alla skalnings uppsättningar för virtuella datorer som har en hög grad av guld eller silver aktiverat. Klustret kommer att ange fel tillstånd om du skalar under det här tröskelvärdet och du måste rensa bort de borttagna noderna manuellt.

2. När den virtuella dator instansen har skalats i måste du anropa [cmdleten Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Service Fabric kluster kräver ett visst antal noder för att kunna upprätthålla tillgänglighet och bevara tillstånd – kallas "Kvarhåll kvorum". Därför är det vanligt vis osäkert att stänga av alla datorer i klustret, såvida du inte har gjort en [fullständig säkerhets kopia av ditt tillstånd](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Nästa steg
Läs följande om du vill veta mer om hur du planerar kluster kapacitet, uppgraderar ett kluster och partitionerar tjänster:

* [Planera din kluster kapacitet](service-fabric-cluster-capacity.md)
* [Kluster uppgraderingar](service-fabric-cluster-upgrade.md)
* [Partitionera tillstånds känsliga tjänster för maximal skala](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-in-out/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-in-out/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
