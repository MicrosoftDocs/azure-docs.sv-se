---
title: Felsöka program uppgraderingar
description: Den här artikeln beskriver några vanliga problem med att uppgradera ett Service Fabric program och hur du löser dem.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d462f2c2482e0fbb4d252967754a9675ed362674
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009355"
---
# <a name="troubleshoot-application-upgrades"></a>Felsök programuppgraderingar

Den här artikeln beskriver några vanliga problem med att uppgradera ett Azure Service Fabric-program och hur du löser dem.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Felsöka en misslyckad program uppgradering

När en uppgradering Miss lyckas innehåller utdata från kommandot **Get-ServiceFabricApplicationUpgrade** ytterligare information för fel sökning av fel.  I följande lista anges hur ytterligare information kan användas:

1. Identifiera typen av problem.
2. Identifiera orsaken till problemet.
3. Isolera en eller flera felaktiga komponenter för ytterligare undersökning.

Den här informationen är tillgänglig när Service Fabric identifierar felet oavsett om **FailureAction** är att återställa eller pausa uppgraderingen.

### <a name="identify-the-failure-type"></a>Identifiera typ av problem

I utdata från **Get-ServiceFabricApplicationUpgrade** identifierar **FailureTimestampUtc** tidsstämpeln (i UTC) då ett uppgraderings problem upptäcktes av Service Fabric och **FailureAction** utlöstes. **FailureReason** identifierar en av tre möjliga orsaker till att fel uppstår på hög nivå:

1. UpgradeDomainTimeout – anger att en viss uppgraderings domän tog för lång tid att slutföra och **UpgradeDomainTimeout** har upphört att gälla.
2. OverallUpgradeTimeout – anger att den totala uppgraderingen tog för lång tid att slutföras och **UpgradeTimeout** har upphört att gälla.
3. HealthCheck-anger att när du har uppgraderat en uppdaterings domän låg programmet i fel tillstånd enligt de angivna hälso principerna och **HealthCheckRetryTimeout** har upphört att gälla.

Dessa poster visas bara i utdata när uppgraderingen Miss lyckas och börjar återställas. Ytterligare information visas beroende på typen av haveri.

### <a name="investigate-upgrade-timeouts"></a>Undersök uppgraderings tids gränser

Fel vid uppgraderings tids gränsen orsakas oftast av tjänst tillgänglighets problem. Utdata efter det här stycket är typiska för uppgraderingar där tjänst repliker eller instanser inte kan starta i den nya kod versionen. Fältet **UpgradeDomainProgressAtFailure** samlar in en ögonblicks bild av väntande uppgraderings arbete vid tidpunkten för haveriet.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

I det här exemplet gick det inte att uppgradera domänen *MYUD1* och två partitioner (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* och *4b43f4d8-b26b-424e-9307-7a7a62e79750*) har fastnat. Partitionerna har fastnat eftersom körningen inte kunde placera primära repliker (*WaitForPrimaryPlacement*) på målnoden *Nod1* och *nod4*.

Kommandot **Get-ServiceFabricNode** kan användas för att kontrol lera att dessa två noder finns i uppgraderings domänen *MYUD1*. *UpgradePhase* säger *PostUpgradeSafetyCheck*, vilket innebär att dessa säkerhets kontroller utförs när alla noder i uppgraderings domänen har uppgraderat. All den här informationen leder till ett möjligt problem med den nya versionen av program koden. De vanligaste problemen är tjänst fel i öppna eller befordra till primära kod Sök vägar.

En *UpgradePhase* av *PreUpgradeSafetyCheck* innebär att det uppstod problem när uppgraderings domänen skulle förberedas innan den utfördes. De vanligaste problemen i det här fallet är tjänst fel i stängnings-eller degradering från primära kod Sök vägar.

Den aktuella **UpgradeState** är *RollingBackCompleted*, så den ursprungliga uppgraderingen måste ha genomförts med en rollback- **FailureAction**, vilket automatiskt återställde uppgraderingen vid ett haveri. Om den ursprungliga uppgraderingen utfördes med en manuell **FailureAction** skulle uppgraderingen i stället vara i ett inaktiverat tillstånd för att tillåta Live-felsökning av programmet.

