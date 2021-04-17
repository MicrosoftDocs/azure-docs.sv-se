---
title: Felsöka biblioteksinstallationsfel
description: Den här självstudien innehåller en översikt över hur du felsöker biblioteksinstallationsfel.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 006abf62c605c2ca34fd1adeadee8e29ae0fb8fb
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588317"
---
# <a name="troubleshoot-library-installation-errors"></a>Felsöka biblioteksinstallationsfel 
Om du vill göra kod från tredje part eller lokalt tillgänglig för dina program kan du installera ett bibliotek på en av dina serverlösa Apache Spark pooler. Paketen som anges i requirements.txt hämtas från PyPi vid tidpunkten för poolstarten. Den här kravfilen används varje gång en Spark-instans skapas från Spark-poolen. När ett bibliotek har installerats för en Spark-pool är det tillgängligt för alla sessioner som använder samma pool. 

I vissa fall kanske du upptäcker att ett bibliotek inte visas i din Apache Spark poolen. Det här fallet inträffar ofta när det finns ett fel i den angivna requirements.txt eller angivna biblioteken. När ett fel uppstår i biblioteksinstallationsprocessen återgår Apache Spark till bibliotek som anges i Synapse-baskörningen.

Målet med det här dokumentet är att tillhandahålla vanliga problem och hjälpa dig att felsöka installationsfel för bibliotek.

## <a name="force-update-your-apache-spark-pool"></a>Tvinga fram uppdatering av Apache Spark poolen
När du uppdaterar biblioteken i Apache Spark-poolen hämtas dessa ändringar när poolen har startats om. Om du har aktiva jobb fortsätter de här jobben att köras på den ursprungliga versionen av Spark-poolen.

Du kan tvinga ändringarna att tillämpas genom att välja alternativet **Framt tvinga nya inställningar.** Den här inställningen avslutar alla aktuella sessioner för den valda Spark-poolen. När sessionerna har avslutats måste du vänta tills poolen har startats om. 

![Lägga till Python-bibliotek](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Lägga till Python-bibliotek")

## <a name="track-installation-progress"></a>Spåra installationsförloppet
Ett systemreserverade Spark-jobb startas varje gång en pool uppdateras med en ny uppsättning bibliotek. Det här Spark-jobbet hjälper dig att övervaka statusen för biblioteksinstallationen. Om installationen misslyckas på grund av bibliotekskonflikter eller andra problem återgår Spark-poolen till dess tidigare eller standardtillstånd. 

Dessutom kan användarna också granska installationsloggarna för att identifiera beroendekonflikter eller se vilka bibliotek som installerades under pooluppdateringen.

Så här visar du dessa loggar:
1. Gå till listan med Spark-program på **fliken** Övervaka. 
2. Välj det system-Spark-programjobb som motsvarar din pooluppdatering. Dessa systemjobb körs under *rubriken SystemReservedJob-LibraryManagement.*
   ![Skärmbild som visar jobb för systemreserverade bibliotek.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Visa systembiblioteksjobb")
3. Växla för att visa **drivrutins-** och **stdout-loggar.** 
4. I resultatet ser du loggarna som rör installationen av dina paket.
    ![Skärmbild som visar resultatet av jobbet för systemreserverade bibliotek.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Visa jobbförloppet för systembibliotek")

## <a name="validate-your-permissions"></a>Verifiera dina behörigheter
Om du vill installera och uppdatera bibliotek måste du ha behörigheten **Storage Blob Data-deltagare** eller **Storage Blob Data-ägare** för det primära Azure Data Lake Storage Gen2 Storage-kontot som är länkat till Azure Synapse Analytics-arbetsytan.

Du kan kontrollera att du har dessa behörigheter genom att köra följande kod:

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
Om du får ett felmeddelande saknar du förmodligen de behörigheter som krävs. Information om hur du skaffar de behörigheter som krävs finns i det här dokumentet: [Tilldela Storage Blob Data-deltagare eller Storage Blob Data-ägarbehörigheter.](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role)

Om du kör en pipeline måste arbetsytans MSI dessutom ha behörigheten Storage Blob Data-ägare eller Storage Blob Data-deltagare. Information om hur du beviljar din arbetsyteidentitet den här behörigheten finns i: [Bevilja behörigheter till arbetsytans hanterade identitet](../security/how-to-grant-workspace-managed-identity-permissions.md).

## <a name="check-the-environment-configuration-file"></a>Kontrollera miljökonfigurationsfilen
En miljökonfigurationsfil kan användas för att uppgradera Conda-miljön. Det här godkända filformatet för Hantering av Python-pooler visas [här.](./apache-spark-manage-python-packages.md)

Det är viktigt att notera följande begränsningar:
   -  Innehållet i kravfilen får inte innehålla extra tomma rader eller tecken. 
   -  [Synapse Runtime innehåller](apache-spark-version-support.md) en uppsättning bibliotek som är förinstallerade på varje serverlös Apache Spark pool. Paket som är förinstallerade i baskörningen kan inte tas bort eller avinstalleras.
   -  Det går inte att ändra PySpark-, Python-, Scala/Java-, .NET- eller Spark-versionen.
   -  Bibliotek med Python-sessionsomfång accepterar endast filer med ett YML-tillägg.

## <a name="validate-wheel-files"></a>Verifiera hjulfiler
De Synapse-serverlösa Apache Spark-poolerna baseras på Linux-distributionen. När du laddar ned och installerar Hjul-filer direkt från PyPI måste du välja den version som är byggd på Linux och körs på samma Python-version som Spark-poolen.

>[!IMPORTANT]
>Anpassade paket kan läggas till eller ändras mellan sessioner. Du måste dock vänta tills poolen och sessionen har startats om för att se det uppdaterade paketet.

## <a name="check-for-dependency-conflicts"></a>Söka efter beroendekonflikter
 I allmänhet kan det vara svårt att hantera Python-beroendematchning. För att felsöka beroendekonflikter lokalt kan du skapa en egen virtuell miljö som baseras på Synapse Runtime och verifiera dina ändringar.

Så här återskapar du miljön och verifierar dina uppdateringar:
 1. [Ladda](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) ned mallen för att återskapa Synapse-körningen lokalt. Det kan finnas små skillnader mellan mallen och den faktiska Synapse-miljön.
   
 2. Skapa en virtuell miljö med hjälp av [följande instruktioner.](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment) Med den här miljön kan du skapa en isolerad Python-installation med den angivna listan över bibliotek. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. Använd ``pip install -r <provide your req.txt file>`` för att uppdatera den virtuella miljön med dina angivna paket. Om installationen resulterar i ett fel kan det finnas en konflikt mellan vad som är förinstallerat i Synapse-baskörningen och vad som anges i den angivna kravfilen. Dessa beroendekonflikter måste lösas för att du ska kunna hämta de uppdaterade biblioteken på din serverlösa Apache Spark poolen.

>[!IMPORTANT]
>Problem kan uppstå när pip och conda används tillsammans. När du kombinerar pip och conda är det bäst att följa dessa [rekommenderade metoder.](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment)

## <a name="next-steps"></a>Nästa steg
- Visa standardbiblioteken: [stöd Apache Spark version](apache-spark-version-support.md)
