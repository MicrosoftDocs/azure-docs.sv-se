---
title: Hantera Scala & Java-bibliotek för Apache Spark
description: Lär dig hur du lägger till och hanterar Scala-och Java-bibliotek i Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: c70ecc4fc5469d728bc12d47024585ccf00ff98e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098714"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>Hantera Scala-och Java-paket för Apache Spark i Azure Synapse Analytics

Bibliotek ger återanvändbar kod som du kanske vill inkludera i dina program eller projekt. 

Du kan behöva uppdatera Server lösa Apache Spark pool miljö av flera orsaker. Du kan till exempel se att:
- ett av dina kärn beroenden släppte en ny version.
- du behöver ett extra paket för att träna din Machine Learning-modell eller förbereda dina data.
- du har hittat ett bättre paket och behöver inte längre det gamla paketet.

Om du vill göra tredje part eller lokalt skapad kod tillgänglig för dina program, kan du installera ett bibliotek på någon av Server lös Apache Spark pooler eller Notebook-sessionen. I den här artikeln tar vi upp hur du kan hantera Scala-och Java-paket.

## <a name="default-installation"></a>Standard installation
Apache Spark i Azure Synapse Analytics har en fullständig uppsättning bibliotek för gemensam data teknik, data förberedelse, maskin inlärning och data visualiserings aktiviteter. Du hittar den fullständiga biblioteks listan på [Apache Spark versions stöd](apache-spark-version-support.md). 

När en spark-instans startar, tas dessa bibliotek automatiskt med. Du kan lägga till ytterligare Scala/Java-paket i Spark-poolen och-sessionstillståndet.

## <a name="workspace-packages"></a>Paket för arbets ytor
Arbets ytans paket kan vara anpassade eller privata jar-filer. Du kan ladda upp dessa paket till din arbets yta och senare tilldela dem till en speciell Spark-pool.

Så här lägger du till paket för arbets ytor:
1. Gå till fliken **Hantera**  >  **arbets yta paket** .
2. Överför dina jar-filer med hjälp av fil väljaren.
3. När filerna har laddats upp till Azure dataSynapses-arbetsytan kan du lägga till dessa JAR-filer till en specifik Apache Spark pool.

