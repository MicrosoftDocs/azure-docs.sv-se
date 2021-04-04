---
title: Uppgradera konfigurationen av ett fristående kluster
description: Lär dig hur du uppgraderar konfigurationen som kör ett fristående Service Fabric-kluster.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 3cb1d40f5b32415588d3fd5a108967cfb4c0e534
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842622"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Uppgradera konfigurationen av ett fristående kluster 

För alla moderna system är möjligheten att uppgradera nyckeln till den långsiktiga framgången av produkten. Ett Azure Service Fabric-kluster är en resurs som du äger. I den här artikeln beskrivs hur du uppgraderar konfigurations inställningarna för det fristående Service Fabric klustret.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Anpassa kluster inställningarna i ClusterConfig.jspå filen
Fristående kluster konfigureras via *ClusterConfig.jsi* filen. Läs mer om de olika inställningarna i [konfigurations inställningar för ett fristående Windows-kluster](service-fabric-cluster-manifest.md).

Du kan lägga till, uppdatera eller ta bort inställningar i `fabricSettings` avsnittet i avsnittet [kluster egenskaper](./service-fabric-cluster-manifest.md#cluster-properties) i *ClusterConfig.jspå*. 

Följande JSON lägger till exempel till en ny inställning *MaxDiskQuotaInMB* i avsnittet *diagnostik* under `fabricSettings` :

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

När du har ändrat inställningarna i ClusterConfig.jspå filen [testar du kluster konfigurationen](#test-the-cluster-configuration) och [uppgraderar sedan kluster konfigurationen](#upgrade-the-cluster-configuration) för att tillämpa inställningarna på klustret. 

## <a name="test-the-cluster-configuration"></a>Testa kluster konfigurationen
Innan du påbörjar konfigurations uppgraderingen kan du testa den nya kluster konfigurations-JSON genom att köra följande PowerShell-skript i det fristående paketet:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Eller Använd det här skriptet:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Vissa konfigurationer kan inte uppgraderas, till exempel slut punkter, kluster namn, nod-IP osv. Den nya kluster konfigurations-JSON testas mot den gamla och genererar fel i PowerShell-fönstret om ett problem uppstår.

## <a name="upgrade-the-cluster-configuration"></a>Uppgradera kluster konfigurationen
Uppgradera kluster konfigurations uppgraderingen genom att köra [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Konfigurations uppgraderingen bearbetas uppgraderings domänen av uppgraderings domänen.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Uppgradera kluster certifikat konfiguration
Ett kluster certifikat används för autentisering mellan klusternoder. Certifikat förnyelsen bör utföras med extra försiktighet eftersom fel blockerar kommunikationen mellan klusternoder.

Fyra alternativ stöds:  

* Uppgradering av enskilt certifikat: uppgraderings vägen är certifikat A (primär)-> certifikat B (primär)-> certifikat C (primär)->...

* Dubbel certifikat uppgradering: uppgraderings Sök vägen är certifikat A (primär)-> certifikat A (primär) och B (sekundär)-> certifikat B (primär)-> certifikat B (primär) och C (sekundär)-> certifikat C (primär)->....

* Uppgradering av certifikat typ: tumavtryck-baserad certifikat konfiguration <-> CommonName-baserad certifikat konfiguration. Till exempel certifikatets tumavtryck A (primärt) och tumavtryck B (sekundär)-> certifikat CommonName C.

* Tumavtryck för certifikat utfärdare: uppgraderings Sök vägen är certifikat CN = A, IssuerThumbprint = IT1 (primär)-> certifikat CN = A, IssuerThumbprint = IT1, IT2 (primär)-> certifikat CN = A, IssuerThumbprint = IT2 (primär).


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du anpassar vissa [Service Fabric kluster inställningar](service-fabric-cluster-fabric-settings.md).
* Lär dig hur du [skalar upp och ut ditt kluster](service-fabric-cluster-scale-in-out.md).
* Lär dig mer om [program uppgraderingar](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
