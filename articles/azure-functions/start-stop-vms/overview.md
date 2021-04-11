---
title: Starta/stoppa VM v2 (för hands version) – Översikt
description: I den här artikeln beskrivs två av funktionerna starta/stoppa virtuella datorer (för hands version) som startar eller stoppar Azure Resource Manager och klassiska virtuella datorer enligt ett schema.
ms.topic: conceptual
ms.service: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.openlocfilehash: aac50f085d3ad63fb0183c19cf7d9aaa679e3d04
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112171"
---
# <a name="startstop-vms-v2-preview-overview"></a>Starta/stoppa VM v2 (för hands version) – Översikt

Funktionen starta/stoppa virtuella datorer v2 (för hands version) startar eller stoppar virtuella Azure-datorer (VM) över flera prenumerationer. Den startar eller stoppar virtuella Azure-datorer enligt användardefinierade scheman, ger insikter via [Azure Application insikter](../../azure-monitor/app/app-insights-overview.md)och skickar valfria aviseringar med hjälp av [Åtgärds grupper](../../azure-monitor/alerts/action-groups.md). Funktionen kan hantera både Azure Resource Manager virtuella datorer och klassiska virtuella datorer för de flesta scenarier.

Den här nya versionen av starta/stoppa virtuella datorer v2 (för hands version) tillhandahåller ett avcentraliserat alternativ för automatisering av låga kostnader för kunder som vill optimera sina VM-kostnader. Den innehåller samma funktioner som den [ursprungliga versionen](../../automation/automation-solution-vm-management.md) som är tillgänglig med Azure Automation, men den är utformad för att dra nytta av nyare teknik i Azure.

## <a name="overview"></a>Översikt

Starta/stoppa virtuella datorer v2 (för hands version) har gjorts om och är inte beroende av Azure Automation eller Azure Monitor loggar, vilket krävs i den [tidigare versionen](../../automation/automation-solution-vm-management.md). Den här versionen använder [Azure Functions](../../azure-functions/functions-overview.md) för att hantera VM-start och stoppa körning.

