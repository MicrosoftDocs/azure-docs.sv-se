---
title: Hantera Python-bibliotek för Apache Spark
description: Lär dig hur du lägger till och hanterar Python-bibliotek som används av Apache Spark i Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 1d64233fc477ec25f91bb73c744b10210571df41
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588351"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Hantera Python-bibliotek för Apache Spark i Azure Synapse Analytics

Bibliotek tillhandahåller återanvändbar kod som du kanske vill inkludera i dina program eller projekt. 

Du kan behöva uppdatera din serverlösa poolmiljö Apache Spark av olika orsaker. Du kan till exempel se att:
- ett av dina kärnberoenden har precis släppt en ny version.
- du behöver ett extra paket för att träna upp maskininlärningsmodellen eller förbereda dina data.
- du har hittat ett bättre paket och inte längre behöver det äldre paketet.

Om du vill göra kod från tredje part eller lokalt tillgänglig för dina program kan du installera ett bibliotek på en av dina serverlösa Apache Spark-pooler eller notebook-session. I den här artikeln beskriver vi hur du kan hantera Python-bibliotek i din serverlösa Apache Spark pool.

## <a name="default-installation"></a>Standardinstallation
Apache Spark i Azure Synapse Analytics har en fullständig uppsättning bibliotek för vanliga datateknik-, dataförberedelse-, maskininlärnings- och datavisualiseringsuppgifter. Den fullständiga bibliotekslistan finns i Apache Spark [versionsstöd](apache-spark-version-support.md). 

När en Spark-instans startar inkluderas dessa bibliotek automatiskt. Extra Python- och specialbyggda paket kan läggas till på Spark-pool- och sessionsnivå.

## <a name="pool-libraries"></a>Poolbibliotek
När du har identifierat de Python-bibliotek som du vill använda för Spark-programmet kan du installera dem i en Spark-pool. Bibliotek på poolnivå är tillgängliga för alla notebook-datorer och jobb som körs i poolen.

Det finns två huvudsakliga sätt att installera ett bibliotek i ett kluster:
-  Installera ett arbetsytebibliotek som har laddats upp som ett arbetsytepaket.
-  Ange en *requirements.txt-* eller *Conda environment.yml-miljöspecifikation* för att installera paket från lagringsplatsen som PyPI, Conda-Forge med mera.

> [!IMPORTANT]
> - Om det paket som du installerar är stort eller tar lång tid att installera påverkar detta starttiden för Spark-instansen.
> - Det finns inte stöd för att ändra PySpark-, Python-, Scala/Java-, .NET- eller Spark-version.
> - Installation av paket från externa databaser som PyPI, Conda-Forge eller Conda-standardkanalerna stöds inte i DEP-aktiverade arbetsytor.

### <a name="install-python-packages"></a>Installera Python-paket
Python-paket kan installeras från lagringsplatsen som PyPI och Conda-Forge genom att tillhandahålla en miljöspecifikationsfil. 

#### <a name="environment-specification-formats"></a>Format för miljöspecifikationer

##### <a name="pip-requirementstxt"></a>PIP-requirements.txt
En *requirements.txt* -fil (utdata `pip freeze` från kommandot) kan användas för att uppgradera miljön. När en pool uppdateras laddas paketen som anges i den här filen ned från PyPI. De fullständiga beroendena cachelagras sedan och sparas för senare återanvändning av poolen. 

