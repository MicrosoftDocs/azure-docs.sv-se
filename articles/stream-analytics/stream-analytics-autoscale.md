---
title: Autoskala Stream Analytics jobb
description: Den här artikeln beskriver hur du automatiskt skalar Stream Analytics jobb baserat på ett fördefinierat schema eller värden för jobb mått
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: a8e089e302e9d40c69cf7ff2a3480c17894e1463
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016294"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>Skala Stream Analytics jobb med Azure Automation

Du kan optimera kostnaden för dina Stream Analytics jobb genom att konfigurera autoskalning. Med automatisk skalning ökar eller minskar du jobbets strömnings enheter (SUs) så att de matchar ändringen i din inläsnings belastning. I stället för att kunna överblicka ditt jobb kan du skala upp eller ned efter behov. Det finns två sätt att konfigurera jobben till autoskalning:
1. **Definiera ett schema för** en förutsägbar inläsning. Du förväntar dig till exempel en högre takt med ingångs händelser under dagtid och vill att jobbet ska köras med mer SUs.
2. **Utlös åtgärder för att skala upp och ned baserat på jobb mått** när du inte har en förutsägbar inläsning. Du kan ändra antalet SUs-enheter dynamiskt baserat på dina jobb mått, till exempel antalet ingångs händelser eller eftersläpande inloggade ingångs händelser.

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar konfigurera autoskalning för jobbet utför du följande steg.
1. Jobbet är optimerat för att ha en [parallell topologi](./stream-analytics-parallelization.md). Om du kan ändra skala för jobbet medan det körs har ditt jobb en parallell topologi och kan konfigureras för autoskalning.
2. [Skapa ett Azure Automation konto](../automation/automation-create-standalone-account.md) med alternativet "RunAsAccount" aktiverat. Kontot måste ha behörighet att hantera dina Stream Analytics-jobb.

## <a name="set-up-azure-automation"></a>Konfigurera Azure Automation
### <a name="configure-variables"></a>Konfigurera variabler
Lägg till följande variabler i Azure Automation-kontot. Dessa variabler kommer att användas i Runbooks som beskrivs i nästa steg.

| Namn | Typ | Värde |
| --- | --- | --- |
| **jobName** | Sträng | Namnet på det Stream Analytics jobb som du vill skala. |
| **resourceGroupName** | Sträng | Namnet på resurs gruppen där jobbet finns. |
| **subId** | Sträng | Prenumerations-ID som jobbet finns i. |
| **increasedSU** | Integer | Det högre SU-värdet som du vill att jobbet ska skalas efter enligt ett schema. Värdet måste vara ett av de giltiga SU-alternativen som visas i **skalnings** inställningarna för jobbet när det körs. |
| **decreasedSU** | Integer | Det lägre SU-värdet som du vill att jobbet ska skalas efter enligt ett schema. Värdet måste vara ett av de giltiga SU-alternativen som visas i **skalnings** inställningarna för jobbet när det körs. |
| **maxSU** | Integer | Det maximala SU-värdet som du vill att jobbet ska skalas till i steg vid automatisk skalning efter belastning. Värdet måste vara ett av de giltiga SU-alternativen som visas i **skalnings** inställningarna för jobbet när det körs. |
| **minSU** | Integer | Det minsta SU-värde som du vill att jobbet ska skalas till i steg vid automatisk skalning efter belastning. Värdet måste vara ett av de giltiga SU-alternativen som visas i **skalnings** inställningarna för jobbet när det körs. |

