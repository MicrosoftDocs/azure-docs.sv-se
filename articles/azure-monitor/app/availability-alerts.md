---
title: Konfigurera tillgänglighets aviseringar med Azure Application Insights | Microsoft Docs
description: Lär dig hur du konfigurerar webbtester i Application Insights. Få aviseringar om en webbplats blir otillgänglig eller svarar långsamt.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: cbafa7997d203cf06a3e91965355258f0088d77e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589882"
---
# <a name="availability-alerts"></a>Tillgänglighetsaviseringar

[Azure Application Insights](./app-insights-overview.md) skickar begäranden till ditt program med jämna mellanrum från platser över hela världen. Det kan varna dig om programmet inte svarar eller om det svarar för långsamt.

## <a name="enable-alerts"></a>Aktivera aviseringar

Aviseringar är nu automatiskt aktiverade som standard, men för att helt kunna konfigurera aviseringen måste du först skapa ditt tillgänglighets test.

![Skapa upplevelse](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Med de [nya enhetliga aviseringarna](../alerts/alerts-overview.md) **måste** aviserings regelns allvarlighets grad och meddelande inställningar med [Åtgärds grupper](../alerts/action-groups.md) konfigureras i aviserings upplevelsen. Utan följande steg får du bara meddelanden i portalen.

1. När du har sparat tillgänglighets testet går du till fliken information och klickar på ellipsen efter det test du nyss gjort. Klicka på "Redigera avisering".

   ![Skärm bild som visar redigerings avisering som valts på menyn.](./media/availability-alerts/edit-alert.png)

2. Ange önskad allvarlighets grad, regel beskrivning och viktigast av de åtgärds grupper som har de aviserings inställningar som du vill använda för den här varnings regeln.

   ![Skärm bild som visar sidan för hantering av regler där du kan redigera regeln.](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> Tillgänglighets aviseringar som skapats i den här miljön är tillstånds baserade. Det innebär att när aviserings villkoret uppfylls genereras en avisering när platsen identifieras som otillgänglig. Om platsen fortfarande visas nästa gången aviserings villkoret utvärderas genererar detta inte en ny avisering. Så om din webbplats har varit nere i en timme och du har angett en e-postavisering, får du bara ett e-postmeddelande när platsen gick ned och en efterföljande e-postadress när platsen säkerhetskopierades. Du får inga varningar om att webbplatsen fortfarande inte är tillgänglig.

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Avisering på X av Y-platser som rapporterar problem

Varnings regeln X out of Y locations är aktive rad som standard i den [nya enhetliga aviserings upplevelsen](../alerts/alerts-overview.md)när du skapar ett nytt tillgänglighets test. Du kan avanmäla genom att välja alternativet "Klassiskt" eller välja att inaktivera varnings regeln.

> [!NOTE]
> Konfigurera de åtgärds grupper som ska få meddelanden när aviseringen utlöses genom att följa stegen ovan. Utan det här steget får du bara meddelanden i portalen när regeln utlöses.
>

### <a name="alert-on-availability-metrics"></a>Avisering om tillgänglighets mått

Med hjälp av de [nya enhetliga aviseringarna](../alerts/alerts-overview.md)kan du även varna för segmenterad mängd tillgänglighet och test varaktighets mått:

1. Välj en Application Insights resurs i mått upplevelsen och välj ett tillgänglighets mått:

    ![Val av tillgänglighets mått](./media/availability-alerts/select-metric.png)

2. Alternativet Konfigurera aviseringar från menyn kommer du till den nya upplevelsen där du kan välja vissa tester eller platser för att ställa in aviserings regeln på. Du kan också konfigurera åtgärds grupper för den här aviserings regeln här.

### <a name="alert-on-custom-analytics-queries"></a>Avisering om anpassade analys frågor

Med hjälp av de [nya enhetliga aviseringarna](../alerts/alerts-overview.md)kan du varna dig om [anpassade logg frågor](../alerts/alerts-unified-log.md). Med anpassade frågor kan du varna dig om godtyckligt villkor som hjälper dig att få den mest pålitliga signalen av tillgänglighets problem. Detta gäller även om du skickar anpassade tillgänglighets resultat med hjälp av TrackAvailability SDK.

> [!Tip]
> Måtten för tillgänglighets data inkluderar eventuella anpassade tillgänglighets resultat som du kan skicka genom att anropa vår TrackAvailability SDK. Du kan använda aviseringar om mått för att få aviseringar om anpassade tillgänglighets resultat.
>

## <a name="automate-alerts"></a>Automatisera aviseringar

Information om hur du automatiserar den här processen med Azure Resource Manager mallar finns i [skapa en Metric-avisering med Resource Manager-mallens](../alerts/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert) dokumentation.

## <a name="troubleshooting"></a>Felsökning

Dedikerad [fel söknings artikel](troubleshoot-availability.md).

## <a name="next-steps"></a>Nästa steg

* [Webbtester med flera steg](availability-multistep.md)
* [Webb test för URL-ping](monitor-web-app-availability.md)

