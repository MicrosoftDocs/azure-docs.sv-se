---
title: Köra skript på en virtuell Azure Windows-dator
description: Det här avsnittet beskriver hur du kör skript på en virtuell Windows-dator
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: 7bf62eb2ab8d2ce82ce73e3e8ae26cf303b8ba67
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765889"
---
# <a name="run-scripts-in-your-windows-vm"></a>Köra skript på din virtuella Windows-dator

Om du vill automatisera uppgifter eller felsöka problem kan du behöva köra kommandon på en virtuell dator. Följande artikel ger en kort översikt över de funktioner som är tillgängliga för att köra skript och kommandon på dina virtuella datorer.

## <a name="custom-script-extension"></a>Anpassat skripttillägg

Det [anpassade skripttillägget](../extensions/custom-script-windows.md) används främst för konfiguration efter distribution och programinstallation.

* Ladda ned och kör skript på virtuella Azure-datorer.
* Kan köras med hjälp Azure Resource Manager-mallar, Azure CLI, REST API, PowerShell eller Azure Portal.
* Skriptfiler kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas från din dator när de körs från Azure Portal.
* Kör PowerShell-skript på Windows-datorer och Bash-skript på Linux-datorer.
* Användbart för konfiguration efter distribution, programvaruinstallation och andra konfigurations- eller hanteringsuppgifter.

## <a name="run-command"></a>Kör kommandot 

Funktionen [Kör kommando](run-command.md) möjliggör hantering av virtuella datorer och program och felsökning med hjälp av skript och är tillgänglig även om datorn inte kan nås, till exempel om gästbrandväggen inte har RDP- eller SSH-porten öppen.

* Köra skript på virtuella Azure-datorer.
* Kan köras med [Azure Portal,](run-command.md) [REST API,](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) [Azure CLI](/cli/azure/vm/run-command#az_vm_run_command_invoke)eller [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand)
* Kör snabbt ett skript och visa utdata och upprepa efter behov i Azure Portal.
* Skriptet kan skrivas direkt eller så kan du köra något av de inbyggda skripten.
* Kör PowerShell-skript på Windows-datorer och Bash-skript på Linux-datorer.
* Användbart för hantering av virtuella datorer och program och för att köra skript på virtuella datorer som inte kan nås.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Den [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) ger allmän dator-, program- och miljöhantering med användarens anpassade skript som lagras i ett Automation-konto.

* Kör skript på Azure- och icke-Azure-datorer.
* Kan köras med Azure Portal, Azure CLI, REST API, PowerShell och webhook.
* Skript som lagras och hanteras i ett Automation-konto.
* Köra PowerShell, PowerShell Workflow, Python eller grafiska runbooks
* Ingen tidsgräns för skriptkörningstid.
* Flera skript kan köras samtidigt.
* Fullständiga skriptutdata returneras och lagras.
* Jobbhistorik som är tillgänglig i 90 dagar.
* Skript kan köras som lokalt system eller med autentiseringsuppgifter som angetts av användaren.
* Kräver [manuell installation](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Seriekonsol

Den [Seriell konsol](/troubleshoot/azure/virtual-machines/serial-console-windows) ger direktåtkomst till en virtuell dator, ungefär som att ha ett tangentbord som är anslutet till den virtuella datorn.

* Kör kommandon på virtuella Azure-datorer.
* Kan köras med hjälp av en textbaserad konsol till datorn i Azure Portal.
* Logga in på datorn med ett lokalt användarkonto.
* Användbart när åtkomst till den virtuella datorn krävs oavsett datorns nätverks- eller operativsystemstillstånd.

## <a name="next-steps"></a>Nästa steg

Läs mer om de olika funktioner som är tillgängliga för att köra skript och kommandon på dina virtuella datorer.

* [Anpassat skripttillägg](../extensions/custom-script-windows.md)
* [Kör kommando](run-command.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)
* [Seriell konsol](/troubleshoot/azure/virtual-machines/serial-console-windows)