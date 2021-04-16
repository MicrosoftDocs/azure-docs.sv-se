---
title: Hantering av VM-tillägg med Azure Arc aktiverade servrar
description: Azure Arc-aktiverade servrar kan hantera distribution av tillägg för virtuella datorer som tillhandahåller konfigurations- och automatiseringsuppgifter efter distributionen med virtuella datorer som inte är Azure-datorer.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: 67f1b5b3db6ef446342e8381d54d487af1f3426a
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389799"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Hantera VM-tillägg med Azure Arc-aktiverade servrar

Tillägg för virtuella datorer (VM) är små program som tillhandahåller konfigurations- och automatiseringsuppgifter efter distributionen på virtuella Azure-datorer. Om en virtuell dator till exempel kräver programvaruinstallation, skydd mot virus eller för att köra ett skript i den, kan ett VM-tillägg användas.

Azure Arc-aktiverade servrar kan du distribuera Azure VM-tillägg till virtuella datorer som inte är Azure Windows- och Linux-datorer, vilket förenklar hanteringen av din hybriddator genom livscykeln. VM-tillägg kan hanteras med följande metoder på dina hybriddatorer eller servrar som hanteras av Arc-aktiverade servrar:

- Den [Azure Portal](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager mallar](manage-vm-extensions-template.md)

> [!NOTE]
> Azure Arc-aktiverade servrar stöder inte distribution och hantering av VM-tillägg till virtuella Azure-datorer. För virtuella Azure-datorer kan du läsa följande [översiktsartikel om VM-tillägg.](../../virtual-machines/extensions/overview.md)

## <a name="key-benefits"></a>Viktiga fördelar

Azure Arc stöd för VM-tillägg för servrar ger följande viktiga fördelar:

- Samla in loggdata för analys [med Loggar i Azure Monitor](../../azure-monitor/logs/data-platform-logs.md) genom att aktivera log Analytics-agentens VM-tillägg. Detta är användbart för att göra komplexa analyser av data från olika typer av källor.

- Med [Azure Monitor for VMs,](../../azure-monitor/vm/vminsights-overview.md)analyserar prestanda för dina virtuella Windows- och Linux-datorer och övervakar deras processer och beroenden på andra resurser och externa processer. Detta uppnås genom att aktivera både Log Analytics-agenten och VM-tilläggen för beroendeagenten.

- Ladda ned och kör skript på hybridanslutna datorer med hjälp av tillägget för anpassat skript. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurations- eller hanteringsuppgifter.

- Uppdatera automatiskt certifikat som lagras i [en Azure Key Vault](../../key-vault/general/overview.md).

## <a name="availability"></a>Tillgänglighet

