---
title: Arbeta med Azure Desired State Configuration Extension versions historik
description: Den här artikeln delar information om versions historiken för det önskade tillägget för tillstånds konfiguration (DSC) i Azure.
ms.date: 02/17/2021
keywords: DSC, PowerShell, Azure, tillägg
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: e51bce6e015ef7367625b010b74e5d2422b35051
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100651810"
---
# <a name="work-with-azure-desired-state-configuration-extension-version-history"></a>Arbeta med Azure Desired State Configuration Extension versions historik

Det virtuella Azure- [tillägget](../virtual-machines/extensions/dsc-overview.md) för önskad tillstånds konfiguration (DSC) uppdateras som-nödvändigt för att stödja förbättringar och nya funktioner som levereras av Azure, Windows Server och Windows Management Framework (WMF) som innehåller Windows PowerShell.

Den här artikeln innehåller information om varje version av Azure DSC VM-tillägget, vilka miljöer som stöds och kommentarer och kommentarer om nya funktioner eller ändringar.

## <a name="latest-version"></a>Senaste version

### <a name="version-283"></a>Version 2,83

- **Utgivnings datum:**
  - Februari 2021
- **Stöd för operativsystem:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows-klient 7/8.1/10
  - Nano Server
- **WMF-support:**
  - WMF 5.1
  - WMF 5,0 RTM
  - WMF 4,0-uppdatering
  - WMF 4,0
- **Miljö**
  - Azure
  - Azure Kina Vianet 21
  - Azure Government
- **Anmärkningar:** Den här versionen innehåller en korrigering för osignerade binärfiler med Windows VM-tillägget.

### <a name="version-280"></a>Version 2,80

- **Utgivnings datum:**
  - 26 september, sep-2019 (Azure) | 6 juli 2020 (Azure Kina Vianet 21) | 20 juli 2020 (Azure Government)
- **Stöd för operativsystem:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows-klient 7/8.1/10
  - Nano Server
- **WMF-support:**
  - WMF 5.1
  - WMF 5,0 RTM
  - WMF 4,0-uppdatering
  - WMF 4,0
- **Miljö**
  - Azure
  - Azure Kina Vianet 21
  - Azure Government
- **Anmärkningar:** Inga nya funktioner ingår i den här versionen.

### <a name="version-276"></a>Version 2,76

- **Utgivnings datum:**
  - 9 maj 2018 (Azure) | 21 juni 2018 (Azure Kina Vianet 21, Azure Government)
- **Stöd för operativsystem:**
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows-klient 7/8.1/10
  - Nano Server
- **WMF-support:**
  - WMF 5.1
  - WMF 5,0 RTM
  - WMF 4,0-uppdatering
  - WMF 4,0
- **Miljö**
  - Azure
  - Azure Kina Vianet 21
  - Azure Government
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016; för andra Windows-OSs installeras [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installation av WMF kräver en omstart). För Nano Server installeras DSC-rollen på den virtuella datorn.
- **Nya funktioner:**
  - Förbättringar i tilläggets metadata för under status och andra mindre fel korrigeringar.

## <a name="supported-versions"></a>Versioner som stöds

> [!WARNING]
> Version 2,4 till 2,13 Använd WMF 5,0 offentlig för hands version, vars signerings certifikat upphör att gälla i augusti 2016.
> Mer information om det här problemet finns i följande [blogg artikel](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/).

### <a name="version-275"></a>Version 2.75

- **Utgivnings datum:** 5 mars 2018
- **OS-support:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-klient 7/8.1/10, Nano Server
- **WMF-support:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Miljö:** Azure
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016; för andra Windows-OSs installeras [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installation av WMF kräver en omstart). För Nano Server installeras DSC-rollen på den virtuella datorn.
- **Nya funktioner:**
  - När GitHub har tvingat fram TLS 1,2 kan du inte publicera en virtuell dator för att Azure Automation tillstånds konfiguration med hjälp av gör det själv Resource Manager-mallar som är tillgängliga på Azure Marketplace eller använda DSC-tillägget för att hämta konfigurationer som finns på GitHub. Ett fel som liknar följande medan distributionen av tillägget returneras:

    ```json
    {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details": [{
            "code": "Conflict",
            "message": "{
                \"status\": \"Failed\",
                \"error\": {
                    \"code\": \"ResourceDeploymentFailure\",
                    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",
                    \"details\": [ {
                        \"code\": \"VMExtensionProvisioningError\",
                        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'.
                        Error message: \\\"The DSC Extension failed to execute: Error downloading
                        https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip
                        after 29 attempts: The request was aborted: Could not create SSL/TLS secure channel..\\nMore information about the failure can
                        be found in the logs located under 'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.74.0.0' on the VM.\\\".\"
                    } ]
                }
            }"
        }]
    }
    ```

  - I den nya tilläggs versionen tillämpas nu TLS 1,2. Vid distribution av tillägget, om du redan har angett `AutoUpgradeMinorVersion = true` i Resource Manager-mallen, uppgraderas tillägget till 2,75. För manuella uppdateringar anger `TypeHandlerVersion = 2.75` du i din Resource Manager-mall.

