---
title: Analysera data i Azure Data Lake Storage Gen1-Power BI
description: Lär dig hur du använder Power BI Desktop för att analysera och visualisera data som lagras i Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bcc561cd5eea4372d798fff4580362ba0879c3a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91574202"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Analysera data i Azure Data Lake Storage Gen1 med Power BI
I den här artikeln får du lära dig hur du använder Power BI Desktop för att analysera och visualisera data som lagras i Azure Data Lake Storage Gen1.

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett data Lake Storage gen1 konto**. Följ anvisningarna i [Kom igång med Azure Data Lake Storage gen1 med hjälp av Azure Portal](data-lake-store-get-started-portal.md). Den här artikeln förutsätter att du redan har skapat ett Data Lake Storage Gen1-konto, kallat **myadlsg1**, och laddat upp en exempel data fil (**Drivers.txt**) till den. Den här exempel filen kan laddas ned från [Azure Data Lake git-lagringsplatsen](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Du kan ladda ned det från [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Skapa en rapport i Power BI Desktop
1. Starta Power BI Desktop på datorn.
2. I menyfliksområdet **Start** klickar du på **Hämta data** och sedan på mer. I dialog rutan **Hämta data** klickar du på **Azure**, klickar på **Azure Data Lake Store** och sedan på **Anslut**.
   
    ![Skärm bild av dialog rutan hämta data med alternativet Azure Data Lake Store markerat och anslutnings alternativet inringat.](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Anslut till Data Lake Storage Gen1")
3. Om du ser en dialog ruta om anslutningen är i utvecklings fasen väljer du att fortsätta.
4. Ange URL: en till ditt Data Lake Storage Gen1-konto i dialog rutan **Azure Data Lake Store** och klicka sedan på **OK**.
   
    ![URL för Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL för Data Lake Storage Gen1")
5. I nästa dialog ruta klickar du på **Logga** in för att logga in på data Lake Storage gen1-kontot. Du kommer att omdirigeras till din organisations inloggnings sida. Följ anvisningarna för att logga in på kontot.
   
    ![Logga in på Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Logga in på Data Lake Storage Gen1")
6. När du har loggat in klickar du på **Anslut**.
   
    ![Skärm bild av dialog rutan Azure Data Lake Store med alternativet Connect som kallas för.](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Anslut till Data Lake Storage Gen1")
7. I nästa dialog ruta visas filen som du laddade upp till ditt Data Lake Storage Gen1-konto. Verifiera informationen och klicka sedan på **Läs in**.
   
    ![Läs in data från Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Läs in data från Data Lake Storage Gen1")
8. När data har lästs in i Power BI visas följande fält på fliken **fält** .
   
    ![Importerade fält](./media/data-lake-store-power-bi/imported-fields.png "Importerade fält")
   
    Men för att visualisera och analysera data, föredrar vi att data är tillgängliga enligt följande fält
   
    ![Önskade fält](./media/data-lake-store-power-bi/desired-fields.png "Önskade fält")
   
    I nästa steg ska vi uppdatera frågan för att konvertera importerade data i det önskade formatet.
9. I menyfliksområdet **Start** klickar du på **Redigera frågor**.
   
    ![Skärm bild av start-menyfliksområdet med alternativet Redigera frågor som kallas för.](./media/data-lake-store-power-bi/edit-queries.png "Redigera frågor")
10. I Frågeredigeraren, under kolumnen **innehåll** , klickar du på **binär**.
    
    ![Skärm bild av Frågeredigeraren med kolumnen innehåll som heter ut.](./media/data-lake-store-power-bi/convert-query1.png "Redigera frågor")
11. En fil ikon visas som representerar den **Drivers.txt** -fil som du överförde. Högerklicka på filen och klicka på **CSV**.    
    
    ![Skärm bild av Frågeredigeraren med alternativet CSV inringat.](./media/data-lake-store-power-bi/convert-query2.png "Redigera frågor")
12. Du bör se utdata som visas nedan. Dina data är nu tillgängliga i ett format som du kan använda för att skapa visualiseringar.
    
    ![Skärm bild av Frågeredigeraren med utdata som visas som förväntat.](./media/data-lake-store-power-bi/convert-query3.png "Redigera frågor")
13. Från menyfliksområdet **Start** klickar du på **Stäng och Verkställ** och sedan på **Stäng och Använd**.
    
    ![Skärm bild av start-menyfliksområdet med alternativet Stäng och Använd som kallas för.](./media/data-lake-store-power-bi/load-edited-query.png "Redigera frågor")
14. När frågan har uppdaterats visas de nya fält som är tillgängliga för visualisering på fliken **fält** .
    
    ![Uppdaterade fält](./media/data-lake-store-power-bi/updated-query-fields.png "Uppdaterade fält")
15. Låt oss skapa ett cirkel diagram som representerar driv rutinerna i varje stad för ett specifikt land/region. Det gör du genom att göra följande val.
    
    1. På fliken visualiseringar klickar du på symbolen för ett cirkel diagram.
       
        ![Skapa cirkel diagram](./media/data-lake-store-power-bi/create-pie-chart.png "Skapa cirkel diagram")
    2. Kolumnerna som vi ska använda är **kolumn 4** (namnet på staden) och **kolumn 7** (namnet på landet/regionen). Dra de här kolumnerna från fliken **fält** till fliken **visualiseringar** som visas nedan.
       
        ![Skapa visualiseringar](./media/data-lake-store-power-bi/create-visualizations.png "Skapa visualiseringar")
    3. Cirkel diagrammet bör nu likna det som visas nedan.
       
        ![Cirkel diagram](./media/data-lake-store-power-bi/pie-chart.png "Skapa visualiseringar")
16. Genom att välja ett land/en viss region från sid nivå filtren kan du nu se antalet driv rutiner i varje stad för det valda landet/den valda regionen. Välj exempelvis **Brasilien** under fliken **visualiseringar** under **sid nivå filter**.
    
    ![Välj ett land eller en region](./media/data-lake-store-power-bi/select-country.png "Välj ett land eller en region")
17. Cirkel diagrammet uppdateras automatiskt så att det visar driv rutinerna i städerna för Brasilien.
    
    ![Driv rutiner i ett land/region](./media/data-lake-store-power-bi/driver-per-country.png "Driv rutiner per land/region")
18. I menyn **Arkiv** klickar du på **Spara** för att spara visualiseringen som en Power BI Desktop-fil.

## <a name="publish-report-to-power-bi-service"></a>Publicera rapporten till Power BI-tjänst
När du har skapat visualiseringarna i Power BI Desktop kan du dela den med andra genom att publicera den på Power BI-tjänst. Instruktioner för hur du gör finns i [publicera från Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Se även
* [Analysera data i Data Lake Storage Gen1 med Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

