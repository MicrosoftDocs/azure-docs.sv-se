---
title: översikt Azure Arc aktiverade servrar
description: Lär dig hur du använder Azure Arc-aktiverade servrar för att hantera servrar som finns utanför Azure, till exempel en Azure-resurs.
keywords: azure automation, DSC, powershell, desired state configuration, update management, change tracking, inventory, runbooks, python, graphical, hybrid
ms.date: 04/21/2021
ms.topic: overview
ms.openlocfilehash: 324f6cc29bd9e4eca1a20413032c213c2618a11e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832004"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Vad är Azure Arc-aktiverade servrar?

Azure Arc-aktiverade servrar kan du hantera dina fysiska Windows- och Linux-servrar och virtuella datorer som finns utanför Azure, i företagsnätverket eller någon annan molnleverantör.  Den här hanteringsupplevelsen är utformad för att vara konsekvent med hur du hanterar interna virtuella Azure-datorer. När en hybriddator är ansluten till Azure blir den en ansluten dator och behandlas som en resurs i Azure. Varje ansluten dator har ett resurs-ID, ingår i en resursgrupp och drar nytta av Azure-standardkonstruktioner som att Azure Policy och tillämpa taggar. Tjänstleverantörer som hanterar en kunds lokala infrastruktur kan hantera sina hybriddatorer, precis som de gör i dag med interna Azure-resurser i flera kundmiljöer, med [hjälp av Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) med Azure Arc.

För att leverera den här upplevelsen med dina hybriddatorer som finns utanför Azure måste Azure Connected Machine-agenten installeras på varje dator som du planerar att ansluta till Azure. Den här agenten levererar inte några andra funktioner och ersätter inte Azure [Log Analytics-agenten](../../azure-monitor/agents/log-analytics-agent.md). Log Analytics-agenten för Windows och Linux krävs när du proaktivt vill övervaka operativsystemet och arbetsbelastningar som körs på datorn, hantera det med hjälp av Automation-runbooks eller lösningar som Uppdateringshantering eller använda andra Azure-tjänster [som Azure Security Center](../../security-center/security-center-introduction.md).

## <a name="supported-scenarios"></a>Scenarier som stöds

När du ansluter datorn till Azure Arc-aktiverade servrar kan du utföra följande hanterings- och övervakningsuppgifter för konfiguration:

- Tilldela [Azure Policy gästkonfigurationer med](../../governance/policy/concepts/guest-configuration.md) samma upplevelse som principtilldelning för virtuella Azure-datorer. Idag tillämpar de flesta gästkonfigurationsprinciper inte konfigurationer, de granskar bara inställningar på datorn. Information om kostnaden för att använda Azure Policy gästkonfigurationsprinciper med Arc-aktiverade servrar finns i Azure Policy [prisguiden](https://azure.microsoft.com/pricing/details/azure-policy/).

- Rapportera om konfigurationsändringar av installerad programvara, Microsoft-tjänster, Windows-register och -filer samt Linux-daemons på övervakade servrar med Azure Automation [Ändringsspårning och inventering](../../automation/change-tracking/overview.md) och [Azure Security Center File Integrity Monitoring](../../security-center/security-center-file-integrity-monitoring.md), för servrar som är aktiverade med Azure Defender för [servrar](../../security-center/defender-for-servers-introduction.md).

- Övervaka prestandan för gästoperativsystemet på den anslutna datorn och identifiera programkomponenter för att övervaka deras processer och beroenden med andra resurser som programmet kommunicerar med hjälp av [VM Insights.](../../azure-monitor/vm/vminsights-overview.md)

- Förenkla distributionen med andra Azure-tjänster som Azure Automation [State Configuration](../../automation/automation-dsc-overview.md) och Azure Monitor Log Analytics-arbetsytan, med hjälp av [de Azure VM-tillägg](manage-vm-extensions.md) som stöds för din Windows- eller Linux-dator som inte är Azure. Detta inkluderar att utföra en konfiguration efter distributionen eller programvaruinstallation med hjälp av det anpassade skripttillägget.

- Använd [Uppdateringshantering](../../automation/update-management/overview.md) i Azure Automation för att hantera uppdateringar av operativsystem för dina Windows- och Linux-servrar

    > [!NOTE]
    > För närvarande stöds inte aktivering Uppdateringshantering direkt från en Arc-aktiverad server. Se [Aktivera Uppdateringshantering automation-kontot för att](../../automation/update-management/enable-from-automation-account.md) förstå kraven och hur du aktiverar för din server.

- Inkludera dina icke-Azure-servrar för hotidentifiering och övervaka proaktivt potentiella säkerhetshot med [hjälp av Azure Security Center](../../security-center/security-center-introduction.md).

Loggdata som samlas in och lagras på en Log Analytics-arbetsyta från hybriddatorn innehåller nu egenskaper som är specifika för datorn, till exempel ett resurs-ID. Detta kan användas för att stödja åtkomst [till resurskontextloggar.](../../azure-monitor/logs/design-logs-deployment.md#access-mode)

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Regioner som stöds

En slutgiltig lista över regioner som stöds med Azure Arc-aktiverade servrar finns på sidan [Azure-produkter efter](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) region.

I de flesta fall bör den plats som du väljer när du skapar installationsskriptet vara den Azure-region som geografiskt ligger närmast datorns plats. Vilodata lagras inom azure-geografin som innehåller den region som du anger, vilket även kan påverka ditt val av region om du har krav på datahemlighet. Om den Azure-region som datorn ansluter till påverkas av ett avbrott påverkas inte den anslutna datorn, men hanteringsåtgärder som använder Azure kanske inte kan slutföras. Om det finns ett regionalt avbrott, och om du har flera platser som stöder en geografiskt redundant tjänst, är det bäst att ansluta datorerna på varje plats till en annan Azure-region.

Följande metadatainformation om den anslutna datorn samlas in och lagras i den region där Azure Arc datorresursen har konfigurerats:

- Namn och version för operativsystem
- Datornamn
- Datorns fullständiga domännamn (FQDN)
- Agentversion för ansluten dator

Om datorn till exempel är registrerad i Azure Arc regionen USA, östra lagras dessa data i regionen USA.

### <a name="supported-environments"></a>Miljöer som stöds

Arc-aktiverade servrar stöder hantering av fysiska servrar och virtuella datorer *som* finns utanför Azure. Specifik information om vilka hybridmolnmiljöer som är värdar för virtuella datorer finns i [Krav för Connected Machine-agenten.](agent-overview.md#supported-environments)

> [!NOTE]
> Arc-aktiverade servrar är inte utformade eller stöds inte för att aktivera hantering av virtuella datorer som körs i Azure.

### <a name="agent-status"></a>Agentstatus

Connected Machine-agenten skickar ett vanligt pulsslagsmeddelande till tjänsten var femte minut. Om tjänsten slutar ta emot dessa pulsslagsmeddelanden från en dator betraktas  datorn som offline och statusen ändras automatiskt till Frånkopplad i portalen inom 15 till 30 minuter. När du får ett efterföljande pulsslagsmeddelande från connected machine-agenten ändras dess status automatiskt till **Ansluten**.

## <a name="next-steps"></a>Nästa steg

Innan du utvärderar eller aktiverar Arc-aktiverade servrar över flera hybriddatorer bör du läsa Översikt över Connected [Machine-agenten](agent-overview.md) för att förstå krav, teknisk information om agenten och distributionsmetoder.