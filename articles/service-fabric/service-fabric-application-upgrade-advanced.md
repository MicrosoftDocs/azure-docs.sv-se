---
title: Avsnitt om avancerad program uppgradering
description: I den här artikeln beskrivs några avancerade ämnen som rör uppgradering av ett Service Fabric-program.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6604300328f2d243077ba341a9028221438dce9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98792056"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Service Fabric program uppgradering: avancerade ämnen

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Lägga till eller ta bort tjänst typer under en program uppgradering

Om en ny tjänst typ läggs till i ett publicerat program som en del av en uppgradering, läggs den nya tjänst typen till i det distribuerade programmet. Sådan uppgradering påverkar inte någon av tjänst instanserna som redan tillhör programmet, men en instans av tjänst typen som lades till måste skapas för att den nya tjänst typen ska vara aktiv (se [New-ServiceFabricService](/powershell/module/servicefabric/new-servicefabricservice)).

På samma sätt kan tjänst typer tas bort från ett program som en del av en uppgradering. Alla tjänst instanser av tjänst typen att ta bort måste dock tas bort innan du fortsätter med uppgraderingen (se [Remove-ServiceFabricService](/powershell/module/servicefabric/remove-servicefabricservice)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>Undvik anslutning vid tillstånds lös planerat drift stopp för tjänsten

För tillstånds lösa instansen av tillstånds lösa instansen, till exempel program/kluster-uppgradering eller nod-inaktive ring, kan anslutningar tas bort eftersom den exponerade slut punkten tas bort när instansen stängs av, vilket resulterar i tvingande anslutnings

Undvik detta genom att konfigurera funktionen *RequestDrain* genom att lägga till en *instans stängnings fördröjning* i tjänst konfigurationen för att tillåta att befintliga begär Anden inifrån klustret töms på exponerade slut punkter. Detta uppnås eftersom den slut punkt som annonseras av den tillstånds lösa instansen tas bort *innan* fördröjningen börjar innan instansen stängs. Den här fördröjningen gör att befintliga begär Anden kan tömmas innan instansen faktiskt slutar fungera. Klienter meddelas om slut punkts ändringen av en callback-funktion vid tidpunkten för start av fördröjningen, så att de kan lösa slut punkten igen och undvika att skicka nya begär anden till instansen som går ur drift. Dessa begär Anden kan härstamma från klienter som använder [omvänd proxy](./service-fabric-reverseproxy.md) eller använder tjänst slut punktens lösnings-API: er med meddelande modellen ([ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription)) för att uppdatera slut punkterna.

### <a name="service-configuration"></a>Tjänstkonfiguration

Det finns flera sätt att konfigurera fördröjningen på tjänst sidan.

 * **När du skapar en ny tjänst** anger du en `-InstanceCloseDelayDuration` :

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>
    ```

 * **När du definierar tjänsten i avsnittet standarder i applikations manifestet** tilldelar du `InstanceCloseDelayDurationSeconds` egenskapen:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **När du uppdaterar en befintlig tjänst** anger du en `-InstanceCloseDelayDuration` :

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

 * **När du skapar eller uppdaterar en befintlig tjänst via arm-mallen** anger du `InstanceCloseDelayDuration` värdet (lägsta API-version som stöds: 2019-11-01-för hands version):

    ```ARM template to define InstanceCloseDelayDuration of 30seconds
    {
      "apiVersion": "2019-11-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications/services",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
      "location": "[variables('clusterLocation')]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
      ],
      "properties": {
        "provisioningState": "Default",
        "serviceKind": "Stateless",
        "serviceTypeName": "[parameters('serviceTypeName')]",
        "instanceCount": "-1",
        "partitionDescription": {
          "partitionScheme": "Singleton"
        },
        "serviceLoadMetrics": [],
        "servicePlacementPolicies": [],
        "defaultMoveCost": "",
        "instanceCloseDelayDuration": "00:00:30.0"
      }
    }
    ```

### <a name="client-configuration"></a>Klientkonfiguration

Om du vill få ett meddelande när en slut punkt har ändrats bör klienter registrera en motringning se [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
Ändrings meddelandet är en indikation på att slut punkterna har ändrats, att klienten måste matcha slut punkterna igen och inte använda slut punkterna som inte annonseras längre, eftersom de snart går nedåt.

### <a name="optional-upgrade-overrides"></a>Valfria uppgraderings åsidosättningar

Förutom att ställa in försenade varaktigheter per tjänst kan du också åsidosätta fördröjningen vid uppgradering av program/kluster med samma ( `InstanceCloseDelayDurationSec` ) alternativ:

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

Den åsidosatta fördröjnings tiden gäller bara för den anropade uppgraderings instansen och ändrar annars inte enskilda tjänst fördröjnings konfigurationer. Du kan till exempel använda detta för att ange en fördröjning `0` i för att hoppa över förkonfigurerade uppgraderings fördröjningar.

> [!NOTE]
> * Inställningarna för att tömma begär Anden kan inte hindra Azure Load Balancer från att skicka nya begär anden till slut punkter som tas bort från minnet.
> * Ett klagomål som bygger på en klagomåls lösning kommer inte att leda till korrekt tömning av begär Anden, eftersom den utlöser en tjänst upplösning efter ett haveri. Som tidigare beskrivits bör detta i stället förbättras för att prenumerera på aviseringar om ändringar av slut punkten med hjälp av [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
> * Inställningarna gäller inte när uppgraderingen är en effekt som är en När replikerna inte kommer att tas bort under uppgraderingen.
>
>

> [!NOTE]
> Den här funktionen kan konfigureras i befintliga tjänster med hjälp av Update-ServiceFabricService-cmdlet eller ARM-mallen som nämnts ovan, när kluster kod versionen är 7.1.XXX eller högre.
>
>

## <a name="manual-upgrade-mode"></a>Manuellt uppgraderings läge

> [!NOTE]
> Det *övervakade* uppgraderings läget rekommenderas för alla Service Fabric-uppgraderingar.
> Uppgraderings läget för *UnmonitoredManual* bör endast övervägas för misslyckade eller pausade uppgraderingar. 
>
>

I *övervakat* läge använder Service Fabric hälso principer för att säkerställa att programmet är felfritt när uppgraderingen fortskrider. Om hälso principerna överskrids, pausas eller återställs uppgraderingen automatiskt beroende på den angivna *FailureAction*.

I *UnmonitoredManual* -läge har program administratören total kontroll över förloppet av uppgraderingen. Det här läget är användbart när du tillämpar principer för anpassad hälso utvärdering eller utför icke-konventionella uppgraderingar för att kringgå hälso övervakningen helt (t. ex. om programmet redan har data förlust). En uppgradering som körs i det här läget inaktive ras automatiskt när varje UD har slutförts och måste återupptas direkt med hjälp av [Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade). När en uppgradering har pausats och är redo att återupptas av användaren, kommer uppgraderings tillståndet att visa *RollforwardPending* (se [UpgradeState](/dotnet/api/system.fabric.applicationupgradestate)).

Slutligen är *UnmonitoredAuto* -läget användbart för att utföra snabba uppgraderingar vid utveckling eller testning av tjänster eftersom inga användarindata krävs och inga program hälso principer utvärderas.

## <a name="upgrade-with-a-diff-package"></a>Uppgradera med ett diff-paket

I stället för att tillhandahålla ett komplett programpaket kan uppgraderingar också utföras genom att du konfigurerar diff-paket som bara innehåller de uppdaterade kod-/konfigurations-och data paketen tillsammans med det fullständiga applikations manifestet och fullständiga tjänst manifest. Fullständiga programpaket krävs bara för den första installationen av ett program i klustret. Efterföljande uppgraderingar kan antingen vara från kompletta programpaket eller diff-paket.  

Alla referenser i program manifestet eller tjänst manifesten för ett diff-paket som inte går att hitta i programpaketet ersätts automatiskt med den för tillfället etablerade versionen.

Scenarier för att använda ett diff-paket är:

* När du har ett stort programpaket som refererar till flera tjänst MANIFEST filer och/eller flera kod paket, konfigurations paket eller data paket.
* När du har ett distributions system som genererar Bygg-layouten direkt från program Bygg processen. I det här fallet, även om koden inte har ändrats, får nyskapade sammansättningar en annan kontroll summa. Om du använder ett fullständigt applikations paket måste du uppdatera versionen på alla kod paket. Med ett diff-paket kan du bara ange de filer som har ändrats och manifest filen där versionen har ändrats.

När ett program uppgraderas med hjälp av Visual Studio publiceras ett diff-paket automatiskt. Om du vill skapa ett diff-paket manuellt måste applikations manifestet och tjänst manifesten uppdateras, men bara de ändrade paketen ska ingå i det slutliga programpaketet.

Låt oss till exempel börja med följande program (versions nummer som tillhandahålls för enkel förståelse):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Låt oss anta att du bara vill uppdatera kod paketet för Service1 med ett diff-paket. Det uppdaterade programmet har följande versions ändringar:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

I det här fallet uppdaterar du applikations manifestet till 2.0.0 och tjänst manifestet för Service1 för att avspegla kod paket uppdateringen. Mappen för programpaketet har följande struktur:

```text
app1/
  service1/
    code/
