---
title: 'Kör R-skript: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen kör R-skript i Azure Machine Learning designer för att köra anpassad R-kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/17/2020
ms.openlocfilehash: bdd7fd8e19bf2de6d0b3c6b2edd4515771fae237
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98119037"
---
# <a name="execute-r-script-module"></a>Kör R-skript-modul

Den här artikeln beskriver hur du använder modulen kör R-skript för att köra R-kod i din Azure Machine Learning designer-pipeline.

Med R kan du utföra uppgifter som inte stöds av befintliga moduler, till exempel: 
- Skapa anpassade data transformationer
- Använd dina egna mått för att utvärdera förutsägelser
- Bygg modeller med algoritmer som inte har implementerats som fristående moduler i designern

## <a name="r-version-support"></a>Stöd för R-versioner

Azure Machine Learning designer använder CRAN-distributionen (omfattande R Archive Network) för R. Den version som används är CRAN 3.5.1.

## <a name="supported-r-packages"></a>R-paket som stöds

R-miljön förinstalleras med fler än 100-paket. En fullständig lista finns i avsnittet [förinstallerade R-paket](#preinstalled-r-packages).

Du kan också lägga till följande kod i valfri EXECUTE R script-modul för att se de installerade paketen.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
> [!NOTE]
> Om din pipeline innehåller flera exekvera R-skript moduler som behöver paket som inte finns i den förinstallerade listan, installerar du paketen i varje modul. 

## <a name="installing-r-packages"></a>Installera R-paket
Om du vill installera ytterligare R-paket använder du- `install.packages()` metoden. Paket installeras för varje EXECUTE R-skript-modul. De delas inte över andra kör R-skript moduler.

> [!NOTE]
> Vi rekommenderar **inte** att du installerar R-paket från skript paketet. Vi rekommenderar att du installerar paket direkt i skript redigeraren.
> Ange CRAN-lagringsplatsen när du installerar paket, till exempel `install.packages("zoo",repos = "http://cran.us.r-project.org")` .

> [!WARNING]
> Excute R script module stöder inte installation av paket som kräver intern kompilering, t `qdap` . ex. paket som kräver Java och `drc` paket som kräver C++. Detta beror på att den här modulen körs i en förinstallerad miljö med icke-administratörs behörighet.
> Installera inte paket som är färdiga i/för Windows, eftersom designer-modulerna körs på Ubuntu. För att kontrol lera om ett paket är förbyggt i Windows kan du gå till [cran](https://cran.r-project.org/) och söka i paketet, ladda ned en binär fil enligt ditt OS och checka in **:** del i **beskrivnings** filen. Följande är ett exempel på en :::image type="content" source="media/module/r-package-description.png" alt-text="Beskrivning av R-paket" lightbox="media/module/r-package-page.png":::

Det här exemplet visar hur du installerar Zoo:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
 > [!NOTE]
 > Innan du installerar ett paket kontrollerar du om det redan finns så att du inte upprepar en installation. Upprepade installationer kan orsaka timeout för webb tjänst begär Anden.     

## <a name="access-to-registered-dataset"></a>Åtkomst till registrerad data uppsättning

Du kan referera till följande exempel kod för att få åtkomst till [registrerade data uppsättningar](../how-to-create-register-datasets.md) på din arbets yta:

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  run = get_current_run()
  ws = run$experiment$workspace
  dataset = azureml$core$dataset$Dataset$get_by_name(ws, "YOUR DATASET NAME")
  dataframe2 <- dataset$to_pandas_dataframe()
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="uploading-files"></a>Laddar upp filer
Kör R-skript-modulen stöder överföring av filer med hjälp av Azure Machine Learning R SDK.

Följande exempel visar hur du överför en bildfil i kör R-skript:
```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

När pipeline-körningen är färdig kan du förhandsgranska bilden i den högra panelen i modulen.

> [!div class="mx-imgBorder"]
> ![Förhands granskning av Uppladdad bild](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Så här konfigurerar du kör R-skript

EXECUTE R-skript-modulen innehåller exempel kod som utgångs punkt.

![Diagram över indata för en R-modul](media/module/execute-r-script.png)

Data uppsättningar som lagras i designern konverteras automatiskt till en R data-ram när de läses in med den här modulen.

1.  Lägg till modulen **Kör R-skript** i din pipeline.  

1. Anslut alla indata som skriptet behöver. Indata är valfria och kan innehålla data och ytterligare R-kod.

    * **Dataset1**: referera till första inmataren som `dataframe1` . Data uppsättningen för indata måste formateras som en CSV-, TSV-eller ARFF-fil. Eller så kan du ansluta en Azure Machine Learning data uppsättning.

    * **Dataset2**: referera till den andra indatamängden som `dataframe2` . Den här data uppsättningen måste också formateras som en CSV-, TSV-eller ARFF-fil eller som en Azure Machine Learning data uppsättning.

    * **Skript paket**: den tredje indatamängden accepterar. zip-filer. En zippad fil kan innehålla flera filer och flera filtyper.

1. I text rutan **R-skript** skriver eller klistrar du in giltigt R-skript.

    > [!NOTE]
    > Var försiktig när du skriver skriptet. Se till att det inte finns några syntaxfel, till exempel att använda variabler som inte har deklarerats eller ej importerade moduler eller funktioner. Betala extra uppmärksamhet för den förinstallerade paket listan i slutet av den här artikeln. Om du vill använda paket som inte finns i listan installerar du dem i skriptet. Ett exempel är `install.packages("zoo",repos = "http://cran.us.r-project.org")`.
    
    För att hjälpa dig att komma igång fylls text rutan **R-skript** i förväg med exempel kod, som du kan redigera eller ersätta.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main,
    # which is the entry point for this module.

    # Note that functions dependent on the X11 library,
    # such as "View," are not supported because the X11 library
    # is not preinstalled.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a .zip file is connected to the third input port, it's
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

    Start punkt funktionen måste innehålla indataargument `Param<dataframe1>` och `Param<dataframe2>` , även om dessa argument inte används i funktionen.

    > [!NOTE]
    > De data som skickas till modulen kör R-skript refereras till som `dataframe1` och `dataframe2` , som skiljer sig från Azure Machine Learning designer (design referensen som `dataset1` , `dataset2` ). Se till att indata är korrekt refererade i skriptet.  
 
    > [!NOTE]
    > Befintlig R-kod kan behöva mindre ändringar för att köras i en designer-pipeline. Indata som du anger i CSV-format ska till exempel uttryckligen konverteras till en data uppsättning innan du kan använda den i din kod. Data-och kolumn typer som används i R-språket skiljer sig också på vissa sätt från data-och kolumn typer som används i designern.

1. Om skriptet är större än 16 KB använder du **Skriptets paket** port för att undvika fel som *kommando raden överskrider gränsen på 16597 tecken*. 
    
    1. Paketera skriptet och andra anpassade resurser i en zip-fil.
    1. Överför zip-filen som en **fil data uppsättning** till Studio. 
    1. Dra data uppsättnings modulen från listan *data uppsättningar* i fönstret till vänster på design sidan i designern. 
    1. Anslut data uppsättnings modulen till **skript paket** porten för **Kör R-skript** -modulen.
    
    Följande är exempel koden för att använda skriptet i skript paketet:

    ```R
    azureml_main <- function(dataframe1, dataframe2){
    # Source the custom R script: my_script.R
    source("./Script Bundle/my_script.R")

    # Use the function that defined in my_script.R
    dataframe1 <- my_func(dataframe1)

    sample <- readLines("./Script Bundle/my_sample.txt")
    return (list(dataset1=dataframe1, dataset2=data.frame("Sample"=sample)))
    }
    ```

1.  För **slumpmässigt utsäde** anger du ett värde som ska användas i R-miljön som det slumpmässiga startvärdet. Den här parametern motsvarar anrop `set.seed(value)` i R-kod.  

1. Skicka pipelinen.  

## <a name="results"></a>Resultat

Kör R-skript moduler kan returnera flera utdata, men de måste tillhandahållas som R data-ramar. Designern konverterar automatiskt data ramar till data uppsättningar för kompatibilitet med andra moduler.

Standard meddelanden och fel från R returneras till modulens logg.

Om du behöver skriva ut resultaten i R-skriptet kan du hitta de utskrifts resultat som visas i **70_driver_log** under fliken **utdata + loggar** i den högra panelen i modulen.

## <a name="sample-scripts"></a>Exempelskript

Det finns många sätt att utöka din pipeline med anpassade R-skript. Det här avsnittet innehåller exempel kod för vanliga uppgifter.


### <a name="add-an-r-script-as-an-input"></a>Lägg till ett R-skript som inmatade

Modulen kör R-skript stöder godtyckliga R-skriptfiler som indata. Om du vill använda dem måste du överföra dem till din arbets yta som en del av. zip-filen.

1. Om du vill ladda upp en zip-fil som innehåller R-koden till din arbets yta går du till till gångs sidan för **data uppsättningar** . Välj **skapa data uppsättning** och välj sedan alternativet **från lokal fil** och **fil** data uppsättnings typ.  

1. Kontrol lera att den zippade filen visas i **mina data uppsättningar** under kategorin **data uppsättningar** i det vänstra modul trädet.

1.  Anslut data uppsättningen till **skript paketets** indataport.

1. Alla filer i zip-filen är tillgängliga under körningen av pipelinen. 

    Om skript paket filen innehåller en katalog struktur bevaras strukturen. Men du måste ändra koden för att lägga Directory **./script-paketet** till sökvägen.

### <a name="process-data"></a>Bearbeta data

I följande exempel visas hur du skalar och normaliserar indata:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a .zip file is connected to the third input port, it's
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # Find the maximum and minimum values of the width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # Calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # Apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Läs en. zip-fil som indata

Det här exemplet visar hur du använder en data uppsättning i en. zip-fil som indata för modulen kör R-skript.

1. Skapa data filen i CSV-format och ge den namnet **mydatafile.csv**.
1. Skapa en zip-fil och Lägg till CSV-filen i arkivet.
1. Ladda upp den zippade filen till din Azure Machine Learning-arbetsyta. 
1. Anslut den resulterande data uppsättningen till **ScriptBundle** -indata för modulen **Kör R-skript** .
1. Använd följande kod för att läsa CSV-data från den zippade filen.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replikera rader

Det här exemplet visar hur du replikerar positiva poster i en data uppsättning för att balansera exemplet:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Pass R-objekt mellan exekvera R-skript moduler

Du kan skicka R-objekt mellan instanser av modulen kör R-skript med hjälp av funktionen för intern serialisering. Det här exemplet förutsätter att du vill flytta R-objektet med namnet `A` mellan två köra r-skript moduler.

1. Lägg till den första **köra R-skriptfilen** i din pipeline. Ange sedan följande kod i text rutan **R-skript** för att skapa ett serialiserat objekt `A` som en kolumn i modulens utgående data tabell:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Den explicita konverteringen till Integer-typ görs eftersom serialiserings funktionen matar ut data i R `Raw` -formatet, som designer inte stöder.

1. Lägg till en andra instans av modulen **Kör R-skript** och Anslut den till utdataporten för den föregående modulen.

1. Skriv följande kod i text rutan **R-skript** för att extrahera objekt `A` från indata-tabellen. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="preinstalled-r-packages"></a>Förinstallerade R-paket

Följande förinstallerade R-paket är tillgängliga för närvarande:

| Paket      | Version    | 
|--------------|------------| 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3 – 22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| cirkumflex        | 6.0 – 84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| klass        | 7.3-15     | 
| cli          | 1.1.0      | 
| klippare        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0,2 – 16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| datauppsättningar     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0,8-71     | 
| ADFS           | 1.3.1      | 
| gdata        | 2.18.0     | 
| generisk typ     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0 – 18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| grafik     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grid         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0,9 – 9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| labeling     | 0.3        | 
| lattice      | 0,20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matris       | 1.2 – 17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| PS           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| tools        | 3.5.1      | 
| tseries      | 0,10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0,11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.