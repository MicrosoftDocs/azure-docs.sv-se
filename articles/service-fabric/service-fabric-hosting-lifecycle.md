---
title: Azure Service Fabric värd för aktivering och inaktive ring av livs cykel
description: Lär dig mer om livs cykeln för ett program och en ServicePack på en nod.
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831830"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Livs cykel för Azure Service Fabric-värd

Den här artikeln innehåller en översikt över händelser som inträffar i Azure Service Fabric när ett program aktive ras på en nod. Den förklarar flera klusterkonfigurationer som styr beteendet.

Innan du fortsätter bör du vara säker på att du förstår koncepten och relationerna som beskrivs i [modellera ett program i Service Fabric][a1]. 

> [!NOTE]
> I den här artikeln används vissa termer på särskilda sätt. Om inget annat anges:
>
> - *Repliken* refererar både till en replik av en tillstånds känslig tjänst och en instans av en tillstånds lös tjänst.
> - *CodePackage* behandlas som likvärdigt med en ServiceHost-process som registrerar en ServiceType. Den är värd för repliker av tjänster för denna ServiceType.
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>Aktivera en ApplicationPackage eller ServicePack

Så här aktiverar du en ApplicationPackage eller ServicePack:

1. Ladda ned ApplicationPackage (till exempel *ApplicationManifest.xml*).
2. Konfigurera en miljö för programmet. Dina steg omfattar till exempel att skapa användare.
3. Börja spåra programmet för inaktive ring.
4. Hämta ServicePack (till exempel *ServiceManifest.xml*, kod, konfigurationsfiler och data paket).
5. Konfigurera en miljö för ServicePack. Dina steg omfattar till exempel att ställa in en brand vägg och allokera portar för slut punkter.
6. Börja spåra ServicePack för inaktive ring.
7. Starta SetupEntryPoint för CodePackages och vänta tills den är klar.
8. Starta MainEntryPoint för CodePackages.

### <a name="servicetype-blocklisting"></a>ServiceType-Blocklisting
`ServiceTypeDisableFailureThreshold` fastställer antalet tillåtna aktiverings-, nedladdnings-och CodePackage-problem. När tröskelvärdet har nåtts schemaläggs ServiceType för Blocklisting. Det första aktiverings problemet, nedladdnings problemet eller CodePackage-kraschen schemalägger ServiceType-Blocklisting. 

`ServiceTypeDisableGraceInterval`Konfigurationen bestämmer Grace-intervallet innan ServiceType är blocklisted på noden. När den här processen spelar ut, görs ett nytt försök att aktivera, ladda ned och CodePackage starta om parallellt. Återförsök innebär till exempel att CodePackage är schemalagd att starta igen efter att kraschen eller Service Fabric kommer att försöka hämta paket igen.

När ServiceType är blocklisted visas ett hälso fel: `'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.`

ServiceType aktive ras igen på noden om någon av följande händelser inträffar:
- Aktiveringen lyckas. Eller om det Miss lyckas uppnås det `ActivationMaxFailureCount` maximala antalet återförsök.
- Nedladdningen lyckades. Eller om det Miss lyckas uppnås det `DeploymentMaxFailureCount` maximala antalet återförsök.
- En CodePackage som har kraschat startar och registrerar ServiceType.

`ActivationMaxFailureCount` och `DeploymentMaxFailureCount` är det maximala antalet försök Service Fabric att aktivera eller hämta ett program på en nod. När det maximala värdet har nåtts aktiverar Service Fabric ServiceType för aktivering igen. 

Dessa tröskelvärden ger tjänsten en annan möjlighet att aktivera. Om aktiveringen lyckas, åtgärdas problemet automatiskt. 

En nyligen placerad eller aktive rad replik kan utlösa en ny aktiverings-eller hämtnings åtgärd. Den här åtgärden kommer att lyckas eller utlösa ServiceType-Blocklisting igen.

> [!NOTE]
> En kraschande CodePackage som inte registrerar en ServiceType påverkar inte ServiceType. Endast en kraschad CodePackage som är värd för en replik påverkar ServiceType.
>

### <a name="codepackage-crash"></a>CodePackage-krasch
När en CodePackage kraschar använder Service Fabric en backoff för att starta den igen. Backoff tillämpas oavsett om CodePackage har registrerat en typ med Service Fabric Runtime.

Värdet för backoff är `Min(RetryTime, ActivationMaxRetryInterval)` . Värdet ökar i fasta, linjära eller exponentiella belopp baserat på `ActivationRetryBackoffExponentiationBase` konfigurations inställningen:

