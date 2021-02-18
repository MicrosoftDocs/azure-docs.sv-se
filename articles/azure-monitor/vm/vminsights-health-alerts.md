---
title: Azure Monitor for VMs varningar om gäst hälsa (för hands version)
description: Beskriver de aviseringar som skapats av Azure Monitor for VMs gäst hälsa, inklusive hur du aktiverar dem och konfigurerar meddelanden.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 30025f387768aaf1e4d642292c21d5b15ccc7451
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625631"
---
# <a name="azure-monitor-for-vms-guest-health-alerts-preview"></a>Azure Monitor for VMs varningar om gäst hälsa (för hands version)
Azure Monitor for VMs gäst hälsa kan du se hälso tillståndet för en virtuell dator så som den definieras av en uppsättning prestanda mätningar som samplas med jämna mellanrum. En avisering kan skapas när en virtuell dator eller övervakar ändringar i ett ohälsosamt tillstånd. Du kan visa och hantera dessa aviseringar med [de som skapats av varnings regler i Azure Monitor](../platform/alerts-overview.md) och välja att proaktivt meddelas när en ny avisering skapas.

## <a name="configure-alerts"></a>Konfigurera varningar
Du kan inte skapa en explicit varnings regel för Azure Monitor for VMs gäst hälsa medan den här funktionen är en för hands version. Som standard skapas aviseringar för varje virtuell dator, men inte för varje övervakare.  Det innebär att om en övervakare ändras till ett tillstånd som inte påverkar den virtuella datorns aktuella tillstånd, skapas ingen avisering eftersom den virtuella datorns tillstånd inte ändrades. 

Du kan inaktivera aviseringar för en viss virtuell dator eller för en viss övervakare på en virtuell dator från inställningen **aviserings status** i konfigurationen för den virtuella datorn i Azure Portal. Mer information om hur du konfigurerar övervakare i Azure Portal finns i [Konfigurera övervakning i Azure Monitor for VMS gäst hälsa (för hands version)](vminsights-health-configure.md) . Mer information om hur du konfigurerar övervakare på en uppsättning virtuella datorer finns i [Konfigurera övervakning i Azure Monitor for VMS gäst hälsa med hjälp av data insamlings regler (för hands version)](vminsights-health-configure-dcr.md) .

## <a name="alert-severity"></a>Allvarlighetsgrad för avisering
Allvarlighets graden för den avisering som skapats av gäst hälsa mappar direkt till den virtuella datorns allvarlighets grad eller Övervakare som utlöser aviseringen.

| Övervaka tillstånd | Allvarlighetsgrad för avisering |
|:---|:---|
| Kritiskt | Sev1 |
| Varning  | Sev2 |
| Felfri  | Sev4 |

## <a name="alert-lifecycle"></a>Aviserings livs cykel
En [Azure-avisering](../platform/alerts-overview.md) skapas för varje virtuell dator när den ändras till ett **varnings** -eller **kritiskt** tillstånd. Visa aviseringen från **aviseringar** på **Azure Monitor** -menyn eller den virtuella datorns meny i Azure Portal.

Om en avisering redan har statusen **utlöst** när den virtuella datorns tillstånd ändras, skapas inte en andra avisering, men allvarlighets graden för samma avisering ändras till att matcha den virtuella datorns tillstånd. Om den virtuella datorn till exempel ändras till **kritiskt** tillstånd när en **varnings** avisering redan har statusen **utlöst** , ändras den här aviseringens allvarlighets grad till **Sev1**. Om den virtuella datorn ändras till ett **varnings** tillstånd när en **Sev1** -avisering redan har statusen **utlöst** , ändras den aviseringens allvarlighets grad till **Sev2**. Om den virtuella datorn flyttas tillbaka till **felfritt** tillstånd löses aviseringen med allvarlighets grad ändrad till **Sev4**.

