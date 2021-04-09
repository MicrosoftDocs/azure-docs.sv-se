---
title: Övervaka prestanda på virtuella Azure-datorer – Azure Application insikter
description: Övervakning av program prestanda för virtuella Azure-datorer och skalnings uppsättningar för virtuella Azure-datorer. Diagrammets inläsnings-och svars tid, beroende information och ange aviseringar för prestanda.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 0951d1d622f59de4780735fad78ac73649ea2369
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711490"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Distribuera Azure Monitor Application Insights-agenten på virtuella Azure-datorer och skalnings uppsättningar för virtuella Azure-datorer

Nu är det enklare än någonsin att aktivera övervakning av .NET-eller Java-baserade webb program som körs på [virtuella Azure-datorer](https://azure.microsoft.com/services/virtual-machines/) och [skalnings uppsättningar för virtuella Azure](../../virtual-machine-scale-sets/index.yml) -datorer. Få alla fördelar med att använda Application Insights utan att ändra koden.

Den här artikeln vägleder dig genom att aktivera Application Insights övervakning med hjälp av Application Insights agent och ger preliminär vägledning för att automatisera processen för storskalig distribution.
> [!IMPORTANT]
> **Java** -baserade program som körs på virtuella Azure-datorer och VMSS övervakas med **[Application Insights Java 3,0-agent](./java-in-process-agent.md)**, som är allmänt tillgänglig.

> [!IMPORTANT]
> Azure Application Insights-agenten för ASP.NET-program som körs på **virtuella Azure-datorer och VMSS** är för närvarande en offentlig för hands version. För att övervaka dina ASP.Net-program som körs **lokalt** använder du [Azure Application Insights-agenten för lokala servrar](./status-monitor-v2-overview.md), som är allmänt tillgänglig och fullt stöd.
> För hands versionen av virtuella Azure-datorer och VMSS tillhandahålls utan service nivå avtal och vi rekommenderar det inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds och vissa kan ha begränsade funktioner.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Aktivera Application Insights

Det finns två sätt att aktivera program övervakning för virtuella Azure-datorer och värdbaserade program för skalnings uppsättningar för virtuella Azure-datorer:

### <a name="auto-instrumentation-via-application-insights-agent"></a>Automatisk instrumentering via Application Insights agent

* Den här metoden är det enklaste sättet att aktivera, och ingen Avancerad konfiguration krävs. Det kallas ofta övervakning av körnings miljön.

* För virtuella Azure-datorer och skalnings uppsättningar för virtuella Azure-datorer rekommenderar vi att du minst aktiverar den här övervaknings nivån. Efter det, baserat på ditt speciella scenario, kan du utvärdera om du behöver manuell Instrumentation.

> [!NOTE]
> Automatisk Instrumentation är för närvarande endast tillgängligt för .NET IIS-värdbaserade program och Java. Använd ett SDK för att instrumentera ASP.NET Core-, Node.js-och python-program som finns på en virtuell Azure-dator och skalnings uppsättningar för virtuella datorer.


#### <a name="net"></a>.NET

  * Application Insights agenten automatiskt samlar in samma beroende signaler som är färdiga som .NET SDK. Mer information finns i [beroende automatisk insamling](./auto-collect-dependencies.md#net) .
        
#### <a name="java"></a>Java
  * För Java är **[Application Insights Java 3,0-agenten](./java-in-process-agent.md)** den rekommenderade metoden. De mest populära biblioteken och ramverken, samt loggar och beroenden, [samlas in automatiskt](./java-in-process-agent.md#auto-collected-requests-dependencies-logs-and-metrics)med en mängd [ytterligare konfigurationer](./java-standalone-config.md)

### <a name="code-based-via-sdk"></a>Kod baserad via SDK
    
#### <a name="net"></a>.NET
  * För .NET-appar är den här metoden mycket mer anpassningsbar, men det kräver [att du lägger till ett beroende på Application Insights SDK NuGet-paketen](./asp-net.md). Den här metoden innebär också att du måste hantera uppdateringarna till den senaste versionen av paketen själv.

  * Om du behöver göra anpassade API-anrop för att spåra händelser/beroenden som inte har registrerats som standard med en agent-baserad övervakning, behöver du använda den här metoden. Mer information finns i [artikeln om API för anpassade händelser och mått](./api-custom-events-metrics.md) .

    > [!NOTE]
    > Endast för .NET-appar – om både agentbaserade övervakning och manuellt SDK-baserad Instrumentation identifieras, kommer inställningarna för manuell Instrumentation att tillämpas. Detta är för att förhindra att duplicerade data skickas. Mer information om det här finns i [fel söknings avsnittet](#troubleshooting) nedan.

#### <a name="net-core"></a>.NET Core
Använd [SDK](./asp-net-core.md) för att övervaka .net Core-program 

#### <a name="java"></a>Java 

Om du behöver ytterligare anpassad telemetri för Java-program, se vad [som är tillgängligt](./java-in-process-agent.md#send-custom-telemetry-from-your-application), Lägg till [anpassade dimensioner](./java-standalone-config.md#custom-dimensions)eller använda [telemetri-processorer](./java-standalone-telemetry-processors.md). 

#### <a name="nodejs"></a>Node.js

Använd [SDK](./nodejs.md)för att instrumentera ditt Node.js program.

#### <a name="python"></a>Python

Använd [SDK](./opencensus-python.md)för att övervaka python-appar.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Hantera Application Insights agent för .NET-program på virtuella Azure-datorer med PowerShell

> [!NOTE]
> Innan du installerar Application Insights agent behöver du en anslutnings sträng. [Skapa en ny Application Insights resurs](./create-new-resource.md) eller kopiera anslutnings strängen från en befintlig Application Insights-resurs.

> [!NOTE]
> Ny till PowerShell? Kolla in [guiden kom igång](/powershell/azure/get-started-azureps).

Installera eller uppdatera Application Insights agenten som ett tillägg för virtuella Azure-datorer
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Du kan installera eller uppdatera Application Insights agenten som ett tillägg i flera Virtual Machines i skala med hjälp av en PowerShell-loop.

Avinstallera Application Insights agent-tillägg från den virtuella Azure-datorn
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Fråga Application Insights agent tilläggs status för virtuell Azure-dator
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Hämta lista över installerade tillägg för virtuell Azure-dator
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
Du kan också Visa installerade tillägg i [bladet för virtuella Azure-datorer](../../virtual-machines/extensions/overview.md) i portalen.

> [!NOTE]
> Verifiera installationen genom att klicka på Live Metrics Stream i den Application Insights resurs som är associerad med den anslutnings sträng som du använde för att distribuera Application Insights agent tillägget. Om du skickar data från flera Virtual Machines väljer du de virtuella Azure-datorerna under Server namn. Det kan ta upp till en minut innan data börjar flöda.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Hantera Application Insights agent för .NET-program på skalnings uppsättningar för virtuella Azure-datorer med hjälp av PowerShell

Installera eller uppdatera Application Insights agenten som ett tillägg för skalnings uppsättningen för virtuella Azure-datorer
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Avinstallera tillägg för program övervakning från skalnings uppsättningar för virtuella Azure-datorer
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Fråga om program övervaknings tilläggets status för skalnings uppsättningar för virtuella Azure-datorer
```powershell
# Not supported by extensions framework
```

Hämta lista över installerade tillägg för skalnings uppsättningar för virtuella Azure-datorer
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Felsökning

Hitta fel söknings tips för Application Insights Monitoring Agent-tillägg för .NET-program som körs på virtuella Azure-datorer och skalnings uppsättningar för virtuella datorer.

> [!NOTE]
> .NET Core-, Node.js-och python-program stöds bara på virtuella Azure-datorer och skalnings uppsättningar för virtuella Azure-datorer via manuell SDK-baserad Instrumentation, och därför gäller inte stegen nedan för dessa scenarier.

Utökning av utdata loggas till filer som finns i följande kataloger:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [distribuerar ett program till en skalnings uppsättning för en virtuell Azure-dator](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Konfigurera webbtester för tillgänglighet](monitor-web-app-availability.md) om du vill få en avisering om slut punkten är nere.