---
title: Service Fabric program uppgradering
description: Den här artikeln innehåller en introduktion till att uppgradera ett Service Fabric program, inklusive att välja uppgraderings lägen och utföra hälso kontroller.
ms.topic: conceptual
ms.date: 8/5/2020
ms.openlocfilehash: f3fad8d0ede92004706d9a1f4e14353715361b63
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792022"
---
# <a name="service-fabric-application-upgrade"></a>Service Fabric program uppgradering
Ett Azure Service Fabric-program är en samling tjänster. Under en uppgradering jämför Service Fabric det nya [applikations manifestet](service-fabric-application-and-service-manifests.md) med den tidigare versionen och avgör vilka tjänster i programmet som behöver uppdateras. Service Fabric jämför versions numren i tjänst manifesten med versions numren i den tidigare versionen. Tjänsten uppgraderas inte om en tjänst inte har ändrats.

> [!NOTE]
> [ApplicationParameter](/dotnet/api/system.fabric.description.applicationdescription.applicationparameters#System_Fabric_Description_ApplicationDescription_ApplicationParameters)s bevaras inte i en program uppgradering. För att bevara aktuella program parametrar bör användaren hämta parametrarna först och skicka dem till uppgraderings-API-anropet som nedan:
```powershell
$myApplication = Get-ServiceFabricApplication -ApplicationName fabric:/myApplication
$appParamCollection = $myApplication.ApplicationParameters

$applicationParameterMap = @{}
foreach ($pair in $appParamCollection)
{
    $applicationParameterMap.Add($pair.Name, $pair.Value);
}

Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/myApplication -ApplicationTypeVersion 2.0.0 -ApplicationParameter $applicationParameterMap -Monitored -FailureAction Rollback
```

## <a name="rolling-upgrades-overview"></a>Översikt över rullande uppgraderingar
I en uppgradering av rullande program utförs uppgraderingen i steg. I varje steg tillämpas uppgraderingen på en delmängd noder i klustret, som kallas för en uppdaterings domän. Därför förblir programmet tillgängligt under uppgraderingen. Under uppgraderingen kan klustret innehålla en blandning av de gamla och nya versionerna.

Därför måste de två versionerna vidarebefordras och vara bakåtkompatibla. Om de inte är kompatibla ansvarar program administratören för att mellanlagra en uppgradering av flera steg för att upprätthålla tillgänglighet. I en uppgradering med flera steg uppgraderar det första steget till en mellanliggande version av programmet som är kompatibelt med den tidigare versionen. Det andra steget är att uppgradera den slutgiltiga versionen som bryter kompatibiliteten med för uppdaterings versionen, men som är kompatibel med den mellanliggande versionen.

Uppdaterings domäner anges i kluster manifestet när du konfigurerar klustret. Uppdaterings domäner tar inte emot uppdateringar i en viss ordning. En uppdaterings domän är en logisk enhet för distribution av ett program. Med uppdaterings domäner kan tjänsterna hållas kvar vid hög tillgänglighet under en uppgradering.

Uppgraderingar som inte går att rulla är möjliga om uppgraderingen tillämpas på alla noder i klustret, vilket är fallet när programmet bara har en uppdaterings domän. Den här metoden rekommenderas inte eftersom tjänsten slutar fungera och inte är tillgänglig vid tidpunkten för uppgraderingen. Dessutom ger Azure inga garantier när ett kluster har kon figurer ATS med endast en uppdaterings domän.

När uppgraderingen är klar kommer alla tjänster och repliker (instanser) att stanna kvar i samma version-i. e. om uppgraderingen lyckas uppdateras de till den nya versionen. Om uppgraderingen Miss lyckas och återställs, återställs de tillbaka till den tidigare versionen.

## <a name="health-checks-during-upgrades"></a>Hälso kontroller under uppgraderingar
För en uppgradering måste hälso principer anges (eller också kan standardvärden användas). En uppgradering kallas slutförd när alla uppdaterings domäner uppgraderas inom angivna tids gränser och när alla uppdaterings domäner anses vara felfria.  En felfri uppdaterings domän innebär att uppdaterings domänen godkände alla hälso kontroller som anges i hälso principen. En hälso princip kan till exempel kräva att alla tjänster i en program instans måste vara *felfria*, så att hälsan definieras av Service Fabric.

Hälso principer och kontroller under uppgraderingen genom Service Fabric är oberoende för tjänster och program. Det innebär att inga tjänstspecifika tester görs.  Din tjänst kan till exempel ha ett data flödes krav, men Service Fabric saknar information om att kontrol lera data flödet. Se [hälso artiklarna](service-fabric-health-introduction.md) för de kontroller som utförs. I de kontroller som inträffar under en uppgradering ingår tester för om programpaketet kopierades korrekt, om instansen startades och så vidare.

Program hälsan är en agg regering av programmets underordnade entiteter. I korthet utvärderar Service Fabric hälso tillståndet för programmet genom det hälso tillstånd som rapporteras i programmet. Den utvärderar också hälso tillståndet för alla tjänster för programmet på det här sättet. Service Fabric ytterligare utvärderar hälso tillståndet för program tjänsterna genom att aggregera hälso tillståndet för deras underordnade, till exempel tjänst repliken. När program hälso principen är uppfylld kan uppgraderingen fortsätta. Om hälso principen överskrids Miss lyckas program uppgraderingen.

## <a name="upgrade-modes"></a>Uppgraderings lägen
Läget som vi rekommenderar för program uppgradering är det övervakade läget, vilket är det vanligaste läget. Övervakat läge utför uppgraderingen på en uppdaterings domän och om alla hälso kontroller godkänns (enligt den angivna principen), flyttas nästa uppdaterings domän automatiskt.  Om hälso kontrollerna inte fungerar och/eller om tids gränsen uppnås, återställs uppgraderingen för uppdaterings domänen eller så ändras läget till oövervakad manuell. Du kan konfigurera uppgraderingen för att välja ett av dessa två lägen för misslyckade uppgraderingar. 

Oövervakat läge behöver manuellt ingripande efter varje uppgradering på en uppdaterings domän för att kunna starta uppgraderingen på nästa uppdaterings domän. Inga Service Fabric hälso kontroller utförs. Administratören utför hälso-och status kontrollerna innan du påbörjar uppgraderingen i nästa uppdaterings domän.

## <a name="upgrade-default-services"></a>Uppgradera standard tjänster
Vissa standard tjänst parametrar som definieras i [applikations manifestet](service-fabric-application-and-service-manifests.md) kan också uppgraderas som en del av en program uppgradering. Endast de tjänst parametrar som stöder ändras via [Update-ServiceFabricService](/powershell/module/servicefabric/update-servicefabricservice) kan ändras som en del av en uppgradering. Beteendet för att ändra standard tjänsterna under program uppgraderingen är följande:

1. Standard tjänster i det nya applikations manifestet som inte redan finns i klustret skapas.
2. Standard tjänster som finns i båda de föregående och nya applikations manifesten uppdateras. Parametrarna för standard tjänsten i det nya applikations manifestet skriver över parametrarna för den befintliga tjänsten. Program uppgraderingen återställs automatiskt om det inte går att uppdatera en standard tjänst.
3. Standard tjänster som inte finns i det nya applikations manifestet tas bort om de finns i klustret. **Observera att om du tar bort en standard tjänst tas all tjänst status bort och kan inte ångras.**

När en program uppgradering återställs återställs standard tjänst parametrarna till sina gamla värden innan uppgraderingen påbörjas, men borttagna tjänster kan inte återskapas med sitt gamla tillstånd.

> [!TIP]
> Kluster konfigurations inställningen [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) måste vara *sann* för att aktivera reglerna 2) och 3) ovan (standard uppdatering och borttagning av tjänst). Den här funktionen stöds från och med Service Fabric version 5,5.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Uppgradera flera program med HTTPS-slutpunkter
Du måste vara noga med att inte använda **samma port** för olika instanser av samma program när du använder http **S**. Orsaken är att Service Fabric inte kan uppgradera certifikatet för en av program instanserna. Till exempel, om program 1 eller program 2 både vill uppgradera sitt certifikat 1 till cert 2. När uppgraderingen sker kan Service Fabric ha rensat certifikat 1-registreringen med http.sys även om det andra programmet fortfarande använder den. För att förhindra detta identifierar Service Fabric att det redan finns en annan program instans registrerad på porten med certifikatet (på grund av http.sys) och åtgärden kan inte utföras.

