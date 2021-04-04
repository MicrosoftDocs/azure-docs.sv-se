---
title: Uppgradera versionen av ett fristående kluster
description: Uppgradera Azure Service Fabric-koden som kör ett fristående Service Fabric-kluster.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 5831fcffe718e4966c3444a29d35c5bbd55e5f57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842945"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Uppgradera den Service Fabric version som körs i klustret 

För alla moderna system är möjligheten att uppgradera nyckeln till den långsiktiga framgången av produkten. Ett Azure Service Fabric-kluster är en resurs som du äger. I den här artikeln beskrivs hur du uppgraderar den version av Service Fabric som körs i det fristående klustret.

> [!NOTE]
> Kontrol lera att klustret alltid kör en Service Fabric-version som stöds. När Microsoft tillkännager lanseringen av en ny version av Service Fabric, markeras den tidigare versionen för slut för ande av support efter minst 60 dagar från dagen för meddelandet. Nya versioner presenteras [i Service Fabric teamets blogg](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric). Den nya versionen är tillgänglig och kan väljas nu.
>
>

Du kan bara uppgradera klustret till den nya versionen om du använder en konfiguration för produktions-Style-noden där varje Service Fabric nod allokeras på en separat fysisk eller virtuell dator. Om du har ett utvecklings kluster där mer än en Service Fabric nod finns på en fysisk eller virtuell dator måste du återskapa klustret med den nya versionen.

Två distinkta arbets flöden kan uppgradera klustret till den senaste versionen eller en Service Fabric version som stöds. Ett arbets flöde är för kluster som har anslutning för att ladda ned den senaste versionen automatiskt. Det andra arbets flödet är för kluster som inte har någon anslutning för att ladda ned den senaste versionen av Service Fabric.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Aktivera automatisk uppgradering av Service Fabric versionen av klustret
Ange `fabricClusterAutoupgradeEnabled` kluster konfigurationen till *Sant* om du vill att klustret ska hämta uppdateringar av Service Fabric när Microsoft släpper en ny version. Om du vill välja en version av Service Fabric som du vill att klustret ska vara på manuellt, ställer du in `fabricClusterAutoupgradeEnabled` kluster konfigurationen på *falskt*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Uppgradera kluster som har anslutning för att ladda ned den senaste koden och konfigurationen
Använd de här stegen för att uppgradera klustret till en version som stöds om dina klusternoder har Internet anslutning till [Microsoft Download Center](https://download.microsoft.com).

För kluster som har anslutning till [Microsoft Download Center](https://download.microsoft.com)söker Microsoft regelbundet efter tillgänglighet för nya Service Fabric-versioner.

När en ny Service Fabric version är tillgänglig laddas paketet ned lokalt till klustret och tillhandahålls för uppgradering. För att informera kunden om den här nya versionen, visar systemet dessutom en uttrycklig varning om kluster hälsa som liknar följande:

"Den aktuella kluster versionen [version #] stöd upphör [datum]."

När klustret har kört den senaste versionen går varnings meddelandet borta.

När du ser varningen för kluster hälsa uppgraderar du klustret:

1. Anslut till klustret från vilken dator som helst som har administratörs åtkomst till alla datorer som listas som noder i klustret. Datorn som skriptet körs på behöver inte vara en del av klustret.

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Hämta listan med Service Fabric-versioner som du kan uppgradera till.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Du bör få utdata som liknar detta:

    ![Hämta Service Fabric-versioner][getfabversions]
3. Starta en kluster uppgradering till en tillgänglig version med hjälp av Windows PowerShell [-kommandot start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade) .

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Du kan övervaka förloppet för uppgraderingen genom att använda Service Fabric Explorer eller köra följande PowerShell-kommando:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Om klustrets hälso principer inte uppfylls, återställs uppgraderingen. Information om hur du anger anpassade hälso principer för Start-ServiceFabricClusterUpgrade kommandot finns i dokumentationen för [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    När du har åtgärdat problemen som resulterade i återställningen, initiera uppgraderingen igen genom att följa samma steg som beskrivs ovan.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Uppgradera kluster som inte har *någon anslutning* för att ladda ned den senaste koden och konfigurationen
Använd de här stegen för att uppgradera klustret till en version som stöds om dina klusternoder inte har Internet anslutning till [Microsoft Download Center](https://download.microsoft.com).

> [!NOTE]
> Om du kör ett kluster som inte är anslutet till Internet, måste du övervaka [Service Fabric teamets blogg](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) för att lära dig om nya versioner. Systemet visar inte en hälso varning från klustret som varnar dig om nya versioner.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatisk etablering eller manuell etablering
Om du vill aktivera automatisk hämtning och registrering för den senaste kod versionen konfigurerar du Service Fabric Update-tjänsten. Instruktioner finns i *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* i det [fristående paketet](service-fabric-cluster-standalone-package-contents.md).

Följ de här instruktionerna för den manuella processen.

Ändra kluster konfigurationen och ange följande egenskap till *false* innan du påbörjar en konfigurations uppgradering:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Information om användning finns i PowerShell [-kommandot start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) . Se till att uppdatera "clusterConfigurationVersion" i JSON innan du startar konfigurations uppgraderingen.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Arbets flöde för kluster uppgradering

1. Kör [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) från en av noderna i klustret och notera *TargetCodeVersion*.

2. Kör följande från en Internet-ansluten dator om du vill visa alla uppgraderings kompatibla versioner med den aktuella versionen och ladda ned motsvarande paket från de associerade nedladdnings länkarna:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Anslut till klustret från vilken dator som helst som har administratörs åtkomst till alla datorer som listas som noder i klustret. Datorn som skriptet körs på behöver inte vara en del av klustret.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Kopiera det hämtade paketet till klustrets avbildnings arkiv.

5. Registrera det kopierade paketet.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Starta en kluster uppgradering till en tillgänglig version.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer, eller så kan du köra följande PowerShell-kommando:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Om klustrets hälso principer inte uppfylls, återställs uppgraderingen. Information om hur du anger anpassade hälso principer för Start-ServiceFabricClusterUpgrade kommandot finns i dokumentationen för [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    När du har åtgärdat problemen som resulterade i återställningen, initiera uppgraderingen igen genom att följa samma steg som beskrivs ovan.

## <a name="next-steps"></a>Nästa steg
* [Uppgradera konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md)
* Anpassa några [Service Fabric kluster inställningar](service-fabric-cluster-fabric-settings.md).
* [Skala ditt kluster in och ut](service-fabric-cluster-scale-in-out.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
