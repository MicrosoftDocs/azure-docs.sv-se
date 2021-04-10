---
title: Självstudie – köra en parallell R-simulering med Azure Batch
description: Självstudie – Stegvisa instruktioner för hur du kör en finansiell Monte Carlo-simulering i Azure Batch med R-paketet doAzureParallel
ms.devlang: r
ms.topic: tutorial
ms.date: 10/08/2020
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 7e74a3b02be35b40e1a92bef269a3aed0b2313e4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491783"
---
# <a name="tutorial-run-a-parallel-r-simulation-with-azure-batch"></a>Självstudie: Kör en parallell R-simulering med Azure Batch

Kör dina parallella R-arbetsbelastningar i full skala med [doAzureParallel](https://www.github.com/Azure/doAzureParallel), ett enkelt R-paket som gör att du kan använda Azure Batch direkt från din R-session. doAzureParallel-paketet bygger på det populära R-paketet [foreach](https://cran.r-project.org/web/packages/foreach/index.html). doAzureParallel tar varje iteration av loopen foreach och skickar den som en Azure Batch-uppgift.

I den här självstudien visas hur du distribuerar en Batch-pool och kör ett parallellt R-jobb i Azure Batch direkt från RStudio. Lär dig att:


> [!div class="checklist"]
> * installera doAzureParallel och konfigurera det för att komma åt dina Batch- och Storage-konton
> * skapa en Batch-pool som en parallell serverdel för R-sessionen
> * köra en parallell exempelsimulering på poolen.

## <a name="prerequisites"></a>Förutsättningar

* En installerad [R](https://www.r-project.org/)-distribution, som [Microsoft R Open](https://mran.microsoft.com/open). Använd version 3.3.1 eller senare av R.

* [RStudio](https://www.rstudio.com/), antingen den kommersiella versionen eller [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) med öppen källkod.

* Ett Azure Batch-konto och ett Azure Storage-konto. Information om hur du skapar de här kontona finns Batch-snabbstarterna som du kommer åt via [Azure-portalen](quick-create-portal.md) eller [Azure CLI](quick-create-cli.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]
## <a name="install-doazureparallel"></a>Installera doAzureParallel

Installera [GitHub-paketet doAzureParallel](https://www.github.com/Azure/doAzureParallel) i RStudio-konsolen. Med följande kommandon laddar du ned och installerar paketet och dess beroenden i den aktuella R-sessionen:

```R
# Install the devtools package
install.packages("devtools")

# Install rAzureBatch package
devtools::install_github("Azure/rAzureBatch")

# Install the doAzureParallel package
devtools::install_github("Azure/doAzureParallel")

# Load the doAzureParallel library
library(doAzureParallel)
```
Installationen kan ta flera minuter.

Om du vill konfigurera doAzureParallel med de autentiseringsuppgifter du hämtade tidigare genererar du en konfigurationsfil med namnet *credentials.json* i arbetskatalogen:

```R
generateCredentialsConfig("credentials.json")
```

Fyll i den här filen med namnen på dina Batch- och Storage-konton samt nycklarna. Lämna inställningen `githubAuthenticationToken`.

När du är färdig ser filen med autentiseringsuppgifter ut ungefär så här:

```json
{
  "batchAccount": {
    "name": "mybatchaccount",
    "key": "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==",
    "url": "https://mybatchaccount.mybatchregion.batch.azure.com"
  },
  "storageAccount": {
    "name": "mystorageaccount",
    "key": "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=="
  },
  "githubAuthenticationToken": ""
}
```

Spara filen. Kör sedan följande kommando för att ange autentiseringsuppgifterna för den aktuella R-sessionen:

```R
setCredentials("credentials.json")
```

## <a name="create-a-batch-pool"></a>Skapa en Batch-pool

doAzureParallel innehåller en funktion för att generera en Azure Batch-pool (ett kluster) för körning av parallella R-jobb. Noderna kör en Ubuntu-baserad [virtuell Azure Data Science-dator](../machine-learning/data-science-virtual-machine/overview.md). Microsoft R Open och andra populära R-paket är förinstallerade i den här avbildningen. Du kan visa eller anpassa vissa inställningar för klustret, till exempel antalet noder och deras storlek.

Så här skapar du en JSON-fil med klusterkonfiguration i arbetskatalogen:

```R
generateClusterConfig("cluster.json")
```

Öppna filen om du vill se standardkonfigurationen, som innehåller 3 dedikerade noder och 3 noder med [låg prioritet](batch-low-pri-vms.md). De här inställningarna är bara exempel som du kan experimentera med eller ändra. Dedikerade noder är reserverade för din pool. Noder med låg prioritet erbjuds till ett reducerat pris från VM-överskottskapacitet i Azure. Noder med låg prioritet är inte tillgängliga om Azure inte har tillräckligt med kapacitet.

Ändra konfigurationen på följande sätt för den här självstudien:

* Öka `taskSlotsPerNode` till *2* för att dra nytta av båda kärnorna vid varje nod
* Sätt `dedicatedNodes` till *0* så att du prova de virtuella datorerna med låg prioritet som är tillgängliga för Batch. Sätt `min` för `lowPriorityNodes` till *5*. och `max` till *10*, eller välj lägre värden om du vill.

Lämna standardinställningarna för återstående inställningar och spara filen. Det bör se ut ungefär så här:

```json
{
  "name": "myPoolName",
  "vmSize": "Standard_D2_v2",
  "taskSlotsPerNode": 2,
  "poolSize": {
    "dedicatedNodes": {
      "min": 0,
      "max": 0
    },
    "lowPriorityNodes": {
      "min": 5,
      "max": 10
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": []
}
```

Skapa nu klustret. Batch skapar poolen omedelbart, men det tar några minuter att allokera och starta beräkningsnoderna. När klustret är tillgängligt registrerar du det som parallell serverdel för R-sessionen.

```R
# Create your cluster if it does not exist; this takes a few minutes
cluster <- makeCluster("cluster.json")

# Register your parallel backend
registerDoAzureParallel(cluster)

# Check that the nodes are running
getDoParWorkers()
```

I utdata visas antalet ”execution workers” (körningsarbetare) för doAzureParallel. Det här antalet är antalet noder multiplicerat med värdet för `taskSlotsPerNode`. Om du ändrade klusterkonfigurationen enligt beskrivningen ovan är talet *10*.

## <a name="run-a-parallel-simulation"></a>Kör en parallell simulering

Nu när klustret har skapats är du redo att köra foreach-loopen med din registrerade parallella serverdel (Azure Batch-pool). Som ett exempel kör du en finansiell Monte Carlo-simulering, först lokalt med en vanlig foreach-loop och sedan genom att köra foreach med Batch. Det här exemplet är en förenklad version av att förutsäga ett aktiepris genom att simulera ett stort antal olika utfall efter 5 år.

Anta att aktien för Contoso Corporation i genomsnitt ökar 1,001 gånger i värde varje dag, men att volatiliteten (standardavvikelsen) är 0,01. Givet startkursen 100 USD kan du använda en Monte Carlo-simulering för priset och räkna ut Contosos aktiekurs efter 5 år.

Parametrar för Monte Carlo-simuleringen:

```R
mean_change = 1.001
volatility = 0.01
opening_price = 100
```

Simulera slutkurser genom att definiera följande funktion:

```R
getClosingPrice <- function() {
  days <- 1825 # ~ 5 years
  movement <- rnorm(days, mean=mean_change, sd=volatility)
  path <- cumprod(c(opening_price, movement))
  closingPrice <- path[days]
  return(closingPrice)
}
```

Kör först 10 000 simuleringar lokalt med hjälp av en vanlig foreach-loop med nyckelordet `%do%`:

```R
start_s <- Sys.time()
# Run 10,000 simulations in series
closingPrices_s <- foreach(i = 1:10, .combine='c') %do% {
  replicate(1000, getClosingPrice())
}
end_s <- Sys.time()
```


Plotta slutkurserna i ett histogram så att du ser fördelningen av utfallen:

```R
hist(closingPrices_s)
```

De utdata som genereras liknar följande:

![Skärm bild som visar ett histogram med avslutnings priser](media/tutorial-r-doazureparallel/closing-prices-local.png)
  
En lokal simulering tar några sekunder eller ännu mindre:

```R
difftime(end_s, start_s)
```

Uppskattad körningstid för 10 miljoner utfall lokalt, med hjälp av en linjär uppskattning, är cirka 30 minuter:

```R
1000 * difftime(end_s, start_s, unit = "min")
```


Kör nu koden med hjälp av `foreach` och nyckelordet `%dopar%`, och jämför hur lång tid det tar att köra 10 miljoner simuleringar i Azure. Om du vill parallellisera simuleringen med Batch kör du 100 iterationer av 100 000 simuleringar:

```R
# Optimize runtime. Chunking allows running multiple iterations on a single R instance.
opt <- list(chunkSize = 10)
start_p <- Sys.time()
closingPrices_p <- foreach(i = 1:100, .combine='c', .options.azure = opt) %dopar% {
  replicate(100000, getClosingPrice())
}
end_p <- Sys.time()
```

I simuleringen distribueras uppgifter till noderna i Batch-poolen. Du kan se aktiviteten i den termiska kartan för poolen i Azure Portal. Gå till **batch-konton**  >  *myBatchAccount*. Klicka på **pooler**  >  *myPoolName*.

![Termisk karta för pool som kör parallella R-uppgifter](media/tutorial-r-doazureparallel/pool.png)

Simuleringen avslutas efter några minuter. Paketet sammanfogar resultaten och hämtar dem från noderna automatiskt. Sedan är du redo att använda resultaten i R-sessionen.

```R
hist(closingPrices_p)
```

De utdata som genereras liknar följande:

![Fördelning av slutkurser](media/tutorial-r-doazureparallel/closing-prices.png)

Hur lång tid tog den parallella simuleringen?

```R
difftime(end_p, start_p, unit = "min")
```

När simuleringen körs i Batch-poolen ser du att du får mycket bättre prestanda jämfört med att köra simuleringen lokalt.

## <a name="clean-up-resources"></a>Rensa resurser

Jobbet tas bort automatiskt när det är färdigt. När klustret längre behövs anropar du funktionen `stopCluster` i doAzureParallel-paketet för att ta bort klustret:

```R
stopCluster(cluster)
```

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> installera doAzureParallel och konfigurera det för att komma åt dina Batch- och Storage-konton
> * skapa en Batch-pool som en parallell serverdel för R-sessionen
> * köra en parallell exempelsimulering på poolen.


Mer information om doAzureParallel finns i dokumentation och i exemplen på GitHub.

> [!div class="nextstepaction"]
> [Paketet doAzureParallel](https://github.com/Azure/doAzureParallel/)
