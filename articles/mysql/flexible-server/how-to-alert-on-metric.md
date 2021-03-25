---
title: Konfigurera mått aviseringar – Azure Portal-Azure Database for MySQL – flexibel Server
description: Den här artikeln beskriver hur du konfigurerar och får åtkomst till mått aviseringar för Azure Database for MySQL flexibel Server från Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: be52bbe58f6c2ff6ee21703860e8d8e00ec30072
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110159"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql---flexible-server"></a>Använd Azure Portal för att ställa in aviseringar för mått för Azure Database for MySQL flexibel Server 

> [!IMPORTANT] 
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Den här artikeln visar hur du konfigurerar Azure Database for MySQL aviseringar med hjälp av Azure Portal. Du kan få en avisering utifrån övervaknings måtten för dina Azure-tjänster.

Aviseringen utlöses när värdet för ett angivet mått korsar ett tröskelvärde som du tilldelar. Aviseringen utlöses både när villkoret först uppfylls och därefter när villkoret inte längre uppfylls. Mått varningar är tillstånds känsliga, det vill säga de skickar bara meddelanden när tillstånd ändras.

Du kan konfigurera en avisering för att utföra följande åtgärder när den utlöser:
* Skicka e-postaviseringar till tjänst administratören och medadministratörer
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* Anropa en webbhook

Du kan konfigurera och hämta information om aviserings regler med hjälp av:
* [Azure-portalen](../../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [REST-API:et för Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Skapa en aviseringsregel för ett mått från Azure-portalen
1. I [Azure Portal](https://portal.azure.com/)väljer du den Azure Database for MySQL flexibla server som du vill övervaka.
2. Under avsnittet **övervakning** på sid panelen väljer du **aviseringar**.
3. Välj **+ Ny aviseringsregel**.
4. Sidan **Skapa regel** öppnas. Fyll i nödvändig information:
5. I avsnittet **villkor** väljer du **Välj villkor**.
6. En lista över signaler som stöds visas, Välj det mått som du vill skapa en avisering på. Välj till exempel "lagrings procent".
7. Ett diagram visas för måttet under de senaste sex timmarna. Använd List rutan **diagram period** för att välja att Visa längre historik för måttet.
8. Välj **tröskel** typ (t. ex. "Statisk" eller "dynamisk"), **operator** (t. ex. "Större än") och **agg regerings typ** (t. ex. Average). Detta fastställer logiken som varnings regeln för mått kommer att utvärdera.
    - Om du använder ett **statiskt** tröskelvärde kan du fortsätta att definiera ett **tröskelvärde** (t. ex. 85 procent). Mått diagrammet kan hjälpa dig att avgöra vad som kan vara ett rimligt tröskelvärde.
    - Om du använder ett **dynamiskt** tröskelvärde kan du fortsätta med att definiera **tröskelns känslighet**. Mått diagrammet visar de beräknade tröskelvärdena baserat på senaste data. [Läs mer om villkors typ och känslighets alternativ för dynamisk tröskel](../../azure-monitor/alerts/alerts-dynamic-thresholds.md).
9. Förfina villkoret genom att justera **agg regerings precisionen (period)** över vilka data punkter grupperas med hjälp av agg regerings typ funktionen (t. ex. "30 minuter") och **frekvens** (t. ex. var 15: e minut).
10. Klicka på **Klar**.
11. Lägg till en åtgärds grupp. En åtgärds grupp är en samling aviserings inställningar som definieras av ägaren av en Azure-prenumeration. I avsnittet **Åtgärds grupper** väljer du **Välj åtgärds grupp** för att välja en redan befintlig åtgärds grupp som ska bifogas aviserings regeln.
12. Du kan också skapa en ny åtgärds grupp för att ta emot meddelanden på aviseringen. Mer information hittar du i [skapa och hantera åtgärds grupp](../../azure-monitor/alerts/action-groups.md) .
13. Om du vill skapa en ny åtgärds grupp väljer du **+ skapa åtgärds grupp**. Fyll i formuläret "skapa åtgärds grupp" med en **prenumeration**, **resurs grupp**, **Åtgärds grupp namn** och **visnings namn**.
14. Konfigurera **meddelanden** för åtgärds gruppen.
    
    I **meddelande typ** väljer du "e-Azure Resource Manager roll" för att välja prenumerations ägare, deltagare och läsare för att ta emot meddelanden. Välj **Azure Resource Manager-rollen** för att skicka e-postmeddelandet.
    Du kan också välja **e-post/SMS-meddelande/push/röst** för att skicka meddelanden till vissa mottagare.

    Ange ett **namn** på meddelande typen och välj **Granska + skapa** när du är klar.

    <!--:::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Action group":::-->
    
15. Fyll i **aviserings regel information** som **varnings regel namn**, **Beskrivning**, **Spara aviserings regel i resurs grupp** och **allvarlighets grad**.

    <!--:::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Action group":::-->

16. Välj **skapa aviserings regel** för att skapa aviseringen.
    Inom några minuter är aviseringen aktiv och utlösare enligt beskrivningen ovan.
## <a name="manage-your-alerts"></a>Hantera aviseringar
När du har skapat en avisering kan du välja den och utföra följande åtgärder:

* Visa ett diagram som visar mått tröskelvärdet och de faktiska värdena från föregående dag som är relevanta för den här aviseringen.
* **Redigera** eller **ta bort** varnings regeln.
* **Inaktivera** eller **Aktivera** aviseringen om du tillfälligt vill stoppa eller återuppta mottagning av meddelanden.


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om att [ställa in aviseringar för mått](../../azure-monitor/alerts/alerts-metric.md).
- Lär dig mer om tillgängliga [mått i Azure Database for MySQL flexibel Server](./concepts-monitoring.md).
- [Förstå hur måttaviseringar fungerar i Azure Monitor](../../azure-monitor/alerts/alerts-metric-overview.md)