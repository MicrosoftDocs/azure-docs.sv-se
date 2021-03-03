---
title: Översikt över Azure Arc-aktiverade servrar
description: Lär dig hur du använder Azure Arc-aktiverade servrar för att hantera servrar som ligger utanför Azure som en Azure-resurs.
keywords: Azure Automation, DSC, PowerShell, önskad tillstånds konfiguration, uppdaterings hantering, ändrings spårning, inventering, Runbooks, python, grafisk, hybrid
ms.date: 02/18/2021
ms.topic: overview
ms.openlocfilehash: 5692dfaceb15086b04ee951b8ecdf88f73c7d122
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686231"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Vad är Azure Arc-aktiverade servrar?

Med Azure Arc-aktiverade servrar kan du hantera dina fysiska Windows-och Linux-servrar och virtuella datorer *utanför* Azure, i företags nätverket eller någon annan moln leverantör. Den här hanterings upplevelsen är utformad för att vara konsekvent med hur du hanterar interna virtuella Azure-datorer. När en hybrid dator är ansluten till Azure blir den en ansluten dator och behandlas som en resurs i Azure. Varje ansluten dator har ett resurs-ID, ingår i en resurs grupp och fördelar med Azures standard konstruktioner, till exempel Azure Policy och att använda taggar. Tjänste leverantörer som hanterar en kunds lokala infrastruktur kan hantera sina hybrid datorer, precis som de gör i dag med interna Azure-resurser, i flera kund miljöer med [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) med Azure Arc.

För att kunna leverera den här upplevelsen med dina hybrid datorer utanför Azure måste den Azure-anslutna dator agenten installeras på varje dator som du planerar att ansluta till Azure. Den här agenten levererar inga andra funktioner och ersätter inte Azure [Log Analytics-agenten](../../azure-monitor/agents/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs om du vill övervaka operativ system och arbets belastningar som körs på datorn proaktivt, hantera den med hjälp av Automation-runbooks eller lösningar som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-introduction.md).

## <a name="supported-scenarios"></a>Scenarier som stöds

När du ansluter datorn till Azure Arc-aktiverade servrar kan du utföra följande konfigurations hanterings-och övervaknings uppgifter:

- Tilldela [Azure policy gäst konfigurationer](../../governance/policy/concepts/guest-configuration.md) med samma erfarenhet som princip tilldelning för virtuella Azure-datorer. I dag tillämpar de flesta principer för gäst konfiguration inte konfigurationerna, de har bara gransknings inställningar i datorn. Mer information om kostnaden för att använda Azure Policy principer för gäst konfiguration med ARC-aktiverade servrar finns i Azure Policy [prissättnings guide](https://azure.microsoft.com/pricing/details/azure-policy/).

- Rapport om konfigurations ändringar av installerad program vara, Microsoft-tjänster, Windows-register och filer och Linux-daemon på övervakade servrar med hjälp av Azure Automation [ändringsspårning och inventering](../../automation/change-tracking/overview.md).

- Övervaka din anslutna dators prestanda för gäst operativ system och identifiera program komponenter för att övervaka deras processer och beroenden med andra resurser som programmet kommunicerar med hjälp av [Azure Monitor for VMS](../../azure-monitor/vm/vminsights-overview.md).

- Förenkla distributionen med andra Azure-tjänster som Azure Automation [tillstånds konfiguration](../../automation/automation-dsc-overview.md) och Azure Monitor Log Analytics arbets yta med de [Azure VM-tillägg](manage-vm-extensions.md) som stöds för din icke-Azure Windows-eller Linux-dator. Detta inkluderar konfiguration av konfiguration eller program vara efter distribution med hjälp av tillägget för anpassat skript.

- Använd [uppdateringshantering](../../automation/update-management/overview.md) i Azure Automation för att hantera operativ system uppdateringar för dina Windows-och Linux-servrar

    > [!NOTE]
    > För närvarande stöds inte aktivering av Uppdateringshantering direkt från en ARC-aktiverad server. Se [aktivera uppdateringshantering från ditt Automation-konto](../../automation/update-management/enable-from-automation-account.md) för att förstå kraven och hur du aktiverar för servern.

- Ta med dina icke-Azure-servrar för hot identifiering och proaktiv övervakning för potentiella säkerhetshot med hjälp av [Azure Security Center](../../security-center/security-center-introduction.md).

Loggdata som samlas in och lagras i en Log Analytics-arbetsyta från hybrid datorn innehåller nu egenskaper som är speciella för datorn, till exempel ett resurs-ID. Detta kan användas för att ge stöd åt [resurs Sammanhangs](../../azure-monitor/logs/design-logs-deployment.md#access-mode) logg åtkomst.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Regioner som stöds

En slutgiltig lista över regioner som stöds med Azure Arc-aktiverade servrar finns på sidan [Azure-produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) .

I de flesta fall ska den plats som du väljer när du skapar installations skriptet vara den Azure-region som är geografiskt närmast din dators plats. Data i vila lagras i den region i Azure som innehåller den region som du anger, vilket även kan påverka ditt val av region om du har data placering-krav. Om den Azure-region som datorn ansluter till påverkas av ett avbrott påverkas inte den anslutna datorn, men hanterings åtgärder som använder Azure kan inte slutföras. Om det uppstår ett regionalt avbrott, och om du har flera platser som stöder en geografiskt redundant tjänst, är det bäst att ansluta datorerna på varje plats till en annan Azure-region.

Följande metadatainformation om den anslutna datorn samlas in och lagras i den region där Azure Arc-datorns resurs har kon figurer ATS:

- Namn och version för operativ system
- Datornamn
- Fullständigt kvalificerat domän namn (FQDN)
- Version av ansluten dator agent

Om datorn till exempel är registrerad i Azure-bågen i regionen USA, östra, lagras dessa data i regionen USA.

### <a name="supported-environments"></a>Miljöer som stöds

Arc-aktiverade servrar stöder hantering av fysiska servrar och virtuella datorer som finns *utanför* Azure. För detaljerad information om vilka hybrid moln miljöer som är värdar för virtuella datorer, se [krav för anslutna maskin agenter](agent-overview.md#supported-environments).

> [!NOTE]
> Arc-aktiverade servrar har inte utformats eller stöds för att aktivera hantering av virtuella datorer som körs i Azure.

### <a name="agent-status"></a>Agent status

Den anslutna dator agenten skickar ett vanligt pulsslags meddelande till tjänsten var 5: e minut. Om tjänsten slutar att ta emot dessa pulsslags meddelanden från en dator anses datorn vara offline och statusen ändras automatiskt till **frånkopplad** i portalen inom 15 till 30 minuter. När du tar emot ett efterföljande pulsslags meddelande från den anslutna dator agenten kommer dess status automatiskt att ändras till **ansluten**.

## <a name="next-steps"></a>Nästa steg

Innan du utvärderar eller aktiverar Arc-aktiverade servrar över flera hybrid datorer kan du läsa [Översikt över anslutna dator agenter](agent-overview.md) för att förstå krav, teknisk information om agenten och distributions metoder.