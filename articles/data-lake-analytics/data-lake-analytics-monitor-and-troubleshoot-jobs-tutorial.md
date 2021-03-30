---
title: Övervaka Azure Data Lake Analytics – Azure Portal
description: Den här artikeln beskriver hur du använder Azure Portal för att felsöka Azure Data Lake Analytics-jobb.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 12/05/2016
ms.openlocfilehash: ddcf41a333d261bd9c5f669fde724a25eeba670e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220286"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Övervaka jobb i Azure Data Lake Analytics med Azure-portalen

## <a name="to-see-all-the-jobs"></a>Visa alla jobb

1. Klicka på **Microsoft Azure** i det övre vänstra hörnet i Azure Portal.

2. Klicka på ikonen med Data Lake Analytics-kontonamnet.  Jobb sammanfattningen visas på panelen **jobb hantering** .

   ![Azure Data Lake Analytics jobb hantering](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Jobb hanteringen ger dig en överblick över jobb statusen. Observera att det finns ett misslyckat jobb.
3. Klicka på panelen **jobb hantering** om du vill se jobben. Jobben kategoriseras i **Kör**, **köade** och **avslutade**. Du får se det misslyckade jobbet i avsnittet **Avsluta** . Den måste först vara en i listan. När du har många jobb kan du klicka på **filter** för att få hjälp att hitta jobb.

   ![Azure Data Lake Analytics filtrera jobb](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. Klicka på det misslyckade jobbet i listan för att öppna jobb informationen:

   ![Azure Data Lake Analytics misslyckade jobbet](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Lägg märke till knappen **Skicka igen** . När du har åtgärdat problemet kan du skicka jobbet igen.

5. Öppna fel informationen genom att klicka på den markerade delen från föregående skärm bild.  Du ser något som liknar:

   ![Azure Data Lake Analytics information om misslyckade jobb](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

   Det visar att källmappen inte kan hittas.

6. Klicka på **duplicera skript**.

7. Uppdatera **från** sökväg till: `/Samples/Data/SearchLog.tsv`

8. Klicka på **Skicka jobb**.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Hantera Azure Data Lake Analytics med Azure Portal](data-lake-analytics-manage-use-portal.md)
