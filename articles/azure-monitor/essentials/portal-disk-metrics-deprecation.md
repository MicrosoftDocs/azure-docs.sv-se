---
title: Föråldrade disk mått i Azure Portal | Microsoft Docs
description: Lär dig vilka disk mått som är föråldrade och hur du uppdaterar mått aviseringar för att använda nya mått.
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: 623d9385b9ae6b13c8964f655fb973fe67a0918a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100623170"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Föråldrade disk mått i Azure Portal

Inaktuella disk-relaterade mått kommer snart att tas bort från Azure Portal. Det finns en ny version av varje föråldrat mått som du kan använda. Den här artikeln visar vilka mått som är nya och hur du uppdaterar dina mått aviseringar för att använda dem.

## <a name="list-of-new-metrics"></a>Lista över nya mått

Den här tabellen mappar varje föråldrat mått till den motsvarar det nya måttet. 

|Föråldrat mått|Nytt mått (ersättnings)|
|----|----|
|KÖDJUP för data disk (inaktuellt)|Data disk Queue djup (för hands version)|
|Lästa byte på datadisk/SEK (inaktuellt)|Lästa byte på datadisk/SEK (för hands version)|
|Läs åtgärder för data disk/SEK (inaktuellt)|Läs åtgärder för data disk/SEK (för hands version)|
|Skrivna byte på datadisk/SEK (inaktuellt)|Skrivna byte på datadisk/SEK (för hands version)|
|Skriv åtgärder för data disk/SEK (inaktuellt)|Skriv åtgärder för data disk/SEK (för hands version)|
|OS-KÖDJUP (inaktuellt)|OS-ködjup (förhands granskning)|
|OS-lästa byte/SEK (inaktuellt)|OS-lästa byte/SEK (för hands version)|
|Läs åtgärder för operativ system/SEK (inaktuellt)|Läs åtgärder för operativ system/SEK (för hands version)|
|Skrivna byte för OS-byte/SEK (inaktuellt)|Skrivna byte för OS-byte/SEK (för hands version)|
|Skriv åtgärder för operativ system/SEK (inaktuellt)|Skriv åtgärder för operativ system/SEK (för hands version)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Migrera mått i dina mått aviseringar

Uppdatera dina mått aviseringar för att använda nya mått.

1. Sök efter **aviseringar** i Azure Portal. I avsnittet **tjänster** väljer du sedan **aviseringar**.

   > [!div class="mx-imgBorder"]
   > ![Aviserings tjänst](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. På sidan **aviseringar** väljer du knappen **Hantera aviserings regler** . 

   > [!div class="mx-imgBorder"]
   > ![Hantera aviseringsregler](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. Markera kryss rutan **Virtual Machines** i list rutan **resurs grupp** och markera kryss rutan **mått** i list rutan **signal typ** . 

   > [!div class="mx-imgBorder"]
   > ![Filtrera aviseringar](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. Identifiera villkor som relaterar till diskar i listan över mått. Klicka på regelns namn. 

   Namnet visas som en hyperlänk i kolumnen **namn** i tabellen.

   > [!div class="mx-imgBorder"]
   > ![Sök efter disk villkor](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. I avsnittet **villkor** på sidan **regel hantering** klickar du på aviseringens villkor. 

   Villkoret visas som en hyperlänk.  

   > [!div class="mx-imgBorder"]
   > ![Justera villkor](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   Sidan **Konfigurera signal logik** visas och inställningarna för villkoret visas i avsnittet **aviserings logik** på sidan.

6. Gör en registrering av de här inställningarna när de försvinner när du tar bort föråldrade mått.

   > [!div class="mx-imgBorder"]
   > ![Villkors regler](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Överväg att samla in dessa inställningar i en skärm bild eller i en textfil. 

7. Klicka på länken **tillbaka till signal-urvalet** .

   > [!div class="mx-imgBorder"]
   > ![Tillbaka till signal val](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. På sidan **Konfigurera signal logik** väljer du lämpligt ersättnings mått (nytt mått). Använd den [tabell](#update-metrics) som visas tidigare i den här artikeln för att identifiera namnet på det nya måttet.

   > [!TIP] 
   > Börja skriva i Sök fältet för att begränsa listan över mått namn. 

   > [!div class="mx-imgBorder"]
   > ![Välj nytt mått](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Klicka på knappen **Slutför** . 

   > [!div class="mx-imgBorder"]
   > ![Ange nytt mått](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Spara ändringarna genom att välja knappen **Spara** . 

    > [!div class="mx-imgBorder"]
    > ![Spara nya mått](./media/portal-disk-metrics-deprecation/save-new-metric.png)






