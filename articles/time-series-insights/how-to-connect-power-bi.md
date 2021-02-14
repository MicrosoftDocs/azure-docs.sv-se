---
title: Anslut din miljö till Power BI-Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du ansluter Azure Time Series Insights till Power BI för att dela, rita och visa data i hela organisationen.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 38403eed56dc718afdfce13375dd2662beb13eb6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374175"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Visualisera data från Azure Time Series Insights i Power BI

Azure Time Series Insights är en plattform för att lagra, hantera, fråga och visualisera Time Series-data i molnet. [Power BI](https://powerbi.microsoft.com) är ett företags analys verktyg med omfattande visualiserings funktioner som gör att du kan dela insikter och resultat i hela organisationen. Båda tjänsterna kan nu integreras så att du kan utöka den kraftfulla analysen av Azure Time Series Insights med stark data visualisering och enkla delnings funktioner i Power BI.

Du lär dig följande:

* Ansluta Azure Time Series Insights till Power BI att använda den interna Azure Time Series Insights anslutningen
* Skapa visuella objekt med dina Time Series-data i Power BI
* Publicera rapporten för att Power BI och dela den med resten av organisationen


## <a name="prerequisites"></a>Förutsättningar

* Registrera dig för en [kostnads fri Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en.
* Hämta och installera den senaste versionen av [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Ha eller skapa en [Azure Time Series Insights Gen2-miljö](./how-to-provision-manage.md)

Granska [miljö åtkomst principer](./concepts-access-policies.md) och se till att du antingen har direkt åtkomst eller gäst åtkomst till Azure Time Series Insights Gen2-miljön. 

> [!IMPORTANT]
> * Hämta och installera den senaste versionen av [Power BI Desktop](https://powerbi.microsoft.com/downloads/). För att följa anvisningarna i den här artikeln måste du kontrol lera att du har minst december 2020-versionen (2.88.321.0) av Power BI Desktop installerad. 

## <a name="export-data-from-azure-time-series-insights-into-power-bi-desktop"></a>Exportera data från Azure Time Series Insights till Power BI Desktop

Så här kommer du igång:

1. Öppna Azure Time Series Insights Gen2 Explorer och granska dina data. Det här är de data som ska exporteras till Power BI.
1. När du har skapat en vy som du är nöjd med, navigerar du till List menyn **fler åtgärder** och väljer **Anslut till Power BI**.

    [![Azure Time Series Insights Gen2 Explorer export](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. Ange parametrar för export:

   * **Data format**: Välj om du vill exportera **sammanställda data** eller **rå händelser** till Power BI. 

       > [!NOTE]
       > Om du exporterar obehandlade händelser kan du samla dessa data senare i Power BI. Men om du exporterar sammanställda data kan du inte återgå till rå data i Power BI. Det finns en gräns för antal 250 000-händelser för rå data på händelse nivå.

   * **Tidsintervall**: Välj om du vill se ett **fast** tidsintervall eller de **senaste** data i Power BI. Om du väljer det fasta tidsintervallet så kommer data i Sök omfånget som du har ritat att exporteras till Power BI. Om du väljer det senaste tidsintervallet innebär det att Power BI hämtar den senaste informationen för Sök omfånget som du har valt (t. ex. om du skapar en data mängd i 1 timme och väljer alternativet "senaste" kommer Power BI Connector alltid att ställa frågor för de senaste 1 timmen med data.)
  
   * **Lagrings typ**: Välj om du vill köra den valda frågan mot **varmt lagrings** utrymme eller **kall lagring**. Om du har valt ett intervall som sträcker sig över både kall och varma butiker dirigeras din fråga till kall lagring som standard eftersom den varma butiken bara kommer att innehålla den senaste informationen. Det går inte att ändra storeType-parametern manuellt, men rekommenderas inte för bästa möjliga upplevelse. 

    > [!TIP] 
    > Azure Time Series Insights Explorer väljer automatiskt de rekommenderade parametrarna beroende på Sök omfång och den vy av data som du har valt att exportera. 

1. När du har konfigurerat inställningarna väljer du **Kopiera fråga till Urklipp**.

    [![Azure Time Series Insights Explorer exportera modal](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

1. Starta Power BI Desktop.
   
1. I Power BI Desktop på fliken **Start** väljer du **Hämta data** i det övre vänstra hörnet och sedan **mer**.

    [![Hämta data i Power BI](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

1. Sök efter **Azure Time Series Insights**, Välj **Azure Time Series Insights (beta)** och **Anslut**.

    [![Anslut Power BI till Azure Time Series Insights](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    Du kan också navigera till fliken **Azure** , välja **Azure Time Series Insights (beta)** och sedan **ansluta**.

1. Klistra in frågan som du kopierade från Azure Time Series Insights Explorer i fältet **anpassad fråga** och tryck sedan på **OK**.

    [![Klistra in i den anpassade frågan och välj OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1.  Data tabellen kommer nu att läsas in. Tryck på **load** för att läsa in i Power BI. Om du vill göra några transformeringar av data kan du göra det nu genom att klicka på **transformera data**. Du kan också transformera dina data efter att de har lästs in.

    [![Granska informationen i tabellen och välj load](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>Skapa en rapport med visuella objekt

Nu när du har importerat data till Power BI är det dags att bygga en rapport med visuella objekt.

1. Kontrol lera att du har valt **rapportvyn** på vänster sida av fönstret.

    [![Skärm bild som visar ikonen rapportvy.](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. Välj önskat visualiserings alternativ i kolumnen **visualiseringar** . Välj till exempel **linje diagram**. Ett tomt linje diagram läggs till på din arbets yta.

1.  I listan **fält** väljer du **_Timestamp** och drar den till fältet **axel** för att visa tiden längs X-axeln. Se till att växla till **_Timestamp** till som värde för **axeln** (Standardvärdet är **date-hierarkin**).

    [![Välj _Timestamp](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  Återigen i listan **fält** väljer du den variabel som du vill rita och drar den till fältet **värden** för att visa värden längs Y-axeln. Välj ditt Time Series ID-värde och dra det till **förklarings** fältet för att skapa flera rader i diagrammet, ett per tids serie-ID. Detta kommer att återge en vy som liknar den som tillhandahålls av Azure Time Series Insights Explorer! 

    [![Skapa ett grundläggande linje diagram](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. Om du vill lägga till ett annat diagram på arbets ytan väljer du var som helst på arbets ytan utanför linje diagrammet och upprepar den här processen.

    [![Skapa ytterligare diagram som ska delas](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

När du har skapat rapporten kan du publicera den till Power BI repor ting Services och dela med andra i din organisation.

## <a name="advanced-editing"></a>Avancerad redigering
Om du redan har läst in en data uppsättning i Power BI men vill ändra frågan (till exempel parametrarna för datum/tid eller miljö-ID), kan du göra detta via Avancerad redigerare-funktionerna i Power BI. Läs Power BI- [dokumentationen](/power-bi/desktop-query-overview) om du vill veta mer om hur du gör ändringar med hjälp av **Power Query redigeraren**. 

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Power BI Desktop](/power-bi/desktop-query-overview).

* Lär dig mer om att [köra frågor mot data](concepts-query-overview.md) i Azure Time Series Insights Gen2.