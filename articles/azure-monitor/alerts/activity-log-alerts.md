---
title: Aktivitets logg aviseringar i Azure Monitor
description: Meddelas via SMS, webhook, SMS, e-post och mer, när vissa händelser inträffar i aktivitets loggen.
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 2762a9fbeef516d62067b670b14ea54f4363d7fc
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045504"
---
# <a name="alerts-on-activity-log"></a>Aviseringar om aktivitetsloggar

## <a name="overview"></a>Översikt

Aktivitets logg aviseringar är aviseringar som aktive ras när en ny [aktivitets logg händelse](../essentials/activity-log-schema.md) inträffar som matchar de villkor som anges i aviseringen. Varnings regeln utlöses baserat på ordningen och volymen av de händelser som registrerats i [Azure aktivitets loggen](../essentials/platform-logs-overview.md). Aktivitets logg aviserings regler är Azure-resurser, så de kan skapas med hjälp av en Azure Resource Manager mall. De kan också skapas, uppdateras eller tas bort i Azure Portal. Den här artikeln beskriver koncepten bakom aktivitets logg aviseringar. Mer information om hur du skapar eller använder aktivitets logg aviserings regler finns i [skapa och hantera aktivitets logg aviseringar](alerts-activity-log.md).

> [!NOTE]
> * **Det går inte** att skapa aviseringar för händelser i aviserings kategorin för aktivitets loggen.
> * Aktivitets logg aviseringar med säkerhets kategorin kan definieras också i ett [nytt uppgraderat flöde](../../security-center/continuous-export.md?tabs=azure-portal) till [ServiceNow](../../security-center/export-to-siem.md)

Normalt skapar du aktivitets logg aviseringar för att ta emot meddelanden när:

* Specifika åtgärder sker i resurser i din Azure-prenumeration, och är ofta begränsade till specifika resurs grupper eller resurser. Du kanske till exempel vill bli meddelad när en virtuell dator i myProductionResourceGroup tas bort. Eller så kanske du vill bli meddelad om eventuella nya roller har tilldelats till en användare i din prenumeration.
* En tjänst hälso händelse inträffar. Tjänste hälso händelser innehåller meddelanden om incidenter och underhålls händelser som gäller för resurser i din prenumeration.

En enkel analoghet för att förstå villkor för vilka aviserings regler som kan skapas i aktivitets loggen är att utforska eller filtrera händelser via [aktivitets loggen i Azure Portal](../essentials/activity-log.md#view-the-activity-log). I Azure Monitor-aktivitets loggen kan en filtrera eller hitta nödvändig händelse och sedan skapa en avisering med hjälp av knappen **Lägg till aktivitets logg avisering** .

I båda fallen övervakar en aktivitets logg avisering endast för händelser i prenumerationen som aviseringen skapas i.

Du kan konfigurera en aktivitets logg avisering baserat på valfri toppnivå egenskap i JSON-objektet för en aktivitets logg händelse. Mer information finns i [Kategorier i aktivitets loggen](../essentials/activity-log.md#view-the-activity-log). Läs mer om tjänst hälso händelser i [ta emot aktivitets logg aviseringar för tjänst meddelanden](../../service-health/alerts-activity-log-service-notifications-portal.md). 

Aktivitets logg aviseringar har några vanliga alternativ:

- **Kategori**: administrativ, service Health, autoskalning, säkerhet, princip och rekommendation. 
- **Omfattning**: den enskilda resurs eller resurs uppsättning som aviseringen för aktivitets loggen har definierats för. Omfattningen för en aktivitets logg avisering kan definieras på olika nivåer:
    - Resurs nivå: till exempel för en enskild virtuell dator
    - Resurs grupps nivå: till exempel alla virtuella datorer i en speciell resurs grupp
    - Prenumerations nivå: till exempel alla virtuella datorer i en prenumeration (eller) alla resurser i en prenumeration
- **Resurs grupp**: som standard sparas varnings regeln i samma resurs grupp som det angivna målet i definitions området. Användaren kan också definiera resurs gruppen där varnings regeln ska lagras.
- **Resurs typ**: Resource Manager-definierad namnrymd för målet för aviseringen.
- **Åtgärds namn**: [Azure Resource providerns åtgärds](../../role-based-access-control/resource-provider-operations.md) namn som används för rollbaserad åtkomst kontroll i Azure. Åtgärder som inte har registrerats med Azure Resource Manager kan inte användas i en varnings regel för aktivitets logg.
- **Nivå**: händelsens allvarlighets grad (information, varning, fel eller kritisk).
- **Status**: händelsens status, vanligt vis startad, misslyckades eller lyckades.
- **Händelse som initieras av**: kallas även för anroparen. E-postadressen eller Azure Active Directory identifieraren för den användare som utförde åtgärden.

> [!NOTE]
> I en prenumeration på upp till 100 kan du skapa aviserings regler för en aktivitet av scope på något av följande: en enda resurs, alla resurser i resurs gruppen (eller) hela prenumerations nivån.

När en aktivitets logg avisering aktive ras använder den en åtgärds grupp för att generera åtgärder eller meddelanden. En åtgärds grupp är en återanvändbar uppsättning aviserings mottagare, till exempel e-postadresser, webhook-URL: er eller SMS-telefonnummer. Mottagarna kan refereras från flera aviseringar för att centralisera och gruppera dina aviserings kanaler. När du definierar aktivitets logg aviseringen har du två alternativ. Du kan:

* Använd en befintlig åtgärds grupp i aktivitets logg aviseringen.
* Skapa en ny åtgärds grupp.

Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper i Azure Portal](./action-groups.md).


## <a name="next-steps"></a>Nästa steg

- Få en [Översikt över aviseringar](./alerts-overview.md).
- Lär dig mer om att [skapa och ändra aktivitets logg aviseringar](alerts-activity-log.md).
- Granska [aktivitets logg aviseringens webhook-schema](../alerts/activity-log-alerts-webhook.md).
- Läs mer om [meddelanden om tjänst hälsa](../../service-health/service-notifications.md).