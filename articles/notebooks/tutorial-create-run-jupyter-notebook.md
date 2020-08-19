---
title: Självstudie – Skapa och köra en Jupyter Notebook-Azure Notebooks för hands version
description: Lär dig hur du skapar och kör en Jupyter Notebook i Azure Notebooks för hands versionen som visar processen för linjär regression i data vetenskap.
ms.topic: tutorial
ms.date: 01/11/2019
ms.custom: devx-track-python
ms.openlocfilehash: cb8be1618de7e95a7096e4ea9dec78bf4aa277f6
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589287"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Självstudie: skapa och köra en Jupyter Notebook med python

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Den här självstudien vägleder dig genom processen att använda Azure Notebooks för att skapa en fullständig Jupyter Notebook som visar enkel linjär regression. I den här självstudien får du bekanta dig med Jupyter Notebook gränssnittet, vilket innefattar att skapa olika celler, köra celler och presentera antecknings boken som ett bild spel.

Den slutförda antecknings boken finns på [GitHub Azure Notebooks-exempel](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Den här själv studie kursen börjar dock med ett nytt projekt och en tom antecknings bok så att du kan skapa det steg för steg.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en projekt antecknings bok med exempel data
> * Använd Notebook-gränssnittet för att skapa en mängd olika typer av celler
> * Köra anteckningsboken
> * Spara antecknings boken
> * Felsöka antecknings boken i Visual Studio Code

## <a name="create-the-project"></a>Skapa projektet

1. Gå till [Azure Notebooks](https://notebooks.azure.com) och logga in. (Mer information finns i [snabb start – logga in till Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Från din offentliga profil sida väljer du **Mina projekt** överst på sidan:

    ![Länken Mina projekt överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På sidan **Mina projekt** väljer du **+ nytt projekt** (kortkommando: n); knappen får bara visas som **+** om webbläsarfönstret är smalare:

    ![Kommandot nytt projekt på sidan mina projekt](media/quickstarts/new-project-command.png)

1. I popup-fönstret **Skapa nytt projekt** som visas anger eller anger du följande information och väljer sedan **skapa**:

   - **Projekt namn**: linjär regression exempel – cricket Chirps
   - **Projekt-ID**: linjär-regression-exempel
   - **Offentligt projekt**: (avmarkerat)
   - **Skapa en README.MD**: (avmarkerad)

1. Efter en liten stund navigerar Azure Notebooks till det nya projektet.

## <a name="create-the-data-file"></a>Skapa data filen

Den linjära Regressions modell som du skapar i antecknings boken hämtar data från en fil i projektet med namnet *cricket_chirps.csv*. Du kan skapa den här filen antingen genom att kopiera den från [GitHub Azure Notebooks-exempel](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)eller genom att ange data direkt. I följande avsnitt beskrivs båda metoderna.

### <a name="upload-the-data-file"></a>Ladda upp data filen

1. På instrument panelen för projektet i Azure Notebooks väljer du **Ladda upp**  >  **från URL**
1. I popup-fönstret anger du följande URL i **fil-URL** och *cricket_chirps.csv* i **fil namn**och väljer sedan **färdig**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Den *cricket_chirps.csv* filen ska nu visas i projektets fillista:

    ![Nyligen skapad CSV-fil som visas i projekt fil listan](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Skapa en fil från grunden

1. På instrument panelen för projektet i Azure Notebooks väljer du **+ ny**  >  **tom fil**
1. Ett fält visas i projektets fil lista. Ange *cricket_chirps.csv* och tryck på RETUR.
1. Högerklicka på *cricket_chirps.csv* och välj **Redigera fil**.
1. I redigeraren som visas anger du följande data:

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. Välj **Spara fil** för att spara filen och återgå till instrument panelen för projektet.

## <a name="install-project-level-packages"></a>Installera paket för projekt nivå

I en bärbar dator kan du alltid använda kommandon som `!pip install` i en kod cell för att installera nödvändiga paket. Sådana kommandon körs dock varje gång du kör antecknings bokens kod celler och kan ta lång tid. Därför kan du i stället installera paket på projekt nivå med en- `requirements.txt` fil.

1. Använd processen som beskrivs i [skapa en fil från grunden](#create-a-file-from-scratch) för att skapa en fil med namnet `requirements.txt` med följande innehåll:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Du kan också ladda upp en `requirements.txt` fil från den lokala datorn om du vill, enligt beskrivningen i [Ladda upp data filen](#upload-the-data-file).

1. På instrument panelen för projektet väljer du **projekt inställningar**.
1. I popup-fönstret som visas väljer du fliken **miljö** och väljer sedan **+ Lägg till**.
1. I den första List rutan (åtgärden) under **miljö konfigurations steg**väljer du **Requirements.txt**.
1. I den andra List rutan (fil namnet) väljer du *requirements.txt* (filen som du skapade).
1. I den tredje List rutan (python-versionen) väljer du **python version 3,6**.
1. Välj **Spara**.

![Fliken projekt inställningar miljö som anger en requirements.txt-fil](media/tutorial/tutorial-requirements-txt.png)

Med det här installations steget på plats kommer alla antecknings böcker som du kör i projektet att köras i en miljö där dessa paket är installerade.

## <a name="create-and-run-a-notebook"></a>Skapa och kör en notebook-fil

Med data filen Ready och projekt miljö uppsättningen kan du nu skapa och öppna antecknings boken.

1. På instrument panelen för projektet väljer du **+ ny**  >  **antecknings bok**.
1. I popup-fönstret anger du *linjär regression exempel – cricket Chirps. ipynb* för **objekt namn**, väljer du **python 3,6** för språket och väljer sedan **nytt**.
1. När den nya antecknings boken visas i fil listan väljer du den för att starta antecknings boken. En ny flik i webbläsaren öppnas automatiskt.
1. Eftersom du har en *requirements.txt* -fil i miljö inställningarna visas meddelandet "väntar på att din behållare ska slutföras". Du kan välja **OK** för att stänga meddelandet och fortsätta arbeta i antecknings boken. Du kan dock inte köra kod celler förrän miljön har kon figurer ATS fullständigt.
1. Antecknings boken öppnas i Jupyter-gränssnittet med en enda tom kod cell som standard.

    [![Inledande vy av en ny antecknings bok i Azure Notebooks](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Upptäck Notebook-gränssnittet

Med den bärbara datorn som kör kan du lägga till kod-och markdown-celler, köra cellerna och hantera antecknings bokens funktion. Det är dock värt att ta några minuter att bekanta dig med gränssnittet. Om du vill ha fullständig dokumentation väljer du **Hjälp**  >  meny kommandot hjälp för**Notebook** .

Längst upp i fönstret ser du följande objekt:

(A) namnet på din bärbara dator, som du kan redigera genom att klicka på.
(B) knappar för att navigera till instrument panelen med projekt och projekt, som öppnar nya flikar i webbläsaren.
(C) en meny med kommandon för att arbeta med antecknings boken.
(D) ett verktygsfält med kortkommandon för vanliga åtgärder.
(E) den redigerings arbets yta som innehåller celler.
(F) indikator för om antecknings boken är betrodd (standard är **inte betrodd**).
(G) den kernel som används för att köra antecknings boken tillsammans med en aktivitets indikator.

[![Primära UI-områden i Jupyter-gränssnittet](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter innehåller en inbyggd rundtur i de primära GRÄNSSNITTs elementen. Starta visningen genom att välja kommandot **för**  >  **visning av användar gränssnitt** och klicka dig igenom popup-fönster.

Grupper med Meny kommandon är följande:

| Meny | Beskrivning |
| --- | --- |
| Fil | Kommandon för att hantera Notebook-filen, inklusive kommandon för att skapa och kopiera antecknings böcker, Visa en förhands granskning och ladda ned antecknings boken i olika format. |
| Redigera | Vanliga kommandon för att klippa ut, kopiera och klistra in celler, söka efter och ersätta värden, hantera cell bilagor och infoga bilder.  |
| Vy | Kommandon för att kontrol lera visningen av olika delar av Jupyter-ANVÄNDARGRÄNSSNITTET. |
| Infogning | Kommandon för att infoga en ny cell ovanför eller under den aktuella cellen. Du använder dessa kommandon ofta när du skapar en antecknings bok. |
| Mobiltelefon | De olika **körnings** kommandona kör en eller flera celler i olika kombinationer. Kommandona **cell typ** ändrar typ av cell mellan **kod**, **markdown**och **rå NBConvert** (oformaterad text). Kommandona **aktuella utdata** och **alla utdata** styr hur utdata från körnings kod visas och innehåller ett kommando för att rensa alla utdata. |
| Kernel | Kommandon för att hantera hur kod körs i kerneln, tillsammans med **ändrings kärnan** för att ändra språket eller python-versionen som används för att köra antecknings boken. |
| Data | Kommandon för att ladda upp och ladda ned filer från projektet eller sessionen. Se [arbeta med Project-datafiler](work-with-project-data-files.md) |
| Widgets | Kommandon för att hantera [Jupyter-widgetar](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), som ger ytterligare funktioner för visualisering, mappning och ritning.|
| Hjälp | Kommandon som innehåller hjälp och dokumentation för Jupyter-gränssnittet. |

De flesta kommandon i verktygsfältet har motsvarande meny kommandon. Ett undantag är att **ange/redigera öknings bild spel**, som beskrivs i [dela och presentera antecknings böcker](present-jupyter-notebooks-slideshow.md).

Du använder ett antal kommandon när du fyller i antecknings boken i de avsnitt som följer.

## <a name="create-a-markdown-cell"></a>Skapa en markdown-cell

1. Klicka i den första tomma cellen som visas på Notebook-arbetsytan. Som standard är en-cell en **kod** typ, vilket innebär att den är utformad för att innehålla körbara-kod för den valda kerneln (python, R eller F #). Den aktuella typen visas i list rutan typ i verktygsfältet:

    ![List rutan cell typs verktygsfält](media/tutorial/tutorial-cell-type-drop-down.png)

1. Ändra cell typen till **markdown** med hjälp av verktygsfältet listruta; Alternativt använder du Meny kommandot **cell**  >  **typ**för cell  >  **markdown** :

    ![Meny kommandot cell typ](media/tutorial/tutorial-cell-type-menu.png)

1. Klicka i cellen för att börja redigera och ange sedan följande markdown:

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Om du vill rendera markdown i HTML för webbläsaren väljer du kommandot **Kör** i verktygsfältet eller använder kommandot **cell**  >  **Kör celler** . Markdown-koden för formatering och länkar visas nu som du förväntar dig i en webbläsare.

1. När du kör den sista cellen i antecknings boken skapar Jupyter automatiskt en ny cell under den som du körde. Lägg fler markdown i den här cellen genom att upprepa stegen i det här avsnittet med följande markdown:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Om du vill redigera markdown igen dubbelklickar du på den återgivna cellen. Kör cellen om du vill rendera HTML igen när du har gjort ändringar.

## <a name="create-a-code-cell-with-commands"></a>Skapa en Code-cell med kommandon

Som tidigare markdown cell förklaras kan du inkludera kommandon direkt i antecknings boken. Du kan använda kommandon för att installera paket, köra sväng-eller wget för att hämta data eller något annat. Jupyter-anteckningsböcker körs effektivt inom en virtuell Linux-dator, så att du har det fullständiga Linux-kommandot inställt på att fungera med.

1. Ange kommandona nedan i den kod cell som visades efter att du använt **körning** i föregående markdown-cell. Om du inte ser en ny cell, skapar du en med **Infoga**  >  **cell under** eller använder **+** knappen i verktygsfältet.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Innan du kör cellen skapar du en ny cell med **+** knappen i verktygsfältet, ställer in den på markdown och anger följande förklaring:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Välj **cell**  >  **Kör alla** -kommando för att köra alla celler i antecknings boken. Observera att markdown-cellerna återges som HTML och kommandot körs i kärnan och observera kernel-indikatorn enligt beskrivningen i själva markdown:

    ![Upptagen indikator för notebook-kärnan](media/tutorial/tutorial-kernel-busy.png)

1. Det tar också lite tid för alla `pip install` kommandon att köra, och eftersom du redan har installerat paketen i projekt miljön (och eftersom de också ingår i Azure Notebooks som standard) visas många meddelanden som läser "krav redan uppfyllt". Alla utdata kan subtraheras visuellt, så Markera cellen (med ett enda klick) och sedan använda **cell**  >  **utmatningen**  >  **Växla** för att dölja utdata. Du kan också ta bort utdata helt genom att använda kommandot **Rensa** på samma undermeny.

    **Växlings** kommandot döljer bara de senaste utdata från cellen. Om du kör cellen igen visas utdata igen.

1. Eftersom paketen installeras i projekt miljön, kommenterar du ut `! pip install` kommandona med hjälp av. på så `#` sätt kan de finnas kvar i antecknings boken som instruktions material, men det tar inte längre tid att köra och genererar inte onödiga utdata. I det här fallet anger de kommenterade kommandona i antecknings boken även antecknings bokens beroenden.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Skapa återstående celler

För att fylla resten av antecknings boken kan du sedan skapa en serie med markdown-och kod celler. För varje cell i listan nedan skapar du först den nya cellen och anger sedan typen och klistrar sedan in innehållet.

Även om du kan vänta med att köra antecknings boken när du har skapat varje cell, är det intressant att köra varje cell när du skapar den. Alla celler visar inte utdata. Om du inte ser några fel antar du att cellen kördes normalt.

Varje kod cell är beroende av den kod som har körts i föregående celler, och om du inte kan köra någon av cellerna kan senare celler generera fel. Om du upptäcker att du har glömt att köra en cell kan du prova att använda **cellen**som  >  **körs ovan** innan du kör den aktuella cellen.

Om du ser oväntade resultat (som du förmodligen kommer!) kontrollerar du att varje cell är inställd på "kod" eller "markdown" vid behov. Till exempel uppstår fel meddelandet "Ogiltig syntax" när du har angett markdown i kod cellen.

1. Markdown cell:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Kod cell; vid körning visas tabell innehållet som utdata. Du kan ignorera utdata genom att kommentera ut `print` instruktionen.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Du kan se varningar från den här koden om "numpy. dtype ändrades"; varningarna kan ignoreras på ett säkert sätt.

1. Markdown cell:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Kod cell; När det körs har den här cellen inga utdata.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Markdown cell:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Kod cell; När det körs visar den här cellen utdata `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)` .

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markdown cell:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Kod cell; När det körs visar den här cellen resultat som `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]` .

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Markdown cell:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Markdown cell:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Kod cell; När du kör den här cellen skapas ett grafiskt område. Om du inte ser ritningen första gången (och se i stället "figur storlek 640x480 med 1 axlar"), kör cellen igen.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![Rita utdata från matplotlib-koden](media/tutorial/tutorial-plot-output.png)

1. Markdown cell:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Rensa utdata och kör om alla celler

När du har använt stegen i föregående avsnitt för att fylla i hela antecknings boken har du skapat både en kod som körs i kontexten för en fullständig själv studie kurs om linjär regression. Den här direkta kombinationen av kod och text är en av de fantastiska fördelarna med antecknings böcker!

Prova att köra hela antecknings boken nu:

1. Rensa alla data för kernelns session och alla cell utdata genom att välja **kernel**  >  **restart & rensa utdata**. Det här kommandot är alltid en bra att köra när du har slutfört en antecknings bok, bara för att se till att du inte har skapat några onormala beroenden mellan kod cellerna.

1. Kör den bärbara datorn igen med **cell**  >  **körningen alla**. Observera att kernel-indikatorn är ifylld medan kod körs.

    Om du har en kod som körs för länge eller på annat sätt fastnar kan du stoppa kerneln med kommandot **kernel**-  >  **avbrott** .

1. Bläddra igenom antecknings boken för att granska resultaten. (Om diagrammet inte visas igen, kör du om cellen.)

## <a name="save-halt-and-close-the-notebook"></a>Spara, stoppa och Stäng antecknings boken

Under den tid som du redigerar en antecknings bok kan du **Spara det aktuella**läget med  >  kommandot**Spara och Checkpoint** eller knappen Spara i verktygsfältet. En "kontroll punkt" skapar en ögonblicks bild som du kan återställa till när som helst under sessionen. Med kontroll punkter kan du göra en serie experiment ändringar och om dessa ändringar inte fungerar kan du bara återställa till en kontroll punkt med **File**  >  kommandot**Återställ till kontroll punkt** . En annan metod är att skapa extra celler och kommentera ut all kod som du inte vill köra. båda sätten fungerar.

Du kan också använda **filen**  >  **gör ett kopierings** kommando när som helst för att göra en kopia av den aktuella statusen för antecknings boken till en ny fil i projektet. Kopian öppnas automatiskt i en ny webbläsare-flik.

När du är klar med en antecknings bok **använder du**  >  kommandot**Stäng och stoppa** , som stänger antecknings boken och stänger av den kernel som har körts. Azure Notebooks stänger sedan fliken webbläsare automatiskt.

## <a name="debug-notebooks-using-visual-studio-code"></a>Felsöka antecknings böcker med Visual Studio Code

Om kod cellerna i antecknings boken inte beter sig på det sätt som du förväntar dig kan du ha kod buggar eller andra fel. Men förutom att använda- `print` instruktioner för att visa värdet för variabler, erbjuder en typisk Jupyter-miljö inga fel söknings funktioner.

Lyckligt vis kan du hämta Notebook *. ipynb* -filen och sedan öppna den i Visual Studio Code med hjälp av python-tillägget. Tillägget importerar en antecknings bok direkt som en enda kod fil och bevarar markdown-cellerna i kommentarer. När du har importerat antecknings boken kan du använda Visual Studio Code debugger för att gå igenom koden, ange Bryt punkter, granska status och så vidare. När du har gjort ändringar i koden exporterar du *. ipynb* -filen från Visual Studio Code och laddar upp den igen till Azure Notebooks.

Mer information finns i [Felsöka en Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) i Visual Studio Code-dokumentationen.

Se även [Visual Studio Code-Jupyter support](https://code.visualstudio.com/docs/python/jupyter-support) för ytterligare funktioner i Visual Studio Code för Jupyter Notebooks.

## <a name="next-steps"></a>Nästa steg

- [Utforska notebook-exempelfiler](azure-notebooks-samples.md)

Instruktionsartiklar:

- [Skapa och klona projekt](create-clone-jupyter-notebooks.md)
- [Konfigurera och hantera projekt](configure-manage-azure-notebooks-projects.md)
- [Installera paket inifrån en notebook-fil](install-packages-jupyter-notebook.md)
- [Presentera ett bildspel](present-jupyter-notebooks-slideshow.md)
- [Arbeta med datafiler](work-with-project-data-files.md)
- [Få åtkomst till dataresurser](access-data-resources-jupyter-notebooks.md)
- [Använd Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