I sällsynta fall kan fältet **UpgradeDomainProgressAtFailure** vara tomt om den totala uppgraderings tiden är på samma sätt som systemet har slutfört allt arbete för den aktuella uppgraderings domänen. Om detta inträffar kan du försöka med att öka värdena för **UpgradeTimeout** och **UpgradeDomainTimeout** och försöka uppgradera igen.

### <a name="investigate-health-check-failures"></a>Undersök hälso kontroll fel

Hälso kontroll fel kan utlösas av olika problem som kan uppstå efter att alla noder i en uppgraderings domän slutfört uppgraderingen och avpassat alla säkerhets kontroller. De utdata som följer efter detta stycke är typiska för ett uppgraderings fel på grund av misslyckade hälso kontroller. Fältet **UnhealthyEvaluations** samlar in en ögonblicks bild av hälso kontroller som misslyckades vid tidpunkten för uppgraderingen enligt den angivna [hälso principen](service-fabric-health-introduction.md).

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Om du undersöker hälso kontroll fel måste du först förstå Service Fabric hälso modellen. Men även utan en djupgående förståelse kan vi se att två tjänster är felaktiga: *Fabric:/DemoApp/Svc3* och *Fabric:/DemoApp/Svc2*, tillsammans med fel hälso rapporter ("InjectedFault" i det här fallet). I det här exemplet är två av fyra tjänster felaktiga, vilket är under standard målet på 0% unhealthy (*MaxPercentUnhealthyServices*).

Uppgraderingen avbröts vid misslyckande genom att ange en **FailureAction** manuellt när uppgraderingen startades. Med det här läget kan vi undersöka Live-systemet i fel tillstånd innan du vidtar ytterligare åtgärder.

### <a name="recover-from-a-suspended-upgrade"></a>Återställa från en pausad uppgradering

Med en **FailureAction** återställs ingen återställning eftersom uppgraderingen automatiskt återställs vid misslyckande. Det finns flera återställnings alternativ med en manuell **FailureAction**:

1.  Utlös en återställning
2. Fortsätt genom resten av uppgraderingen manuellt
3. Återuppta den övervakade uppgraderingen

Kommandot **Start-ServiceFabricApplicationRollback** kan användas när som helst för att börja återställa programmet. När kommandot har returnerats har återställnings förfrågan registrerats i systemet och startar strax därefter.

Kommandot **Resume-ServiceFabricApplicationUpgrade** kan användas för att gå igenom resten av uppgraderingen manuellt, en uppgraderings domän i taget. I det här läget utförs endast säkerhets kontroller av systemet. Inga fler hälso kontroller utförs. Det här kommandot kan endast användas när *UpgradeState* visar *RollingForwardPending*, vilket innebär att den aktuella uppgraderings domänen är klar med uppgraderingen men att nästa inte har startats (väntar).

Kommandot **Update-ServiceFabricApplicationUpgrade** kan användas för att återuppta den övervakade uppgraderingen med både säkerhets-och hälso kontroller som utförs.

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Uppgraderingen fortsätter från uppgraderings domänen där den senast pausades och använder samma uppgraderings parametrar och hälso principer som tidigare. Vid behov kan alla uppgraderings parametrar och hälso principer som visas i föregående utdata ändras i samma kommando när uppgraderingen återupptas. I det här exemplet återupptogs uppgraderingen i övervakat läge, med parametrarna och hälso principerna oförändrade.

## <a name="further-troubleshooting"></a>Ytterligare fel sökning

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric följer inte de angivna hälso principerna

Möjlig orsak 1:

Service Fabric översätter alla procent andelar till faktiska antal entiteter (till exempel repliker, partitioner och tjänster) för utvärdering av hälso tillstånd och avrundar alltid upp till hela entiteter. Om den maximala *MaxPercentUnhealthyReplicasPerPartition* är till exempel 21% och det finns fem repliker, kan Service Fabric upp till två felaktiga repliker (det vill säga `Math.Ceiling (5*0.21)` ). Därför bör hälso principerna anges i enlighet med detta.

