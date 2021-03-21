---
title: Felsöka VM Insights-gäst hälsa (för hands version)
description: Beskriver fel söknings steg som du kan vidta när du har problem med VM Insights-hälsa.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: d8b37569ebaa8e75be601a1efd65a23a61aeaa75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051947"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>Felsöka VM Insights-gäst hälsa (för hands version)
I den här artikeln beskrivs fel söknings steg som du kan vidta när du har problem med VM Insights-hälsa.

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
Kontrol lera att systemets tilldelade identitet är aktive rad på den virtuella datorn. Välj **identitet** från den virtuella datorns meny i Azure Portal. Om användarens hanterade identitet är aktive rad, oavsett status för systemets hanterade identitet, kommer Azure Monitor Agent inte att kunna kommunicera med konfigurations tjänsten och gäst hälso tillägget fungerar inte.

![Tilldelad identitet](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Verifiera data insamlings regel
Kontrol lera att data insamlings regeln som anger hälso tillägg som data källa är associerad med den virtuella datorn.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Fel meddelande för felaktig begäran på grund av otillräcklig behörighet
Det här felet indikerar att **Microsoft. WorkloadMonitor** Resource Provider inte registrerades i prenumerationen. Se [Azure Resource providers och-typer](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) för information om hur du registrerar resurs leverantören. 

![Felaktig begäran](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>Nästa steg

- [Få en översikt över gäst hälso funktionen i VM Insights](vminsights-health-overview.md)