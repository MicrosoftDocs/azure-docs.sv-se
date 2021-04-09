---
title: Korrigera operativ systemet Windows i Service Fabric-klustret
description: Den här artikeln beskriver hur du automatiserar uppdatering av operativ system på ett Service Fabric-kluster med hjälp av programmet för uppdaterings dirigering.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: e51b247f8c1a5a9ed8f6ec8e24363015afb2f7de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614419"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Korrigera operativ systemet Windows i Service Fabric-klustret

## <a name="automatic-os-image-upgrades"></a>Automatiska uppgraderingar av operativ system avbildningar

Att hämta [automatiska operativ system avbildningar på din Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) är den bästa metoden för att hålla operativ systemet uppdaterad i Azure. För skalnings uppsättningar för virtuella datorer som baseras på automatiska uppgraderingar av OS-avbildningar krävs silver eller större hållbarhet på en skalnings uppsättning.

Krav för automatisk uppgradering av operativ system avbildningar med Virtual Machine Scale Sets
-   Service Fabric [hållbarhets nivå](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) är silver eller guld och inte brons.
-   Service Fabric-tillägget i modell definitionen för skalnings uppsättningen måste ha TypeHandlerVersion 1,1 eller senare.
-   Hållbarhets nivån ska vara samma på Service Fabric klustret och Service Fabric tillägget i modell definitionen för skalnings uppsättningen.
- Det krävs ingen ytterligare hälso avsökning eller användning av program hälso tillägg för Virtual Machine Scale Sets.

Se till att hållbarhets inställningarna inte stämmer överens med Service Fabric klustret och Service Fabric tillägget, eftersom ett matchnings fel resulterar i uppgraderings fel. Du kan ändra hållbarhets nivåer enligt de rikt linjer som beskrivs på [den här sidan](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels).

