---
title: Installera Azure Data Lake Tools för Visual Studio
description: Den här artikeln beskriver hur du installerar Azure Data Lake verktyg för Visual Studio.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: cb0183ae229c328588a31c2c0549e7e93fd19b78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019012"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Installera Data Lake-verktyg för Visual Studio

Lär dig hur du använder Visual Studio för att skapa Azure Data Lake Analytics-konton. Du kan definiera jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md) och skicka jobb till tjänsten Data Lake Analytics. Mer information om Data Lake Analytics finns i [Azure Data Lake Analytics översikt](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Förutsättningar

* **Visual Studio**: Alla utgåvor utom Express stöds.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK för .NET** version 2.7.1 eller senare. Installera den med hjälp av [installationsprogrammet för webbplattformen](https://www.microsoft.com/web/downloads/platform.aspx).
* Ett **Data Lake Analytics**-konto. Information om hur du skapar ett konto finns i [Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Installera Azure Data Lake verktyg för Visual Studio 2017 eller Visual Studio 2019

Azure Data Lake verktyg för Visual Studio stöds i Visual Studio 2017 15,3 eller senare. Verktyget är en del av arbets belastningarna **data lagring och bearbetning** och **Azure-utveckling** . Aktivera någon av dessa två arbetsbelastningar som en del av Visual Studio-installationen.

Aktivera arbets belastningen **data lagring och bearbetning** som det visas:

![Aktivera arbets belastningen lagring och bearbetning av data](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Aktivera arbets belastningen **Azure Development** enligt följande:

![Välj arbets belastning för Azure-utveckling](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Installera Azure Data Lake Tools för Visual Studio 2013 och 2015

Hämta och installera [Microsoft Azure Data Lake-och Stream Analytics-verktyg för Visual Studio ](https://aka.ms/adltoolsvs). Efter installationen har Visual Studio följande ändringar:

* **Server Explorer**  >  **Azure** -noden innehåller en **data Lake Analytics** -nod.
* Alternativet **Data Lake** finns på **Verktyg**-menyn.

## <a name="next-steps"></a>Nästa steg

* Information om hur du loggar diagnostikinformation finns i [komma åt diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Om du vill använda körnings vyn för hörn visas [i Använd vyn hörn körning i data Lake verktyg för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
