---
title: Ansluta hybrid datorer till Azure med hjälp av PowerShell
description: I den här artikeln får du lära dig hur du installerar agenten och ansluter en dator till Azure med hjälp av Azure Arc-aktiverade servrar. Du kan göra detta med PowerShell.
ms.date: 10/28/2020
ms.topic: conceptual
ms.openlocfilehash: 07a00de9077378ce3e3f7a7578b66e93d1b04f2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584939"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Ansluta hybrid datorer till Azure med hjälp av PowerShell

För servrar som är aktiverade med Azure ARC kan du utföra manuella steg för att aktivera dem för en eller flera Windows-eller Linux-datorer i din miljö. Du kan också använda PowerShell-cmdleten [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) för att ladda ned den anslutna dator agenten, installera agenten och registrera datorn med Azure-bågen. Cmdlet: en laddar ned Windows agent-paketet (Windows Installer) från Microsoft Download Center och Linux Agent-paketet från Microsoft Package-lagringsplatsen.

Den här metoden kräver att du har administratörs behörighet på datorn för att installera och konfigurera agenten. I Linux, med hjälp av rot kontot och i Windows, är du medlem i den lokala gruppen Administratörer. Du kan slutföra den här processen interaktivt eller via fjärr anslutning på en Windows-Server med hjälp av [PowerShell-fjärrkommunikation](/powershell/scripting/learn/ps101/08-powershell-remoting).

Innan du börjar ska du läsa igenom [kraven och kontrol](agent-overview.md#prerequisites) lera att din prenumeration och dina resurser uppfyller kraven. Information om regioner som stöds och andra relaterade överväganden finns i [Azure-regioner som stöds](overview.md#supported-regions).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- En dator med Azure PowerShell. Anvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/).

Du använder PowerShell för att hantera VM-tillägg på dina hybrid servrar som hanteras av Azure Arc-aktiverade servrar. Installera modulen innan du använder PowerShell `Az.ConnectedMachine` . Kör följande kommando på servern som är aktive rad med Azure Arc:

```powershell
Install-Module -Name Az.ConnectedMachine
```

När installationen är klar visas följande meddelande:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Installera agenten och Anslut till Azure

1. Öppna en PowerShell-konsol med utökade privilegier.

2. Logga in på Azure genom att köra kommandot `Connect-AzAccount` .

3. Om du vill installera den anslutna dator agenten använder `Connect-AzConnectedMachine` du med `-Name` `-ResourceGroupName` parametrarna,, och `-Location` . Använd `-SubscriptionId` parametern för att åsidosätta standard prenumerationen som ett resultat av Azure-kontexten som skapats efter inloggning. Kör något av följande kommandon:

    * Kör följande om du vill installera den anslutna dator agenten på mål datorn som kan kommunicera direkt med Azure:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * Om du vill installera den anslutna dator agenten på mål datorn som kommunicerar via en proxyserver kör du:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

Om agenten inte startar efter att installationen har slutförts, kontrollerar du i loggarna om det finns detaljerad fel information. I Windows kontrollerar du den här filen: *%programdata%\AzureConnectedMachineAgent\Log\himds.log*. I Linux kontrollerar du den här filen: */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>Installera och Anslut med hjälp av PowerShell-fjärrkommunikation

Så här konfigurerar du en eller flera Windows-servrar med servrar aktiverade med Azure Arc. Du måste aktivera PowerShell-fjärrkommunikation på fjärrdatorn. Gör detta med hjälp av cmdleten `Enable-PSRemoting`.

1. Öppna en PowerShell-konsol som administratör.

2. Logga in på Azure genom att köra kommandot `Connect-AzAccount` .

3. Om du vill installera den anslutna dator agenten använder `Connect-AzConnectedMachine` du med `-ResourceGroupName` parametrarna, och `-Location` . Azure-resursens namn kommer automatiskt att använda värd namnet för varje server. Använd `-SubscriptionId` parametern för att åsidosätta standard prenumerationen som ett resultat av Azure-kontexten som skapats efter inloggning.

    * Kör följande kommando för att installera den anslutna dator agenten på mål datorn som kan kommunicera direkt med Azure:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Om du vill installera den anslutna dator agenten på flera fjärrdatorer samtidigt lägger du till en lista över fjärrdatornamn, varje avgränsade med kommatecken.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    I följande exempel visas resultatet av kommandot som riktar sig mot en enskild dator:
    
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

När du har installerat och konfigurerat agenten för att registrera dig för Azure Arc-aktiverade servrar går du till Azure Portal för att kontrol lera att servern har anslutits. Visa datorn i [Azure Portal](https://portal.azure.com).

![Skärm bild av instrument panelen för servrar med en lyckad Server anslutning.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nästa steg

* Om det behövs kan du läsa [fel söknings guiden för anslutna dator agenter](troubleshoot-agent-onboard.md).

* Lär dig hur du hanterar din dator med hjälp av [Azure policy](../../governance/policy/overview.md). Du kan använda [gäst konfiguration](../../governance/policy/concepts/guest-configuration.md)för virtuella datorer, kontrol lera att datorn rapporterar till den förväntade Log Analytics arbets ytan och aktiverar övervakning med [Azure monitor med virtuella datorer](../../azure-monitor/vm/vminsights-enable-policy.md).

* Läs mer om den [Log Analytics agenten](../../azure-monitor/agents/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs när du vill samla in operativ system och data för övervakning av arbets belastning, eller hantera dem med hjälp av Azure Automation runbooks eller funktioner som Uppdateringshantering. Den här agenten måste också använda andra Azure-tjänster, t. ex. [Azure Security Center](../../security-center/security-center-introduction.md).
