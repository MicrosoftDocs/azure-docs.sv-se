---
title: Skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor
description: Lär dig hur du använder Azure Portal eller CLI för att skapa, Visa och hantera mått varnings regler.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 01/11/2021
ms.subservice: alerts
ms.openlocfilehash: e3a9810aef356b7acf5ae2522645c14ee445812b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100622012"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Skapa, visa och hantera måttaviseringar med Azure Monitor

Mått varningar i Azure Monitor ger dig ett sätt att få ett meddelande när ett av dina mått överskrider ett tröskelvärde. Du kan använda måttaviseringar för en mängd olika flerdimensionella plattformsmått, anpassade mått samt vanliga och anpassade Application Insights-mått. I den här artikeln beskriver vi hur du skapar, visar och hanterar aviserings regler för mått via Azure Portal och Azure CLI. Du kan också skapa Mät regler för mått med Azure Resource Manager mallar, som beskrivs i [en separat artikel](../platform/alerts-metric-create-templates.md).

Du kan lära dig mer om hur mått aviseringar fungerar från [Översikt över mått aviseringar](../platform/alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Skapa med Azure Portal

Följande procedur beskriver hur du skapar en regel för mått varningar i Azure Portal:

1. Klicka på **övervaka** i [Azure Portal](https://portal.azure.com). Bladet övervakning konsoliderar alla övervaknings inställningar och data i en vy.

2. Klicka på **aviseringar** och klicka sedan på **+ ny varnings regel**.

    > [!TIP]
    > De flesta resurs blad innehåller **aviseringar** på resurs menyn under **övervakning**. du kan även skapa aviseringar därifrån.

3. Klicka på **Välj mål**, i kontext fönstret som läses in, väljer du en mål resurs som du vill Avisera om. Använd List rutorna **prenumeration** och **resurs typ** för att hitta den resurs som du vill övervaka. Du kan också använda Sök fältet för att hitta din resurs.

4. Om den valda resursen har mått som du kan skapa aviseringar på, kommer **tillgängliga signaler** längst ned till höger att innehålla mått. Du kan visa en fullständig lista över resurs typer som stöds för mått aviseringar i den här [artikeln](../platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

5. När du har valt en mål resurs klickar du på **Lägg till villkor**.

6. En lista över signaler som stöds för resursen visas, Välj det mått som du vill skapa en avisering på.

7. Ett diagram visas för måttet under de senaste sex timmarna. Använd List rutan **diagram period** för att välja att Visa längre historik för måttet.

8. Om måttet har dimensioner, visas en dimensions tabell som visas. Välj ett eller flera värden per dimension.
    - Dimensions värden som visas baseras på mått data från den senaste dagen.
    - Om det dimensions värde som du söker efter inte visas klickar du på Lägg till anpassat värde om du vill lägga till ett anpassat dimensions värde.
    - Du kan också **välja alla aktuella och framtida värden** för någon av dimensionerna. Detta skalar markeringen dynamiskt till alla aktuella och framtida värden för en dimension.

    Mått varnings regeln utvärderar villkoret för alla kombinationer av värden som har valts. [Lär dig mer om hur aviseringar om flerdimensionella mått fungerar](../platform/alerts-metric-overview.md).

9. Välj typ av **tröskel** , **operator** och **sammansättning**. Detta fastställer logiken som varnings regeln för mått kommer att utvärdera.
    - Om du använder ett **statiskt** tröskelvärde kan du fortsätta att definiera ett **tröskelvärde**. Mått diagrammet kan hjälpa dig att avgöra vad som kan vara ett rimligt tröskelvärde.
    - Om du använder ett **dynamiskt** tröskelvärde kan du fortsätta med att definiera **tröskelns känslighet**. Mått diagrammet visar de beräknade tröskelvärdena baserat på senaste data. [Läs mer om villkors typ och känslighets alternativ för dynamisk tröskel](../alerts/alerts-dynamic-thresholds.md).

10. Du kan också förfina villkoret genom att justera **agg regerings precisionen** och **utvärderings frekvensen**. 

11. Klicka på **Klar**.

12. Du kan också lägga till ytterligare villkor om du vill övervaka en komplex varnings regel. För närvarande kan användare ha varnings regler med villkor för dynamiska tröskelvärden som ett enda kriterium.

13. Fyll i **aviserings information** som **aviserings regel namn**, **Beskrivning** och **allvarlighets grad**.

14. Lägg till en åtgärds grupp i aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.

15. Klicka på **Slutför** om du vill spara varnings regeln för mått.

> [!NOTE]
> Mått varnings regler som skapas via portalen skapas i samma resurs grupp som mål resursen.

## <a name="view-and-manage-with-azure-portal"></a>Visa och hantera med Azure Portal

Du kan visa och hantera mått varnings regler med hjälp av bladet hantera regler under aviseringar. I proceduren nedan visas hur du visar måttet för mått avisering och redigerar en av dem.

1. I Azure Portal navigerar du till **övervaka**

2. Klicka på **aviseringar** och **Hantera regler**

3. På bladet **Hantera regler** kan du Visa alla aviserings regler för alla prenumerationer. Du kan filtrera reglerna ytterligare med  **resurs grupp**, **resurs typ** och **resurs**. Om du bara vill se mått aviseringar väljer du **signal typ** som mått.

    > [!TIP]
    > På bladet **Hantera regler** kan du välja flera varnings regler och aktivera/inaktivera dem. Detta kan vara användbart när vissa mål resurser måste placeras under underhåll

4. Klicka på namnet på mått varnings regeln som du vill redigera

5. I redigera-regeln klickar du på det **aviserings villkor** som du vill redigera. Du kan ändra mått, tröskel villkor och andra fält efter behov

    > [!NOTE]
    > Du kan inte redigera **mål resursen** och **aviserings regelns namn** när måttets avisering har skapats.

6. Klicka på **Slutför** för att spara ändringarna.


## <a name="with-azure-cli"></a>Med Azure CLI

I föregående avsnitt beskrivs hur du skapar, visar och hanterar aviserings regler för mått med hjälp av Azure Portal. Det här avsnittet beskriver hur du gör på samma sätt som med plattforms oberoende [Azure CLI](/cli/azure/get-started-with-azure-cli). Det snabbaste sättet att börja använda Azure CLI är genom [Azure Cloud Shell](../../cloud-shell/overview.md). I den här artikeln ska vi använda Cloud Shell.

1. Gå till Azure Portal och klicka på **Cloud Shell**.

2. I kommando tolken kan du använda kommandon med ``--help`` alternativ för att lära dig mer om kommandot och hur du använder det. Följande kommando visar till exempel en lista med kommandon som är tillgängliga för att skapa, Visa och hantera mått varningar

    ```azurecli
    az monitor metrics alert --help
    ```

3. Du kan skapa en enkel mått varnings regel som övervakar om genomsnitts procent CPU på en virtuell dator är större än 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Du kan visa alla mått varningar i en resurs grupp med hjälp av följande kommando

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Du kan se information om en viss mått varnings regel med hjälp av namnet eller resurs-ID: t för regeln.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. Du kan inaktivera en regel för mått avisering med hjälp av följande kommando.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Du kan ta bort en regel för mått avisering med hjälp av följande kommando.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="with-powershell"></a>Med PowerShell

Mått för varnings regler har dedikerade PowerShell-cmdlets tillgängliga:

- [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2): skapa en ny regel för mått varning eller uppdatera en befintlig.
- [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2): Hämta en eller flera mått för varnings regler.
- [Remove-AzMetricAlertRuleV2](/powershell/module/az.monitor/remove-azmetricalertrulev2): ta bort en mått regel för varningar.

## <a name="with-rest-api"></a>Med REST-API

- [Skapa eller uppdatera](/rest/api/monitor/metricalerts/createorupdate): skapa en ny regel för mått varning eller uppdatera en befintlig.
- [Hämta](/rest/api/monitor/metricalerts/get): Hämta en varnings regel för en bestämd mått.
- [Lista efter resurs grupp](/rest/api/monitor/metricalerts/listbyresourcegroup): Hämta en lista över mått varnings regler i en speciell resurs grupp.
- [Lista efter prenumeration](/rest/api/monitor/metricalerts/listbysubscription): Hämta en lista över mått varnings regler i en speciell prenumeration.
- [Uppdatera](/rest/api/monitor/metricalerts/update): uppdatera en varnings regel för mått.
- [Ta bort](/rest/api/monitor/metricalerts/delete): ta bort en regel för mått varningar.

## <a name="next-steps"></a>Nästa steg

- [Skapa mått aviseringar med hjälp av Azure Resource Manager mallar](../platform/alerts-metric-create-templates.md)
- [Förstå hur mått aviseringar fungerar](../platform/alerts-metric-overview.md)
- [Förstå hur mått aviseringar med villkor för dynamiskt tröskelvärden fungerar](../alerts/alerts-dynamic-thresholds.md)
- [Förstå webbhook-schemat för mått varningar](../platform/alerts-metric-near-real-time.md#payload-schema)
- [Felsöka problem i mått aviseringar](../platform/alerts-troubleshoot-metric.md)