Därför stöder Service Fabric inte uppgradering av två olika tjänster med **samma port** i olika program instanser. Med andra ord kan du inte använda samma certifikat på olika tjänster på samma port. Om du behöver ett delat certifikat på samma port måste du se till att tjänsterna placeras på olika datorer med placerings begränsningar. Eller Överväg att använda Service Fabric dynamiska portar om det är möjligt för varje tjänst i varje program instans. 

Om det inte går att uppgradera med https visas ett fel meddelande om att Windows HTTP server API inte stöder flera certifikat för program som delar en port. "

## <a name="application-upgrade-flowchart"></a>Flödes schema för program uppgradering
Flödesschemat som följer detta stycke kan hjälpa dig att förstå uppgraderings processen för ett Service Fabric program. I synnerhet beskriver flödet hur timeout, inklusive *HealthCheckStableDuration*, *HealthCheckRetryTimeout* och *UpgradeHealthCheckInterval*, hjälper till att kontrol lera när uppgraderingen i en uppdaterings domän betraktas som lyckad eller misslyckad.

![Uppgraderings processen för ett Service Fabric program][image]

## <a name="next-steps"></a>Nästa steg
Genom [att uppgradera programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md) går du igenom en program uppgradering med Visual Studio.

Genom [att uppgradera ditt program med hjälp av PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) får du en program uppgradering med PowerShell.

Styr hur programmet uppgraderas med hjälp av [uppgraderings parametrar](service-fabric-application-upgrade-parameters.md).

Gör dina program uppgraderingar kompatibla genom att lära dig hur du använder [Dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att titta på [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i program uppgraderingar genom att följa stegen i [Felsöka program uppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
