---
title: Hantering av VM-tillägg med Azure Arc-aktiverade servrar
description: Azure Arc-aktiverade servrar kan hantera distribution av virtuella dator tillägg som tillhandahåller konfiguration och automatiserings uppgifter efter distributionen med icke-virtuella datorer i Azure.
ms.date: 03/22/2021
ms.topic: conceptual
ms.openlocfilehash: 9af2700f036352188e646188485285482ee70c69
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799589"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Hantera VM-tillägg med Azure Arc-aktiverade servrar

Tillägg för virtuella datorer (VM) är små program som ger konfigurations-och automatiserings åtgärder efter distributionen på virtuella Azure-datorer. Om en virtuell dator till exempel kräver program varu installation, antivirus skydd eller för att köra ett skript i den, kan ett VM-tillägg användas.

Med Azure Arc-aktiverade servrar kan du distribuera virtuella Azure-tillägg till virtuella Windows-och Linux-datorer som inte är Azure-datorer, vilket fören klar hanteringen av din hybrid dator via deras livs cykel. VM-tillägg kan hanteras med hjälp av följande metoder på dina hybrid datorer eller servrar som hanteras av Arc-aktiverade servrar:

- [Azure Portal](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager-mallar](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Viktiga fördelar

Stöd för VM-tillägg för Azure Arc-aktiverade servrar ger följande viktiga fördelar:

- Samla in loggdata för analys med [loggar i Azure Monitor](../../azure-monitor/logs/data-platform-logs.md) genom att aktivera det virtuella Log Analytics agent-tillägget. Detta är användbart för att utföra komplex analys i data från olika typer av källor.

- Med [Azure Monitor for VMS](../../azure-monitor/vm/vminsights-overview.md)analyseras prestandan för dina virtuella Windows-och Linux-datorer och övervaka deras processer och beroenden på andra resurser och externa processer. Detta uppnås genom att aktivera både VM-tillägg för Log Analytics agent och beroende agent.

- Ladda ned och kör skript på Hybrid anslutna datorer med tillägget för anpassat skript. Det här tillägget är användbart för konfiguration av distribution, program varu installation eller andra konfigurations-eller hanterings uppgifter.

- Automatisk uppdatering av certifikat som lagras i en [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="availability"></a>Tillgänglighet

Funktioner för virtuella dator tillägg är bara tillgängliga i listan över [regioner som stöds](overview.md#supported-regions). Se till att du har registrerat datorn i någon av dessa regioner.

## <a name="extensions"></a>Tillägg

I den här versionen har vi stöd för följande VM-tillägg på Windows-och Linux-datorer.

Mer information om paketet för Azure Connected Machine agent och information om tilläggs Agent komponenten finns i [agent översikt](agent-overview.md#agent-component-details).

> [!NOTE]
> Nyligen stöd för det virtuella DSC-tillägget har tagits bort för Arc-aktiverade servrar. Alternativt rekommenderar vi att du använder tillägget för anpassat skript för att hantera konfigurationen efter distributionen av servern eller datorn.

### <a name="windows-extensions"></a>Windows-tillägg

|Anknytning |Publisher |Typ |Ytterligare information |
|----------|----------|-----|-----------------------|
|Azure Defender integrerad sårbarhets-skanner |Qualys |WindowsAgent.AzureSecurityCenter |[Azure Defenders integrerade lösning för sårbarhets bedömning för Azure och hybrid datorer](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Anpassat skripttillägg |Microsoft.Compute | CustomScriptExtension |[Anpassat skript tillägg för Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|Log Analytics-agent |Microsoft. EnterpriseCloud. Monitoring |MicrosoftMonitoringAgent |[Log Analytics VM-tillägg för Windows](../../virtual-machines/extensions/oms-windows.md)|
|Azure Monitor for VMs (insikter) |Microsoft. Azure. Monitoring. DependencyAgent |DependencyAgentWindows | [Tillägg för virtuell dator för beroende agent för Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault synkronisering av certifikat | Microsoft. Azure. Key. Vault |KeyVaultForWindows | [Key Vault tillägg för virtuell dator för Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|Azure Monitor-agent |Microsoft. Azure. Monitor |AzureMonitorWindowsAgent |[Installera Azure Monitor Agent (förhands granskning)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Linux-tillägg

|Anknytning |Publisher |Typ |Ytterligare information |
|----------|----------|-----|-----------------------|
|Azure Defender integrerad sårbarhets-skanner |Qualys |LinuxAgent.AzureSecurityCenter |[Azure Defenders integrerade lösning för sårbarhets bedömning för Azure och hybrid datorer](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Anpassat skripttillägg |Microsoft. Azure. Extensions |CustomScript |[Anpassat skript tillägg för Linux version 2](../../virtual-machines/extensions/custom-script-linux.md) |
|Log Analytics-agent |Microsoft. EnterpriseCloud. Monitoring |OmsAgentForLinux |[Log Analytics VM-tillägg för Linux](../../virtual-machines/extensions/oms-linux.md) |
|Azure Monitor for VMs (insikter) |Microsoft. Azure. Monitoring. DependencyAgent |DependencyAgentLinux |[Tillägg för virtuell dator för beroende agent för Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault synkronisering av certifikat | Microsoft. Azure. Key. Vault |KeyVaultForLinux | [Key Vault tillägg för virtuell dator för Linux](../../virtual-machines/extensions/key-vault-linux.md) |
|Azure Monitor-agent |Microsoft. Azure. Monitor |AzureMonitorLinuxAgent |[Installera Azure Monitor Agent (förhands granskning)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>Förutsättningar

Den här funktionen är beroende av följande Azure-resurs leverantörer i din prenumeration:

- **Microsoft. HybridCompute**
- **Microsoft. GuestConfiguration**

Om de inte redan har registrerats följer du stegen under [Registrera Azure Resource providers](agent-overview.md#register-azure-resource-providers).

Se till att läsa dokumentationen för varje VM-tillägg som refereras till i föregående tabell för att förstå om det innehåller några nätverks-eller system krav. Detta kan hjälpa dig att undvika problem med anslutningen till en Azure-tjänst eller funktion som förlitar sig på det virtuella dator tillägget.

### <a name="log-analytics-vm-extension"></a>Log Analytics VM-tillägg

Det virtuella dator tillägget för Log Analytics agent för Linux kräver python 2. x installerat på mål datorn.

### <a name="azure-key-vault-vm-extension-preview"></a>Azure Key Vault VM-tillägg (för hands version)

Key Vault VM-tillägget (för hands version) stöder inte följande Linux-operativ system:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

Distribution av Key Vault VM-tillägget (för hands version) stöds bara med:

- Azure CLI
- Azure PowerShell
- Azure Resource Manager-mall

Innan du distribuerar tillägget måste du göra följande:

1. [Skapa ett valv och certifikat](../../key-vault/certificates/quick-create-portal.md) (självsignerat eller importerat).

2. Ge Azure Arc-aktiverad åtkomst till certifikat hemligheten. Om du använder RBAC- [förhandsgranskningen](../../key-vault/general/rbac-guide.md)söker du efter namnet på Azure Arc-resursen och tilldelar den rollen som **Key Vault hemligheter (förhands granskning)** . Om du använder [Key Vault åtkomst princip](../../key-vault/general/assign-access-policy-portal.md)tilldelar du hemligt **Get** behörighet till Azure Arc-resursens tilldelade identitet.

### <a name="connected-machine-agent"></a>Ansluten dator agent

Kontrol lera att din dator matchar de versioner av Windows och Linux-operativsystem som [stöds](agent-overview.md#supported-operating-systems) för den Azure-anslutna dator agenten.

Den lägsta versionen av den anslutna dator agenten som stöds av den här funktionen i Windows och Linux är 1,0-versionen.

Om du vill uppgradera datorn till den version av agenten som krävs, se [uppgraderings agenten](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Nästa steg

Du kan distribuera, hantera och ta bort VM-tillägg med hjälp av [Azure CLI](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md)från [Azure Portal](manage-vm-extensions-portal.md)eller [Azure Resource Manager mallar](manage-vm-extensions-template.md).
