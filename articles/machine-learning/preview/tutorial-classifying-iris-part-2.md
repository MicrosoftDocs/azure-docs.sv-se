---
title: "Skapa en modell för Azure Machine Learning (förhandsversion) | Microsoft Docs"
description: "I den här kompletta självstudien får du lära dig att använda Azure Machine Learning (förhandsversion) från slutpunkt till slutpunkt. Det här är del 2 om experimentering."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f1f270b95a60e847ce7d29c2d7d60c57891924cc
ms.contentlocale: sv-se
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-2-build-a-model"></a>Klassificera Iris del 2: skapa en modell
Azure Machine Learning (förhandsversion) är en integrerad, avancerad lösning för datavetenskap och analys, som datatekniker kan använda till att förbereda data, utveckla experiment och distribuera modeller i molnskala.

Den här självstudien är del två i en serie med tre delar. I den här delen av självstudien använder du Azure Machine Learning (förhandsversion) till att lära dig att:

> [!div class="checklist"]
> * Arbeta i Azure Machine Learning Workbench
> * Öppna skript och granskar kod
> * Köra skript i en lokal miljö
> * Granska körningshistorik
> * Köra skript i en lokal Docker-miljö
> * Köra skript i ett lokalt Azure CLI-fönster
> * Köra skript i en fjärransluten Docker-miljö
> * Köra skript i en HDInsight-molnmiljö