- **Konstant**: om `ActivationRetryBackoffExponentiationBase == 0` , sedan `RetryTime = ActivationRetryBackoffInterval` .
- **Linjärt**: om  `ActivationRetryBackoffExponentiationBase == 0` , sedan `RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval` , där `ContinuousFailureCount` är antalet gånger som en CodePackage kraschar eller inte kan aktive ras.
- **Exponentiell**: `RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)` .
    
Du kan styra beteendet genom att ändra värdena. Om du till exempel vill använda flera snabb omstarts försök kan du använda linjära belopp genom `ActivationRetryBackoffExponentiationBase` att ställa in till `0` och ställa in `ActivationRetryBackoffInterval` på `10` . Så om en CodePackage kraschar blir start intervallet efter 10 sekunder. Om paketet fortsätter att krascha ändras backoff till 20 sekunder, 30 sekunder, 40 sekunder och så vidare tills aktiveringen av CodePackage lyckas eller CodePackage inaktive ras. 
    
Den maximala tid som Service Fabric säkerhets kopie ras (vänte läge väntar) efter att ett haveri regleras av `ActivationMaxRetryInterval` .
    
Om dina CodePackage kraschar och kommer att säkerhets kopie ras, måste de hållas igång för den tids period som anges av `CodePackageContinuousExitFailureResetInterval` . Efter det här intervallet anser Service Fabric att CodePackage är felfritt. Service Fabric skriver sedan över föregående fel hälso rapport som OK och återställer `ContinuousFailureCount` .

### <a name="codepackage-not-registering-servicetype"></a>CodePackage registrerar inte ServiceType
Om en CodePackage hålls igång och förväntas registrera en ServiceType, men inte, genererar Service Fabric en varnings hälso rapport efter `ServiceTypeRegistrationTimeout` . Rapporten visar att ServiceType inte har registrerats inom den förväntade tids perioden.

### <a name="activation-failure"></a>Aktiverings problem
När Service Fabric hittar ett fel under aktiveringen, använder den alltid en linjär backoff, som det gör med en CodePackage-krasch. Aktiverings åtgärden upprättas efter nya försök med följande intervall: (0 + 10 + 20 + 30 + 40) = 100 sekunder. (Det första försöket är omedelbart.) Efter den här sekvensen har aktiveringen inte gjorts om.
    
Den maximala aktiverings backoff kan vara `ActivationMaxRetryInterval` . Återförsök kan vara `ActivationMaxFailureCount` .

### <a name="download-failure"></a>Nedladdnings problem
Service Fabric använder alltid en linjär backoff när ett fel påträffas under hämtningen. Aktiverings åtgärden upprättas efter nya försök med följande intervall: (0 + 10 + 20 + 30 + 40) = 100 sekunder. (Det första försöket är omedelbart.) Efter den här sekvensen provas inte hämtningen. 

Den linjära backoff för en nedladdning är lika med `ContinuousFailureCount`  *  `DeploymentRetryBackoffInterval` . Det maximala backoff kan vara `DeploymentMaxRetryInterval` . Precis som aktiveringar kan hämtnings åtgärder försöka igen för `ActivationMaxFailureCount` gränsen.

> [!NOTE]
> Kom ihåg följande exempel innan du ändrar inställningarna:
>
>* Om CodePackage håller på att krascha och inaktive ras, kommer ServiceType att inaktive ras. Men om aktiverings konfigurationen har en snabb omstart kan CodePackage komma upp några gånger innan ServiceType är i själva verket blocklisted. 
>
>    Anta till exempel att CodePackage kommer upp, registrerar ServiceType med Service Fabric och sedan kraschar. I så fall annulleras perioden när den får en typ registrering `ServiceTypeDisableGraceInterval` . Den här processen kan upprepas tills din CodePackage säkerhets kopie ras till ett värde som är större än `ServiceTypeDisableGraceInterval` perioden. Sedan kommer ServiceType att blocklisted på noden. ServiceType-Blocklisting kan ta längre tid än förväntat.
>
>* När en Service Fabric system måste placera en replik på en nod vid aktiveringar, ställer omkonfigurations agenten till värd under systemet för att aktivera programmet. Den gör en begäran om aktivering var 15: e sekund. (Varaktigheten styrs av `RAPMessageRetryInterval` konfigurations inställningen.) Service Fabric inte veta att ServiceType har varit blocklisted om inte aktiverings åtgärden i värd drift är igång under en längre period än återförsöksintervallet och `ServiceTypeDisableGraceInterval` . 
>
>    Anta till exempel att klustrets `ActivationMaxFailureCount` uppsättning är 5 och `ActivationRetryBackoffInterval` att det är inställt på 1 sekund. I det här fallet skapas aktiverings åtgärden efter intervallet (0 + 1 + 2 + 3 + 4) = 10 sekunder. (Det första försöket är omedelbart.) Efter den här sekvensen ger värdarna ett nytt försök. Aktiverings åtgärden slutförs och kommer inte att göras igen om 15 sekunder. 
>
>    Service Fabric har förbrukat alla tillåtna återförsök inom 15 sekunder. Varje nytt försök från omkonfigurations agenten skapar en ny aktiverings åtgärd i värd under systemet och mönstret upprepas. Därför blocklisted ServiceType aldrig på noden. Eftersom ServiceType inte är blocklisted på noden, kommer repliken inte att flyttas och göras på en annan nod.
> 

