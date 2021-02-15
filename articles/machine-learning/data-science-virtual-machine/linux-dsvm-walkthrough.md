---
title: Utforska Linux
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig hur du utför flera vanliga data vetenskaps uppgifter med hjälp av Linux-Data Science Virtual Machine.
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 09/17/2020
ms.openlocfilehash: 42136d0d58dbc318aab0e111fcef46f80751ca88
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517679"
---
# <a name="data-science-with-an-ubuntu-data-science-virtual-machine-in-azure"></a>Data vetenskap med en Ubuntu Data Science Virtual Machine i Azure

Den här genom gången visar hur du utför flera vanliga data vetenskaps uppgifter med hjälp av Ubuntu-Data Science Virtual Machine (DSVM). Ubuntu-DSVM är en virtuell dator avbildning som är tillgänglig i Azure och som är förinstallerad med en samling verktyg som ofta används för data analys och maskin inlärning. Viktiga program varu komponenter är indelade i [etablera Ubuntu-data science Virtual Machine](./dsvm-ubuntu-intro.md). DSVM-avbildningen gör det enkelt att komma igång med data vetenskap på några minuter, utan att behöva installera och konfigurera var och en av verktygen individuellt. Du kan enkelt skala upp DSVM om du behöver, och du kan stoppa den när den inte används. DSVM-resursen är både elastisk och kostnads effektiv.

I den här genom gången analyserar vi [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) -datauppsättningen. Spambase är en uppsättning e-postmeddelanden som marker ATS som skräp post eller Ham (inte spam). Spambase innehåller också viss statistik om e-postmeddelandets innehåll. Vi pratar om statistiken senare i genom gången.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan använda en Linux-DSVM måste du ha följande krav:

