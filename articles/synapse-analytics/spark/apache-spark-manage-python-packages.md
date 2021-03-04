---
title: Hantera python-bibliotek för Apache Spark
description: Lär dig hur du lägger till och hanterar python-bibliotek som används av Apache Spark i Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 4bb323e0e8f72456b6a522ede9a98d193e1c3c7e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098782"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Hantera python-bibliotek för Apache Spark i Azure Synapse Analytics

Bibliotek ger återanvändbar kod som du kanske vill inkludera i dina program eller projekt. 

Du kan behöva uppdatera Server lösa Apache Spark pool miljö av olika anledningar. Du kan till exempel se att:
- ett av dina kärn beroenden släppte precis en ny version.
- du behöver ett extra paket för att träna din Machine Learning-modell eller förbereda dina data.
- du har hittat ett bättre paket och behöver inte längre det gamla paketet.

Om du vill göra tredje part eller lokalt skapad kod tillgänglig för dina program, kan du installera ett bibliotek på någon av Server lös Apache Spark pooler eller Notebook-sessionen. I den här artikeln tar vi upp hur du kan hantera python-bibliotek i Apache Spark poolen utan server.

## <a name="default-installation"></a>Standard installation
Apache Spark i Azure Synapse Analytics har en fullständig uppsättning bibliotek för gemensam data teknik, data förberedelse, maskin inlärning och data visualiserings aktiviteter. Du hittar den fullständiga biblioteks listan på [Apache Spark versions stöd](apache-spark-version-support.md). 

När en spark-instans startar, tas dessa bibliotek automatiskt med. Extra python och anpassade-skapade paket kan läggas till i Spark-poolen och-sessionstillståndet.

## <a name="pool-libraries"></a>Bibliotek för pooler
När du har identifierat de python-bibliotek som du vill använda för ditt Spark-program kan du installera dem i en spark-pool. Bibliotek på Poolnivå är tillgängliga för alla antecknings böcker och jobb som körs på poolen.

Det finns två huvudsakliga sätt att installera ett bibliotek på ett kluster:
-  Installera ett bibliotek för arbets ytor som har laddats upp som ett paket för arbets ytan.
-  Ange en *requirements.txt* -eller *Conda miljö. yml* -miljö specifikation för att installera paket från databaser som pypi, Conda-falska med mera.

> [!IMPORTANT]
> - Om paketet som du installerar är stort eller tar lång tid att installera, påverkar detta start tiden för Spark-instansen.
> - Det finns inte stöd för att ändra PySpark-, python-, Scala-/Java-, .NET-eller Spark-versionen.
> - Det går inte att installera paket från externa databaser som PyPI, Conda-falska eller standard Conda-kanaler i DEP-aktiverade arbets ytor.

### <a name="install-python-packages"></a>Installera python-paket
Python-paket kan installeras från databaser som PyPI och Conda-Forge genom att tillhandahålla en miljö Specifikations fil. 

#### <a name="environment-specification-formats"></a>Miljö Specifikations format

##### <a name="pip-requirementstxt"></a>PIP requirements.txt
En *requirements.txt* -fil (utdata från `pip freeze` kommandot) kan användas för att uppgradera miljön. När en pool uppdateras laddas de paket som anges i den här filen ned från PyPI. Alla beroenden cachelagras och sparas för senare åter användning av poolen. 

I följande kodfragment visas formatet för krav filen. PyPI-paketets namn visas tillsammans med en exakt version. Den här filen följer formatet som beskrivs i referens dokumentationen för [pip Freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) . 

Det här exemplet fäster en speciell version. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>YML-format (förhands granskning)
Dessutom kan du också ange en *miljö. yml* -fil för att uppdatera poolens miljö. Paketen som anges i den här filen hämtas från standard kanalerna Conda, Conda-falska och PyPI. Du kan ange andra kanaler eller ta bort standard kanalerna med hjälp av konfigurations alternativen.