## <a name="deactivation"></a>Inaktive ring

När ett ServicePack aktive ras på en nod spåras det för inaktive ring. Inaktive ringen fungerar på två sätt:

- **Periodisk inaktive ring**: vid varje sökning `DeactivationScanInterval` kontrollerar systemet efter tjänst paket som *aldrig* har en replik och markerar dem som kandidater för inaktive ring.
- **ReplicaClose-inaktive ring**: om en replik stängs, hämtas en `DecrementUsageCount` . Antalet är vid 0 när ServicePack inte är värd för någon replik, så att ServicePack är en kandidat för inaktive ring.

Aktiverings läget avgör när kandidater är schemalagda för inaktive ring. I delat läge schemaläggs kandidater för inaktive ring efter `DeactivationGraceInterval` . I exklusivt läge är de schemalagda efter `ExclusiveModeDeactivationGraceInterval` . Om en ny replik placering inkommer mellan dessa tider avbryts inaktive ringen. 

Mer information finns i [exklusivt läge och delat läge][a2].

### <a name="periodic-deactivation"></a>Periodisk inaktive ring
Här följer några exempel på periodisk inaktive ring:

* **Exempel 1**: Låt oss säga att inaktive ras startar en genomsökning vid tid T ( `DeactivationScanInterval` ). Nästa genomsökning kommer att finnas på 2T. Anta att en ServicePacks aktivering skedde vid T + 1. Detta ServicePack är inte värd för en replik, så det måste inaktive ras. 

    För att en kandidat ska kunna inaktive ras måste ServicePack vara värd för ingen replik under minst T-tid. Den kommer att vara tillgänglig för inaktive ring vid 2T + 1. Genomsökningen vid 2T identifierar därför inte detta ServicePack som en kandidat för inaktive ring. 

    Nästa inaktive rings cykel, 3T, kommer att schemalägga detta ServicePack för inaktive ring eftersom paketet har varit i ett läge med ingen replik för tid T.  

* **Exempel 2**: Låt oss säga att ett servicepack aktive ras vid tiden T-1 och att inaktive ras startar en sökning på T. ex. ServicePack är inte värd för en replik. Vid nästa genomsökning kommer 2T att identifieras som en kandidat för inaktive ring, så att det schemaläggs för inaktive ring.  

* **Exempel 3**: Låt oss säga att ett servicepack aktive ras vid t – 1 och att inaktive ras startar en sökning vid t. ServicePack är inte värd för en replik ännu. Nu vid T + 1 placeras en replik. Det innebär att värd hämtar en `IncrementUsageCount` , vilket innebär att en replik skapas. 

    På 2T kommer detta ServicePack inte att schemaläggas för inaktive ring. Eftersom paketet innehåller en replik följer inaktive ringen ReplicaClose Logic, som nästa avsnitt i den här artikeln förklarar.

* **Exempel 4**: anta att ditt servicepack är 10 GB. Eftersom paketet är stort tar det tid att ladda ned på noden. När ett program aktive ras spårar den sin livs cykel. Om `DeactivationScanInterval` är inställt på ett litet värde kanske din servicepack inte har tid att aktivera på noden på grund av den tid det tog att ladda ned. För att lösa det här problemet kan du [Hämta servicepack i förväg på noden][p1] eller öka `DeactivationScanInterval` . 

