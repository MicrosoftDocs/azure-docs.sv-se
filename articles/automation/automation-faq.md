---
title: Azure Automation vanliga frågor och svar | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 2b40cc3d4cea4476ffde8bee8cec694975eb5083
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97724280"
---
# <a name="azure-automation-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Automation

Microsoft FAQ (vanliga frågor och svar) är en lista över vanliga frågor om Azure Automation. Om du har andra frågor om dess funktioner går du till diskussions forumet och publicerar dina frågor. När en fråga ofta är tillfrågad, lägger vi till den i den här artikeln så att den snabbt och enkelt kan hittas.

## <a name="update-management"></a>Uppdateringshantering

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Kan jag förhindra oväntade uppgraderingar på äldre operativ system.

På vissa Linux-varianter, till exempel Red Hat Enterprise Linux, kan uppgraderingar av operativ Systems nivå ske via paket. Detta kan leda till att Uppdateringshantering körs där operativ systemets versions nummer ändras. Eftersom Uppdateringshantering använder samma metoder för att uppdatera paket som en administratör använder lokalt på en Linux-dator är detta avsiktligt avsiktligt.

Använd **undantags** funktionen för att undvika att uppdatera operativ system versionen genom uppdateringshantering distributioner.

I Red Hat Enterprise Linux, det paket namn som ska undantas `redhat-release-server.x86_64` .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Varför tillämpas inte kritiska/säkerhets uppdateringar?

När du distribuerar uppdateringar till en Linux-dator kan du välja uppdaterings klassificeringar. Med det här alternativet filtreras de uppdateringar som uppfyller de angivna kriterierna. Det här filtret används lokalt på datorn när uppdateringen distribueras.

Eftersom Uppdateringshantering utför uppdaterings berikning i molnet kan du flagga vissa uppdateringar i Uppdateringshantering som en säkerhets påverkan, även om den lokala datorn inte har den informationen. Om du använder kritiska uppdateringar på en Linux-dator kan det finnas uppdateringar som inte har marker ATS som en säkerhets påverkan på den datorn och som därför inte tillämpas. Uppdateringshantering kan dock fortfarande rapportera datorn som inkompatibel, eftersom den innehåller ytterligare information om den relevanta uppdateringen.

Distribution av uppdateringar efter uppdaterings klassificering fungerar inte på RTM-versioner av CentOS. Om du vill distribuera uppdateringar för CentOS korrekt väljer du alla klassificeringar för att se till att uppdateringarna tillämpas. För SUSE väljer du bara **andra uppdateringar** som klassificering kan installera andra säkerhets uppdateringar om de är relaterade till zypper (paket hanteraren) eller dess beroenden krävs först. Det här beteendet är en begränsning i zypper. I vissa fall kan du behöva köra uppdaterings distributionen igen och sedan verifiera distributionen via uppdaterings loggen.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Kan jag distribuera uppdateringar i Azure-klienter?

Om du har datorer som behöver korrigeringar i en annan Azure-klient rapporterar till Uppdateringshantering måste du använda en lösning för att få dem schemalagda. Du kan använda cmdleten [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) med `ForUpdateConfiguration` parametern som anges för att skapa ett schema. Du kan använda cmdleten [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) och skicka datorerna i den andra klienten till- `NonAzureComputer` parametern. I följande exempel visas hur du gör detta.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>Process automatisering – python-Runbooks

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Vilken python 3-version stöds i Azure Automation?

Python 3,8 stöds för moln jobb. Skript och paket från vilken 3. x-version som helst kan fungera om koden är kompatibel mellan olika versioner.

För Hybrid jobb i Windows hybrid Runbook Worker kan du välja att installera vilken 3. x-version som du vill använda. För Hybrid jobb på Linux hybrid Runbook Worker är det beroende av python 3-versionen som är installerad på datorn för att köra DSC-OMSConfig och Linux-Hybrid Worker. Vi rekommenderar att du installerar version 3,6; olika versioner bör dock också fungera om det inte finns några större ändringar i metoden signaturer eller kontrakt mellan versioner av python 3.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Kan python 2 och python 3-Runbooks köras i samma Automation-konto?

Ja, det finns ingen begränsning för att använda python 2-och python 3-Runbooks i samma Automation-konto.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Vad är planen för att migrera befintliga python 2-Runbooks och-paket till python 3?

Azure Automation planerar inte att migrera python 2-Runbooks och-paket till python 3. Du kommer att behöva utföra migreringen själv. Befintliga och nya python 2-Runbooks och-paket fungerar fortfarande.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Vilka paket stöds som standard i python 3-miljön?

Azure Package 4.0.0 installeras som standard i python 3 Automation-miljö. Du kan importera en högre version av Azure-paketet manuellt om du vill åsidosätta standard versionen.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>Vad händer om jag kör en python 3-Runbook som refererar till ett python 2-paket eller vice versa?

Python 2 och python 3 har olika körnings miljöer. Medan en python 2-runbook körs kan endast python 2-paket importeras och liknande för python 3.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Hur gör jag för att skilja mellan de olika Runbooks och paketen python 2 och python 3?

Python 3 är en ny Runbook-definition som särskiljer mellan python 2-och python 3-Runbooks. På samma sätt introduceras en annan paket typ för python 3-paket.

## <a name="next-steps"></a>Nästa steg

Om din fråga inte besvaras här kan du referera till följande källor för fler frågor och svar.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Feedback-forum](https://feedback.azure.com/forums/905242-update-management)