I den här självstudien gör vi det enkelt för oss och använder den tidlösa [Iris-datauppsättningen](https://en.wikipedia.org/wiki/Iris_flower_data_set). Skärmbilderna är Windows-specifika, men upplevelsen är nästan identisk i macOS.

## <a name="prerequisites"></a>Krav
Du bör först gå igenom del ett i den här självstudieserien. Gå igenom [självstudien Förbered data](tutorial-classifying-iris-part-1.md), där du får skapa Azure Machine Learning-resurser och installera Azure Machine Learning Workbench, innan du börjar med stegen i den här självstudien.

## <a name="review-irissklearnpy-and-configuration-files"></a>Granska iris_sklearn.py och konfigurationsfilerna
1. Starta **Azure Machine Learning Workbench** och öppna projektet **myIris** som du skapade i den förra delen av självstudieserien.

2. När projektet är öppet klickar du på knappen **Filer** (mappikon) i det vänstra verktygsfältet i Azure Machine Learning Workbench, så att du öppnar fillistan i projektmappen.

3. Om du markerar filen **iris_sklearn.py** viss Python-koden i en ny textredigeringsflik i Workbench.

   ![öppna fil](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >Koden du ser kanske inte är exakt samma som föregående kod, eftersom det här exempelprojektet uppdateras ofta.

4. Granska Python-skriptkoden så att du bekantar dig med kodningsstilen. Lägg märke till att skriptet utför följande uppgifter:

   - Databearbetningspaketet **iris.dprep** läses in för att skapa en [pandas-DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

        >[!NOTE]
        >Vi använder databearbetningspaketet `iris.dprep` som finns med i exempelprojektet. Det ska vara likadant som filen `iris-1.dprep` du skapade i del 1 av självstudien.

   - Slumpvis utvalda funktioner läggs till som gör det svårare att lösa problemet. (Slumpmässigheten behövs eftersom Iris är en liten datamängd som enkelt kan klassificeras med nästan 100 % noggrannhet.)

   - Machine Learning-biblioteket [scikit-learn](http://scikit-learn.org/stable/index.html) används till att skapa en enkel Logistic Regression modell. 

   - Modellen serialiseras med biblioteket [pickle](https://docs.python.org/2/library/pickle.html) till en fil i mappen `outputs`. Sedan läses den in och avserialiseras tillbaka till minnet.

   - Den avserialiserade modellen används till att göra en förutsägelse för en ny post. 

   - Två diagram ritas upp med hjälp av biblioteket [matplotlib](https://matplotlib.org/), en felmatris och en ROC-kurva med flera klasser, och de sparas i mappen `outputs`.

   - Objektet `run_logger` används kontinuerligt till att lagra regulariseringshastigheten och modellnoggrannheten i loggar, och loggarna plottas automatiskt i körhistoriken.


## <a name="execute-irissklearnpy-script-in-local-environment"></a>Köra skriptet iris_sklearn.py i en lokal miljö

Nu ska vi förbereda oss för att köra skriptet **iris_sklearn.py** för första gången. För skriptet krävs paketen scikit-learn och matplotlib. **scikit-learn** är redan installerat via Azure ML Workbench. Vi behöver däremot installera **matplotlib**. 

1. Klicka på menyn **Arkiv** i Azure Machine Learning Workbench och välj **Öppna kommandotolken** så att kommandotolken startar. Vi refererar till det här kommandoradsfönstret som CLI-fönstret för Azure Machine Learning Workbench, eller bara CLI-fönstret.

2. Skriv följande kommando i CLI-fönstret för att installera Python-paketet **matplotlib**. Det bör vara klart på mindre än en minut.

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >Om du hoppar över kommandot `pip install` ovan kommer det fortfarande gå att köra koden i `iris_sklearn.py`, men du kommer inte att se felmatrisen eller ROC-kurvan för flera klasser i visualiseringen av historiken.

3. Gå tillbaka till Workbench-fönstret. 

4. I det övre vänstra hörnet av fliken **iris_sklearn.py**, bredvid ikonen Spara, klickar du på listrutan och väljer **Kör konfiguration**.  Välj **lokal** som körningsmiljö och `iris_sklearn.py` som skriptet att köra.

5. Flytta sedan åt höger på samma flik och fyll i värdet `0.01` i fältet **Argument**. 

   ![bild](media/tutorial-classifying-iris/run_control.png)

6. Klicka på knappen **Kör**. Ett jobb schemaläggs omedelbart. Jobbet står med på panelen **Jobb** till höger i Workbench-fönstret. 

7. Efter en liten stund ändras statusen för jobbet från **Skickar** till **Körs**, och slutligen till **Slutfört** strax därefter.

   ![kör sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. Klicka på ordet **Slutfört** i jobbstatustexten på panelen Jobb. Ett popup-fönster öppnas med standardutdata (stdout) från skriptkörningen. Om du vill stänga stdout-texten klickar du på **X**-knappen längst upp till höger i popup-fönstret.

9. I samma jobbstatus i panelen Jobb klickar du på den blå texten **iris_sklearn.py [1]** precis ovanför statusen **Slutförd** och starttiden. Sidan **Körningsegenskaper** öppnas och visar informationen om körningen, **utdatafiler**, eventuella **visualiseringar** och **loggar** från körningen. 

   När körningen har slutförts visas följande resultat i popup-fönstret:

   >[!NOTE]
   >Eftersom vi introducerat viss slumpmässighet i inlärningsuppsättningen kommer de exakta resultaten att variera något.

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details page.
   ```

10. Stäng fliken **Körningsegenskaper** och återgå till fliken **iris_sklearn.py**. 

11. Kör skriptet flera gånger. 

    Ange en serie med olika numeriska värden i fältet **Argument**, från `0.001` till `10`. Klicka på **Kör** för att köra koden några gånger till. De argumentvärden du ändrar varje gång skickas till Logistic Regression-algoritmen i koden, vilket ger olika resultat varje gång.

## <a name="review-run-history-in-detail"></a>Granska körningshistoriken i detalj
I Azure Machine Learning Workbench sparas varje skriptkörning som en post i körningshistoriken. Du kan visa körningshistoriken för ett visst skript genom att öppna vyn **Körningar**.

1. Klicka på knappen **Körningar** (klockikonen) i det vänstra verktygsfältet så att du öppnar listan med **körningar**. Klicka på **iris_sklearn.py** så att du visar **Instrumentpanelen för körningar** för `iris_sklearn.py`.

   ![bild](media/tutorial-classifying-iris/run_view.png)

2. Fliken med **Instrumentpanelen för körningar** öppnas. Granska statistiken som sparats från de många körningarna. Diagram återges längst upp på fliken och varje numrerad körning visas med detaljerad information i tabellen längst ned på sidan.

   ![bild](media/tutorial-classifying-iris/run_dashboard.png)

3. Filtrera tabellen och klicka i diagrammen interaktivt för att visa status, längd, noggrannhet och regulariseringshastighet för varje körning. 

4. Markera två eller tre körningar i tabellen **Körningar** och klicka på knappen **Jämför**, så öppnas en detaljerad jämförelsesida. Granska jämförelsen. Klicka på bakåtknappen **Körningslista** uppe till vänster på jämförelsesidan så att du återgår till **Instrumentpanelen för körningar**.

5. Klicka på en enskild körning så att detaljvyn visas. Observera att statistiken för den valda körningen visas i avsnittet _Körningsegenskaper_. Filerna som skrivits till utdatamappen visas i avsnittet **Utdata** och kan laddas ned.

   ![bild](media/tutorial-classifying-iris/run_details.png)

   De två diagrammen, felmatrisen och ROC-kurvan för flera klasser, återges i avsnittet **Visualisering**. Du hittar även alla loggfiler i avsnittet **Loggar**.

## <a name="execute-scripts-in-the-local-docker-environment"></a>Köra skript i den lokala Docker-miljön

I Azure ML kan du enkelt konfigurera ytterligare körningsmiljöer, till exempel Docker, och köra skriptet i dessa miljöer. 

>[!IMPORTANT]
>Du måste ha Docker-motorn installerad lokalt och startad innan du kan utföra det här steget. Mer information finns i installationshandboken.

1. Välj mappikonen i det vänstra verktygsfältet så att du öppnar listan med **Filer** för projektet. Expandera mappen `aml_config`. 

2. Observera att det finns flera förkonfigurerade miljöer, som **docker-python**, **docker-spark** och **lokalt**. 

   Alla miljöer har två filer, som `docker-python.compute` och `docker-python.runconfig`. Öppna de olika filtyperna att lägg märke till att vissa alternativ kan konfigureras i textredigeraren.  

   Stäng (X) flikarna för eventuella öppna textredigerare för att frigöra utrymme.

3. Kör skriptet **iris_sklearn.py** i miljön **docker-python**. 

   - Klicka på klockikonen i det vänstra verktygsfältet så att du öppnar panelen **Körningar**. Klicka på **Alla körningar**. 
   - Längst upp på fliken **Alla körningar** väljer du **docker-python** som målmiljö istället för standardvärdet **lokalt**. 
   - Gå sedan till höger och välj **iris_sklearn.py** som skriptet att köra. 
   - Lämna fältet **Argument** tomt eftersom skriptet anger ett standardvärde. 
   - Klicka på knappen **Kör**.

4. Observera att ett nytt jobb startar på panelen **Jobb** till höger i Workbench-fönstret.

   När du kör mot Docker för första gången tar körningen några minuter mer att slutföra. 

   I bakgrunden skapar Azure Machine Learning Workbench en ny docker-fil som refererar till basavbildningen för Docker, som anges i filen `docker.compute`, Python-paket med beroenden som anges i filen `conda_dependencies.yml`. Docker-motorn laddar sedan ned basavbildningen från Azure, installerar de Python-paket som anges i filen `conda_dependencies.yml` och startar sedan en dockerbehållare. Sedan kopieras (eller refereras, beroende på körningskonfigurationen) den lokala kopian av projektmappen, och därefter körs skriptet `iris_sklearn.py`. I slutändan bör du se exakt samma resultat som när du använder **lokalt** som mål.

5. Nu ska vi prova Spark. Docker-basavbildningen innehåller en förinstallerad och förkonfigurerad Spark-instans. Därför kan du köra ett PySpark-skript i den. Det här är ett enkelt sätt att utveckla och testa ditt eget Spark-program utan att behöva ägna tid åt att installera och konfigurera Spark själv. 

   Öppna filen `iris_pyspark.py`. Skriptet läser in datafilen `iris.csv`, och sedan används Logistic Regression-algoritmen från Spark ML-biblioteket till att klassificera Iris-datauppsättningen. Ändra nu körningsmiljön till **docker-spark**, och skriptet till **iris_pyspark.py**, och kör skriptet igen. Det här tar lite längre tid eftersom en Spark-session måste skapas och startas i Docker-behållaren. Du kan också se att stdout inte är samma som stdout för `iris_pyspark.py`.

6. Gör några fler körningar och prova olika argument. 

7. Öppna filen `iris_pyspark.py` så att du ser Logistic Regression-modellen som skapats med Spark ML-biblioteket. 

8. Interagera med panelen **Jobb**, öppna listan med körningshistorik och visa detaljer om dina körningar i olika körningsmiljöer.

## <a name="execute-scripts-in-the-azure-ml-cli-window"></a>Köra skript i Azure ML CLI-fönstret

1. Öppna Azure Machine Learning Workbench och starta kommandotolken genom att klicka på menyn **Arkiv** och sedan på **Öppna kommandotolken**. Kommandotolken startar i projektmappen med prompten `C:\Temp\myIris\>`.

   >[!Important]
   >Du måste använda kommandotolken (som startats från Workbench) till att göra följande:

2. Använd Kommandotolken (CLI) för att logga in på Azure. 

   Workbench-appen och CLI använder oberoende cacheminnen för inloggningsuppgifter vid autentisering mot Azure-resurser. Du behöver bara göra det här en gång, tills token i cacheminnet upphör att gälla. Kommandot **az account list** returnerar listan med prenumerationer som är tillgängliga för din inloggning. Om det finns fler än en prenumeration använder du ID-värdet för den önskade prenumerationen, och anger den som standardkonto med kommandot **az account set -s** samt prenumerationens ID-värde. Bekräfta sedan inställningen med kommandot account show.

   ```azurecli
   REM login using aka.ms/devicelogin site.
   az login
   
   REM list all Azure subscriptions you have access to. 
   az account list -o table
   
   REM set the current Azure subscription to the one you want to use.
   az account set -s <subscriptionId>
   
   REM verify your current subscription is set correctly
   az account show
   ```

3. När du är autentiserad och har ställt in den aktuella Azure-prenumerationskontexten skriver du följande kommandon i CLI-fönstret för att installera matplotlib, och att skicka Python-skriptet som ett experiment att köra.

   ```azurecli
   REM You don't need to do this if you have installed matplotlib locally from the previous steps.
   pip install matplotlib
   
   REM Kick off an execution of the iris_sklearn.py file against local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. Granska utdata. Lägg märke till att utdata och resultat är samma som när du tidigare i självstudien använde Workbench till att köra skriptet. 

5. Kör samma skript i Docker-körningsmiljön om du har Docker installerat på datorn.

   ```azurecli
   REM Execute iris_sklearn.py in local Docker container Python environment.
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM Execute iris_pyspark.py in local Docker container Spark environment.
   az ml experiment submit -c docker-spark .\iris_pyspark.py 0.1
   ```
6. Klicka på mappikonen i verktygsfältet till vänster i Azure Machine Learning Workbench så att du visar listan med projektfiler, och öppna Python-skriptet **run.py**. 

   Det här skriptet är användbart när du vill loopa över olika regulariseringshastigheter och köra experimentet flera gånger med dessa hastigheter. Skriptet startar ett `iris_sklearn.py`-jobb med regulariseringshastigheten `10.0` (ett löjligt stort tal), halverar hastigheten i nästa körning. Det här fortsätter tills hastigheten är mindre än `0.005`. 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   När du ska starta skriptet **run.py** från kommandotolken kör du följande kommandon:

   ```cmd
   REM Submit iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   När `run.py` har slutförts visas ett diagram i körningshistoriken i Azure Machine Learning Workbench.

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>Köra i en Docker-behållare på en fjärrdator
Om du vill köra skriptet i en Docker-behållare på en fjärrdator med Linux måste du ha SSH-åtkomst (användarnamn och lösenord) till fjärrdatorn. Dessutom måste fjärrdatorn ha Docker-motorn installerad och igång. Det enklaste sättet att ordna en sådan Linux-dator är att skapa en [Ubuntu-baserad DSVM (Data Science Virtual Machine)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) i Azure. (Observera att CentOS-baserade DSVM:er inte stöds.) 

1. När den virtuella datorn har skapats kan du koppla den som en körningsmiljö genom att generera ett par av `.runconfig`- och `.compute`-filer med nedanstående kommando: Vi ger den nya miljön namnet `myvm`.
 
   ```azurecli
   REM create myvm compute target
   az ml computetarget attach --name myvm --address <IP address> --username <username> --password <password> --type remotedocker
   ```
   
   >[!NOTE]
   >IP-adressområdet kan även vara offentligt adresserbara FQDN (fullständigt kvalificerade domännamn), till exempel `vm-name.southcentralus.cloudapp.azure.com`. Det är en bra idé att lägga till FQDN för din DSVM och använda dem här i stället för IP-adresser, eftersom du kanske vill stänga av den virtuella datorn senare för att spara på kostnaden. Nästa gång du startar den virtuella datorn kan IP-adresserna ha ändrats.

   Kör sedan följande kommando för att skapa Docker-avbildningen i den virtuella datorn, så att den blir redo att köra skripten.
   
   ```azurecli
   REM prepare the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >Du kan också ändra värdet för `PrepareEnvironment` i `myvm.runconfig` från standardvärdet `false` till `true`. Då förbereds automatiskt Docker-behållaren vid den första körningen.

2. Redigera den genererade filen `myvm.runconfig` under `aml_config` och ändra FrameWork från standardvärdet `PySpark` till `Python`:

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >Det bör också fungera att lämna FrameWork-inställningen som PySpark. Det är däremot något ineffektivt om du inte verkligen behöver en Spark-session för att köra Python-skriptet.

3. Kör samma kommando som du gjorde förut i CLI-fönstret, förutom att vi använder _myvm_ som mål den här gången:
   ```azurecli
   REM execute iris_sklearn.py in remote Docker container
   az ml experiment submit -c myvm .\iris_sklearn.py
   ```
   Kommandot körs precis som om du använde en `docker-python`-miljö, förutom att körningen sker på den virtuella fjärrdatorn med Linux. Samma utdata visas i CLI-fönstret.

4. Nu ska vi prova med Spark i behållaren. Öppna Utforskaren (du kan också göra detta från CLI-fönstret om du är bekant med grundläggande kommandon för filmanipulering). Skapa en kopia av filen `myvm.runconfig` och ge den namnet `myvm-spark.runconfig`. Redigera den nya filen och ändra inställningen `Framework` från `Python` till `PySpark`:
   ```yaml
   "Framework": "PySpark"
   ```
   Gör inga ändringar i filen `myvm.compute`. Samma Docker-avbildning på samma virtuella dator används vid Spark-körningen. I den nya filen `myvy-spark.runconfig` pekar fältet `target` på samma fil `myvm.compute` via namnet `myvm`.

5. Skriv kommandot nedan för att köra skriptet i Spark-instansen i den fjärranslutna Docker-behållaren:
   ```azureli
   REM execute iris_pyspark.py in Spark instance on remote Docker container
   az ml experiment submit -c myvm-spark .\iris_pyspark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>Köra skript i ett HDInsight-kluster
Du kan också köra skriptet i ett faktiskt Spark-kluster. 

1. Om du har tillgång till ett Spark for Azure HDInsight-kluster kan du generera ett kommando för konfiguration av HDI-körningen på det sätt som visas. Ange HDInsight-klusternamnet samt ditt användarnamn och lösenord för HDInsight som parametrar. Ange följande kommando:

   ```azurecli
   REM create a compute target that points to a HDI cluster
   az ml computetarget attach --name myhdi --address <cluster head node FQDN> --username <username> --password <password> --type cluster

   REM prepare the HDI cluster
   az ml experiment prepare -c myhdi
   ```

   Klusterhuvudnodens FQDN (fullständigt kvalificerade domännamn) är vanligtvis `<cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` är klustrets SSH-användarnamn. Standardvärdet är `sshuser` om du inte ändrat det under HDI-etableringen. Det är inte `admin`, som är den andra användaren som skapades under etableringen för att ge åtkomst till klustrets administrationswebbplats. 

2. Kör följande kommando så körs skriptet i HDInsight-klustret:

   ```azurecli
   REM execute iris_pyspark on the HDI cluster
   az ml experiment submit -c myhdi .\iris_pyspark.py
   ```

   >[!NOTE]
   >När du kör mot ett fjärranslutet HDI-kluster kan du även visa körningsinformation om YARN-jobbet på `https://<cluster_name>.azurehdinsight.net/yarnui` med användarkontot `admin`.


## <a name="next-steps"></a>Nästa steg
I den här andra delen av självstudieserien i tre delar har du lärt dig att använda Azure Machine Learning till att:
> [!div class="checklist"]
> * Arbeta i Azure Machine Learning Workbench
> * Öppna skript och granskar kod
> * Köra skript i en lokal miljö
> * Granska körningshistorik
> * Köra skript i en lokal Docker-miljö
> * Köra skript i ett lokalt Azure CLI-fönster
> * Köra skript i en fjärransluten Docker-miljö
> * Köra skript i en HDInsight-molnmiljö

Du är nu redo att gå vidare till den tredje delen i serien. Nu när vi har skapat Logistic Regression-modellen ska vi distribuera den som en realtidswebbtjänst.

> [!div class="nextstepaction"]
> [Distribuera en modell](tutorial-classifying-iris-part-3.md)