> [!NOTE]
> En ServicePack kan inaktive ras var som helst mellan ( `DeactivationScanInterval` till 2 * `DeactivationScanInterval` ) + `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 
>

### <a name="replicaclose-deactivation"></a>ReplicaClose-inaktive ring

> [!NOTE]
> Det här avsnittet hänvisar till följande konfigurations inställningar:
> - **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: den tid som ett servicepack har fått som värd för en annan replik om det redan har en replik. 
> - **DeactivationScanInterval**: den minsta tiden som en servicepack har fått som värd för en replik om den *aldrig* har haft någon replik, det vill säga om det inte har använts.
>

Systemet sparar antalet repliker som ett ServicePack innehåller. Om ett ServicePack innehåller en replik och repliken är stängd eller avstängd, får du ett `DecrementUsageCount` . När en replik öppnas får du en `IncrementUsageCount` . 

Minskningen anger att antalet repliker som ServicePack är värd för har minskats av en replik. När antalet sjunker till 0 schemaläggs ServicePack för inaktive ring. Den tid det tar att inaktive ras `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 

Anta till exempel att en minskning sker vid T och att ServicePack är schemalagt att inaktivera vid 2T + X ( `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` ). Under den här tiden `IncrementUsage` avbryts inaktive ringen om värd hämtar en replik.

### <a name="ctrl--c"></a>Ctrl + C
Om ett servicepack skickar `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` och fortfarande inte är värd för en replik, kan inaktive ringen inte avbrytas. CodePackages får en CTRL + C-hanterare. Så nu måste pipelinen för inaktive ringen slutföras för att göra processen nere. 

Under den här tiden går det inte att göra om en ny replik för samma ServicePack försöker komma ut, eftersom processen inte kan övergå från inaktive ringen till aktivering.

## <a name="cluster-configurations"></a>Klusterkonfigurationer

I det här avsnittet visas konfigurationer med standardinställningar som påverkar aktivering och inaktive ring.

### <a name="servicetype"></a>ServiceType
- **ServiceTypeDisableFailureThreshold**: standard: 1. Tröskelvärde för antal försök. När det här tröskelvärdet har uppnåtts meddelas FailoverManager för att inaktivera tjänst typen på noden och testa en annan nod för placering.
- **ServiceTypeDisableGraceInterval**: standard: 30 sekunder. Tidsintervall efter vilket tjänst typen kan inaktive ras.
- **ServiceTypeRegistrationTimeout**: standard: 300 sekunder. Tids gräns för ServiceType att registrera med Service Fabric.

### <a name="activation"></a>Aktivering
- **ActivationRetryBackoffInterval**: standard: 10 sekunder. Backoff-intervall för varje aktiverings haveri.
- **ActivationMaxFailureCount**: standard: 20. Det maximala antal som systemet kommer att försöka köra en misslyckad aktivering innan. 
- **ActivationRetryBackoffExponentiationBase**: standard: 1,5.
- **ActivationMaxRetryInterval**: standard: 3 600 sekunder. Maximalt intervall för backoff-försök för aktivering efter fel.
- **CodePackageContinuousExitFailureResetInterval**: standard: 300 sekunder. Timeout-intervall för att återställa det kontinuerliga avslutnings antalet för CodePackage.

### <a name="download"></a>Ladda ned
- **DeploymentRetryBackoffInterval**: standard: 10. Backoff-intervall för distributions felen.
- **DeploymentMaxRetryInterval**: standard: 3 600 sekunder. Maximalt backoff-intervall för distributionen efter ett haveri.
- **DeploymentMaxFailureCount**: standard: 20. Ett nytt försök att distribuera program görs `DeploymentMaxFailureCount` innan distributionen av programmet på noden.

### <a name="deactivation"></a>Inaktive ring
- **DeactivationScanInterval**: standard: 600 sekunder. Den minsta tiden som krävs för att vara värd för en replik om den aldrig har haft någon replik (det vill säga om det inte används).
- **DeactivationGraceInterval**: standard: 60 sekunder. I en *delad* process modell har tiden som ett servicepack tilldelats till en annan replik när den redan har varit värd för en replik.
- **ExclusiveModeDeactivationGraceInterval**: standard: 1 sekund. I en *exklusiv* process modell har tiden som ett servicepack tilldelats igen en annan replik efter det redan har varit värd för en replik.

## <a name="next-steps"></a>Nästa steg

- [Paketera ett program][a3] och Förbered det för distribution.
- [Distribuera och ta bort program][a4] i PowerShell.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