Följande kodfragment visar formatet för kravfilen. PyPI-paketnamnet visas tillsammans med en exakt version. Den här filen följer det format som beskrivs i [referensdokumentationen för pip-lås.](https://pip.pypa.io/en/stable/reference/pip_freeze/) 

Det här exemplet fäster en specifik version. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>YML-format (förhandsversion)
Dessutom kan du även ange en *environment.yml-fil* för att uppdatera poolmiljön. Paketen som anges i den här filen hämtas från Conda-standardkanalerna, Conda-Forge och PyPI. Du kan ange andra kanaler eller ta bort standardkanalerna med hjälp av konfigurationsalternativen.

Det här exemplet anger kanaler och Conda/PyPI-beroenden. 

```
name: stats2
channels:
- defaults
dependencies:
- bokeh
- numpy
- pip:
  - matplotlib
  - koalas==1.7.0
```
Mer information om hur du skapar en miljö från filen environment.yml finns i Skapa en miljö [från en environment.yml-fil.](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment
)

#### <a name="update-python-packages"></a>Uppdatera Python-paket
När du har identifierat miljöspecifikationsfilen eller uppsättningen bibliotek som du vill installera i Spark-poolen kan du uppdatera Spark-poolbiblioteken genom att gå till Azure Synapse Studio eller Azure Portal. Här kan du ange miljöspecifikationen och välja de arbetsytebibliotek som ska installeras. 

När ändringarna har sparats kör ett Spark-jobb installationen och cachelagrar den resulterande miljön för senare återanvändning. När jobbet är klart använder nya Spark-jobb eller notebook-sessioner de uppdaterade poolbiblioteken. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Hantera paket från Azure Synapse Studio eller Azure Portal
Spark-poolbibliotek kan antingen hanteras från Azure Synapse Studio eller Azure Portal. 

Så här uppdaterar eller lägger du till bibliotek i en Spark-pool:
1. Gå till Azure Synapse Analytics arbetsyta från Azure Portal.

    Om du uppdaterar från **Azure Portal**:

    - Under avsnittet **Synapse-resurser** väljer du **fliken Apache Spark pooler** och väljer en Spark-pool i listan.
     
    - Välj **paketen** i **avsnittet Inställningar** i Spark-poolen.
  
    ![Skärmbild som visar knappen ladda upp miljökonfigurationsfil.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Lägga till Python-bibliotek")
   
    Om du uppdaterar från **Synapse Studio**:
    - Välj **Hantera** från huvudnavigeringspanelen och välj sedan Apache Spark **pooler**.

    - Välj avsnittet **Paket** för en specifik Spark-pool.
    ![Skärmbild som visar alternativet för att ladda upp miljökonfiguration från Studio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Lägga till Python-bibliotek från Studio")
   
2. Ladda upp miljökonfigurationsfilen med hjälp av filväljaren  **i avsnittet** Paket på sidan.
3. Du kan också välja ytterligare **arbetsytepaket att** lägga till i din pool. 
4. När du sparar ändringarna utlöses ett systemjobb för att installera och cachelagra de angivna biblioteken. Den här processen hjälper till att minska den övergripande sessionens starttid. 
5. När jobbet har slutförts hämtar alla nya sessioner de uppdaterade poolbiblioteken.

> [!IMPORTANT]
> Genom att välja alternativet Tvinga **nya inställningar avslutar** du alla aktuella sessioner för den valda Spark-poolen. När sessionerna har avslutats måste du vänta tills poolen har startats om. 
>
> Om den här inställningen är avmarkerad måste du vänta tills den aktuella Spark-sessionen avslutas eller stoppa den manuellt. När sessionen har avslutats måste du låta poolen starta om.


##### <a name="track-installation-progress-preview"></a>Spåra installationsförloppet (förhandsversion)
Ett systemreserverade Spark-jobb initieras varje gång en pool uppdateras med en ny uppsättning bibliotek. Det här Spark-jobbet hjälper dig att övervaka statusen för biblioteksinstallationen. Om installationen misslyckas på grund av bibliotekskonflikter eller andra problem återgår Spark-poolen till dess tidigare eller standardtillstånd. 

Dessutom kan användare granska installationsloggarna för att identifiera beroendekonflikter eller se vilka bibliotek som installerades under pooluppdateringen.

Så här visar du dessa loggar:
1. Gå till listan med Spark-program på **fliken** Övervaka. 
2. Välj det system-Spark-programjobb som motsvarar din pooluppdatering. Dessa systemjobb körs under *rubriken SystemReservedJob-LibraryManagement.*
   ![Skärmbild som visar jobb för systemreserverade bibliotek.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Visa systembiblioteksjobb")
3. Växla för att visa **drivrutins-** **och stdout-loggarna.** 
4. I resultatet visas loggarna som rör installationen av dina beroenden.
    ![Skärmbild som visar resultatet av jobbet för systemreserverade bibliotek.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Visa jobbförloppet för systembibliotek")

## <a name="install-wheel-files"></a>Installera hjulfiler
Python-hjulfiler är ett vanligt sätt att paketera Python-bibliotek. Inom Azure Synapse Analytics kan användarna ladda upp sina hjulfiler till en välkänd plats för Azure Data Lake Storage-kontot eller ladda upp med hjälp Azure Synapse gränssnittet för arbetsytepaket.

### <a name="workspace-packages-preview"></a>Arbetsytepaket (förhandsversion)
Arbetsytepaket kan vara anpassade eller privata hjulfiler. Du kan ladda upp dessa paket till din arbetsyta och senare tilldela dem till en specifik Spark-pool.

Så här lägger du till arbetsytepaket:
1. Gå till fliken **Hantera**  >  **arbetsytepaket.**
2. Ladda upp hjulfilerna med hjälp av filväljaren.
3. När filerna har laddats upp till Azure Synapse arbetsytan kan du lägga till dessa paket i en Apache Spark pool.

![Skärmbild som visar arbetsytepaket.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Visa arbetsytepaket")

>[!WARNING]
>- Inom Azure Synapse kan en Apache Spark-pool utnyttja anpassade bibliotek som antingen laddas upp som arbetsytepaket eller laddas upp inom en välkänd Azure Data Lake Storage sökväg. Båda dessa alternativ kan dock inte användas samtidigt inom samma Apache Spark pool. Om paket tillhandahålls med båda metoderna installeras endast de hjulfiler som anges i listan med arbetsytepaket. 
>
>- När arbetsytepaket (förhandsversion) används för att installera paket på en viss Apache Spark-pool finns det en begränsning att du inte längre kan ange paket med lagringskontots sökväg i samma pool.  

### <a name="storage-account"></a>Lagringskonto
Anpassade hjulpaket kan installeras i Apache Spark-poolen genom att ladda upp alla hjulfiler till Azure Data Lake Storage-kontot (Gen2) som är länkat till Synapse-arbetsytan. 

Filerna ska laddas upp till följande sökväg i lagringskontots standardcontainer: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!WARNING]
> I vissa fall kan du behöva skapa filsökvägen baserat på strukturen ovan om den inte redan finns. Du kan till exempel behöva lägga till ```python``` mappen i mappen om den inte redan ```libraries``` finns.

> [!IMPORTANT]
> Om du vill installera anpassade bibliotek med Azure DataLake Storage-metoden måste du ha behörigheten **Storage Blob Data-deltagare** eller **Storage Blob Data-ägare** på det primära Gen2-lagringskontot som är länkat till Azure Synapse Analytics arbetsytan.


## <a name="session-scoped-packages-preview"></a>Sessionsomfångspaket (förhandsversion)
Förutom paket på poolnivå kan du även ange bibliotek med sessionsomfång i början av en notebook-session.  Med sessionsomfångsbaserade bibliotek kan du ange och använda anpassade Python-miljöer i en notebook-session. 

När du använder bibliotek med sessionsomfång är det viktigt att ha följande i åtanke:
   - När du installerar bibliotek med sessionsomfång har endast den aktuella notebook-datorn åtkomst till de angivna biblioteken. 
   - De här biblioteken påverkar inte andra sessioner eller jobb som använder samma Spark-pool. 
   - De här biblioteken installeras ovanpå baskörnings- och poolnivåbiblioteken. 
   - Notebook-bibliotek har högst prioritet.

Så här anger du paket med sessionsomfång:
1.  Gå till den valda Spark-poolen och kontrollera att du har aktiverat bibliotek på sessionsnivå.  Du kan aktivera den här inställningen genom att gå **till fliken**  >  **Hantera Apache Spark-poolpaket.** Aktivera  >   ![sessionspaket.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Aktivera sessionspaket")
2.  När inställningen har tillämpats kan du öppna en notebook-fil och välja **Konfigurera** >  **sessionspaket.**
  ![Ange sessionspaket.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Uppdatera sessionskonfigurationen")
3.  Här kan du ladda upp en Conda *environment.yml-fil* för att installera eller uppgradera paket i en session. När du startar sessionen installeras de angivna biblioteken. När sessionen avslutas är dessa bibliotek inte längre tillgängliga eftersom de är specifika för din session.

## <a name="verify-installed-libraries"></a>Verifiera installerade bibliotek
Kontrollera om rätt versioner av rätt bibliotek har installerats från PyPI genom att köra följande kod:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
I vissa fall kan du behöva granska paketversionen individuellt för att kunna visa paketversionerna från Conda.

## <a name="next-steps"></a>Nästa steg
- Visa standardbiblioteken: [stöd Apache Spark version](apache-spark-version-support.md)
- Felsöka biblioteksinstallationsfel: [Felsöka biblioteksfel](apache-spark-troubleshoot-library-errors.md)
- Skapa en privat Conda-kanal med ditt Azure Data Lake Storage konto: [Privata Conda-kanaler](./spark/../apache-spark-custom-conda-channel.md)
