---
title: Ta bort en nodtyp i Azure Service Fabric | Microsoft Docs
description: Lär dig hur du tar bort en nodtyp från ett Service Fabric kluster som körs i Azure.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: chrpap
ms.openlocfilehash: ede999bee9ce1a4a9dd10652a2c52a840d5b24be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88163585"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Så här tar du bort en Service Fabric Node-typ
Den här artikeln beskriver hur du skalar ett Azure Service Fabric-kluster genom att ta bort en befintlig nodtyp från ett kluster. Ett Service Fabric kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras i. En dator eller en virtuell dator som ingår i ett kluster kallas för en nod. Skalnings uppsättningar för virtuella datorer är en Azure Compute-resurs som du använder för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster har [kon figurer ATS som en separat skalnings uppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. När du har skapat ett Service Fabric-kluster kan du skala ett kluster vågrätt genom att ta bort en nodtyp (skalnings uppsättning för virtuell dator) och alla dess noder.  Du kan skala klustret när som helst, även när arbets belastningar körs på klustret.  När klustret skalas, skalas programmen automatiskt.

> [!WARNING]
> Att använda den här metoden för att ta bort en nodtyp från ett produktions kluster bör inte användas regelbundet. Det är ett farligt kommando eftersom det tar bort den virtuella datorns skalnings uppsättnings resurs bakom nodtypen. 

## <a name="durability-characteristics"></a>Hållbarhets egenskaper
Säkerhet prioriteras över hastighet när du använder Remove-AzServiceFabricNodeType. Nodtypen måste vara silver eller Gold- [hållbarhets nivå](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), eftersom:
- Brons ger inga garantier för att spara statusinformation.
- Silver-och guld tåligheten sväller alla ändringar i skalnings uppsättningen.
- Guld ger dig också kontroll över Azure updates under skalnings uppsättning.

Service Fabric "dirigerar" underliggande ändringar och uppdateringar så att inga data förloras. Men när du tar bort en nodtyp med brons hållbarhet kan du förlora statusinformation. Om du tar bort en primär nodtyp och ditt program är tillstånds löst, är brons acceptabelt. När du kör tillstånds känsliga arbets belastningar i produktionen bör den lägsta konfigurationen vara silver. På samma sätt bör den primära nodtypen alltid vara silver eller guld för produktions scenarier.

### <a name="more-about-bronze-durability"></a>Mer om brons-hållbarhet

När du tar bort en nodtyp som är brons går alla noder i nodtypen omedelbart ned. Service Fabric sväller inte alla de virtuella datorerna för skalnings uppsättningar i brons-noder, och därför går alla virtuella datorer omedelbart ned Om du hade något tillstånd för dessa noder försvinner data. Även om du har tillstånds lösa kan alla noder i Service Fabric delta i ringen, vilket innebär att hela nätverket kan gå förlorat, vilket kan göra själva klustret.

## <a name="remove-a-node-type"></a>Ta bort en nodtyp

1. Följ dessa krav innan du startar processen.

    - Klustret är felfritt.
    - Det kommer fortfarande att finnas tillräckligt med kapacitet efter att nodtypen har tagits bort, t. ex. antal noder som krävs för att placera det nödvändiga replik antalet.

2. Flytta alla tjänster som har placerings begränsningar att använda Node-typen av nodtypen.

    - Ändra programmet/tjänst manifestet till att inte längre referera till nodtypen.
    - Distribuera ändringen.

    Kontrol lera sedan att:
    - Alla tjänster som ändras ovan körs inte längre på den nod som tillhör nodtypen.
    - Alla tjänster är felfria.

3. Avmarkera nod-typen som icke-primär (hoppa över för icke-primära nodtyper)

    - Leta upp den Azure Resource Manager-mall som används för distribution.
    - Hitta avsnittet som är relaterat till nodtypen i avsnittet Service Fabric.
    - Ändra egenskapen isPrimary till false. * * Ta inte bort avsnittet som är relaterat till nodtypen i den här uppgiften.
    - Distribuera den ändrade Azure Resource Manager-mallen. * * Beroende på kluster konfigurationen kan det här steget ta en stund.
    
    Kontrol lera sedan att:
    - Service Fabric avsnittet i portalen indikerar att klustret är klart.
    - Klustret är felfritt.
    - Ingen av noderna som tillhör nodtypen är markerade som Seed-nod.

4. Inaktivera varje nod i nodtypen.

    Anslut till klustret med PowerShell och kör sedan följande steg.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - För brons hållbarhet väntar du tills alla noder får inaktiverat tillstånd
    - För silver-och guld tålighet kommer vissa noder att gå in till inaktiverade och resten kommer att inaktive ras. På fliken information i noderna inaktive ras, om alla har fastnat på att säkerställa kvorum för infrastruktur tjänst partitioner, är det säkert att fortsätta.

5. Stoppa data för nodtypen.

    Anslut till klustret med PowerShell och kör sedan följande steg.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    Vänta till alla noder för nodtypen är markerade.

6. Frigör noder i den ursprungliga skalnings uppsättningen för virtuella datorer
    
    Logga in på Azure-prenumerationen där skalnings uppsättningen distribuerades och ta bort skalnings uppsättningen för den virtuella datorn. 

    ```powershell
    $scaleSetName="myscaleset"
    $scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"
    
    Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
    ```

    
7. Ta bort data för nodtypen.

    Anslut till klustret med PowerShell och kör sedan följande steg.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    Vänta till alla noder tas bort från klustret. Noderna ska inte visas i SFX.

8. Ta bort nodtypen från Service Fabric-avsnittet.

    - Leta upp den Azure Resource Manager-mall som används för distribution.
    - Hitta avsnittet som är relaterat till nodtypen i avsnittet Service Fabric.
    - Ta bort avsnittet som motsvarar nodtypen.
    - Uppdatera kluster resursen i mallen och konfigurera hälso principer för att ignorera infrastruktur resurser:/systemets program hälsa genom att lägga till `applicationDeltaHealthPolicies` under kluster resurs `properties` enligt vad som framgår nedan. Principen nedan bör ignorera befintliga fel men inte tillåta nya hälso fel. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - Distribuera den ändrade Azure Resource Manager-mallen. * * Detta steg tar en stund, vanligt vis upp till två timmar. Den här uppgraderingen ändrar inställningarna till InfrastructureService, vilket innebär att en omstart av noden krävs. I det här fallet `forceRestart` ignoreras. 
    Parametern `upgradeReplicaSetCheckTimeout` anger den maximala tid som Service Fabric väntar på att en partition ska vara i ett säkert tillstånd, om den inte redan är i säkert tillstånd. När säkerhets kontrollerna har godkänts för alla partitioner på en nod fortsätter Service Fabric med uppgraderingen på noden.
    Värdet för parametern `upgradeTimeout` kan minskas till 6 timmar, men för maximal säkerhet 12 timmar bör användas.

    Kontrol lera sedan att:
    - Service Fabric resursen i portalen är klar.

9. Ta bort alla referenser till resurserna som är relaterade till nodtypen från ARM-mallen.

    - Leta upp den Azure Resource Manager-mall som används för distribution.
    - Ta bort den virtuella datorns skalnings uppsättning och andra resurser som är relaterade till nodtypen från mallen.
    - Distribuera ändringarna.

    Sedan:
    - Vänta tills distributionen har slutförts.
    
10. Ta bort resurser som är relaterade till nodtypen som inte längre används. Exempel Load Balancer och offentlig IP-adress. 

    - Om du vill ta bort dessa resurser kan du använda samma PowerShell-kommando som används i steg 6 för att ange den angivna resurs typen och API-versionen. 

> [!Note]
> Det här steget är valfritt om samma Load Balancer och IP återanvänds mellan olika nodtyper.

## <a name="next-steps"></a>Nästa steg
- Läs mer om klustrets [hållbarhets egenskaper](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster).
- Läs mer om [nodtyper och Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md).
- Läs mer om hur du [Service Fabric kluster skalning](service-fabric-cluster-scaling.md).
