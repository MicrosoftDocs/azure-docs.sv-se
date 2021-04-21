---
title: Ansluta hybriddatorer till Azure med hjälp av PowerShell
description: I den här artikeln får du lära dig hur du installerar agenten och ansluter en dator till Azure med Azure Arc aktiverade servrar. Du kan göra detta med PowerShell.
ms.date: 10/28/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d6963a53ac14c9d6727a8d53e781bc8b8389b76e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831626"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Ansluta hybriddatorer till Azure med hjälp av PowerShell

För servrar som är Azure Arc kan du vidta manuella åtgärder för att aktivera dem för en eller flera Windows- eller Linux-datorer i din miljö. Du kan också använda PowerShell-cmdleten [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) för att ladda ned Connected Machine-agenten, installera agenten och registrera datorn med Azure Arc. Cmdleten laddar ned Windows-agentpaketet (Windows Installer) från Microsoft Download Center och Linux-agentpaketet från Microsoft-paketdatabasen.

Den här metoden kräver att du har administratörsbehörighet på datorn för att installera och konfigurera agenten. I Linux använder du rotkontot och i Windows är du medlem i gruppen Lokala administratörer. Du kan slutföra den här processen interaktivt eller via fjärrkommunikation på en Windows-server med [hjälp av PowerShell-fjärrkommunikation.](/powershell/scripting/learn/ps101/08-powershell-remoting)

Innan du börjar granskar du kraven [och kontrollerar](agent-overview.md#prerequisites) att din prenumeration och dina resurser uppfyller kraven. Information om regioner som stöds och andra relaterade överväganden finns i [Azure-regioner som stöds.](overview.md#supported-regions)

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- En dator med Azure PowerShell. Anvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/).

Du använder PowerShell för att hantera VM-tillägg på dina hybridservrar som hanteras Azure Arc aktiverade servrar. Innan du använder PowerShell installerar du `Az.ConnectedMachine` modulen. Kör följande kommando på servern som är aktiverad med Azure Arc:

```powershell
Install-Module -Name Az.ConnectedMachine
```

När installationen är klar visas följande meddelande:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Installera agenten och ansluta till Azure

1. Öppna en PowerShell-konsol med utökade privilegier.

2. Logga in på Azure genom att köra kommandot `Connect-AzAccount` .

3. Om du vill installera connected machine-agenten `Connect-AzConnectedMachine` använder du med `-Name` `-ResourceGroupName` parametrarna , och `-Location` . Använd `-SubscriptionId` parametern för att åsidosätta standardprenumerationen på grund av Azure-kontexten som skapades efter inloggningen. Kör något av följande kommandon:

    * Om du vill installera Connected Machine-agenten på måldatorn som kan kommunicera direkt med Azure kör du:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * Om du vill installera Connected Machine-agenten på måldatorn som kommunicerar via en proxyserver kör du:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

Om agenten inte kan starta när installationen är klar kontrollerar du loggarna för detaljerad felinformation. I Windows kontrollerar du den här filen: *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. I Linux kontrollerar du den här filen: */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>Installera och ansluta med Hjälp av PowerShell-fjärrkommunikation

Så här konfigurerar du en eller flera Windows-servrar med servrar som är aktiverade med Azure Arc. Du måste aktivera PowerShell-fjärrkommunikation på fjärrdatorn. Gör detta med hjälp av cmdleten `Enable-PSRemoting`.

1. Öppna en PowerShell-konsol som administratör.

2. Logga in på Azure genom att köra kommandot `Connect-AzAccount` .

3. Om du vill installera Connected Machine-agenten `Connect-AzConnectedMachine` använder du med `-ResourceGroupName` parametrarna , och `-Location` . Azure-resursnamnen använder automatiskt värdnamnet för varje server. Använd `-SubscriptionId` parametern för att åsidosätta standardprenumerationen på grund av Azure-kontexten som skapades efter inloggningen.

    * Kör följande kommando för att installera Connected Machine-agenten på måldatorn som kan kommunicera direkt med Azure:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Om du vill installera Connected Machine-agenten på flera fjärrdatorer samtidigt lägger du till en lista över fjärrdatornamn, var och en avgränsad med ett kommatecken.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    I följande exempel visas resultatet av kommandot som riktar in sig på en enskild dator:
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Kontrollera anslutningen med Azure Arc

När du har installerat och konfigurerat agenten för registrering med Azure Arc aktiverade servrar går du till Azure Portal för att kontrollera att servern har anslutits. Visa datorn i [Azure Portal](https://portal.azure.com).

![Skärmbild av instrumentpanelen Servrar som visar en lyckad serveranslutning.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nästa steg

* Om det behövs kan du gå [till guiden Felsök Connected Machine-agenten.](troubleshoot-agent-onboard.md)

* Lär dig hur du hanterar din dator med hjälp [av Azure Policy](../../governance/policy/overview.md). Du kan använda [gästkonfiguration för virtuella](../../governance/policy/concepts/guest-configuration.md)datorer, kontrollera att datorn rapporterar till den förväntade Log Analytics-arbetsytan och aktivera övervakning [med Azure Monitor med virtuella datorer](../../azure-monitor/vm/vminsights-enable-policy.md).

* Läs mer om [Log Analytics-agenten](../../azure-monitor/agents/log-analytics-agent.md). Log Analytics-agenten för Windows och Linux krävs när du vill samla in övervakningsdata för operativsystem och arbetsbelastningar, eller hantera dem med hjälp av Azure Automation-runbooks eller funktioner som Uppdateringshantering. Den här agenten måste också använda andra Azure-tjänster, till exempel [Azure Security Center](../../security-center/security-center-introduction.md).
