---
title: 'ML Studio (klassisk): importera/exportera data i webb tjänster – Azure'
description: Lär dig hur du använder modulerna importera data och exportera data för att skicka och ta emot data från en webb tjänst.
services: machine-learning
author: likebupt
ms.author: keli19
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
ms.date: 03/28/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 4824b7a4233bc65d521e1c6ded7d1ea276b2a929
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520569"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Distribuera Azure Machine Learning Studio (klassiska) webb tjänster som använder moduler för data import och data export

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


När du skapar ett förutsägelse experiment lägger du vanligt vis till en webb tjänst indata och utdata. När du distribuerar experimentet kan konsumenter skicka och ta emot data från webb tjänsten via indata och utdata. För vissa program kan en konsument data vara tillgängliga från en datafeed eller finnas redan i en extern data källa, till exempel Azure Blob Storage. I dessa fall behöver de inte läsa och skriva data med hjälp av webb tjänst indata och utdata. De kan i stället använda BES (batch execution service) för att läsa data från data källan med hjälp av en modul för att importera data och skriva poängsättnings resultatet till en annan data plats med hjälp av en export data-modul.

Modulerna importera data och exportera data kan läsa från och skriva till olika data platser, till exempel en webb-URL via HTTP, en Hive-fråga, en databas i Azure SQL Database, Azure Table Storage, Azure Blob Storage, en datafeed eller en SQL Server-databas.

I det här avsnittet används exemplet "exempel 5: träna, test, utvärdera för binär klassificering: vuxen data uppsättning" och förutsätter att data uppsättningen redan har lästs in i en Azure SQL-tabell med namnet censusdata.

## <a name="create-the-training-experiment"></a>Skapa ett övnings experiment
När du öppnar exemplet "exempel 5: träna, test", utvärdera för binär klassificering: vuxen data uppsättning "exempel på det använder sig av den binära data uppsättningen för insamlings inkomst i vuxen räkning. Och experimentet i arbets ytan ser ut ungefär som på följande bild:

