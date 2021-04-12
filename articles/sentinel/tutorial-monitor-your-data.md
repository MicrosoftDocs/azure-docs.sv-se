---
title: Visualisera dina data med Azure Monitor arbets böcker i Azure Sentinel | Microsoft Docs
description: I den här självstudien får du lära dig hur du visualiserar dina data med hjälp av arbets böcker i Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2021
ms.author: yelevin
ms.openlocfilehash: c26d86c98c83d9762acb8a75bba8fe464cc2a58e
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491523"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Självstudie: Visualisera och övervaka dina data

När du har [anslutit dina data källor](quickstart-onboard.md) till Azure Sentinel kan du visualisera och övervaka data med hjälp av Azure Sentinel-införandet av Azure Monitor arbets böcker, vilket ger mångsidighet i att skapa anpassade instrument paneler. Även om arbets böckerna visas på olika sätt i Azure Sentinel, kan det vara praktiskt att se hur du [skapar interaktiva rapporter med Azure Monitor arbets böcker](../azure-monitor/visualize/workbooks-overview.md). Med Azure Sentinel kan du skapa anpassade arbets böcker i dina data och även med inbyggda mallar för arbets böcker så att du snabbt kan få insikter om dina data så snart du ansluter en data källa.

Den här självstudien hjälper dig att visualisera dina data i Azure Sentinel.
> [!div class="checklist"]
> * Använda inbyggda arbets böcker
> * Skapa nya arbets böcker

## <a name="prerequisites"></a>Förutsättningar

Du måste ha minst behörigheter för **arbets boks läsare** eller **arbets bok deltagare** för resurs gruppen i Azure Sentinel-arbetsytan.

> [!NOTE]
> Arbets böckerna som du kan se i Azure Sentinel sparas i resurs gruppen i Azure Sentinel-arbetsytan och märks av arbets ytan där de skapades.

## <a name="use-built-in-workbooks"></a>Använda inbyggda arbets böcker

