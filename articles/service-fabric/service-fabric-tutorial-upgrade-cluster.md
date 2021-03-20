---
title: Uppgradera Service Fabric runtime i Azure
description: I den här guiden får du lära dig hur du använder PowerShell och uppgraderar körningen för ett Service Fabric-kluster med Azure som värd.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 23b3aabf8e991e512ef9a5c07d725c3084ea7f83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86244743"
---
# <a name="tutorial-upgrade-the-runtime-of-a-service-fabric-cluster-in-azure"></a>Självstudie: Uppgradera körningen av ett Service Fabric-kluster i Azure

Den här självstudien är del fyra i en serie och visar hur du uppgraderar Service Fabric runtime på ett Azure Service Fabric-kluster. Den här själv studie kursen är skriven för Service Fabric kluster som körs på Azure och som inte gäller för fristående Service Fabric-kluster.

> [!WARNING]
> För den delen av kursen krävs PowerShell. Stöd för uppgradering av klusterkörning finns inte i Azure CLI-verktygen ännu. Ett kluster kan också uppgraderas på portalen. Mer information finns i [Uppgradera till ett Azure Service Fabric-kluster](service-fabric-cluster-upgrade.md).

Om klustret redan kör den senaste Service Fabric körningen behöver du inte göra det här steget. Den här artikeln kan dock användas för att installera alla stödda körningar på ett Azure Service Fabric-kluster.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * läser klusterversionen
> * ställer in klusterversionen.

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i Azure med hjälp av en mall
> * [Övervaka ett kluster](service-fabric-tutorial-monitor-cluster.md)
> * [Skala in eller ut ett kluster](service-fabric-tutorial-scale-cluster.md)
> * uppgradera körningen för ett kluster
> * [Ta bort ett kluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installera [Azure PowerShell](/powershell/azure/install-az-ps) eller [Azure CLI](/cli/azure/install-azure-cli).
* Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i Azure
* Konfigurera en Windows-utvecklingsmiljö. Installera [Visual Studio 2019](https://www.visualstudio.com) och **Azure-utveckling**, **ASP.net och webb utveckling**, och **.net Core plattforms oberoende utvecklings** arbets belastningar.  Konfigurera sedan en [.NET-utvecklingsmiljö](service-fabric-get-started.md).

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på ditt Azure-konto och välj din prenumeration innan du kör Azure-kommandon.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>Hämta körningsversion

När du har anslutit till Azure, valt den prenumeration som innehåller Service Fabric klustret, kan du hämta kör tids versionen av klustret.

```powershell
Get-AzServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

Eller så får du bara en lista över alla kluster i din prenumeration med följande exempel:

```powershell
Get-AzServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

Notera värdet för **ClusterCodeVersion**. Det här värdet används i nästa avsnitt.

## <a name="upgrade-the-runtime"></a>Uppgradera körningsmiljön

Använd värdet för **ClusterCodeVersion** från föregående avsnitt tillsammans med cdmlet `Get-ServiceFabricRuntimeUpgradeVersion` för att identifiera vilka versioner du kan uppgradera till. Denna cmdlet kan endast köras från en dator som är ansluten till internet. Om du till exempel vill se vilka körningsversioner du kan uppgradera till från version `5.7.198.9494` använder du följande kommando:

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

Med en versionslista kan du instruera Azure Service Fabric-klustret att uppgradera till en nyare körning. Om till exempel version `6.0.219.9494` är tillgänglig använder du följande kommando för att uppgradera klustret.

```powershell
Set-AzServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> Uppgraderingen av klusterkörningen kan ta lång tid att slutföra. PowerShell blockeras när uppgraderingen körs. Du kan använda en annan PowerShell-session för att kontrollera uppgraderingens status.

Status för uppgraderingen kan övervakas med antingen PowerShell eller Azure Service Fabrix CLI (sfctl).

Anslut först till klustret med TLS/SSL-certifikatet som skapades i den första delen av självstudien. Använd cdmlet `Connect-ServiceFabricCluster` eller `sfctl cluster upgrade-status`.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```console
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Använd sedan `Get-ServiceFabricClusterUpgrade` eller `sfctl cluster upgrade-status` för att visa statusen. Något som liknar följande resultat visas.

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```console
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Hämta versionen av klusterkörningen
> * Uppgradera körningstiden för kluster
> * Övervaka uppgraderingen

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Ta bort ett kluster](service-fabric-tutorial-delete-cluster.md)