![Inledande konfiguration av experimentet.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Så här läser du data från Azure SQL-tabellen:

1. Ta bort data uppsättnings modulen.
2. I sökrutan komponenter skriver du importera.
3. Lägg till en modul för att *Importera data* till arbets ytan för experimentet i resultat listan.
4. Anslut utdata från modulen *Importera data* indata för modulen *Rensa data som saknas* .
5. I fönstret Egenskaper väljer du **Azure SQL Database** i list rutan **data källa** .
6. I fälten **databas server namn**, **databas namn**, **användar namn** och **lösen ord** anger du lämplig information för databasen.
7. Ange följande fråga i fältet databas fråga.

    ```tsql
     select [age],
        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     from dbo.censusdata;
    ```
8. Klicka på **Kör** längst ned i experiment arbets ytan.

## <a name="create-the-predictive-experiment"></a>Skapa förutsägande experiment
Härnäst ställer du in det förutsägelse experiment som du använder för att distribuera webb tjänsten.

1. Klicka på **Konfigurera webb tjänst** i slutet av experiment arbets ytan och välj **förutsägbar webb tjänst [Recommended]**.
2. Ta bort modulerna för *indata för webb tjänsten* och *webb tjänstens utdata* från det förutsägande experimentet.
3. I sökrutan komponenter skriver du exportera.
4. Lägg till en modul för att *Exportera data* till experimentets arbets yta i resultat listan.
5. Anslut utdata från modulen *Poäng modell* indata för modulen *Exportera data* .
6. I fönstret Egenskaper väljer du **Azure SQL Database** i list rutan data mål.
7. I fälten **databas server namn**, **databas namn**, **serverns användar konto namn** och **lösen ord för serverns användar konto** anger du lämplig information för databasen.
8. Skriv betygs etiketter i den **kommaavgränsade listan över kolumner som ska sparas** .
9. I **fältet data tabell namn** skriver du dbo. ScoredLabels. Om tabellen inte finns skapas den när experimentet körs eller så anropas webb tjänsten.
10. I fältet **kommaavgränsad lista över DataTable-kolumner** skriver du ScoredLabels.

När du skriver ett program som anropar den slutliga webb tjänsten kanske du vill ange en annan indatamängds fråga eller mål tabell vid körning. Om du vill konfigurera dessa indata och utdata använder du funktionen för webb tjänst parametrar för att ange *data källans egenskap för data källa* i modulen *Importera data* och egenskapen *Exportera* Dataläge.  Mer information om parametrarna för webb tjänsten finns i [posten Azure Machine Learning Studio Web Service Parameters](/archive/blogs/machinelearning/azureml-web-service-parameters) på Cortana Intelligence och Machine Learning blogg.

Konfigurera webb tjänst parametrarna för import frågan och mål tabellen:

1. I rutan Egenskaper för modulen *Importera data* klickar du på ikonen längst upp till höger i fältet **databas fråga** och väljer **Ange som webb tjänst parameter**.
2. I fönstret Egenskaper för modulen *Exportera data* klickar du på ikonen längst upp till höger i fältet **data tabell namn** och väljer **Ange som webb tjänst parameter**.
3. Längst ned i fönstret Egenskaper för *Exportera* datamodul, i avsnittet **webb tjänst parametrar** , klickar du på databas fråga och byter namn på frågan.
4. Klicka på **data tabell namn** och Byt namn på **tabellen**.

När du är färdig bör experimentet se ut ungefär som på följande bild:

![Slutligt utseende på experimentet.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Nu kan du distribuera experimentet som en webb tjänst.

## <a name="deploy-the-web-service"></a>Distribuera webbtjänsten
Du kan distribuera till antingen en klassisk eller en ny webb tjänst.

### <a name="deploy-a-classic-web-service"></a>Distribuera en klassisk webb tjänst
Distribuera som en klassisk webb tjänst och skapa ett program för att använda det:

1. Klicka på Kör längst ned i experiment arbets ytan.
2. När körningen har slutförts klickar du på **distribuera webb tjänst** och väljer **distribuera webb tjänst [klassisk]**.
3. På instrument panelen för webb tjänster letar du upp din API-nyckel. Kopiera och spara den för att använda senare.
4. I **standard slut punkts** tabellen klickar du på länken för **batch-körning** för att öppna API-hjälp sidan.
5. I Visual Studio skapar du ett C#-konsol program: **nytt**  >  **projekt**  >  **Visual C#**  >  **Windows klassisk Desktop**  >  **console-app (.NET Framework)**.
6. På sidan API-hjälp hittar du avsnittet **exempel kod** längst ned på sidan.
7. Kopiera och klistra in C#-exempel koden i Program.cs-filen och ta bort alla referenser till blob-lagringen.
8. Uppdatera värdet för variabeln *apiKey* med API-nyckeln Sparad tidigare.
9. Leta upp deklarationen för begäran och uppdatera värdena för webb tjänst parametrar som skickas till modulerna *Importera data* och *Exportera data* . I det här fallet använder du den ursprungliga frågan, men definierar ett nytt tabell namn.

    ```csharp
    var request = new BatchExecutionRequest()
    {
        GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
        }
    };
    ```
10. Kör appen.

När körningen har slutförts läggs en ny tabell till i databasen som innehåller resultat resultaten.

### <a name="deploy-a-new-web-service"></a>Distribuera en ny webb tjänst

> [!NOTE]
> Om du vill distribuera en ny webb tjänst måste du ha tillräcklig behörighet i den prenumeration som du distribuerar webb tjänsten till. Mer information finns i [hantera en webb tjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md).

Distribuera som en ny webb tjänst och skapa ett program för att använda den:

1. Klicka på **Kör** längst ned i experiment arbets ytan.
2. När körningen har slutförts klickar du på **distribuera webb tjänst** och väljer **distribuera webb tjänst [ny]**.
3. På sidan distribuera experiment anger du ett namn för din webb tjänst och väljer en pris plan och klickar sedan på **distribuera**.
4. Klicka på **förbruka** på sidan **snabb start** .
5. I avsnittet **exempel kod** klickar du på **batch**.
6. I Visual Studio skapar du ett C#-konsol program: **nytt**  >  **projekt**  >  **Visual C#**  >  **Windows klassisk Desktop**  >  **console-app (.NET Framework)**.
7. Kopiera och klistra in C#-exempel koden i din Program.cs-fil.
8. Uppdatera värdet för variabeln *apiKey* med **primär nyckeln** som finns i avsnittet **grundläggande förbruknings information** .
9. Leta upp *ScoreRequest* -deklarationen och uppdatera värdena för webb tjänst parametrar som skickas till modulerna *Importera data* och *Exportera data* . I det här fallet använder du den ursprungliga frågan, men definierar ett nytt tabell namn.

    ```csharp
    var scoreRequest = new
    {
        Inputs = new Dictionary<string, StringTable>()
        {
        },
        GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable3" },
        }
    };
    ```
10. Kör appen.