Möjlig orsak 2:

Hälso principer anges i procent av totalt antal tjänster och inte specifika tjänst instanser. Till exempel, före en uppgradering, om ett program har fyra tjänst instanser A, B, C och D, där tjänst D inte är felfritt, men med liten påverkan på programmet. Vi vill ignorera den kända felaktiga tjänsten D under uppgraderingen och ange att parametern *MaxPercentUnhealthyServices* ska vara 25%, förutsatt att bara A, B och C måste vara felfria.

Men under uppgraderingen kan D bli felfritt medan C blir skadad. Uppgraderingen kan ändå lyckas eftersom endast 25% av tjänsterna är felaktiga. Det kan dock leda till oväntade fel på grund av att C inte är i fel tillstånd i stället för D. I den här situationen bör D modelleras som en annan tjänst typ från A, B och C. Eftersom hälso principer har angetts per tjänst typ kan olika tröskelvärden för felaktiga procent gälla för olika tjänster. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Jag angav ingen hälso princip för program uppgradering, men uppgraderingen Miss lyckas under vissa tids gränser som jag aldrig har angett

När det inte finns några hälso principer för uppgraderings förfrågan hämtas de från den *ApplicationManifest.xml* av den aktuella program versionen. Om du till exempel uppgraderar program X från version 1,0 till version 2,0, används program hälso principer som anges i version 1,0. Om en annan hälso princip ska användas för uppgraderingen måste principen anges som en del av API-anropet för program uppgradering. De principer som anges som en del av API-anropet gäller endast under uppgraderingen. När uppgraderingen är klar används de principer som anges i *ApplicationManifest.xml* .

### <a name="incorrect-time-outs-are-specified"></a>Felaktiga tids gränser har angetts

Du kanske har undrat vad som händer när tids gränsen har angetts inkonsekvent. Du kan till exempel ha en *UpgradeTimeout* som är mindre än *UpgradeDomainTimeout*. Svaret är att ett fel returneras. Fel returneras om *UpgradeDomainTimeout* är mindre än summan av *HealthCheckWaitDuration* och *HealthCheckRetryTimeout*, eller om *UpgradeDomainTimeout* är mindre än summan av *HealthCheckWaitDuration* och *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Mina uppgraderingar tar för lång tid

Tiden då uppgraderingen ska slutföras beror på hälso kontrollerna och de angivna tids gränsarna. Hälso kontroller och tids gränser beror på hur lång tid det tar att kopiera, distribuera och stabilisera programmet. Är för aggressiva med timeout kan innebära fler misslyckade uppgraderingar, så vi rekommenderar att du startar försiktigt med längre tids gränser.

Här är en snabb uppdatering av hur tids gränsen interagerar med uppgraderings tiderna:

Uppgraderingar av en uppgraderings domän kan inte slutföras snabbare än *HealthCheckWaitDuration*  +  *HealthCheckStableDuration*.

Uppgraderings fel kan inte inträffa snabbare än *HealthCheckWaitDuration*-  +  *HealthCheckRetryTimeout*.

Uppgraderings tiden för en uppgraderings domän begränsas av *UpgradeDomainTimeout*.  Om *HealthCheckRetryTimeout* och *HealthCheckStableDuration* är både icke-noll och hälso tillståndet för programmet fortsätter att växla fram och tillbaka, så kan uppgraderingen slutligen ta längre tid på *UpgradeDomainTimeout*. *UpgradeDomainTimeout* börjar räkna upp när uppgraderingen för den aktuella uppgraderings domänen börjar.

## <a name="next-steps"></a>Nästa steg

Genom [att uppgradera programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md) går du igenom en program uppgradering med Visual Studio.

Genom [att uppgradera ditt program med hjälp av PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) får du en program uppgradering med PowerShell.

Styr hur programmet uppgraderas med hjälp av [uppgraderings parametrar](service-fabric-application-upgrade-parameters.md).

Gör dina program uppgraderingar kompatibla genom att lära dig hur du använder [Dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att titta på [avancerade ämnen](service-fabric-application-upgrade-advanced.md).