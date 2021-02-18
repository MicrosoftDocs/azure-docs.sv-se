---
title: Felsöka Azure Monitor for VMs gäst hälsa (för hands version)
description: Beskriver fel söknings steg som du kan vidta när du har problem med Azure Monitor for VMs hälsa.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: da8097341f8499be4e28fa37c06d963d057966ea
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100622174"
---
# <a name="troubleshoot-azure-monitor-for-vms-guest-health-preview"></a>Felsöka Azure Monitor for VMs gäst hälsa (för hands version)
I den här artikeln beskrivs fel söknings steg som du kan vidta när du har problem med Azure Monitor for VMs hälsan.

## <a name="error-message-that-no-data-is-available"></a>Fel meddelande om att inga data är tillgängliga 

![Inga data](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Kontrol lera att den virtuella datorn uppfyller konfigurations kraven

1. Kontrollera att den virtuella datorn är en virtuell Azure-dator. Azure Arc för servrar stöds inte för närvarande.
2. Kontrollera att den virtuella datorn kör ett [operativsystem som stöds](vminsights-health-enable.md?current-limitations.md).
3. Kontrollera att den virtuella datorn är installerad i en [region som stöds](vminsights-health-enable.md?current-limitations.md).
4. Kontrollera att Log Analytics-arbetsytan är installerad i en [region som stöds](vminsights-health-enable.md?current-limitations.md).

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Kontrol lera att den virtuella datorn har registrerats korrekt
Verifiera att Azure Monitor Agent-tillägget och den virtuella gäst datorns hälso agent har allokerats på den virtuella datorn. Välj **tillägg** på den virtuella datorns meny i Azure Portal och kontrol lera att de två agenterna visas.

![VM-tillägg](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Kontrol lera att den tilldelade system identiteten är aktive rad på den virtuella datorn
Kontrol lera att systemets tilldelade identitet är aktive rad på den virtuella datorn. Välj **identitet** från den virtuella datorns meny i Azure Portal. 

![Tilldelad identitet](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Verifiera data insamlings regel
Kontrol lera att data insamlings regeln som anger hälso tillägg som data källa är associerad med den virtuella datorn.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Fel meddelande för felaktig begäran på grund av otillräcklig behörighet
Det här felet indikerar att **Microsoft. WorkloadMonitor** Resource Provider inte registrerades i prenumerationen. Se [Azure Resource providers och-typer](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) för information om hur du registrerar resurs leverantören. 

![Felaktig begäran](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>Nästa steg

- [Få en översikt över gäst hälso funktionen i Azure Monitor for VMs](vminsights-health-overview.md)