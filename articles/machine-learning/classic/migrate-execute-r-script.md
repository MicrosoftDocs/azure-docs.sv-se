---
title: 'ML Studio (klassisk): migrera till Azure Machine Learning-EXECUTE R-skript'
description: Bygg om Studio (klassisk) köra R-skript moduler som ska köras på Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: ac9ad296029451d624345d8b3bb365d881ba9a84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103565288"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>Migrera kör R-skript moduler i Studio (klassisk)

I den här artikeln får du lära dig att återskapa en Studio-modul (klassisk) **köra R-skript** i Azure Machine Learning.

Mer information om hur du migrerar från Studio (klassisk) finns i [översikts artikeln om migrering](migrate-overview.md).

## <a name="execute-r-script"></a>Köra R-skript

Azure Machine Learning designer körs nu på Linux. Studio (klassisk) körs i Windows. På grund av plattforms ändringen måste du justera **Kör R-skriptet** under migreringen, annars Miss förloppet.

Om du vill migrera en **execute R script** -modul från Studio (klassisk) måste du ersätta- `maml.mapInputPort` och- `maml.mapOutputPort` gränssnitten med standard funktioner.

I följande tabell sammanfattas ändringarna i modulen R-skript:

|Funktion|Studio (klassisk)|Azure Machine Learning Designer|
|---|---|---|
|Skript gränssnitt|`maml.mapInputPort` och `maml.mapOutputPort`|Funktions gränssnitt|
|Plattform|Windows|Linux|
|Tillgänglig för Internet |Inga|Ja|
|Minne|14 GB|Beroende av Compute SKU|

### <a name="how-to-update-the-r-script-interface"></a>Så här uppdaterar du R-skript gränssnittet

Här följer innehållet i ett exempel på **Kör R-skript** i Studio (klassisk):
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

Här är det uppdaterade innehållet i designern. Observera att `maml.mapInputPort` och `maml.mapOutputPort` har ersatts med standard funktions gränssnittet `azureml_main` . 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
Mer information finns i modulen för att [köra R-skript](../algorithm-module-reference/execute-r-script.md)i designern.

### <a name="install-r-packages-from-the-internet"></a>Installera R-paket från Internet

Med Azure Machine Learning Designer kan du installera paket direkt från CRAN.

Detta är en förbättring jämfört med Studio (klassisk). Eftersom Studio (klassisk) körs i en sandbox-miljö utan Internet åtkomst var du tvungen att ladda upp skript i ett zip-paket för att installera fler paket. 

Använd följande kod för att installera CRAN-paket i designerns **Kör R-skript** -modul:
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du migrerar köra R-skript-moduler till Azure Machine Learning.

Se de andra artiklarna i serien Studio (klassisk) migrering:

1. [Översikt över migrering](migrate-overview.md).
1. [Migrera data uppsättning](migrate-register-dataset.md).
1. [Återskapa en Studio (klassisk) utbildnings pipeline](migrate-rebuild-experiment.md).
1. [Återskapa en Studio-webbtjänst (klassisk)](migrate-rebuild-web-service.md).
1. [Integrera en Azure Machine Learning-webbtjänst med klient program](migrate-rebuild-integrate-with-client-app.md).
1. **Migrera kör R-skript moduler**.