### <a name="version-270---272"></a>Version 2,70-2,72

- **Utgivnings datum:** 13 november 2017
- **OS-support:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-klient 7/8.1/10, Nano Server
- **WMF-support:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Miljö:** Azure
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016; för andra Windows-OSs installeras [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installation av WMF kräver en omstart). För Nano Server installeras DSC-rollen på den virtuella datorn.
- **Nya funktioner:**
  - Fel korrigeringar & förbättringar som fören klar användningen av Azure Automations tillstånds konfiguration i portalen och med en Resource Manager-mall. Mer information finns i [standard konfigurations skript](../virtual-machines/extensions/dsc-overview.md) i dokumentationen för DSC-tillägget.

### <a name="version-226"></a>Version 2.26

- **Utgivnings datum:** 9 juni 2017
- **OS-support:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-klient 7/8.1/10, Nano Server
- **WMF-support:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Miljö:** Azure
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016; för andra Windows-OSs installeras [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installation av WMF kräver en omstart). För Nano Server installeras DSC-rollen på den virtuella datorn.
- **Nya funktioner:**
  - Förbättringar av telemetri.

### <a name="version-225"></a>Version 2.25

- **Utgivnings datum:** 2 juni 2017
- **OS-support:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-klient 7/8.1/10, Nano Server
- **WMF-support:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Miljö:** Azure
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016; för andra Windows-OSs installeras [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installation av WMF kräver en omstart). För Nano Server installeras DSC-rollen på den virtuella datorn.
- **Nya funktioner:**
  - Flera fel korrigeringar och andra mindre förbättringar har lagts till.

### <a name="version-224"></a>Version 2.24

- **Utgivnings datum:** 13 april 2017
- **OS-support:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **WMF-support:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Miljö:** Azure
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016; för andra Windows-OSs installeras [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installation av WMF kräver en omstart). För Nano Server installeras DSC-rollen på den virtuella datorn.
- **Nya funktioner:**
  - Exponerar VM-UUID & DSC-agent-ID som tilläggs-metadata. Andra mindre förbättringar har lagts till.

### <a name="version-223"></a>Version 2.23

- **Utgivnings datum:** 15 mars 2017
- **OS-support:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **WMF-support:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Miljö:** Azure
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016; för andra Windows-OSs installeras [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installation av WMF kräver en omstart). För Nano Server installeras DSC-rollen på den virtuella datorn.
- **Nya funktioner:**
  - Fel korrigeringar och andra förbättringar har lagts till.

### <a name="version-222"></a>Version 2.22

