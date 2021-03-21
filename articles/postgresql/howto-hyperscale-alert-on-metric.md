---
title: Konfigurera aviseringar – storskalig (citus) – Azure Database for PostgreSQL
description: Den här artikeln beskriver hur du konfigurerar och får åtkomst till mått aviseringar för Azure Database for PostgreSQL-storskalig (citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: f5557140d77865a6d4c44316cecd512f877736e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577089"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Använd Azure Portal för att ställa in aviseringar för mått för Azure Database for PostgreSQL-storskalig skalning (citus)

Den här artikeln visar hur du konfigurerar Azure Database for PostgreSQL aviseringar med hjälp av Azure Portal. Du kan få en avisering utifrån [övervaknings måtten](concepts-hyperscale-monitoring.md) för dina Azure-tjänster.

Vi ställer in en avisering som ska utlösas när värdet för ett angivet mått överskrider ett tröskelvärde. Aviseringen utlöses när villkoret först uppfylls och fortsätter att utlösa efteråt.

Du kan konfigurera en avisering för att utföra följande åtgärder när den utlöser:
* Skicka e-postaviseringar till tjänst administratör och-administratörer.
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* Anropa en webhook.

Du kan konfigurera och hämta information om aviserings regler med hjälp av:
* [Azure-portalen](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [REST-API:et för Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Skapa en aviseringsregel för ett mått från Azure-portalen
1. I [Azure Portal](https://portal.azure.com/)väljer du den Azure Database for PostgreSQLs server som du vill övervaka.

2. Under avsnittet **övervakning** på sid panelen väljer du **aviseringar** som visas:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/2-alert-rules.png" alt-text="Välj aviserings regler":::

3. Välj **ny varnings regel** (+ ikon).

4. Sidan **Skapa regel** öppnas som visas nedan. Fyll i nödvändig information:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png" alt-text="Lägg till mått aviserings formulär":::

5. I avsnittet **villkor** väljer du **Lägg till**.

6. Välj ett mått i listan över signaler att bli aviserad om. I det här exemplet väljer du "lagrings procent".
   
   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png" alt-text="Skärm bild som visar sidan Konfigurera signal logik där du kan visa flera signaler.":::

7. Konfigurera aviserings logiken:

    * **Operator** (t. ex. "Större än")
    * **Tröskel värde** (t. ex. 85 procent)
    * Sammanställnings **kornig het** som mått regeln måste uppfyllas innan aviserings utlösare (t. ex. "Under de senaste 30 minuterna")
    * **utvärderings frekvens** (t. ex. "1 minut")
   
   Välj **klar** när du är klar.

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png" alt-text="Skärm bild som visar fönstret där du kan konfigurera aviserings logik.":::

8. I avsnittet **Åtgärds grupper** väljer du **Skapa nytt** för att skapa en ny grupp för att ta emot meddelanden på aviseringen.

9. Fyll i formuläret "Lägg till åtgärds grupp" med ett namn, ett kort namn, en prenumeration och en resurs grupp.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/9-add-action-group.png" alt-text="Skärm bild som visar formuläret Lägg till åtgärds grupp där du kan ange de värden som beskrivs.":::

10. Konfigurera en **e-post/SMS/push/röst** -åtgärds typ.
    
    Välj "e-Azure Resource Manager roll" om du vill skicka meddelanden till prenumerations ägare, deltagare och läsare.
   
    Välj **OK** när du är klar.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/10-action-group-type.png" alt-text="Skärm bild som visar fönstret e-post/S/push/Voice.":::

11. Ange ett namn, en beskrivning och en allvarlighets grad för varnings regeln.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png" alt-text="Skärm bild som visar fönstret aviserings information."::: 

12. Välj **skapa aviserings regel** för att skapa aviseringen.

    Inom några minuter är aviseringen aktiv och utlösare enligt beskrivningen ovan.

### <a name="managing-alerts"></a>Hantera aviseringar

När du har skapat en avisering kan du välja den och utföra följande åtgärder:

* Visa ett diagram som visar mått tröskelvärdet och de faktiska värdena från föregående dag som är relevanta för den här aviseringen.
* **Redigera** eller **ta bort** varnings regeln.
* **Inaktivera** eller **Aktivera** aviseringen om du tillfälligt vill stoppa eller återuppta mottagning av meddelanden.

## <a name="suggested-alerts"></a>Föreslagna aviseringar

### <a name="disk-space"></a>Diskutrymme

Övervakning och avisering är viktigt för varje citus-servergrupp (productal Scale). Den underliggande PostgreSQL-databasen kräver ledigt disk utrymme för att fungera korrekt. Om disken blir full kopplas databasens servernod till offline och vägrar att starta tills utrymmet är tillgängligt. Vid detta tillfälle kräver det en support förfrågan från Microsoft för att åtgärda situationen.

Vi rekommenderar att du ställer in disk utrymmes aviseringar på varje nod i varje server grupp, även för användning utan produktion. Aviseringar om disk utrymmes användning ger den förvarning som krävs för att gå över och hålla noderna felfria. För bästa resultat kan du prova en serie aviseringar på 75%, 85% och 95% användning. Procent andelen som ska väljas beror på data inmatnings hastigheten, eftersom snabb data inmatning fyller upp disken snabbare.

När disken närmar sig utrymmes gränsen kan du prova dessa tekniker för att få mer ledigt utrymme:

* Granska princip för data bevarande. Flytta äldre data till kall lagring om det är möjligt.
* Överväg att [lägga till noder](howto-hyperscale-scale-grow.md#add-worker-nodes) i Server gruppen och balansera om Shards. Ombalansering distribuerar data mellan flera datorer.
* Överväg att [växa kapaciteten](howto-hyperscale-scale-grow.md#increase-or-decrease-vcores-on-nodes) för arbetsnoder. Varje arbets tagare kan ha upp till 2 TiB lagrings utrymme. Du bör dock försöka lägga till noder innan du ändrar storlek på noder eftersom det är snabbare att lägga till noder.

### <a name="cpu-usage"></a>CPU-användning

Övervakning av processor användning är användbart för att upprätta en bas linje för prestanda. Du kanske till exempel märker att CPU-användningen vanligt vis är cirka 40-60%. Om CPU-användningen plötsligt börjar Hovra runt 95% kan du identifiera en avvikelse. CPU-användningen kan återspegla ekologisk tillväxt, men kan också visa en lösa fråga. När du skapar en CPU-avisering kan du ange en lång agg regerings kornig het för att fånga långvariga ökningar och ignorera tillfälliga toppar.

## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du [konfigurerar Webhooks i aviseringar](../azure-monitor/alerts/alerts-webhooks.md).
* Få en [Översikt över mått samlingen](../azure-monitor/data-platform.md) för att se till att tjänsten är tillgänglig och svarar.
