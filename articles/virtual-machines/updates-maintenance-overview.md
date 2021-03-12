---
title: Översikt över uppdateringar och underhåll
description: Lär dig mer om de uppdateringar och underhålls alternativ som är tillgängliga med virtuella datorer i Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 81c6fb2e7f25abc9a236c80d1412b84fd6761872
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103022360"
---
# <a name="updates-and-maintenance-overview"></a>Översikt över uppdateringar och underhåll
Den här artikeln innehåller en översikt över de olika alternativen för uppdatering och underhåll för virtuella Azure-datorer (VM).

## <a name="automatic-os-image-upgrade"></a>Automatisk uppgradering av operativ system avbildning

Om du aktiverar [automatiska uppgraderingar av OS-avbildningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) på din skalnings uppsättning kan du under lätta uppdaterings hanteringen på ett säkert och automatiskt uppgradera operativ system disken för alla instanser i skalnings uppsättningen.

[Automatisk uppgradering av operativ system](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) har följande egenskaper:

- När den har kon figurer ATS, tillämpas den senaste OS-avbildningen som publicerats av avbildnings utgivare automatiskt på skalnings uppsättningen utan att användaren behöver göra
- Uppgraderar batchar av instanser på ett rullandet sätt varje gång en ny avbildning publiceras av utgivaren.
- Integrerar med program hälso avsökningar och [tillägg för program hälsa](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context).
- Fungerar för alla VM-storlekar och för både Windows-och Linux-avbildningar.
- Du kan när som helst välja att inte utföra automatiska uppgraderingar (OS-uppgraderingar kan även initieras manuellt).
- Operativ system disken för en virtuell dator ersätts med den nya OS-disken som skapats med den senaste avbildnings versionen. Konfigurerade tillägg och anpassade data skript körs, medan beständiga data diskar bevaras.
- Stöd för [sekvensering](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context) stöds.
- Automatisk uppgradering av operativ system avbildningar kan aktive ras på en skalnings uppsättning i valfri storlek.


## <a name="automatic-vm-guest-patching-preview"></a>Automatisk uppdatering av gäst dator (för hands version)

Om du aktiverar automatisk uppdatering av [virtuella gäst](automatic-vm-guest-patching.md) datorer för dina virtuella Azure-datorer kan du under lätta uppdaterings hanteringen genom att säkert och automatiskt uppdatera virtuella datorer för att upprätthålla säkerhetskompatibilitet.

