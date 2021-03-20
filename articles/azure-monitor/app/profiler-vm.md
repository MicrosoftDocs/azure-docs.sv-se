---
title: Profilera webb program på en virtuell Azure-dator – Application Insights Profiler
description: Profilera webbappar på en virtuell Azure-dator med hjälp av Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: f514dd7b54ac091535aeab43a8a7d2a645b50a09
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87315843"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profilera webbappar som körs på en virtuell Azure-dator eller en virtuell dators skalnings uppsättning med hjälp av Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan också Distribuera Azure Application Insights profiler på följande tjänster:
* [Azure App Service](./profiler.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](?toc=%2fazure%2fazure-monitor%2ftoc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Distribuera profiler på en virtuell dator eller en skalnings uppsättning för virtuella datorer
Den här artikeln visar hur du kan få Application Insights Profiler som körs på din virtuella Azure-dator (VM) eller skalnings uppsättning för virtuella Azure-datorer. Profiler installeras med Azure-diagnostik-tillägget för virtuella datorer. Konfigurera tillägget för att köra profiler och skapa Application Insights SDK i ditt program.

1. Lägg till Application Insights SDK i [ASP.net-programmet](./asp-net.md).

   Om du vill visa profiler för dina begär Anden måste du skicka begäran om telemetri till Application Insights.

1. Installera Azure-diagnostik tillägget på den virtuella datorn. Fullständiga exempel på Resource Manager-mallar finns i:  
   * [Virtuell dator](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Skaluppsättning för virtuella datorer](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     Nyckel delen är ApplicationInsightsProfilerSink i WadCfg. Om du vill att Azure-diagnostik aktivera profiler för att skicka data till din iKey lägger du till ytterligare en mottagare i det här avsnittet.
    
     ```json
     "SinksConfig": {
       "Sink": [
         {
           "name": "ApplicationInsightsSink",
           "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
         },
         {
           "name": "MyApplicationInsightsProfilerSink",
           "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
         }
       ]
     },
     ```

1. Distribuera den ändrade miljö distributions definitionen.  

   Att tillämpa ändringarna innebär vanligt vis en fullständig mall distribution eller en molnbaserad publicering via PowerShell-cmdletar eller Visual Studio.  

   Följande PowerShell-kommandon är en alternativ metod för befintliga virtuella datorer som bara vidrör Azure-diagnostik-tillägget. Lägg till de tidigare nämnda ProfilerSink i konfigurationen som returneras av kommandot Get-AzVMDiagnosticsExtension. Sedan skickar du den uppdaterade konfigurationen till kommandot Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Aktivera Windows-funktionen om det avsedda [](https://www.microsoft.com/web/downloads/platform.aspx)programmet körs via IIS `IIS Http Tracing` .

   a. Upprätta fjärråtkomst till miljön och Använd sedan fönstret [Lägg till Windows-funktioner](/iis/configuration/system.webserver/tracing/) . Eller kör följande kommando i PowerShell (som administratör):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Om du har problem med att upprätta fjärråtkomst kan du använda [Azure CLI](/cli/azure/get-started-with-azure-cli) för att köra följande kommando:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Distribuera ditt program.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Ange profilerings mottagare med hjälp av Azure Resource Explorer
Vi har ännu inget sätt att ange Application Insights Profiler mottagare från portalen. I stället för att använda PowerShell som det som beskrivs ovan kan du använda Azure Resource Explorer för att ange mottagaren. Men Observera att om du distribuerar den virtuella datorn igen går sinken förlorad. Du måste uppdatera den konfiguration som du använder när du distribuerar den virtuella datorn för att bevara den här inställningen.

1. Kontrol lera att Windows Azure-diagnostik-tillägget har installerats genom att visa de tillägg som är installerade för den virtuella datorn.  

    ![Kontrol lera om WAD-tillägget har installerats][wadextension]

2. Hitta tillägget för VM-diagnostik för den virtuella datorn. Gå till [https://resources.azure.com](https://resources.azure.com). Expandera resurs gruppen, Microsoft. Compute-virtualMachines, namn på virtuell dator och tillägg.  

    ![Navigera till WAD-konfiguration i Azure Resource Explorer][azureresourceexplorer]

3. Lägg till Application Insights Profiler-Sink i SinksConfig-noden under WadCfg. Om du inte redan har ett SinksConfig-avsnitt kan du behöva lägga till ett. Se till att ange rätt Application Insights iKey i dina inställningar. Du måste växla Explorer-läget till läsa/skriva i det övre högra hörnet och trycka på den blå knappen Redigera.

    ![Lägg till Application Insights Profiler mottagare][resourceexplorersinksconfig]

4. När du är klar med redigeringen av config trycker du på "flytta". Om placeringen lyckas visas en grön bock i mitten av skärmen.

    ![Skicka skicka begäran om att tillämpa ändringarna][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Kan profiler köras på lokala servrar?
Vi har ingen plan för att stödja Application Insights Profiler för lokala servrar.

## <a name="next-steps"></a>Nästa steg

- Generera trafik till ditt program (till exempel starta ett [tillgänglighets test](monitor-web-app-availability.md)). Vänta sedan 10 till 15 minuter så att spårningar börjar skickas till Application Insights-instansen.
- Se [profilerade spår](profiler-overview.md?toc=/azure/azure-monitor/toc.json) i Azure Portal.
- Hjälp med fel sökning av profilerings problem finns i [fel sökning av profileraren](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png

