---
title: Självstudie – Azure Toolkit for IntelliJ (Spark-program)
description: Självstudie – Använd Azure Toolkit for IntelliJ för att utveckla Spark-program som är skrivna i Scala och skicka dem till en server lös Apache Spark-pool.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: 39f39b6f53944510b5f3692bb8b3fdd472004454
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121234"
---
# <a name="tutorial-create-an-apache-spark-application-with-intellij-using-a-synapse-workspace"></a>Självstudie: skapa ett Apache Spark program med IntelliJ med hjälp av en Synapse-arbetsyta

Den här självstudien visar hur du använder Azure Toolkit for IntelliJ plugin-programmet för att utveckla Apache Spark-program som är skrivna i [Scala](https://www.scala-lang.org/)och sedan skicka dem till en server lös Apache Spark pool direkt från IntelliJ Integrated Development Environment (IDE). Du kan använda plugin-programmet på några sätt:

- Utveckla och skicka in ett Scala Spark-program på en spark-pool.
- Få åtkomst till dina Spark-pooler resurser.
- Utveckla och kör ett Scala Spark-program lokalt.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> - Använd plugin-programmet Azure Toolkit for IntelliJ
> - Utveckla Apache Spark program
> - Skicka program till Spark-pooler

## <a name="prerequisites"></a>Förutsättningar

- [INTELLIJ idé community-version](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Azure toolkit-plugin-3.27.0 – 2019.2 – Installera från [IntelliJ plugin-lagringsplatsen](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [JDK (Version 1,8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Scala-plugin – installera från [IntelliJ-plugin-lagringsplatsen](../../hdinsight/spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).
- Följande förutsättningar gäller endast för Windows-användare:

  När du kör det lokala Spark Scala-programmet på en Windows-dator kan du få ett undantag, enligt beskrivningen i [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Undantaget beror på att WinUtils.exe saknas i Windows.
  Lös problemet genom att ladda ned [WinUtils-körbara filen](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) till en plats, till exempel **C:\WinUtils\bin**. Lägg sedan till miljövariabeln **HADOOP_HOME** och ange värdet för variabeln till **C:\WinUtils**.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Skapa ett Spark Scala-program för en spark-pool

1. Starta IntelliJ IDEA och välj **Skapa nytt projekt** för att öppna fönstret **Nytt projekt**.
2. Välj **Apache Spark/HDInsight** i det vänstra fönstret.
3. Välj **Spark-projekt med exempel (Scala)** från huvud fönstret.
4. I list rutan **build-verktyg** väljer du någon av följande typer:

   - **Maven** för guidestöd när du skapar Scala-projekt.
   - **SBT** för att hantera beroenden när du skapar Scala-projektet.

    ![Dialog rutan IntelliJ idé New Project](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Välj **Nästa**.
6. I fönstret **Nytt projekt** anger du följande information:

    | Egenskap | Beskrivning |
    | ----- | ----- |
    |Projektnamn| Ange ett namn. I den här självstudien används `myApp`.|
    |Projektplats| Ange önskad plats för att spara projektet.|
    |Projekt-SDK| Det kan vara tomt vid din första användning av idén. Välj **Nytt...** och navigera till din JDK.|
    |Spark-version|Skapandeguiden integrerar rätt version för Spark SDK och Scala SDK. Synapse stöder endast **Spark-2.4.0**.|
    |||

    ![Välja Apache Spark SDK](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Välj **Slutför**. Det kan ta några minuter innan projektet blir tillgängligt.
8. Spark-projektet skapar automatiskt en artefakt åt dig. Om du vill visa artefakten gör du följande:

   a. I meny raden navigerar du till **fil**  >  **projekt struktur...**.

   b. I fönstret **projekt struktur** väljer du **artefakter**.

   c. Välj **Avbryt** när du har visat artefakten.

    ![Artefakt information i dialog rutan](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Hitta **LogQuery** från **Mittprog**  >  **src**  >  **main**  >  **Scala** >  **Sample** >  **LogQuery**. I den här självstudien används **LogQuery** för att köra.

   ![Kommandon för att skapa en Scala-klass från Project](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Anslut till dina Spark-pooler

Logga in på Azure-prenumerationen för att ansluta till dina Spark-pooler.

### <a name="sign-in-to-your-azure-subscription"></a>Logga in på din Azure-prenumeration

1. Gå till **Visa**-  >  **verktyget Windows**  >  **Azure Explorer** från meny raden.

   ![IntelliJ idé visar Azure Explorer](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. I Azure Explorer högerklickar du på **Azure** -noden och väljer sedan **Logga** in.

   ![IntelliJ idé Explorer högerklickar du på Azure](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. I dialog rutan **Azure-inloggning** väljer du **enhets inloggning** och väljer sedan **Logga** in.

    ![IntelliJ idé Azure-inloggning](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. I dialog rutan **Azure-enhets inloggning** väljer du **Kopiera&öppna**.

   ![IntelliJ idé Azure-enhets inloggning](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. I webb läsar gränssnittet klistrar du in koden och väljer sedan **Nästa**.

   ![Microsoft anger kod dialog rutan för HDI](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Ange dina autentiseringsuppgifter för Azure och stäng sedan webbläsaren.

   ![Microsoft anger e-postdialog för HDI](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. När du har loggat in visar dialog rutan **Välj prenumerationer** alla Azure-prenumerationer som är associerade med autentiseringsuppgifterna. Välj din prenumeration och välj sedan **Välj**.

    ![Dialogrutan Select Subscriptions (Välj prenumerationer)](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. I **Azure Explorer** expanderar du **Apache Spark på Synapse** för att visa de arbets ytor som finns i dina prenumerationer.

    ![IntelliJ idé Azure Explorer Main View](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Om du vill visa Spark-pooler kan du ytterligare expandera en arbets yta.

    ![Lagrings konton i Azure Explorer](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Fjärrköra ett Spark Scala-program i en spark-pool

När du har skapat ett Scala-program kan du köra det på distans.

1. Öppna fönstret **Kör/Felsök konfigurationer** genom att välja ikonen.

    ![Skicka Spark-programmet till HDInsight-kommando 1](./media/intellij-tool-synapse/open-configuration-window.png)

2. I dialog rutan **Kör/Felsök konfigurationer** väljer **+** du och väljer sedan **Apache Spark på Synapse**.

    ![Skicka Spark-programmet till HDInsight-kommando 2](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. I fönstret **Kör/Felsök konfigurationer** anger du följande värden och väljer sedan **OK**:

    |Egenskap |Värde |
    |----|----|
    |Spark-pooler|Välj de Spark-pooler som du vill köra programmet på.|
    |Välj en artefakt som ska skickas|Lämna standardvärdet.|
    |Huvud klass namn|Standardvärdet är huvud klassen från den valda filen. Du kan ändra klassen genom att välja ellipsen (**...**) och välja en annan klass.|
    |Jobb konfiguration|Du kan ändra standard nyckel och standardvärden. Mer information finns i [Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Kommando rads argument|Du kan ange argument avgränsade med blank steg för huvud klassen om det behövs.|
    |Refererade jar v7 och refererade filer|Du kan ange sökvägar för refererade jar v7 och filer om det finns några. Du kan också bläddra i filer i det virtuella Azure-filsystemet, som för närvarande endast stöder ADLS Gen2 kluster. Mer information: [Apache Spark Configuration] https://spark.apache.org/docs/2.4.5/configuration.html#runtime-environment) och [hur du överför resurser till kluster](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).|
    |Jobb överförings lagring|Expandera för att visa fler alternativ.|
    |Lagringstyp|Välj **Använd Azure Blob för att ladda upp** eller **använda klustrets standard lagrings konto för att ladda upp** från List rutan.|
    |Lagringskonto|Ange ditt lagrings konto.|
    |Lagrings nyckel|Ange din lagrings nyckel.|
    |Lagrings behållare|Välj din lagrings behållare i list rutan när **lagrings kontot** och **lagrings nyckeln** har angetts.|

    ![Dialog rutan för Spark-överföring 1](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Välj **SparkJobRun** -ikonen för att skicka projektet till den valda Spark-poolen. I fliken **fjärr-Spark-jobb i kluster** visas jobb körnings förloppet längst ned. Du kan stoppa programmet genom att välja den röda knappen.

    ![Apache Spark sändnings fönster](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![Dialog rutan Spark-överföring 2](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Lokal körning/fel sökning Apache Spark program

Du kan följa anvisningarna nedan för att konfigurera din lokala körnings-och lokal fel sökning för ditt Apache Spark-jobb.

### <a name="scenario-1-do-local-run"></a>Scenario 1: utför lokal körning

1. Öppna dialog rutan **Kör/Felsök konfigurationer** och välj plus tecknet ( **+** ). Välj sedan alternativet **Apache Spark på Synapse** . Ange information om **namn**, **huvud klass namn** som ska sparas.

    ![IntelliJ kör felsöka konfigurationer lokal körning 1](./media/intellij-tool-synapse/local-run-synapse.png)

    - Miljövariabler och WinUtils.exe plats är endast för Windows-användare.
    - Miljövariabler: system miljö variabeln kan identifieras automatiskt om du har angett den och inte behöver lägga till manuellt.
    - [WinUtils.exe plats](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe): du kan ange platsen för WinUtils genom att välja mappikonen till höger.

2. Välj sedan knappen lokal uppspelning.

    ![IntelliJ kör felsöka konfigurationer lokal körning 2](./media/intellij-tool-synapse/local-run-synapse01.png)

3. När den lokala körningen är klar kan du kontrol lera utdatafilen från **data** standard om skriptet innehåller utdata  >  ****.

    ![IntelliJ-projektets lokala körnings resultat 1](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Scenario 2: utför lokal fel sökning

1. Öppna **LogQuery** -skriptet och ange Bryt punkter.
2. Välj **lokal fel söknings** ikon för lokal fel sökning.

    ![IntelliJ-projektets lokala körnings resultat 2](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Komma åt och hantera Synapse-arbetsyta

Du kan utföra olika åtgärder i Azure Explorer i Azure Toolkit for IntelliJ. Gå till **Visa**-  >  **verktyget Windows**  >  **Azure Explorer** från meny raden.

### <a name="launch-workspace"></a>Starta arbets yta

1. Från Azure Explorer navigerar du till **Apache Spark på Synapse** och expanderar den sedan.

    ![IntelliJ idé Azure Explorer Main View](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Högerklicka på en arbets yta och välj sedan **Starta arbets yta**, så öppnas webbplatsen.

    ![Spark-jobb Visa program information 1](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Spark-jobb Visa program information 2](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Spark-konsol

Du kan köra Spark Local Console (Scala) eller köra Spark livy Interactive Session Console (Scala).

### <a name="spark-local-console-scala"></a>Spark-lokal konsol (Scala)

Se till att du har uppfyllt kraven för WINUTILS.EXE.

1. Gå till meny raden och navigera till **Kör**  >  **Redigera konfigurationer...**.
2. I fönstret **Kör/Felsök-konfigurationer** går du till vänster-fönstret och navigerar till **Apache Spark på Synapse**  >  **[Spark på Synapse] MyApp**.
3. Välj fliken **lokalt körning** i huvud fönstret.
4. Ange följande värden och välj sedan **OK**:

    |Egenskap |Värde |
    |----|----|
    |Miljövariabler|Se till att värdet för HADOOP_HOME är korrekt.|
    |WINUTILS.exe plats|Se till att sökvägen är korrekt.|

    ![Konfiguration av lokal konsol uppsättning](./media/intellij-tool-synapse/local-console-synapse01.png)

5. Från projekt navigerar du till **Mittprog**  >  **src**  >  **main**  >  **Scala**  >  **Mittprog**.
6. I meny raden navigerar du till **verktyg**  >  **Spark-konsolen**  >  **Kör Spark Local Console (Scala)**.
7. Sedan kan två dialog rutor visas för att fråga dig om du vill åtgärda beroenden automatiskt. I så fall väljer du **automatisk korrigering**.

    ![IntelliJ idé Spark, Auto Fix dialog1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ idé Spark, Auto Fix Dialog2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. Konsolen bör se ut ungefär som på bilden nedan. I konsol fönstret typ `sc.appName` , och tryck sedan på CTRL + RETUR. Resultatet visas. Du kan stoppa den lokala konsolen genom att välja röd knapp.

    ![Resultat av IntelliJ idén-lokal konsol](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Spark livy interaktiv Session Console (Scala)

Det stöds endast på IntelliJ 2018,2 och 2018,3.

1. Gå till meny raden och navigera till **Kör**  >  **Redigera konfigurationer...**.

2. I fönstret **Kör/Felsök-konfigurationer** går du till vänster-fönstret och navigerar till **Apache Spark på Synapse**  >  **[Spark på Synapse] MyApp**.

3. Välj fliken **fjärrkörning på kluster** i huvud fönstret.

4. Ange följande värden och välj sedan **OK**:

    |Egenskap |Värde |
    |----|----|
    |Huvud klass namn| Välj huvud klass namnet.| 
    |Spark-pooler|Välj de Spark-pooler som du vill köra programmet på.|
    ||

    ![Konfiguration av interaktiv konsol uppsättning](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. Från projekt navigerar du till **Mittprog**  >  **src**  >  **main**  >  **Scala**  >  **Mittprog**.

6. I meny raden navigerar du till **verktyg**  >  **Spark-konsolen**  >  **Kör Spark livy Interactive Session Console (Scala)**.
7. Konsolen bör se ut ungefär som på bilden nedan. I konsol fönstret typ `sc.appName` , och tryck sedan på CTRL + RETUR. Resultatet visas. Du kan stoppa den lokala konsolen genom att välja röd knapp.

    ![Resultat av IntelliJ-idé interaktiv konsol](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Skicka markering till Spark-konsolen

Du kanske vill se skript resultatet genom att skicka kod till den lokala konsolen eller livy-Scala (Interactive Session Console). Om du vill göra det kan du markera en kod i Scala-filen och högerklicka på **Skicka markering till Spark-konsolen**. Den valda koden kommer att skickas till-konsolen och göras. Resultatet kommer att visas efter koden i-konsolen. -Konsolen kommer att kontrol lera de befintliga felen.

   ![Skicka markering till Spark-konsolen](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Nästa steg

- [Azure Synapse Analytics](../overview-what-is.md)
- [Skapa en ny Apache Spark pool för en Azure Synapse Analytics-arbetsyta](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)