En hanterad identitet skapas i Azure Active Directory (Azure AD) för det här Azure Functions programmet och gör det möjligt att starta/stoppa VM v2 (för hands version) för enkel åtkomst till andra Azure AD-skyddade resurser, till exempel Logic Apps och virtuella Azure-datorer. Mer information om hanterade identiteter i Azure AD finns i [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

En slut punkts funktion för HTTP-utlösare skapas för att stödja schema-och sekvens-scenarier som ingår i funktionen, som visas i följande tabell.

|Name |Utlösare |Beskrivning |
|-----|--------|------------|
|AlertAvailabilityTest |Timer |Den här funktionen utför tillgänglighets testet för att kontrol lera att den primära funktionen **AutoStopVM** alltid är tillgänglig.|
|Stoppa |HTTP |Den här funktionen stöder scenariot för **autostopp** , vilket är start punkts funktionen som anropas från Logic app.|
|AutoStopAvailabilityTest |Timer |Den här funktionen utför tillgänglighets testet för att kontrol lera att den primära funktionen **autostop** alltid är tillgänglig.|
|AutoStopVM |HTTP |Den här funktionen utlöses automatiskt av den virtuella datorns avisering när varnings villkoret är sant.|
|CreateAutoStopAlertExecutor |Kö |Den här funktionen hämtar nytto Last informationen från funktionen **autostop** för att skapa aviseringen på den virtuella datorn.|
|Schemalagd |HTTP |Den här funktionen är för både schemalagt och sekvenserat scenario (differentieras av nytto Last schema). Det är start punkts funktionen som anropas från Logic-appen och tar nytto lasten för att bearbeta den virtuella datorns start-eller stopp åtgärd. |
|ScheduledAvailabilityTest |Timer |Den här funktionen utför tillgänglighets testet för att kontrol lera att den **schemalagda** primära funktionen alltid är tillgänglig.|
|VirtualMachineRequestExecutor |Kö |Den här funktionen utför den faktiska start-och stopp åtgärden på den virtuella datorn.|
|VirtualMachineRequestOrchestrator |Kö |Den här funktionen hämtar nytto Last informationen från den **schemalagda** funktionen och dirigerar start-och stopp förfrågningar för virtuella datorer.|

Till exempel används funktionen för **schemalagd** http-utlösare för att hantera scenarier för schema och sekvenser. På samma sätt hanterar funktionen **autostop** http-utlösare det automatiska stopp scenariot.

De köade utlösare som krävs för den här funktionen krävs. Alla timer-baserade utlösare används för att utföra tillgänglighets testet och för att övervaka systemets hälso tillstånd.

 [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) används för att konfigurera och hantera start-och stopp scheman för den virtuella datorns åtgärd genom att anropa funktionen med en JSON-nyttolast. Som standard skapas totalt fem Logic Apps i följande scenarier under den inledande distributionen:

- Schemalagda start-och stopp åtgärder baseras på ett schema som du anger mot Azure Resource Manager och klassiska virtuella datorer. **ststv2_vms_Scheduled_start** och **ststv2_vms_Scheduled_stop** Konfigurera schemalagd start och stopp.

- Sekvenserade-start-och stopp åtgärder baseras på ett schema som riktar sig mot virtuella datorer med fördefinierade sekvensering-taggar. Endast två namngivna Taggar stöds – **sequencestart** och **sequencestop**. **ststv2_vms_Sequenced_start** och **ststv2_vms_Sequenced_stop** konfigurera sekvenserad start och stopp.

    > [!NOTE]
    > Det här scenariot stöder endast Azure Resource Manager virtuella datorer.

- Autostopp – den här funktionen används bara för att utföra en stopp åtgärd mot både Azure Resource Manager och klassiska virtuella datorer baserat på dess CPU-belastning. Det kan också vara en schemalagd-baserad *åtgärd*, som skapar aviseringar på virtuella datorer och baserat på villkoret, som aviseringen utlöses för att utföra åtgärden stoppa. **ststv2_vms_AutoStop** konfigurerar funktionen för automatisk stopp.

Varje start/stopp-åtgärd stöder tilldelning av en eller flera prenumerationer, resurs grupper eller en lista över virtuella datorer.

Ett Azure Storage konto, som krävs av functions, används också av starta/stoppa VM v2 (för hands version) i två syfte:

   - Använder Azure Table Storage för att lagra metadata för körnings åtgärden (det vill säga åtgärden starta/stoppa VM).

   - Använder Azure Queue Storage för att stödja Azure Functions Queue-baserade utlösare.

Alla telemetridata, som är spårnings loggar från funktionen app-körning, skickas till den anslutna Application Insights-instansen. Du kan visa telemetri-data som lagras i Application Insights från en uppsättning fördefinierade visualiseringar som presenteras i en delad [Azure-instrumentpanel](../../azure-portal/azure-portal-dashboards.md).

:::image type="content" source="media/overview/shared-dashboard-sml.png" alt-text="Starta/stoppa VM-delad status instrument panel":::

E-postaviseringar skickas också som ett resultat av de åtgärder som utförs på de virtuella datorerna.

## <a name="new-releases"></a>Nya versioner

När en ny version av starta/stoppa virtuella datorer v2 (för hands version) lanseras uppdateras din instans automatiskt utan att behöva distribuera manuellt.

## <a name="supported-scoping-options"></a>Omfångs alternativ som stöds

### <a name="subscription"></a>Prenumeration

Omfånget till en prenumeration kan användas när du behöver utföra start-och stopp åtgärden på alla virtuella datorer i en hel prenumeration, och du kan välja flera prenumerationer om det behövs.  

Du kan också ange en lista över virtuella datorer som ska undantas och ignorera dem från åtgärden. Du kan också använda jokertecken för att ange alla namn som samtidigt kan ignoreras.

### <a name="resource-group"></a>Resursgrupp

Omfånget till en resurs grupp kan användas när du behöver utföra start-och stopp åtgärden på alla virtuella datorer genom att ange ett eller flera resurs grupp namn, och över en eller flera prenumerationer.

Du kan också ange en lista över virtuella datorer som ska undantas och ignorera dem från åtgärden. Du kan också använda jokertecken för att ange alla namn som samtidigt kan ignoreras.

### <a name="vmlist"></a>VMList

Att ange en lista över virtuella datorer kan användas när du behöver utföra start-och stopp åtgärder på en särskild uppsättning virtuella datorer och över flera prenumerationer. Det här alternativet har inte stöd för att ange en lista över virtuella datorer som ska undantas.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).

- Ditt konto har beviljats [deltagar](../../role-based-access-control/built-in-roles.md#contributor) behörighet i prenumerationen.

- Starta/stoppa VM v2 (för hands version) är tillgängligt i alla globala Azure-regioner som visas på sidan produkter som är [tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=functions) för Azure Functions. För Azure Government molnet är det endast tillgängligt i den amerikanska regeringen i Virginia-regionen.

## <a name="next-steps"></a>Nästa steg

Om du vill distribuera den här funktionen läser du [distribuera starta/stoppa virtuella datorer](deploy.md) (för hands version).