---
title: Azure-tillägg och -funktioner för virtuella datorer
description: Läs mer om Azure VM-tillägg
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.date: 08/03/2020
ms.openlocfilehash: e1b96293db0389201fdab3340d8f0e74fefc4c52
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559723"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure-tillägg och -funktioner för virtuella datorer
Tillägg är små program som tillhandahåller konfiguration av efter distribution och automatisering på virtuella Azure-datorer. Azure-plattformen är värd för många tillägg som omfattar program för VM-konfiguration, övervakning, säkerhet och verktyg. Utgivare tar ett program, placerar det i ett tillägg och fören klar installationen. Allt du behöver göra är att ange obligatoriska parametrar. 

## <a name="how-can-i-find-what-extensions-are-available"></a>Hur ser jag vilka tillägg som är tillgängliga?
Du kan visa tillgängliga tillägg genom att välja en virtuell dator, välja **tillägg** på den vänstra menyn. Om du vill hämta en fullständig lista över tillägg, se [identifiera VM-tillägg för Linux](features-linux.md) och [identifiera VM-tillägg för Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Hur kan jag installera ett tillägg?
Azure VM-tillägg kan hanteras med Azure CLI-, PowerShell-, Resource Manager-mallar och Azure Portal. Om du vill testa ett tillägg går du till Azure Portal, väljer det anpassade skript tillägget och skickar sedan ett kommando eller skript för att köra tillägget.

Mer information finns i [Windows anpassat skript tillägg](custom-script-windows.md) och [anpassat skript tillägg för Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Hur gör jag för att hantera tilläggs programmets livs cykel?
Du behöver inte ansluta till en virtuell dator direkt för att installera eller ta bort ett tillägg. Azure Extensions livs cykel hanteras utanför den virtuella datorn och integreras i Azure-plattformen.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Allt annat jag bör tänka på vid tillägg?
Vissa enskilda program för VM-tillägg kan ha sina egna miljö krav, till exempel åtkomst till en slut punkt. Varje tillägg har en artikel som förklarar alla krav, inklusive vilka operativ system som stöds.

## <a name="troubleshoot-extensions"></a>Felsökning av tillägg

Felsöknings information för varje tillägg finns i avsnittet **fel sökning och support** i översikten för tillägget. Här är en lista över den felsöknings information som är tillgänglig:

| Namnområde | Felsökning |
|-----------|-----------------|
| Microsoft. Azure. Monitoring. dependencyagent. dependencyagentlinux | [Azure Monitor beroende för Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Monitoring. dependencyagent. dependencyagentwindows | [Azure Monitor beroende för Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryptionforlinux | [Azure Disk Encryption för Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryption | [Azure Disk Encryption för Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| Microsoft. Compute. CustomScriptExtension | [Anpassat skript för Windows](custom-script-windows.md#troubleshoot-and-support) |
| Microsoft. ostcextensions. customscriptforlinux | [Önskad tillstånds konfiguration för Linux](dsc-linux.md#troubleshoot-and-support) |
| Microsoft. PowerShell. DSC | [Önskad tillstånds konfiguration för Windows](dsc-windows.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverlinux | [NVIDIA GPU-drivrutins tillägg för Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverwindows | [NVIDIA GPU-drivrutins tillägg för Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. iaasantimalware | [Tillägg för program mot skadlig kod för Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| Microsoft. enterprisecloud. Monitoring. omsagentforlinux | [Azure Monitor för Linux](oms-linux.md#troubleshoot-and-support)
| Microsoft. enterprisecloud. Monitoring. microsoftmonitoringagent | [Azure Monitor för Windows](oms-windows.md#troubleshoot-and-support) |
| Stackify. linuxagent. extension. stackifylinuxagentextension | [Stackify-omspårning för Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| tillägget vmaccessforlinux. Microsoft. ostcextensions | [Återställ lösen ord för Linux](vmaccess.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Ögonblicks bild för Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Ögonblicks bild för Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Nästa steg
* Mer information om hur Linux-agenten och-tillägg fungerar finns i [tillägg och funktioner för Azure VM i Linux](features-linux.md).
* Mer information om hur Windows gästa Gent och tillägg fungerar finns i [tillägg för virtuella Azure-datorer och Windows-funktioner](features-windows.md).  
* Information om hur du installerar gäst agenten för Windows finns i [Översikt över Azure Windows Virtual Machine agent](agent-windows.md).  
* Information om hur du installerar Linux-agenten finns i [Översikt över Azure Linux Virtual Machine agent](agent-linux.md).  

