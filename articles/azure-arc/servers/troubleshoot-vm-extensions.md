---
title: Felsök problem med VM-tillägg för Azure Arc-aktiverade servrar
description: Den här artikeln beskriver hur du felsöker och löser problem med Azure VM-tillägg som uppstår med Azure Arc-aktiverade servrar.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 799e5c8ec00a894c6a54c64494edd8f259faf2dc
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584674"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>Felsök problem med ARC-aktiverade VM-tillägg

Den här artikeln innehåller information om hur du felsöker och löser problem som kan uppstå vid försök att distribuera eller ta bort virtuella Azure-tillägg på Arc-aktiverade servrar. Allmän information finns i [Hantera och använda Azure VM-tillägg](./manage-vm-extensions.md).

## <a name="general-troubleshooting"></a>Allmän fel sökning

Information om tillstånd för tilläggs distributioner kan hämtas från Azure Portal.

Följande fel söknings steg gäller för alla VM-tillägg.

1. Kontrol lera gäst agents loggen genom att titta på aktiviteten när ditt tillägg har allokerats i `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` för Windows, och för Linux under `/var/lib/GuestConfig/ext_mgr_logs` .

2. Kontrol lera tilläggs loggarna för det angivna tillägget för mer information i `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` för Windows. Utökning av utdata loggas till en fil för varje tillägg som installeras på Linux under `/var/lib/GuestConfig/extension_logs` .

3. Sök efter felkoder, kända problem osv. i tilläggs dokumentation fel söknings avsnitt. Ytterligare felsöknings information för varje tillägg finns i avsnittet **fel sökning och support** i Översikt över tillägget. Detta inkluderar beskrivningen av felkoder som skrivs till loggen. Tilläggs artiklarna är länkade i tilläggs [tabellen](manage-vm-extensions.md#extensions).

4. Titta på system loggarna. Kontrol lera om det finns andra åtgärder som kan ha stör tillägget, till exempel en tids krävande installation av ett annat program som kräver exklusiv åtkomst till paket hanteraren.

## <a name="troubleshooting-specific-extension-scenarios"></a>Felsöka vissa scenarier för tillägg

### <a name="vm-insights"></a>VM-insikter

- När du aktiverar VM Insights för en Azure Arc-aktiverad Server installeras beroende och Log Analytics agenten. På en långsam dator eller en med långsam nätverks anslutning är det möjligt att se tids gränser under installations processen. Microsoft tar stegen för att åtgärda detta i den anslutna dator agenten för att förbättra det här tillståndet. Under tiden kan ett nytt försök att installera igen lyckas.

### <a name="log-analytics-agent-for-linux"></a>Log Analytics agent för Linux

- 1.13.9-agentens versions nummer (motsvarande tilläggs version är 1.13.15) har inte korrekt märkning av överförda data med resurs-ID för den Azure Arc-aktiverade servern. Log Analytics Även om loggar skickas till tjänsten och du försöker visa data från den valda aktiverade servern efter att ha valt **loggar** eller **insikter** returneras inga data. Du kan visa dess data genom att köra frågor från Azure Monitor loggar eller från Azure Monitor for VMs, som är begränsade till arbets ytan.

- Vissa distributioner stöds för närvarande inte av Log Analytics agent för Linux. Agenten kräver att ytterligare beroenden installeras, inklusive python 2. Granska support mat ris och nödvändiga komponenter [här](../../azure-monitor/agents/agents-overview.md#supported-operating-systems).

- Felkod 52 i status meddelandet indikerar ett saknas beroende. Se utdata och loggfiler för mer information om vilket beroende som saknas.

- Om en installation Miss lyckas läser du avsnittet **fel sökning och support** i översikten över tillägget. I de flesta fall finns det en felkod som ingår i status meddelandet. För Log Analytics agent för Linux beskrivs status meddelanden [här](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support), tillsammans med allmän felsöknings information för det här VM-tillägget.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet här eller om du inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

- Få svar från Azure-experter via [Microsoft Q&A](/answers/topics/azure-arc.html).

- Anslut till [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Azure-support ansluter Azure-communityn till svar, support och experter.

- Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/)och välj **få support**.
