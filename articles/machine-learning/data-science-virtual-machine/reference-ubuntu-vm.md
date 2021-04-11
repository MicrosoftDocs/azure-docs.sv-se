---
title: 'Referens: Ubuntu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Information om verktyg som ingår i Ubuntu-Data Science Virtual Machine
author: gvashishtha
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: e9a55f72718d6ed5991f3d0f16323409bb0f699f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "101661077"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Referens: Ubuntu (Linux) Data Science Virtual Machine

Nedan visas en lista över tillgängliga verktyg på din Ubuntu Data Science Virtual Machine. 

## <a name="deep-learning-libraries"></a>Djup inlärnings bibliotek

### <a name="cntk"></a>CNTK

Microsoft Cognitive Toolkit är ett djup inlärnings verktyg med öppen källkod. Python-bindningar är tillgängliga i rot-och py35 Conda-miljöerna. Det har också ett kommando rads verktyg (CNTK) som redan finns i sökvägen.

Exempel på python-anteckningsböcker finns i JupyterHub. Kör följande kommandon i gränssnittet för att köra ett grundläggande exempel på kommando raden:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Mer information finns i avsnittet CNTK i [GitHub](https://github.com/Microsoft/CNTK) och [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

### <a name="caffe"></a>Caffe

Caffe är ett djup inlärnings ramverk från Berkeley Vision and Learning Center. Den finns i/opt/Caffe. Du kan hitta exempel i/opt/Caffe/examples.

### <a name="caffe2"></a>Caffe2

Caffe2 är ett djup inlärnings ramverk från Facebook som bygger på Caffe. Den finns i python 2,7 i rot miljön Conda. Om du vill aktivera den kör du följande kommando från gränssnittet:

```bash
source /anaconda/bin/activate root
```

Några exempel på antecknings böcker finns i JupyterHub.

### <a name="h2o"></a>H2O

H2O är en snabb, intern, distribuerad maskin inlärnings plattform och en förutsägelse analys plattform. Ett python-paket är installerat i både rot-och py35 Anaconda-miljöerna. Ett R-paket installeras också. 

Öppna H2O från kommando raden genom att köra `java -jar /dsvm/tools/h2o/current/h2o.jar` . Det finns olika [kommando rads alternativ](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) som du kanske vill konfigurera. Du kan komma åt Flow-webbgränssnittet genom att bläddra till `http://localhost:54321` för att komma igång. Exempel på antecknings böcker finns också i JupyterHub.

### <a name="keras"></a>Keras

Keras är ett neurala nätverks-API med hög nivå i python. Den kan köras ovanpå TensorFlow, Microsoft Cognitive Toolkit eller Theano. Det finns i rot-och py35 python-miljö.

### <a name="mxnet"></a>MXNet

MXNet är ett djup inlärnings ramverk som har utformats för både effektivitet och flexibilitet. Den har R-och Python-bindningar som ingår i DSVM. Exempel på bärbara datorer ingår i JupyterHub och exempel kod finns i/dsvm/samples/mxnet.

### <a name="nvidia-digits"></a>NVIDIA-SIFFROR

GPU-inlärnings systemet för NVIDIA djup inlärning, som kallas siffror, är ett system för att förenkla vanliga djup inlärnings uppgifter. Dessa uppgifter omfattar hantering av data, utformning och träning av neurala-nätverk på GPU-system och övervakning av prestanda i real tid med avancerad visualisering.

SIFFROR är tillgängligt som en tjänst som kallas *siffror*. Starta tjänsten och bläddra till `http://localhost:5000` för att komma igång.

SIFFROR installeras också som en python-modul i Conda-rot miljön.

### <a name="tensorflow"></a>TensorFlow

TensorFlow är Googles djup inlärnings bibliotek. Det är ett program bibliotek med öppen källkod för numerisk beräkning med data flödes diagram. TensorFlow finns i py35 python-miljön och vissa exempel antecknings böcker ingår i JupyterHub.

### <a name="theano"></a>Theano

Theano är ett Python-bibliotek för effektiv Numerisk beräkning. Det finns i rot-och py35 python-miljö. 

### <a name="torch"></a>Torch

Torch är ett ramverk för vetenskaplig data behandling med bred support för Machine Learning-algoritmer. Den är tillgänglig i/dsvm/tools/Torch **och den interaktiva** sessionen och LuaRocks Package Manager finns på kommando raden. Exempel finns i/dsvm/samples/Torch.

PyTorch är också tillgängligt i rot Anacondas miljön. Exempel finns i/dsvm/samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

R är ett av de mest populära språken för data analys och maskin inlärning. Om du vill använda R för din analys har den virtuella datorn Microsoft Machine Learning Server med Microsoft R Open och Math kernel-biblioteket. Matematiska kernel-bibliotek optimerar matematiska åtgärder som är vanliga i analytiska algoritmer. Microsoft R Open är 100 procent kompatibel med CRAN R och alla R-bibliotek som publiceras i CRAN kan installeras på Microsoft R Open. 

Machine Learning Server ger dig skalning och driftsättning av R-modeller till webb tjänster. Du kan redigera R-programmen i någon av standard redigerare, t. ex. RStudio, vi eller emacs. Om du föredrar att använda emacs-redigeraren har den förinstallerats. Paketet emacs ESS (emacs pratar Statistics) fören klar arbetet med R-filer i emacs-redigeraren.

Om du vill öppna R-konsolen anger du **r** i gränssnittet. Det här kommandot tar dig till en interaktiv miljö. För att utveckla R-programmet använder du vanligt vis ett redigerings program som emacs eller vi och kör sedan skripten i R. Med RStudio har du en fullständig grafisk IDE för att utveckla R-programmet.

Det finns också ett R-skript som du kan använda för att installera de [högsta 20 R-paketen](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) om du vill. Du kan köra det här skriptet när du är i det interaktiva R-gränssnittet. Som tidigare nämnts kan du öppna gränssnittet genom att ange **R** i gränssnittet.  

## <a name="python"></a>Python

Anaconda python installeras med python 2,7 och 3,5-miljöer. 2,7-miljön kallas _rot_ och 3,5-miljön kallas _py35_. Den här distributionen innehåller bas python tillsammans med cirka 300 av de mest populära matematik-, teknik-och data analys paketen.

Py35-miljön är standard. Använd följande kommando för att aktivera rot miljön (2,7):

```bash
source activate root
```

Använd följande kommando för att aktivera py35-miljön igen:

```bash
source activate py35
```

Om du vill anropa en python-interaktiv session anger du **python** i gränssnittet. 

Installera ytterligare python-bibliotek med Conda eller pip. För pip aktiverar du rätt miljö först om du inte vill använda standardvärdet:

```bash
source activate root
pip install <package>
```

Eller ange den fullständiga sökvägen till pip:

```bash
/anaconda/bin/pip install <package>
```

För Conda ska du alltid ange miljö namnet (py35 eller root):

```bash
conda install <package> -n py35
```

Om du har ett grafiskt gränssnitt eller har konfigurerat begäran om x11-vidarebefordran kan du ange **pycharm med** för att öppna PYCHARM med python IDE. Du kan använda standard text redigerare. Dessutom kan du använda Spyder, en python IDE som är paketerad med Anaconda python-distributioner. Spyder kräver en grafisk Skriv bords-eller begäran om x11-vidarebefordran. Det grafiska Skriv bordet har en genväg till Spyder.

## <a name="jupyter-notebook"></a>Jupyter Notebook

Anaconda-distributionen levereras också med en Jupyter Notebook, en miljö för att dela kod och analys. Jupyter Notebook nås via JupyterHub. Du loggar in med ditt lokala Linux-användarnamn och lösen ord.

Jupyter Notebook-servern har förkonfigurerats med python 2, python 3 och R-kernel. Använd ikonen **Jupyter Notebook** skriv bord för att öppna webbläsaren och få åtkomst till Notebook-servern. Om du befinner dig på den virtuella datorn via SSH eller X2Go-klienten, kan du också komma åt Jupyter Notebook-servern på `https://localhost:8000/` .

> [!NOTE]
> Fortsätt om du får några certifikat varningar.

Du kan komma åt Jupyter Notebook-servern från vilken värd som helst. Ange **https:// \<VM DNS name or IP address\> : 8000/**.

> [!NOTE]
> Port 8000 öppnas i brand väggen som standard när den virtuella datorn är etablerad. 

Vi har paketerade exempel antecknings böcker – en i python och en i R. Du kan se länken till exemplen på hem sidan för antecknings boken när du har autentiserat dig för Jupyter Notebook genom att använda ditt lokala Linux-användarnamn och lösen ord. Du kan skapa en ny antecknings bok genom att välja **ny** och sedan välja den aktuella språk kärnan. Om du inte ser knappen **nytt** väljer du ikonen **Jupyter** längst upp till vänster för att gå till hem sidan för notebook-servern.

## <a name="apache-spark-standalone"></a>Fristående Apache Spark

En fristående instans av Apache Spark förinstalleras på Linux-DSVM för att hjälpa dig att utveckla Spark-program lokalt innan du testar och distribuerar dem i stora kluster. 

Du kan köra PySpark-program via Jupyter-kärnan. När du öppnar Jupyter väljer du knappen **nytt** och du bör se en lista över tillgängliga kernels. **Spark – python** är PySpark-kärnan som gör att du kan bygga Spark-program med hjälp av python-språket. Du kan också använda en python IDE som pycharm med eller Spyder för att bygga Spark-programmet. 

I den här fristående instansen körs Spark-stacken i det anropande klient programmet. Den här funktionen gör det snabbare och enklare att felsöka problem, jämfört med att utveckla i ett Spark-kluster.

Jupyter tillhandahåller en exempel PySpark Notebook. Du hittar den i katalogen SparkML under arbets katalogen för Jupyter ($HOME/notebooks/SparkML/pySpark). 

Om du använder programmering i R för Spark kan du använda Microsoft Machine Learning Server, sparker eller sparklyr. 

Innan du kör i en spark-kontext i Microsoft Machine Learning Server måste du göra ett engångs steg för att aktivera en lokal instans av Hadoop HDFS och garn som är en enskild nod. Hadoop-tjänster installeras som standard men inaktive ras på DSVM. Om du vill aktivera det måste du köra följande kommandon som rot första gången:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Du kan stoppa de Hadoop-relaterade tjänsterna när du inte behöver dem genom att köra ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` .

/Dsvm/samples/MRS-katalogen innehåller ett exempel som visar hur du utvecklar och testar Microsoft Machine Learning Server i en fjärr Spark-kontext (den fristående Spark-instansen på DSVM).

## <a name="ides-and-editors"></a>IDE: er och redigerare

Du kan välja mellan flera kod redigerare, inklusive vi/vim, emacs, pycharm med, RStudio och IntelliJ. 

Pycharm med, RStudio och IntelliJ är grafiska redigerings program. Om du vill använda dem måste du vara inloggad på ett grafiskt skriv bord. Du öppnar dem med hjälp av meny gen vägar för Skriv bordet och program.

Vim och emacs är textbaserade redigerare. I emacs gör ESS-tilläggs paketet arbetet med R enklare i emacs-redigeraren. Du hittar mer information på ess- [webbplatsen](https://ess.r-project.org/).

LaTex installeras via texlive-paketet, tillsammans med ett Emacs tilläggs paket som kallas [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Det här paketet fören klar redigeringen av dina LaTex-dokument i emacs.  

## <a name="databases"></a>Databaser

### <a name="graphical-sql-client"></a>Grafisk SQL-klient

SQuirrel SQL, en grafisk SQL-klient, kan ansluta till olika databaser (till exempel Microsoft SQL Server och MySQL) och köra SQL-frågor. Du kan köra SQuirrel SQL från en grafisk fjärrskrivbordssession (till exempel via X2Go-klienten) genom att använda en Skriv bords ikon. Eller så kan du köra-klienten med hjälp av följande kommando i gränssnittet:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Innan du börjar använda konfigurerar du dina driv rutiner och databas Ali Aset. JDBC-drivrutinerna finns på/usr/share/Java/jdbcdrivers.

Mer information finns i [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Kommando rads verktyg för att komma åt Microsoft SQL Server

ODBC driv rutins paketet för SQL Server levereras också med två kommando rads verktyg:

- **BCP**: BCP-verktyget kopierar data mellan en instans av Microsoft SQL Server och en datafil i ett användardefinierat format. Du kan använda BCP-verktyget för att importera ett stort antal nya rader till SQL Server tabeller eller för att exportera data från tabeller till datafiler. Om du vill importera data till en tabell måste du använda en format fil som skapats för tabellen. Eller så måste du förstå tabell strukturen och vilka typer av data som är giltiga för kolumnerna.

  Mer information finns i [ansluta med BCP](/sql/connect/odbc/linux-mac/connecting-with-bcp).

- **SQLCMD**: du kan ange Transact-SQL-uttryck med hjälp av SQLCMD-verktyget. Du kan också ange system procedurer och skriptfiler i kommando tolken. Verktyget använder ODBC för att köra Transact-SQL-batchar.

  Mer information finns i [ansluta till SQLCMD](/sql/connect/odbc/linux-mac/connecting-with-sqlcmd).

  > [!NOTE]
  > Det finns vissa skillnader i det här verktyget mellan Linux-och Windows-plattformar. Mer information finns i dokumentationen.

### <a name="database-access-libraries"></a>Bibliotek för databas åtkomst

Bibliotek är tillgängliga i R och python för databas åtkomst:

* I R kan du använda RODBC-paketet eller dplyr-paketet för att fråga eller köra SQL-uttryck på databas servern.
* I python ger pyodbc-biblioteket databas åtkomst med ODBC som underliggande lager.  

## <a name="azure-tools"></a>Azure-verktyg

Följande Azure-verktyg är installerade på den virtuella datorn:

* **Azure CLI**: du kan använda kommando rads gränssnittet i Azure för att skapa och hantera Azure-resurser via Shell-kommandon. Öppna Azure-verktygen genom att ange **Azure-hjälpen**. Mer information finns på sidan med [Azure CLI-dokumentation](/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer**: Azure Storage Explorer är ett grafiskt verktyg som du kan använda för att bläddra bland de objekt som du har lagrat på ditt Azure Storage-konto och för att ladda upp och ladda ned data till och från Azure-blobbar. Du kan komma åt Storage Explorer från Skriv bords gen vägs ikonen. Du kan också öppna den från en Shell-prompt genom att ange **StorageExplorer**. Du måste vara inloggad från en X2Go-klient eller konfigurera begäran om x11-vidarebefordring.
* **Azure-bibliotek**: följande är några av de förinstallerade biblioteken.
  
  * **Python**: Azure-relaterade bibliotek i python är *Azure*, *azureml*, *pydocumentdb* och *pyodbc*. Med de tre första biblioteken kan du komma åt Azure Storage-tjänster, Azure Machine Learning och Azure Cosmos DB (en NoSQL-databas på Azure). Det fjärde biblioteket, pyodbc (tillsammans med Microsoft ODBC-drivrutinen för SQL Server), ger åtkomst till SQL Server, Azure SQL Database och Azure Synapse-analys från python med hjälp av ett ODBC-gränssnitt. Ange **pip-listan** för att visa alla bibliotek i listan. Se till att köra det här kommandot i både python 2,7-och 3,5-miljöerna.
  * **R**: Azure-relaterade bibliotek i R är AZUREML och RODBC.
  * **Java**: listan med Azure Java-bibliotek finns i katalogen/dsvm/SDK/AzureSDKJava på den virtuella datorn. Nyckel biblioteken är API: er för Azure Storage och hantering, Azure Cosmos DB och JDBC driv rutiner för SQL Server.  

Du kan komma åt [Azure Portal](https://portal.azure.com) från den förinstallerade Firefox-webbläsaren. På Azure Portal kan du skapa, hantera och övervaka Azure-resurser.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning är en helt hanterad moln tjänst som gör att du kan bygga, distribuera och dela lösningar för förutsägelse analys. Du kan bygga experiment och modeller i Azure Machine Learning Studio (för hands version). Du kan komma åt den från en webbläsare på Data Science Virtual Machine genom att besöka [Microsoft Azure Machine Learning](https://ml.azure.com).

När du har loggat in på Azure Machine Learning Studio kan du använda en experiment arbets yta för att bygga ett logiskt flöde för Machine Learning-algoritmer. Du har också till gång till en Jupyter-anteckningsbok som finns på Azure Machine Learning och kan arbeta sömlöst med experimenten i Azure Machine Learning Studio. 

Operationalisera de maskin inlärnings modeller som du har skapat genom att figursätta dem i ett webb tjänst gränssnitt. Genom att använda Machine Learning-modeller kan klienter skrivna på valfritt språk för att anropa förutsägelser från dessa modeller. Mer information finns i Machine Learning- [dokumentationen](https://azure.microsoft.com/documentation/services/machine-learning/).

Du kan också bygga dina modeller i R eller python på den virtuella datorn och sedan distribuera dem i produktion på Azure Machine Learning. Vi har installerat bibliotek i R (**azureml**) och python (**azureml**) för att aktivera den här funktionen.

> [!NOTE]
> Dessa instruktioner skrevs för Windows-versionen av Data Science Virtual Machine. Men informationen som ges vid distribution av modeller till Azure Machine Learning är tillämplig på den virtuella Linux-datorn.

## <a name="machine-learning-tools"></a>Machine Learning-verktyg

Den virtuella datorn levereras med Machine Learning-verktyg och algoritmer som har förkompilerats och redan installerats lokalt. Dessa omfattar:

* **Vowpal Wabbit**: en snabb onlineutbildning för inlärning.
* **xgboost**: ett verktyg som tillhandahåller optimerade, förstärkta träd algoritmer.
* **Rattle**: ett R-baserat grafiskt verktyg för enkel utforskning och modellering av data.
* **Python**: Anaconda python kommer att paketeras med Machine Learning-algoritmer med bibliotek som Scikit-lär. Du kan installera andra bibliotek med hjälp av `pip install` kommandot.
* **LightGBM**: ett snabb, distribuerat, högpresterande tonings ramverk som bygger på algoritmer för besluts träd.
* **R**: ett omfattande bibliotek med Machine Learning-funktioner är tillgängligt för R. förinstallerade bibliotek är lm, GLM, randomForest och rpart. Du kan installera andra bibliotek genom att köra det här kommandot:

    ```r
    install.packages(<lib name>)
    ```

Här är ytterligare information om de tre första verktygen för maskin inlärning i listan.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit är ett maskin inlärnings system som använder tekniker som online, hashing, allreduce, reduktioner, learning2search, aktiv och interaktiv utbildning.

Om du vill köra verktyget på ett Basic-exempel använder du följande kommandon:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Det finns andra, större demonstrationer i den katalogen. Mer information om Vowpal-Wabbit finns i  [det här avsnittet av GitHub](https://github.com/JohnLangford/vowpal_wabbit) och [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>xgboost

Xgboost-biblioteket är utformat och optimerat för förstärkta algoritmer (Tree). Syftet med det här biblioteket är att skicka datorernas beräknings gränser till de extrem värden som krävs för att skapa storskaliga träd förstärkningar som är skalbara, bärbara och exakta.

Den tillhandahålls som en kommando rad och ett R-bibliotek. Om du vill använda det här biblioteket i R kan du starta en interaktiv R-session (genom att ange **R** i gränssnittet) och läsa in biblioteket.

Här är ett enkelt exempel som du kan köra i en R-prompt:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

Följande kommandon körs i gränssnittet för att köra xgboost-kommando raden:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

En modell fil skrivs till den angivna katalogen. Du kan hitta information om det här demonstrations exemplet [på GitHub](https://github.com/dmlc/xgboost/tree/master/demo/CLI/binary_classification).

Mer information om xgboost finns på [dokumentations sidan för xgboost](https://xgboost.readthedocs.org/en/latest/) och dess [GitHub-lagringsplats](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle ( **R** **A** nalysdatamängd **t** OOL **t** o **L** s **E** asily) använder GUI-baserad data utforskning och modellering. Den visar statistiska och visuella sammanfattningar av data, transformerar data som kan modelleras, skapar både oövervakade och övervakade modeller från data, visar prestanda för modeller grafiskt och ger nya data uppsättningar. Den genererar också R-kod och replikerar åtgärderna i användar gränssnittet som kan köras direkt i R eller används som en start punkt för ytterligare analys.

Om du vill köra Rattle måste du vara i en grafisk Skriv bords inloggnings session. Öppna R-miljön genom att ange **r** i terminalen. Ange följande kommandon vid R-prompten:

```R
library(rattle)
rattle()
```

Nu öppnas ett grafiskt gränssnitt med en uppsättning flikar. Använd följande snabb starts steg i Rattle för att använda en exempel data uppsättning för väder och skapa en modell. I vissa av stegen uppmanas du att automatiskt installera och läsa in vissa nödvändiga R-paket som inte redan finns i systemet.

> [!NOTE]
> Om du inte har åtkomst för att installera paketet i system katalogen (standard) kan du se en prompt i R-konsolens fönster för att installera paket i ditt personliga bibliotek. Svara **y** om du ser dessa frågor.

1. Välj **Kör**.
1. En dialog ruta visas där du tillfrågas om du vill använda exempel data uppsättningen för väder. Välj **Ja** om du vill läsa in exemplet.
1. Välj fliken **modell** .
1. Välj **Kör** för att bygga ett besluts träd.
1. Välj **rit** för att Visa besluts trädet.
1. Välj alternativet **skog** och välj **Kör** för att bygga en slumpmässig skog.
1. Välj fliken **utvärdera** .
1. Välj alternativet **risk** och välj **Kör** för att visa två **riskhanterings (kumulativa)** prestanda observationer.
1. Välj fliken **logg** för att visa den genererade R-koden för föregående åtgärder.
   (På grund av ett fel i den aktuella versionen av Rattle måste du infoga ett **#** tecken framför **Exportera loggen** i texten i loggen.)
1. Välj knappen **Exportera** för att spara R-skript filen med namnet *weather_script. R* till arbetsmappen.

Du kan avsluta Rattle och R. Nu kan du ändra det genererade R-skriptet. Du kan också använda skriptet som det är och köra det när som helst för att upprepa allt som har gjorts i Rattle-ANVÄNDARGRÄNSSNITTET. I synnerhet för nybörjare i R är detta ett sätt att snabbt utföra analyser och maskin inlärning i ett enkelt grafiskt gränssnitt, medan automatiskt genererar kod i R för att ändra eller lära sig.

## <a name="next-steps"></a>Nästa steg

Har du fler frågor? Överväg att skapa ett [support ärende](https://azure.microsoft.com/support/create-ticket/).