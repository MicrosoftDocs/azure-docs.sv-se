---
title: Skapa ett fristående Azure Service Fabric-kluster
description: Skapa ett Azure Service Fabric-kluster på valfri dator (fysisk eller virtuell) som kör Windows Server, oavsett om det är lokalt eller i valfritt moln.
ms.topic: conceptual
ms.date: 2/21/2019
ms.openlocfilehash: 41af655be07ccae2b66e75f5bfe87629cdb54924
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785692"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Skapa ett fristående kluster som körs på Windows Server
Du kan använda Azure Service Fabric för att skapa Service Fabric-kluster på alla virtuella datorer eller datorer som kör Windows Server. Det innebär att du kan distribuera och köra Service Fabric program i vilken miljö som helst som innehåller en uppsättning anslutna Windows Server-datorer, vara den lokalt eller med någon annan moln leverantör. Service Fabric innehåller ett installations paket för att skapa Service Fabric-kluster som kallas fristående Windows Server-paket. Traditionella Service Fabric kluster i Azure är tillgängliga som en hanterad tjänst, medan fristående Service Fabric kluster är självbetjäning. Mer information om skillnaderna finns i [jämföra Azure och fristående Service Fabric kluster](./service-fabric-deploy-anywhere.md).

Den här artikeln vägleder dig genom stegen för att skapa ett Service Fabric fristående kluster.