- **Utgivnings datum:** 8 februari 2017
- **OS-support:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **WMF-support:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Miljö:** Azure
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016; för andra Windows-OSs installeras [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (installation av WMF kräver en omstart). För Nano Server installeras DSC-rollen på den virtuella datorn.
- **Nya funktioner:**
  - DSC-tillägget stöder nu WMF 5,1.
  - Mindre andra förbättringar har lagts till.

### <a name="version-221"></a>Version 2.21

- **Utgivnings datum:** 2 december 2016
- **OS-support:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **WMF-support:** WMF 5,1 Preview, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Miljö:** Azure
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016; för andra Windows-OSs installeras [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installation av WMF kräver en omstart). För Nano Server installeras DSC-rollen på den virtuella datorn.
- **Nya funktioner:**
  - DSC-tillägget är nu tillgängligt på Nano Server. Den här versionen innehåller främst kod ändringar för att köra tillägget på Nano Server.
  - Mindre andra förbättringar har lagts till.

### <a name="version-220"></a>Version 2.20

- **Utgivnings datum:** 2 augusti 2016
- **OS-support:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-support:** WMF 5,1 Preview, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Miljö:** Azure
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016 Technical Preview; för andra Windows-OSs installeras [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installation av WMF kräver en omstart).
- **Nya funktioner:**
  - Stöd för för hands versionen av WMF 5,1. Vid den första publiceringen var den här versionen en valfri uppgradering och du måste ange Wmfversion = ' 5,1 PP ' i Resource Manager-mallar för att installera WMF 5,1 Preview.
    Wmfversion = "senaste" installerar fortfarande [WMF 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/).
    Mer information om för hands versionen av WMF 5,1 finns i [den här bloggen](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/).
  - Mindre andra korrigeringar och förbättringar har lagts till.

### <a name="version--219"></a>Version 2,19

- **Utgivnings datum:** 3 juni 2016
- **OS-support:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-support:** WMF 5,0 RTM, WMF 4,0 uppdatering, WMF 4,0
- **Miljö:** Azure, Azure Kina Vianet 21, Azure Government
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016 Technical Preview; för andra Windows-OSs installeras [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installation av WMF kräver en omstart).
- **Nya funktioner:**
  - DSC-tillägget är nu tillgängligt i Azure Kina Vianet 21. Den här versionen innehåller korrigeringar för att köra tillägget på Azure Kina Vianet 21.

### <a name="version-218"></a>Version 2.18

- **Utgivnings datum:** 3 juni 2016
- **OS-support:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-support:** WMF 5,0 RTM, WMF 4,0 uppdatering, WMF 4,0
- **Miljö:** Azure
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016 Technical Preview; för andra Windows-OSs installeras [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installation av WMF kräver en omstart).
- **Nya funktioner:**
  - Gör telemetri icke-blockerande om ett fel inträffar under hämtningen av snabb korrigering för telemetri (känt Azure DNS problem) eller under installationen.
  - Åtgärda det tillfälliga problemet när tillägget slutar att bearbeta konfigurationen efter en omstart. Detta gjorde att DSC-tillägget är kvar i läget "över gång".
  - Mindre andra korrigeringar och förbättringar har lagts till.

### <a name="version-217"></a>Version 2.17

- **Utgivnings datum:** 26 april 2016
- **OS-support:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-support:** WMF 5,0 RTM, WMF 4,0 uppdatering, WMF 4,0
- **Miljö:** Azure
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016 Technical Preview; för andra Windows-OSs installeras [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installation av WMF kräver en omstart).
- **Nya funktioner:**
  - Stöd för WMF 4,0-uppdatering. Mer information om WMF 4,0-uppdateringen finns i [den här bloggen](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/).
  - Försök att använda logiken på fel som inträffar under DSC-tillägget installera eller tillämpa en installation av DSC-konfiguration efter tillägg. Som en del av den här ändringen kommer tillägget att försöka installera igen om en tidigare installation misslyckades eller att införa en ny DSC-konfiguration som tidigare har misslyckats, under högst tre gånger tills den når slut för ande tillstånd (lyckades/fel) eller om en ny begäran kommer. Om tillägget Miss lyckas på grund av ogiltiga användar inställningar/användarindata, görs inget nytt försök. I det här fallet måste tillägget anropas igen med en ny begäran och rätt användar inställningar. 

  > [!NOTE]
   > DSC-tillägget är beroende av Azure VM-agenten för nya försök. Azure VM-agenten anropar tillägget med den senaste misslyckade begäran tills den når ett lyckat eller fel tillstånd.

### <a name="version-216"></a>Version 2.16

- **Utgivnings datum:** 21 april 2016
- **OS-support:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-support:** WMF 5,0 RTM, WMF 4,0
- **Miljö:** Azure
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016 Technical Preview; för andra Windows-OSs installeras [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installation av WMF kräver en omstart).
- **Nya funktioner:**
  - Förbättring av fel hantering och andra mindre fel korrigeringar.
  - Ny egenskap i inställningar för DSC-tillägg. `ForcePullAndApply` i AdvancedOptions har lagts till för att aktivera DSC-tillägget implementera DSC-konfigurationer när uppdaterings läget är pull (till skillnad från standard push-läget). Mer information om inställningarna för DSC-tillägg finns i [den här bloggen](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/).

### <a name="version-215"></a>Version 2.15

- **Utgivnings datum:** 14 mars 2016
- **OS-support:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-support:** WMF 5,0 RTM, WMF 4,0
- **Miljö:** Azure
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016 Technical Preview; för andra Windows-OSs installeras [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installation av WMF kräver en omstart).
- **Nya funktioner:**
  - I tillägg version 2,14 inkluderades ändringar i installationen av WMF RTM. När du uppgraderar från tilläggets version 2.13.2.0 till 2.14.0.0, kan det hända att vissa DSC-cmdlets Miss lyckas eller att konfigurationen Miss lyckas med ett fel – "ingen instans hittades med de tilldelade egenskapsvärdena". Mer information finns i viktig information om [DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc). Lösningarna för de här problemen har lagts till i 2,15-versionen. 
  - Om du redan har installerat version 2,14 och körs på något av ovanstående två problem, måste du utföra dessa steg manuellt. Kör följande kommandon i en upphöjd PowerShell-session:
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>Version 2.14

- **Utgivnings datum:** 25 februari 2016
- **OS-support:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-support:** WMF 5,0 RTM, WMF 4,0
- **Miljö:** Azure
- **Anmärkningar:** Den här versionen använder DSC som ingår i Windows Server 2016 Technical Preview; för andra Windows-OSs installeras [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installation av WMF kräver en omstart).
- **Nya funktioner:**
  - Använder WMF RTM.
  - Möjliggör insamling av data för att förbättra kvaliteten på DSC-tillägget. Mer information finns i den här [blogg artikeln](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/).
  - Innehåller ett uppdaterat inställnings format för tillägget i en Resource Manager-mall. Mer information finns i den här [blogg artikeln](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/).
  - Fel korrigeringar och andra förbättringar.

## <a name="next-steps"></a>Nästa steg

- Mer information om PowerShell DSC finns i [PowerShell Documentation Center](/powershell/scripting/dsc/overview/overview).
- Granska [Resource Manager-mallen för DSC-tillägget](../virtual-machines/extensions/dsc-template.md).
- För andra funktioner och resurser som du kan hantera med PowerShell DSC kan du bläddra i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Mer information om hur du skickar känsliga parametrar till konfigurationer finns i [Hantera autentiseringsuppgifter säkert med DSC-tilläggs hanteraren](../virtual-machines/extensions/dsc-credentials.md).