![Skärm bild som visar arbets ytans paket.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Visa paket för arbets ytor")

## <a name="pool-libraries"></a>Bibliotek för pooler
När du har identifierat de Scala-och Java-paket som du vill använda för ditt Spark-program kan du installera dem i en spark-pool. Bibliotek på Poolnivå är tillgängliga för alla antecknings böcker och jobb som körs på poolen.

Du kan uppdatera Spark-poolens bibliotek genom att gå till Azure Synapse Studio eller Azure Portal. Här kan du välja de bibliotek för arbets ytor som ska installeras. 

När ändringarna har sparats kör ett Spark-jobb installationen och cachelagrar den resulterande miljön för senare åter användning. När jobbet är klart kommer nya Spark-jobb eller Notebook-sessioner att använda de uppdaterade pool biblioteken. 

> [!IMPORTANT]
> - Om paketet som du installerar är stort eller tar lång tid att installera, påverkar detta start tiden för Spark-instansen.
> - Det finns inte stöd för att ändra PySpark-, python-, Scala-/Java-, .NET-eller Spark-versionen.

#### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Hantera paket från Azure Synapse Studio eller Azure Portal
Bibliotek av Spark-pooler kan hanteras från Azure Synapse Studio eller Azure Portal. 

Uppdatera eller lägga till bibliotek i en spark-pool:
1. Navigera till din Azure Synapse Analytics-arbetsyta från Azure Portal.

    Om du uppdaterar från **Azure Portal**:

    - Under avsnittet **Synapse-resurser** väljer du fliken **Apache Spark pooler** och väljer en spark-pool i listan.
     
    - Välj **paketen** i avsnittet **Inställningar** i Spark-poolen.
  
    ![Skärm bild som visar knappen Ladda upp miljö konfigurations fil.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Lägg till Python-bibliotek")
   
    Om du uppdaterar från **Synapse Studio**:
    - Välj **Hantera** från huvud navigerings panelen och välj sedan **Apache Spark pooler**.

    - Välj avsnittet **paket** för en speciell Spark-pool.
    ![Skärm bild som visar konfigurations alternativet Ladda upp miljö från Studio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Lägga till Python-bibliotek från Studio")
   
2. Om du vill lägga till jar-filer går du till avsnittet **paket för arbets ytor** som du vill lägga till i poolen. 
3. När du har sparat ändringarna aktive ras ett system jobb för att installera och cachelagra de angivna biblioteken. Den här processen bidrar till att minska start tiden för den totala sessionen. 
4. När jobbet har slutförts, kommer alla nya sessioner att hämta de uppdaterade pool biblioteken.

> [!IMPORTANT]
> Genom att välja alternativet för att **tvinga nya inställningar** kommer du att avsluta alla aktuella sessioner för den valda Spark-poolen. När sessionerna har slutförts måste du vänta tills poolen startats om. 
>
> Om den här inställningen är omarkerad måste du vänta tills den aktuella Spark-sessionen avslutas eller stoppa den manuellt. När sessionen har upphört måste du låta poolen starta om.

#### <a name="track-installation-progress-preview"></a>Spåra installations förloppet (förhands granskning)
Ett system reserverat Spark-jobb initieras varje gången en pool uppdateras med en ny uppsättning bibliotek. Detta Spark-jobb hjälper dig att övervaka status för biblioteks installationen. Om installationen Miss lyckas på grund av biblioteks konflikter eller andra problem, kommer Spark-poolen att återgå till sitt tidigare eller standard tillstånd. 

Dessutom kan användarna också kontrol lera installations loggarna för att identifiera beroende konflikter eller se vilka bibliotek som installerades under uppdateringen av poolen.

Så här visar du loggarna:
1. Navigera till listan Spark-program på fliken **övervaka** . 
2. Välj det system Spark-programjobb som motsvarar din uppdatering av poolen. Dessa system jobb körs under rubriken *SystemReservedJob-LibraryManagement* .
   ![Skärm bild som fokuserar på system reserverat biblioteks jobb.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Visa system biblioteks jobb")
3. Växla för att visa **driv rutins** -och **STDOUT** -loggar. 
4. I resultaten ser du de loggar som är relaterade till installationen av dina paket.
    ![Skärm bild som visar system reserverade biblioteks jobb resultat.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Visa jobb förlopp för system bibliotek")

## <a name="session-scoped-libraries"></a>Bibliotek med sessionsbaserade omfång 
Förutom bibliotek på en samlings nivå kan du också ange bibliotek för sessionsläge i början av en Notebook-session.  Med session-definitions bibliotek kan du ange och använda jar-paket enbart inom en Notebook-session. 

När du använder bibliotek med sessionsbaserade bibliotek är det viktigt att tänka på följande saker:
   - När du installerar sessionsbaserade bibliotek har endast den aktuella antecknings boken åtkomst till de angivna biblioteken. 
   - De här biblioteken påverkar inte andra sessioner eller jobb med samma Spark-pool. 
   - De här biblioteken installeras ovanpå bas körnings-och Poolnivå biblioteken. 
   - Bärbara bibliotek har högsta prioritet.

Om du vill ange ett Scala Java-eller-paket kan du använda ```%%configure``` alternativet:

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

Vi rekommenderar att du kör%% i början av din antecknings bok. Du kan referera till det här [dokumentet](https://github.com/cloudera/livy#request-body) för att se en fullständig lista över giltiga parametrar.

## <a name="next-steps"></a>Nästa steg
- Visa standard biblioteken: [Apache Spark versions stöd](apache-spark-version-support.md)
- Felsöka biblioteks installations fel: [Felsöka biblioteks fel](apache-spark-troubleshoot-library-errors.md)
