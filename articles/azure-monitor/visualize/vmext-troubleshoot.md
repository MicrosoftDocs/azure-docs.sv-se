---
title: Felsöka Azure Log Analytics VM-tillägg
description: Beskriv symptom, orsaker och lösningar för de vanligaste problemen med Log Analytics VM-tillägget för virtuella Windows-och Linux Azure-datorer.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: c499633931bd44fed5372f1f05521980f0b61a85
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621539"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Felsöka Log Analytics-tillägget för virtuella datorer i Azure Monitor
Den här artikeln innehåller fel söknings fel som kan uppstå i Log Analytics VM-tillägget för virtuella Windows-och Linux-datorer som körs på Microsoft Azure och ger förslag på möjliga lösningar för att lösa dem.

För att kontrol lera status för tillägget, utför följande steg från Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **virtuella datorer**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Virtuella datorer**.
3. Leta upp och markera den i listan över virtuella datorer.
3. Klicka på **tillägg** på den virtuella datorn.
4. I listan kontrollerar du om Log Analytics tillägget är aktiverat eller inte.  För Linux visas-agenten som **OMSAgentforLinux** och för Windows visas agenten som **MicrosoftMonitoringAgent**.

   ![Vyn VM-tillägg](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Klicka på tillägget för att visa information. 

   ![Information om VM-tillägg](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Felsöka Azure Windows VM-tillägg

Om VM-tillägget för *Microsoft Monitoring Agent* inte installeras eller rapporteras, kan du utföra följande steg för att felsöka problemet.

1. Kontrol lera om Azure VM-agenten är installerad och fungerar korrekt med hjälp av stegen i [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Du kan också granska logg filen för VM-agenten `C:\WindowsAzure\logs\WaAppAgent.log`
   * Om loggen inte finns installeras inte VM-agenten.
   * [Installera Azure VM-agenten](../vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Granska loggfilerna för VM-tillägg för Microsoft Monitoring Agent i `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Se till att den virtuella datorn kan köra PowerShell-skript
4. Se till att behörigheter för C:\Windows\temp inte har ändrats
5. Visa status för Microsoft Monitoring Agent genom att skriva följande i ett upphöjd PowerShell-fönster på den virtuella datorn `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Granska loggfilerna för installation av Microsoft Monitoring Agent i `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Mer information finns i [Felsöka Windows-tillägg](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Felsöka VM-tillägg för Linux
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Om *Log Analytics agent för* VM-tillägget för Linux inte installeras eller rapporteras, kan du utföra följande steg för att felsöka problemet.

1. Om tilläggets status är *okänd* kontrollerar du om Azure VM-agenten är installerad och fungerar korrekt genom att granska logg filen för VM-agenten `/var/log/waagent.log`
   * Om loggen inte finns installeras inte VM-agenten.
   * [Installera Azure VM-agenten på virtuella Linux-datorer](../../virtual-machines/extensions/agent-linux.md#installation)
2. Om du har andra statusar som inte är felfria kontrollerar du Log Analytics agent för Linux VM-tillägg loggfiler i `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` och `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Om tilläggets status är felfri, men data inte överförs, granska Log Analytics agent för Linux-loggfiler i `/var/opt/microsoft/omsagent/log/omsagent.log`

Mer information finns i [Felsöka Linux-tillägg](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Nästa steg

Ytterligare fel söknings anvisningar som rör Log Analytics-agenten för Linux som finns på datorer utanför Azure finns i [Felsöka Azure Log Analytics Linux-agenten](../agents/agent-linux-troubleshoot.md).