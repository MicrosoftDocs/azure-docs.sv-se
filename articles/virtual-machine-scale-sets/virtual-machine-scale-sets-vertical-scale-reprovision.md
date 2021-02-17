---
title: Skala skalnings uppsättningar för virtuella Azure-datorer vertikalt
description: Hur du lodrätt skalar en virtuell dator som svar på övervaknings aviseringar med Azure Automation
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/18/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: b172f1f7137b53e98384d92c9c709694eaf0b7e9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594502"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Lodrät autoskalning med skalnings uppsättningar för virtuella datorer

Den här artikeln beskriver hur du skalar Azure- [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/) i lodrät skala med eller utan att etablera. 

Vertikal skalning, som även kallas *skala upp* och *ned*, innebär att öka eller minska storleken på virtuella datorer som svar på en arbets belastning. Jämför det här beteendet med [horisontell skalning](virtual-machine-scale-sets-autoscale-overview.md), vilket även kallas *skala ut* och *skala i*, där antalet virtuella datorer ändras beroende på arbets belastningen.

Reetablering innebär att en befintlig virtuell dator tas bort och ersätts med en ny. När du ökar eller minskar storleken på virtuella datorer i en skalnings uppsättning för virtuella datorer kan du i vissa fall ändra storlek på befintliga virtuella datorer och behålla dina data, men i andra fall måste du distribuera nya virtuella datorer med den nya storleken. Det här dokumentet omfattar båda fallen.

Lodrät skalning kan vara användbart när:

* En tjänst som bygger på virtuella datorer används under användning (till exempel på helger). Att minska storleken på den virtuella datorn kan minska månads kostnaderna.
* Öka storleken på virtuella datorer för att hantera större efter frågan utan att skapa ytterligare virtuella datorer.

Du kan ställa in vertikal skalning så att den utlöses baserat på måttbaserade aviseringar från skalnings uppsättningen för den virtuella datorn. När aviseringen aktive ras utlöses en webhook som utlöser en Runbook som kan skala upp eller ned din skalnings uppsättning. Lodrät skalning kan konfigureras genom att följa dessa steg:

1. Skapa ett Azure Automation-konto med kör som-funktion.
2. Importera Azure Automation de lodräta skalnings runbooksna för virtuella datorers skalnings uppsättningar till din prenumeration.
3. Lägg till en webhook i din Runbook.
4. Lägg till en avisering i skalnings uppsättningen för den virtuella datorn med en webhook-avisering.

> [!NOTE]
> På grund av storleken på den första virtuella datorn kan storlekarna som kan skalas till, begränsas på grund av tillgängligheten för de andra storlekarna i klustret som den aktuella virtuella datorn har distribuerats i. I de publicerade Automation-runbooks som används i den här artikeln tar vi hand om det här fallet och skalar bara inom de följande virtuella datorernas storleks par. Det innebär att en Standard_D1v2 virtuell dator plötsligt inte skalas upp till Standard_G5 eller skalas ned till Basic_A0. Även begränsade storlekar för virtuella datorer skalar upp/ned stöds inte. Du kan välja att skala mellan följande par storlekar:
> 
> | Skalnings medlem för VM-storlekar | Medlem |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Skapa ett Azure Automation-konto med kör som-funktion
Det första du behöver göra är att skapa ett Azure Automation-konto som är värd för de Runbooks som används för att skala de virtuella datorernas skalnings uppsättnings instanser. Nyligen [Azure Automation](https://azure.microsoft.com/services/automation/) introducerade funktionen "kör som-konto" som gör det enkelt att konfigurera tjänstens huvud namn för att köra Runbooks automatiskt för användarens räkning. Mer information finns i:

* [Autentisera runbooks med ett ”Kör som”-konto i Azure](../automation/manage-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importera Azure Automation vertikala skalnings-Runbooks till din prenumeration

Runbooks som behövs för att skala dina virtuella dator skalnings uppsättningar i höjdled är redan publicerade i Azure Automation Runbook-galleriet. Följ stegen i den här artikeln om du vill importera dem till din prenumeration:

* [Runbook- och modulgallerier för Azure Automation](../automation/automation-runbook-gallery.md)

Välj alternativet Bläddra i galleriet från menyn Runbooks:

![Runbooks som ska importeras][runbooks]

Runbooks som behöver importeras visas. Välj en Runbook baserat på om du vill ha vertikal skalning med eller utan att etablera:

![Runbook-Galleri][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Lägg till en webhook i din Runbook

När du har importerat Runbooks lägger du till en webhook i runbooken så att den kan utlösas av en avisering från en skalnings uppsättning för virtuella datorer. Information om hur du skapar en webhook för din Runbook beskrivs i den här artikeln:

* [Azure Automation Webhooks](../automation/automation-webhooks.md)

> [!NOTE]
> Se till att du kopierar webhook-URI: n innan du stänger dialog rutan webhook eftersom du kommer att behöva den här adressen i nästa avsnitt.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Lägg till en avisering i skalnings uppsättningen för den virtuella datorn

Nedan visas ett PowerShell-skript som visar hur du lägger till en avisering till en skalnings uppsättning för virtuella datorer. Läs följande artikel för att hämta namnet på måttet för att utlösa aviseringen på: [Azure Monitor autoskalning av vanliga mått](../azure-monitor/autoscale/autoscale-common-metrics.md).

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Vi rekommenderar att du konfigurerar ett rimligt tids fönster för aviseringen för att undvika att den vertikala skalningen utlöses, och eventuella associerade avbrott i tjänsten för ofta. Överväg ett fönster på minst 20-30 minuter eller mer. Överväg horisontell skalning om du behöver undvika avbrott.
> 
> 

Mer information om hur du skapar aviseringar finns i följande artiklar:

* [Azure Monitor PowerShell-exempel](../azure-monitor/powershell-samples.md)
* [Azure Monitor cross-platform CLI-exempel](../azure-monitor/cli-samples.md)

## <a name="summary"></a>Sammanfattning

Den här artikeln visade exempel på enkla lodräta skalnings exempel. Med dessa Bygg stenar – Automation-konto, Runbooks, Webhooks, Alerts – du kan ansluta flera olika händelser till en anpassad uppsättning åtgärder.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
