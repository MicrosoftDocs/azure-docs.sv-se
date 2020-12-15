---
title: Hantering av VM-tillägg med Azure Arc-aktiverade servrar
description: Azure Arc-aktiverade servrar kan hantera distribution av virtuella dator tillägg som tillhandahåller konfiguration och automatiserings uppgifter efter distributionen med icke-virtuella datorer i Azure.
ms.date: 12/14/2020
ms.topic: conceptual
ms.openlocfilehash: 55e21f9c6bcd2dfe5f995093034773f2a87d9b03
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504516"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Hantering av virtuella dator tillägg med Azure Arc-aktiverade servrar

Tillägg för virtuella datorer (VM) är små program som ger konfigurations-och automatiserings åtgärder efter distributionen på virtuella Azure-datorer. Om en virtuell dator till exempel kräver program varu installation, antivirus skydd eller för att köra ett skript i den, kan ett VM-tillägg användas.

Med Azure Arc-aktiverade servrar kan du distribuera virtuella Azure-tillägg till virtuella Windows-och Linux-datorer som inte är Azure-datorer, vilket fören klar hanteringen av din hybrid dator via deras livs cykel. VM-tillägg kan hanteras med hjälp av följande metoder på dina hybrid datorer eller servrar som hanteras av Arc-aktiverade servrar:

- [Azure Portal](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager-mallar](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Viktiga fördelar

Stöd för VM-tillägg för Azure Arc-aktiverade servrar ger följande viktiga fördelar:

- Använd [Azure Automation tillstånds konfiguration](../../automation/automation-dsc-overview.md) för att centralt lagra konfigurationer och upprätthålla det önskade läget för Hybrid anslutna datorer som är aktiverade via DSC VM-tillägget.

- Samla in loggdata för analys med [loggar i Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) aktiverat via det virtuella Log Analytics agent-tillägget. Detta är användbart för att utföra komplex analys i data från olika typer av källor.

- Med [Azure Monitor for VMS](../../azure-monitor/insights/vminsights-overview.md)analyseras prestandan för dina virtuella Windows-och Linux-datorer och övervaka deras processer och beroenden på andra resurser och externa processer. Detta uppnås genom att aktivera både VM-tillägg för Log Analytics agent och beroende agent.

- Ladda ned och kör skript på Hybrid anslutna datorer med tillägget för anpassat skript. Det här tillägget är användbart för konfiguration av distribution, program varu installation eller andra konfigurations-eller hanterings uppgifter.

- Automatisk uppdatering av certifikat som lagras i en [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="availability"></a>Tillgänglighet

Funktioner för virtuella dator tillägg är bara tillgängliga i listan över [regioner som stöds](overview.md#supported-regions). Se till att du har registrerat datorn i någon av dessa regioner.

## <a name="extensions"></a>Tillägg

I den här versionen har vi stöd för följande VM-tillägg på Windows-och Linux-datorer.

|Filnamnstillägg |Operativsystem |Publisher |Ytterligare information |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Anpassat skript tillägg för Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Windows PowerShell DSC-tillägg](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics-agent |Windows |Microsoft. EnterpriseCloud. Monitoring |[Log Analytics VM-tillägg för Windows](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft-beroende agent | Windows |Microsoft.Compute | [Tillägg för virtuell dator för beroende agent för Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Key Vault | Windows | Microsoft.Compute | [Key Vault tillägg för virtuell dator för Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|CustomScript|Linux |Microsoft. Azure. extension |[Anpassat skript tillägg för Linux version 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft. OSTCExtensions |[PowerShell DSC-tillägg för Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics-agent |Linux |Microsoft. EnterpriseCloud. Monitoring |[Log Analytics VM-tillägg för Linux](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft-beroende agent | Linux |Microsoft.Compute | [Tillägg för virtuell dator för beroende agent för Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Key Vault | Linux | Microsoft.Compute | [Key Vault tillägg för virtuell dator för Linux](../../virtual-machines/extensions/key-vault-linux.md) |

Mer information om paketet för Azure Connected Machine agent och information om tilläggs Agent komponenten finns i [agent översikt](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Krav

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