[Automatisk korrigering av virtuella gäst datorer](automatic-vm-guest-patching.md) har följande egenskaper:
- Korrigeringar som klassificeras som *kritiska* eller *säkerhet* hämtas automatiskt och tillämpas på den virtuella datorn.
- Uppdateringar tillämpas under låg belastnings tider i den virtuella datorns tidszon.
- Uppdaterings dirigeringen hanteras av Azure och korrigeringarna tillämpas efter [principer för tillgänglighets första](automatic-vm-guest-patching.md#availability-first-patching).
- Hälso tillstånd för virtuella datorer, som fastställs genom plattformens hälso signaler, övervakas för att identifiera korrigerings fel.
- Fungerar för alla VM-storlekar.


## <a name="automatic-extension-upgrade-preview"></a>Automatisk tilläggs uppgradering (förhands granskning)

[Automatisk tilläggs uppgradering](automatic-extension-upgrade.md) är tillgänglig i för hands versionen för virtuella Azure-datorer och Azure Virtual Machine Scale Sets. När automatisk tilläggs uppgradering har Aktiver ATS på en virtuell dator eller skalnings uppsättning uppgraderas tillägget automatiskt varje gång som tilläggets utgivare släpper en ny version för tillägget.

 Automatisk uppgradering av tillägg har följande funktioner:
- Stöds för virtuella Azure-datorer och Azure-Virtual Machine Scale Sets. Service Fabric Virtual Machine Scale Sets stöds inte för närvarande.
- Uppgraderingar tillämpas i en tillgänglighets-Första distributions modell.
- För en skalnings uppsättning för virtuella datorer uppgraderas inte mer än 20% av de virtuella datorerna för skalnings uppsättningen i en enda batch. Den minsta batchstorleken är en virtuell dator.
- Fungerar för alla VM-storlekar och för både Windows-och Linux-tillägg.
- Du kan när som helst välja att inte använda automatiska uppgraderingar.
- Automatisk uppgradering av tillägg kan aktive ras i en Virtual Machine Scale Sets i valfri storlek.
- Alla tillägg som stöds registreras individuellt och du kan välja vilka tillägg som ska uppgraderas automatiskt.
- Stöds i alla offentliga moln regioner.

## <a name="hotpatch"></a>Hotpatch 

[HotPatching](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context) är ett nytt sätt att installera uppdateringar på nya virtuella Windows Server Azure-datorer (VM) som inte kräver en omstart efter installationen. Hotpatch för virtuella Windows Server Azure-versioner har följande fördelar:

- Lägre arbets belastnings påverkan med mindre omstarter
- Snabbare distribution av uppdateringar eftersom paketen är mindre, installera snabbare och har enklare uppdaterings dirigering med Azure Update Manager
- Bättre skydd eftersom Hotpatch uppdaterings paket är begränsade till Windows-säkerhetsuppdateringar som installeras snabbare utan att starta om


## <a name="azure-update-management"></a>Hantering av Azure-uppdateringar

Du kan använda [uppdateringshantering i Azure Automation](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context) för att hantera operativ system uppdateringar för virtuella Windows-och Linux-datorer i Azure, i lokala miljöer och i andra moln miljöer. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agent datorer och hantera processen för att installera nödvändiga uppdateringar för servrar.

## <a name="maintenance-control"></a>Kontroll av underhåll

Hantera plattforms uppdateringar, som inte kräver omstart, med hjälp av [underhålls kontroll](maintenance-control.md). Azure uppdaterar ofta sin infrastruktur för att förbättra tillförlitligheten, prestandan, säkerheten eller lansera nya funktioner. De flesta uppdateringar är transparenta för användarna. Vissa känsliga arbets belastningar, t. ex. spel, medie direkt uppspelning och ekonomiska transaktioner, kan inte tolerera ens några sekunder av en virtuell dator som fryser eller kopplar från för underhåll. Med underhålls kontrollen får du möjlighet att vänta på plattforms uppdateringar och tillämpa dem i ett rullande 35-dagars fönster. 

Med underhålls kontrollen kan du bestämma när du ska tillämpa uppdateringar på dina isolerade virtuella datorer och Azure-dedikerade värdar.

Med [underhålls kontroll](maintenance-control.md)kan du:
- Batch-uppdateringar till ett uppdaterings paket.
- Vänta upp till 35 dagar för att installera uppdateringar. 
- Automatisera plattforms uppdateringar genom att konfigurera ett underhålls schema eller genom att använda [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- Underhålls konfigurationer fungerar mellan prenumerationer och resurs grupper. 


## <a name="scheduled-events"></a>Schemalagda händelser

Schemalagda händelser är en Azure-Metadata Service som ger din program tid att förbereda för underhåll av virtuella datorer. Den innehåller information om kommande underhålls händelser (till exempel omstart) så att ditt program kan förbereda sig för dem och begränsa avbrott. Den är tillgänglig för alla typer av Azure-Virtual Machines, inklusive PaaS och IaaS på både Windows och Linux. 

Information om Schemalagda händelser finns [schemalagda händelser för virtuella Windows-datorer](./windows/scheduled-events.md) och [schemalagda händelser för Linux](./linux/scheduled-events.md)

## <a name="next-steps"></a>Nästa steg

Läs dokumentationen om [tillgänglighet och skala](availability.md) för fler sätt att öka drift tiden för dina program och tjänster. 