## <a name="viewing-alerts"></a>Visa aviseringar
Visa aviseringar som skapats av Azure Monitor for VMs gäst hälsa med andra [aviseringar i Azure Portal](../platform/alerts-overview.md#alerts-experience). Du kan välja **aviseringar** på **Azure Monitor** -menyn om du vill visa aviseringar för alla övervakade resurser eller välja **aviseringar** från en virtuell dators meny för att visa aviseringar för just den virtuella datorn.

## <a name="alert-properties"></a>Aviseringsegenskaper

### <a name="properties-in-the-azure-portal"></a>Egenskaper i Azure Portal
Egenskaperna för aviseringen när du visar den i Azure Portal beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:---|:---|
| Övervaka tillstånd innan aviseringen skapades | Tillstånd för övervakaren eller den virtuella datorn innan den här aviseringen startades första gången. |
| Övervaka tillstånd när aviseringen skapades | Tillstånd för övervakaren eller den virtuella datorn när aviseringen startades första gången. Detta är det tillstånd som orsakade aviseringen vid brand. |
| Om du vill veta mer om tillstånds över gången när aviseringen skapades | Länk till sidan för VM-hälsa där du kan se den exakta tillstånds över gången. Den här tillstånds över gången representerar instansen när övervakaren först **gick från felfritt tillstånd** till ett icke-felfritt tillstånd. |

Till exempel går en övervakare från **felfritt** till **kritiskt** vid tid t0 och en ny avisering utlöses med **Sev1**. Den går sedan från **kritisk** till **Varning** vid tiden T1 och aviseringens allvarlighets grad uppdateras till **Sev2**. Den blir **felfri** vid tiden T2 och aviseringen löses.

Aviserings egenskaperna kommer att ha dessa värden under hela sekvensen.

- Övervaka tillstånd innan aviseringen skapades: felfri
- Övervaka tillstånd när aviseringen skapades: kritiskt
- Om du vill veta mer om tillstånds över gången när aviseringen skapades: navigerings länken till tillstånds över gången skedde vid tidpunkten t0.


### <a name="properties-in-notifications"></a>Egenskaper i meddelanden
Egenskaperna för aviseringen som ingår i meddelanden beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:---|:---|
| Föregående övervaknings tillstånd | Tillstånd för övervakaren eller den virtuella datorn innan statusen ändrades. Om uppdateringen av allvarlighets graden för aviseringen utlöser den här aviseringen representerar den här egenskapen det tillstånd som var precis innan allvarlighets graden uppdaterades. |
| Nuvarande övervaknings tillstånd | Tillstånd för övervakaren eller den virtuella datorn när den ändrade status. Om aviseringens allvarlighets grad uppdatering utlöser den här aviseringen representerar den här egenskapen det nya läget. |
| Om du vill veta mer om den här tillstånds över gången | Länk till sidan för VM-hälsa där du kan se den exakta tillstånds över gången. Den här tillstånds över gången representerar instansen när övervakaren ändrade tillstånd som utlöste meddelandet. |

I exemplet ovan skulle meddelanden ha följande egenskaper vid varje tillfälle.

Meddelande mottaget vid tid T0
- Föregående övervaknings tillstånd: felfritt
- Aktuellt övervaknings tillstånd: kritiskt
- Om du vill veta mer om denna tillstånds över gång: navigerings länken till tillstånds över gången skedde vid tidpunkten t0.

Meddelande mottaget vid tid T1
- Föregående övervaknings tillstånd: kritiskt
- Aktuellt övervaknings tillstånd: varning
- Om du vill veta mer om denna tillstånds över gång: navigerings länken till tillstånds över gången skedde vid tiden T1.

Meddelande mottaget vid tid T2
- Föregående övervaknings tillstånd: varning
- Aktuellt övervaknings tillstånd: felfritt
- Om du vill veta mer om denna tillstånds över gång: navigerings länken till tillstånds över gången skedde vid tiden T2.

## <a name="configure-notifications"></a>Konfigurera meddelanden
För att proaktivt meddela en avisering som utlösts av gäst hälsa skapar du en [Åtgärds grupp](../alerts/action-groups.md) för att definiera olika åtgärder att utföra, till exempel skicka ett SMS-meddelande eller starta en Logic app. Skapa sedan en [Åtgärds regel](../alerts/alerts-action-rules.md) som anger omfattningen av övervakare och virtuella datorer och som använder den åtgärds gruppen.

I menyn **övervakare** i Azure Portal väljer du **aviseringar**.  Välj **Hantera åtgärder** och sedan **Åtgärds regler (för hands version)**. 

![Ny åtgärds regel](media/vminsights-health-alerts/action-rule-new.png)

Klicka på **ny åtgärds regel** för att skapa en ny regel. Klicka på **Välj** bredvid omfång och välj antingen en prenumeration, en resurs grupp eller en eller flera enskilda virtuella datorer. Meddelandet utlöses bara för virtuella datorer som faller inom omfånget.

![Åtgärds regel omfång](media/vminsights-health-alerts/action-rule-scope.png)

Klicka på **Lägg till** bredvid **filter**. Skapa ett filter där **övervaknings tjänsten är lika med VM-insikter – hälsa**. Lägg till andra filter för att ange de specifika aviseringar som ska utlösa meddelandet. Du kan till exempel använda **allvarlighets grad** för att matcha aviseringar från alla Övervakare som matchar en viss allvarlighets grad.

![Åtgärds regel filter](media/vminsights-health-alerts/action-rule-filter.png)

I **definiera i det här omfånget** väljer du **Åtgärds grupp** och väljer sedan den åtgärds grupp som ska associeras med övervakaren. Ge regeln ett namn och välj den resurs grupp som den ska sparas i. Skapa regeln genom att klicka på **skapa** .

![Åtgärds regel](media/vminsights-health-alerts/action-rule.png)


## <a name="next-steps"></a>Nästa steg

- [Aktivera gäst hälsa i Azure Monitor for VMs och inbyggda agenter.](vminsights-health-enable.md)
- [Konfigurera övervakare med hjälp av Azure Portal.](vminsights-health-configure.md)
- [Konfigurera övervakare med hjälp av data insamlings regler.](vminsights-health-configure-dcr.md)