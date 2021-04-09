---
title: Använda R med Machine Learning Studio (klassisk) – Azure
description: Använd den här vägledningen för R-programmering för att komma igång med Azure Machine Learning Studio (klassisk) i R för att skapa en prognos lösning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: c619b51f9323477bda4f1ec99aeeb1bfa01028fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100517747"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>Kom igång med Azure Machine Learning Studio (klassisk) i R

**gäller för:** ![ Detta är en bock markering, vilket innebär att den här artikeln gäller Machine Learning Studio (klassisk). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ Detta är ett X, vilket innebär att den här artikeln gäller för Azure Machine Learning ](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

<!-- Stephen F Elston, Ph.D. -->
I den här självstudien får du lära dig hur du använder Azure Machine Learning Studio (klassisk) för att skapa, testa och köra R-kod. I slutet har du en fullständig prognos lösning.

> [!div class="checklist"]
> * Skapa kod för rengöring och transformering av data.
> * Analysera korrelationerna mellan flera av variablerna i vår data uppsättning.
> * Skapa en prognos modell för säsongs tids serier för mjölk produktion.


Machine Learning Studio (klassisk) innehåller många kraftfulla moduler för maskin inlärning och data manipulation. Med R-programmeringsspråket ger den här kombinationen skalbarhet och enkel distribution av Machine Learning Studio (klassisk) med flexibiliteten och djup analysen av R.

Prognosticering är en mycket anställd och användbar analys metod. Vanliga användnings områden från förutsägelse försäljning av säsongs poster och fastställa optimala lager nivåer för att förutsäga makroekonomiska variabler. Prognosticering utförs vanligt vis med tids serie modeller. Tids serie data är data där värdena har ett tids index. Tids indexet kan vara vanligt, till exempel varje månad eller varje minut. Tids indexet kan också vara oregelbundet. En tids serie modell baseras på tids serie data. R-programmeringsspråket innehåller ett flexibelt ramverk och omfattande analys för Time Series-data.

## <a name="get-the-data"></a>Hämta data

I den här självstudien använder du produkter för produktion och prissättning i Kalifornien, som innehåller månatlig information om produktionen av flera mejeri produkter och priset på mjölk fett, som är en benchmark-produkt.

De data som används i den här artikeln, tillsammans med R-skript, kan hämtas från [MachineLearningSamples-Notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Data i filen `cadairydata.csv` har ursprungligen syntetiskts från information som är tillgänglig från sidan för University of Wisconsin- [mejeri på marknaden](https://dairymarkets.com).

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Interagera med R-språk i Machine Learning Studio (klassisk)

Det här avsnittet beskriver grunderna i hur du interagerar med R-programmeringsspråket i den Machine Learning Studio (klassiska) miljön. R-språket är ett kraftfullt verktyg för att skapa anpassade moduler för analys och datamanipulering i den Machine Learning Studio (klassiska) miljön.

Vi använder RStudio för att utveckla, testa och felsöka R-kod på en liten skala. Den här koden klipps ut och klistras in i en [execute R-skriptfil][execute-r-script] som är redo att köras i Machine Learning Studio (klassisk).

### <a name="the-execute-r-script-module"></a>Kör R-skript-modulen

I Machine Learning Studio (klassisk) körs R-skript i modulen [Kör R-skript][execute-r-script] . Ett exempel på modulen [Kör R-skript][execute-r-script] i Machine Learning Studio (klassisk) visas här.

 ![Skärm bild som visar R-programmeringsspråk: modulen kör R-skript valt i Machine Learning Studio (klassisk).](./media/r-quickstart/fig1.png)

I föregående bild visas några av de viktigaste delarna i Machine Learning Studios miljön (klassisk) för att arbeta med modulen [Kör R-skript][execute-r-script] :

* Modulerna i experimentet visas i mittenfönstret.
* Den övre delen av den högra rutan innehåller ett fönster som du kan använda för att visa och redigera R-skript.
* I den nedre delen av den högra rutan visas egenskaper för [Kör R-skriptet][execute-r-script]. Du kan visa fel-och utgående loggar genom att välja lämpliga områden i det här fönstret.

Vi diskuterar [Kör R-skriptet][execute-r-script] i större detalj i resten av den här artikeln.

När du arbetar med komplexa R-funktioner rekommenderar vi att du redigerar, testar och felsöker i RStudio. Precis som med all program utveckling utökar du koden stegvis och testar den på små, enkla test fall. Klipp sedan ut och klistra in funktionerna i R-skript fönstret i modulen [Kör R-skript][execute-r-script] . Med den här metoden kan du utnyttja både RStudio-Integrated Development Environment (IDE) och kraften i Machine Learning Studio (klassisk).

#### <a name="execute-r-code"></a>Kör R-kod

All R-kod i modulen [Kör R-skript][execute-r-script] körs när du kör experimentet genom att klicka på knappen **Kör** . När körningen har slutförts visas en bock på [skript ikonen kör R][execute-r-script] .

#### <a name="defensive-r-coding-for-machine-learning-studio-classic"></a>Försvars-R-kodning för Machine Learning Studio (klassisk)

Om du utvecklar R-kod för, t. ex. en webb tjänst med hjälp av Machine Learning Studio (klassisk) bör du planera hur din kod ska hantera en oväntad data inmatning och undantag. För att upprätthålla klarhet har vi inte inkluderat mycket på sättet att kontrol lera eller hantera undantag i de flesta kod exemplen som visas. Som vi går vidare ger vi dig flera exempel på funktioner med hjälp av R s undantags hanterings funktion.

Om du behöver en mer fullständig behandling av R undantags hantering läser du de relevanta avsnitten i boken av Wickham som listas i [vidare läsning](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Felsöka och testa R i Machine Learning Studio (klassisk)

Testa och Felsök din R-kod på en liten skala i RStudio. Det finns också fall där du behöver spåra R-kod problem i själva [execute r-skriptet][execute-r-script] . Dessutom är det en bra idé att kontrol lera resultaten i Machine Learning Studio (klassisk).

Utdata från körningen av R-koden och på den Machine Learning Studio (klassiska)-plattformen identifieras främst i output. log. Det finns mer information i error. log.

Om ett fel inträffar i Machine Learning Studio (klassisk) när du kör din R-kod bör din första åtgärd vara att titta på error. log. Den här filen kan innehålla användbara fel meddelanden som hjälper dig att förstå och korrigera felet. Om du vill visa fel. log väljer du **Visa fel logg** i rutan Egenskaper för [skriptet kör R][execute-r-script] som innehåller felet.

Vi körde till exempel följande R-kod, med en odefinierad variabel i y, i en [execute r-skript][execute-r-script] -modul.

```r
x <- 1.0
z <- x + y
```

Den här koden kan inte köras, vilket resulterar i ett fel tillstånd. Om du väljer **Visa fel logg** i fönstret Egenskaper visas följande visning.

  ![Skärm bild som visar ett fel meddelande popup-fönster.](./media/r-quickstart/fig2.png)

Det verkar som om vi måste titta i output. log för att se R-fel meddelandet. Välj modulen [Kör R-skript][execute-r-script] och välj sedan objektet **Visa utdata. log** i fönstret Egenskaper till höger. Ett nytt webbläsarfönster öppnas och följande fel meddelande visas.

```output
[Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
---------- Start of error message from R ----------
object 'y' not found


object 'y' not found
----------- End of error message from R -----------
```

Det här fel meddelandet innehåller inga överraskningar och identifierar tydligt problemet.

Om du vill kontrol lera värdet för ett objekt i R kan du skriva ut dessa värden till filen output. log. Reglerna för att undersöka objekt värden är i princip samma som i en interaktiv R-session. Om du till exempel anger ett variabel namn på en rad skrivs värdet för objektet ut till filen output. log.

#### <a name="packages-in-machine-learning-studio-classic"></a>Paket i Machine Learning Studio (klassisk)

Machine Learning Studio (klassisk) levereras med fler än 350 förinstallerade R-språkpaket. Du kan använda följande kod i modulen [Kör R-skript][execute-r-script] för att hämta en lista över de förinstallerade paketen.

```r
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Om du inte förstår den sista raden i koden för tillfället läser du vidare. I resten av den här artikeln diskuterar vi omfattande användning av R i den Machine Learning Studio (klassiska) miljön.

### <a name="introduction-to-rstudio"></a>Introduktion till RStudio

RStudio är en mycket Använd IDE för R. Vi använder RStudio för att redigera, testa och felsöka en del av R-koden som används i den här guiden. När R-koden har testats och är klar kan du klippa ut och klistra in från RStudio-redigeraren i en Machine Learning Studio (klassisk) [köra R-skript][execute-r-script] -modulen.

Om du inte har installerat R-programmeringsspråket på den station ära datorn gör du det nu. Kostnads fria hämtningar av R-språket med öppen källkod är tillgängliga på det [omfattande r Archive-nätverket (cran)](https://www.r-project.org/). Hämtningar är tillgängliga för Windows, macOS och Linux/UNIX. Välj en närliggande spegling och följ anvisningarna för hämtning. Dessutom innehåller CRAN många användbara analys-och data manipulations paket.

Om du inte har använt RStudio bör du ladda ned och installera Skriv bords versionen. Du kan hitta RStudio-nedladdningar för Windows, macOS och Linux/UNIX på [RStudio](http://www.rstudio.com/products/RStudio/). Följ anvisningarna för att installera RStudio på din station ära dator.

En själv studie kurs introduktion till RStudio finns i [använda RSTUDIO IDE](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Mer information om hur du använder RStudio finns i [hand boken till RStudio-dokumentationen](#appendixa).

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Hämta data in och ut ur modulen kör R-skript

I det här avsnittet diskuterar vi hur du hämtar data till och från [köra R-skript][execute-r-script] -modulen. Vi kommer också att se hur du hanterar olika data typer som läses in i och ut ur modulen [Kör R-skript][execute-r-script] .

Den fullständiga koden för det här avsnittet är i [MachineLearningSamples-Notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data"></a>Läsa in och kontrol lera data

#### <a name="load-the-dataset"></a><a id="loading"></a>Läs in data uppsättningen

Vi börjar med att läsa in **csdairydata.csv** -filen i Machine Learning Studio (klassisk).

1. Starta din Machine Learning Studio (klassiska) miljö.
1. Välj **+ ny** längst ned till vänster på skärmen och välj **data uppsättning**.
1. Välj **från lokal fil** och välj sedan **Bläddra** för att välja filen.
1. Se till att du **har valt en allmän CSV-fil med sidhuvud (. csv)** som typ för data uppsättningen.
1. Markera kryss rutan.
1. När data uppsättningen har överförts bör du se den nya data uppsättningen när du väljer fliken **data uppsättningar** .

#### <a name="create-an-experiment"></a>Skapa ett experiment

Nu när vi har några data i Machine Learning Studio (klassisk) måste vi skapa ett experiment för att utföra analysen.  

1. Välj **+ ny** längst ned till vänster på skärmen och välj **experiment**  >  **tomt experiment**.
1. Namnge experimentet genom att välja och ändra **experimentet som skapats på** rubrik överst på sidan. Ändra till exempel den till **ca mejeri analys**.
1. Till vänster på experiment-sidan väljer du **sparade data uppsättningar**  >  **mina data uppsättningar**. Du bör se **cadairydata.csv** -filen som du överförde tidigare.
1. Dra **csdairydata.csv data uppsättning** till experimentet.
1. I rutan **Sök efter experiment objekt** längst upp i det vänstra fönstret anger du [Kör R-skript][execute-r-script]. Modulen visas i Sök listan.
1. Dra modulen [Kör R-skript][execute-r-script] till din lastpall.
1. Anslut utdata från **csdairydata.csv data uppsättning** till den vänstra inmatningen (**Dataset1**) för [execute R-skriptet][execute-r-script].
1. Välj **Spara**.

I det här läget bör experimentet se ut ungefär som det här exemplet.

![Diagram som visar analys av CA mejeri analyser med data uppsättning och köra R-skript-modulen.](./media/r-quickstart/fig3.png)


#### <a name="check-on-the-data"></a>Kontrol lera data

Nu ska vi titta på de data som vi har läst in i vårt experiment. I experimentet väljer du utdata från den **cadairydata.csv data uppsättningen** och väljer **visualisera**. Du bör se något som liknar denna sammanfattning.

![Skärm bild som visar en sammanfattning av cadairydata.csv data uppsättningen.](./media/r-quickstart/fig4.png)

I den här vyn visas mycket värdefull information. Vi kan se de första flera raderna i data uppsättningen. Om vi väljer en kolumn, visas mer information om kolumnen i **statistik** avsnittet. Exempelvis visar raden **funktions typ** vilka data typer Machine Learning Studio (klassisk) som har tilldelats till kolumnen. Kontrol lera den här vyn innan du börjar göra ett allvarligt arbete.

### <a name="first-r-script"></a>Första R-skriptet

Nu ska vi skapa ett enkelt första R-skript för att experimentera i Machine Learning Studio (klassisk). Vi har skapat och testat följande skript i RStudio.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Nu måste vi överföra skriptet till Machine Learning Studio (klassisk). Du kan klippa ut och klistra in, men i det här fallet överför du R-skriptet via en zip-fil.

### <a name="data-input-to-the-execute-r-script-module"></a>Data inmatning i modulen kör R-skript

Nu ska vi titta på indata till modulen [Kör R-skript][execute-r-script] . I det här exemplet ska vi läsa in mejeri data från Kalifornien i modulen [Kör R-skript][execute-r-script] .

Det finns tre möjliga indata för modulen [Kör R-skript][execute-r-script] . Du kan använda vilken som helst av dessa indata, beroende på ditt program. Du kan också använda ett R-skript som inte tar emot några inmatade värden.

Nu ska vi titta på var och en av dessa indata, från vänster till höger. Du kan se namnen på var och en av indata genom att placera markören över indata och läsa knapp beskrivningen.

#### <a name="script-bundle"></a>Skript paket

Med skript paketets indata kan du skicka innehållet i en zip-fil till modulen [Kör R-skript][execute-r-script] . Du kan använda något av följande kommandon för att läsa innehållet i zip-filen till din R-kod.

```r
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Machine Learning Studio (klassisk) behandlar filer i zip-filen som om de finns i src/Directory, så du måste använda prefixet för fil namnen med detta katalog namn. Om zip-filen till exempel innehåller filerna `yourfile.R` och `yourData.rdata` i roten för zip-filen, så kan du adressera dessa filer som `src/yourfile.R` och `src/yourData.rdata` när du använder `source` och `load` .

Vi har redan diskuterat inläsning av data uppsättningar i [läsa in data uppsättningen](#loading). När du har skapat och testat R-skriptet som visas i föregående avsnitt, gör du följande:

1. Spara R-skriptet i en. R-fil. Vi anropar skript filen **simpleplot. R**. Här är what's i filen:

   ```r
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Skapa en zip-fil och kopiera skriptet till den här zip-filen. I Windows kan du högerklicka på filen och välja **Skicka till**  >  **komprimerad mapp**. Den här åtgärden skapar en ny zip-fil som innehåller **simpleplot. R** -fil.

1. Lägg till din fil till **data uppsättningarna** i Machine Learning Studio (klassisk) och ange typen som **zip**. Du bör nu se zip-filen i dina data uppsättningar.

1. Dra zip-filen från **data uppsättningar** till den **ml Studio (klassiska) arbets ytan**.

1. Anslut utdata från **zip** -dataikonen till **skript bunts** indata för modulen [Kör R-skript][execute-r-script] .

1. Ange `source()` funktionen med namnet på zip-filen i fönstret kod för modulen [Kör R-skript][execute-r-script] . I det här fallet angav vi `source("src/simpleplot.R")` .

1. Välj **Spara**.

När dessa steg har utförts körs r [-skriptet i][execute-r-script] zip-filen när experimentet körs. I det här läget bör experimentet se ut ungefär som det här exemplet.

![Diagram som visar ett experiment med ett zippat R-skript.](./media/r-quickstart/fig6.png)

#### <a name="dataset1"></a>Dataset1

Du kan skicka en rektangulär tabell med data till din R-kod genom att använda Dataset1-indata. I vårt enkla skript `maml.mapInputPort(1)` läser funktionen data från port 1. Dessa data tilldelas sedan till ett dataframe-variabel namn i din kod. I vårt enkla skript utför den första raden i koden tilldelningen.

```r
cadairydata <- maml.mapInputPort(1)
```

Kör experimentet genom att klicka på knappen **Kör** . När körningen är klar väljer du modulen [Kör R-skript][execute-r-script] och väljer sedan **Visa utgående logg** i fönstret Egenskaper. En ny sida bör visas i webbläsaren som visar innehållet i filen utdata. log. När du bläddrar nedåt bör du se något som liknar följande utdata.

```output
[ModuleOutput] InputDataStructure
[ModuleOutput]
[ModuleOutput] {
[ModuleOutput]  "InputName":Dataset1
[ModuleOutput]  "Rows":228
[ModuleOutput]  "Cols":9
[ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
[ModuleOutput] }
```

Längre ned sidan är mer detaljerad information om kolumnerna, som ser ut ungefär så här.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput]
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput]
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
[ModuleOutput]
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput]
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput]
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput]
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
```

Dessa resultat är främst som förväntat, med 228 observationer och 9 kolumner i dataframe. Vi kan se kolumn namnen, data typen R och ett exempel på varje kolumn.

> [!NOTE]
> Samma utskrivna utdata är enkelt att komma åt från R-enhetens utdata från modulen [Kör R-skript][execute-r-script] . Vi diskuterar utdata för modulen [Kör R-skript][execute-r-script] i nästa avsnitt.  

#### <a name="dataset2"></a>Dataset2

Beteendet för Dataset2-indatamängden är identiskt med Dataset1. Med den här inmatningen kan du skicka en andra rektangulär tabell med data till din R-kod. Funktionen `maml.mapInputPort(2)` med argumentet 2 används för att skicka dessa data.  

### <a name="execute-r-script-outputs"></a>Köra R-skript-utdata

#### <a name="output-a-dataframe"></a>Mata ut en dataframe

Du kan mata ut innehållet i en R-dataframe som en rektangulär tabell via resultatet Dataset1-porten med hjälp av `maml.mapOutputPort()` funktionen. I vårt enkla R-skript utförs den här åtgärden av följande rad.

```r
maml.mapOutputPort('cadairydata')
```

När du har kört experimentet väljer du utdataporten result Dataset1 och väljer sedan **visualisera**. Du bör se något som liknar det här exemplet.

![Skärm bild som visar visualiseringen av resultatet av mejeri data från Kalifornien.](./media/r-quickstart/fig7.png)

Resultatet ser likadant ut som indata, precis som vi förväntade dig.

### <a name="r-device-output"></a>R enhets utdata

Enhetens utdata från modulen [Kör R-skript][execute-r-script] innehåller meddelanden och grafik utdata. Både standard-och standard fel meddelanden från R skickas till utdataporten R Device.

Om du vill visa utdata för R-enheten väljer du porten och väljer sedan **visualisera**. Vi ser standardutdata och standard fel från R-skriptet här.

![Skärm bild som visar standardutdata och standard fel från R-enhetens port.](./media/r-quickstart/fig8.png)

Bläddra nedåt ser vi bildutdata från vårt R-skript.

![Skärm bild som visar grafik utdata från R-enhetens port.](./media/r-quickstart/fig9.png)

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Data filtrering och transformering

I det här avsnittet ska vi utföra vissa grundläggande data filtrerings-och omvandlings åtgärder på mejeri data från Kalifornien. I slutet av det här avsnittet har vi data i ett format som lämpar sig för att skapa en analys modell.

Mer specifikt i det här avsnittet ska vi utföra flera vanliga åtgärder för data rensning och omvandling: typ omvandling, filtrering av dataframes, lägga till nya beräknade kolumner och värde transformationer. Den här bakgrunden ska hjälpa dig att hantera de många variationer som uppstått i verkliga problem.

Den fullständiga R-koden för det här avsnittet är tillgänglig i [MachineLearningSamples-Notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Skriv omvandlingar

Nu när vi kan läsa in mejeri data från Kalifornien i R-koden i modulen [Kör R-skript][execute-r-script] , måste vi se till att data i kolumnerna har den tänkta typen och formatet.

R är ett dynamiskt inskrivet språk, vilket innebär att data typerna tvingas från ett till ett annat vid behov. Atomiska data typer i R inkluderar numeriska, logiska och tecken. Faktor typen används för att komprimera kategoriska-data på ett komprimerat lager. Mer information om data typer finns i referenserna i [vidare läsning](#appendixb).

När tabell data läses in i R från en extern källa är det alltid en bra idé att kontrol lera de resulterande typerna i kolumnerna. Du kanske vill ha en kolumn av typen Character, men i många fall visas kolumnen som faktor eller vice versa. I andra fall ska en kolumn som du tror ska vara numerisk representeras av tecken data, till exempel "1,23" i stället för 1,23 som ett flytt ALS nummer.

Lyckligt vis är det enkelt att konvertera en typ till en annan, så länge mappning är möjlig. Du kan till exempel inte konvertera "Nevada" till ett numeriskt värde, men du kan konvertera det till en faktor (kategoriska variabel). Ett annat exempel är att konvertera ett numeriskt värde 1 till ett tecken "1" eller en faktor.

Syntaxen för någon av dessa konverteringar är enkel: `as.datatype()` . Dessa typer av konverterings funktioner innehåller följande funktioner:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

När du tittar på data typerna i de kolumner som vi har angett i föregående avsnitt, är alla kolumner av typen numerisk. Undantaget är kolumnen "månad", som är av typen Character. Nu ska vi konvertera den här typen till en faktor och testa resultatet.

Vi har tagit bort raden som skapade punkt ritnings matrisen och lade till en rad för att konvertera kolumnen månad till en faktor. I det här experimentet klipper vi ut och klistrar in R-koden i fönstret kod i modulen [Kör R-skript][execute-r-script] . Du kan också uppdatera zip-filen och ladda upp den till Machine Learning Studio (klassisk), men det här alternativet tar flera steg.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Nu ska vi köra den här koden och titta på utdata-loggen för R-skriptet. Relevanta data från loggen visas här.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Typen för månad bör nu säga **faktorn med 14 nivåer**. Den här typen är ett problem eftersom det bara finns 12 månader under året. Du kan också kontrol lera att typen i **visualiseringen** av resultat data uppsättnings porten är **kategoriska**.

Problemet är att kolumnen month inte har kodats systematiskt. I vissa fall kallas en månad april, och i andra är den förkortad som apr. Vi kan lösa det här problemet genom att rensa strängen till tre tecken. Kodraden ser nu ut som i följande exempel.

```r
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Kör experimentet igen och visa utdata-loggen. De förväntade resultaten visas här.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


Vår Factor Variable har nu de 12 nivåer som önskas.

### <a name="basic-dataframe-filtering"></a>Grundläggande filtrering av dataframe

R-dataframes stöder kraftfulla filtrerings funktioner. Data uppsättningar kan subsetted med hjälp av logiska filter på antingen rader eller kolumner. I många fall krävs komplexa filter kriterier. Omfattande exempel på filtrering av dataframes finns i referenserna för att [läsa mer](#appendixb).

Det finns en del av filtreringen som vi bör göra på vår data uppsättning. Om du tittar på kolumnerna i cadairydata-dataframe visas två onödiga kolumner. Den första kolumnen innehåller ett rad nummer som inte är användbart. Den andra kolumnen år. månad innehåller redundant information. Vi kan enkelt undanta dessa kolumner genom att använda följande R-kod.

> [!NOTE]
> Från och med nu i det här avsnittet visar vi den ytterligare kod vi lägger till i modulen [Kör R-skript][execute-r-script] . Vi lägger till varje ny rad *före* `str()` funktionen. Vi använder den här funktionen för att verifiera resultaten i Machine Learning Studio (klassisk).

Vi lägger till följande rad i R-koden i modulen [Kör R-skript][execute-r-script] .

```r
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Kör den här koden i experimentet och kontrol lera resultatet från utgående loggen. Dessa resultat visas här.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  7 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Vi får nu det förväntade resultatet.

### <a name="add-a-new-column"></a>Lägg till en ny kolumn

För att skapa tids serie modeller är det lämpligt att ha en kolumn som innehåller månaderna sedan tids serien startades. Vi ska skapa den nya kolumn månaden. Count.

Vi hjälper dig att ordna koden genom att skapa vår första enkla funktion `num.month()` . Vi använder sedan den här funktionen för att skapa en ny kolumn i dataframe. Den nya koden är följande:

```r
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

Kör nu det uppdaterade experimentet och Använd utdata-loggen för att visa resultatet. Dessa resultat visas här.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


Det verkar som om allt fungerar. Vi har den nya kolumnen med de förväntade värdena i vår dataframe.

### <a name="value-transformations"></a>Värde omvandlingar

I det här avsnittet ska vi utföra några enkla transformeringar för värdena i några av kolumnerna i dataframe. R-språket stöder nästan godtyckliga värde transformationer. Fler exempel finns i referenserna i [vidare läsning](#appendixb).

Om du tittar på värdena i sammanfattningarna av dataframe bör du se någonting udda här. Är mer glass grädde än mjölk producerad i Kalifornien? Nej, naturligtvis inte. Problemet är att enheterna är olika. Priset är i enheter om amerikanska pund, mjölk är i enheter om 1 000 000 amerikanska pund, Ice grädde är i enheter om 1 000 amerikanska gallons och Cottage ost är i enheter om 1 000 US pund. Förutsatt att Ice-grädde väger cirka 6,5 kg per liter kan vi enkelt omvandla dessa värden så att de är lika med 1 000 pund.

För vår prognos modell använder vi en multiplicative modell för trend-och säsongs anpassning av dessa data. En logg omvandling gör att vi kan använda en linjär modell som fören klar processen. Vi kan använda logg omvandlingen i samma funktion där multiplikatorn används.

I följande kod definierar vi en ny funktion, `log.transform()` och tillämpar den på de rader som innehåller numeriska värden. Funktionen R `Map()` används för att tillämpa `log.transform()` funktionen på de markerade kolumnerna i dataframe. `Map()`Funktionen liknar `apply()` , men den tillåter fler än en lista med argument till funktionen. Observera att en lista med multiplikatorer tillhandahåller det andra argumentet för `log.transform()` funktionen. `na.omit()`Funktionen används som en del av rensningen för att se till att det inte finns saknade eller odefinierade värden i dataframe.

```r
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warning message to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

Det är ganska en bit i `log.transform()` funktionen. Merparten av den här koden kontrollerar eventuella problem med argumenten eller hanterar undantag, som fortfarande kan uppstå under beräkningarna. Endast några rader i den här koden gör beräkningar.

Syftet med försvars programmering är att förhindra att en enskild funktion Miss lyckas, vilket förhindrar bearbetning från att fortsätta. Ett plötsligt avbrott i en tids krävande analys kan vara frustrerande för användare. För att undvika den här situationen, måste standard retur värden väljas som begränsar skador till efterföljande bearbetning. Ett meddelande skapas också för att varna användare om att något har gått fel.

Om du inte använder för att dra in program vara i R kan all den här koden vara överbelastad. Låt oss gå igenom de viktigaste stegen:

1. En Vector med fyra meddelanden har definierats. Dessa meddelanden används för att förmedla information om några av de möjliga fel och undantag som kan uppstå med den här koden.
1. Vi returnerar värdet NA för varje fall. Det finns många andra möjligheter som kan ha färre sido effekter. Vi kan till exempel returnera en Vector med nollor eller den ursprungliga Indataporten.
1. Kontrollerna körs på argumenten till funktionen. Om ett fel upptäcks i varje fall returneras ett standardvärde och ett meddelande skapas av `warning()` funktionen. Vi använder `warning()` i stället för `stop()` eftersom det senare kommer att avsluta körningen, vilket är vad vi försöker undvika. Den här koden är skriven i ett procedur format, eftersom i det här fallet är en funktionell metod som är komplicerad och skymd.
1. Logg beräkningarna är omslutna `tryCatch()` så att undantag inte orsakar en kraftig stoppad bearbetning. Utan `tryCatch()` de flesta fel som skapats av R Functions resulterar i en stopp signal, vilket bara gör det.

Kör den här R-koden i experimentet och titta på de utskrifter som skrivs ut i filen output. log. Du ser nu transformerade värden för de fyra kolumnerna i loggen, som du ser här.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Vi ser att värdena har omvandlats. Mjölk produktion är nu avsevärt större än alla andra mejeri produkt produktioner, vilket erinrar om att vi nu tittar på en logg skala.

I det här läget rensas våra data och vi är redo för vissa modeller. Om du tittar på visualiserings sammanfattningen för resultatet av den resulterande data uppsättningen i [Kör R-skriptet][execute-r-script] ser du att kolumnen month är kategoriska med 12 unika värden, vilket är precis som vi ville.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Time Series-objekt och korrelations analys

I det här avsnittet ska vi utforska några grundläggande R Time Series-objekt och analysera korrelationer mellan några av variablerna. Vårt mål är att mata ut en dataframe som innehåller den korrelations informationen på flera lags.

Den fullständiga R-koden för det här avsnittet finns i [MachineLearningSamples-Notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Time Series-objekt i R

Som redan nämnts är tids serier en serie data värden som indexeras per tid. R Time Series-objekt används för att skapa och hantera tids index. Det finns flera fördelar med att använda Time Series-objekt. Time Series-objekt är kostnads fria från de många detaljerna för hantering av tids serie index värden som är inkapslade i objektet. Dessutom tillåter Time Series-objekt att du kan använda många tids serie metoder för att rita, skriva ut, modellera och så vidare.

POSIXct Time Series-klassen används ofta och är relativt enkel. Den här Time Series-klassen mäter tid från början av epoken, 1 januari 1970. Vi använder POSIXct Time Series-objekt i det här exemplet. Andra vanliga R Time Series-objekt klasser inkluderar Zoo-och XTS (utöknings bar tids serie).

### <a name="time-series-object-example"></a>Exempel på tids serie objekt

Vi börjar med vårt exempel. Dra en ny [köra R-skriptfil][execute-r-script] till experimentet. Anslut resultat Dataset1-utdataporten för den befintliga [Kör r-skript][execute-r-script] -modulen till Dataset1-Indataporten för den nya [Kör r-skript][execute-r-script] -modulen.

Som vi gjorde för de första exemplen kommer vi att fortskrida genom exemplet. Vid vissa tidpunkter visar vi bara de stegvisa ytterligare raderna i R-koden i varje steg.

#### <a name="read-the-dataframe"></a>Läs dataframe

Som ett första steg ska vi läsa i en dataframe och se till att vi får förväntat resultat. Följande kod ska utföra jobbet.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Kör nu experimentet. Loggen för den nya kör R skript formen bör se ut som i det här exemplet.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
```

Dessa data är av förväntade typer och format. Nu är kolumnen månad av typen faktor och har det förväntade antalet nivåer.

#### <a name="create-a-time-series-object"></a>Skapa ett Time Series-objekt

Vi måste lägga till ett Time Series-objekt i vårt dataframe. Ersätt den aktuella koden med följande kod, som lägger till en ny kolumn i klassen POSIXct.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Kontrol lera nu loggen. Den bör se ut som i det här exemplet.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

Vi kan se från sammanfattningen att den nya kolumnen är i själva verket för klassen POSIXct.

### <a name="explore-and-transform-the-data"></a>Utforska och transformera data

Nu ska vi utforska några av variablerna i den här data uppsättningen. En matris för punkt diagram är ett bra sätt att skapa en snabb titt. Vi ersätter `str()` funktionen i föregående R-kod med följande rad.

```r
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Kör den här koden och se vad som händer. Observations området som skapas på R-enhets porten bör se ut som i det här exemplet.

![Skärm bild som visar matrisen för punkt diagram för markerade variabler.](./media/r-quickstart/fig17.png)

Det finns en del udda struktur i relationerna mellan dessa variabler. Den här strukturen uppstår kanske från trender i data och från det faktum att vi inte har standardiserat variablerna.

### <a name="correlation-analysis"></a>Korrelationsanalys

För att kunna utföra korrelations analyser måste vi både ta bort och standardisera variablerna. Vi kan bara använda R `scale()` -funktionen, som båda centrerar och skalar variabler. Den här funktionen kan köras snabbare. Men låt oss titta på ett exempel på en försvars-programmering i R.

`ts.detrend()`Följande funktion utför båda dessa åtgärder. Följande två rader med kod som trendar data och standardiserar sedan värdena.

```r
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

Det är ganska en bit i `ts.detrend()` funktionen. Merparten av den här koden kontrollerar eventuella problem med argumenten eller hanterar undantag, som fortfarande kan uppstå under beräkningarna. Endast några rader i den här koden utför beräkningar.

Vi har redan diskuterat ett exempel på en försvars-programmering i värde transformationer. Båda beräknings blocken har omslutits `tryCatch()` . För vissa fel är det klokt att returnera den ursprungliga Indataporten. I andra fall returnerar vi en Vector med nollor.

Observera att den linjära regressionen som används för avtrendering är en tids serie regression. Förutsägelse variabeln är ett Time Series-objekt.

När `ts.detrend()` har definierats tillämpas den på variabler av intresse i vår dataframe. Vi måste bearbeta den resulterande listan som skapats av `lapply()` till data i dataframe med hjälp av `as.data.frame()` . På grund av försvars aspekter av `ts.detrend()` , förhindrar inte bearbetningen av en av variablerna de andra.

Den sista kodraden skapar ett biplacerat spridnings område. När du har kört R-koden visas resultatet av punkt ritningen här.

![Skärm bild som visar det bislagna spridnings området för trendade och standardiserade tids serier.](./media/r-quickstart/fig18.png)

Du kan jämföra dessa resultat med de som visas i föregående exempel. När trenden har tagits bort och variablerna standardiseras, ser vi en mycket mindre struktur i relationerna mellan dessa variabler.

Koden för att beräkna korrelationer som R CCF-objekt är följande.

```r
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

Om du kör den här koden skapas den logg som visas här.

```output
[ModuleOutput] Loading objects:
[ModuleOutput]   port1
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] [[1]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.148 0.358 0.317 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[2]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.395 -0.186 -0.238 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[3]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.059 -0.089 -0.127 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[4]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.140 0.294 0.293 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[5]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.002 -0.074 -0.124 
```

Det finns ett korrelations värde för varje fördröjning. Ingen av dessa korrelations värden är tillräckligt stor för att vara signifikant. Vi kan anta att vi kan modellera varje variabel separat.

### <a name="output-a-dataframe"></a>Mata ut en dataframe
Vi har beräknat de bislagna korrelationerna som en lista med R CCF-objekt. Detta innebär ett problem eftersom utdataporten av resultat data uppsättningen kräver en dataframe. Dessutom är CCF-objektet en lista och vi vill bara ha värdena i det första elementet i den här listan, korrelationerna vid de olika lags.

Följande kod extraherar fördröjnings värden från listan över CCF-objekt, som är själva listor.

```r
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation dataframe and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

Den första kodraden är lite knepig och en förklaring kan hjälpa dig att förstå den. Vi arbetar från insidan, vi har:

1. **[[** Operator med argumentet **1** väljer vektorn för korrelationer i lags från det första elementet i objekt listan CCF.
1. `do.call()`Funktionen tillämpar `rbind()` funktionen över elementen i listan som returneras av `lapply()` .
1. `data.frame()`Funktionen tvingar fram resultatet som genereras av `do.call()` till en dataframe.

Observera att rad namnen finns i en kolumn i dataframe. Om du gör det bevaras rad namnen när de matas ut från [skriptet kör R][execute-r-script].

Om du kör koden skapas de utdata som visas här när vi väljer **visualisera** för att visa utdata på den resulterande data uppsättnings porten. Rad namnen visas i den första kolumnen som avsett.

![Skärm bild som visar resultatet från korrelations analysen.](./media/r-quickstart/fig20.png)

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Exempel på tids serier: säsongs prognoser

Våra data är nu i ett formulär som lämpar sig för analys, och vi har fastställt att det inte finns några betydande korrelationer mellan variablerna. Vi går vidare och skapar en prognos modell för tids serier. Med den här modellen kommer vi att beräkna en mjölk produktion på Kalifornien för 12 månaders 2013.

Vår prognos modell kommer att ha två komponenter, en trend komponent och en säsongs komponent. Den fullständiga prognosen är produkten av de här två komponenterna. Den här typen av modell kallas en multiplicative modell. Alternativet är en additiv modell. Vi har redan tillämpat en logg omvandling för variabler av intresse, vilket gör den här analysen dragbar.

Den fullständiga R-koden för det här avsnittet finns i [MachineLearningSamples-Notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="create-the-dataframe-for-analysis"></a>Skapa dataframe för analys

Börja med att lägga till en ny [Kör R-skript][execute-r-script] -modul i experimentet. Anslut resultat data uppsättningens utdata från den befintliga [Kör R-skriptfilen][execute-r-script] till **Dataset1** -indata för den nya modulen. Resultatet bör se ut ungefär som det här exemplet.

![Diagram som visar experimentet med den nya modulen köra R-skript tillagd.](./media/r-quickstart/fig21.png)

Precis som med korrelations analysen som vi precis slutfört, måste vi lägga till en kolumn med ett POSIXct Time Series-objekt. I följande kod läggs kolumnen till.

```r
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Kör den här koden och titta på loggen. Resultatet bör se ut som i det här exemplet.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

Med det här resultatet är vi redo att starta vår analys.

### <a name="create-a-training-dataset"></a>Skapa en data uppsättning för utbildning

När dataframe är konstruerad måste vi skapa en data uppsättning för utbildning. Dessa data kommer att innehålla alla observationer utom de senaste 12, år 2013, som är vår test data uppsättning. I följande kod under anges dataframe och skapas observationer av mejeri produktion och prisvariabler. Vi skapar sedan ritytor av de fyra produktions-och prisvariablerna. En anonym funktion används för att definiera några ökningar för observationer och sedan iterera över listan över de andra två argumenten med `Map()` . Om du funderar på att en for-slinga skulle ha fungerat bra här är du rätt. Men eftersom R är ett funktionellt språk tittar vi på en funktionell metod.

```r
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Om du kör koden skapas en serie med tids serie kurvor från R-enhetens utdata som visas här. Tids axeln är i datum enheter, vilket är en bra fördel med observations metoden för tids serier.

![Första tids serie ritningen för produktion och pris data från Kalifornien.](./media/r-quickstart/unnamed-chunk-161.png)

![Andra Time Series-området i s mejeri produktion och pris data.](./media/r-quickstart/unnamed-chunk-162.png)

![Tredje Time Series-ritytan i produkt-och pris data från Kalifornien.](./media/r-quickstart/unnamed-chunk-163.png).

![Fjärde tids serie observationer i California mejeri produktion och pris data](./media/r-quickstart/unnamed-chunk-164.png)

### <a name="a-trend-model"></a>En trend modell

Nu när vi har skapat ett Time Series-objekt och hade tittat på data kan vi börja skapa en trend modell för produktion data från Kalifornien. Vi kan använda en Time Series-regression. Det är tydligt från observations området att vi behöver mer än en lutning och en spärr för att korrekt modellera den observerade trenden i tränings data.

Med tanke på små data skalor bygger vi modellen för trend i RStudio och klipper sedan ut och klistrar in den resulterande modellen i Machine Learning Studio (klassisk). RStudio tillhandahåller en interaktiv miljö för den här typen av interaktiva analyser.

Som ett första försök kommer vi att testa en polynom regression med en behörighet på upp till tre. Det finns en riktig risk för överanpassning av de här typerna av modeller. Det är bäst att undvika hög ordnings villkor. `I()`Funktionen förhindrar tolkning av innehållet (tolkar innehållet som det är) och gör att du kan skriva en bokstavligen tolkad funktion i en Regressions ekvation.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Den här funktionen genererar följande utdata.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12667 -0.02730  0.00236  0.02943  0.10586
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0418 on 212 degrees of freedom
## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16
```

Från P-värden ( `Pr(>|t|)` ) i dessa utdata kan vi se att kvadratvärdet kanske inte är signifikant. Vi använder `update()` funktionen för att ändra den här modellen genom att ta bort kvadraten.

```r
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Den här funktionen genererar följande utdata.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12597 -0.02659  0.00185  0.02963  0.10696
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0417 on 213 degrees of freedom
## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16
```

Dessa utdata ser bättre ut. Alla villkor är viktiga. 2e-16-värdet är ett standardvärde och bör inte tas för allvarligt.  

Som ett Sanity-test, ska vi göra en tids serie kurva om produktions data från California mejeri produkter med den trend kurva som visas. Vi har lagt till följande kod i Machine Learning Studio (klassisk) [Kör R skript][execute-r-script] modell (inte RStudio) för att skapa modellen och skapa en rityta. Resultatet visas i följande exempel.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Kalifornien mjölk produktions data med trend modell som visas.](./media/r-quickstart/unnamed-chunk-18.png)

Det ser ut som om trend modellen passar bra för data. Vidare verkar det inte vara något tecken på överanpassning, till exempel udda Wiggles i modell kurvan.

### <a name="seasonal-model"></a>Säsongs modell

Med en trend modell i handen måste vi skicka vidare och ta med säsongs effekter. Vi använder årets månad som en dummy-variabel i den linjära modellen för att avbilda månads månads effekterna. När du introducerar Factor-variabler i en modell får inte avlyssningen beräknas. Om du inte gör det är formeln över-specificerad och R raderar en av de önskade faktorerna men behåller spärr perioden.

Eftersom vi har en tillfredsställande trend modell kan vi använda `update()` funktionen för att lägga till de nya villkoren i den befintliga modellen. -1 i uppdaterings formeln släpper spärr perioden. Fortsätta i RStudio för tillfället:

```r
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Den här funktionen genererar följande utdata.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.06879 -0.01693  0.00346  0.01543  0.08726
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0263 on 202 degrees of freedom
## Multiple R-squared:     1,    Adjusted R-squared:     1
## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16
```

Vi ser att modellen inte längre har en spärr period och har 12 signifikanta månads faktorer. Resultatet är vad vi ville se.

Låt oss göra en annan Time Series-kurva om produktions data från Kalifornien för att se hur väl säsongs modellen fungerar. Vi har lagt till följande kod i Machine Learning Studio (klassisk) [köra R-skriptet][execute-r-script] för att skapa modellen och skapa en rityta.

```r
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Genom att köra den här koden i Machine Learning Studio (klassisk) skapar du ritytan som visas här.

![Kalifornien mjölk produktion med modell, inklusive säsongs effekter.](./media/r-quickstart/unnamed-chunk-20.png)

Anpassningen av de data som visas i det här exemplet är i stället att uppmuntras. Både trenden och säsongs effekterna (månatlig variation) ser rimliga ut.

Låt oss ta en titt på resten, som en annan kontroll i vår modell. Följande kod beräknar de förväntade värdena från våra två modeller, beräknar resterna för säsongs modellen och ritar sedan dessa rester för tränings data.

```r
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time&quot;, ylab =&quot;Residuals of Seasonal Model")
```

Resten av området visas här.

![Rester av säsongs modellen för tränings data.](./media/r-quickstart/unnamed-chunk-21.png)

Dessa rester ser rimliga ut. Det finns ingen särskild struktur, förutom resultatet av 2008-2009-tillbakagången, som vår modell inte har något som konto för att vara särskilt bra.

Ritytan som visas i det här exemplet är användbart för att upptäcka eventuella tids beroende mönster i resten. Den explicita metoden för data behandling och att rita återstående rester som vi använde för att placera resten i tid i observations området. Om vi har ritats `milk.lm$residuals` skulle ritytan inte ha varit i tid i rätt ordning.

Du kan också använda `plot.lm()` för att skapa en serie diagnostiska områden.

```r
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Den här koden skapar en serie diagnostiska områden som visas i följande exempel.

![Det första diagnostiska området för säsongs modellen.](./media/r-quickstart/unnamed-chunk-221.png)

![Andra diagnostiska ritytan för säsongs modellen.](./media/r-quickstart/unnamed-chunk-222.png)

![Tredje Diagnostic-ritytan för säsongs modellen.](./media/r-quickstart/unnamed-chunk-223.png)

![Fjärde Diagnostic-ritytan för säsongs modellen.](./media/r-quickstart/unnamed-chunk-224.png)

Det finns några Influential punkter som identifieras i dessa områden, men ingenting kan orsaka bra problem. Dessutom kan vi se från den vanliga Q-Q-kurvan att resten är nära distribuerade normalt, vilket är ett viktigt antagande för linjära modeller.

### <a name="forecasting-and-model-evaluation"></a>Prognoser och modell utvärdering

Det finns bara en sak att göra för att utföra vårt exempel. Vi behöver beräkna prognoser och mäta felet mot faktiska data. Vår prognos gäller för 12 månader på 2013. Vi kan beräkna ett fel mått för den här prognosen till faktiska data som inte ingår i vår utbildnings data uppsättning. Dessutom kan vi jämföra prestanda på 18 års utbildnings data till tolv månaders test data.

Ett antal mått används för att mäta prestanda för tids serie modeller. I vårt fall använder vi fel meddelandet rot medelvärde (RMS). Följande funktion beräknar RMS-felet mellan två serier.

```r
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

Som med `log.transform()` funktionen som vi diskuterade i avsnittet "värde omvandlingar", finns det en stor mängd fel kontroller och kod för undantags återställning i den här funktionen. Principerna som används är desamma. Arbetet görs på två platser som omslutits `tryCatch()` . Först är tids serien exponentiated, eftersom vi har arbetat med loggarna för värdena. För det andra beräknas det faktiska RMS-felet.

Med en funktion för att mäta RMS-felet kan vi bygga och generera en dataframe som innehåller RMS-felen. Vi inkluderar villkor för enbart trend modellen och den kompletta modellen med säsongs faktorer. Följande kod utför jobbet genom att använda de två linjära modeller som vi har byggt.

```r
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

Om du kör den här koden skapas de utdata som visas här i utdataporten result data uppsättning.

![Skärm bild som visar jämförelsen av RMS-fel för modeller.](./media/r-quickstart/fig26.png)

Från dessa resultat ser vi att det minskar RMS-felet avsevärt om du lägger till säsongs faktorer i modellen. Inte för överraskande, RMS-felet för tränings data är lite mindre än för prognosen.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Guide till RStudio-dokumentation

RStudio är väl dokumenterad. Här följer några länkar till de viktigaste avsnitten i RStudio-dokumentationen för att komma igång.

* **Skapa projekt**: du kan organisera och hantera din R-kod i projekt med hjälp av RStudio. Mer information finns i [using Projects](https://support.rstudio.com/hc/articles/200526207-Using-Projects). Följ dessa anvisningar och skapa ett projekt för R-kod exemplen i den här artikeln.
* **Redigera och kör r-kod**: RStudio tillhandahåller en integrerad miljö för att redigera och köra r-kod. Mer information finns i [Redigera och köra kod](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code).
* **Felsök**: RStudio innehåller kraftfulla fel söknings funktioner. Mer information om dessa funktioner finns i [fel sökning med RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio). Information om fel söknings funktioner för Bryt punkter finns i [fel sökning av Bryt punkter](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting).

## <a name="further-reading"></a><a id="appendixb"></a>Mer läsning

Den här själv studie kursen beskriver grunderna om vad du behöver för att använda R-språket med Machine Learning Studio (klassisk). Om du inte är bekant med R finns två introduktioner på CRAN:

* [R för nybörjare](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) av Emmanuel paradis är en bra plats att börja på.
* [En introduktion till R](https://cran.r-project.org/doc/manuals/R-intro.html) med W. N. Venables et al. går i större djup.

Det finns många böcker på R som kan hjälpa dig att komma igång:

* **Bild av r-programmering: en rundtur i statistisk program design** av Norman Matloff är en utmärkt introduktion till programmering i R.
* **R Cookbook** av Paul Teetor ger en problem-och lösnings metod för att använda R.
* **R i praktiken** av Robert Kabacoff är en annan praktisk introduktions bok. Den medföljande [snabb R-webbplatsen](https://www.statmethods.net/) är en användbar resurs.
* **R-Inferno** av Patrick frätande är en överraskande lättsam-bok som hanterar ett antal olika typer av svåra och svåra ämnen som kan påträffas när du program i R. Boken är tillgänglig kostnads fritt på [R-Inferno](https://www.burns-stat.com/documents/books/the-r-inferno/).
* **Avancerad R** av Hadley Wickham ger en djup inblick i avancerade ämnen i R. Online-versionen av den här boken är tillgänglig kostnads fritt på [Avancerad R](http://adv-r.had.co.nz/).
* **Inledande tids serier med r** av Paul Cowpertwait och Andrew Metcalfe ger en introduktion till att använda R för tids serie analys. Många fler teoretiska texter innehåller R-exempel.

Här är några bra Internet resurser:

* [Cran aktivitetsvy: tids serie analys](https://cran.r-project.org/web/views/TimeSeries.html) har en katalog med R Time Series-paket. Information om vissa tids serie objekt paket finns i dokumentationen för det paketet.
* [Introduktion till R](https://www.datacamp.com/courses/introduction-to-r) är en kostnads fri interaktiv kurs från DataCamp som undervisar R i webbläsarens bekvämlighet med video lektioner och kodnings övningar. Det finns interaktiva självstudier om de senaste R-teknikerna och paketen.
* [Lär dig R-programmering, den definitiva guiden](https://www.datamentor.io/r-programming/) från DataMentor.
* [R programmerare](https://r-coder.com/) innehåller detaljerade r-självstudier och en kostnads fri r-kurs för nybörjare.
* [R-självstudier](https://www.cyclismo.org/tutorial/R/) av Kelly Black från Clarkson University är en snabb kurs.
* [Främsta R-språkresurser för att förbättra dina data kunskaper](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html) visar mer än 60 R-resurser.

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
