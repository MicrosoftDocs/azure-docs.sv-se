---
title: Fråga Azure Data Lake Analytics – Visual Studio
description: Lär dig hur du installerar Data Lake Tools för Visual Studio och hur du utvecklar och testar U-SQL-skript.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: d3812cb27d2d9ea7a49038f566f8b4cc5d779172
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95241700"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Data Lake och Stream Analytics verktyg innehåller funktioner relaterade till två Azure-tjänster, Azure Data Lake Analytics och Azure Stream Analytics. Mer information om Azure Stream Analytics scenarier finns [Azure Stream Analytics verktyg för Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

I den här artikeln beskrivs hur du använder Visual Studio för att skapa Azure Data Lake Analytics-konton. Du kan definiera jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md)och skicka jobb till tjänsten Data Lake Analytics. Mer information om Data Lake Analytics finns i [Azure Data Lake Analytics översikt](data-lake-analytics-overview.md).

> [!IMPORTANT]
> Vi rekommenderar att du uppgraderar till Azure Data Lake verktyg för Visual Studio version 2.3.3000.4 eller senare. Tidigare versioner är inte längre tillgängliga för hämtning och är nu föråldrade.
>
> 1. Kontrollera om du använder en tidigare version än 2.3.3000.4 för Azure Data Lake-verktyg för Visual Studio.
>
>    ![Kontrollera version av verktyget](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. Om din version är en tidigare version av 2.3.3000.4 uppdaterar du ditt Azure Data Lake-verktyg för Visual Studio genom att besöka hämtningscentret:
>    - [För Visual Studio 2017 och 2019](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [För Visual Studio 2013 och 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>Förutsättningar

* **Visual Studio**: Alla utgåvor utom Express stöds.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK för .NET** version 2.7.1 eller senare. Installera den med hjälp av [installationsprogrammet för webbplattformen](https://www.microsoft.com/web/downloads/platform.aspx).
* Ett **Data Lake Analytics**-konto. Information om hur du skapar ett konto finns i [Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Installera Azure Data Lake Tools för Visual Studio

För den här självstudien krävs att Data Lake Tools för Visual Studio har installerats. Mer information finns i [installera Data Lake verktyg för Visual Studio](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Ansluta till ett Azure Data Lake Analytics-konto

1. Öppna Visual Studio.

1. Öppna **data Lake Analytics Explorer** genom att välja **Visa**  >  **data Lake Analytics Explorer**.

1. Högerklicka på **Azure** och välj sedan **Anslut till Microsoft Azure prenumeration**. Följ anvisningarna i **Logga in på ditt konto**.

1. I **Server Explorer** väljer du **Azure**  >  **data Lake Analytics**. En lista över dina Data Lake Analytics-konton visas.

## <a name="write-your-first-u-sql-script"></a>Skriv ditt första U-SQL-skript

Följande text är ett enkelt U-SQL-skript. Skriptet definierar ett liten datauppsättning och skriver den till standard-Data Lake Store som en fil med namnet `/data.csv`.

```sql
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Skicka ett Data Lake Analytics-jobb

1. I Visual Studio väljer du **fil**  >  **nytt**  >  **projekt**.

1. Välj typen **U-SQL-projekt** och välj sedan **Nästa**. I **Konfigurera det nya projektet** väljer du **skapa**.

   Visual Studio skapar en lösning som innehåller en **script. usql** -fil.

1. Klistra in skriptet från [Skriv ditt första U-SQL-skript](#write-your-first-u-sql-script) i fönstret **script. usql** .

1. I **Solution Explorer** högerklickar du på **script. Usql** och väljer **Skicka skript**.

1. I **Skicka jobb** väljer du ditt data Lake Analytics konto och väljer **Skicka**.

   ![Skicka U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

När jobbet har skickats öppnas fliken **Job view** (Jobbvy) där du kan följa jobbförloppet.

* **Jobbsammanfattning** visar en sammanfattning av jobbet.
* **Jobbdiagram** visualiserar jobbförloppet.
* **Metadataåtgärder** visar alla åtgärder som vidtagits för U-SQL-katalogen.
* **Data** visar alla indata och utdata.
* **State History** (Tillståndshistorik) visar tidslinjen och tillståndsinformation.
* I **au-analysen** visas hur många Australien som användes i jobbet och utforska simuleringar av olika strategier för au-allokering.
* **Diagnostik** ger en avancerad analys för jobbkörning och prestandaoptimering.

![Prestandadiagram för U-SQL Visual Studio Data Lake Analytics-jobbet](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

Om du vill se senaste jobb status och uppdatera skärmen väljer du **Uppdatera**.

## <a name="check-job-status"></a>Kontrollera jobbstatus

1. I **Server Explorer** väljer du **Azure**  >  **data Lake Analytics**.

1. Expandera Data Lake Analytics-kontonamnet.

1. Dubbelklicka på **Jobb**.

1. Välj det jobb som du skickade tidigare.

## <a name="see-the-job-output"></a>Visa jobbutdata

1. I **Server Explorer** bläddrar du till jobbet du skickade.

1. Klicka på fliken **Data**.

1. Välj filen `"/data.csv"` på fliken **Job Outputs** (Jobbutdata).

## <a name="next-steps"></a>Nästa steg

* [Kör U-SQL-skript på din egen arbetsstation för testning och felsökning](data-lake-analytics-data-lake-tools-local-run.md)
* [Felsöka C#-kod i U-SQL-jobb med hjälp av Azure Data Lake-verktyg för Visual Studio-kod](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