I det här exemplet anges kanaler och Conda/PyPI-beroenden. 

```
name: stats2
channels:
  - defaults
dependencies:
  - bokeh=0.9.2
  - numpy=1.9.*
  - flask
  - pip:
    - matplotlib
```
Mer information om hur du skapar en miljö från den här miljön. yml-filen finns i [skapa en miljö från en miljö. YML-fil](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually).

#### <a name="update-python-packages"></a>Uppdatera python-paket
När du har identifierat miljö Specifikations filen eller uppsättningen bibliotek som du vill installera i Spark-poolen kan du uppdatera Spark-poolens bibliotek genom att gå till Azure Synapse Studio eller Azure Portal. Här kan du ange miljö specifikationen och välja de bibliotek för arbets ytor som ska installeras. 

När ändringarna har sparats kommer ett Spark-jobb att köra installationen och cachelagra den resulterande miljön för senare åter användning. När jobbet är klart kommer nya Spark-jobb eller Notebook-sessioner att använda de uppdaterade pool biblioteken. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Hantera paket från Azure Synapse Studio eller Azure Portal
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
   
2. Ladda upp miljö konfigurations filen med fil väljaren i avsnittet  **paket** på sidan.
3. Du kan också välja ytterligare **paket för arbets ytor** som ska läggas till i poolen. 
4. När du har sparat ändringarna aktive ras ett system jobb för att installera och cachelagra de angivna biblioteken. Den här processen bidrar till att minska start tiden för den totala sessionen. 
5. När jobbet har slutförts, kommer alla nya sessioner att hämta de uppdaterade pool biblioteken.

> [!IMPORTANT]
> Genom att välja alternativet för att **tvinga nya inställningar** kommer du att avsluta alla aktuella sessioner för den valda Spark-poolen. När sessionerna har slutförts måste du vänta tills poolen startats om. 
>
> Om den här inställningen är omarkerad måste du vänta tills den aktuella Spark-sessionen avslutas eller stoppa den manuellt. När sessionen har upphört måste du låta poolen starta om.


##### <a name="track-installation-progress-preview"></a>Spåra installations förloppet (förhands granskning)
Ett system reserverat Spark-jobb initieras varje gången en pool uppdateras med en ny uppsättning bibliotek. Detta Spark-jobb hjälper dig att övervaka status för biblioteks installationen. Om installationen Miss lyckas på grund av biblioteks konflikter eller andra problem, kommer Spark-poolen att återgå till sitt tidigare eller standard tillstånd. 

Dessutom kan användarna också kontrol lera installations loggarna för att identifiera beroende konflikter eller se vilka bibliotek som installerades under uppdateringen av poolen.