```

Du kan med andra ord skapa ett fullständigt programpaket normalt och sedan ta bort alla koder/config/datapaket-mappar som versionen inte har ändrats för.

## <a name="upgrade-application-parameters-independently-of-version"></a>Uppgradera program parametrar oberoende av version

Ibland är det önskvärt att ändra parametrarna för ett Service Fabric program utan att ändra manifest versionen. Detta kan göras bekvämt med hjälp av flaggan **-ApplicationParameter** med cmdleten **Start-ServiceFabricApplicationUpgrade** Azure Service Fabric PowerShell. Anta ett Service Fabric program med följande egenskaper:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Uppgradera nu programmet med cmdleten **Start-ServiceFabricApplicationUpgrade** . I det här exemplet visas en övervakad uppgradering, men en oövervakad uppgradering kan också användas. Om du vill se en fullständig beskrivning av flaggor som har godkänts av denna cmdlet, se [referens för Azure Service Fabric PowerShell-modulen](/powershell/module/servicefabric/start-servicefabricapplicationupgrade#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

När du har uppgraderat kontrollerar du att programmet har de uppdaterade parametrarna och samma version:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Återställa program uppgraderingar

Även om uppgraderingar kan skickas framåt i ett av tre lägen (*övervakad*, *UnmonitoredAuto* eller *UnmonitoredManual*) kan de bara återställas i antingen *UnmonitoredAuto* -eller *UnmonitoredManual* -läge. Att återställa i *UnmonitoredAuto* -läget fungerar på samma sätt som när du följer undantaget att standardvärdet för *UpgradeReplicaSetCheckTimeout* är olika – se [program uppgraderings parametrar](service-fabric-application-upgrade-parameters.md). Återställningen av *UnmonitoredManual* -läget fungerar på samma sätt som bakåt. återställningen inaktive ras automatiskt när du har slutfört varje UD och måste återupptas direkt med hjälp av [Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade) för att fortsätta med återställningen.

Återställningar kan aktive ras automatiskt när hälso principerna för en uppgradering i det *övervakade* läget med en *FailureAction* *återställning* bryter mot (se [program uppgraderings parametrar](service-fabric-application-upgrade-parameters.md)) eller uttryckligen använder [Start-ServiceFabricApplicationRollback](/powershell/module/servicefabric/start-servicefabricapplicationrollback).

Under återställningen kan värdet för *UpgradeReplicaSetCheckTimeout* och läget fortfarande ändras när som helst med hjälp av [Update-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/update-servicefabricapplicationupgrade).

## <a name="next-steps"></a>Nästa steg
Genom [att uppgradera programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md) går du igenom en program uppgradering med Visual Studio.

Genom [att uppgradera ditt program med hjälp av PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) får du en program uppgradering med PowerShell.

Styr hur programmet uppgraderas med hjälp av [uppgraderings parametrar](service-fabric-application-upgrade-parameters.md).

Gör dina program uppgraderingar kompatibla genom att lära dig hur du använder [Dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Åtgärda vanliga problem i program uppgraderingar genom att följa stegen i [Felsöka program uppgraderingar](service-fabric-application-upgrade-troubleshooting.md).