Funktionen för VM-tillägg är endast tillgänglig i listan över [regioner som stöds.](overview.md#supported-regions) Se till att registrera din dator i någon av dessa regioner.

## <a name="extensions"></a>Tillägg

I den här versionen stöder vi följande VM-tillägg på Windows- och Linux-datorer.

Mer information om Azure Connected Machine agentpaketet och information om tilläggsagentkomponenten finns i [Agentöversikt](agent-overview.md#agent-component-details).

> [!NOTE]
> Stöd för DSC VM-tillägget har nyligen tagits bort för Arc-aktiverade servrar. Alternativt rekommenderar vi att du använder tillägget för anpassat skript för att hantera konfigurationen efter distributionen av din server eller dator.

### <a name="windows-extensions"></a>Windows-tillägg

|Anknytning |Publisher |Typ |Ytterligare information |
|----------|----------|-----|-----------------------|
|Azure Defender integrerad sårbarhetsskanner |Qualys |WindowsAgent.AzureSecurityCenter |[Azure Defender integrerad lösning för sårbarhetsbedömning för Azure- och hybriddatorer](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Anpassat skripttillägg |Microsoft.Compute | CustomScriptExtension |[Anpassat skripttillägg för Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|Log Analytics-agent |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[Log Analytics VM-tillägg för Windows](../../virtual-machines/extensions/oms-windows.md)|
|Azure Monitor for VMs (insikter) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [Beroendeagent för virtuell datortillägg för Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault certifikatsynkronisering | Microsoft.Azure.Key.Vault |KeyVaultForWindows | [Key Vault tillägg för virtuella datorer för Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|Azure Monitor-agent |Microsoft.Azure.Monitor |AzureMonitorWindowsAgent |[Installera Azure Monitor agenten (förhandsversion)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Linux-tillägg

|Anknytning |Publisher |Typ |Ytterligare information |
|----------|----------|-----|-----------------------|
|Azure Defender integrerad sårbarhetsskanner |Qualys |LinuxAgent.AzureSecurityCenter |[Azure Defender integrerad lösning för sårbarhetsbedömning för Azure- och hybriddatorer](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Anpassat skripttillägg |Microsoft.Azure.Extensions |CustomScript |[Anpassat Linux-skripttillägg version 2](../../virtual-machines/extensions/custom-script-linux.md) |
|Log Analytics-agent |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[Log Analytics VM-tillägg för Linux](../../virtual-machines/extensions/oms-linux.md) |
|Azure Monitor for VMs (insikter) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[Beroendeagent för virtuell datortillägg för Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault certifikatsynkronisering | Microsoft.Azure.Key.Vault |KeyVaultForLinux | [Key Vault tillägg för virtuella datorer för Linux](../../virtual-machines/extensions/key-vault-linux.md) |
|Azure Monitor-agent |Microsoft.Azure.Monitor |AzureMonitorLinuxAgent |[Installera Azure Monitor agent (förhandsversion)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>Förutsättningar

Den här funktionen är beroende av följande Azure-resursproviders i din prenumeration:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Om de inte redan är registrerade följer du stegen under Registrera [Azure-resursproviders.](agent-overview.md#register-azure-resource-providers)

Läs dokumentationen för varje VM-tillägg som refereras i föregående tabell för att förstå om det har några nätverks- eller systemkrav. Detta kan hjälpa dig att undvika anslutningsproblem med en Azure-tjänst eller -funktion som förlitar sig på det virtuella datortillägget.

### <a name="log-analytics-vm-extension"></a>Log Analytics VM-tillägg

Log Analytics-agentens VM-tillägg för Linux kräver att Python 2.x är installerat på måldatorn.

### <a name="azure-key-vault-vm-extension-preview"></a>Azure Key Vault VM-tillägg (förhandsversion)

Tillägget Key Vault virtuell dator (förhandsversion) stöder inte följande Linux-operativsystem:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

Distribution av Key Vault VM-tillägg (förhandsversion) stöds endast med:

- Azure CLI
- Den Azure PowerShell
- Azure Resource Manager-mall

Innan du distribuerar tillägget måste du slutföra följande:

1. [Skapa ett valv och certifikat](../../key-vault/certificates/quick-create-portal.md) (själv signerat eller importera).

2. Ge den Azure Arc aktiverade servern åtkomst till certifikathemligheten. Om du använder [RBAC-förhandsversionen](../../key-vault/general/rbac-guide.md)söker du efter namnet på Azure Arc resursen och tilldelar den rollen Key Vault Secrets **User (förhandsversion).** Om du använder en [Key Vault åtkomstprincip](../../key-vault/general/assign-access-policy-portal.md)tilldelar du **behörigheter** för hemlighets get till Azure Arc-resursens systemtilldelningsidentitet.

### <a name="connected-machine-agent"></a>Ansluten datoragent

Kontrollera att datorn matchar de [versioner av](agent-overview.md#supported-operating-systems) Windows- och Linux-operativsystem som stöds för Azure Connected Machine agenten.

Den lägsta versionen av connected machine-agenten som stöds med den här funktionen i Windows och Linux är version 1.0.

Information om hur du uppgraderar datorn till den version av agenten som krävs finns [i Uppgradera agenten](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Nästa steg

Du kan distribuera, hantera och ta bort VM-tillägg med hjälp av [Azure CLI,](manage-vm-extensions-cli.md) [Azure PowerShell](manage-vm-extensions-powershell.md), [från Azure Portal](manage-vm-extensions-portal.md)eller Azure Resource Manager [mallar](manage-vm-extensions-template.md).
