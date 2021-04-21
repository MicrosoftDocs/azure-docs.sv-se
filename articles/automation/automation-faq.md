---
title: Azure Automation vanliga frågor och svar | Microsoft Docs
description: Den här artikeln ger svar på vanliga frågor om Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9ab4ce9b8691f27fe392d2e3099677d45900d2e3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834236"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation vanliga frågor och svar

Dessa vanliga frågor och svar från Microsoft är en lista över vanliga frågor om Azure Automation. Om du har andra frågor om dess funktioner kan du gå till diskussionsforumet och ställa dina frågor. När en fråga ställs ofta lägger vi till den i den här artikeln så att den snabbt och enkelt kan hittas.

## <a name="update-management"></a>Uppdateringshantering

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Kan jag förhindra oväntade uppgraderingar på operativsystemnivå?

På vissa Linux-varianter, till exempel Red Hat Enterprise Linux, kan uppgraderingar på operativsystemnivå ske via paket. Detta kan leda till Uppdateringshantering körs där operativsystemets versionsnummer ändras. Eftersom Uppdateringshantering använder samma metoder för att uppdatera paket som en administratör använder lokalt på en Linux-dator är det här beteendet avsiktligt.

Om du vill undvika att uppdatera os-Uppdateringshantering distributioner använder du **undantagsfunktionen.**

I Red Hat Enterprise Linux är paketnamnet som ska undantas `redhat-release-server.x86_64` .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Varför tillämpas inte kritiska uppdateringar/säkerhetsuppdateringar?

När du distribuerar uppdateringar till en Linux-dator kan du välja uppdateringsklassificering. Det här alternativet filtrerar de uppdateringar som uppfyller de angivna kriterierna. Det här filtret tillämpas lokalt på datorn när uppdateringen distribueras.

Eftersom Uppdateringshantering utför uppdateringsberikning i molnet kan du flagga vissa uppdateringar i Uppdateringshantering som har en säkerhetspåverkan, även om den lokala datorn inte har den informationen. Om du tillämpar kritiska uppdateringar på en Linux-dator kan det finnas uppdateringar som inte har markerats som säkerhetspåverkan på den datorn och därför inte tillämpas. Men Uppdateringshantering fortfarande rapportera datorn som inkompatibla eftersom den har ytterligare information om den relevanta uppdateringen.

Distribution av uppdateringar efter uppdateringsklassificering fungerar inte på RTM-versioner av CentOS. Om du vill distribuera uppdateringar för CentOS korrekt väljer du alla klassificeringar för att se till att uppdateringarna tillämpas. För SUSE kan du välja **ENDAST** andra uppdateringar eftersom klassificeringen kan installera vissa andra säkerhetsuppdateringar om de är relaterade till zypper (pakethanteraren) eller dess beroenden först. Det här beteendet är en begränsning på zypper. I vissa fall kan du behöva köra uppdateringsdistributionen igen och sedan verifiera distributionen via uppdateringsloggen.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Kan jag distribuera uppdateringar mellan Azure-klienter?

Om du har datorer som behöver korrigeras i en annan Azure-klientorganisation som rapporterar till Uppdateringshantering måste du använda en lösning för att schemalägga dem. Du kan använda cmdleten [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) med `ForUpdateConfiguration` parametern angiven för att skapa ett schema. Du kan använda cmdleten [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) och skicka datorerna i den andra klientorganisationen till `NonAzureComputer` parametern . I följande exempel visas hur du gör detta.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>Processautomatisering – Python-runbooks

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Vilken Python 3-version stöds i Azure Automation?

Python 3.8 stöds för molnjobb. Skript och paket från valfri version 3.x kan fungera om koden är kompatibel i olika versioner.

För hybridjobb i Windows Hybrid Runbook Workers kan du välja att installera valfri version av 3.x som du vill använda. För hybridjobb på Linux Hybrid Runbook Workers är vi beroende av att Python 3-versionen är installerad på datorn för att köra DSC OMSConfig och Linux-Hybrid Worker. Vi rekommenderar att du installerar version 3.6; Men olika versioner bör också fungera om det inte finns några större ändringar i metodsignaturer eller kontrakt mellan versioner av Python 3.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Kan Python 2- och Python 3-runbooks köras i samma Automation-konto?

Ja, det finns ingen begränsning för att använda Python 2- och Python 3-runbooks i samma Automation-konto.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Vad är planen för att migrera befintliga Python 2-runbooks och paket till Python 3?

Azure Automation planerar inte att migrera Python 2-runbooks och -paket till Python 3. Du måste utföra den här migreringen själv. Befintliga och nya Python 2-runbooks och paket fortsätter att fungera.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Vilka paket stöds som standard i Python 3-miljön?

Azure-paket 4.0.0 installeras som standard i Automation-miljön för Python 3. Du kan importera en högre version av Azure-paketet manuellt för att åsidosätta standardversionen.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>Vad händer om jag kör en Python 3-runbook som refererar till ett Python 2-paket eller tvärtom?

Python 2 och Python 3 har olika körningsmiljöer. När en Python 2-runbook körs kan endast Python 2-paket importeras och liknande för Python 3.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Hur gör jag för att skilja mellan Runbooks och paket för Python 2 och Python 3?

Python 3 är en ny runbook-definition som skiljer mellan Python 2- och Python 3-runbooks. På samma sätt introduceras en annan typ av paket för Python 3-paket.

## <a name="next-steps"></a>Nästa steg

Om din fråga inte besvaras här kan du referera till följande källor för fler frågor och svar.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Feedback-forum](https://feedback.azure.com/forums/905242-update-management)