Med brons hållbarhet är automatisk uppgradering av OS-avbildningen inte tillgänglig. Även om [program för uppdaterings dirigering](#patch-orchestration-application ) (endast avsett för icke-Azure-värdbaserade kluster) *inte rekommenderas* för silver eller större hållbarhets nivåer, är det bara ditt alternativ att automatisera Windows-uppdateringar med avseende på Service Fabric uppgraderings domäner.

> [!IMPORTANT]
> Uppgraderingar vid virtuella datorer där "Windows Update" tillämpar operativ Systems korrigeringar utan att ersätta operativ system disken stöds inte på Azure Service Fabric.

Det finns två steg som krävs för att aktivera funktionen med inaktiverat Windows Update i åtgärds systemet på rätt sätt.

1. Aktivera automatisk uppgradering av operativ Systems avbildning, inaktivera Windows updates ARM 
    ```json
    "virtualMachineProfile": { 
        "properties": {
          "upgradePolicy": {
            "automaticOSUpgradePolicy": {
              "enableAutomaticOSUpgrade":  true
            }
          }
        }
      }
    ```
    
    ```json
    "virtualMachineProfile": { 
        "osProfile": { 
            "windowsConfiguration": { 
                "enableAutomaticUpdates": false 
            }
        }
    }
    ```

    Azure PowerShell
    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -AutomaticOSUpgrade $true -EnableAutomaticUpdate $false
    ``` 
    
1. Uppdatera skalnings uppsättnings modellen efter den här konfigurationen ändra en avbildning av alla datorer som behövs för att uppdatera skalnings uppsättnings modellen så att ändringen träder i funktion.
    
    Azure PowerShell
    ```azurepowershell-interactive
    $scaleSet = Get-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName
    $instances = foreach($vm in $scaleSet)
    {
        Set-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -InstanceId $vm.InstanceID -Reimage
    }
    ``` 
    
Ta en titt på [automatiska avbildnings uppgraderingar av operativ systemet genom att Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) för ytterligare instruktioner.

## <a name="patch-orchestration-application"></a>Program för uppdaterings dirigering

> [!IMPORTANT]
> Från och med den 30 april 2019 stöds inte längre uppdaterings program version 1,2. *. Se till att uppgradera till den senaste versionen.

POA (patch Orchestration Application) är en omslutning runt Azure Service Fabric Repair Manager-tjänsten, som möjliggör konfigurations-baserad operativ Systems uppdaterings schemaläggning för icke-Azure-värdbaserade kluster. POA krävs inte för icke-Azure-värdbaserade kluster, men planerings korrigerings installation av uppdaterings domän krävs för att korrigera Service Fabric kluster värdar utan att det uppstår avbrott.

POA är ett Service Fabric program som automatiserar operativ Systems korrigeringar i ett Service Fabric kluster utan att det uppstår avbrott.

POA tillhandahåller följande funktioner:

- **Automatisk installation av operativ system uppdateringar**. Operativ system uppdateringar laddas ned och installeras automatiskt. Klusternoder startas om efter behov utan att det uppstår avbrott i klustret.

- **Kluster medveten korrigering och hälso integrering**. När POA tillämpar uppdateringar övervakas hälsan för klusternoderna. Klusternoder uppdateras en nod i taget eller en uppdaterings domän i taget. Om hälso tillståndet för klustret slutar fungera på grund av korrigerings processen, stoppas korrigeringen för att förhindra att problemet förvärras.

## <a name="internal-details-of-poa"></a>Intern information om POA

POA består av följande del komponenter:

- **Koordinator tjänst**: den här tillstånds känsliga tjänsten ansvarar för:
    - Koordinera Windows Update-jobbet på hela klustret.
    - Lagrings resultatet av slutförda Windows Update åtgärder.

- **Node agent service**: den här tillstånds lösa tjänsten körs på alla Service Fabric klusternoder. Tjänsten ansvarar för:
    - Starta NTService för Node-agenten.
    - Övervaka NTService för Node-agenten.

- **Node agent-NTService**: den här Windows NT-tjänsten körs på en högre nivå privilegie rad (system). Dessutom körs Node-agenttjänsten och koordinator tjänsten på en behörighet på lägre nivå (nätverks tjänsten). Tjänsten ansvarar för att utföra följande Windows Update jobb på alla klusternoder:
    - Inaktivera automatiska Windows-uppdateringar på noden.
    - Hämta och installera Windows-uppdateringar enligt principen som användaren har angett.
    - Startar om installationen av datorn efter Windows updates.
    - Överför resultaten av Windows-uppdateringar till koordinator tjänsten.
    - Rapportera hälso rapporter om en åtgärd Miss lyckas när alla återförsök har uppnåtts.

> [!NOTE]
> POA använder tjänsten Service Fabric Repair Manager för att inaktivera eller aktivera noden och utföra hälso kontroller. Den reparations uppgift som skapas av POA spårar Windows Update förloppet för varje nod.

## <a name="prerequisites"></a>Förutsättningar

> [!NOTE]
> Den lägsta .NET Framework versionen som krävs är 4,6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Aktivera Repair Manager tjänsten (om den inte redan körs)

POA kräver att Repair Managers tjänsten är aktive rad i klustret.

#### <a name="azure-clusters"></a>Azure-kluster

Azure-kluster i silver hållbarhets nivån har Repair Manager tjänsten aktiverat som standard. Azure-kluster i guld hållbarhets nivån kan ha Repair Manager tjänsten aktive rad, beroende på när dessa kluster skapades. Azure-kluster på brons hållbarhets nivån har som standard inte aktiverat tjänsten Repair Manager. Om tjänsten redan är aktive rad kan du se att den körs i avsnittet system tjänster i Service Fabric Explorer.

##### <a name="the-azure-portal"></a>Azure Portal
Du kan aktivera Repair Manager från Azure Portal när du konfigurerar klustret. När du konfigurerar klustret väljer du alternativet **inkludera Repair Manager** under **tilläggs funktioner**.

![Bild av att aktivera Repair Manager från Azure Portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Distributions modellen för Azure Resource Manager
Du kan också använda [Azure Resource Manager distributions modell](./service-fabric-cluster-creation-via-arm.md) för att aktivera tjänsten Repair Manager på nya och befintliga Service Fabric-kluster. Hämta mallen för det kluster som du vill distribuera. Du kan antingen använda exempel mallarna eller skapa en anpassad mall för Azure Resource Manager distributions modell. 

Gör så här om du vill aktivera Repair Manager tjänsten med hjälp av [mallen för Azure Resource Manager distributions modell](./service-fabric-cluster-creation-via-arm.md):

1. Kontrol lera att `apiVersion` är inställt på *2017-07-01 – för hands version* för resursen *Microsoft. ServiceFabric/Clusters* . Om den är annorlunda måste du uppdatera `apiVersion` till *2017-07-01-för hands version* eller senare:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Aktivera Repair Manager tjänsten genom att lägga till följande `addonFeatures` avsnitt efter `fabricSettings` avsnittet:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. När du har uppdaterat kluster mal len med de här ändringarna kan du tillämpa dem och låta uppdateringen slutföras. Nu kan du se Repair Manager-tjänsten som körs i klustret. Den heter *Fabric:/system/RepairManagerService* i avsnittet system services i Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Fristående lokala kluster

Om du vill aktivera Repair Manager tjänsten på ett nytt eller befintligt Service Fabric-kluster kan du använda [konfigurations inställningarna för fristående Windows-kluster](./service-fabric-cluster-manifest.md).

Så här aktiverar du tjänsten Repair Manager:

1. Kontrol lera att `apiVersion` i [allmänna klusterkonfigurationer](./service-fabric-cluster-manifest.md#general-cluster-configurations) är inställt på *04-2017* eller senare, som du ser här:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Aktivera Repair Manager tjänsten genom att lägga till följande `addonFeatures` avsnitt efter `fabricSettings` avsnittet, som du ser här:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Uppdatera ditt kluster manifest med de här ändringarna genom att använda det uppdaterade kluster manifestet [skapa ett nytt kluster](./service-fabric-cluster-creation-for-windows-server.md) eller [uppgradera kluster konfigurationen](./service-fabric-cluster-upgrade-windows-server.md). 

   När klustret har körts med ett uppdaterat kluster manifest kan du se Repair Manager-tjänsten som körs i klustret. Den heter *Fabric:/system/RepairManagerService* och finns i avsnittet system tjänster i Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Konfigurera Windows-uppdateringar för alla noder

Automatiska Windows-uppdateringar kan leda till tillgänglighets förlust, eftersom flera klusternoder kan startas om samtidigt. POA försöker som standard inaktivera automatiska Windows-uppdateringar på varje klusternod. Men om inställningarna hanteras av en administratör eller en grupprincip rekommenderar vi att du anger Windows Update principen "meddela innan nedladdning" uttryckligen.

## <a name="download-the-application-package"></a>Ladda ned programpaketet

Om du vill ladda ned programpaketet går du till [sidan för korrigerings program utgåvor](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) på GitHub.

## <a name="configure-poa-behavior"></a>Konfigurera POA beteende

Du kan konfigurera POA-beteendet så att det passar dina behov. Åsidosätt standardvärdena genom att skicka i program parametern när du skapar eller uppdaterar programmet. Du kan ange program parametrar genom `ApplicationParameter` att ange till-eller- `Start-ServiceFabricApplicationUpgrade` `New-ServiceFabricApplication` cmdletarna.

| Parameter        | Typ                          | Information |
|:-|-|-|
|MaxResultsToCache    |Lång                              | Det maximala antalet Windows Update resultat som ska cachelagras. <br><br>Standardvärdet är 3000, förutsatt att: <br> &nbsp;&nbsp;-Antalet noder är 20. <br> &nbsp;&nbsp;– Antalet uppdateringar till en nod per månad är 5. <br> &nbsp;&nbsp;-Antalet resultat per åtgärd kan vara 10. <br> &nbsp;&nbsp;-Resultaten för de senaste tre månaderna bör lagras. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy anger den princip som ska användas av koordinator tjänsten för att installera Windows-uppdateringar över Service Fabric klusternoder.<br><br>De tillåtna värdena är: <br>*NodeWise*: Windows-uppdateringar installeras en nod i taget. <br> *UpgradeDomainWise*: Windows-uppdateringar installeras en uppdaterings domän i taget. (Högst, kan alla noder som tillhör en uppdaterings domän gå för en Windows Update.)<br><br> Information om vilka principer som passar bäst för klustret finns i avsnittet [vanliga frågor och svar](#frequently-asked-questions) .
|LogsDiskQuotaInMB   |Lång  <br> (Standard: *1024*)               | Den maximala storleken för loggning av korrigeringsfiler för att dirigera om i MB, som kan sparas lokalt på noder.
| WUQuery               | sträng<br>(Standard: *IsInstalled = 0*)                | Fråga för att hämta Windows-uppdateringar. Mer information finns i [WuQuery.](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search)
| InstallWindowsOSOnlyUpdates | *Boolesk* <br> (standard: falskt)                 | Använd den här flaggan för att styra vilka uppdateringar som ska hämtas och installeras. Följande värden är tillåtna <br>True – installerar endast uppdateringar för Windows-operativsystem.<br>false-installerar alla tillgängliga uppdateringar på datorn.          |
| WUOperationTimeOutInMinutes | Int <br>(Standard: *90*)                   | Anger tids gränsen för alla Windows Update åtgärder (Sök eller ladda ned eller installera). Om åtgärden inte slutförs inom den angivna tids gränsen avbryts den.       |
| WURescheduleCount     | Int <br> (Standard: *5*)                  | Det maximala antalet gånger som tjänsten omplanerar Windows Update om en åtgärd Miss lyckas beständigt.          |
| WURescheduleTimeInMinutes | Int <br>(Standard: *30*) | Intervallet då tjänsten omplanerar Windows-uppdateringar om ett problem kvarstår. |
| WUFrequency           | Kommaavgränsad sträng (standard: *veckovis, onsdag, 7:00:00*)     | Frekvensen för att installera Windows-uppdateringar. Formatet och möjliga värden är: <br>– Varje månad, DD, HH: MM: SS (exempel: *Monthly, 5, 12:22:32*). Tillåtna värden för fältet _DD_ (dag) är tal från 1 till 28 och _sist_. <br>– Varje vecka, dag, HH: MM: SS (exempel: *Weekly, tisdag, 12:22:32*)  <br>– Dagligen, HH: MM: SS (exempel: *Daily, 12:22:32*)  <br>– Vecka, dag, HH: MM: SS (exempel: *2, fredag, 21:00:00* anger 9:00 pm UTC på fredag i den andra veckan varje månad) <br>- *Ingen* anger att Windows-uppdateringar inte ska göras.  <br><br> Tiden är i UTC-tid.|
| AcceptWindowsUpdateEula | Boolesk <br>(Standard: *Sant*) | Genom att ställa in den här flaggan godkänner programmet End-User licens avtal för Windows Update för ägare av datorn.              |

> [!TIP]
> Om du vill att Windows-uppdateringar ska ske direkt anger du `WUFrequency` i förhållande till programmets distributions tid. Anta till exempel att du har ett test kluster med fem noder och planerar att distribuera appen med cirka 5:00 PM UTC. Om du antar att program uppgraderingen eller distributionen tar 30 minuter högst, ställer du in WUFrequency som *dagligen, 17:30:00*.

## <a name="deploy-poa"></a>Distribuera POA

1. Slutför alla nödvändiga steg för att förbereda klustret.
1. Distribuera POA som andra Service Fabric-appar. Information om hur du distribuerar den med hjälp av PowerShell finns i [distribuera och ta bort program med hjälp av PowerShell](./service-fabric-deploy-remove-applications.md).
1. Om du vill konfigurera programmet vid tidpunkten för distributionen skickar `ApplicationParameter` du till `New-ServiceFabricApplication` cmdleten. För din bekvämlighet har vi tillhandahållit skriptet Deploy.ps1 tillsammans med programmet. Använda skriptet:

    - Anslut till ett Service Fabric-kluster med hjälp av `Connect-ServiceFabricCluster` .
    - Kör PowerShell-skriptet Deploy.ps1 med lämpligt `ApplicationParameter` värde.

> [!NOTE]
> Behåll skriptet och programmappen *PatchOrchestrationApplication* i samma katalog.

## <a name="upgrade-poa"></a>Uppgradera POA

Om du vill uppgradera POA-versionen med hjälp av PowerShell följer du anvisningarna i [Service Fabric program uppgradering med PowerShell](./service-fabric-application-upgrade-tutorial-powershell.md).

## <a name="remove-poa"></a>Ta bort POA

Om du vill ta bort programmet följer du anvisningarna i [distribuera och ta bort program med hjälp av PowerShell](./service-fabric-deploy-remove-applications.md).

För din bekvämlighet har vi tillhandahållit Undeploy.ps1-skriptet tillsammans med programmet. Använda skriptet:

  - Anslut till ett Service Fabric-kluster med hjälp av ```Connect-ServiceFabricCluster``` .
  - Kör PowerShell-skriptet Undeploy.ps1.

> [!NOTE]
> Behåll skriptet och programmappen *PatchOrchestrationApplication* i samma katalog.

## <a name="view-the-windows-update-results"></a>Visa Windows Update resultat

POA exponerar REST-API: er för att visa historiska resultat för användare. Här är ett exempel på resultatet JSON:

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

JSON-fälten beskrivs i följande tabell:

Fält | Värden | Information
-- | -- | --
OperationResult under pågående | 0-lyckades<br> 1 – lyckades med fel<br> 2 – misslyckades<br> 3-avbruten<br> 4 – avbruten med timeout | Visar resultatet av den övergripande åtgärden, som normalt innebär installation av en eller flera uppdateringar.
ResultCode | Samma som OperationResult under pågående | Det här fältet visar resultatet av installations åtgärden för en enskild uppdatering.
Åtgärdstyp | 1 – installation<br> 0-Sök och hämta| Som standard är installationen den enda OperationType som visas i resultaten.
WindowsUpdateQuery | Standardvärdet är "IsInstalled = 0" | Den Windows Update-fråga som användes för att söka efter uppdateringar. Mer information finns i [WuQuery](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search).
RebootRequired | True-omstart krävs<br> false-omstart krävs inte | Anger om en omstart krävs för att slutföra installationen av uppdateringar.
OperationStartTime | DateTime | Anger tiden då åtgärden (hämtning/installation) startades.
OperationTime | DateTime | Anger tiden då åtgärden (hämtning/installation) slutfördes.
HResult | 0 – lyckad<br> annat – haveri| Anger orsaken till problemet med Windows-uppdateringen med updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Om ingen uppdatering har schemalagts ännu är resultatet JSON tomt.

Logga in på klustret för att fråga Windows Update resultat. Ta reda på IP-adressen för replikeringen för den primära adressen för koordinator tjänsten och öppna följande URL från webbläsaren: http:// &lt; Replica-IP &gt; : &lt; ApplicationPort &gt; /PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

REST-slutpunkten för koordinator tjänsten har en dynamisk port. Se Service Fabric Explorer för att kontrol lera den exakta URL: en. Resultatet är till exempel tillgängligt på *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* .

![Bild av REST-slutpunkten](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Om den omvända proxyn är aktive rad i klustret, kan du även komma åt webb adressen utanför klustret.

Den slut punkt som du måste trycka på är *http:// &lt; SERVERURL &gt; : &lt; REVERSEPROXYPORT &gt; /PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

Om du vill aktivera omvänd proxy i klustret följer du anvisningarna i [omvänd proxy i Azure Service Fabric](./service-fabric-reverseproxy.md). 

> 
> [!WARNING]
> När den omvända proxyn har kon figurer ATS kan alla mikrotjänster i klustret som exponerar en HTTP-slutpunkt adresseras utanför klustret.

## <a name="diagnostics-and-health-events"></a>Diagnostik-och hälso händelser

I det här avsnittet beskrivs hur du felsöker eller diagnostiserar problem med uppdaterings uppdateringar via POA i Service Fabric kluster.

> [!NOTE]
> För att få många av följande utgångna, själv diagnos förbättringar bör du ha POA version 1.4.0 eller senare installerad.

Node agent-NTService skapar [reparations uppgifter](/dotnet/api/system.fabric.repair.repairtask) för att installera uppdateringar på noderna. Varje aktivitet förbereds sedan av koordinator tjänsten enligt principen för godkännande av aktiviteter. Slutligen godkänns de för beredda uppgifterna av Repair Manager, som inte godkänner någon aktivitet om klustret är i ett ohälsosamt tillstånd. 

För att hjälpa dig att förstå hur uppdateringar fortsätter på en nod, ska vi gå igenom steg för steg:

1. NodeAgentNTService, som körs på varje nod, söker efter tillgängliga Windows-uppdateringar på den schemalagda tiden. Om det finns uppdateringar, hämtas de på noden.

1. När uppdateringarna har hämtats skapar Node-agentens NTService en motsvarande reparations uppgift för noden med namnet *POS___ \<unique_id>*. Du kan visa dessa reparations uppgifter med hjälp av cmdleten [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask) eller använda SFX i avsnittet Node details. När reparations uppgiften har skapats flyttas den snabbt till [ *begärt* tillstånd](/dotnet/api/system.fabric.repair.repairtaskstate).

1. Koordinator tjänsten söker regelbundet efter reparations uppgifter i *begärt* tillstånd och uppdaterar dem sedan för att *förbereda* tillstånd baserat på TaskApprovalPolicy. Om TaskApprovalPolicy har kon figurer ATS att vara NodeWise, förbereds en reparations uppgift som motsvarar en nod bara om ingen annan reparations aktivitet för närvarande *förbereder*, *godkänt*, *Kör* eller *återställer* tillstånd. 

   På samma sätt har UpgradeWise-TaskApprovalPolicy endast uppgifter i föregående steg för noder som tillhör samma uppdaterings domän. När en reparations aktivitet har flyttats till *förberedelse* tillståndet [inaktive ras](/powershell/module/servicefabric/disable-servicefabricnode) motsvarande Service Fabric nod med den avsikt som är inställd för *omstart*.

   POA-versioner 1.4.0 och senare skickar händelser med egenskapen ClusterPatchingStatus på CoordinatorService för att visa de noder som korrigeras. Uppdateringarna installeras på _poanode_0, som du ser i följande bild:

    [![Avbildning av status för kluster uppdatering](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. När noden har inaktiverats flyttas reparations uppgiften till att tillståndet *körs* . 
   
   > [!NOTE]
   > En nod som fastnar i *inaktiverat* tillstånd kan blockera en ny reparations uppgift, vilket stoppar uppdaterings åtgärden i klustret.

1. När reparations *aktiviteten körs,* börjar korrigerings installationen på den noden. När korrigeringen har installerats kan noden eventuellt startas om, beroende på korrigeringen. Sedan flyttas reparations uppgiften till *återställnings* läge, vilket aktiverar noden. Reparations uppgiften markeras sedan som slutförd.

   I POA-versioner 1.4.0 och senare kan du se status för uppdateringen genom att Visa hälso händelser på NodeAgentService med WUOperationStatus- \<NodeName> egenskapen. De markerade avsnitten i följande bilder visar status för Windows-uppdateringar på noder *poanode_0* och *poanode_2*:

   [![Skärm bild som visar konsol fönster med Windows Update åtgärds status med poanode_0 markerat.](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Skärm bild som visar konsol fönster med Windows Update åtgärds status med poanode_1 markerat.](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Du kan också få information genom att använda PowerShell. Det gör du genom att ansluta till klustret och hämta tillståndet för reparations uppgiften med hjälp av [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask). 
   
   I följande exempel är aktiviteten "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" i *DownloadComplete* -tillstånd. Det innebär att uppdateringar har laddats ned på *poanode_2* -noden och att installationen görs när aktiviteten flyttas till *körnings* tillstånd.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Logga in på den virtuella datorn (VM) eller virtuella datorer och lär dig mer om dem med hjälp av Windows-händelseloggar om det finns fler problem att hitta. Den tidigare nämnda reparations uppgiften kan bara finnas i följande utförar-deltillstånd:

      ExecutorSubState | Beskrivning
    -- | -- 
      Ingen = 1 |  Innebär att det inte fanns någon pågående åtgärd på noden. Statusen kanske är i över gång.
      DownloadCompleted = 2 | Innebär att hämtningen slutfördes med slutförd, delvis misslyckad eller misslyckad.
      InstallationApproved = 3 | Innebär att hämtningen har slutförts tidigare och Repair Manager har godkänt installationen.
      InstallationInProgress = 4 | Motsvarar tillståndet för körningen av reparations uppgiften.
      InstallationCompleted = 5 | Innebär att installationen slutfördes med lyckat, delvis lyckades eller misslyckades.
      RestartRequested = 6 | Innebär att installationen av korrigeringen slutfördes och att det finns en väntande omstart på noden.
      RestartNotNeeded = 7 |  Innebär att omstart inte behövdes efter att installationen av korrigeringen slutförts.
      RestartCompleted = 8 | Indikerar att omstarten har slutförts.
      OperationCompleted = 9 | Åtgärden Windows Update har slutförts.
      OperationAborted = 10 | Indikerar att Windows Update åtgärden avbröts.

1. I POA-versioner 1.4.0 och senare kommer en händelse med egenskapen "WUOperationStatus-[nodnamn]" att publiceras på NodeAgentService för att meddela dig när nästa försök att ladda ned och installera Windows-uppdateringar kommer att börja. Detta visas i följande bild:

     [![Skärm bild som visar konsol fönster med Windows Update åtgärds status med NodeAgentService.](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Diagnostikloggar

Uppdaterings program loggar samlas in som en del av Service Fabric körnings loggar.

Du kan samla in loggar med hjälp av det diagnostikverktyg eller den pipeline som du väljer. POA använder följande fasta Provider-ID: n för att logga händelser via [händelse källa](/dotnet/api/system.diagnostics.tracing.eventsource):

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Hälso rapporter

POA publicerar också hälso rapporter mot Node-agenttjänsten eller koordinator tjänsten i följande scenarier:

* NTService för Node-agenten är inte tillgänglig

   Om Node agent-NTService inte finns på en nod genereras en hälso rapport på varnings nivå mot Node Agent-tjänsten.

* Tjänsten Repair Manager är inte aktive rad

   Om tjänsten Repair Manager inte hittas i klustret genereras en hälso rapport på varnings nivå för koordinator tjänsten.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Varför visas mitt kluster i fel tillstånd när POA körs?**

A: under installationen inaktive ras eller startas noderna om, vilket kan tillfälligt leda till ett kluster som inte är i fel tillstånd.

Beroende på programmets princip kan en nod gå nedåt under en uppdaterings åtgärd *eller* så kan en hel uppdaterings domän gå ned alla samtidigt.

I slutet av installationen av Windows Update aktive ras noderna igen efter omstart.

I följande exempel gick klustret till ett fel tillstånd tillfälligt eftersom två noder var nere och MaxPercentageUnhealthyNodes-principen har överskridits. Felet är tillfälligt tills uppdaterings åtgärden kan börja.

![Bild av ohälsosamt kluster](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Läs fel söknings avsnittet om problemet kvarstår.

**F: Vad kan jag göra om POA är i ett varnings tillstånd?**

A: kontrol lera om en hälso rapport som publicerats i programmet anger rotor saken. Varningen innehåller vanligt vis information om problemet. Om problemet är tillfälligt förväntas programmet återställas automatiskt.

**F: Vad kan jag göra om mitt kluster inte är felfritt och jag behöver göra en brådskande uppdatering av operativ systemet?**

A: POA installerar inte uppdateringar när klustret inte är felfritt. Försök att ta klustret till felfritt tillstånd för att avblockera POA-arbetsflödet.

**F: ska jag ange TaskApprovalPolicy som "NodeWise" eller "UpgradeDomainWise" för mitt kluster?**

A: inställningen "UpgradeDomainWise" påskyndar den övergripande kluster reparationen genom att uppdatera parallellt alla noder som tillhör en uppdaterings domän. Under processen är noder som tillhör en hel uppdaterings domän otillgängliga (i [ *inaktiverat* tillstånd](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabled)).

Inställningen "NodeWise" uppdaterar däremot bara en nod i taget, vilket skulle innebära att den övergripande kluster korrigeringen kan ta längre tid. Men endast en nod som mest skulle vara otillgänglig (i *inaktiverat* tillstånd) under korrigerings processen.

Om klustret kan tolerera körning på ett N-1 antal uppdaterings domäner under uppdaterings cykeln (där N är det totala antalet uppdaterings domäner i klustret) kan du ange principen som "UpgradeDomainWise". Annars anger du det till "NodeWise".

**F: hur lång tid tar det att uppdatera en nod?**

A: korrigering av en nod kan ta från minuter (till exempel [Windows Defender-Definitions uppdateringar](https://www.microsoft.com/en-us/wdsi/definitions)) till timmar (till exempel [Windows-kumulativa uppdateringar](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). Den tid som krävs för att korrigera en nod beror främst på: 
 - Storleken på uppdateringarna.
 - Antalet uppdateringar som måste tillämpas i ett korrigerings fönster.
 - Hur lång tid det tar att installera uppdateringarna, starta om noden (om det behövs) och slutför installations stegen efter omstart.
 - Prestanda för den virtuella datorn eller datorn och nätverks förhållandena.

**F: hur lång tid tar det att korrigera ett helt kluster?**

S: den tid som krävs för att korrigera ett helt kluster är beroende av:

- Tiden som krävs för att korrigera en nod.

- Principen för koordinator tjänsten. Standard principen, "NodeWise", resulterar i att endast en nod i taget korrigeras, en metod som är långsammare än att använda "UpgradeDomainWise". 

   Exempel: om en nod tar ~ 1 timme att korrigeras, korrigerar en 20-nods-kluster (samma typ av noder) med 5 uppdaterings domäner, som innehåller 4 noder, kräver:
    - För "NodeWise": ~ 20 timmar.
    - För "UpgradeDomainWise": ~ 5 timmar.

- Kluster belastningen. Varje uppdaterings åtgärd kräver omlokalisering av kund arbets belastningen till andra tillgängliga noder i klustret. En nod som korrigeras skulle ha [ *inaktiverat* tillstånd](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabling) under den här tiden. Om klustret körs nära högsta belastning tar det längre tid att inaktivera processen. Därför kan den övergripande uppdaterings processen förefalla vara långsam under sådana ingångs förhållanden.

- Kluster hälso fel under uppdatering. Eventuell [försämring](/dotnet/api/system.fabric.health.healthstate#System_Fabric_Health_HealthState_Error) [av klustrets hälsa](./service-fabric-health-introduction.md) skulle avbryta korrigerings processen. Det här problemet skulle läggas till den sammanlagda tid som krävs för att korrigera hela klustret.

**F: Varför visas några uppdateringar i Windows Update resultat som hämtas via REST API, men inte under Windows Update historik på datorn?**

A: vissa produkt uppdateringar visas endast i sin egen uppdaterings-eller korrigerings historik. Windows Defender-uppdateringar kan till exempel inte visas i Windows Update historik på Windows Server 2016.

**F: kan POA användas för att uppdatera mitt dev-kluster (kluster med en nod)?**

A: Nej, POA kan inte användas för att korrigera ett kluster med en nod. Den här begränsningen är avsiktligt, eftersom [Service Fabric system tjänster](./service-fabric-technical-overview.md#system-services) eller andra kund program skulle ådra sig drift stopp. Därför godkänns patcha reparations jobb aldrig av Repair Manager.

**F: Hur gör jag för att korrigerar du klusternoder på Linux?**

A: information om hur du dirigerar uppdateringar i Linux finns i [skalnings uppsättningar för virtuella Azure-datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

**F: Varför tar uppdaterings cykeln så länge?**

A: fråga efter resultat-JSON, ange uppdaterings cykeln för alla noder och försök sedan att ta reda på hur lång tid det tar att installera uppdateringen på varje nod med hjälp av OperationStartTime och OperationTime (OperationCompletionTime). 

Om det finns ett stort tidsfönster där ingen uppdatering sker, kan klustret vara i fel tillstånd och kan därför Repair Manager inte godkänna några reparations uppgifter för POA. Om installationen av uppdateringen tar lång tid på en nod, kanske noden inte har uppdaterats på ett tag. Många uppdateringar kan vänta på att installeras, vilket kan leda till fördröjningar. 

Det kan också vara möjligt att uppdatering av noden är blockerad eftersom den fastnar i läget *inaktivera* . Detta inträffar vanligt vis eftersom inaktive ring av noden kan leda till kvorum-eller data förlust situationer.

**F: Varför måste noden inaktive ras när POA korrigeras?**

A: POA inaktiverar noden med en omstart, vilket stoppar eller allokerar *om* alla Service Fabric-tjänster som körs på noden. POA gör detta för att säkerställa att program inte upphör med en blandning av nya och gamla DLL-filer, så vi rekommenderar att inte korrigera en nod utan att inaktivera den.

**F: Vad är det högsta antalet noder som kan uppdateras med hjälp av POA?**

A: POA använder Service Fabric Repair Manager för att skapa reparations aktiviteter för noder för uppdateringar. Det kan dock finnas högst 250 reparations uppgifter på samma tidpunkt. För närvarande skapar POA reparations aktiviteter för varje nod samtidigt, så POA kan inte uppdatera fler än 250 noder i ett kluster. 

## <a name="disclaimers"></a>Ansvarsfriskrivningar

- POA accepterar End-User licens avtalet för Windows Update för användarens räkning. Du kan också stänga av inställningen i programmets konfiguration.

- POA samlar in telemetri för att spåra användning och prestanda. Programmets telemetri följer inställningen för inställningen för Service Fabric körningens telemetri (som är aktiverat som standard).

## <a name="troubleshooting"></a>Felsökning

Det här avsnittet innehåller möjliga fel söknings lösningar för problem med att korrigera noder.

### <a name="a-node-is-not-coming-back-to-up-state"></a>En nod kommer inte tillbaka till tillstånd

* Noden kanske fastnar i *inaktive rings* läge eftersom:

  - En säkerhets kontroll väntar. För att åtgärda den här situationen ser du till att tillräckligt många noder är tillgängliga i felfritt tillstånd.

* Noden kan ha fastnat i *inaktiverat* tillstånd på grund av följande:

  - Den inaktiverades manuellt.
  - Den har inaktiverats på grund av ett pågående Azure-infrastruktur jobb.
  - Den inaktiverades tillfälligt av POA för att korrigera noden.

* Noden kan ha fastnat i ett läge eftersom:

  - Den placerades i ett tillstånd manuellt.
  - Den genomgår en omstart (som kan utlösas av POA).
  - Den innehåller en felaktig virtuell dator eller dator, eller så har den problem med nätverks anslutningen.

### <a name="updates-were-skipped-on-some-nodes"></a>Uppdateringar hoppades över på vissa noder

POA försöker installera en Windows-uppdatering enligt principen för att ändra schemat. Tjänsten försöker återställa noden och hoppa över uppdateringen enligt användnings principen.

I sådana fall genereras en hälso rapport på varnings nivå mot Node Agent-tjänsten. Windows Updates resultatet innehåller också möjliga orsaker till problemet.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>Hälso tillståndet för klustret går fel när uppdateringen installeras

En felaktig Windows-uppdatering kan ta bort hälso tillståndet för ett program eller kluster på en viss nod eller uppdaterings domän. POA avvecklar eventuella efterföljande Windows Update åtgärder tills klustret är felfritt.

En administratör måste ingripa och ta reda på varför programmet eller klustret fick felfri på grund av Windows Update.

## <a name="poa-release-notes"></a>Viktig information om POA

>[!NOTE]
> För POA-versioner 1.4.0 och senare kan du hitta viktig information och versioner på [sidan för korrigerings program lansering](https://github.com/microsoft/Service-Fabric-POA/releases/) på GitHub.

### <a name="version-110"></a>Version 1.1.0
- Offentlig version

### <a name="version-111"></a>Version 1.1.1
- Ett fel har åtgärd ATS i SetupEntryPoint av NodeAgentService som förhindrade installation av NodeAgentNTService.

### <a name="version-120"></a>Version 1.2.0

- Fel korrigeringar kring arbets flödet för omstart av systemet.
- Fel korrigering vid skapande av RM-uppgifter på grund av vilken hälso kontroll under förberedelserna av reparations aktiviteter som förväntades.
- Start läget för Windows-POANodeSvc ändrades från Auto till fördröjt automatiskt.

### <a name="version-121"></a>Version 1.2.1

- Fel korrigering i kluster för skalbar arbets flöde. Introducerade skräp insamlings logik för POA reparations uppgifter som hör till obefintliga noder.

### <a name="version-122"></a>Version 1.2.2

- Diverse fel korrigeringar.
- Binärfiler är nu signerade.
- Lade till sfpkg-länk för programmet.

### <a name="version-130"></a>Version 1.3.0

- Om du anger InstallWindowsOSOnlyUpdates till false installeras alla tillgängliga uppdateringar nu.
- Ändrade logiken för att inaktivera automatiska uppdateringar. Detta åtgärdar ett fel där automatiska uppdateringar inte inaktiverades på Server 2016 och senare.
- Parametriserad placerings begränsning för båda mikrotjänsterna i POA för avancerade användnings fall.

### <a name="version-131"></a>Version 1.3.1
- Åtgärda regression där POA 1.3.0 inte fungerar på Windows Server 2012 R2 eller tidigare på grund av ett problem med att inaktivera automatiska uppdateringar. 
- Åtgärda fel där InstallWindowsOSOnlyUpdates-konfigurationen alltid plockas som sant.
- Ändrar standardvärdet för InstallWindowsOSOnlyUpdates till false.

### <a name="version-132"></a>Version 1.3.2
- Åtgärda ett problem som påverkar uppdaterings livs cykeln på en nod, om det finns noder med ett namn som är en delmängd av det aktuella nodnamnet. För sådana noder är det möjligt att korrigeringen missades eller att en omstart väntar.