Så här visar du loggarna:
1. Navigera till listan Spark-program på fliken **övervaka** . 
2. Välj det system Spark-programjobb som motsvarar din uppdatering av poolen. Dessa system jobb körs under rubriken *SystemReservedJob-LibraryManagement* .
   ![Skärm bild som fokuserar på system reserverat biblioteks jobb.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Visa system biblioteks jobb")
3. Växla för att visa **driv rutins** -och **STDOUT** -loggar. 
4. I resultaten ser du de loggar som är relaterade till installationen av dina beroenden.
    ![Skärm bild som visar system reserverade biblioteks jobb resultat.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Visa jobb förlopp för system bibliotek")

## <a name="install-wheel-files"></a>Installera Wheel-filer
Python-Wheel-filer är ett vanligt sätt för att packa python-bibliotek. I Azure Synapse Analytics kan användarna ladda upp sina Wheel-filer till en välkänd plats Azure Data Lake Storage kontot eller ladda upp med paket gränssnittet för Azure Synapse-arbetsyta.

### <a name="workspace-packages-preview"></a>Paket för arbets ytor (förhands granskning)
Arbets ytans paket kan vara anpassade eller privata Wheel-filer. Du kan ladda upp dessa paket till din arbets yta och senare tilldela dem till en speciell Spark-pool.

Så här lägger du till paket för arbets ytor:
1. Gå till fliken **Hantera**  >  **arbets yta paket** .
2. Ladda upp dina Wheel-filer med hjälp av fil väljaren.
3. När filerna har laddats upp till Azure dataSynapses-arbetsytan kan du lägga till dessa paket i en specifik Apache Spark pool.

![Skärm bild som visar arbets ytans paket.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Visa paket för arbets ytor")

### <a name="storage-account"></a>Lagringskonto
Anpassade hjul paket kan installeras på den Apache Spark poolen genom att ladda upp alla hjul till Azure Data Lake Storage-kontot (Gen2) som är länkat till arbets ytan Synapse. 

Filerna ska överföras till följande sökväg i lagrings kontots standard behållare: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

Du kan behöva lägga till ```python``` mappen i ```libraries``` mappen om den inte redan finns.

> [!IMPORTANT]
> Om du vill installera anpassade bibliotek med hjälp av lagrings metoden för Azure-DataLake måste du ha behörigheter för **Storage BLOB-data deltagare** eller **lagrings-BLOB-data** för det primära Gen2-lagrings kontot som är länkat till Azure Synapse Analytics-arbetsytan.

>[!WARNING]
> När du tillhandahåller anpassade Wheel-filer kan användarna inte ange drivrutinsfiler i både lagrings kontot och i bibliotekets biblioteks gränssnitt. Om båda anges installeras bara de Wheel-filer som anges i listan med paket för arbets ytor. 

## <a name="session-scoped-packages-preview"></a>Paket med sessionsbaserade paket (för hands version)
Förutom paket på Poolnivå kan du också ange bibliotek för sessionsbaserade i början av en Notebook-session.  Med session-definitions bibliotek kan du ange och använda anpassade python-miljöer i en Notebook-session. 

När du använder bibliotek med sessionsbaserade bibliotek är det viktigt att tänka på följande saker:
   - När du installerar sessionsbaserade bibliotek har endast den aktuella antecknings boken åtkomst till de angivna biblioteken. 
   - De här biblioteken påverkar inte andra sessioner eller jobb med samma Spark-pool. 
   - De här biblioteken installeras ovanpå bas körnings-och Poolnivå biblioteken. 
   - Bärbara bibliotek har högsta prioritet.

Så här anger du paket som omfattas av sessioner:
1.  Navigera till den valda Spark-poolen och se till att du har aktiverat bibliotek på sessions nivå.  Du kan aktivera den här inställningen genom att gå till   >  fliken Apache Spark hantera paket för **programpooler**  >   . ![Aktivera session paket.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Aktivera sessionstillstånd")
2.  När inställningen har tillämpats kan du öppna en antecknings bok och välja **Konfigurera session** >  **paket**.
  ![Ange sessions paket.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Uppdatera konfiguration av session")
3.  Här kan du ladda upp en Conda- *miljö. yml* -fil för att installera eller uppgradera paket i en session. När du startar sessionen installeras de angivna biblioteken. När sessionen har slutförts kommer de här biblioteken inte längre vara tillgängliga eftersom de är speciella för din session.

## <a name="verify-installed-libraries"></a>Verifiera installerade bibliotek
Kör följande kod för att kontrol lera om rätt versioner av rätt bibliotek installeras från PyPI:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
I vissa fall kan du behöva kontrol lera paket versionen separat för att kunna visa paket versionerna från Conda.

## <a name="next-steps"></a>Nästa steg
- Visa standard biblioteken: [Apache Spark versions stöd](apache-spark-version-support.md)
- Felsöka biblioteks installations fel: [Felsöka biblioteks fel](apache-spark-troubleshoot-library-errors.md)
- Skapa en privat Conda-kanal med ditt Azure Data Lake Storage-konto: [Conda privata kanaler](./spark/../apache-spark-custom-conda-channel.md)
