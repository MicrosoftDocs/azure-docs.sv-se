---
title: 'Program uppgradering: uppgraderings parametrar'
description: Beskriver parametrar för att uppgradera ett Service Fabric program, inklusive hälso kontroller som ska utföras och principer för att automatiskt återställa uppgraderingen.
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 6b6116bf1188fcf191b2d672e6c698bb3c050e6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018485"
---
# <a name="application-upgrade-parameters"></a>Programuppgraderingsparametrar
I den här artikeln beskrivs de olika parametrarna som gäller under uppgraderingen av ett Azure Service Fabric-program. Parametrarna för program uppgradering styr de tids gränser och hälso kontroller som tillämpas under uppgraderingen och de anger de principer som måste tillämpas när en uppgradering Miss lyckas. Program parametrarna gäller uppgraderingar som använder:
- PowerShell
- Visual Studio
- SFCTL
- [REST](/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Program uppgraderingar initieras via ett av tre uppgraderings lägen som kan väljas. Varje läge har en egen uppsättning program parametrar:
- Övervakas
- Oövervakad automatisk
- Oövervakad manuell

Tillämpliga obligatoriska och valfria parametrar beskrivs i varje avsnitt enligt följande.

## <a name="visual-studio-and-powershell-parameters"></a>Visual Studio-och PowerShell-parametrar

Service Fabric program uppgraderingar med PowerShell använder du kommandot [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) . Uppgraderings läget väljs genom att antingen den **övervakade**, **UnmonitoredAuto** eller **UnmonitoredManual** -parametern skickas till [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

Visual Studio Service Fabric program uppgraderings parametrar anges via dialog rutan uppgraderings inställningar för Visual Studio. Uppgraderings läget för Visual Studio väljs i list rutan **uppgraderings läge** till antingen **övervakad**, **UnmonitoredAuto** eller **UnmonitoredManual**. Mer information finns i [Konfigurera uppgraderingen av ett Service Fabric program i Visual Studio](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Obligatoriska parametrar
(PS = PowerShell, VS = Visual Studio)

| Parameter | Gäller för | Beskrivning |
| --- | --- | --- |
ApplicationName |PS| Namnet på det program som uppgraderas. Exempel: Fabric:/VisualObjects, Fabric:/ClusterMonitor. |
ApplicationTypeVersion|PS|Den version av program typen som uppgraderings målen gäller. |
FailureAction |PS, VS|Tillåtna värden är **ångring**, **manual** och **ogiltigt**. Den kompenserande åtgärden som ska utföras när en *övervakad* uppgradering påträffar övervaknings principen eller hälso policy överträdelser. <br>**Rollback** anger att uppgraderingen ska återställas automatiskt till före uppgraderings versionen. <br>**Manuell** anger att uppgraderingen ska växlas till uppgraderings läget för *UnmonitoredManual* . <br>**Ogiltig** anger att fel åtgärden är ogiltig.|
Övervakas |PS|Anger att uppgraderings läget övervakas. När cmdleten har slutfört en uppgradering för en uppgraderings domän, om hälso tillståndet för uppgraderings domänen och klustret uppfyller hälso principerna som du definierar, Service Fabric uppgraderar nästa uppgraderings domän. Om uppgraderings domänen eller klustret inte uppfyller hälso principerna Miss lyckas uppgraderingen och Service Fabric återställer uppgraderingen för uppgraderings domänen eller återgår till manuellt läge enligt den angivna principen. Detta är det rekommenderade läget för program uppgraderingar i en produktions miljö. |
UpgradeMode | MELLAN | Tillåtna värden **övervakas** (standard), **UnmonitoredAuto** eller **UnmonitoredManual**. Mer information finns i PowerShell-parametrar för varje läge i den här artikeln. |
UnmonitoredAuto | PS | Anger att uppgraderings läget inte är övervakat automatiskt. När Service Fabric uppgraderar en uppgraderings domän Service Fabric uppgraderar nästa uppgraderings domän oberoende av programmets hälso tillstånd. Det här läget rekommenderas inte för produktion och är bara användbart när du utvecklar ett program. |
UnmonitoredManual | PS | Anger att uppgraderings läget är oövervakad manuell. När Service Fabric uppgraderar en uppgraderings domän väntar det tills du uppgraderar nästa uppgraderings domän med hjälp av cmdleten *Resume-ServiceFabricApplicationUpgrade* . |

### <a name="optional-parameters"></a>Valfria parametrar

Parametrarna för hälso utvärdering är valfria. Om villkoren för hälso utvärdering inte anges när en uppgradering startar, använder Service Fabric de program hälso principer som anges i ApplicationManifest.xml i program instansen.

> [!div class="mx-tdBreakAll"]
> | Parameter | Gäller för | Beskrivning |
> | --- | --- | --- |
> | ApplicationParameter |PS, VS| Anger åsidosättningar för program parametrar.<br>PowerShell-programparametrar anges som hash-namn/värdepar. Till exempel @ {"VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1"}.<br>Visual Studio-programparametrar kan anges i dialog rutan publicera Service Fabric program i fältet **program parameter fil** .
> | Bekräfta |PS| Tillåtna värden är **True** och **false**. Du uppmanas att bekräfta innan du kör cmdleten. |
> | ConsiderWarningAsError |PS, VS |Tillåtna värden är **True** och **false**. Standardvärdet är **false**. Behandla varnings hälso händelser för programmet som fel vid utvärdering av programmets hälso tillstånd under uppgraderingen. Som standard utvärderar Service Fabric inte varnings hälso händelser för fel (fel), så uppgraderingen kan fortsätta även om det finns varnings händelser. |
> | DefaultServiceTypeHealthPolicy | PS, VS |Anger hälso principen för den standard tjänst typ som ska användas för den övervakade uppgraderingen i formatet MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. Till exempel innehåller 5, 10, 15 följande värden: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
> | Force | PS, VS | Tillåtna värden är **True** och **false**. Anger att uppgraderings processen hoppar över varnings meddelandet och tvingar fram uppgraderingen även om versions numret inte har ändrats. Detta är användbart för lokal testning, men rekommenderas inte för användning i en produktions miljö eftersom den kräver att den befintliga distributionen tas bort, vilket leder till data förlust. |
> | ForceRestart |PS, VS |Om du uppdaterar en konfiguration eller ett data paket utan att uppdatera tjänst koden startas tjänsten om endast om ForceRestart-egenskapen har angetts till **True**. När uppdateringen är klar meddelar Service Fabric tjänsten om att ett nytt konfigurations paket eller ett nytt data paket är tillgängligt. Tjänsten ansvarar för att tillämpa ändringarna. Om det behövs kan tjänsten starta om sig själv. |
> | HealthCheckRetryTimeoutSec |PS, VS |Den tid (i sekunder) som Service Fabric fortsätter att utföra hälso utvärderingen innan uppgraderingen av uppgraderingen är klar. Standardvärdet är 600 sekunder. Varaktigheten börjar när *HealthCheckWaitDurationSec* har nåtts. I den här *HealthCheckRetryTimeout* kan Service Fabric utföra flera hälso kontroller av programmets hälso tillstånd. Standardvärdet är 10 minuter och bör anpassas korrekt för ditt program. |
> | HealthCheckStableDurationSec |PS, VS |Varaktighet (i sekunder) för att kontrol lera att programmet är stabilt innan du flyttar till nästa uppgraderings domän eller Slutför uppgraderingen. Den här vänte tiden används för att förhindra att ej identifierade hälso tillstånds ändringar direkt efter att hälso kontrollen har utförts. Standardvärdet är 120 sekunder och bör anpassas på lämpligt sätt för ditt program. |
> | HealthCheckWaitDurationSec |PS, VS | Vänte tiden (i sekunder) efter uppgraderingen har avslut ATS på uppgraderings domänen innan Service Fabric utvärderar programmets hälso tillstånd. Den här varaktigheten kan också betraktas som den tid som ett program ska köras innan det kan anses vara felfritt. Om hälso kontrollen lyckas fortsätter uppgraderings processen till nästa uppgraderings domän.  Om hälso kontrollen Miss lyckas, Service Fabric vänta på [UpgradeHealthCheckInterval](./service-fabric-cluster-fabric-settings.md#clustermanager) innan du försöker igen med hälso kontrollen tills *HealthCheckRetryTimeoutSec* har nåtts. Standard och Rekommenderat värde är 0 sekunder. |
> | MaxPercentUnhealthyDeployedApplications|PS, VS |Standard och Rekommenderat värde är 0. Ange det maximala antalet distribuerade program (se [avsnittet hälso tillstånd](service-fabric-health-introduction.md)) som kan vara felfritt innan programmet betraktas som ohälsosamt och inte kan uppgraderas. Den här parametern definierar program hälsan på noden och hjälper till att identifiera problem under uppgraderingen. Normalt kan program varans repliker bli belastningsutjämnad till den andra noden, vilket gör att programmet verkar vara felfritt, vilket gör att uppgraderingen kan fortsätta. Genom att ange en strikt *MaxPercentUnhealthyDeployedApplications* -hälsa kan Service Fabric upptäcka ett problem med programpaketet snabbt och hjälpa till att skapa en snabb uppgradering som inte fungerar. |
> | MaxPercentUnhealthyServices |PS, VS |En parameter till *DefaultServiceTypeHealthPolicy* och *ServiceTypeHealthPolicyMap*. Standard och Rekommenderat värde är 0. Ange det maximala antalet tjänster i program instansen som kan vara ohälsosamt innan programmet betraktas som ohälsosamt och inte kan uppgraderas. |
> | MaxPercentUnhealthyPartitionsPerService|PS, VS |En parameter till *DefaultServiceTypeHealthPolicy* och *ServiceTypeHealthPolicyMap*. Standard och Rekommenderat värde är 0. Ange det maximala antalet partitioner i en tjänst som kan vara ohälsosamt innan tjänsten betraktas som ohälsosam. |
> | MaxPercentUnhealthyReplicasPerPartition|PS, VS |En parameter till *DefaultServiceTypeHealthPolicy* och *ServiceTypeHealthPolicyMap*. Standard och Rekommenderat värde är 0. Ange det maximala antalet repliker i partitionen som kan vara felfria innan partitionen betraktas som ohälsosam. |
> | ServiceTypeHealthPolicyMap | PS, VS | Representerar hälso principen som används för att utvärdera hälso tillståndet för tjänster som tillhör en tjänst typ. Tar en hash-tabell indatatyp i följande format: @ {"ServiceTypeName": "MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices"} till exempel: @ {"ServiceTypeName01" = "5, 10, 5"; "ServiceTypeName02" = "5, 5, 5"} |
> | TimeoutSec | PS, VS | Anger tids gräns tiden i sekunder för åtgärden. |
> | UpgradeDomainTimeoutSec |PS, VS |Maximal tid (i sekunder) för uppgradering av en enda uppgraderings domän. Om tids gränsen uppnås stannar uppgraderingen och fortsätter baserat på inställningen för *FailureAction*. Standardvärdet är aldrig (oändligt) och bör anpassas på lämpligt sätt för ditt program. |
> | UpgradeReplicaSetCheckTimeoutSec |PS, VS |Mätt i sekunder.<br>**Tillstånds lös tjänst**– inom en enda uppgraderings domän, Service Fabric försöker se till att ytterligare instanser av tjänsten är tillgängliga. Om antalet instanser av mål instansen är fler än ett, väntar Service Fabric att fler än en instans ska vara tillgängliga, upp till ett maximalt timeout-värde. Denna timeout anges med hjälp av egenskapen *UpgradeReplicaSetCheckTimeoutSec* . Om timeout-värdet går ut Service Fabric fortsätter med uppgraderingen, oavsett antalet tjänst instanser. Om antalet mål instanser är ett, väntar Service Fabric inte och fortsätter omedelbart med uppgraderingen.<br><br>**Tillstånds känslig tjänst**--inom en enda uppgraderings domän, Service Fabric försöker se till att replik uppsättningen har ett kvorum. Service Fabric väntar på att ett kvorum ska vara tillgängligt, upp till ett maximalt timeout-värde (anges av egenskapen *UpgradeReplicaSetCheckTimeoutSec* ). Om tids gränsen går ut Service Fabric fortsätter uppgraderingen, oavsett kvorum. Den här inställningen anges som aldrig (oändlig) vid löpande vidarebefordran och 1200 sekunder vid återställning. |
> | UpgradeTimeoutSec |PS, VS |En timeout (i sekunder) som gäller för hela uppgraderingen. Om tids gränsen uppnås stoppas uppgraderingen och *FailureAction* utlöses. Standardvärdet är aldrig (oändligt) och bör anpassas på lämpligt sätt för ditt program. |
> | WhatIf | PS | Tillåtna värden är **True** och **false**. Visar vad som skulle hända om cmdleten kördes. Cmdleten körs inte. |

*MaxPercentUnhealthyServices*-, *MaxPercentUnhealthyPartitionsPerService*-och *MaxPercentUnhealthyReplicasPerPartition* -kriteriet kan anges per tjänst typ för en program instans. Genom att ange dessa parametrar per tjänst kan ett program innehålla olika typer av tjänster med olika utvärderings principer. En tillstånds lös tjänst typ kan till exempel ha en *MaxPercentUnhealthyPartitionsPerService* som skiljer sig från en tillstånds känslig motor tjänst typ för en viss program instans.

## <a name="sfctl-parameters"></a>SFCTL-parametrar

Service Fabric program uppgraderingar med hjälp av Service Fabric CLI använder du kommandot [sfctl Application Upgrade](./service-fabric-sfctl-application.md#sfctl-application-upgrade) tillsammans med följande obligatoriska och valfria parametrar.

### <a name="required-parameters"></a>Obligatoriska parametrar

| Parameter | Beskrivning |
| --- | --- |
| program-ID  |ID för programmet som uppgraderas. <br> Detta är vanligt vis det fullständiga namnet på programmet utan URI-schemat "Fabric:". Från och med version 6,0 avgränsas hierarkiska namn med- \~ symbolen. Om program namnet exempelvis är Fabric:/MyApp/APP1, skulle program identiteten vara ' MyApp \~ APP1 ' i 6.0 + och ' MyApp/APP1 ' i tidigare versioner.|
program version |Den version av program typen som uppgraderings målen gäller.|
parametrar  |En JSON-kodad lista över program parameter åsidosättningar som ska tillämpas vid uppgradering av programmet.|

### <a name="optional-parameters"></a>Valfria parametrar

| Parameter | Beskrivning |
| --- | --- |
standard-service-Health-Policy | [JSON](/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) -kodad specifikation av den hälso princip som används som standard för att utvärdera hälso tillståndet för en tjänst typ. Kartan är tom som standard. |
haveri åtgärd | Tillåtna värden är **ångring**, **manual** och **ogiltigt**. Den kompenserande åtgärden som ska utföras när en *övervakad* uppgradering påträffar övervaknings principen eller hälso policy överträdelser. <br>**Rollback** anger att uppgraderingen ska återställas automatiskt till före uppgraderings versionen. <br>**Manuell** anger att uppgraderingen ska växlas till uppgraderings läget för *UnmonitoredManual* . <br>**Ogiltig** anger att fel åtgärden är ogiltig.|
Framtvinga omstart | Om du uppdaterar en konfiguration eller ett data paket utan att uppdatera tjänst koden startas tjänsten om endast om ForceRestart-egenskapen har angetts till **True**. När uppdateringen är klar meddelar Service Fabric tjänsten om att ett nytt konfigurations paket eller ett nytt data paket är tillgängligt. Tjänsten ansvarar för att tillämpa ändringarna. Om det behövs kan tjänsten starta om sig själv. |
hälso kontroll-retry-timeout | Hur lång tid det tar att utvärdera hälso utvärderingen när programmet eller klustret är i fel tillstånd innan *FailureAction* körs. Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. Standard: PT0H10M0S. |
hälso kontroll – stabil varaktighet | Hur lång tid programmet eller klustret måste vara felfritt innan uppgraderingen fortsätter till nästa uppgraderings domän. Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. Standard: PT0H2M0S. |
hälso kontroll-vänte tid | Vänte tiden när en uppgraderings domän har slutförts innan hälso principer tillämpas. Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. Standard: 0.|
Max-ej felfri – appar | Standard och Rekommenderat värde är 0. Ange det maximala antalet distribuerade program (se [avsnittet hälso tillstånd](service-fabric-health-introduction.md)) som kan vara felfritt innan programmet betraktas som ohälsosamt och inte kan uppgraderas. Den här parametern definierar program hälsan på noden och hjälper till att identifiera problem under uppgraderingen. Normalt kan program varans repliker bli belastningsutjämnad till den andra noden, vilket gör att programmet verkar vara felfritt, vilket gör att uppgraderingen kan fortsätta. Genom att ange ett strikt *Max-/hälso skydd – appar* kan Service Fabric upptäcka ett problem med programpaketet snabbt och hjälpa till att skapa en snabb uppgradering som inte fungerar. Representeras som ett tal mellan 0 och 100. |
mode | Tillåtna värden **övervakas**, **UpgradeMode**, **UnmonitoredAuto**, **UnmonitoredManual**. Standardvärdet är **UnmonitoredAuto**. Se avsnittet *obligatoriska parametrar* i Visual Studio och PowerShell för beskrivningar av dessa värden.|
replik-uppsättning-kontrol lera timeout |Mätt i sekunder. <br>**Tillstånds lös tjänst**– inom en enda uppgraderings domän, Service Fabric försöker se till att ytterligare instanser av tjänsten är tillgängliga. Om antalet instanser av mål instansen är fler än ett, väntar Service Fabric att fler än en instans ska vara tillgängliga, upp till ett maximalt timeout-värde. Denna timeout anges med hjälp av egenskapen *replik-set-check-timeout* . Om timeout-värdet går ut Service Fabric fortsätter med uppgraderingen, oavsett antalet tjänst instanser. Om antalet mål instanser är ett, väntar Service Fabric inte och fortsätter omedelbart med uppgraderingen.<br><br>**Tillstånds känslig tjänst**--inom en enda uppgraderings domän, Service Fabric försöker se till att replik uppsättningen har ett kvorum. Service Fabric väntar på att ett kvorum ska vara tillgängligt, upp till ett maximalt timeout-värde (anges av egenskapen *replik-set-check-timeout* ). Om tids gränsen går ut Service Fabric fortsätter uppgraderingen, oavsett kvorum. Den här inställningen anges som aldrig (oändlig) vid löpande vidarebefordran och 1200 sekunder vid återställning. |
tjänstens hälso tillstånd | JSON-kodad karta med tjänst typens hälso princip per tjänst typs namn. Kartan är tom som standard. [JSON-format för parameter.](/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). JSON för "value"-delen innehåller **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService** och **MaxPercentUnhealthyReplicasPerPartition**. Se avsnittet valfria parametrar för Visual Studio och PowerShell för beskrivningar av dessa parametrar.
timeout | Anger tids gräns tiden i sekunder för åtgärden. Standard: 60. |
uppgradering – domän-timeout | Hur lång tid varje uppgraderings domän måste vara slutförd innan *FailureAction* körs. Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. Standardvärdet är aldrig (oändligt) och bör anpassas på lämpligt sätt för ditt program. Standard: P10675199DT02H48M 05.4775807 S. |
uppgradering-timeout | Hur lång tid varje uppgraderings domän måste vara slutförd innan *FailureAction* körs. Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. Standardvärdet är aldrig (oändligt) och bör anpassas på lämpligt sätt för ditt program. Standard: P10675199DT02H48M 05.4775807 S.|
varning-som-fel | Tillåtna värden är **True** och **false**. Standardvärdet är **false**. Kan skickas som en flagga. Behandla varnings hälso händelser för programmet som fel vid utvärdering av programmets hälso tillstånd under uppgraderingen. Som standard utvärderar Service Fabric inte varnings hälso händelser för fel (fel), så uppgraderingen kan fortsätta även om det finns varnings händelser. |

## <a name="next-steps"></a>Nästa steg
Genom [att uppgradera programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md) går du igenom en program uppgradering med Visual Studio.

Genom [att uppgradera ditt program med hjälp av PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) får du en program uppgradering med PowerShell.

Genom [att uppgradera programmet med Service Fabric cli i Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) går du igenom en program uppgradering med Service Fabric cli.

[Uppgradera ditt program med hjälp av Service Fabric Sol förmörkelse-plugin](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Gör dina program uppgraderingar kompatibla genom att lära dig hur du använder [Dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att titta på [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i program uppgraderingar genom att följa stegen i [Felsöka program uppgraderingar](service-fabric-application-upgrade-troubleshooting.md).