![Lägg till variabler i Azure Automation](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>Skapa runbooks
Nästa steg är att skapa två PowerShell-Runbooks. En för att skala upp och den andra för skalnings åtgärder.
1. I ditt Azure Automation konto går du till **Runbooks** under **process automatisering**  och väljer **skapa Runbook**.
2. Namnge den första Runbook- *ScaleUpRunbook* med typen inställt på PowerShell. Använd [ScaleUpRunbook PowerShell-skriptet](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1) som finns i GitHub. Spara och publicera den.
3. Skapa en annan Runbook med namnet *ScaleDownRunbook* med typen PowerShell. Använd [ScaleDownRunbook PowerShell-skriptet](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1) som finns i GitHub. Spara och publicera den.

![Autoskala Runbooks i Azure Automation](./media/autoscale/runbooks.png)

Nu har du Runbooks som automatiskt kan utlösa skalning upp-och nedskalning på ditt Stream Analytics jobb. Dessa Runbooks kan utlösas med ett fördefinierat schema eller ställas in dynamiskt baserat på jobb statistik.

## <a name="autoscale-based-on-a-schedule"></a>Autoskalning baserat på ett schema
Med Azure Automation kan du konfigurera ett schema för att utlösa dina runbooks.
1. I ditt Azure Automation konto väljer du **scheman** under **delade resurser**. Välj sedan **Lägg till ett schema**.
2. Du kan till exempel skapa två scheman. En som representerar när du vill att jobbet ska skalas upp och till ett annat som representerar när du vill att jobbet ska skalas ned. Du kan definiera en upprepning för dessa scheman.

   ![Scheman i Azure Automation](./media/autoscale/schedules.png)

3. Öppna din **ScaleUpRunbook** och välj sedan **scheman** under **resurser**. Sedan kan du länka din Runbook till ett schema som du skapade i föregående steg. Du kan ha flera scheman länkade till samma Runbook som kan vara till hjälp när du vill köra samma skalnings åtgärd vid olika tidpunkter på dagen.

![Schemalägga Runbooks i Azure Automation](./media/autoscale/schedulerunbook.png)

1. Upprepa föregående steg för **ScaleDownRunbook**.

## <a name="autoscale-based-on-load"></a>Autoskalning baserat på inläsning
Det kan finnas fall där du inte kan förutsäga inläsningen. I sådana fall är det mer optimalt att skala upp/ned i steg inom ett lägsta och högsta gräns värde. Du kan konfigurera varnings regler i Stream Analytics jobb för att utlösa runbooks när jobb måtten är över eller under ett tröskelvärde.
1. I ditt Azure Automation-konto skapar du två fler heltals variabler som heter **minSU** och **maxSU**. Detta anger de gränser inom vilka jobbet kommer att skalas i steg.
2. Skapa två nya Runbooks. Du kan använda [StepScaleUp PowerShell-skriptet](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1) som ökar SUS-jobbet i steg innan värdet för **maxSU** . Du kan också använda [PowerShell-skriptet StepScaleDown](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1) som minskar SUS-jobbet i steg tills **minSU** -värdet nås. Du kan också använda Runbooks från föregående avsnitt om du har vissa SU-värden som du vill skala till.
3. I Stream Analytics jobb väljer du **aviserings regler** under **övervakning**. 
4. Skapa två åtgärds grupper. En som ska användas för skalnings åtgärder och en annan för skalnings åtgärder. Välj **Hantera åtgärder** och klicka sedan på **Lägg till åtgärds grupp**. 
5. Fyll i de obligatoriska fälten. Välj **Automation Runbook** när du väljer **Åtgärds typ**. Välj den Runbook som du vill utlösa när aviseringen utlöses. Skapa sedan åtgärds gruppen.

   ![Skapa åtgärdsgrupp](./media/autoscale/create-actiongroup.png)
6. Skapa en [**ny varnings regel**](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) i jobbet. Ange ett villkor baserat på ett mått som du väljer. [ *Inmatade händelser*, *Su%-användning* eller *eftersläpande inloggade ingångs händelser*](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics) rekommenderas för att definiera logik för automatisk skalning. Vi rekommenderar också att du använder en *agg regerings kornig het* på 1 minut och *utvärderings frekvensen* när du utlöser skalnings åtgärder. Detta säkerställer att ditt jobb har gott om resurser för att hantera stora toppar i volymen.
7. Välj den åtgärds grupp som skapades i det sista steget och skapa aviseringen.
8. Upprepa steg 2 till 4 för eventuella ytterligare skalnings åtgärder som du vill utlösa baserat på villkoret för jobb mått.

Vi rekommenderar att du kör skalnings test innan du kör jobbet i produktionen. När du testar jobbet mot olika inläsningar av indata får du en uppfattning om hur många SUs-jobb som krävs för olika indata flöde. Detta kan informera de villkor som du definierar i aviserings reglerna som utlöser skalnings-och skalnings åtgärder. 

## <a name="next-steps"></a>Nästa steg
* [Skapa kan göras parallella-frågor i Azure Stream Analytics](stream-analytics-parallelization.md)
* [Skala Azure Stream Analytics jobb för att öka data flödet](stream-analytics-scale-jobs.md)