> [!NOTE]
> Det fristående Windows Server-paketet är kommersiellt tillgängligt utan kostnad och kan användas för produktions distributioner. Det här paketet kan innehålla nya Service Fabric-funktioner som finns i "för hands version". Rulla ned till "för[hands versions funktioner som ingår i det här paketet](#previewfeatures_anchor)". i avsnittet om förhands gransknings funktionerna. Du kan [Ladda ned en kopia av licens avtalet](https://go.microsoft.com/fwlink/?LinkID=733084) nu.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Få stöd för Service Fabric för Windows Server-paket
* Fråga communityn om Service Fabric fristående paketet för Windows Server på [sidan Microsoft Q&en fråga för Azure-Service Fabric](/answers/topics/azure-service-fabric.html).
* Öppna en biljett för [professionell support för Service Fabric](https://support.microsoft.com/oas/default.aspx?prid=16146).  Lär dig mer om Professional support från Microsoft [här](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Du kan också få support för det här paketet som en del av [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).
* Mer information finns i [Support alternativ för Azure Service Fabric](./service-fabric-support.md).
* Om du vill samla in loggar i support syfte kör du [Service Fabric fristående logg insamlare](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Ladda ned Service Fabric för Windows Server-paketet
Om du vill skapa klustret använder du Service Fabric för Windows Server-paketet (Windows Server 2012 R2 och senare). <br>
[Hämta länk Service Fabric fristående paket – Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

Hitta information om paketets innehåll [här](service-fabric-cluster-standalone-package-contents.md).

Service Fabric Runtime-paketet hämtas automatiskt när klustret skapas. Om du distribuerar från en dator som inte är ansluten till Internet laddar du ned Runtime-paketet out-of-band härifrån: <br>
[Hämta länk Service Fabric runtime – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Hitta konfigurations exempel för fristående kluster på: <br>
[Konfigurations exempel för fristående kluster](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Skapa klustret
Flera exempelkonfigurationsfiler för kluster installeras med konfigurationspaketet. *ClusterConfig.Unsecure.DevCluster.json* är den enklaste klusterkonfigurationen: ett oskyddat kluster med tre noder som körs på en enskild dator.  Andra konfigurationsfiler beskriver kluster med en eller flera datorer som skyddas med X.509-certifikat eller Windows-säkerhet.  Du behöver inte ändra några av standardinställningarna i den här självstudien, men titta igenom konfigurationsfilen så att du bekantar dig med inställningarna.  I avsnittet **Noder** beskrivs de tre noderna i klustret: namn, IP-adress, [nodtyp, feldomän och uppgraderingsdomän](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  I avsnittet **Egenskaper** definieras [säkerhet, tillförlitlighetsnivå, diagnostiksamling och nodtyper](service-fabric-cluster-manifest.md#cluster-properties) för klustret.

Klustret som skapades i den här artikeln är inte säkert.  Vem som helst kan ansluta anonymt och utföra hanteringsåtgärder, så produktionskluster bör alltid skyddas med X.509-certifikat eller Windows-säkerhet.  Säkerhetsfunktioner konfigureras bara när kluster skapas, och du kan inte aktivera någon säkerhet i efterhand. Uppdatera konfigurations filen aktivera [certifikat säkerhet](service-fabric-windows-cluster-x509-security.md) eller [Windows-säkerhet](service-fabric-windows-cluster-windows-security.md). Mer information om klustersäkerheten med Service Fabric finns i avsnittet om hur du [skyddar ett kluster](service-fabric-cluster-security.md).

### <a name="step-1-create-the-cluster"></a>Steg 1: skapa klustret

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Scenario A: skapa ett oskyddat lokalt utvecklings kluster
Service Fabric kan distribueras till ett utvecklings kluster med en enda dator med hjälp av *ClusterConfig.Unsecure.DevCluster.jspå* en fil som ingår i [exempel](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Packa upp det fristående paketet på datorn, kopiera exempel konfigurations filen till den lokala datorn och kör sedan *CreateServiceFabricCluster.ps1* -skriptet via en administratörs-PowerShell-session, från mappen för det fristående paketet.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Se avsnittet miljö konfiguration i [planera och Förbered kluster distributionen](service-fabric-cluster-standalone-deployment-preparation.md) för information om fel sökning.

Om du är klar med utvecklings scenarier kan du ta bort Service Fabric-klustret från datorn genom att följa stegen i avsnittet "[ta bort ett kluster](#removecluster_anchor)". 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Scenario B: skapa ett kluster med flera datorer
När du har gått igenom planerings-och förberedelse stegen som beskrivs i [planera och förbereda kluster distributionen](service-fabric-cluster-standalone-deployment-preparation.md), är du redo att skapa ditt produktions kluster med hjälp av kluster konfigurations filen.

Klusteradministratören som distribuerar och konfigurerar klustret måste ha administratörsbehörighet på datorn. Du kan inte installera Service Fabric på en domänkontrollant.

1. Skriptet *TestConfiguration.ps1* i det fristående paketet används för att analysera bästa praxis och för att kontrollera om ett kluster kan distribueras i en viss miljö. [Distributionsförberedelser](service-fabric-cluster-standalone-deployment-preparation.md) visar krav och förutsättningar. Kör skriptet för att kontrollera om du kan skapa utvecklingsklustret:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Du bör se utdata som liknar följande. Om det nedre fältet "överfört" returneras som "true", så har Sanity kontroller körts och klustret verkar vara distribuerat baserat på inkonfigurations konfigurationen.

    ```powershell
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Skapa klustret: kör *CreateServiceFabricCluster.ps1* -skriptet för att distribuera Service Fabric-klustret på varje dator i konfigurationen. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Distributionsspårningar skrivs till den virtuella dator där du körde PowerShell-skriptet CreateServiceFabricCluster.ps1. Dessa finns i undermappen DeploymentTraces i katalogen som skriptet kördes från. Om du vill se ifall Service Fabric distribuerades korrekt till en dator, letar du reda på de installerade filerna i katalogen FabricDataRoot enligt anvisningarna i klusterkonfigurationsfilens FabricSettings-avsnitt (som standard c:\ProgramData\SF). Dessutom kan man se att processerna FabricHost.exe och Fabric.exe körs i Aktivitetshanteraren.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Scenario C: skapa ett frånkopplat kluster (Internet-frånkopplat)
Service Fabric Runtime-paketet hämtas automatiskt när klustret skapas. När du distribuerar ett kluster till datorer som inte är anslutna till Internet måste du ladda ned Service Fabric Runtime-paketet separat och ange sökvägen till det när klustret skapas.
Runtime-paketet kan laddas ned separat från en annan dator som är ansluten till Internet vid [nedladdnings länk-Service Fabric runtime-Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Kopiera Runtime-paketet till den plats där du distribuerar det frånkopplade klustret från och skapa klustret genom `CreateServiceFabricCluster.ps1` att köra med den `-FabricRuntimePackagePath` parameter som anges, som i det här exemplet: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.jspå* och *.\MicrosoftAzureServiceFabric.cab* är Sök vägarna till kluster konfigurationen och filen Runtime. cab.

### <a name="step-2-connect-to-the-cluster"></a>Steg 2: Anslut till klustret
Anslut till klustret för att kontrol lera att klustret körs och är tillgängligt. ServiceFabric PowerShell-modulen installeras med runtime.  Du kan ansluta till klustret från en av klusternoderna eller från en fjärrdator med Service Fabric Runtime.  Cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) upprättar en anslutning till klustret.

Kör följande PowerShell-kommando för att ansluta till ett oskyddat kluster:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Exempel:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.234:19000
```

Fler exempel på hur du ansluter till ett kluster finns i [Ansluta till ett säkert kluster](service-fabric-connect-to-secure-cluster.md). När du har anslutit till klustret använder du cmdleten [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) för att visa en lista över noder i klustret och statusinformation för varje nod. **HealthState** bör vara *OK* för varje nod.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Steg 3: visualisera klustret med hjälp av Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) är ett bra verktyg för att visualisera klustret och hantera program.  Service Fabric Explorer är en tjänst som körs i klustret och som du kommer åt via en webbläsare genom att gå till `http://localhost:19080/Explorer` .

Instrumentpanelen för klustret innehåller en översikt över klustret, inklusive en sammanfattning av program- och nodhälsan. Nodvyn visar klustrets fysiska layout. För en viss nod kan du inspektera vilka program som har kod distribuerad på noden.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Lägga till och ta bort noder
Du kan lägga till eller ta bort noder i det fristående Service Fabric-klustret när ditt företag behöver ändras. Se [Lägga till eller ta bort noder i ett fristående Service Fabric-kluster](service-fabric-cluster-windows-server-add-remove-nodes.md) för detaljerade anvisningar.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Ta bort ett kluster
Om du vill ta bort ett kluster kör du PowerShell-skriptet *RemoveServiceFabricCluster.ps1* från paketmappen och lägger till sökvägen till JSON-konfigurationsfilen. Om du vill kan du ange en plats för loggen över borttagningen.

Det här skriptet kan köras på alla datorer som har administratörs åtkomst till alla datorer som listas som noder i kluster konfigurations filen. Datorn som skriptet körs på behöver inte vara en del av klustret.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Telemetri-data som samlas in och hur de ska avanmälas
Som standard samlar produkten in telemetri på Service Fabric användning för att förbättra produkten. Best Practices Analyzer som körs som en del av installations programmet för att kontrol lera anslutningen till [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) . Om det inte går att komma åt, Miss lyckas installationen om du inte väljer att använda telemetri.

1. Pipelinen för telemetri försöker överföra följande data till [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) en gång varje dag. Det är en bra uppladdning och har ingen inverkan på kluster funktionerna. Telemetri skickas endast från den nod som kör den primära hanteraren för växling vid fel. Inga andra noder skickar ut telemetri.
2. Telemetrin består av följande:

* Antal tjänster
* Antal ServiceTypes
* Antal program
* Antal ApplicationUpgrades
* Antal FailoverUnits
* Antal InBuildFailoverUnits
* Antal UnhealthyFailoverUnits
* Antal repliker
* Antal InBuildReplicas
* Antal StandByReplicas
* Antal OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Antal noder
* IsContextComplete: TRUE/FALSE
* ClusterId: det här är ett GUID som skapas slumpmässigt för varje kluster
* ServiceFabricVersion
* IP-adressen för den virtuella datorn eller datorn som Telemetrin överförs från

Om du vill inaktivera telemetri lägger du till följande i *Egenskaper* i kluster konfigurationen: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>För hands versions funktioner som ingår i det här paketet
Inga.


> [!NOTE]
> Från och med den nya [ga-versionen av det fristående klustret för Windows Server (version 5.3.204. x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)kan du uppgradera klustret till framtida versioner, manuellt eller automatiskt. Mer information finns i [uppgradera ett fristående Service Fabric kluster versions](service-fabric-cluster-upgrade-windows-server.md) dokument.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Distribuera och ta bort program med PowerShell](service-fabric-deploy-remove-applications.md)
* [Konfigurations inställningar för fristående Windows-kluster](service-fabric-cluster-manifest.md)
* [Lägga till eller ta bort noder i ett fristående Service Fabric-kluster](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Uppgradera en fristående Service Fabric kluster version](service-fabric-cluster-upgrade-windows-server.md)
* [Skapa ett fristående Service Fabric-kluster med virtuella Azure-datorer som kör Windows](./service-fabric-cluster-creation-via-arm.md)
* [Skydda ett fristående kluster i Windows med Windows-säkerhet](service-fabric-windows-cluster-windows-security.md)
* [Skydda ett fristående kluster i Windows med hjälp av X509-certifikat](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