* **Azure-prenumeration**. Information om hur du skaffar en Azure-prenumeration finns i [skapa ett kostnads fritt Azure-konto idag](https://azure.microsoft.com/free/).

* [**Ubuntu data science Virtual Machine**](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Information om hur du etablerar den virtuella datorn finns i [etablera Ubuntu data science Virtual Machine](./release-notes.md).
* [**X2Go**](https://wiki.x2go.org/doku.php) installerat på datorn med en öppen xfce-session. Mer information finns i [Installera och konfigurera X2Go-klienten](dsvm-ubuntu-intro.md#x2go).
* Om du vill ha en smidigare rullnings upplevelse, går du till DSVM Firefox-webbläsare och växlar `gfx.xrender.enabled` flaggan i `about:config` . [Läs mer](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Överväg också att ställa in `mousewheel.enable_pixel_scrolling` på `False` . [Läs mer](https://support.mozilla.org/questions/981140).

## <a name="download-the-spambase-dataset"></a>Ladda ned spambase-datauppsättningen

[Spambase](https://archive.ics.uci.edu/ml/datasets/spambase) -datauppsättningen är en relativt liten uppsättning data som innehåller 4 601-exempel. Data uppsättningen är en bekväm storlek för att demonstrera några av de viktigaste funktionerna i DSVM eftersom den håller resurs kraven på en liten stund.

> [!NOTE]
> Den här genom gången har skapats med hjälp av en D2 v2-storlek Linux-DSVM (Ubuntu 18,04-utgåva). Du kan använda en DSVM för den här storleken för att slutföra de procedurer som visas i den här genom gången.

Om du behöver mer lagrings utrymme kan du skapa ytterligare diskar och koppla dem till din DSVM. Diskarna använder beständig Azure-lagring, så deras data bevaras även om servern har reserver ATS på grund av storleks ändring eller avstängning. Om du vill lägga till en disk och koppla den till din DSVM slutför du stegen i [lägga till en disk till en virtuell Linux-dator](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Stegen för att lägga till en disk använder Azure CLI, som redan är installerad på DSVM. Du kan slutföra stegen helt och hållet från själva DSVM. Ett annat alternativ för att öka lagrings utrymmet är att använda [Azure Files](../../storage/files/storage-how-to-use-files-linux.md).

Om du vill hämta data öppnar du ett terminalfönster och kör sedan det här kommandot:

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

Den hämtade filen har inte någon rubrik rad. Nu ska vi skapa en annan fil som har en rubrik. Kör det här kommandot för att skapa en fil med lämpliga huvuden:

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

Sammanfoga sedan de två filerna tillsammans:

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

Data uppsättningen har flera typer av statistik för varje e-post:

* Kolumner som **Word \_ FREQ \_ _Word_** anger procent andelen ord i e-postmeddelandet som matchar *Word*. Om **Word- \_ FREQ \_** till exempel är **1**, *gör* 1% av alla ord i e-postmeddelandet.
* Kolumner som **char \_ FREQ \_ _char_** anger procent andelen alla tecken i e-postmeddelandet som är *char*.
* **längd på stor begynnelse \_ \_ \_** är den längsta längden för en sekvens med versaler.
* **\_ \_ \_ genomsnittlig genomsnittlig längd på versaler** är den genomsnittliga längden för alla sekvenser med versaler.
* **Summa för kapital \_ körnings \_ längd \_** är den totala längden för alla sekvenser med versaler.
* **spam** anger om e-postmeddelandet ansågs vara skräp post eller inte (1 = spam, 0 = inte skräp post).

## <a name="explore-the-dataset-by-using-r-open"></a>Utforska data uppsättningen med R Open

Vi ska undersöka data och göra några grundläggande Machine Learning med hjälp av R. DSVM levereras med [Microsoft R Open](https://mran.revolutionanalytics.com/open/) förinstallerat. De flertrådiga matematiska biblioteken i den förinstallerade versionen av R ger bättre prestanda än versioner med en tråd. R Open tillhandahåller också reproducerbarhet genom en ögonblicks bild av CRAN-paketets lagrings plats.

Om du vill hämta kopior av kod exemplen som används i den här genom gången använder du git för att klona Azure-Machine-Learning-data-science-lagringsplatsen. Git förinstalleras på DSVM. På git-kommandoraden kör du:

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

Öppna ett terminalfönster och starta en ny R-session i den R interaktiva konsolen. Du kan också använda RStudio, som är förinstallerat på DSVM.

Så här importerar du data och konfigurerar miljön:

```R
data <- read.csv("spambaseHeaders.data")
set.seed(123)
```

Så här visar du sammanfattnings statistik om varje kolumn:

```R
summary(data)
```

För en annan vy av data:

```R
str(data)
```

I den här vyn visas typen av varje variabel och de första värdena i data uppsättningen.

Kolumnen **spam** lästes som ett heltal, men det är egentligen en kategoriska variabel (eller faktor). Så här anger du dess typ:

```R
data$spam <- as.factor(data$spam)
```

Om du vill göra en viss analys av analyser använder du [ggplot2](https://ggplot2.tidyverse.org/) -paketet, ett populärt diagram bibliotek för R som är förinstallerat på DSVM. Baserat på de sammanfattnings data som visas tidigare har vi sammanfattande statistik om hur ofta utrops tecknet är. Låt oss Rita dessa frekvenser här genom att köra följande kommandon:

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Eftersom noll fältet skevar ritningen tar vi bort det:

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Det finns en inte trivial densitet över 1 som ser intressant ut. Nu ska vi titta på endast dessa data:

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Dela sedan upp den med skräp post jämfört med Ham:

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

Dessa exempel bör hjälpa dig att göra liknande observationer och utforska data i de andra kolumnerna.

## <a name="train-and-test-a-machine-learning-model"></a>Träna och testa en maskin inlärnings modell

Vi ska träna ett par maskin inlärnings modeller för att klassificera e-postmeddelandena i data uppsättningen som innehåller antingen skräp post eller Ham. I det här avsnittet ska vi träna en besluts träd modell och en slumpmässig skogs modell. Sedan testar vi precisionen för förutsägelserna.

> [!NOTE]
> Paketet *rpart* (recursive partitioning and regression Tree) som används i följande kod är redan installerat på DSVM.

Först ska vi dela upp data uppsättningen i tränings uppsättningar och test uppsättningar:

```R
rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)
```

Skapa sedan ett besluts träd för att klassificera e-postmeddelanden:

```R
require(rpart)
model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
plot(model.rpart)
text(model.rpart)
```

Här är resultatet:

![Ett diagram över besluts trädet som skapas](./media/linux-dsvm-walkthrough/decision-tree.png)

Använd följande kod för att avgöra hur bra den fungerar på inlärnings uppsättningen:

```R
trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Så här tar du reda på hur bra den fungerar på test uppsättningen:

```R
testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Vi kan också prova en slumpmässig skogs modell. Slumpmässiga skogar tränar en mängd besluts träd och utvärderar en klass som är läget för klassificeringarna från alla enskilda besluts träd. De ger en mer kraftfull maskin inlärnings metod eftersom de passar för tendensen av en besluts träd modell för att overfit en tränings data uppsättning.

```R
require(randomForest)
trainVars <- setdiff(colnames(data), 'spam')
model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Guider för djup inlärning och genom gångar

Förutom de ramverkbaserade exemplen tillhandahålls även en uppsättning omfattande genom gångar. Med de här genom gången kan du komma igång med att utveckla djup inlärnings program i domäner som bild och text/språk förståelse.

- Att [köra neurala-nätverk i olika ramverk](https://github.com/ilkarman/DeepLearningFrameworks): en omfattande genom gång som visar hur du migrerar kod från ett ramverk till ett annat. Det visar också hur du jämför modell-och körnings prestanda i ramverk. 

- [En instruktions guide för att skapa en komplett lösning för att identifiera produkter i bilder](https://github.com/Azure/cortana-intelligence-product-detection-from-images): bild identifiering är en teknik som kan användas för att hitta och klassificera objekt i bilder. Tekniken har möjlighet att få enorma förmåner i många affärs domäner i real tid. Åter försäljare kan till exempel använda den här metoden för att avgöra vilken produkt en kund har hämtat från hyllan. Den här informationen i sin tur hjälper butiker att hantera produkt inventering. 

- [Djup inlärning för ljud](/archive/blogs/machinelearning/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure): den här självstudien visar hur du tränar en djup inlärnings modell för att upptäcka ljud händelser i [data uppsättningen för urbana ljud](https://urbansounddataset.weebly.com/). I självstudien får du en översikt över hur du arbetar med ljuddata.

- [Klassificering av text dokument](https://github.com/anargyri/lstm_han): den här genom gången visar hur du skapar och tränar två olika neurala-nätverks arkitekturer: hierarkiskt Attention-nätverk och långsiktigt kortvarigt minne (LSTM). Dessa neurala-nätverk använder keras-API: et för djup inlärning för att klassificera text dokument. Keras är en klient del till tre populära ramverk för djup inlärning: Microsoft Cognitive Toolkit, TensorFlow och Theano.

## <a name="other-tools"></a>Andra verktyg

I de återstående avsnitten får du se hur du använder några av de verktyg som är installerade på Linux-DSVM. Vi diskuterar följande verktyg:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL och SQuirreL SQL
* Azure Synapse Analytics (tidigare SQL DW)

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) tillhandahåller en snabb och korrekt utökat träd implementering.

```R
require(xgboost)
data <- read.csv("spambaseHeaders.data")
set.seed(123)

rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)

bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

pred <- predict(bst, data.matrix(testSet[, 0:57]))
accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
print(paste("test accuracy = ", accuracy))
```

XGBoost kan också anropa från python eller en kommando rad.

### <a name="python"></a>Python

För python-utveckling installeras Anaconda python-distributionerna 3,5 och 2,7 på DSVM.

> [!NOTE]
> Anaconda-distributionen innehåller [Conda](https://conda.pydata.org/docs/index.html). Du kan använda Conda för att skapa anpassade python-miljöer som har olika versioner eller paket installerade.

Vi läser i några av spambase-datauppsättningen och klassificerar e-postmeddelandena med stöd för vektor datorer i Scikit – lära:

```Python
import pandas
from sklearn import svm
data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
X = data.ix[:, 0:57]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

För att göra förutsägelser:

```Python
clf.predict(X.ix[0:20, :])
```

För att visa hur du publicerar en Azure Machine Learning slut punkt, ska vi skapa en grundläggande modell. Vi använder de tre variabler som vi använde när vi publicerade R-modellen tidigare:

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

### <a name="jupyterhub"></a>JupyterHub

Anaconda-distributionen i DSVM levereras med en Jupyter Notebook, en plattforms oberoende miljö för att dela python-, R-eller Julia-kod och-analys. Jupyter Notebook nås via JupyterHub. Du loggar in med ditt lokala Linux-användarnamn och lösen ord på https:// \<DSVM DNS name or IP address\> : 8000/. Alla konfigurationsfiler för JupyterHub finns i/etc/jupyterhub.

> [!NOTE]
> Om du vill använda python-paketfilen (via `pip` kommandot) från en Jupyter Notebook i den aktuella kärnan, använder du det här kommandot i cellen Code:
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Om du vill använda Conda-installationsprogrammet (via `conda` kommandot) från en Jupyter Notebook i den aktuella kärnan använder du det här kommandot i en kod cell:
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Flera exempel antecknings böcker är redan installerade på DSVM:

* Exempel på python-anteckningsboker:
  * [IntroToJupyterPython. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
* Exempel på R-anteckningsbok:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> Julia-språket är också tillgängligt från kommando raden på Linux-DSVM.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A* nalysdatamängd *t* OOL *t* o *L* s *E* asily) är ett grafiskt R-verktyg för Data utvinning. Rattle har ett intuitivt gränssnitt som gör det enkelt att läsa in, utforska och transformera data och bygga och utvärdera modeller. [Rattle: ett användar gränssnitt för Data utvinning för R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) innehåller en genom gång som visar Rattle-funktioner.

Installera och starta Rattle genom att köra följande kommandon:

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> Du behöver inte installera Rattle på DSVM. Du kan dock uppmanas att installera ytterligare paket när Rattle öppnas.

Rattle använder ett flik-baserat gränssnitt. De flesta flikarna motsvarar stegen i [team data science-processen](../team-data-science-process/index.yml), t. ex. inläsning av data eller utforska data. Data vetenskaps processen flödar från vänster till höger genom flikarna. Den sista fliken innehåller en logg med de R-kommandon som kördes av Rattle.

Så här läser du in och konfigurerar data uppsättningen:

1. Om du vill läsa in filen väljer du fliken **data** .
1. Välj väljaren bredvid **fil namn** och välj sedan **spambaseHeaders. data**.
1. För att läsa in filen. Välj **Kör**. Du bör se en sammanfattning av varje kolumn, inklusive den identifierade data typen. om det är en indatatyp, ett mål eller en annan typ av variabel. och antalet unika värden.
1. Rattle har identifierat kolumnen **spam** som mål. Välj kolumnen **spam** och ange sedan **mål data typen** till **Categoric**.

För att utforska data:

1. Välj fliken **utforska** .
1. Om du vill se information om variabel typer och viss sammanfattnings statistik väljer du **sammanfattnings**  >  **körning**.
1. Om du vill visa andra typer av statistik om varje variabel väljer du andra alternativ, t. ex. **Beskriv** eller **grunderna**.

Du kan också använda fliken **utforska** för att generera inblickade områden. För att rita ett histogram med data:

1. Välj **distributioner**.
1. För **word_freq_remove** och **Word_freq_you** väljer du **histogram**.
1. Välj **Kör**. Du bör se båda Täthets ritningarna i ett enda diagram fönster, där det är klart att ordet _du_ ser mycket oftare i e-postmeddelanden än _ta bort_.

**Korrelations** områdena är också intressanta. Så här skapar du en rityta:

1. I **typ** väljer du **korrelation**.
1. Välj **Kör**.
1. Rattle varnar dig om att det rekommenderar högst 40 variabler. Välj **Ja** om du vill visa ritningen.

Det finns några intressanta korrelationer som följer: _teknik_ är starkt korrelerad med _HP_ och _labb_, till exempel. Det är också starkt korrelerat med _650_ eftersom rikt numret för data uppsättnings givarna är 650.

De numeriska värdena för korrelationer mellan ord är tillgängliga i fönstret **utforska** . Det är intressant att notera, till exempel att _tekniken_ är negativ korrelerad med _dina_ och _pengar_.

Rattle kan transformera data uppsättningen för att hantera några vanliga problem. Till exempel kan den skala om funktioner, räkna upp saknade värden, hantera avvikande värden och ta bort variabler eller observationer som saknar data. Rattle kan också identifiera associerings regler mellan observationer och variabler. De här flikarna beskrivs inte i den här inledande genom gången.

Rattle kan också köra kluster analys. Vi kan utesluta vissa funktioner för att göra utdata lättare att läsa. På fliken **data** väljer du **Ignorera** bredvid var och en av variablerna förutom de 10 objekten:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* skräppost

Gå tillbaka till fliken **kluster** . Välj **KMeans** och ange sedan **antal kluster** till **4**. Välj **Kör**. Resultaten visas i fönstret utdata. Ett kluster har hög frekvens på _George_ och _HP_ och är förmodligen ett legitimt företags-e-postmeddelande.

Så här skapar du en grundläggande besluts träd Machine Learning-modell:

1. Välj fliken **modell** ,
1. I **typ** väljer du **träd**.
1. Välj **Kör** för att visa trädet i text form i fönstret utdata.
1. Välj knappen **Rita** om du vill visa en grafisk version. Besluts trädet liknar det träd vi fick tidigare genom att använda rpart.

En användbar funktion i Rattle är möjligheten att köra flera Machine Learning-metoder och snabbt utvärdera dem. Här är stegen:

1. För **typ** väljer du **alla**.
1. Välj **Kör**.
1. När Rattle är klar kan du välja valfritt **typ** värde, t. ex. **SVM**, och visa resultatet.
1. Du kan också jämföra modellens prestanda på validerings uppsättningen med hjälp av fliken **utvärdera** . Till exempel visar **fel mat** ris urvalet visualiserings mat ris, övergripande fel och genomsnittligt klass fel för varje modell i validerings uppsättningen. Du kan också Rita ROC kurvor, köra känslighets analyser och utföra andra typer av modell utvärderingar.

När du är klar med att skapa modeller väljer du fliken **logg** för att visa den R-kod som kördes av Rattle under sessionen. Du kan välja knappen **Exportera** för att spara den.

> [!NOTE]
> Den aktuella versionen av Rattle innehåller en bugg. Om du vill ändra skriptet eller använda det för att upprepa stegen senare, måste du infoga ett **#** tecken framför *Exportera loggen...* i logg filens text.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL och SQuirreL SQL

DSVM levereras med PostgreSQL installerat. PostgreSQL är en avancerad Relations databas med öppen källkod. I det här avsnittet visas hur du läser in spambase-datauppsättningen i PostgreSQL och sedan frågar den.

Innan du kan läsa in data måste du tillåta lösenordsautentisering från localhost. Kör följande i en kommandotolk:

```Bash
sudo gedit /var/lib/pgsql/data/pg_hba.conf
```

Nästan längst ned i konfigurations filen finns flera rader som beskriver de tillåtna anslutningarna:

```
# "local" is only for Unix domain socket connections:
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```

Ändra linjen för **IPv4-lokala anslutningar** så att den använder **MD5** i stället för **identitet**, så att vi kan logga in med ett användar namn och lösen ord:

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

Starta sedan om PostgreSQL-tjänsten:

```Bash
sudo systemctl restart postgresql
```

Kör det här kommandot om du vill starta *psql* (en interaktiv Terminal för postgresql) som den inbyggda postgres-användaren:

```Bash
sudo -u postgres psql
```

Skapa ett nytt användar konto med hjälp av användar namnet för det Linux-konto som du använde för att logga in. Skapa ett lösen ord:

```Bash
CREATE USER <username> WITH CREATEDB;
CREATE DATABASE <username>;
ALTER USER <username> password '<password>';
\quit
```

Logga in på psql:

```Bash
psql
```

Importera data till en ny databas:

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

Nu ska vi utforska data och köra några frågor med hjälp av SQuirreL SQL, ett grafiskt verktyg som du kan använda för att interagera med databaser via en JDBC-drivrutin.

Kom igång genom att öppna SQuirreL SQL på **program** -menyn. Så här konfigurerar du driv rutinen:

1. Välj **Windows**  >  **View-drivrutiner**.
1. Högerklicka på **postgresql** och välj **ändra driv rutin**.
1. Välj **extra klass Sök väg**  >  **Lägg till**.
1. För **fil namn** anger du **/usr/share/Java/jdbcdrivers/postgresql-9.4.1208.jre6.jar**.
1. Välj **Öppna**.
1. Välj **list driv rutiner**. För **klass namn** väljer du **org. postgresql. driver** och väljer sedan **OK**.

Så här konfigurerar du anslutningen till den lokala servern:

1. Välj alias för **Windows**-  >  **vy.**
1. Välj **+** knappen för att skapa ett nytt alias. För det nya aliasnamnet anger du **spam-databasen**. 
1. För **driv rutin** väljer du **postgresql**.
1. Ange URL: en till **JDBC: postgresql://localhost/spam**.
1. Ange ditt användarnamn och lösenord.
1. Välj **OK**.
1. Öppna **anslutnings** fönstret **genom att dubbelklicka på det.**
1. Välj **Anslut**.

Så här kör du några frågor:

1. Välj fliken **SQL** .
1. I rutan fråga högst upp på fliken **SQL** anger du en grundläggande fråga som `SELECT * from data;` .
1. Tryck på CTRL + RETUR för att köra frågan. Som standard returnerar SQuirreL SQL de första 100 raderna från frågan.

Det finns många fler frågor som du kan köra för att utforska dessa data. Hur *skiljer sig* ordet ofta mellan skräp post och Ham?

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

Eller vilka egenskaper för e-post som ofta innehåller *3D*?

```SQL
SELECT * from data order by word_freq_3d desc;
```

De flesta e-postmeddelanden som har en stor förekomst av *3D* kan vara skräp post. Den här informationen kan vara användbar för att skapa en förutsägelse modell för att klassificera e-postmeddelanden.

Om du vill göra Machine Learning med hjälp av data som lagras i en PostgreSQL-databas bör du överväga att använda [MADlib](https://madlib.incubator.apache.org/).

### <a name="azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics (tidigare SQL DW)

Azure Synapse Analytics är en molnbaserad, skalbar databas som kan bearbeta stora mängder data, både relationella och icke-relationella. Mer information finns i [Vad är Azure Synapse Analytics?](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Anslut till data lagret och skapa tabellen genom att köra följande kommando från en kommando tolk:

```Bash
sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I
```

Kör följande kommando i SQLCMD-prompten:

```SQL
CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
GO
```

Kopiera data med hjälp av BCP:

```bash
bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"
```

> [!NOTE]
> Den hämtade filen innehåller rad slut i Windows-stil. BCP-verktyget förväntar sig rad brytningar i UNIX-format. Använd flaggan-r för att meddela BCP.

Fråga sedan med hjälp av SQLCMD:

```sql
select top 10 spam, char_freq_dollar from spam;
GO
```

Du kan också fråga genom att använda SQuirreL SQL. Följ stegen som liknar PostgreSQL med driv rutinen SQL Server JDBC. JDBC-drivrutinen finns i mappen/usr/share/Java/jdbcdrivers/sqljdbc42.jar.