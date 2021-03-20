---
title: Konfigurera uppgraderingen av ett Service Fabric program
description: Lär dig hur du konfigurerar inställningarna för att uppgradera ett Service Fabric program med hjälp av Microsoft Visual Studio.
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: 11df0387db07669ebacdba2e3d850e32903da133
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96573688"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Konfigurera uppgraderingen av ett Service Fabric program i Visual Studio
Visual Studio Tools för Azure Service Fabric ger uppgraderings stöd för publicering till lokala eller fjärranslutna kluster. Det finns tre scenarier där du vill uppgradera programmet till en nyare version i stället för att ersätta programmet under testning och fel sökning:

* Program data går inte förlorade under uppgraderingen.
* Tillgängligheten är hög så att det inte uppstår några avbrott i tjänsten under uppgraderingen, om det finns tillräckligt många tjänst instanser i uppgraderings domäner.
* Tester kan köras mot ett program medan det uppgraderas.

## <a name="parameters-needed-to-upgrade"></a>Parametrar som behövs för att uppgradera
Du kan välja mellan två typer av distribution: regelbunden eller uppgradering. En vanlig distribution raderar eventuell tidigare distributions information och data i klustret, medan en uppgraderings distribution bevarar den. När du uppgraderar ett Service Fabric-program i Visual Studio måste du ange parametrar för program uppgradering och principer för hälso kontroll. Parametrarna för program uppgradering hjälper till att kontrol lera uppgraderingen, medan hälso kontroll principerna avgör om uppgraderingen lyckades. Se [Service Fabric program uppgradering: uppgraderings parametrar](service-fabric-application-upgrade-parameters.md) för mer information.

Det finns tre uppgraderings lägen: *övervakad*, *UnmonitoredAuto* och *UnmonitoredManual*.

* En övervakad uppgradering automatiserar uppgraderings-och program hälso kontrollen.
* En UnmonitoredAuto-uppgradering automatiserar uppgraderingen, men hoppar över program hälso kontrollen.
* När du gör en UnmonitoredManual-uppgradering måste du uppgradera varje uppgraderings domän manuellt.

Varje uppgraderings läge kräver olika uppsättningar parametrar. Mer information om tillgängliga uppgraderings alternativ finns i [parametrarna för program uppgradering](service-fabric-application-upgrade-parameters.md) .

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Uppgradera ett Service Fabric-program i Visual Studio
Om du använder Visual Studio-Service Fabric verktyg för att uppgradera ett Service Fabric program kan du ange en publicerings process som ska uppgraderas i stället för en vanlig distribution genom att markera kryss rutan **uppgradera programmet** .

### <a name="to-configure-the-upgrade-parameters"></a>Konfigurera uppgraderings parametrarna
1. Klicka på knappen **Inställningar** bredvid kryss rutan. Dialog rutan **Redigera uppgraderings parametrar** visas. Dialog rutan **Redigera uppgraderings parametrar** stöder de övervakade, UnmonitoredAuto och UnmonitoredManual uppgraderings lägena.
2. Välj det uppgraderings läge som du vill använda och fyll sedan i parameter rutnätet.

    Varje parameter har standardvärden. Den valfria parametern *DefaultServiceTypeHealthPolicy* tar en hash-tabell-Indatatyp. Här är ett exempel på hash-tabellens indataformat för *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* är en annan valfri parameter som tar in en hash-tabell i följande format:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Här är ett exempel på verkliga livet:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Om du väljer UnmonitoredManual uppgraderings läge måste du starta en PowerShell-konsol manuellt för att fortsätta och slutföra uppgraderings processen. Läs mer i [Service Fabric program uppgradering: avancerade ämnen](service-fabric-application-upgrade-advanced.md) för att lära dig hur manuell uppgradering fungerar.

## <a name="upgrade-an-application-by-using-powershell"></a>Uppgradera ett program med hjälp av PowerShell
Du kan använda PowerShell-cmdletar för att uppgradera ett Service Fabric-program. Mer information finns i [självstudien om Service Fabric program uppgradering](service-fabric-application-upgrade-tutorial.md) och [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) .

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Ange en hälso kontroll princip i program manifest filen
Varje tjänst i ett Service Fabric program kan ha egna hälso princip parametrar som åsidosätter standardvärdena. Du kan ange dessa parameter värden i program manifest filen.

I följande exempel visas hur du tillämpar en unik hälso kontroll princip för varje tjänst i applikations manifestet.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Nästa steg
Mer information om hur du uppgraderar ett program finns i [uppgradera ett program med Visual Studio](service-fabric-application-upgrade-tutorial.md).