1. Gå till **arbets böcker** och välj sedan **mallar** för att se en fullständig lista över inbyggda Azure Sentinel-arbetsböcker. 

    För att se vilka som är relevanta för de data typer som du har anslutit, visar fältet **obligatoriska data typer** i varje arbets bok data typen bredvid en grön bock markering om du redan strömmar relevanta data till Azure Sentinel.

    [![Gå till arbets böcker. ](media/tutorial-monitor-data/access-workbooks.png)](media/tutorial-monitor-data/access-workbooks.png#lightbox)

1. Välj **Visa mall** för att se mallen som är ifylld med dina data.

1. Om du vill redigera arbets boken väljer du **Spara** och väljer sedan den plats där du vill spara JSON-filen för mallen.

   > [!NOTE]
   > Detta skapar en Azure-resurs som baseras på den relevanta mallen och sparar JSON-filen för arbets boken och inte data.


1. Välj **Visa Sparad arbets bok**. 

    [![Visa arbets böcker. ](media/tutorial-monitor-data/workbook-graph.png)](media/tutorial-monitor-data/workbook-graph.png#lightbox)

    Välj knappen **Redigera** i arbets bokens verktygsfält för att anpassa arbets boken efter dina behov. När du är klar väljer du **Spara** för att spara ändringarna.

    Mer information finns i så här [skapar du interaktiva rapporter med Azure Monitor arbets böcker](../azure-monitor/visualize/workbooks-overview.md).

> [!TIP]
> Om du vill klona arbets boken väljer du **Redigera** och sedan **Spara som**, så att du sparar den med ett annat namn, under samma prenumeration och resurs grupp.
> Klonade arbets böcker visas under fliken **Mina arbets böcker** .
>
## <a name="create-new-workbook"></a>Skapa ny arbets bok

1. Gå till **arbets böcker** och välj sedan **Lägg till arbets bok** för att skapa en ny arbets bok från grunden.

    [![Ny arbets bok. ](media/tutorial-monitor-data/create-workbook.png)](media/tutorial-monitor-data/create-workbook.png#lightbox)

1. Om du vill redigera arbets boken väljer du **Redigera** och lägger sedan till text, frågor och parametrar vid behov. Mer information om hur du anpassar arbets boken finns i så här [skapar du interaktiva rapporter med Azure Monitor arbets böcker](../azure-monitor/visualize/workbooks-overview.md). 

1. När du skapar en fråga kontrollerar du att **data källan** har angetts till **loggar** och att **resurs typen** har angetts till **Log Analytics** och väljer sedan de relevanta arbets ytorna. 

1. När du har skapat din arbets bok sparar du arbets boken och kontrollerar att du sparar den under prenumerationen och resurs gruppen för din Azure Sentinel-arbetsyta.

1. Om du vill låta andra i organisationen använda arbets boken går du till Välj **delade rapporter** under **Spara** . Om du vill att den här arbets boken bara ska vara tillgänglig för dig väljer du **Mina rapporter**.

1. Om du vill växla mellan arbets böcker i din arbets yta väljer du **Öppna** ![ ikon för att öppna en arbets bok.](./media/tutorial-monitor-data/switch.png) i verktygsfältet för en arbets bok. Skärmen växlar till en lista över andra arbets böcker som du kan växla till.

    Välj den arbets bok som du vill öppna:

    [![Växla arbets böcker. ](media/tutorial-monitor-data/switch-workbooks.png)](media/tutorial-monitor-data/switch-workbooks.png#lightbox)

## <a name="refresh-your-workbook-data"></a>Uppdatera dina arbets boks data

Uppdatera din arbets bok för att Visa uppdaterade data. Välj något av följande alternativ i verktygsfältet:

- :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false":::**Uppdatera** för att uppdatera dina arbets boks data manuellt.

- :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false":::**Automatisk uppdatering** för att ange att arbets boken ska uppdateras automatiskt vid ett konfigurerat intervall.

    - Intervall för automatisk uppdatering som stöds är mellan **5** och **1 dag**.

    - Automatisk uppdatering pausas när du redigerar en arbets bok, och intervall startas om varje gången du växlar tillbaka till visnings läge från redigerings läge.

    - Intervall för automatisk uppdatering startas också om du uppdaterar dina data manuellt.

    > [!TIP]
    > Automatisk uppdatering är inaktive rad som standard. För att optimera prestanda stängs den automatiska uppdateringen också av varje gången du stänger en arbets bok och körs inte i bakgrunden. Aktivera automatisk uppdatering igen vid behov nästa gången du öppnar arbets boken.
    >

## <a name="print-a-workbook-or-save-as-pdf"></a>Skriva ut en arbets bok eller Spara som PDF

Om du vill skriva ut en arbets bok eller spara den som en PDF, använder du menyn alternativ till höger om arbets bokens rubrik.

1. Välj alternativ för > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Skriv ut innehåll**. 
2. I utskrifts skärmen justerar du utskrifts inställningarna efter behov eller väljer **Spara som PDF** för att spara den lokalt.

Exempel:

[![Skriv ut din arbets bok eller Spara som PDF. ](media/whats-new/print-workbook.png)](media/whats-new/print-workbook.png#lightbox)

## <a name="how-to-delete-workbooks"></a>Ta bort arbetsböcker

Om du vill ta bort en sparad arbets bok (antingen en sparad mall eller en anpassad arbets bok) går du till sidan arbets böcker och väljer den sparade arbets bok som du vill ta bort och väljer **ta bort**. Den sparade arbets boken tas bort.

> [!NOTE]
> Detta tar bort arbets boks resursen och eventuella ändringar som du har gjort i mallen. Den ursprungliga mallen är fortfarande tillgänglig.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du visualiserar dina data i Azure Sentinel med Azure-arbetsböcker.

Information om hur du automatiserar dina svar på hot finns i [Konfigurera automatiserade hot svar i Azure Sentinel](tutorial-respond-threats-playbook.md).
